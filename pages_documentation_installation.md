---
layout: page
title: Installation

baselink: /documentation/
permalink: /documentation/installation/
---

<h1 id="top">Contents</h1>
---------------------
*   [Quickstart: Vagrant Virtual Machine](#vagrant)
*   [Standard Build Instructions](#standard)
    *   [Prerequisites](#pre)
*   [How to Install Elemental on MacOSX](#MacOSX)
    *   [OSX:Install the latest GNU compilers](#mac_GNU)
    *   [OSX:Install OpenMPI](#mac_mpi)
    *   [OSX:Install libFlame](#mac_libflame)
    *   [OSX:Install Elemental](#mac_elemental)
*   [How to Install Elemental on Linux](#Linux)
    *   [Linux:Install the latest GNU compilers](#lin_GNU)
    *   [Linux:Install MPI Tools](#lin_mpi)
    *   [Linux:Install libFlame](#lin_libflame)
    *   [Linux:Install BLAS Library](#lin_blas)
    *   [Linux:Install Elemental](#lin_elemental)
*   [Build and Installation of SmallK](#smallk)
    *   [Obtain the source code](#source_code)
    *   [Build the SmallK library](#build_smallk)
    *   [Matrix File Formats](#matrix_files)
*   [Installation of Python libraries](#python_install)
    *   [OSX:Install Python libraries](#osx_python)
    *   [Linux:Install Python libraries](#lin_python)
    *   [Build pysmallk shared library](#build_python)
*   [Contact Information](#contact)


<h1 id="vagrant"> Quickstart: Vagrant Virtual Machine </h1>

Installing SmallK into a virtual machine (OSX, Linux, Windows) is intended for those who are not doing development and/or do not have a reason to do the full installation on Linux or OSX outlined in the sections to follow.

The complete stack of software dependencies for SmallK as well as SmallK itself can be rapidly set up and configured through use of Vagrant and VirtualBox and the files included in the repository. To deploy the SmallK VM:
The Vagrant install has been tested on Linux Ubuntu 14.04, Mac OSX Yosemite 10.10.5, and Windows 8.

**1.** Install [Vagrant](http://www.vagrantup.com/downloads.html) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads).

**2.** ’git clone’ the [smallk_data](https://github.com/smallk/smallk_data) repository so that it is parallel with the smallk repository. This will be important for testing the installation and starting to work with SmallK. This directory will be synced with a directory of the same name in the VM.

_[Note: For Windows, ensure that you have a VirtualBox version >= 4.3.12. After installing Vagrant, you may need to log out and log back in to ensure that you can run vagrant commands in the command prompt.]_

**3..** From within the vagrant/ directory in the repository, run:
		
		vagrant up
		
This can take as long as an hour to build the VM, which will be based on a minimal Ubuntu 14.04 installation. The Vagrantfile can be customized in many ways to change the specifications for the VM that is built. See more information [here](http://docs.vagrantup.com/v2/). The default configuration provides the VM with 4 GB of memory and 2 CPUs. Increasing these allocations will improve the performance of the application. This can be done by modifying this line in the Vagrantfile:

		vb.customize ["modifyvm", :id, "--memory", "4056", "--cpus", "2"]

After ‘vagrant up’ has completed, the SmallK library will have been built and the smallk_data directory, cloned as in **2.** above, will have been synced into the VM.
<br>[--back to top--](#top)

**4..** Once the VM has been built, run:

		vagrant ssh

_[Note: For Windows, you will need an ssh client in order to run the above command. This can be obtained via [CygWin](https://www.cygwin.com/), [MinGW](http://sourceforge.net/projects/mingw/files/), or [Git](http://git-scm.com/downloads). If you would like to use PuTTY to connect to your virtual machine, follow [these](https://github.com/Varying-Vagrant-Vagrants/VVV/wiki/Connect-to-Your-Vagrant-Virtual-Machine-with-PuTTY) instructions.]_

This will drop you into the command line of the VM that was just created. Note that the *bootstrap.sh* file can be modified to perform various tasks when going to the vagrant command line session. Customize bootstrap.sh to set up a custom install of libsmallk as required.

From there, you can navigate to /home/vagrant/libsmallk-<version>, e.g., libsmallk-1.6.2, and run

		make check DATA_DIR=../smallk_data
		
make check will verify your installation was successful. 

To check the installation of pysmallk, run

		make pysmallk_check DATA_DIR=../smallk_data

In case you need it, the username/password for the VM created will be vagrant/vagrant.

The command

		sudo make install

will install smallk to the /usr/local/smallk/bin directory. If you want to install pysmallk as well, run

		sudo make install PYSMALLK=1 INSTALLATION_DIR=/usr/local/lib/python2.7/site-packages/

To add smallk to your PATH variable: 

		export PATH=/usr/local/smallk/bin:$PATH
		
[--back to top--](#top)

**5.** To test the installation at the command line, assuming the $PATH variable has been set as above, run

		nmf

This will produce the help out put for the nmf library function:

	Usage: nmf
        --matrixfile <filename>  Filename of the matrix to be factored.
                                 Either CSV format for dense or MatrixMarket format for sparse.
        --k <integer value>      The common dimension for factors W and H.
        [--algorithm  BPP]       NMF algorithms: 
                                     MU:    multiplicative updating 
                                     HALS:  hierarchical alternating least squares
                                     RANK2: rank2 with optimal active set selection
                                     BPP:   block principal pivoting
        [--stopping  PG_RATIO]   Stopping criterion: 
                                     PG_RATIO: Ratio of projected gradients
                                     DELTA:    Change in relative F-norm of W
        [--tol  0.005]           Tolerance for the selected stopping criterion.
        [--tolcount  1]          Tolerance count; declare convergence after this many 
                                 iterations with metric < tolerance; default is to 
                                 declare convergence on the first such iteration.
        [--infile_W  (empty)]    Dense mxk matrix to initialize W; CSV file.
                                 If unspecified, W will be randomly initialized.
        [--infile_H  (empty)]    Dense kxn matrix to initialize H; CSV file. 
                                 If unspecified, H will be randomly initialized. 
        [--outfile_W  w.csv]     Filename for the W matrix result.
        [--outfile_H  h.csv]     Filename for the H matrix result.
        [--miniter  5]           Minimum number of iterations to perform. 
        [--maxiter  5000]        Maximum number of iterations to perform.
        [--outprecision  6]      Write results with this many digits of precision.
        [--maxthreads    4]      Upper limit to thread count. 
        [--normalize  1]         Whether to normalize W and scale H.
                                     1 == yes, 0 == no 
        [--verbose  1]           Whether to print updates to the screen. 
                                     1 == print updates, 0 == silent 

**6.** To test the installation of pysmallk, open a python terminal and import numpy and pysmallk. Numpy must be imported BEFORE pysmallk is imported.

		python
		import numpy
		import pysmallk
		
If there is no import error, pysmallk was installed correctly and is globally available.
		

**7.** When you are ready to shut down the VM, run one of the following.

Save the current running state:

		vagrant suspend

Gracefully shut down the machine:

		vagrant halt

Remove the VM from your machine (this will require rebuilding the VM to restart it)

		vagrant destroy

If you want to work with the VM again, from any of the above states you can run

		vagrant up
		
again and the VM will be resumed or recreated.

<br>[--back to top--](#top)

<h1 id="standard"> Standard Build Instructions </h1>

<h2 id="pre"> Prerequisites </h2>
* A modern C++ compiler that supports the C++11 standard, such as the latest release of the GNU or clang compilers
* [Elemental](http://libelemental.org/), a high-performance library for dense, distributed linear algebra, 
which requires:
	* An MPI installation, such as [OpenMPI](http://www.open-mpi.org/software/ompi/v1.6/) and [mpich](http://www.mpich.org/)
	* A BLAS implementation, preferably optimized/tuned for the local system
	* [libFLAME](http://www.cs.utexas.edu/~flame/web/libFLAME.html): a high-performance library for dense linear algebra
	* [OpenMP](http://openmp.org/wp/) (optional, see below)
	* CMake 
* Python 2.7, including the following libraries: 
	* numpy
	* scipy
	* cython version 0.22

Elemental can make use of MPI parallelization if available. This is generally advantageous for large problems. The SmallK code is also internally parallelized to take full advantage of multiple CPU cores for maximum performance. SmallK does not currently support distributed computation. However, future updates are planned that provide this capability. Please see the [About](http://smallk.github.io/about/) page for information regarding distributed versions of many of the algorithms within SmallK.

We **strongly** recommend that users install both the HybridRelease and PureRelease builds of [Elemental](http://libelemental.org/). OpenMP is enabled in the HybridRelease build and disabled in the PureRelease build. So why install both? For smaller problems the overhead of *MPI can actually cause code to run slower* than without it. Whereas for large problems MPI parallelization generally helps, but there is no clear transition point between where it helps and where it hurts. Thus, we encourage users to experiment with both builds to find the one that performs best for their typical problems.

We also recommend that users clearly separate the different build types as well as the versions of Elemental on their systems. Elemental is under active development, and new releases can introduce changes to the API that are not backwards-compatible with previous releases. To minimize build problems and overall hassle, we recommend that Elemental be installed so that the different versions and build types are cleanly separated.

Thus, two versions of Elemental need to be built. One is a hybrid release build with OpenMP parallelization, and the other is the pure release build without OpenMP parallelization.  A separate build folder will be created for each build. The build that uses internal OpenMP parallelization is called a ‘HybridRelease’ build; the build that doesn’t is called a ‘PureRelease’ build. The debug build is called a ‘PureDebug’ build. The HybridRelease build is best for large problems, where the problem size is large enough to overcome the OpenMP parallel overhead. The following is for the 0.84 version of elemental. Set the version to that specified in the README.html file. Note that the files will be installed in **/usr/local/elemental/[version]/[build type]**.

**The SmallK software supports the latest stable release of Elemental, version 0.85 and above.**

**A note of caution: copying the command lines from this website and pasting them into a terminal may result in the commands not properly executing due to how characters are interpreted: the double dash --, “double quotes”, etc. For pasting the commands to a terminal, first copy the command lines to a text editor and copy/paste from there.**
<br>[--back to top--](#top)

<h1 id="MacOSX"> How to Install Elemental on MacOSX </h1>

On MacOSX we recommend using [Homebrew](http://mxcl.github.io/homebrew/) as the package manager. Homebrew does not require sudo privileges for package installation, unlike other package managers such as MacPorts. Thus the chances of corrupting vital system files are greatly reduced using Homebrew.

It is convenient to be able to view hidden files (like .file) in the MacOSX Finder. To do so run the following at the command line:

	defaults write com.apple.finder AppleShowAllFiles -bool YES

To revert back to hiding hidden files, set the Boolean flag to NO:

	defaults write com.apple.finder AppleShowAllFiles -bool NO

If you use Homebrew, ensure that your PATH is configured to search Homebrew’s installation directory first. Homebrew’s default installation location is /usr/local/bin, so that location needs to be first on your path. To check, run this command from a terminal window:

	cat /etc/paths

We also recommend running the following commands on a daily basis to refresh your brewed installations:

	brew update
	brew upgrade
	brew cleanup
	brew doctor

This will maintain your Homebrew installed software and diagnose any issues with the installations.
If the first entry is not /usr/local/bin, you will need to edit the /etc/paths file. This is a system file, so first create a backup. Move the line /usr/local/bin so that it is on the first line of the file. Save the file, then close the terminal session and start a new terminal session so that the path changes will take effect.
<br>[--back to top--](#top)
<h2 id="mac_GNU"> OSX:Install the latest GNU compilers </h2>
Elemental and SmallK both require a modern C++ compiler compliant with the C++11 standard. We recommend that you install the latest stable version of the clang and GNU C++ compilers. To do this, first install the XCode command line tools with this command:

		xcode-select --install
If this command produces an error, download and install XCode from the AppStore, then repeat the command. If that should still fail, install the command line tools from the XCode preferences menu. After the installation completes, run this command from a terminal window:

		clang++ --version
You should see output similar to this:

		Apple LLVM version 7.0.2 (clang-700.1.81)		
		Target: x86_64-apple-darwin14.5.0		
		Thread model: posix

The latest version of the GNU compiler at the time of writing is g++ 6.2.0, which is provided by the ‘gcc’ homebrew package. In addition to the gcc package, homebrew also provides a gcc49 package from the homebrew/versions tap. If this alternative gcc49 package is installed on your system it will prevent homebrew from symlinking the gcc package correctly. We recommend uninstalling the gcc49 versioned package and just using the gcc package instead. The Fortran compiler provided with the gcc package will also be configured to properly build numpy, which is required for the python interface to SmallK.
If you need to uninstall the gcc49 package, run the following commands:

		brew uninstall gcc49
		brew cleanup
		brew doctor
Then install the gcc package as follows:

		brew install gcc
The Apple-provided gcc and g++ will not be overwritten by this installation. The new compilers will be installed into /usr/local/bin as gcc-6, g++-6, and gfortran-6. The Fortran compiler is needed for the installation of MPI and for building the python interface to SmallK.
<br>[--back to top--](#top)
<h2 id="mac_mpi"> OSX:Install MPI Tools</h2>
Install the latest version of [mpich](http://www.mpich.org/) with Homebrew as follows:

		brew install mpich

We recommend installing mpich rather than openMPI due to some superior features of mpich (prior versions of Elemental use openMPI, which can be installed using Homebrew as well). Also, Elemental 0.85 (discussed below) now uses mpich. Please see some discussion regarding openMPI vs mpich at:
[http://stackoverflow.com/questions/2427399/mpich-vs-openmpi](http://stackoverflow.com/questions/2427399/mpich-vs-openmpi)
<br>[--back to top--](#top)

<h2 id="mac_libflame"> OSX:Install libFlame </h2>
Next we detail the installation of the high performance numerical library libflame. The library can be gotten from the libflame git repository on github.

It’s important to perform the git clone into a subdirectory NOT called ‘flame’ since this can cause name conflicts with the installation. Typically, a git clone is performed into a directory called ‘libflame’. However, other directory names will work as well. **Please do not use the directory name ‘flame’**.

To obtain the latest version of the FLAME library, clone the FLAME git repository with this command:

		git clone https://github.com/flame/libflame.git
Run the configure script in the top-level FLAME directory as follows (assuming the install path is /usr/local/flame):

	./configure –-prefix=/usr/local/flame –-with-cc=/usr/local/bin/gcc-6 –-with-ranlib=/usr/local/bin/gcc-ranlib-6
A complete list of configuration options can be obtained by running ./configure –-help.

After the configuration process completes, build the FLAME library as follows:

		make –j4

The –j4 option tells Make to use four processes to perform the build.  This number can be increased if you have a more capable system. Libflame will be installed with the following command:

		make install

The FLAME library is now installed.
<br>[--back to top--](#top)
<h2 id="mac_elemental"> OSX:Install Elemental </h2>

### Here is a recommended installation scheme for Elemental: ###

Choose a directory for the root of the Elemental installation.  For example, this may be

		/usr/local/elemental

Download one of the SmallK-supported releases of Elemental, unzip and untar the distribution, and cd to the top-level directory of the unzipped distribution. This directory  will be denoted by UNZIP_DIR in the following instructions.
**We now recommend using Elemental 0.85 or later. Earlier versions will no longer be supported**.

**<u>HybridRelease Build</u>**


From the Elemental-0.85 directory, run the following command to create a local build directory for the HybridRelease build:

		mkdir build_hybrid
		cd build_hybrid

Use the following CMake command for the HybridRelease build, substituting 0.85 for <VERSION_STRING>:

	cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/<VERSION_STRING>/HybridRelease
	-D CMAKE_BUILD_TYPE=HybridRelease 
	-D CMAKE_CXX_COMPILER=/usr/local/bin/g++-6 
	-D CMAKE_C_COMPILER=/usr/local/bin/gcc-6 
	-D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-6 
	-D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate" 
	–D ELEM_EXAMPLES=ON –D ELEM_TESTS=ON  ..

Note that we have installed g++-6 into /usr/local/bin and libFLAME into /usr/local/flame. Alter these paths, if necessary, to match the installation location on your system.

Once the CMake configuration step completes, you can build Elemental from the generated Makefiles with the following command:

		make –j4

The –j4 option tells Make to use four processes to perform the build. This number can be increased if you have a more capable system.

After the build completes, install elemental as follows:

		make install

If you installed Elemental version 0.85 or later, in order for the system to find the Elemental dynamic library insert the following in the appropriate startup script (~/.bash_profile) or in a terminal window, enter the following command on a single line, replacing <VERSION_STRING> as above:

		export DYLD_LIBRARY_PATH=
			$DYLD_LIBRARY_PATH:/usr/local/elemental/<VERSION_STRING>/HybridRelease/lib/

**<u>PureRelease Build</u>**

Run these commands to create a build directory for the PureRelease build:

		cd ..
		mkdir build_pure
		cd build_pure 

Then repeat the CMake configuration process, this time with the following command for the PureRelease build:

	cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/<VERSION_STRING>/PureRelease 
	-D CMAKE_BUILD_TYPE=PureRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-6 
	-D CMAKE_C_COMPILER=/usr/local/bin/gcc-6 
	-D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-6 
	-D MATH_LIBS="/usr/local/flame/lib/libflame.a;-framework Accelerate"  
	–D ELEM_EXAMPLES=ON –D ELEM_TESTS=ON ..

Repeat the build commands and install this build of Elemental. 

If you installed Elemental version 0.85 or later, in order for the system to find the Elemental dynamic library insert the following in the appropriate startup script (~/.bash_profile) or in a terminal window, enter the following command on a single line, replacing <VERSION_STRING> as above:

		export DYLD_LIBRARY_PATH=
			$DYLD_LIBRARY_PATH:/usr/local/elemental/<VERSION_STRING>/PureRelease/lib/

Note: you will need to set this variable to point to either the HybridRelease or the PureRelease build of the Elemental dynamic library whenever you want to use SmallK.

This completes the two builds of the Hybrid and Pure releases of Elemental.

To test the installation, follow Elemental’s [test instructions](http://libelemental.org/documentation/0.85/build.html) for the SVD test to verify that Elemental is working correctly.
<br>[--back to top--](#top)

<h1 id="Linux"> How to Install Elemental on Linux </h1>

We strongly recommend using a package manager for your Linux distribution for installation and configuration of the required dependencies. We cannot provide specific installation commands for every variant of Linux, so we specify the high-level steps below. The following was tested on a system with Ubuntu 16.04 installed.

<h2 id="lin_GNU"> Linux:Install the latest GNU compilers </h2>
We recommend installation of the latest stable release of the GNU C++ compiler, which is g++-6 at the time of this writing. Also, install the latest version of GNU Fortran, which is needed for the installation of the Message Passing Interface (MPI) tools. 
<h2 id="lin_mpi"> Linux:Install MPI Tools </h2>
Elemental version 0.85 and higher uses [mpich](http://www.mpich.org/) for its MPI implementation.

		sudo apt-get update
		sudo apt-get install mpich
This completes the installation of the MPI tools. It should also be noted that the Open MP implementation of the MPI tools could also be used for the following installations.
<br>[--back to top--](#top)
<h2 id="lin_libflame"> Linux:Install libFlame </h2>

Next we detail the installation of the high performance numerical library libflame. The library can be gotten from the libflame git repository on github.

It’s important to perform the git clone into a subdirectory NOT called ‘flame’ since this can cause name conflicts with the installation. We normally do a git clone into a directory called ‘libflame’. However, other directory names will work as well, but not ‘flame’.

To obtain the latest version of the FLAME library, clone the FLAME git repository with this command:

		git clone https://github.com/flame/libflame.git
Run the configure script in the top-level FLAME folder as follows (assuming you want to install to /usr/local/flame; if not, change the prefix path):

		./configure --prefix=/usr/local/flame --with-cc=/usr/local/bin/gcc-6
			--with-ranlib=/usr/local/bin/gcc-ranlib-6
A complete list of configuration options can be obtained by running

		./configure –-help

Then build and install the code as follows:

		make -j4
		make install

This completes the installation of the FLAME library.
<br>[--back to top--](#top)

<h2 id="lin_blas"> Linux:Install an accelerated BLAS library </h2>

It is essential to link Elemental with an accelerated BLAS library for maximum performance. Linking Elemental with a ‘reference’ BLAS implementation will cripple performance, since the reference implementations are designed for correctness not speed. If you do not have an accelerated BLAS on your system, you can download and build OpenBLAS. Download, unzip, and untar the tarball (version 0.2.19 as of this writing) and cd into the top-level folder. Build OpenBLAS with this command, assuming you have a 64-bit system:

		make BINARY=64 USE_OPENMP=1

Install with this command, assuming the installation directory is /usr/local/openblas/0.2.19/:

		make PREFIX=/usr/local/openblas/0.2.19/ installThis completes the installation of OpenBLAS.
<br>[--back to top--](#top)
<h2 id="lin_elemental"> Linux:Install Elemental </h2>

### Here is our suggested installation scheme for Elemental: ###

We strongly recommend that users install both the HybridRelease and PureRelease builds of Elemental. MPI tools are enabled in the HybridRelease build and disabled in the PureRelease build. So why install both? For smaller problems the overhead of MPI can actually cause code to run slower than without it. On the other hand, for large problems, MPI parallelization generally helps. However, there is no clear transition point between where it helps and where it hurts. Thus, we encourage users to experiment with both builds to find the one that performs best for their typical problems.Another strong recommendation is that users clearly separate the different build types as well as the versions of Elemental on their systems. Elemental is under active development, and new releases can introduce changes to the API that are not backwards compatible with previous releases. To minimize build problems and overall hassle, we recommend that Elemental be installed so that the different versions and build types are cleanly separated.
Choose a directory for the root of the Elemental installation. A good choice is

		/usr/local/elemental

Download one of the SmallK-supported releases of Elemental (see above), unzip and untar the distribution, and cd to the top-level folder of the unzipped distribution.  This directory will be denoted by UNZIP_DIR in the following instructions.

Note that Elemental version 0.85 or later is the version currently supported; earlier versions are not supported. If an earlier version is needed for Linux, use the following instructions.

For the first step of the installation, for Elemental versions prior to 0.85, we need to fix a few problems with the CMake configuration files.  Open the following file in a text editor:

		UNZIP_DIR/cmake/tests/OpenMP.cmake

On the first line of the file, change

		if(HYBRID)

to this:

		if(ELEM_HYBRID)

Next, open this file in a text editor:

		UNZIP_DIR/cmake/tests/Math.cmake

Near the first line of the file, change

		if(PURE)

to this:

		if(ELEM_PURE)

Save both files.

Run these commands to create the required directories for the build types:

		mkdir build_hybrid
		mkdir build_pure

**<u>HybridRelease build</u>**

From the Elemental-<VERSION> folder, run the following command to change to the local build folder for the HybridRelease build:

		cd build_hybrid

For the first step of the installation, we need to fix a few problems with the CMake configuration files. Open the following file in a text editor:

		Elemental-<VERSION>/cmake/tests/OpenMP.cmake

On the first line of the file, change

		if(HYBRID)

to this:

		if(ELEM_HYBRID)

Next, open this file in a text editor:

		Elemental-<version>/cmake/tests/Math.cmake
Near the first line of the file, change

		if(PURE)

to this:

		if(ELEM_PURE)

Save both files.

Run the following command to create a local build folder for the HybridRelease build:

		cd build_hybrid

Use the following CMake command for the HybridRelease build:

	cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/<VERSION>/HybridRelease
	-D CMAKE_BUILD_TYPE=HybridRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-6
	-D CMAKE_C_COMPILER=/usr/local/bin/gcc-6
	-D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-6
	-D MATH_LIBS="/usr/local/flame/lib/libflame.a;-L/usr/local/openblas/0.2.19/ –lopenblas –lm"
	–D ELEM_EXAMPLES=ON –D ELEM_TESTS=ON  ..

Note that we have installed g++-6 into /usr/local/bin and libFLAME into /usr/local/flame. Alter these paths, if necessary, to match the installation location on your system.

If this command does not work on your system, you may need to define the BLAS_LIBS and/or GFORTRAN_LIB config options.

Version 0.85 of Elemental has an error in one of its cmake files. The file is:

		Elemental-0.85/cmake/tests/CXX.cmake

Modify the first line of this file from

		include(FindCXXFeatures)

to

		include_directories(FindCXXFeatures)

since FindCXXFeatures is now a directory. After this change, Elemental should Make without errors.

Once the CMake configuration step completes, you can build Elemental from the generated Makefiles with the following command:

		make –j4

The –j4 option tells Make to use four processes to perform the build. This number can be increased if you have a more capable system.

After the build completes, install elemental as follows:

		make install


After installing Elemental version 0.85, setup the system to find the Elemental shared library.  Either in the startup script (~/.bashrc) or in a terminal window, enter the following command on a single line, replacing VERSION_STRING as above:

		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/elemental/VERSION_STRING/HybridRelease/lib/
<br>[—back to top--](#top)

**<u>PureRelease build</u>**

After this, run these commands to create a build folder for the PureRelease build:

		cd ..
		cd build_pure

Then repeat the CMake configuration process, this time with the following command for the PureRelease build:

	cmake -D CMAKE_INSTALL_PREFIX=/usr/local/elemental/0.84-p1/PureRelease
	-D CMAKE_BUILD_TYPE=PureRelease -D CMAKE_CXX_COMPILER=/usr/local/bin/g++-6
	-D CMAKE_C_COMPILER=/usr/local/bin/gcc-6
	-D CMAKE_Fortran_COMPILER=/usr/local/bin/gfortran-6
	-D MATH_LIBS="/usr/local/flame/lib/libflame.a;-L/usr/local/openblas/0.2.19/ –lopenblas –lm"
	–D ELEM_EXAMPLES=ON –D ELEM_TESTS=ON  ..

If this command does not work on your system, you may need to define the BLAS_LIBS and/or GFORTRAN_LIB config options.

Repeat the build commands and install this build of Elemental. Then, if you installed a version of Elemental **prior** to the 0.84 release, edit the /usr/local/elemental/<version>/PureRelease/conf/ElemVars file and replace the CXX line as indicated above.

Version 0.85 of Elemental has an error in one of its cmake files. The file is:

		Elemental-0.85/cmake/tests/CXX.cmake

Modify the first line of this file from

		include(FindCXXFeatures)

to

		include_directories(FindCXXFeatures)

since FindCXXFeatures is now a directory. After this change, Elemental should Make without errors.

If Elemental version 0.85 or later was installed, setup the system to find the Elemental shared library for the PureRelease build. Enter the following command in a terminal window on a single line, replacing VERSION_STRING as above:

	export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/elemental/VERSION_STRING/PureRelease/lib/

Note: set this variable to point to either the HybridRelease or the PureRelease build of the Elemental shared library whenever you want to use SmallK.

This completes the two builds of Elemental.

To test the installation, follow Elemental’s [test instructions](http://libelemental.org/documentation/<version> /build.html) for the SVD test to verify that Elemental is working correctly.
<br>[—back to top--](#top)


<h1 id="smallk"> Build and Installation of SmallK </h1>

<h2 id="source_code"> Obtain the Source Code </h2>
The source code for the SmallK library can be downloaded from the [SmallK repository](https://github.com/smallk/smallk.github.io/tree/master/code) on github.
Once downloaded uncompress the tar ball and follow the installation instructions below.


<h2 id="build_smallk"> Build the SmallK library </h2>

After downloading and unpacking the code tarball cd into the top-level libsmallk1_<version> directory, where version is MAJOR.MINOR.PATCH (for example 1.6.2). The makefiles assume that you followed our suggested installation plan for Elemental. If this is NOT the case you will need to do one of the following:

		1. Create an environment variable called ELEMENTAL_INSTALL_DIR which contains the 
			path to the root folder of your Elemental installation
		2. Define the variable ELEMENTAL_INSTALL_DIR on the make command line
		3. Edit the SmallK makefile so that it can find your Elemental installation
Assuming that the default install locations are acceptable, build the SmallK code by running this command from the root directory of the distribution:

		make all
This will build the SmallK and pysmallk (optional; see section [Installation of Python libraries](#python_install)) below for setup of the Python libraries) libraries and several command-line applications. These are:

	1. libsmallk.a, the SmallK library
	2. preprocess_tf, a command-line application for processing and scoring term-frequency matrices
	3. matrixgen, a command-line application for generating random matrices
	4. nmf, a command-line application for NMF
	5. hierclust, a command-line application for fast hierarchical clustering
	6. flatclust, a command-line application for flat clustering via NMF
	7. pysmallk.so, if PYSMALLK=1 (0: default), the Python-wrapped SmallK library, making SmallK available via Python
To install the code, run this command to install to the default location, which is /usr/local/smallk:

		make install
This will install the binary files listed above into the /usr/local/smallk/bin directory, which needs to be on your path to run the executables from anywhere on your system and avoid prepending with the entire path. To install the binary code to a different location, either create an environment variable called SMALLK_INSTALL_DIR and set it equal to the desired installation location prior to running the install command, or supply a prefix argument:

		make prefix=/path/to/smallk  install
If PYSMALLK=1, this will install pysmallk.so into the site-packages directory associated with the Python binary, which is determined by ‘brew install python’ as discussed above or wherever the python distribution is installed on the system, e.g., [Continuum’s Anaconda Python](https://www.continuum.io/) distribution is installed in the user’s home directory. To install the Python library to a different location, create an environment variable called SITE_PACKAGES_DIR and set it equal to the desired installation location prior to running the install command, or supply this as an argument for make:

		make SITE_PACKAGES_DIR=/path/to/site-packages install

Or, as a last resort, you can edit the top-level SmallK makefile to conform to the installation scheme of your system.  You may need root privileges to do the installation, depending on where you choose to install it.

Before testing the installation, the test code needs to access data. The data is located in a separate github repository so that when cloning the code, the large amount of data is not included. The data repository is located on github at [smallk_data](https://github.com/smallk/smallk_data):

To test the build, run this command with DATA_DIR set to wherever the SmallK data repository was cloned:

		make check DATA_DIR=../smallk_data
This will run a series of tests, none of which should report a failure.  Sample output from a run of these tests can be found in section [SmallK Test Results](http://smallk.github.io/documentation/tests/#smalk_tests).

Note: if you installed Elemental version 0.85, you will need to configure your system to find the Elemental shared library.  See the Elemental installation instructions above for information on how to do this.

The command-line applications can be built individually by running the appropriate make command from the top-level SmallK directory.  These commands are:

		To build the smallk library only: 		make libsmallk
		To build the preprocessor only:			make preprocessor
		To build the matrix generator only:		make matrixgen
		To build the nmf only:					make nmf
		To build hierclust only:				make hierclust
		To build flatclust only:				make flatclust

This completes the SmallK NMF library installation.

*Note: Pysmallk requires builds of libsmallk, preprocessor, matrixgen, hierclust, and flatclust*.
<br>[—back to top--](#top)

<h1 id="python_install"> Installation of Python libraries </h1>

<h2 id="osx_python"> OSX:Install Python libraries</h2>

### Install Python scientific packages
Assuming that you have used brew to install gcc, as indicated earlier, you can run the following commands to install the necessary libraries:

		brew install python
		brew install numpy
		brew install scipy

To check your installation, run:

		brew test numpy

IMPORTANT: Check to see that your numpy installation has correctly linked to the needed BLAS libraries.

Ensure that you are running the correct python:

		which python

This should print out /usr/local/bin/python. Open a python terminal by typing ‘python’ at the command line and run the following:

		import numpy as np
		np.__config__.show()

You should see something similar to the following:

		lapack_opt_info:
		extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
		extra_compile_args = ['-msse3']
		define_macros = [('NO_ATLAS_INFO', 3)]

		blas_opt_info:
		extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
		extra_compile_args = ['-msse3', '-I/System/Library/Frameworks/vecLib.framework/Header’]
		define_macros = [('NO_ATLAS_INFO', 3)]

If you are using OpenBLAS, you should see that indicated as well.

### Install Cython: a Python interface to C/C++

First install the Python Package Index utility, pip. Many Python packages are configured to use this package manager, Cython being one.

		brew install pip

Only Cython 0.22 is supported at this time. To check which version is installed on your system use this commands:

		$ python
		>> import Cython
		>> Cython.__version__
		'0.22'
		>> 

To install Cython version 0.22 (if not already installed):

		pip uninstall cython
		pip install cython==0.22

Check the version of cython as above to ensure that Cython version 0.22 is installed.

<br>[—back to top--](#top)
<h2 id="lin_python"> Linux:Install Python libraries</h2>
The Python libraries can easily be installed via pip and apt-get with the following commands:

		apt-get install pip
		pip install numpy
		apt-get install python-scipy
		pip uninstall cython
		pip install cython==0.22

This also ensures that cython version 0.22 is installed, which is the currently supported version. The Makefile assumes an installation path of /usr/local/lib/python2.7/site-packages for the compiled library file. If you are not using apt-get to install your packages, you will need to tell the Makefile where the appropriate site-packages directory is located on your system. Setting the SITE_PACKAGES_DIR command line variable when running make accomplishes this. If this doesn’t work, an alternative way to set this up is to add a line to the .bash_profile file (always back up first):

		export SITE_PACKAGES_DIR="<path to lib/python2.7>/site-packages/"

This allows for special installations of Python such as [Continuum Analytics’ [Anaconda](https://www.continuum.io/) distribution site-packages to be accessed.
<h2 id="build_python"> Build pysmallk shared library</h2>
Before building pysmallk, you must ensure that you have already built the standard SmallK library and applications: libsmallk, preprocessor, matrixgen, hierclust, and flatclust.

All C++ and python libraries and applications can be built simultaneously by setting the PYSMALLK command line variable:

		make PYSMALLK=1

To build pysmallk individually from the pysmallk subdirectory (<path to SmallK>/libsmallk-<version>/pysmallk:
		
		make pysmallk
		
To check the library installation:

		make pysmallk_check DATA_DIR=../smallk_data
This will run a series of tests, none of which should report a failure.

To install the shared library in a globally accessible location, enable the PYSMALLK command line variable and, if needed, specify an INSTALLATION_DIR.
The Makefile assumes an installation path of /usr/local/lib/python2.7/site-packages for the compiled library file. If you are not using brew to install your packages, you will need to tell the Makefile where the appropriate site-packages directory is located on your system. Setting the INSTALLATION_DIR command line variable when running make accomplishes this. Also, make sure that there is not another site-packages directory in your PATH before the site-packages you intend to use since ‘make install’ will copy pysmallk.so to /usr/local/lib/python2.7/site-packages by default. Other Python distributions will probably interfere with the pysmallk installation.

		make install PYSMALLK=1	INSTALLATION_DIR=/usr/local/lib/python2.7/site-packages/

To uninstall the libraries:

		make uninstall PYSMALLK=1	INSTALLATION_DIR=/usr/local/lib/python2.7/site-packages/

<h1 id="matrix_files"> Matrix file formats </h1>

The SmallK software supports comma-separated value (CSV) files for dense matrices and [Matrix Market](http://math.nist.gov/MatrixMarket/formats.html) files for sparse matrices.

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

The matrix is loaded exactly as it appears in the file.  **Internally, SmallK stores dense matrices in column-major order**. Sparse matrices are stored in **compressed column format**.
<br>[—back to top--](#top)

Disclaimer
----------

This software is a work in progress. It will be updated throughout the course of the 
XDATA program with additional algorithms and examples. The distributed NMF 
factorization routine uses sequential algorithms, but it replaces the matrices and matrix 
operations with distributed versions. The GA Tech research group is working on proper 
distributed NMF algorithms, and when such algorithms are available they will be added to 
the library. Thus, the performance of the distributed code should be viewed as being the
baseline for our future distributed NMF implementations.

<h2 id="contact">Contact Info</h2>
For comments, questions, bug reports, suggestions, etc., contact:

Ashley Beavers <br>
Research Scientist <br>
Information and Communications Laboratory (ICL) <br>
Information and Cyber Sciences Directorate (ICSD) <br>
Georgia Tech Research Institute (GTRI) <br>
75 5TH St. NW STE 900 <br>
ATLANTA, GA 30308-1018
<span><a href="mailto:ashley.beavers@gtri.gatech.edu">ashley.beavers@gtri.gatech.edu</a></span>

Barry Drake <br>
Research Scientist <br>
Information and Communications Laboratory (ICL) <br>
Information and Cyber Sciences Directorate (ICSD) <br>
Georgia Tech Research Institute (GTRI) <br>
75 5TH St. NW STE 900 <br>
ATLANTA, GA 30308-1018
<br><span><a href="mailto:barry.drake@gtri.gatech.edu">barry.drake@gtri.gatech.edu</a></span>

Richard Boyd <br>
Research Scientist <br>
Information and Communications Laboratory (ICL) <br>
Information and Cyber Sciences Directorate (ICSD) <br>
Georgia Tech Research Institute (GTRI) <br>
75 5TH St. NW STE 900 <br>
ATLANTA, GA 30308-1018 <br>

<br>[—back to top--](#top)


