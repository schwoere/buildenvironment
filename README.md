buildenvironment
================

Clone this repository if you want to build Ubitrack from the source code.

All other repositories are added as submodules to this repository

How to build Ubitrack
---------------------

###1. Required tools
In order to build ubitrack the following tools have to be installed:

- Git

- C++ Compiler like gcc or MSVC

- Python (at least Version 2.7, e.g. [PythonXY](http://code.google.com/p/pythonxy/)  )

- [Scons](http://www.scons.org/)

You can compile Ubitrack for Windows, Linux and Android on the appropriate system. However, the cross-compilation for Android is only possible on Linux. If there are any differences to the compilation for the Linux, they are explained in the appropriate Linux sections.


###1.1 How to install build-tools
<TODO: WINDOWS>


**Ubitrack-Compilation on Linux**

Ubuntu is shipped with gcc and python pre-installed, to install Git and Scons, open up a terminal and type:

    sudo apt-get install git scons
**Ubitrack-Compilation for Android on Linux**

In addition to scons, for the compilation of Ubitrack for Android we need to compile an Android standalone-toolchain.
Therefore, you have to download the newest [Android-NDK](http://developer.android.com/tools/sdk/ndk/index.html) and extract it in a folder of your choice.

###2. Setting up buildenvironment
Change your current directory to the folder where you want to clone ubitrack.

    git clone https://github.com/Ubitrack/buildenvironment.git ubitrack
    cd ubitrack

###3. Adding additional components as submodules
There are several components represented by repositories which can be added as submodules to ubitrack. These components have to be placed in the  \<ubitrack\>/modules folder. Usually you will need utcore, utvision, utdataflow, utfacade, utcomponents and utvisioncomponents:

<table>
  <tr>
    <th>Component</th><th>Description</th><th>Repository</th>
  </tr>
  <tr>
    <td>utvisualization</td><td>contains a standalone 3D rendering module. Mostly for quick visualizations and debugging</td><td>https://github.com/Ubitrack/utvisualization.git</td>
  </tr>
  <tr>
     <td>utvisioncomponents</td><td>contains algorithms working on (mostly) camera images.</td><td>https://github.com/Ubitrack/utvisioncomponents.git</td>
  </tr>

  <tr>
     <td>utfacade</td><td>contains frontend adapters for ubitrack dataflow networks. Also contains the utConsole.</td><td>https://github.com/Ubitrack/utfacade.git</td>
  </tr>

  <tr>
     <td>utdataflow</td><td>contains the dataflow network and related graph methods.</td><td>https://github.com/Ubitrack/utdataflow.git</td>
  </tr>

  <tr>
     <td>utcore</td><td>contains fundamental datastructures and algorithms. Needed by all other modules.</td><td>https://github.com/Ubitrack/utcore.git</td>
  </tr>

  <tr>
     <td>utcomponents</td><td>contains basic dataflow modules. These cover most common tracking and registration cases.</td><td>https://github.com/Ubitrack/utvisioncomponents.git</td>
  </tr>

  <tr>
     <td>directshow</td><td>contains MS Windows specific directshow framegrabber</td><td>https://github.com/Ubitrack/directshow.git</td>
  </tr>

  <tr>
     <td>buildenvironment</td><td> contains general build scripts </td><td>https://github.com/Ubitrack/utvisualization.git</td>
  </tr>

</table>
You can pick the submodules manually or alternatively add all existing components at once by executing a script.
####3.a Manually adding submodules
This example will create the \<ubitrack\>/module/utcore directory and add utcore as submodule.

    git submodule add https://github.com/Ubitrack/utcore.git modules/utcore


####3.b Automized adding submodules
<scripts that will do that for you in {YourDirectory}/misc/setup/[windows|linux]>

**Ubitrack-Compilation for Windows:**

For Windows open a Git Console, change to the <ubitrack> and execute

    misc/setup/windows/addStandardModules.bat

**Ubitrack-Compilation on Linux:**

In order to add all components, just execute the following script for linux:

    sh misc/setup/linux/addStandardModules.sh





###4. Configure the needed libraries
Ubitrack is based on the following libraries:

Mandatory

- [Boost](http://www.boost.org/) (> 1.35)

Optional 

- Lapack

- [OpenCV](http://opencv.org/)

- Glut (or [Freeglut](http://freeglut.sourceforge.net/))

These libraries have to be downloaded and configured. In order to do that there exist 3 ways, the first one described in 4.1 offers ready-to-use download packages and uses the Ubitrack library finder, which needs all the libraries in one specific folder. 4.2 configures the libraries with the command line and in 4.3 each possible configuration and path is set manually by editing a textfile. 


####4.1 Make use of the Ubitrack library finder

Ubitrack is able to find libraries which have a specific folder structure that looks like this:


    <LibrariesDirectory>/[linux|windows|android]_[x64|x86]/LibraryName/[bin|include|lib|lib_debug]. 


An example for windows would be:

    external_libraries/windows_x64/boost
    external_libraries/windows_x64/boost/include
    external_libraries/windows_x64/boost/lib
    external_libraries/windows_x64/boost/lib_debug
     

The Ubitrack library finder will take all library files in the "lib" and "lib_debug" folders and link to their paths. So you have to separate the release and debug libraries in different folders.

**Ubitrack-Compilation for Windows:**


You can download ready-to-use library packages for windows and extract them e.g. in the \<ubitrack\> folder:

- [Minimal (Boost and Lapack)](http://campar.in.tum.de/personal/pankratz/UbiTrack/external_libraries_min.zip)
- [All (Boost,Lapack, OpenCV, Freeglut)](http://campar.in.tum.de/personal/pankratz/UbiTrack/external_libraries_all.zip)

This results in a structure that should look like this:
    
    <ubitrack>/external_libraries/windows_x64/
    <ubitrack>/external_libraries/windows_x86/


**Ubitrack-Compilation for Linux on Linux:**

If you haven't already done, yet, you can download and install BOOST, LAPACK and Freeglut with the terminal:

    sudo apt-get update
    sudo apt-get install libboost-all-dev libblas-dev liblapack-dev libopencv-dev freeglut3-dev
As these libraries are installed to the /usr/lib/ folder, an easy way to result in the Ubitrack library finder required folder structure is to link from the external\_libraries folder to the /usr/local/include/. This example shows how it is done for linux\_x64-architecture:

    cd /path/to/external_libraries
    mkdir -p linux_x64/boost/lib/
    mkdir -p linux_x64/boost/include/


    //linking for boost
    ln -s /usr/lib/libboost_filesystem.* boost/include/lib/
    ln -s /usr/lib/libboost_program_options.* boost/include/lib/
    ln -s /usr/lib/libboost_regex.* boost/include/lib/
    ln -s /usr/lib/libboost_serialization.* boost/include/lib/
    ln -s /usr/lib/libboost_system.* boost/include/lib/
    ln -s /usr/lib/libboost_thread.* boost/include/lib/

    ln -s /usr/include/boost boost/include/include/

    //linking for lapack
    mkdir -p lapack/lib
    ln -s /usr/lib/lapack/liblapack.a lapack/lib/
    ln -s /usr/lib/lapack/liblapack.so lapack/lib/
    ln -s /usr/lib/lapack/libblas.a lapack/lib/
    ln -s /usr/lib/lapack/llibblas.so lapack/lib/

    //linking for freeglut
    mkdir -p glut/lib
    mkdir -p glut/include
    ln -s /usr/lib/x86_64-linux-gnu/libglut* glut/lib/
    ln -s /usr/include/GL/ glut/include/

For OpenCV have a look at the official Guide [OpenCV Installation in Linux](http://docs.opencv.org/doc/tutorials/introduction/linux_install/linux_install.html) or a special Ubuntu-Guide [OpenCV on Ubuntu](https://help.ubuntu.com/community/OpenCV). After that, the libraries and includes can be linked to the external\_libraries folder:

    //linking for opencv
    mkdir -p opencv/lib
    mkdir -p opencv/include
**Ubitrack-Compilation for Android on Linux:**

In order to build Ubitrack for Android, you have to place BOOST, LAPACK and OpenCV for Android in the external\_libraires\android folder.

BOOST for Android:

    mkdir -p android/boost

If you have placed the libraries in a different folder to < ubitrack >/external\_libraries, you have to specify the path where Ubitrack has to search for the libraries. This can be done by executing the following command in the < ubitrack > folder:

    scons EXTERNAL_LIBRARIES=/home/user/path/to/all/external/libraries/


Alternatively, you can execute

    scons

and afterwards add the following line manually to the < ubitrack >/config.cache file:

    EXTERNAL_LIBRARIES = '/home/user/path/to/all/external/libraries/'
Note: The first time this document may be empty.


Taking similar steps, you can easily extend Ubitrack with additional libraries for your own need.

####4.2 Configure the libraries using command line options and library finder

In this option the library configuration is done by appending parameters to a scons call similar to the previous section. This method gives you the ability to define your own library-folder structure as you may have already installed some of the libraries in different directories. Just like in the previous section, you can write the paramerters into a \<ubitrack\>\config.cache document, alternatively.

The basic syntax for these parameters looks like this:

    {LIBNAME}_{PARAMERTER}_{PLATFORM}_{CONFIGURATION}

Examples:

    {LIBNAME}: 

     - BOOST, OPENCV, LAPACK, GLUT



    {PARAMETER}: 

    - INCLUDEPATH (path to include files)

    - LIBPATH (path to library files)

    - LIBS (comma separated list of library files to link against)

    - DEFINES (C++ defines passed to the compiler )



    {PLATFORM}: 

    - x64 (64bit)

    - x86 (32bit)

    - android (armeabi-v7a)



    {CONFIGURATION}:

    - RELEASE (in this case empty) 

    - DEBUG

Full Examples for library configurations by a scons call:

    scons BOOST_LIBPATH=/path/to/boost/lib

    scons BOOST_LIBPATH_DEBUG=/path/to/boost/lib_debug

    scons BOOST_LIBPATH_X86_DEBUG=/path/to/boost/x64/debug

    scons BOOST_LIBPATH_ANDROID=/path/to/boost/android/lib

    scons OPENCV_LIBS="opencv_stitching242.lib, opencv_legacy242.lib"

####4.3 Set everything by hand

Run the scons command once. After that the library configurations are stored in \<ubitrack\>/config/configStorage. Edit the files and set Include/Library Paths and Libraries to link against. 

Set havelib to true

add "HAVE_{LIBNAME}" to CPPDEFINES

Example:

[x64_release]
havelib = true
cpppath = ["C:\\Libraries\\freeglut\\freeglut-2.8.0\\include"]
libpath = ["C:\\Libraries\\freeglut\\freeglut-2.8.0\\lib\\x64"]
libs = ["freeglut.lib"]
cppdefines = ["HAVE_GLUT"]

###5. Compile Ubitrack
Run:

    scons install-all
Speed up the build process for parallel builds:

    scons install-all -j{NumProcessors} 
Clean the build by calling:

    scons -c
You can see all command line parameters by calling:

    scons -h

Create a Visual Studio Project:

    scons vcproj
