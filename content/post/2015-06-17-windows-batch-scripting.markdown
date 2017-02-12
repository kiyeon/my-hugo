---
categories: windows scripting
comments: true
date: 2015-06-17T09:42:37Z
title: Windows Batch Scripting
url: /2015/06/17/windows-batch-scripting/
---

MS의 DOS로 시작해 Windows를 거쳐 유닉스와 리눅스를 만나고 이제는 유닉스(Mac)에 정착했다. 하지만 여전히 일선상에서는 Windows를 벗어나지 못하고 있다. 로컬OS는 Windows를 사용하지만 대부분 서비스를 이용하기위한(업무환경) 수단일뿐 실제 개발은 리눅스상에서 더 많이 한다. 좋든 실든 업무효율성을 높이기 위해 다양한 유틸리티와 Script가 필요하다.

여러대의 서버로 파일을 업로드하는 간단한 스크립트를 살펴보며 Window Batch Script를 알아보자.

<!--more-->

## Echoing off와 주석
Windows Batch Script를 실행하면 Command가 화면에 함께 출력된다. 좀 특이하지만 이런 출력을 `echo on/off`로 제어할 수 있다.[^2]  [echo]자체도 command이므로 앞에 `@`를 붙여줘서 [echo]역시 화면에 출력되지 않게 한다.

newline을 출력하려면 `echo.`을 사용한다.

주석은 `::`를 사용한다. [REM]을 사용할 수도있지만 [REM]은 `echo on/off`에 영향을 받는다.[^1]
``` batch
:: command echoing을 off한다. 앞에 @는 echo자체 역시 화면에 출력하지 않도록 한다.
@echo off
```

## 변수
변수를 설정할때는 [SET]을 사용하며 변수명과 값사이는 공백이 들어가면 안된다. 사용할때는 `%변수명%`처럼 변수명 양옆을 `%`로 감싼다.

``` batch
set ID=kiyeon
set PW=niceguy
set TODAY=%date:~0,4%%date:~5,2%%date:~8,2%
set SRCFILE=c:/dump.zip
set DSTFILE=./dump_%TODAY%.zip
```

[date]는 시스템의 시간을 출력하는데 Script내에서 `%date%`로 사용할 수 있으며[^8] 출력포맷을 지정하고 싶다면 `set isodate=%date:~0,4%%date:~5,2%%date:~8,2%`처럼 사용한다.
``` batch
set isodate=%date:~0,4%%date:~5,2%%date:~8,2%
echo %isodate%
20150617
```

## 배열과 루프
Batch script는 배열을 지원하지 않는다. 하지만 아래와 같은 꼼수로 배열처럼 사용할 수 있다.[^3]

루프는 [for]문이 있으며 값이 대입되는 변수의 명은 한자리 알파벳이여야 한다. [goto]와 [if]문을 응용할 수도 있다.[^4]

이 예제에서는 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)에서 제공하는 [pscp](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)[^5]와 [plink](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)[^6]를 사용한다.

`goto :eof`(end of file)는 스크립트의 마지막으로 간다. 즉 스크립트를 종료한다. 함수를 스크립트 파일 하단에 위치시키는데 이 구문이 없으면 하단에 있는 명령들을 모두 실행하고 스크립트가 종료하게 된다.
``` batch
set IP[01]=0.0.0.1
set IP[02]=0.0.0.2
set IP[03]=0.0.0.3
set IP[04]=0.0.0.4

call :alert Upload dump file ...
:: echo %result% 

for /F "tokens=2 delims==" %%v in ('set IP[') do (
  pscp -pw %PW% %SRCFILE% %ID%@%%v:%DSTFILE%
  call :result %%v

  plink -pw %PW% -batch %ID%@%%v chmod 700 %DSTFILE%
  call :result %%v
)

call :alert Finished upload.
goto :eof
```

## 함수
함수역시 일반적인 프로그래밍언어에서 사용하는 방식과 다르게 [call], labels, [setlocal], [endlocal]을 이용하여 함수처럼 사용한다.

함수는 스크립트 파일의 하단에 위치시키고, 인자는 `%1 %2`처럼 사용한다.

[exit]는 batch script를 종료하거나 cmd.exe program을 종료시킨다. `/b`옵션과 함께 사용하면 현재 batch script를 종료한다. `/b`옵션과함께 `ExitCode`를 사용하면 [ERRORLEVEL](https://en.wikibooks.org/wiki/Windows_Batch_Scripting#Special_names)에 `ExitCode`가 지정되어 call function의 성공 유무를 체크할 수 있다. `%ERRORLEVEL%`은 마지막으로 실행된 명령 또는 배치스크립트의  [error level](https://en.wikibooks.org/wiki/Windows_Batch_Scripting#Error_level)을 리턴한다.

return value를 전달하는 기능은 없지만 역시 꼼수로 변수에 리턴값을 지정해 사용할 수 있다.[^7]

[setlocal]과 [endlocal]로 함수내에서 사용된 변수를 지역화할 수 있다.

함수의 호출은 `call :label`로 한다.

``` batch
:alert
setlocal
  echo ##########################################################
  echo ## %*
  echo ##########################################################
  pause
endlocal & set result=end of alert
exit /b 0

:result
setlocal
  if %errorlevel% neq 0 (
    echo Failed upload to the %1. Errorlevel:%errorlevel%
  ) else (
    echo Completed upload to the %1. Errorlevel:%errorlevel%
  )
endlocal & set result=end of result
exit /b 0
```

## 재사용
공통으로 사용할 수 있는 설정및 함수를 따로 파일로 분리한뒤 [call]로 재사용할 수 있다.[^9] 위 예제를 재구성 해보자.
``` batch sendfile.cmd
@echo off
call config.cmd
set SRCFILE=c:/dump.zip
set DSTFILE=./dump_%TODAY%.zip

call function_alert.cmd Upload dump file ...

for /F "tokens=2 delims==" %%v in ('set IP[') do (
  pscp -pw %PW% %SRCFILE% %ID%@%%v:%DSTFILE%
  call function_result.cmd %%v

  plink -pw %PW% -batch %ID%@%%v chmod 700 %DSTFILE%
  call function_result.cmd %%v
)

call function_alert.cmd Finished upload.
goto :eof
```

``` batch config.cmd
set ID=kiyeon
set PW=niceguy
set IP[01]=0.0.0.1
set IP[02]=0.0.0.2
set IP[03]=0.0.0.3
set IP[04]=0.0.0.4
set TODAY=%date:~0,4%%date:~5,2%%date:~8,2%
```

``` batch function_alert.cmd
:alert
setlocal
  echo ##########################################################
  echo ## %*
  echo ##########################################################
  pause
endlocal & set result=end of alert
exit /b 0
```

``` batch function_result.cmd
:result
setlocal
  if %errorlevel% neq 0 (
    echo Failed upload to the %1. Errorlevel:%errorlevel%
  ) else (
    echo Completed upload to the %1. Errorlevel:%errorlevel%
  )
endlocal & set result=end of result
exit /b 0
```

여러대의 서버에 파일을 업로드하는 간단한 스크립트를 만들어 봤다.

여기서는 변수의 선언과 배열방식으로의 사용 반복문과 함수, 조건문과 외부 유틸리티를 사용하고 상호 동작하는 방법도 엿볼 수 있다.

Windows의 Batch Script는 낯설지만 윈도우 환경에서 간편하게 사용하기에 괜찮은거 같다. 이상하지만 재미난 스크립트다.

좀더 제대로 사용하려면 [Cygwin](https://www.cygwin.com/)을 이용한 쉘스크립팅이나 [Power Shell](https://en.wikipedia.org/wiki/Windows_PowerShell)이 있다.


## References
  * [Windows Batch Scripting](https://en.wikibooks.org/wiki/Windows_Batch_Scripting) at wikibooks
  * [An A-Z Index of the Windows CMD command line](http://ss64.com/nt/) at ss64
  * [Command-line reference A-Z](http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/ntcmds.mspx?mfr=true) at the Microsoft
  * [Guide to Windows Batch Scripting](http://steve-jansen.github.io/guides/windows-batch-scripting/index.html) by steve jansen
  * [Win32 Shell Scripting Tutorial](http://www.csie.ntu.edu.tw/~r92092/ref/win32/win32scripting.html)



[^1]: [A comment command. Unlike double-colon (::), the command can be executed.](https://en.wikibooks.org/wiki/Windows_Batch_Scripting#REM)
[^2]: [If you use echo off, the command prompt does not appear on your screen. To prevent echoing of a line, insert an at sign (@) in front of a command in a batch program.](http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/echo.mspx)
[^3]: [How to Use Array in Windows Batch Programming?](https://helloacm.com/how-to-use-array-in-windows-batch-programming/), [How to loop through array in batch?](http://stackoverflow.com/a/18480952)
[^4]: [http://stackoverflow.com/a/25791900](http://stackoverflow.com/a/25791900)
[^5]: command line에서 secure file copy를 지원하는 SCP client다.
[^6]: command line connection utility로 host로 command를 실행할 수 있다.
[^7]: [Functions](https://en.wikibooks.org/wiki/Windows_Batch_Scripting#Functions)
[^8]: [Special names](https://en.wikibooks.org/wiki/Windows_Batch_Scripting#Special_names)
[^9]: [Batch file include external file for variables](http://stackoverflow.com/a/2763907)


[SET]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/set.mspx
[REM]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/rem.mspx
[ECHO]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/echo.mspx
[FOR]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/for.mspx
[GOTO]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/goto.mspx
[IF]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/if.mspx
[CALL]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/call.mspx
[SETLOCAL]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/setlocal.mspx
[ENDLOCAL]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/endlocal.mspx
[EXIT]: http://www.microsoft.com/resources/documentation/windows/xp/all/proddocs/en-us/exit.mspx
[DATE]: https://en.wikibooks.org/wiki/Windows_Batch_Scripting#DATE

