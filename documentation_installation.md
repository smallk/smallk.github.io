---
layout: page
title: Installation

baselink: /documentation/
permalink: /documentation/installation/
---

<h1 id="top">Contents</h1>
---------------------

*   [How to Install Elemental on MacOSX](#MacOSX)
    *   [Install the latest GNU compilers](#mac_GNU)
    *   [Install OpenMPI](#mac_open_mpi)
    *   [Install libFlame](#mac_libflame)
    *   [Install Elemental](#mac_elemental)
*   [How to Install Elemental on Linux](#Linux)
    *   [Install the latest GNU compilers](#lin_GNU)
    *   [Install OpenMPI](#lin_open_mpi)
    *   [Install libFlame](#lin_libflame)
    *   [Install Elemental](#lin_elemental)
*   [Contact Information](#contact)

<h1 id="MacOSX"> How to Install Elemental on MacOSX </h1>

On MacOSX we recommend using [Homebrew](http://mxcl.github.io/homebrew/) as the package manager. Homebrew does not require sudo privileges for package installation, unlike other package managers such as MacPorts. Thus the chances of corrupting vital system files are greatly reduced with Homebrew.
If you use Homebrew, ensure that your PATH is configured to search Homebrew’s installation directory first. Homebrew’s default installation location is /usr/local/bin, so that location needs to be first on your path. To check, run this command from a terminal window:
	cat /etc/paths
If the first entry is not /usr/local/bin, you will need to edit this file.  This is a system file, so first create a backup. Move the line /usr/local/bin so that it is on the first line of the file. Save the file, then close the terminal session and start a new terminal session so that the path changes will take effect.

[back to top](#top)
<h2 id="mac_GNU"> Install the latest GNU compilers </h2>
Elemental and SmallK both require a modern C++ compiler compliant with the C++11 standard.  We recommend that you install the latest stable version of the clang and GNU C++ compilers.  To do this, first install the XCode command line tools with this command:	xcode-select --install
If this command produces an error, download and install XCode from the AppStore, then repeat the command.  If that should still fail, install the command line tools from the XCode preferences menu.  After the installation completes, run this command from a terminal window:
	clang++ --version
You should see output similar to this:	Apple LLVM version 5.1 (clang-503.0.40) (based on LLVM 3.4svn)	Target: x86-64-apple-darwin13.2.0	Thread model: posixThe latest version of the GNU compiler at the time of writing is g++-4.9.  This can be installed with Homebrew as follows:
 	brew tap homebrew/versions
	brew install gcc49 --enable-fortran
The Apple-provided gcc and g++ will not be overwritten by this installation.  The new compilers will be installed into /usr/local/bin as gcc-4.9, g++-4.9, and gfortran-4.9. The Fortran compiler is needed for the installation of MPI.

[back to top](#top)
<h2 id="mac_open_mpi"> Install OpenMPI </h2>
Install the latest version of OpenMPI with Homebrew as follows:	brew install open-mpi –-c++11The Homebrew install formula provides an option for “thread-multiple” support, but do not enable this option, as it is still experimental, not optimized for performance, and may have bugs.

[back to top](#top)

<h2 id="mac_libflame"> Install libFlame </h2>
To obtain the latest version of the FLAME library, clone the FLAME git repository with this command:
	git clone https://github.com/flame/libflame.git
Run the configure script in the top-level FLAME folder as follows (assuming the install path is /usr/local/flame):
	./configure –-prefix=/usr/local/flame –-with-cc=/usr/local/bin/gcc-4.9 –-with-ranlib=/usr/local/bin/gcc-ranlib-4.9
A complete list of configuration options can be obtained by running ./configure –-help.

After the configuration process completes, build the FLAME library as follows:
	make –j4
	make install
[back to top](#top)
<h2 id="mac_elemental"> Install Elemental </h2>
Download the specified (check the README.html file) distribution of [Elemental](http://libelemental.org/), unzip and untar the distribution, and cd to the untarred directory.

Two versions of Elemental need to be built.  One is a hybrid release build with OpenMP parallelization, and one is the pure release build without OpenMP parallelization.  A separate build folder will be created for each build.  The build that uses internal OpenMP parallelization is called a ‘HybridRelease’ build; the build that doesn’t is called a ‘PureRelease’ build.  The debug build is called a ‘PureDebug’ build.  The HybridRelease build is best for large problems, where the problem size is large enough to overcome the OpenMP parallel overhead. The following is for the 0.83 version of elemental. Set the version to that specified in the README.html file. Note that the files will be installed in **/usr/local/elemental/[version]/[build type]**.


###Here is our suggested installation scheme for Elemental:###

Choose a folder for the root of the Elemental installation.  For our systems, this is

	/usr/local/elemental

At this point an environment variable can be created that points to the above directory, which will be used for the build. At the command line type

	export ELEMENTAL_INSTALL_DIR=/usr/local/elemental

Alternatively, the directory structure can be set up manually.
Inside this folder create a new folder named with the release version of Elemental:

	/usr/local/elemental/0.83/

Inside of this version folder, create two additional folders for each Elemental build type.  These should be named HybridRelease and PureRelease, to match Elemental's terminology.  Thus the final folder configuration is

	/usr/local/elemental/0.83/HybridRelease
	/usr/local/elemental/0.83/PureRelease

**1.1.**  Run these commands to create the required directories for the build types:
		mkdir build_hybrid
		mkdir build_pure
###Perform the HybridRelease build
**1.2.** The HybridRelease version will be built first.
		cd build_hybrid
**1.3.** Configure the Elemental HybridRelease build with this command (all one line):
		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.83/HybridRelease -D CMAKE_BUILD_TYPE=HybridRelease -D 		CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/		local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate"  ..
**1.4.** Build and install the code as follows:
		make -j4
		make install
**1.5.** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental/0.83/HybridRelease/conf/
**1.6.** Open the ‘elemvariables’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.9
change it to
		CXX = /usr/local/bin/g++-4.9 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.
###Perform the PureRelease build
**1.7.** Change directories to the untarred Elemental folder and run the following commands:
		cd build_pure
**1.8.** Configure the Elemental PureRelease build with this command (all one line):
		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.83/PureRelease -D CMAKE_BUILD_TYPE=PureRelease -D 		CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/		local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate"  ..
**1.9.** Build and install the code as follows:
		make -j4
		make install
**1.10.** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental-0.83-PureRelease/conf/
**1.11.** Open the ‘elemvariables’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.9
change it to
		CXX = /usr/local/bin/g++-4.9 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.This completes the two builds of Elemental.

**1.12.** To test the installation, follow Elemental’s test instructions for the SVD test to verify that Elemental is working correctly.

[back to top](#top)

<h1 id="Linux"> How to Install Elemental on Linux </h1>

We strongly recommend using a package manager for your Linux distribution for installation and configuration of the required dependencies.  We cannot provide specific installation commands for every variant of Linux, so we specify the high-level steps below.

<h2 id="lin_GNU"> Install the latest GNU compilers </h2>
Elemental requires a modern C++ compiler, and the Georgia Tech NMF library requires C++11. We recommend that you install the latest stable version of the GNU compilers (version 4.9 as of this writing) using the appropriate command for your Linux distribution and package manager.Also install the latest version of GNU Fortran, which is needed for the installation of MPI.

<h2 id="lin_open_mpi"> Install OpenMPI </h2>
Download the latest version of OpenMPI, unzip and untar the downloaded zip file, and cd to the untarred directory.  Run configure as follows, all on a single line.  This command assumes that gcc-4.8 has been installed; change the version number if a later version was installed, or if the paths are incorrect for your system:

		./configure --enable-mpi-thread-multiple --prefix=/usr/local  CC=/usr/local/bin/gcc-4.8 CXX=/usr/local/bin/g++-4.8 F77=usr/local/bin/gfortran  FC=/usr/local/bin/gfortran

Wait for the configure script to finish – this could take several minutes. Then build the code as follows:
		make -j4
Install with:
		make install
OpenMPI provides many tests to verify the installation; it’s a good idea to run at least some of these tests to ensure that MPI was installed successfully.

[back to top](#top)

<h2 id="lin_libflame"> Install libFlame </h2>

Download the latest version of libFlame, which at the time of this writing was libflame-r11488.tar.gz. Unzip and untar the distribution and cd to the untarred directory.Run configure as follows, suitably modifying the paths as appropriate for your system:
		./configure --prefix=/usr/local/flame --with-cc=/usr/local/bin/gcc-4.8  --with-ranlib=/usr/local/bin/gcc-ranlib-4.8
Then build and install the code as follows:
		make -j4		make install
<h2 id="lin_elemental"> Install Elemental </h2>

Download the specified (check the README.html file) distribution of Elemental, unzip and untar the distribution, and cd to the untarred directory.Three versions of Elemental need to be built.  One is a debug build, one is a release build with OpenMP parallelization, and one is a release build without OpenMP parallelization.  A separate build folder will be created for each build.  The build that uses internal OpenMP parallelization is called a ‘HybridRelease’ build; the build that doesn’t is called a ‘PureRelease’ build.  The debug build is called a ‘PureDebug’ build.  The HybridRelease build is best for large problems, where the problem size is large enough to overcome the OpenMP parallel overhead. The following is for the 0.81 version of elemental. Set the version to that specified in the README.html file. Note that the files will be installed in /usr/local/elemental/<version>/<build type>**2.1.**  Run these commands to create the required directories for the build types:
		mkdir localbuild_hybridrelease		mkdir localbuild_puredebug

###Perform the HybridRelease build

**2.2** Change to the localbuild_hybridrelease directory.
		cd localbuild_hybridrelease
**2.3** Configure the Elemental HybridRelease build with this command (all one line):
		cmake
			-D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.81/HybridRelease
 			-D CMAKE_BUILD_TYPE=HybridRelease
			-D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.8
			-D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.8
			-D CXX_FLAGS=”-std=c++11 –O3”
 			-D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran
			-D MATH_LIBS="/usr/local/flame/lib/libflame.a; -L/usr/local/lib –lopenblas -lm"
			-D ELEM_EXAMPLES=ON
			-D ELEM_TESTS=ON  ..
If this command does not work, you may need to define the BLAS_LIBS and/or GFORTRAN_LIB config options.
**2.4** Build and install the code as follows:
		make -j4		make install
**2.5** Edit the Elemental configuration file as follows:
	cd /usr/local/elemental/0.81/HybridRelease/conf/
**2.6** Open the ‘elemvariables’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.8
change it to
		CXX = /usr/local/bin/g++-4.8 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.

###Perform the PureRelease build
**2.7** Change to the localbuild_purerelease directory.
		cd localbuild_purerelease
**2.8** Configure the Elemental PureRelease build with this command (all one line):		cmake
			-D CMAKE_INSTALL_PREFIX=/usr/local/elemental-0.81-PureRelease
			-D CMAKE_BUILD_TYPE=PureRelease
			-D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.8
			-D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.8
			-D CXX_FLAGS=”-std=c++11 –O3”
			-D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran
			-D MATH_LIBS="/usr/local/flame/lib/libflame.a; -L/usr/local/lib –lopenblas -lm"
			-D ELEM_EXAMPLES=ON
			-D ELEM_TESTS=ON  ..
If this command does not work, you may need to define the BLAS_LIBS and/or GFORTRAN_LIB config options.
**2.9** Build and install the code as follows:
		make -j4		make install
**2.10** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental-0.81-PureRelease/conf/
**2.11** Open the ‘elemvariables’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.8
change it to
		CXX = /usr/local/bin/g++-4.8 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.This completes the builds of Elemental.

**2.12** To test the installation, follow Elemental’s test instructions for the SVD test to verify that Elemental is working correctly.

[back to top](#top)


Disclaimer
----------

This software is a work in progress.  It will be updated throughout the course of the 
XDATA program with additional algorithms and examples.  The distributed NMF 
factorization routine uses sequential algorithms, but it replaces the matrices and matrix 
operations with distributed versions.  The GA Tech research group is working on proper 
distributed NMF algorithms, and when such algorithms are available they will be added to 
the library.  Thus the performance of the distributed code should be viewed as being the
baseline for our future distributed NMF implementations.

<h2 id="contact">Contact Info</h2>
For comments, questions, bug reports, suggestions, etc., contact:

     Richard Boyd
     Senior Research Scientist
	 Cyber Technology and Information Security Laboratory
     Georgia Tech Research Institute
     250 14th St NW
     Atlanta, GA 30318
     richard.boyd@gtri.gatech.edu

     Barry Drake
     Senior Research Scientist
	 Cyber Technology and Information Security Laboratory
     Georgia Tech Research Institute
     250 14th St NW
     Atlanta, GA 30318
     barry.drake@gtri.gatech.edu Laboratory




