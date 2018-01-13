# MSBuildTest
An example Visual Studio UWP C# project using MSBuild to process some assets.

You should understand basic MSBuild concepts (Properties, Items, Metadata, Targets, Tasks) to follow this.
See https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild-concepts

This repo contains an empty UWP app with some assets that need to be processed as part of the build. In this case, the "processing" 
is just copying a couple text files from one folder to another, and also generating a text file containing lists of assets. 
The intent is to demostrate one-to-one and many-to-one asset processing using MSBuild, but a real project would use a more
complex asset processing task than Copy or WriteLinesToFile!

All of the content processing is in [MSBuildTest/content.targets](MSBuildTest/content.targets)

The source assets are referred to as **RawData** items in the msbuild file, and they get converted into **ConvertedData** items. 
There are also **DataList** items which are created by the build - they are text files containing a list of items, 
with the item list to use being specified as custom item metadata.

The **ConvertedData** and **DataList** items are deployed to the AppX folder when the deploy step is run. This is handled
in the targets by adding them as Content items. Note that the targets must run before the *AssignTargetPaths* target
in order for the Content items to be deployed correctly.

# Building
Clone the repository, and open MSBuildTest.sln with Visual Studio 2017. Select **Build/Build Solution** or **Build/Rebuild Solution** and you should see the following 
in the Visual Studio output window:

    1>------ Rebuild All started: Project: MSBuildTest, Configuration: Debug x86 ------
    1>  Deleting Data folder
    1>  Begin Debug Messages
    1>    RawData: RawData\Hello.txt
    1>    RawData: RawData\World.txt
    1>    ConvertedData: Data\Hello.txt
    1>    ConvertedData: Data\World.txt
    1>    DataList: Data\ConvertedList.txt
    1>    DataList: Data\RawList.txt
    1>  End Debug Messages
    1>  Converting Data: RawData\Hello.txt -> Data\Hello.txt
    1>  Converting Data: RawData\World.txt -> Data\World.txt
    1>  Creating List: Data\Hello.txt;Data\World.txt -> Data\ConvertedList.txt
    1>  Creating List: RawData\Hello.txt;RawData\World.txt -> Data\RawList.txt
    1>  MSBuildTest -> C:\Dev\Home\Apps\MSBuildTest\MSBuildTest\bin\x86\Debug\MSBuildTest.exe
    ========== Rebuild All: 1 succeeded, 0 failed, 0 skipped ==========

If you then immediately run an incremental build, you should see the following:

    ========== Build: 0 succeeded, 0 failed, 1 up-to-date, 0 skipped ==========
    
indicating there was nothing to be built.
