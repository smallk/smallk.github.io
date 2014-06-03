---
layout: page
title: <u>Installation</u>

baselink: /documentation/
permalink: /documentation/installation/
---

<h1 id="top">Contents</h1>
---------------------

*   [How to Install Elemental on MacOSX](#MacOSX)
    *   [Mac:Install the latest GNU compilers](#mac_GNU)
    *   [Mac:Install OpenMPI](#mac_open_mpi)
    *   [Mac:Install libFlame](#mac_libflame)
    *   [Mac:Install Elemental](#mac_elemental)
*   [How to Install Elemental on Linux](#Linux)
    *   [Linux:Install the latest GNU compilers](#lin_GNU)
    *   [Linux:Install OpenMPI](#lin_open_mpi)
    *   [Linux:Install libFlame](#lin_libflame)
    *   [Linux:Install BLAS Library](#lin_blas)
    *   [Linux:Install Elemental](#lin_elemental)
*   [SmallK NMF Library Installation](#smalk)
    *   [Obtain the source code](#source_code)
    *   [Build the SmallK library](#build_smallk)
    *   [Examples of API Usage](#example_api)
    *   [Matrix file formatsI Usage](#matrix_files)
    *   [SmallK APII Usage](#smallk_api)
*   [Contact Information](#contact)




<h1 id="MacOSX"> How to Install Elemental on MacOSX </h1>
On MacOSX we recommend using [Homebrew](http://mxcl.github.io/homebrew/) as the package manager. Homebrew does not require sudo privileges for package installation, unlike other package managers such as MacPorts. Thus the chances of corrupting vital system files are greatly reduced with Homebrew.
If you use Homebrew, ensure that your PATH is configured to search Homebrew’s installation directory first. Homebrew’s default installation location is /usr/local/bin, so that location needs to be first on your path. To check, run this command from a terminal window:
	cat /etc/paths

We also recommend running the following commands on a daily basis to refresh your brewed installations:

	brew update
	brew upgrade
	brew doctor

This will maintain your Homebrew installed software and diagnose any issues with the installations.
If the first entry is not /usr/local/bin, you will need to edit this file.  This is a system file, so first create a backup. Move the line /usr/local/bin so that it is on the first line of the file. Save the file, then close the terminal session and start a new terminal session so that the path changes will take effect.

[--back to top--](#top)
<h2 id="mac_GNU"> Mac:Install the latest GNU compilers </h2>
Elemental and SmallK both require a modern C++ compiler compliant with the C++11 standard.  We recommend that you install the latest stable version of the clang and GNU C++ compilers.  To do this, first install the XCode command line tools with this command:	xcode-select --install
If this command produces an error, download and install XCode from the AppStore, then repeat the command.  If that should still fail, install the command line tools from the XCode preferences menu.  After the installation completes, run this command from a terminal window:
	clang++ --version
You should see output similar to this:	Apple LLVM version 5.1 (clang-503.0.40) (based on LLVM 3.4svn)	Target: x86-64-apple-darwin13.2.0	Thread model: posixThe latest version of the GNU compiler at the time of writing is g++-4.9.  This can be installed with Homebrew as follows:
 	brew tap homebrew/versions
	brew install gcc49 --enable-fortran
The Apple-provided gcc and g++ will not be overwritten by this installation.  The new compilers will be installed into /usr/local/bin as gcc-4.9, g++-4.9, and gfortran-4.9. The Fortran compiler is needed for the installation of MPI.

[--back to top--](#top)
<h2 id="mac_open_mpi"> Mac:Install OpenMPI </h2>
Install the latest version of OpenMPI with Homebrew as follows:	brew install open-mpi –-c++11The Homebrew install formula provides an option for “thread-multiple” support, but do not enable this option, as it is still experimental, not optimized for performance, and may have bugs.

[--back to top--](#top)

<h2 id="mac_libflame"> Mac:Install libFlame </h2>
Next we detail the installation of the high performance numerical library libflame. The library can be gotten from the libflame git repository on github.

It’s important to perform the git clone into a subdirectory NOT called ‘flame’ since this can cause name conflicts with the installation. We normally do a git clone into a directory called ‘libflame’. However, other directory names will work as well, but not ‘flame’.

To obtain the latest version of the FLAME library, clone the FLAME git repository with this command:
	git clone https://github.com/flame/libflame.git
Run the configure script in the top-level FLAME folder as follows (assuming the install path is /usr/local/flame):
	./configure –-prefix=/usr/local/flame –-with-cc=/usr/local/bin/gcc-4.9 –-with-ranlib=/usr/local/bin/gcc-ranlib-4.9
A complete list of configuration options can be obtained by running ./configure –-help.

After the configuration process completes, build the FLAME library as follows:
	make –j4

The –j4 option tells Make to use four processes to perform the build.  This number can be increased if you have a more capable system.

	make install

The FLAME library is now installed.
[--back to top--](#top)
<h2 id="mac_elemental"> Mac:Install Elemental </h2>

We strongly recommend that users install both the HybridRelease and PureRelease builds of [Elemental](http://libelemental.org/).  OpenMP is enabled in the HybridRelease build and disabled in the PureRelease build.  So why install both?  For smaller problems the overhead of *OpenMP can actually cause code to run slower* than without it.  Whereas for large problems OpenMP parallelization generally helps, but there is no clear transition point between where it helps and where it hurts.  Thus we encourage users to experiment with both builds to find the one that performs best for their typical problems.

We also recommend that users clearly separate the different build types as well as the versions of Elemental on their systems.  Elemental is under active development, and new releases can introduce changes to the API that are not backwards-compatible with previous releases.  To minimize build problems and overall hassle, we recommend that Elemental be installed so that the different versions and build types are cleanly separated.

Thus, two versions of Elemental need to be built.  One is a hybrid release build with OpenMP parallelization, and the other is the pure release build without OpenMP parallelization.  A separate build folder will be created for each build.  The build that uses internal OpenMP parallelization is called a ‘HybridRelease’ build; the build that doesn’t is called a ‘PureRelease’ build.  The debug build is called a ‘PureDebug’ build.  The HybridRelease build is best for large problems, where the problem size is large enough to overcome the OpenMP parallel overhead. The following is for the 0.83 version of elemental. Set the version to that specified in the README.html file. Note that the files will be installed in **/usr/local/elemental/[version]/[build type]**.


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


Download the 0.83 release of [Elemental](http://libelemental.org/releases/), unzip and untar the distribution, and cd to the top-level folder.

**1.1.**  Run these commands to create the required directories for the build types:
		mkdir build_hybrid
		mkdir build_pure

[--back to top--](#top)
###Perform the HybridRelease build
**1.2.** The HybridRelease version will be built first.
		cd build_hybrid
**1.3.** Configure the Elemental HybridRelease build with this command (all one line):
		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.83/HybridRelease -D CMAKE_BUILD_TYPE=HybridRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate"  ..
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

[--back to top--](#top)
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

[--back to top--](#top)

<h1 id="Linux"> How to Install Elemental on Linux </h1>

We strongly recommend using a package manager for your Linux distribution for installation and configuration of the required dependencies.  We cannot provide specific installation commands for every variant of Linux, so we specify the high-level steps below.

<h2 id="lin_GNU"> Linux:Install the latest GNU compilers </h2>
We recommend installation of the latest stable release of the GNU C++ compiler, which is g++-4.9 at the time of this writing. Also install the latest version of GNU Fortran, which is needed for the installation of MPI. 
<h2 id="lin_open_mpi"> Install OpenMPI </h2>
Download the latest version of [OpenMPI](http://www.open-mpi.org/), unzip and untar the downloaded zip file, and cd to the untarred directory.  Run configure as follows, all on a single line.  This command assumes that gcc-4.9 has been installed; change the version number if a later version was installed, or if the paths are incorrect for your system:

		./configure --enable-mpi-thread-multiple --prefix=/usr/local  CC=/usr/local/bin/gcc-4.9 CXX=/usr/local/bin/g++-4.9 F77=usr/local/bin/gfortran-4.9 FC=/usr/local/bin/gfortran-4.9

Wait for the configure script to finish – this could take several minutes. Then build the code as follows:
		make -j4
Install with:
		make install
OpenMPI provides many tests to verify the installation; it’s a good idea to run at least some of these tests to ensure that MPI was installed successfully.

[--back to top--](#top)

<h2 id="lin_libflame"> Linux:Install libFlame </h2>

Next we detail the installation of the high performance numerical library libflame. The library can be gotten from the libflame git repository on github.

It’s important to perform the git clone into a subdirectory NOT called ‘flame’ since this can cause name conflicts with the installation. We normally do a git clone into a directory called ‘libflame’. However, other directory names will work as well, but not ‘flame’.

To obtain the latest version of the FLAME library, clone the FLAME git repository with this command:
		git clone https://github.com/flame/libflame.git
Run the configure script in the top-level FLAME folder as follows (assuming you want to install to /usr/local/flame; if not, change the prefix path):

		./configure --prefix=/usr/local/flame --with-cc=/usr/local/bin/gcc-4.9 --with-ranlib=/usr/local/bin/gcc-ranlib-4.9
Then build and install the code as follows:
		make -j4
		make install

This completes the installation of the FLAME library.

[--back to top--](#top)

<h2 id="lin_blas"> Linux:Install an accelerated BLAS library </h2>

It is essential to link Elemental with an accelerated BLAS library for maximum performance.  Linking Elemental with a ‘reference’ BLAS implementation will cripple performance, since the reference implementations are designed for correctness not speed.

If you do not have an accelerated BLAS on your system, you can download and build [OpenBLAS](http://www.openblas.net/).  Download, unzip, and untar the tarball (version 0.2.8 as of this writing) and cd into the top-level folder.  Build OpenBLAS with this command, assuming you have a 64-bit system:

		make BINARY=64 USE_OPENMP=1

Install with this command, assuming the installation directory is /usr/local/openblas/0.2.8/:

		make PREFIX=/usr/local/openblas/0.2.8/ install

[--back to top--](#top)
<h2 id="lin_elemental"> Linux:Install Elemental </h2>

We strongly recommend that users install both the HybridRelease and PureRelease builds of Elemental.  OpenMP is enabled in the HybridRelease build and disabled in the PureRelease build.  So why install both?  Because for smaller problems the overhead of OpenMP can actually cause code to run slower than without it.  For large problems OpenMP parallelization generally helps, but there is no clear transition point between where it helps and where it hurts.  Thus we encourage users to experiment with both builds to find the one that performs best for their typical problems.

We also recommend that users clearly separate the different build types as well as the versions of Elemental on their systems.  Elemental is under active development, and new releases can introduce changes to the API that are not backwards-compatible with previous releases.  To minimize build problems and overall hassle, we recommend that Elemental be installed so that the different versions and build types are cleanly separated.

Thus, two versions of Elemental need to be built.  One is a hybrid release build with OpenMP parallelization, and the other is the pure release build without OpenMP parallelization.  A separate build folder will be created for each build.  The build that uses internal OpenMP parallelization is called a ‘HybridRelease’ build; the build that doesn’t is called a ‘PureRelease’ build.  The debug build is called a ‘PureDebug’ build.  The HybridRelease build is best for large problems, where the problem size is large enough to overcome the OpenMP parallel overhead. The following is for the 0.83 version of elemental. Set the version to that specified in the README.html file. Note that the files will be installed in **/usr/local/elemental/[version]/[build type]**.

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


Download the 0.83 release of [Elemental](http://libelemental.org/releases/), unzip and untar the distribution, and cd to the top-level folder.

[--back to top--](#top)

**2.1.**  Run these commands to create the required directories for the build types:
		mkdir build_hybrid
		mkdir build_pure

###Perform the HybridRelease build

**2.2** Change to the build_hybrid directory.
		cd build_hybrid
**2.3** Configure the Elemental HybridRelease build with this command (all one line):
		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.83/HybridRelease -D CMAKE_BUILD_TYPE=HybridRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-L/usr/local/openblas/0.2.8/ –lopenblas –lm" –D ELEM_EXAMPLES=ON –D ELEM_TESTS=ON  ..
If this command does not work, you may need to define the BLAS_LIBS and/or GFORTRAN_LIB config options.
**2.4** Build and install the code as follows:
		make -j4
		make install
**2.5** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental/0.81/HybridRelease/conf/
**2.6** Open the ‘elemvariables’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.9
change it to
		CXX = /usr/local/bin/g++-4.9 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.

[--back to top--](#top)

###Perform the PureRelease build
**2.7** Change to the build_pure directory.
		cd build_pure
**2.8** Configure the Elemental PureRelease build with this command (all one line):

		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.83/PureRelease -D CMAKE_BUILD_TYPE=PureRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-L/usr/local/openblas/0.2.8/ –lopenblas –lm" –D ELEM_EXAMPLES=ON –D ELEM_TESTS=ON  ..

If this command does not work, you may need to define the BLAS_LIBS and/or GFORTRAN_LIB config options.
**2.9** Build and install the code as follows:
		make -j4
		make install
**2.10** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental-0.81-PureRelease/conf/
**2.11** Open the ‘elemvariables’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.9
change it to
		CXX = /usr/local/bin/g++-4.9 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.This completes the builds of Elemental.

**2.12** To test the installation, follow Elemental’s [test instructions](http://libelemental.org/documentation/0.83/build.html) for the SVD test to verify that Elemental is working correctly.

[--back to top--](#top)

<h1 id="smallk"> SmallK NMF Library Installation </h1>

<h2 id="source_code"> Obtain the Source Code </h2>
The source code for the SmallK library can be obtained by cloning the [SmallK](https://github.com/smallk/smallk.github.io) repository on github.

<< Describe how to do this – TBD >>

<h2 id="build_smallk"> Build the SmallK library </h2>

After cloning the repo cd into the top-level SmallK folder.  The makefiles assume that you followed our suggested installation plan for Elemental.  If this is not the case you will need to do one of the following things:
	1. Create an environment variable called ELEMENTAL_INSTALL_DIR which contains the 
		path to the root folder of your Elemental installation
	2. Define the variable ELEMENTAL_INSTALL_DIR on the make command line
	3. Edit the SmallK makefile so that it can find your Elemental installation
Assuming that the default install locations are acceptable, build the SmallK code by running this command from the root folder of the distribution:
		make all
This will build the SmallK library and several command-line applications.  These are:
	1. libsmallk.a, the SmallK library
	2. preprocess_tf, a command-line application for processing and scoring term-frequency matrices
	3. matrixgen, a command-line application for generating random matrices
	4. nmf, a command-line application for NMF
	5. hierclust, a command-line application for fast hierarchical clustering
	6. flatclust, a command-line application for flat clustering via NMF
To install the code, run this command to install to the default location, which is /usr/local/smallk:
		make install
To install the code to a different location, either create an environment variable called SMALLK_INSTALL_DIR and set it equal to the desired installation location prior to running the install command, or supply a prefix argument:
		make prefix=/path/to/smallk  install
Or, as a last resort, you can edit the top-level SmallK makefile to conform to the installation scheme of your system.  You may need root privileges to do the installation, depending on where you choose to install it.To test the installation, run this command:
		make check
This will run a series of tests, none of which should report a failure.  Sample output from a run of these tests can be found in section [SmallK Test Results](http://smallk.github.io/documentation/tests/#smalk_tests).
The command-line applications can be built individually by running the appropriate make command from the top-level smallk folder.  These commands are:
	To build the smallk library only: 	make libsmallk
	To build the preprocessor only:		make preprocessor
	To build the matrix generator only:	make matrixgen
	To build the nmf only:			make nmf
	To build hierclust only:		make hierclust
	To build flatclust only:		make flatclustThis completes the SmallK NMF library installation.

[--back to top--](#top)

<h2 id="example_api"> Examples of API Usage </h2>

In the examples folder you will find a file called smallk_example.cpp. This file contains several examples of how to use the SmallK library.  Also included in the examples folder is a makefile that you can customize for your use.  Note that the SmallK library must first be installed before the example project can be built.

<h2 id="matrix_files"> Matrix file formats </h2>

The SmallK software supports comma-separated value (CSV) files for dense matrices and MatrixMarket files for sparse matrices.

For example, the 5x3 dense matrix

		42	47	52
		43	48	53
		44	49	54
		45	50	55
		46	51	56

would be stored in a CSV file as follows:
	
		42,47,52
		43,48,53
		44,49,54
		45,50,55
		46,51,56

The matrix is loaded exactly as it appears in the file.  Internally, SmallK stores dense matrices in column-major order.  Sparse matrices are stored in compressed column format.

<h2 id="smallk_api"> SmallK API </h2>

The SmallK API is an extremely simplistic API for basic NMF and clustering.  Users who require more control over the factorization or clustering algorithms can instead run one of the command-line applications in the SmallK distribution.Documentation for the SmallK API can be found here: 
<b> >>Under Construction<< </b>

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




