
##Smallk: a Library for Nonnegative Matrix Factorization and Clustering

The *smallk* library provides routines for low-rank matrix approximation via nonnegative matrix factorization (NMF).
Given a nonnegative matrix A, the smallk software computes nonnegative matrices W and H such that 

<center>A ~ W*H.</center>


Matrix A has m rows and n columns and is either sparse or dense. Matrix W has m rows and k columns, and matrix H has k rows and n columns.  Matrices W and H are always dense, even when matrix A is sparse.  The value of k is an input parameter to the factorization routines; typically k << m and k << n.   

The idea underlying NMF is to choose the smallest value of k that gives an acceptable approximation error.  An exact *nonnegative* factorization of matrix A is generally not possible, so the factorization proceeds iteratively, attempting to globally minimize an objective function.  As the iterations proceed, the smallk software computes a metric that estimates the progress achieved so far.  When the metric falls below a user-specified tolerance the iterations stop and convergence is declared.
**All matrices are assumed to be in column-major order.**

The smallk library provides implementations of several different NMF algorithms.  These algorithms are:

* Multiplicative Updating (MU)
* Hierarchical Alternating Least Squares (HALS)
* Block Principal Pivoting (BPP)
* Rank2 Specialization (RANK2)

Additional NMF algorithms will be provided in future updates.

Smallk also provides an implementation of a new algorithm for hierarchical and flat clustering, HierNMF2, based on the Rank2 NMF algorithm.  

###Prerequisites

* A modern, C++11-compliant compiler, such as the latest stable release of the GNU or clang compilers
* [Elemental](http://libelemental.org/), a high-performance library for dense, distributed linear algebra, which requires:
	* An MPI installation, such as [OpenMPI](www.open-mpi.org)
	* A BLAS implementation, hopefully optimized/tuned for the local system
	* libFLAME
	* OpenMP
	* CMake

###Installation Instructions

Copy the source code distribution to a folder and edit the Makefile, found in build/Linux/Makefile. Set the path to the elemvariables file appropriate for the local system.  The elemvariables file is found in the Elemental install folder, under conf/elemvariables.

Edit the elemvariables file and add the string -std=c++11 to the `CXX` macro line.  For instance, change

	CXX = /usr/local/bin/g++-4.8

to 

	CXX = /usr/local/bin/g++-4.8 -std=c++11

Then build the code according to the documentation below.

-------------------------
<h3 id="howto_build"> How to Build the Code </h3>
-------------------

This code builds and runs on either Linux or MacOSX.  The makefile builds a static library, libnmf.a, as well as test code.  To build the code, open a terminal shell and cd to build/Linux.

To perform a debug build run these commands:

    make clean
    make CFG=debug

Binaries will be written to ./bin.debug/.

To perform a release build run these commands:

    make clean
    make CFG=release

Binaries will be written to ./bin.release/.

The test program is found in the file ``nmflib2/tests/main_tests.cpp``; the associated binary is called `tests`.  The following tests are included:

* sparse NMF test
* Matlab comparison test
* **clustering test - TBD**

To run the test suite, perform a release build and enter the following at
the command line, from the build directory:

    ./bin.release/tests

The sparse NMF test will generate 64 random sparse matrices of variable sparsity.  It computes the NMF factors W and H, then repeats the computation for a fully dense version of the sparse matrix.  Residuals are computed for the sparse and dense W and H factors, and the maximum residual across all matrices is determined.  The test passes if the maximum residual is less than a small value.

**The Matlab comparison test will...TBD**

**The clustering test will...TBD**

---------------------
<h3 id="NMF sw"> NMF Software </h3>
---------------------

The code for performing NMF on arbitrary matrices is found in the file ``nmflib2/src/main.cpp``.  This is a driver routine that supports a wide range of command-line options for NMF.  To illustrate the use of the code, perform a release build and enter the following at the command line, from the build directory:

	./bin.release/nmf --matrixfile RANDOM --rows 256 --cols 256 --k 16

This command invokes the NMF code and tells it to generate a random 256x256 matrix and factor it into two 256x16 factors W and H.  The W and H factors will be written to default-named files w.csv and h.csv in the build directory.

All command-line options supported by the code can be listed by running this command:

	./bin.release/nmf

The command-line options are described below.

-------------------------
<h3 id="csv_format"> CSV File Format </h3>
-------------------

This software supports a simple CSV format for dense matrix data.  The W and H factors will be written in this format.  The first line of the CSV file specifies the number of rows and columns, separated by a comma.  Subsequent lines of the file give the elements in each **COLUMN** separated by commas.  

For example, the 5x3 matrix 

    42  47  52
    43  48  53
    44  49  54
	45  50  55
	46  51  56

would be stored in a CSV file as:

    5,3
    42,43,44,45,46
    47,48,49,50,51
    52,53,54,55,56

-------------------------
<h3 id="Matrix Market Format"> Matrix Market Format </h3>
-------------------

This library supports sparse matrix file in the common Matrix Market format.  A document describing this format is included in the folder ``nmflib2/doc/MMformat.pdf``.  Several example files are included in the data folder ``nmflib2/data/``.  These files have an MTX extension.

To factor one of the sample sparse matrices (comsol.mtx), use this minimal command from the build folder:

	./bin.release/nmf --matrixfile ../../data/comsol.mtx --k 16

This will tell the code to load the comsol.mtx file from the data folder and factor it into W and H factors having 16 columns.  The W and H factors are written to the default output files w.csv and h.csv in the build folder.

-------------------------
<h3 id="cmdline_options"> Command Line Options </h3>
-------------------

The demo programs support these command line options:

*  `--matrixfile <filename>`
	
	[Required] The required filename argument is either the name of a CSV file (dense matrix input), 
	a Matrix Market file (sparse matrix input) or the string "RANDOM", 
	in which case a random dense matrix will be generated.

*  `--k <integer>`

	[Required] The k-value specifies the number of columns of the W and H matrices.  Smaller values of k allow the code to run faster, but at the cost of a less accurate factorization.  Larger values of k 
	produce a more accurate factorization, but at greatly increased runtime.  The runtime 
	in general is superlinear in k.  The value of k must be less than or equal to the number of columns of the matrix to be factored.

*  `--rows [integer]`

	[Optional] The number of rows in the matrix to be factored.  If the --matrixfile argument specifies a data file, this argument is not required, since the matrix dimensions are taken from the file.  This argument is required only if the matrixfile argument is equal to RANDOM. 

*  `--cols [integer]`

	[Optional] The number of columns in the matrix to be factored.  If the --matrixfile argument specifies a data file, this argument is not required, since the matrix dimensions are taken from the file.  This argument is required only if the matrixfile argument is equal to RANDOM. 


*  `--algorithm [algorithm string]`

	*  ``MU`` : multiplicative updating
	*  ``HALS`` : hierarchical alternating least squares
	*  ``RANK2``: specialization of block principal pivoting for k=2

	[Optional] The default algorithm is HALS.
 
*  `--stopping [stopping criterion string]`


	*  ``PG``: use the projected gradient to estimate progress
	*  ``FNORM``: use the relative change in the Frobenius norm of the W matrix to estimate progress

	[Optional] The stopping criterion argument determines the method used to estimate progress of the computation. NMF is an iterative method.  At each iteration, a progress metric is computed and compared with the tolerance
	argument (see below).  Iterations terminate whenever the progress metric is less than the tolerance.  Two methods are provided to estimate progress of the computation:
	
*  `--infile_W [filename]`

	[Optional] The name of a CSV file containing initial values for the W matrix, or the string "RANDOM", 
	in which case W will be initialized randomly.  The default is random initialization.

*  `--infile_H [filename]`

	[Optional] The path to a CSV file containing initial values for the H matrix, or the string "RANDOM",
	in which case H will be initialized randomly.  The default is random initialization.

*  `--outfile_W [filename]`

	[Optional] The name of the file into which the W factor should be written.  The file is written in
	CSV format.  The default filename is w.csv.

*  `--outfile_H [filename]`

	[Optional] The name of the file into which the H factor should be written.  The file is written in
	CSV format.  The default filename is h.csv.

*  `--tol [float]`

	[Optional] The computation stops whenever the fractional change in the Frobenius norm of the W matrix from one
	iteration to the next is less than this tolerance value.  The default value is 0.005.

*  `--maxiter [integer]`

	[Optional] The maximum number of iterations to perform.  The default value is the largest positive integer value.

*  `--outprecision [integer]`

	[Optional] The number of decimal places to use for writing out the computed W and H factors.  The default
	is ``std::numeric_limits<float>::max_digits10``.

*	`--normalize [integer]`

	[Optional] Whether to normalize the columns of the W matrix to unit norm.  The default is to not normalize W.

*  `--verbose [integer]`

	* 0 == do not print progress updates to the screen on the rank 0 process
	* 1 == print progress updates to the screen on the rank 0 process

	[Optional] Default is 1 (print updates to the screen as the computation progresses).


<h2 id="contact">Contact Info</h2>
For comments, questions, bug reports, suggestions, etc., contact:

     Richard Boyd
     Senior Research Scientist
	 Cyber Technology and Information Security Laboratory
     GA Tech Research Institute
     250 14th St NW
     Atlanta, GA 30318
     richard.boyd@gtri.gatech.edu

     Barry Drake
     Senior Research Scientist
	 Cyber Technology and Information Security Laboratory
     GA Tech Research Institute
     250 14th St NW
     Atlanta, GA 30318
     barry.drake@gtri.gatech.edu


