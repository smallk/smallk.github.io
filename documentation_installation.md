---
layout: page
title: Installation - Documentation

baselink: /documentation/
permalink: /documentation/installation/
---

Page under construction

<p class="body-header-links"><a href="#method-1">Method #1</a> | <a href="#method-2">Method #2</a> | <a href="#method-3">Method #3</a></p>

# How to Install Elemental on MacOSX
We strongly recommend using Homebrew as the Mac package manager. Homebrew does not require sudo privileges for package installation, unlike other package managers such as MacPorts. Thus the chances of corrupting vital system files are greatly reduced with Homebrew.If you use Homebrew, first make sure that your PATH is configured to search Homebrew’s installation directory first. Homebrew’s default installation location is /usr/local/bin, so that location needs to be first on your path. To check, run this command:	cat /etc/pathsIf the first entry is not /usr/local/bin, edit this file and move /usr/local/bin so that it is on the first line of the file. Save the file, then close the terminal session and start a new terminal session so that the path changes will take effect.

##Install the latest GNU compilers
Elemental requires a modern C++ compiler, and the Georgia Tech NMF library requires C++11. We recommend that you install the latest stable version of the GNU compilers (version 4.8.2 as of this writing). To install gcc-4.8, first install the XCode command line tools with this command:

		xcode-select --install

If this command produces an error message, download and install XCode from the AppStore, then repeat the command. The g++-4.8 compiler requires a specific version of ISL (v 0.11), which can be installed with this command:
 		brew install isl011
Then use Homebrew to install the latest version of g++-4.8 as follows:
 		brew tap homebrew/versions
		brew install gcc48 --enable-cxx --enable-fortran --enable-objc --enable-objcxx
The Apple-provided gcc and g++ will not be overwritten by this installation.  The new compilers will be installed as gcc-4.8, g++-4.8, etc.

Install the latest version of GNU fortran with this command:
		brew install gfortran
The Fortran compiler is needed for the installation of MPI.
##Install OpenMPI
Uninstall any existing Homebrew MPI installation. The reason for this is that Elemental can take advantage of MPI multithreading, but Homebrew does not provide an option for building MPI with multithreading support.
		brew uninstall open-mpi
Download the latest version of OpenMPI, unzip and untar the downloaded zip file, and cd to the untarred directory.  Run configure as follows, all on a single line (assuming gcc-4.8 has been installed; change the version number if a later version was installed):
		./configure --enable-mpi-thread-multiple --prefix=/usr/local  CC=/usr/local/bin/gcc-4.8 CXX=/usr/local/bin/g++-4.8 F77=/usr/local/bin/gfortran  FC=/usr/local/bin/gfortran
Wait for the configure script to finish – this could take several minutes. Then build the code as follows:
		make -j4
Install with:
		make install
OpenMPI provides many tests to verify the installation; it’s a good idea to run at least some of these tests to ensure that MPI was installed successfully.

##Install the latest version of libflame
Download the latest version of libFlame, which at the time of this writing was libflame-r9999.tar.gz. Unzip and untar the distribution and cd to the untarred directory.Run configure as follows:
		./configure --prefix=/usr/local/flame --with-cc=/usr/local/bin/gcc-4.8  --with-ranlib=/usr/local/bin/gcc-ranlib-4.8
Then build and install the code as follows:
		make -j4
		make install
##Install Elemental
Download the specified (check the README.html file) distribution of Elemental, unzip and untar the distribution, and cd to the untarred directory.

Three versions of Elemental need to be built.  One is a debug build, one is a release build with OpenMP parallelization, and one is a release build without OpenMP parallelization.  A separate build folder will be created for each build.  The build that uses internal OpenMP parallelization is called a ‘HybridRelease’ build; the build that doesn’t is called a ‘PureRelease’ build.  The debug build is called a ‘PureDebug’ build.  The HybridRelease build is best for large problems, where the problem size is large enough to overcome the OpenMP parallel overhead. The following is for the 0.81 version of elemental. Set the version to that specified in the README.html file. Note that the files will be installed in /usr/local/elemental/<version>/<build type>

**1.1.**  Run these commands to create the required directories for the build types:
		mkdir localbuild_hybridrelease
		mkdir localbuild_puredebug
###Perform the HybridRelease build
**1.2.** The HybridRelease version will be built first.
		cd localbuild_hybridrelease
**1.3.** Configure the Elemental HybridRelease build with this command (all one line):
		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.81/HybridRelease
 			-D CMAKE_BUILD_TYPE=HybridRelease
			-D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.8
 			-D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.8
			-D CXX_FLAGS=”-std=c++11 –O3”
			-D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran
			-D GFORTRAN_LIB=/usr/local/Cellar/gfortran/4.8.2/gfortran/lib/libgfortran.a
			-D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate"
			-D ELEM_EXAMPLES=ON
			-D ELEM_TESTS=ON  ..
**1.4.** Build and install the code as follows:
		make -j4
		make install
**1.5.** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental/0.81/HybridRelease/conf/
**1.6.** Open the ‘elemvariables’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.8
change it to
		CXX = /usr/local/bin/g++-4.8 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.
###Perform the PureRelease build
**1.7.** Change directories to the untarred Elemental folder and run the following commands:
		cd localbuild_purerelease
**1.8.** Configure the Elemental PureRelease build with this command (all one line):
		cmake
			-D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.81/PureRelease
			-D CMAKE_BUILD_TYPE=PureRelease
			-D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.8
			-D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.8
			-D CXX_FLAGS=”-std=c++11 –O3”
			-D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran
			-D GFORTRAN_LIB=/usr/local/Cellar/gfortran/4.8.2/gfortran/lib/libgfortran.a
			-D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate"
			-D ELEM_EXAMPLES=ON
			-D ELEM_TESTS=ON  ..
V1.9.** Build and install the code as follows:
		make -j4
		make install
**1.10.** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental-0.81-PureRelease/conf/
**1.11.** Open the ‘elemvariables’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.8
change it to
		CXX = /usr/local/bin/g++-4.8 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.This completes the two builds of Elemental.

**1.12.** To test the installation, follow Elemental’s test instructions for the SVD test to verify that Elemental is working correctly.