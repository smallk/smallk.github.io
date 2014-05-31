---
layout: page
title: SmallK Command Line Tools

baselink: /documentation/
permalink: /documentation/tools/
---

<h1 id="top">Contents</h1>
---------------------

* [Preprocessor](#preproc)
	* [Overview](#overview)
	* [Input Files](#input_files)
	* [Command Line Options](#cmd_options)
	* [Sample Runs](#sample_run)
* [Matrixgen](#matrix_gen)
	* [Overview](#overview_matgen)
	* [Command Line Options](#cmd_options_matgen)
	* [Sample Runs](#sample_run_matgen)
* [NMF](#nmf)
	* [Overview](#overview_nmf)
	* [Command Line Options](#cmd_options_nmf)
	* [Sample Runs](#sample_run_nmf)
* [Hierclust](#hier)
	* [Overview](#overview_hier)
	* [Command Line Options](#cmd_options_hier)
	* [Sample Runs](#sample_run_hier)
* [Flatclust](#flat)
	* [Overview](#overview_flat)
	* [Command Line Options](#cmd_options_flat)
	* [Sample Runs](#sample_run_flat)

<h1 id="preproc"> Preprocessor </h1>

<h2 id="overview"> Overview </h2>

The preprocessor prunes rows and columns from term-frequency matrices, attempting to generate a result matrix that is more suitable for clustering.  It also computes tf-idf weights for the remaining entries.  Therefore the input matrix consists of nonnegative integers, and the output matrix consists of floating point numbers between 0.0 and 1.0.  The MatrixMarket file format is used for the input and output matrices.

Rows (terms) are pruned if a given term appears in fewer than 'DOCS_PER_TERM' documents.  The value of DOCS_PER_TERM is a command-line parameter with a default value of 3.  For a term-frequencyinput matrix, in which the matrix elements represent occurrence counts for the terms, this parameter actually specifies the minimum row sum for each term.  Any rows whose rowsums are less than this value will be pruned.

Columns (docs) are pruned if a given document contains fewer than 'TERMS_PER_DOC' terms.  The value of TERMS_PER_DOC is a command-line parameter with a default value of 5.

Whenever columns (documents) are pruned the preprocessor checks the remaining columns for uniqueness.  Any duplicate columns are identified and a representative column is chosen as the survivor. The code always selects the column with the largest column index in such groups as the survivor. The preprocessor continues to prune rows and columns until it finds no further candidates for pruning. It then computes new tf-idf scores for the resulting entries and writes out the result matrix in MatrixMarket format.

If the preprocessor should prune all rows or columns, it writes an error message to the screen and terminates without generating any output.

<h2 id="input_files"> Input Files </h2>

The preprocessor requires three input files: a matrix file, a dictionary file, and a document file.  The matrix file contains a sparse matrix in MatrixMarket format (.mtx).  This is a term-frequency matrix, and all entries should be positive integers. The preprocessor can also read in matrices containing floating-point inputs, but only if ‘boolean mode’ is enabled; this will be described below.  The preprocessor does not support dense matrices, since the typical matrices encountered in topic modeling problems are extremely sparse, with occupancies generally less than 1%.

The second file required by the preprocessor is a ‘dictionary file’.  This is a simple ASCII text file containing one entry per line.  Entries represent keywords, bigrams, or other general text strings the user is interested in.  Each line of the file is treated as a ‘keyword’, so multi-word keywords are supported as well.  The smallk/data folder contains a sample dictionary file called ‘dictionary.txt’.  The first few entries are:

		triumph
		dey
		canada
		finger
		circuit
		...

The third file required by the preprocessor is a ‘documents file’.  This is another simple ASCII text file containing one entry per line.  Entries represent document names or other unique identifiers.  The smallk/data folder also contains a sample documents file called ‘documents.txt’.  The first few entries of this file are:
<h2 id="cmd_options"> Command Line Options </h2>

The preprocessor binary is called ‘preprocess_tf’, to emphasize the fact that it operates on term-frequency matrices.  If the binary is run with no arguments, it prints out the following information:

	./preprocess_tf
		--indir <path>
		[--outdir (defaults to current directory)]
		[--docs_per_term 3]
		[--terms_per_doc 5]
		[--maxiter 1000]
		[--precision 4]
		[--boolean_mode 0]

Only the first parameter, --indir, is required.  All remaining params are optional and have the default values indicated.The meanings of the various options are as follows:

	1. --indir: path to the folder containing the files ‘matrix.mtx’, ‘dictionary.txt’, and ‘documents.txt’
	2. –-outdir: path to the folder to into which results should be written
	3. –-docs_per_term: any rows whose entries sum to less than this value will be pruned
	4. –-terms_per_doc: any columns whose entries sum to less than this value will be pruned
	5. –-maxiter: perform no more than this many iterations
	6. –-precision: the number of digits of precision with which to write the output matrix
	7. –-boolean_mode:  all nonzero matrix elements will be treated as if they had the value 1.0.  In other words, the
			preprocessor will ignore the actual frequency counts and treat all nonzero entries as if they were 1.0.

<h2 id="sample_run"> Sample Runs </h2>

Here is a sample run of the preprocessor using the data provided in the smallk distribution.  This run was performed from the top-level smallk folder after building the code:

	./preprocessor/bin/preprocess_tf --indir data

      Command line options: 

                 indir: data/
                outdir: current directory
         docs_per_term: 3
         terms_per_doc: 5
              max_iter: 1000
             precision: 4
          boolean_mode: 0

	Loading input matrix data/matrix.mtx
    		Input file load time: 1.176s.

	Starting iterations...
    		[1] height: 39771, width: 11237, nonzeros: 877453
	Iterations finished.
	New height: 39727
	New width: 11237
	New nonzero count: 877374
	Processing time: 0.074s.

	Writing output matrix reduced_matrix.mtx
	Output file write time: 2.424s.
	Writing dictionary file reduced_dictionary.txt
	Writing documents file reduced_documents.txt
	Dictionary + documents write time: 0.08s.


<h1 id="matrix_gen"> Matrixgen </h1>

<h2 id="overview_matgen"> Overview </h2>

The matrix generator application is a simple tool for generating simple matrices.  These matrices can be loaded by the NMF and clustering tools for various testing scenarios.  Use of the matrix generator is entirely optional.

<h2 id="cmd_options_matgen"> Command Line Options </h2>

Running the matrixgen binary with no options generates the following output:

	./matrixgen 

	Usage: ./matrixgen
         	--height <number of rows> 
         	--width  <number of cols> 
         	--filename <path> 
        	[--type  UNIFORM]  UNIFORM:     matrix with uniformly-distributed random entries
                           DENSE_DIAG:  dense diagonal matrix with uniform random entries
                           SPARSE_DIAG: sparse diagonal matrix with uniform random entries
                           IDENTITY:    identity matrix
                           ONES:        matrix of all ones
                           ZEROS:       matrix of all zeros
                           SPARSE:      sparse matrix with uniform random entries
                                        specify 'nz_per_col' to control occupancy

        	[--rng_center  0.5]   center of random numbers
        	[--rng_radius  0.5]   radius of random numbers
        	[--precision   6]     digits of precision
        	[--nz_per_col  1]     (SPARSE only) nonzeros per column

The --height, --width, and –-filename options are required.  All others are optional and have the default values indicated.

The meanings of the various options are as follows:

	1. --height: number of rows in the generated matrix
	2. –-width: number of columns in the generated matrix
	3. –-filename: name of the output file
	4. –-type: the type of matrix to be generated; the default is a uniformly-distributed random matrix
	5. –-rng_center: random number distribution will be centered on this value
	6. –-rng_radius: random numbers will span this distance to either side of the center value
	7. –-precision: the number of digits of precision with which to write the output matrix
	8. –-nz_per_col:  number of nonzero entries per sparse matrix column; valid only for SPARSE type

<h2 id="sample_run_matgen"> Sample Runs </h2>

Suppose we want to generate a matrix of uniformly-distributed random numbers.  The matrix should have a height of 100 and a width of 16, and should be written to a file called ‘w_init.csv’.  Use the matrix generator as follows:

	./matrixgen –-height 100 –-width 16 –-filename w_init.csv


<h1 id="nmf"> NMF </h1>


<h2 id="overview_nmf"> Overview </h2>

The NMF command line application performs nonnegative matrix factorization on dense or sparse matrices. If the input matrix is denoted by A, nonnegative matrix factors Wand H are computed such that

<p style="text-align: center; font-weight: bold;">A &cong; W H</p>

Matrix A can be either dense or sparse; matrices W and H are always dense. Matrix A has m rows and n columns; matrix W has m rows and k columns; matrix H has k rows and n columns. Parameter k is a positive integer and is typically much less than either m or n.

<h2 id="cmd_options_nmf"> Command Line Options </h2>


Running the nmf application with no command line parameters will cause the application to display all params that it supports. These are:

	Usage: ./nmf
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
        [--maxthreads    8]      Upper limit to thread count. 
        [--normalize  1]         Whether to normalize W and scale H.
                                     1 == yes, 0 == no 
        [--verbose  1]           Whether to print updates to the screen. 
                                     1 == print updates, 0 == silent

The –-matrixfile and –-k options are required; all others are optional and have the default values indicated.  The meanings of the various options are as follows:

	1.  --matrixfile: Filename of the matrix to be factored.  CSV files are supported for dense matrices and MTX files
 			for sparse matrices.
	2.  –-k: the width of the W matrix (identical to the height of the H matrix)
	3.  –-algorithm: identifier for the factorization algorithm
	4.  –-stopping: the method used to terminate the iterations; use PG_RATIO unless you have a specific reason not to
	5.  –-tol: tolerance value used to terminate iterations; when the progress metric falls below this value iterations
 			will stop; typical values are in the 1.0e-3 or 1.0e-4 range
	6.  –-tolcount: a positive integer representing the number of successive iterations for which the progress metric
 			must have a value <= tolerance; default is 1, which means the iterations will terminate on the first 			iteration with: progress_metric <= tolerance 
	7.  –-infile_W: CSV file containing the mxk initial values for matrix W; if omitted, W is randomly initialized
	8.  –-infile_H:  CSV file containing the kxn initial values for matrix H; if omitted, H is randomly initialized
	9.  –-outfile_W: filename for the computed W factor; default is w.csv
	10. –-outfile_H: filename for the computed H factor; default is h.csv
	11. –-miniter: the minimum number of iterations to perform before checking progress; for smaller tolerance values,
 			you may want to increase this number to avoid needless progress checks
	12. –-maxiter: the maximum number of iterations to perform
	13. –-outprecision: matrices W and H will be written to disk using this many digits of precision
	14. –-maxthreads: the maximum number of threads to use; the default is to use as many threads as the hardware can
		 	support (your number may differ from that shown) 
	15. –-normalize: whether to normalize the columns of the W matrix and correspondingly scale the rows of H after
 			convergence
	16. –-verbose: whether to display updates to the screen as the iterations progress 


<h2 id="sample_run_nmf"> Sample Runs </h2>

The smallk distribution contains a ‘data’ directory with a matrix file ‘reuters.mtx’.  This is a tf-idf weighted matrix derived from the popular Reuters data set used in machine learning experiments.  

Suppose we want to factor the Reuters matrix using a k value of 8.  We would do that as follows, assuming that we are in the top-level smallk folder after building the code:

		nmf/bin/nmf –-matrixfile data/reuters.mtx  --k 8

If we want to instead use the HALS algorithm with k=16, a tolerance of 1.0e-4, and also perform 10 iterations prior to checking progress, we would use this command line:

		nmf/bin/nmf –-matrixfile data/reuters.mtx –-k 16 –-algorithm HALS –-tol 1.0e-4 –-miniter 10


To repeat the previous experiment but with new names for the output files, we would do this:

		nmf/bin/nmf –-matrixfile data/reuters.mtx –-k 16 –-algorithm HALS –-tol 1.0e-4 
			–-miniter 10 –-outfile_W w_hals.csv –outfile_H h_hals.csv


<h1 id="hier"> Hierclust </h1>

<h2 id="overview_hier"> Overview </h2>

<h2 id="cmd_options_hier"> Command Line Options </h2>

<h2 id="sample_run_hier"> Sample Runs </h2>

<h1 id="flat"> Flatclust </h1>

<h2 id="overview_flat"> Overview </h2>

<h2 id="cmd_options_flat"> Command Line Options </h2>

<h2 id="sample_run_flat"> Sample Runs </h2>

