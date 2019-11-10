# Virtualbox Open Source Edition

## Introduction in Virtualbox OSE

Virtualbox Open Source Edition is a quite large complicated solutions, with lots of dependencies and tools. 
If you want to start contributing in the project, make your own fork, or incorporate Virtualbox into your own product, 
you should be ready to invest time into mastering its complicated build toolchain, 
numerous dependencies and additional tools. 
Just remember, Virtualbox is licensed under GPLv2, and all its derivatives also in fact subjects of this license.

Source code of Virtualbox is available from the official product website: https://www.virtualbox.org/wiki/Downloads
However, the main issue is that this build scripts and configs are optimized for Oracle own build infrastructure, 
which is near to nothing known about, and the lack of official documentation requires significant effort 
just to compile the solution, not to talk about starting active development.

This fork has minor differences, and allows building the solution as smooth and easy as possible.
https://github.com/yuchdev/VirtualBox-OSE
You can see the full list of differences with comments below, also if you already have your code, you can apply patch
 or perform code merge with one of the tools provided.

Solution could be compiled under Windows, MacOS and Linux, and every system has its own pre-requisites.
In general, there are two ways of preparing 3rd party dependencies: compile it on your own
 or install pre-compiled libraries, which is obviously easier. We will guide you through both processes.

## Windows development environment

### System requirements
You need Windows x64, has been tested under Windows 7, Windows 8.0/8.1 or Windows 10, 
however older unsupported versions like Windows XP might work as well.
As many software have web installers, and it is highly recommended to update it right after installation, 
your internet connection should be fast enough for comfortable work.

### Signing drivers
Be aware that the solution contains drivers, which either must be signed with development cross-certificate, 
special certificates for open-source products (TODO link), 
or Extended Validation Certificate from certificate issuing authorities.
Before installing developer cross-certificate you must switch your system into Test Mode by the following command.
`bcdedit.exe /set TESTSIGNING ON`

On Windows 7, 8.0 and 8.1 it works without additional requirements, 
however on Windows 10 you can't switch your system into the Test Mode if Secure Boot BIOS setting is set to ON. 
Refer to your BIOS documentation to turn it off.

### Prerequisites

These tools are necessary both for compiling Virtualbox itself, and most of 3rd party libraries, 
so you should install it first. Not surprisingly, most of them are Microsoft compilers, SDKs and tools.

* Windows Software Developer Kit 7.1
* Windows Software Developer Kit 8.1
* Windows Driver Developer Kit 7.1
* Windows Driver Developer Kit 8.0
* Visual Studio 2010 SP1 Professional
* Visual Studio 2010 updates
* Visual Studio 2010 VersionInfo plugin
* Visual Studio 2013 Update 5 Professional or Ultimate
* ActivePerl 5.26
* ActivePython 2.7

## Installing all necessary software

### Windows Software Developer Kit 7.1

Should be installed first, so that Visual Studio 2010 updates could re-write obsolete versions of runtimes and tools.

***Command for unattended installation:***

`start /wait setup.exe -q -params:ADDLOCAL=ALL`


### Windows Software Developer Kit 8.1

***Command for unattended installation:***

`start /wait sdksetup.exe /features + /quiet`

### Windows Driver Developer Kit 7.1
You need to install at lest Build Environments for compiling Virtualbox for Windows kernel-space components. 
If you need to debug drivers, you may use standard WinDbg debugger, version 1.* usually included in Windows SDK 7.1, 
version 2.* could be installed from Windows Store.
Unattended installation
Like any other components, WDK 7.1 could be installed in unattended mode, if answers file in `*.ini` format is provided.

### Windows Driver Developer Kit 8.0
It is important to install.
Unattended installation
Like any other components, WDK 7.1 could be installed in unattended mode, if answers file in `*.ini` format is provided.

### Microsoft Visual Studio 2010 Service Pack 1 (Professional or Ultimate Editions)
Virtualbox for Windows requires ATL library, as it heavily relies on COM (Component Object Model) 
for interprocess communication and all of the front-ends (GUI, VBoxManage and official APIs). 
Community Edition does not contain ATL, that's why you need Professional or Ultimate Editions. 
Occasional problems with Professional Edition under Windows 10 were noticed, however Ultimate Edition worked just fine.
After installing Visual Studio 2010 SP1 you also need to apply all updates, which were issued later than SP1 
(see the complete list below), you may check if all updates were applied successfully using special plugin
for Visual Studio 2010 (TODO).

### Visual Studio 2010 Unattended installation
All of the installation steps could be automated and performed in a single script execution. 
For installing Visual Studio itself you need an unattended answer file (*.ini format), 
passed as a command-line parameter to the installer. Usually components expected to be installed are provided there,
and User Agreement question could be answered without user action, 
also you may change default installation path (TODO anything else?).
After Visual Studio 2010 installation you may install Service Pack 1, 
all updates and a plugin for checking all updates were installed successfully, 
for all of these steps you just need only pass command line key for silent install, 
and check system return code, which should be zero for successful update installation.
Plugin for checking updates consistency could be run from the Visual Studio 2010 IDE (TODO how).

## Downloading dependencies
The most convenient way of installing all of the 3rd-party dependencies, 
is to download it from Virtualbox OSE Git repository, on the Github.
Dependencies are sorted out into several archives, which are install by default to the root of the system drive. 
Such a measure is important, because in most of the cases configured paths must not contain spaces. 

**By the way, also they may need opposite slash directions from dependency to dependency,
when putting the path in configuration. It could be explained with an enormous legacy of a product, 
different libraries have been added on different stages of the project development, 
some before acquiring Sun Microsystems by Oracle, which might be affected everything 
from coding style to development approach.**

Naming conventions keep POSIX style - binaries are installed in C:/bin, libraries in C:/lib. 
If you already had these directories, Virtualbox dependencies are simple being added 
to other applications and libraries. Qt is also installed into its default path, 
C:\Qt, alongside with other, usually newest versions of that framework. 
If you have several versions of Qt installed, avoid hardcode it in the PATH environment variable.
Some components, like BIOS, require legacy compiler WATCOM 1.9 to build, 
it could also be installed in the system drive root with setting some environment variables. 
Possibility of migration to WATCOM 2.0 is being discussed.
In summary, after installing 3rd party dependencies, you should have the following directories 
in your system drive root:

* C:/bin
* C:/lib
* C:/Qt
* C:/WATCOM

## Compiling dependencies from the scratch

You may need this for numerous reasons, the most obvious, you may want to update some of dependencies 
before the maintenance of binary archives happen, or you may want to create your own build 
of any number of dependencies. 
Toolset of applications and compilers becomes here way more diverse, and compilation of prerequisites
takes significant amount of time even on a powerful multi-core CPU, simply because of the every step requires
a number of pre- and post-build configuration steps.
Good news here we tried to automate pre-configuration of a build system as much as possible, 
same as it was done with pre-built dependencies, not so good news are that this kind of automation 
taking in account much larger number of unknown variables and side-effects, 
therefore smooth compilation of all 3rd parties is not guaranteed. However, every automated step
is associated with detailed explanation of manual build process, and vice versa. 
We encourage you to leave pull request if any bug found, or you want just to improve the automation toolchain. 

## Set up local variables

## Differences with official codebase
It's hard to say, why all these changes are necessary. It means, it's all should work on 
Oracle Continuous Integration Infrastructure, but fail to run locally in some specific circumstances. 
Most of them are insignificant changes, most probably caused by differences with a closed parts of a build toolchain, 
making some steps of configuration (building, testing and deployment) unknown to us. 
However, some improvements also included in the build process.

## Create symlinks without whitespaces
As mentioned earlier, Oracle building infrastructure configuration does not included into Virtualbox codebase.
You may only build assumptions how it should work, and try to create build configuration as close as possible.
However, no one guarantee that new even minor release would not break your building process because of slightest
differences in configurations and/or scripts.

Historically, whitespaces in Windows paths keep staying one of dangerous points of building process. 
Working fine most of the time, it may stop working with introducing new feature, like it happened with migrating 
Virtualbox Guest Additions compiler to Visual C++ 12.0 (part of Visual Studio 2013). Build system refused to 
recognize paths with whitespaces, which is basically everything installed into `C:/Program Files`. 
Installation into the altername location does not work completely as expected, since some files are installed
into `C:/Program Files` anyway, which seems to be path hardcoding example.

Reasonable solution to the problem appeared to be creating symlinks to any solution including SDK, DDK and IDE, 
installed into `C:/Program Files`. Creating symlinks from default installation place to directories in `C:/` root 
shorten paths in configuration files, makes it more readable and protect against any whitespace problems in future.

List of symlinks you are strongly advised to create:

* Windows SDK 7.1 `C:/Program Files/Microsoft SDKs/Windows/v7.1` to `C:/WinSDK/7.1`
* Windows SDK 8.1 `C:/Program Files (x86)/Windows Kits/8.1` to `C:/WinSDK/8.1`
* Microsoft DirectX SDK `C:/Program Files (x86)/Microsoft DirectX SDK` to `C:/WinSDK/DirectX`
* Windows DDK 8.0 `C:/Program Files (x86)/Windows Kits/8.0` to `C:/WinDDK/8.0`
* Visual Studio 2010 `C:/Program Files (x86)/Microsoft Visual Studio 10.0` to `C:/MSVS/10.0`
* Visual Studio 2013 `C:/Program Files (x86)/Microsoft Visual Studio 12.0` to `C:/MSVS/12.0`

Windows DDK 7.1 is installed into the root directory by default, so make sense to put it near Windows DDK 8.0,
for example `C:/WinDDK/7600.16385.1`

## Compile Virtualbox

## Compile Guest Additions

## Run and debug Virtualbox

