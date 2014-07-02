original/source .iso download:
>  [Microsoft Visual Studio Express 2012 for Windows Desktop](http://www.microsoft.com/en-us/download/details.aspx?id=34673)

information regarding the official license:
>  [License Extensions for Visual Studio 2012 and Visual Studio 2012 SDK](http://msdn.microsoft.com/en-US/vstudio/hh857605)

git project comments:
---------------------
  * all ownership and copyright belong to Microsoft
  * to be perfectly honest, I've only just now glanced over
    the above mentioned "License Extensions" document.
  * most of the files in this project appear to be included under the section:
    "BuildServer Files for Visual Studio 2012";
    however, this section doesn't include the "Express" edition.
  * that being said, I'm fairly certain that distribution of this project is a violation of terms.
  * never-the-less,
    gyp (and node-gyp) are common build tools..
    and in a Windows environment, MSBuild and Visual C++ are dependencies.
    Microsoft makes these tools available for free distribution (see above link);
    however, the .iso from Microsoft is 600+MB, with a system requirement of 5GB of hard disk space.
	For the purposes of satisfying said dependencies, a minimal toolset is desirable.
  * The singular purpose of this "project" is to provide a "lean and mean" build system,
    which is stand-alone and as portable as possible.
  * To answer the question: "how well did you do?"
    Here are a few bullet points:
      - size as a compressed 7-zip archive: 41MB
      - size uncompressed: 500MB
      - included: Visual C++, MSBuild
      - runtime dependencies: Microsoft .NET 4+ (for MSBuild)

comments related to project goals:
----------------------------------
  * gyp (and node-gyp) also require Python 2.7
    This dependency is easily met by downloading:

        http://sourceforge.net/projects/pythonstdalone/
        http://sourceforge.net/projects/pythonstdalone/files/python_27_standalone_complete_v_1_r_2011_07_01.7z/download

in summary:
-----------
  * please don't sue me or throw me in jail
  * I'm just a software developer who:
      - prefers to work in a Windows environment
      - recently needed to "npm" a node module that threw the error:

            MSBUILD : error MSB3428: Could not load the Visual C++ component "VCBuild.exe".
            To fix this,
                1) install the .NET Framework 2.0 SDK,
                2) install Microsoft Visual Studio 2005, or
                3) add the location of the component to the system path if it is installed elsewhere.
      - googled and googled hoping to find a ready to use solution.. to download.. unzip.. and continue coding;
        no such solution could be found
      - spent an entire weekend (nearly 48 blurry-eyed hours straight) trying to figure out how to extract
        the necessary components from Visual Studio Express.
        Interesting side note:
          - the error message says to use VS2005.
            I did that, and the build failed.
            Digging a little deeper:

                .\nodejs\node_modules\npm\node_modules\node-gyp\gyp\pylib\gyp\MSVSVersion.py
            reveals that VS2012 Express is (currently) the most recent version supported.
          - per the installation notes:

                https://github.com/TooTallNate/node-gyp#installation
            VS2012 Express (for Windows Desktop) is the only version that supports both Windows 7 and 8.
      - wants to share the end product with other developers who find themselves in the same predicament..
        so now there is a simple to download, ready to use solution.
  * did I happen to mention: please don't sue me?!

installation notes:
-------------------
  * extract the contents of the archive using 7-zip:

        http://portableapps.com/apps/utilities/7-zip_portable
    to anywhere you like.
    (for "usage" below, lets say that we'll assign this path to the variable: gyp_msvs_version_2012e)
  * there are 2 top-level directories:
      - dependencies

            This directory contains everything.
      - [tests]

            This directory contains a few tests that I wrote to confirm proper functionality.
            You can safely delete this.

usage:
------
  * Visual C++ (only)

        call "%gyp_msvs_version_2012e%\dependencies\_environment\vc_vars_32.bat"
	now:

      - %VisualStudioVersion%, %INCLUDE%, %LIB%, %LIBPATH% (and many other environment variables) are properly set
      - %PATH% includes cl.exe and link.exe
  * Visual C++ and MSBuild

        call "%gyp_msvs_version_2012e%\dependencies\_environment\vc_vars_32.bat"
        call "%gyp_msvs_version_2012e%\dependencies\_environment\msbuild_vars_32.bat"
    now:
      - same as above, but now %PATH% also includes MSBuild.exe
      - assuming that %PATH% also includes Python 2.7,
        all of gyp (and node-gyp) dependencies are now satisfied.
  * personally, I added a file:

        .\nodejs\nodevars.node-gyp.bat
    that contains:

        set PATH=%PATH%;%~dp0..\python\2.7\complete
        call "%~dp0..\visual_studio_express_2012\dependencies\_environment\vc_vars_32.bat"
        call "%~dp0..\visual_studio_express_2012\dependencies\_environment\msbuild_vars_32.bat"
        set GYP_MSVS_VERSION=2012e
        set node_gyp="%~dp0node.exe" "%~dp0node_modules\npm\node_modules\node-gyp\bin\node-gyp.js"
    which assumes the directory structure:

        .\nodejs
        .\nodejs\node.exe
        .\nodejs\node_modules\npm\node_modules\node-gyp
        .\python\2.7\complete
        .\visual_studio_express_2012\dependencies
    obviously, you can customize as you see fit..
  * within the same shell:

        npm install XYZ
    assuming, of course, that npm is in your %PATH%.
    otherwise, you may want to do something like:
      - add/edit the file:

            .\nodejs\nodevars.bat
        with the commands:

            set NODE_PATH=%cd%;%~dp0node_modules;%~dp0node_modules\npm\node_modules
            set npm="%~dp0node.exe" "%~dp0node_modules\npm\bin\npm-cli.js"
      - then to use npm:

            call .\nodejs\nodevars.bat
            call .\nodejs\nodevars.node-gyp.bat
            %npm% install XYZ
