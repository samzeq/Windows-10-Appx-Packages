# Windows 10 Appx Packages
So, i published this because i wanted people to actually install missing UWP apps

## How i made it
So, You need to have the Windows SDK installed, then add C:\Program Files (x86)\Windows SDK\10\bin\<replace with version>\x86 into your system PATH.

Script:
´´
echo off
setlocal enabledelayedexpansion

:: Define the source directory (WindowsApps)
set "sourceDir=C:\Program Files\WindowsApps"

:: Define the output directory where .appx files will be saved
set "outputDir=C:\OutputAppxFiles"

:: Define the output directory for the zip file
set "zipOutputDir=C:\OutputZipFiles"

:: Create output directories if they don't exist
if not exist "%outputDir%" mkdir "%outputDir%"
if not exist "%zipOutputDir%" mkdir "%zipOutputDir%"

:: Loop through each folder in the WindowsApps directory
for /d %%I in ("%sourceDir%\*") do (
    if exist "%%I\appxmanifest.xml" (
        echo Processing folder: %%I

        :: Extract the folder name to use in the output file name
        set "folderName=%%~nxI"

        :: Create the .appx file
        makeappx pack /d "%%I" /p "%outputDir%\!folderName!.appx"
        
        if !errorlevel! neq 0 (
            echo Failed to create .appx file for %%I
        ) else (
            echo Successfully created .appx: !folderName!.appx
        )
    )
)

:: Now create a .zip file of the entire output folder
echo Zipping the entire output folder...
powershell -Command "Compress-Archive -Path '%outputDir%\*' -DestinationPath '%zipOutputDir%\OutputAppxFiles.zip'"

if !errorlevel! neq 0 (
    echo Failed to zip the output folder.
) else (
    echo Successfully zipped the output folder into OutputAppxFiles.zip
)

echo Done!
pause
´´

## How to add to PATH
First, press Win + R, then type sysdm.cpl, then enter.
Go to Advanced > Envroinment Variables then click Path on User Variables.
Select Add then put: C:\Program Files (x86)\Windows SDK\10\bin\<replace with version>\x86
Then press enter. You can now close the window.
