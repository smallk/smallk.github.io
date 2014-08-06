---
layout: page
title: Installation

baselink: /documentation/
permalink: /documentation/installation/
---

<h1 id="top">Contents</h1>
---------------------
*   [Quickstart: Vagrant Virtual Machine](#vagrant)
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
    *   [Matrix File Formats](#matrix_files)
    *   [SmallK API](#smallk_api)
        *    [Enumerations](#enums)
        * [API functions](#api_functions)
            * [Initialization and cleanup](#init_cleanup)
            * [Versioning ](#versions)
            * [Common functions](#common_funcs)
            * [NMF functions](#nmf_funcs)
*   [Contact Information](#contact)


<h1 id="vagrant"> Quickstart: Vagrant Virtual Machine </h1>
Installing SmallK into a virtual machine is intended for those who are not doing development and/or do not have a reason to do the full installation on Linux or OSX outlined in sections Mac:Install and Linux:Install.

The complete stack of software dependencies for SmallK as well as SmallK itself can be rapidly set up and configured through use of Vagrant and VirtualBox and the files included in the repository. To deploy the SmallK VM:

**1.** Install [Vagrant](http://www.vagrantup.com/downloads.html) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads).

**2.** From within the vagrant/ directory in the repository, run:
		
		vagrant up
		
This can take as long as an hour to build the VM, which will be based on a minimal Ubuntu 14.04 installation. The VagrantFile can be customized in many ways to change the specifications for the VM that is built. See more information [here](http://docs.vagrantup.com/v2/).

**3.** Once the VM has been built, run:

		vagrant ssh
		
This will drop you into the command line of the VM that was just created. From there, you can navigate to /home/vagrant/smallk-1.1.0 and run

		make check
		
to verify your installation was successful. In case you need it, the username/password for the VM created will be vagrant/vagrant.

**4.** When you are ready to shut down the VM, run one of the following:

		vagrant suspend # this command will save the current running state
		vagrant halt # this command will gracefully shut down the machine
		vagrant destroy #this command will remove the VM from your machine

If you want to work with the VM again, from any of the above states you can run

		vagrant up
		
again and the VM will be resumed or recreated.

[--back to top--](#top)

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

Thus, two versions of Elemental need to be built.  One is a hybrid release build with OpenMP parallelization, and the other is the pure release build without OpenMP parallelization.  A separate build folder will be created for each build.  The build that uses internal OpenMP parallelization is called a ‘HybridRelease’ build; the build that doesn’t is called a ‘PureRelease’ build.  The debug build is called a ‘PureDebug’ build.  The HybridRelease build is best for large problems, where the problem size is large enough to overcome the OpenMP parallel overhead. The following is for the 0.84 version of elemental. Set the version to that specified in the README.html file. Note that the files will be installed in **/usr/local/elemental/[version]/[build type]**.


###Here is our suggested installation scheme for Elemental:###

Choose a folder for the root of the Elemental installation.  For our systems, this is

	/usr/local/elemental

At this point an environment variable can be created that points to the above directory, which will be used for the build. At the command line type

	export ELEMENTAL_INSTALL_DIR=/usr/local/elemental

Alternatively, the directory structure can be set up manually.
Inside this folder create a new folder named with the release version of Elemental:

	/usr/local/elemental/0.84/

Inside of this version folder, create two additional folders for each Elemental build type.  These should be named HybridRelease and PureRelease, to match Elemental's terminology.  Thus the final folder configuration is

	/usr/local/elemental/0.84/HybridRelease
	/usr/local/elemental/0.84/PureRelease


Download the 0.84 release of [Elemental](http://libelemental.org/releases/), unzip and untar the distribution, and cd to the top-level folder.

**1.1.**  Run these commands to create the required directories for the build types:
		mkdir build_hybrid
		mkdir build_pure

[--back to top--](#top)
###Perform the HybridRelease build
**1.2.** The HybridRelease version will be built first.
		cd build_hybrid
**1.3.** Configure the Elemental HybridRelease build with this command (all one line):
		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.84/HybridRelease -D CMAKE_BUILD_TYPE=HybridRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate"  ..
**1.4.** Build and install the code as follows:
		make -j4
		make install
**1.5.** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental/0.84/HybridRelease/conf/
**1.6.** Open the ‘ElemVars’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.9
change it to
		CXX = /usr/local/bin/g++-4.9 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.

[--back to top--](#top)
###Perform the PureRelease build
**1.7.** Change directories to the untarred Elemental folder and run the following commands:
		cd build_pure
**1.8.** Configure the Elemental PureRelease build with this command (all one line):
		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.84/PureRelease -D CMAKE_BUILD_TYPE=PureRelease -D 		CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/		local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate"  ..
**1.9.** Build and install the code as follows:
		make -j4
		make install
**1.10.** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental/0.84-PureRelease/conf/
**1.11.** Open the ‘ElemVars’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
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
<h2 id="lin_open_mpi"> Linux:Install OpenMPI </h2>
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

Thus, two versions of Elemental need to be built.  One is a hybrid release build with OpenMP parallelization, and the other is the pure release build without OpenMP parallelization.  A separate build folder will be created for each build.  The build that uses internal OpenMP parallelization is called a ‘HybridRelease’ build; the build that doesn’t is called a ‘PureRelease’ build.  The debug build is called a ‘PureDebug’ build.  The HybridRelease build is best for large problems, where the problem size is large enough to overcome the OpenMP parallel overhead. The following is for the 0.84 version of elemental. Set the version to that specified in the README.html file. Note that the files will be installed in **/usr/local/elemental/[version]/[build type]**.

###Here is our suggested installation scheme for Elemental:###

Choose a folder for the root of the Elemental installation.  For our systems, this is

	/usr/local/elemental

At this point an environment variable can be created that points to the above directory, which will be used for the build. At the command line type

	export ELEMENTAL_INSTALL_DIR=/usr/local/elemental

Alternatively, the directory structure can be set up manually.
Inside this folder create a new folder named with the release version of Elemental:

	/usr/local/elemental/0.84/

Inside of this version folder, create two additional folders for each Elemental build type.  These should be named HybridRelease and PureRelease, to match Elemental's terminology.  Thus the final folder configuration is

	/usr/local/elemental/0.84/HybridRelease
	/usr/local/elemental/0.84/PureRelease


Download the 0.84 release of [Elemental](http://libelemental.org/releases/), unzip and untar the distribution, and cd to the top-level folder.

[--back to top--](#top)

**2.1.**  Run these commands to create the required directories for the build types:
		mkdir build_hybrid
		mkdir build_pure

###Perform the HybridRelease build

**2.2** Change to the build_hybrid directory.
		cd build_hybrid
**2.3** Configure the Elemental HybridRelease build with this command (all one line):
		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.84/HybridRelease -D CMAKE_BUILD_TYPE=HybridRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-L/usr/local/openblas/0.2.8/ –lopenblas –lm" –D ELEM_EXAMPLES=ON –D ELEM_TESTS=ON  ..
If this command does not work, you may need to define the BLAS_LIBS and/or GFORTRAN_LIB config options.
**2.4** Build and install the code as follows:
		make -j4
		make install
**2.5** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental/0.84/HybridRelease/conf/
**2.6** Open the ‘ElemVars’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.9
change it to
		CXX = /usr/local/bin/g++-4.9 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.

[--back to top--](#top)

###Perform the PureRelease build
**2.7** Change to the build_pure directory.
		cd build_pure
**2.8** Configure the Elemental PureRelease build with this command (all one line):

		cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.84/PureRelease -D CMAKE_BUILD_TYPE=PureRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-4.9 -D CMAKE_C_COMPILER=/usr/local/bin/gcc-4.9 -D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-4.9 -D MATH_LIBS="/usr/local/flame/lib/libflame.a;-L/usr/local/openblas/0.2.8/ –lopenblas –lm" –D ELEM_EXAMPLES=ON –D ELEM_TESTS=ON  ..

If this command does not work, you may need to define the BLAS_LIBS and/or GFORTRAN_LIB config options.
**2.9** Build and install the code as follows:
		make -j4
		make install
**2.10** Edit the Elemental configuration file as follows:
		cd /usr/local/elemental-0.84-PureRelease/conf/
**2.11** Open the ‘ElemVars’ file in a text editor and add the string -std=c++11 to the CXX macro line. For instance, if the CXX macro is
		CXX = /usr/local/bin/g++-4.9
change it to
		CXX = /usr/local/bin/g++-4.9 -std=c++11
This will enable the C++11 features in the compiler when building Elemental-based projects.This completes the builds of Elemental.

**2.12** To test the installation, follow Elemental’s [test instructions](http://libelemental.org/documentation/0.84 /build.html) for the SVD test to verify that Elemental is working correctly.

[--back to top--](#top)

<h1 id="smallk"> SmallK NMF Library Installation </h1>

<h2 id="source_code"> Obtain the Source Code </h2>
The source code for the SmallK library can be obtained by downloading from the [SmallK](https://github.com/smallk/smallk.github.io/tree/master/code) repository on github.

Once downloaded uncompress and follow the installation instructions below.


<h2 id="build_smallk"> Build the SmallK library </h2>

After downloading and unpacking the code tarball cd into the top-level SmallK folder.  The makefiles assume that you followed our suggested installation plan for Elemental.  If this is not the case you will need to do one of the following things:
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

As an example of how to use the sample project, assume the smallk software has been installed into /usr/local/smallk.  Also assume that the user chose to create the recommended environment variable SMALLK_INSTALL_DIR that stores the location of the top-level install folder, i.e. the user’s .bashrc file contains this statement:

		export SMALLK_INSTALL_DIR=/usr/local/smallk 

To build the smallk example project, open a terminal window and cd to the smallk/examples folder and run this command: 

		make

To run the example project, run this command:

		./bin/example ../data

Note: the output will be *similar* to the following not identical since some problems are randomly initialized:


	Smallk major version: 1
	Smallk minor version: 0
	Smallk patch level:   0
	Smallk version string: 1.0.0
	Loading matrix...

	************************************************************
	*                                                          *
	*                Running NMF-BPP using k=32                *
	*                                                          *
	************************************************************
	Initializing matrix W...
	Initializing matrix H...

                parameters: 

             algorithm: Nonnegative Least Squares with Block Principal Pivoting
    stopping criterion: Ratio of Projected Gradients
                height: 12411
                 width: 7984
                     k: 32
               miniter: 5
               maxiter: 5000
                   tol: 0.005
            matrixfile: ../data/reuters.mtx
            maxthreads: 8

	1:  progress metric:    (min_iter)
	2:  progress metric:    (min_iter)
	3:  progress metric:    (min_iter)
	4:  progress metric:    (min_iter)
	5:  progress metric:    (min_iter)
	6:  progress metric:    0.0747031
	7:  progress metric:    0.0597987
	8:  progress metric:    0.0462878
	9:  progress metric:    0.0362883
	10: progress metric:    0.030665
	11: progress metric:    0.0281802
	12: progress metric:    0.0267987
	13: progress metric:    0.0236731
	14: progress metric:    0.0220778
	15: progress metric:    0.0227083
	16: progress metric:    0.0244029
	17: progress metric:    0.0247552
	18: progress metric:    0.0220007
	19: progress metric:    0.0173831
	20: progress metric:    0.0137033

	Solution converged after 39 iterations.

	Elapsed wall clock time: 4.354 sec.

	Writing output files...

	************************************************************
	*                                                          *
	*               Running NMF-HALS using k=16                *
	*                                                          *
	************************************************************
	Initializing matrix W...
	Initializing matrix H...

                parameters: 

             algorithm: HALS
    stopping criterion: Ratio of Projected Gradients
                height: 12411
                 width: 7984
                     k: 16
               miniter: 5
               maxiter: 5000
                   tol: 0.005
            matrixfile: ../data/reuters.mtx
            maxthreads: 8

	1:  progress metric:    (min_iter)
	2:  progress metric:    (min_iter)
	3:  progress metric:    (min_iter)
	4:  progress metric:    (min_iter)
	5:  progress metric:    (min_iter)
	6:  progress metric:    0.710219
	7:  progress metric:    0.580951
	8:  progress metric:    0.471557
	9:  progress metric:    0.491855
	10: progress metric:    0.531999
	11: progress metric:    0.353302
	12: progress metric:    0.201634
	13: progress metric:    0.1584
	14: progress metric:    0.142572
	15: progress metric:    0.12588
	16: progress metric:    0.113239
	17: progress metric:    0.0976934
	18: progress metric:    0.0821207
	19: progress metric:    0.0746089
	20: progress metric:    0.0720616
	40: progress metric:    0.0252854
	60: progress metric:    0.0142085
	80: progress metric:    0.0153269

	Solution converged after 88 iterations.

	Elapsed wall clock time: 1.560 sec.

	Writing output files...

	************************************************************
	*                                                          *
	*       Running NMF-RANK2 with W and H initializers        *
	*                                                          *
	************************************************************
	Initializing matrix W...
	Initializing matrix H...

                parameters: 

             algorithm: Rank 2
    stopping criterion: Ratio of Projected Gradients
                height: 12411
                 width: 7984
                     k: 2
               miniter: 5
               maxiter: 5000
                   tol: 0.005
            matrixfile: ../data/reuters.mtx
            maxthreads: 8

	1:  progress metric:    (min_iter)
	2:  progress metric:    (min_iter)
	3:  progress metric:    (min_iter)
	4:  progress metric:    (min_iter)
	5:  progress metric:    (min_iter)
	6:  progress metric:    0.0374741
	7:  progress metric:    0.0252389
	8:  progress metric:    0.0169805
	9:  progress metric:    0.0113837
	10: progress metric:    0.00761077
	11: progress metric:    0.0050782
	12: progress metric:    0.00338569

	Solution converged after 12 iterations.

	Elapsed wall clock time: 0.028 sec.

	Writing output files...

	************************************************************
	*                                                          *
	*       Repeating the previous run with tol = 1.0e-5       *
	*                                                          *
	************************************************************
	Initializing matrix W...
	Initializing matrix H...

                parameters: 

             algorithm: Rank 2
    stopping criterion: Ratio of Projected Gradients
                height: 12411
                 width: 7984
                     k: 2
               miniter: 5
               maxiter: 5000
                   tol: 1e-05
            matrixfile: ../data/reuters.mtx
            maxthreads: 8

	1:  progress metric:    (min_iter)
	2:  progress metric:    (min_iter)
	3:  progress metric:    (min_iter)
	4:  progress metric:    (min_iter)
	5:  progress metric:    (min_iter)
	6:  progress metric:    0.0374741
	7:  progress metric:    0.0252389
	8:  progress metric:    0.0169805
	9:  progress metric:    0.0113837
	10: progress metric:    0.00761077
	11: progress metric:    0.0050782
	12: progress metric:    0.00338569
	13: progress metric:    0.00225761
	14: progress metric:    0.00150429
	15: progress metric:    0.00100167
	16: progress metric:    0.000666691
	17: progress metric:    0.000443654
	18: progress metric:    0.000295213
	19: progress metric:    0.000196411
	20: progress metric:    0.000130604

	Solution converged after 27 iterations.

	Elapsed wall clock time: 0.061 sec.

	Writing output files...
	Minimum value in W matrix: 0.
	Maximum value in W matrix: 0.397027.


	************************************************************
	*                                                          *
	*      Running HierNMF2 with 5 clusters, JSON format       *
	*                                                          *
	************************************************************
	loading dictionary...
	creating random W initializers...
	creating random H initializers...

            parameters: 

                height: 12411
                 width: 7984
            matrixfile: ../data/reuters.mtx
              dictfile: ../data/reuters_dictionary.txt
                   tol: 0.0001
               miniter: 5
               maxiter: 5000
              maxterms: 5
            maxthreads: 8
	[1] [2] [3] [4] 

	Elapsed wall clock time: 391 ms.
	9/9 factorizations converged.

	Writing output files...

	************************************************************
	*                                                          *
	* Running HierNMF2 with 10 clusters, 12 terms, XML format  *
	*                                                          *
	************************************************************
	creating random W initializers...
	creating random H initializers...

            parameters: 

                height: 12411
                 width: 7984
            matrixfile: ../data/reuters.mtx
              dictfile: ../data/reuters_dictionary.txt
                   tol: 0.0001
               miniter: 5
               maxiter: 5000
              maxterms: 12
            maxthreads: 8
	[1] [2] [3] [4] [5] [6] dropping 20 items ...
	[7] [8] [9] 

	Elapsed wall clock time: 837 ms.
	21/21 factorizations converged.

	Writing output files...

	************************************************************
	*                                                          *
	*  Running HierNmf2 with 18 clusters, 8 terms, with flat   *
	*                                                          *
	************************************************************
	creating random W initializers...
	creating random H initializers...

            parameters: 

                height: 12411
                 width: 7984
            matrixfile: ../data/reuters.mtx
              dictfile: ../data/reuters_dictionary.txt
                   tol: 0.0001
               miniter: 5
               maxiter: 5000
              maxterms: 8
            maxthreads: 8
	[1] [2] [3] [4] [5] [6] dropping 20 items ...
	[7] [8] [9] dropping 25 items ...
	[10] [11] [12] [13] [14] [15] [16] [17] 

	Running NNLS solver...
	1:  progress metric:    1
	2:  progress metric:    0.264152
	3:  progress metric:    0.0760648
	4:  progress metric:    0.0226758
	5:  progress metric:    0.00743562
	6:  progress metric:    0.00280826
	7:  progress metric:    0.00103682
	8:  progress metric:    0.000361738
	9:  progress metric:    0.000133087
	10: progress metric:    5.84849e-05

	Elapsed wall clock time: 1.362 s.
	40/40 factorizations converged.

	Writing output files...

The output files are written to the default directory or the directory specified on the command line.

[--back to top--](#top)

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

[--back to top--](#top)

<h2 id="smallk_api"> SmallK API </h2>

The SmallK API is an extremely simplistic API for basic NMF and clustering.  Users who require more control over the factorization or clustering algorithms can instead run one of the command-line applications in the SmallK distribution.

The SmallK API is exposed by the file smallk.hpp, which can be found in this location: SMALLK_INSTALL_DIR/include/smallk.hpp.  All API functions are contained within the smallk namespace. 

An example of how to use the API can be found in the file examples/smallk_example.cpp.

The smallk library maintains a set of state variables that are used to control the Nmf and clustering routines.  Once set, the state variables maintain their values until changed by an API function.  For instance, one state variable represents the matrix to be factored (or used for clustering).  The API provides a function to load this matrix; once loaded, it can be repeatedly factored without the need for reloading.  The state variables and their default values are documented below.

All computations with the smallk library are performed in double precision.
<h3 id="enums"> Enumerations </h3>

The SmallK API provides two enumerated types, one for the supported NMF algorithms and one for the clustering file output format.  These are:

	enum Algorithm
	{
    		MU,      // Multiplicative Updating, Lee & Seung
    		BPP,     // Block Principal Pivoting, Kim and Park
    		HALS,    // Hierarchical Alternating Least Squares, Cichocki & Pan
    		RANK2    // Rank2, Kuang and Park
	};

The default NMF algorithm is BPP.  The Rank2 algorithm is optimized for two-column or two-row matrices and is the underlying factorization routine for the clustering code.

	enum OutputFormat
	{
    		XML,  // Extensible Markup Language
    		JSON  // JavaScript Object Notation
	};

<h3 id="api_functions"> API functions </h3>

<h4 id="init_cleanup"> Initialization and cleanup </h4>

	void Initialize(int& argc,     // in
 		char**& argv)  // in

Call this function first, before all others in the API; initializes Elemental and the smallk library.

	bool IsInitialized()
    
Returns true if the library has been initialized via a call to Initialize(), false otherwise.

Call this function last, after all others in the API; performs cleanup for Elemental and the smallk library.

	void Finalize()

<h4 id="versions"> Versioning </h4>

	unsigned int GetMajorVersion()

Returns the major release version number of the library as an unsigned integer.

	unsigned int GetMinorVersion()

Returns the minor release version number of the library as an unsigned integer.

	unsigned int GetPatchLevel()

Returns the patch version number of the library as an unsigned integer.

	std::string GetVersionString()

Returns the version of the library as a string, formatted as major.minor.patch.

[--back to top--](#top)

<h4 id="common_funcs"> Common functions </h4>

	unsigned int GetOutputPrecision()

Returns the floating point precision with which numerical output will be written (i.e., the computed W and H matrix factors from the Nmf routine).  The default precision is six digits. 

	void SetOutputPrecision(const unsigned int num_digits)

Sets the floating point precision with which numerical output will be written.  Input values should be within the range [1, precision(double)].  Any inputs outside of this range will be adjusted. 

	unsigned int GetMaxIter()

Returns the maximum number of iterations allowed for NMF computations.  The default value is 5000.

	void SetMaxIter(const unsigned int max_iterations = 5000)

Sets the maximum number of iterations allowed for NMF computations.  The default of 5000 should be more than sufficient for most computations. 

	unsigned int GetMinIter()

Returns the minimum number of NMF iterations. The default value is 5.

	void SetMinIter(const unsigned int min_iterations = 5)

Sets the minimum number of NMF iterations to perform before checking for convergence. The convergence and progress estimation routines are non-trivial calculations, so increasing this value may result in faster performance. 

	unsigned int GetMaxThreads()

Returns the maximum number of threads used for NMF or clustering computations. The default value is hardware-dependent, but is generally the maximum number allowed by the hardware.

	void SetMaxThreads(const unsigned int max_threads);

Sets an upper limit to the number of threads used for NMF and clustering computations.  Inputs that exceed the capabilities of the hardware will be adjusted. This function is provided for scaling and performance studies.  

	void Reset()

Resets all state variables to their default values. 

[--back to top--](#top)


	void SeedRNG(const int seed)

Seeds the random number generator (RNG) within the smallk library. Normally this RNG is seeded from the system time whenever the library is initialized.  The RNG is the ‘19937’ Mersenne Twister implementation provided by the C++ standard library.

	void LoadMatrix(const std::string& filepath)

Loads a matrix contained in the given file.  The file must either be a comma-separated value (.CSV) file for a dense matrix, or a MatrixMarket-format file (.MTX) for a sparse matrix. If the matrix cannot be loaded the library throws a std::runtime_error exception.

	bool IsMatrixLoaded()

Returns true if a matrix is currently loaded, false if not.

	std::string GetOuputDir()

Returns a string indicating the directory into which output files will be written.  The default is the current directory.

	void SetOutputDir(const std::string& outdir)

Sets the directory into which output files should be written. The ‘outdir’ argument can either be an absolute or relative path.  The default is the current directory.

[--back to top--](#top)

<h4 id="nmf_funcs"> NMF functions </h4>

	void Nmf(const unsigned int k, 
 		const Algorithm algorithm     = Algorithm::BPP,
 		const std::string& initfile_w = std::string(“”),
 		const std::string& initfile_h = std::string(“”))

This function nonnegatively factors the loaded input matrix A as follows: A ~ WH.  If a matrix is not currently loaded a std::logic_error exception will be thrown.  The default algorithm is NMF-BPP; provide one of the enumerated algorithm values to use a different algorithm.

Matrix A has dimension mxn; matrix W has dimension mxk; matrix H has dimension kxn.  The value of k is provided as an argument.

Optional initializer matrices can be provided for the W and H factors via the ‘initfile_w’ and ‘initfile_h’ arguments. These files must contain fully dense matrices in .CSV format.  The W matrix initializer must have dimension mxk, and the H matrix initializer must have dimension kxn. If the initializer matrices do not match these dimensions exactly a std::logic_error exception is thrown.  If initializers are not provided, matrices W and H will be randomly initialized.

The computed factors W and H will be written to the output directory in the files ‘w.csv’ and ‘h.csv’.
    
Exceptions will be thrown (either from Elemental or smallk) in case of error.


	const double* LockedBufferW(unsigned int& ldim, unsigned int& height, unsigned int& width)

This function returns a READONLY pointer to the buffer containing the W factor computed by the Nmf routine, along with buffer and matrix dimensions.  The ‘ldim’, ‘height’, and ‘width’ arguments are all out parameters.  The buffer has a height of ‘ldim’ and a width of ‘width’.  The matrix W has the same width but a height of ‘height’, which may differ from ldim.  The W matrix is stored in the buffer in column-major order.  See the examples/smallk_example.cpp file for an illustration of how to use this function. 

	const double* LockedBufferH(unsigned int& ldim, unsigned int& height, unsigned int& width)

Same as LockedBufferW, but for the H matrix.

	double GetNmfTolerance()

Returns the tolerance value used to determine NMF convergence. The default value is 0.005. 

	void SetNmfTolerance(const double tol=0.005)

Sets the tolerance value used to determine NMF convergence.  The NMF algorithms are iterative, and at each iteration a progress metric is computed and compared with the tolerance value.  When the metric falls below the tolerance value the iterations stop and convergence is declared.  The tolerance value should satisfy 0.0 < tolerance < 1.0.  Any inputs outside this range will cause a std::logic_error exception to be thrown.
Clustering Functions

	void LoadDictionary(const std::string& filepath)

Loads the dictionary used for clustering. The dictionary is an ASCII file of text strings as described in the preprocessor input files section below.  If the dictionary file cannot be loaded a std::runtime_error exception is thrown.

[--back to top--](#top)

	unsigned int GetMaxTerms()

Returns the number of highest-probability dictionary terms to store per cluster. The default value is 5.

	void SetMaxTerms(const unsigned int max_terms = 5)

Sets the number of highest-probability dictionary terms to store per cluster.
    
	OutputFormat GetOutputFormat()

Returns a member of the OutputFormat enumerated type; this is the file format for the clustering results.  The default output format is JSON.

	void SetOutputFormat(const OutputFormat = OutputFormat::JSON)

Sets the output format for the clustering result file. The argument must be one of the values in the OutputFormat enumerated type.

	double GetHierNmf2Tolerance()

Returns the tolerance value used by the NMF-RANK2 algorithm for hierarchical clustering.  The default value is 1.0e-4.

	void SetHierNmf2Tolerance(const double tol=1.0e-4)

Sets the tolerance value used by the NMF-RANK2 algorithm for hierarchical clustering.  The tolerance value should satisfy 0.0 < tolerance < 1.0.  Any inputs outside this range will cause a std::logic_error exception to be thrown.

	void HierNmf2(const unsigned int num_clusters)

This function performs hierarchical clustering on the loaded matrix, generating the number of clusters specified by the ‘num_clusters’ argument.  For an overview of the hierarchical clustering process, see the description below for the hierclust command line application.

This function generates two output files in the output directory: ‘assignments_N.csv’ and ‘tree_N.{json, xml}’.  Here N is the number of clusters specified as an argument, and the tree file can be in either JSON XML format.

The content of the files is described below in the section on the hierclust command line application.

	void HierNmf2WithFlat(const unsigned int num_clusters)

This function performs hierarchical clustering on the loaded matrix, exactly as described for HierNmf2. In addition, it also computes a flat clustering result.  Thus four output files are generated.  The flat clustering result files are ‘assignments_flat_N.csv’ and ‘clusters_N.{json, xml}’.  The cluster file contents are documented below in the section on the flatclust command line application.

[--back to top--](#top)


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

Richard Boyd <br>
Senior Research Scientist <br>
Cyber Technology and Information Security Laboratory <br>
Georgia Tech Research Institute <br>
250 14th St NW <br>
Atlanta, GA 30318 <br>
<span><a href="mailto:richard.boyd@gtri.gatech.edu">richard.boyd@gtri.gatech.edu</a></span>

Barry Drake <br>
Senior Research Scientist <br>
Cyber Technology and Information Security Laboratory <br>
Georgia Tech Research Institute <br>
250 14th St NW <br>
Atlanta, GA 30318 <br>
<span><a href="mailto:barry.drake@gtri.gatech.edu">barry.drake@gtri.gatech.edu</a></span>

[--back to top--](#top)



