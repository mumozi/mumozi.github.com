# windows下php版本动态切换

## php7.bat

```bash
@ECHO off

rem 设置目标路径
SET DIR=D:\phpstudy_pro\Extensions\php\php7.3.4nts

IF DEFINED ORIGIN_PATH (
	SET PATH="%ORIGIN_PATH%"
)
rem 去除变量中所有的引号
SET PATH=%PATH:"=%

SET ORIGIN_PATH=%PATH%
SET PATH=%DIR%;%PATH%
SET PARAM=%~1

IF NOT DEFINED PARAM (
	php -v
	GOTO end
)

php %~1 %~2 %~3 %~4 %~5 %~6 %~7 %~8 %~9

:end
rem SET PATH=%ORIGIN_PATH%
```

## php5.bat

```bash
@ECHO off

rem 设置目标路径
SET DIR=D:\phpstudy_pro\Extensions\php\php5.3.29nts

IF DEFINED ORIGIN_PATH (
	SET PATH="%ORIGIN_PATH%"
)
rem 去除变量中所有的引号
SET PATH=%PATH:"=%

SET ORIGIN_PATH=%PATH%
SET PATH=%DIR%;%PATH%
SET PARAM=%~1

IF NOT DEFINED PARAM (
	php -v
	GOTO end
)

php %~1 %~2 %~3 %~4 %~5 %~6 %~7 %~8 %~9

:end
rem SET PATH=%ORIGIN_PATH%
```

## 使用方式

把2个文件放在一个文件夹下，文件夹路径添加到环境变量的path中

在cmd下执行`php5`或者`php7`切换版本，仅限当前cmd下有效。