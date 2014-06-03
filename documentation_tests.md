---
layout: page
title: Tests

baselink: /documentation/
permalink: /documentation/tests/
---

<h1 id="top">Contents</h1>
---------------------

*   [SmallK Test Results](#smalk_tests)

Below we provide example output for many of the tests that can be run in the SmallK library. This will provide guidance for identifying issues with your installation. Of course results will vary on different machines.

<h1 id="smalk_tests">SmallK Test Results </h1>


After building the smallk library, the ‘make check’ command will run a bash script that performs a series of tests on the code.  This is a sample output of those tests:

		make check
		sh tests/scripts/test_smallk.sh

	*****************************************************
	*                                                   *
	*           Testing the smallk interface.           *
	*                                                   *
	*****************************************************
	WARNING: Could not achieve THREAD_MULTIPLE support.
	Smallk major version: 1
	Smallk minor version: 0
	Smallk patch level:   0
	Smallk version string: 1.0.0
	Loading matrix...

	Running rank2 Nmf...

	Initializing matrix W...
	Initializing matrix H...

                parameters: 

             algorithm: Rank 2
    stopping criterion: Ratio of Projected Gradients
                height: 12411
                 width: 7984
                     k: 2
               miniter: 1
               maxiter: 5000
                   tol: 0.005
            matrixfile: data/reuters.mtx
            maxthreads: 8

	1:  progress metric:    (min_iter)
	2:  progress metric:    0.426546
	3:  progress metric:    0.145075
	4:  progress metric:    0.0852205
	5:  progress metric:    0.0558555
	6:  progress metric:    0.0374741
	7:  progress metric:    0.0252389
	8:  progress metric:    0.0169805
	9:  progress metric:    0.0113837
	10: progress metric:    0.00761077
	11: progress metric:    0.0050782
	12: progress metric:    0.00338569

	Solution converged after 12 iterations.

	Norms for matrix W: 
	|| W ||_max = 0.395894
	|| W ||_1   = 12.5708
	|| W ||_oo  = 0.57522
	|| W ||_F   = 1.41421

	Norms for matrix H: 
	|| H ||_max = 0.861319
	|| H ||_1   = 0.884251
	|| H ||_oo  = 1018.43
	|| H ||_F   = 23.6581

	Elapsed wall clock time: 0.033 sec.

	Writing output files...

	Running HierNmf2...

	loading dictionary...
	creating random W initializers...
	creating random H initializers...

            parameters: 

                height: 12411
                 width: 7984
            matrixfile: data/reuters.mtx
              dictfile: data/reuters_dictionary.txt
                   tol: 0.0001
               miniter: 1
               maxiter: 5000
              maxterms: 5
            maxthreads: 8
	[1] [2] [3] [4] 

	Elapsed wall clock time: 444 ms.
	9/9 factorizations converged.

	Writing output files...
	W matrix test passed
	H matrix test passed
	*****************************************************
	*                                                   *
	*            Testing the preprocessor.              *
	*                                                   *
	*****************************************************

      Command line options: 

                 indir: data/
                outdir: current directory
         docs_per_term: 3
         terms_per_doc: 5
              max_iter: 1000
             precision: 4
          boolean_mode: 0

	Loading input matrix data/matrix.mtx
    		Input file load time: 1.216s.

	Starting iterations...
    		[1] height: 39771, width: 11237, nonzeros: 877453
	Iterations finished.
    		New height: 39727
    		New width: 11237
    		New nonzero count: 877374
	Processing time: 0.045s.

	Writing output matrix reduced_matrix.mtx
	Output file write time: 2.357s.
	Writing dictionary file reduced_dictionary.txt
	Writing documents file reduced_documents.txt
	Dictionary + documents write time: 0.082s.
	preprocessor matrix test passed
	preprocessor dictionary test passed
	preprocessor documents test passed
	*****************************************************
	*                                                   *
	*            Testing the NMF routines.              *
	*                                                   *
	*****************************************************
	WARNING: Could not achieve THREAD_MULTIPLE support.
	Loading matrix...
	Initializing matrix W...
	Initializing matrix H...

      Command line options: 

             algorithm: Rank 2
    		stopping criterion: Ratio of Projected Gradients
                height: 12411
                 width: 7984
                     k: 2
               miniter: 1
               maxiter: 5000
                   tol: 0.005
              tolcount: 1
               verbose: 1
             normalize: 1
          outprecision: 6
            matrixfile: data/reuters.mtx
              infile_W: data/nmf_init_w.csv
              infile_H: data/nmf_init_h.csv
             outfile_W: w.csv
             outfile_H: h.csv
            maxthreads: 8

	1:  progress metric:    (min_iter)
	2:  progress metric:    0.426546
	3:  progress metric:    0.145075
	4:  progress metric:    0.0852205
	5:  progress metric:    0.0558555
	6:  progress metric:    0.0374741
	7:  progress metric:    0.0252389
	8:  progress metric:    0.0169805
	9:  progress metric:    0.0113837
	10: progress metric:    0.00761077
	11: progress metric:    0.0050782
	12: progress metric:    0.00338569

	Solution converged after 12 iterations.

	Norms for matrix W: 
	|| W ||_max = 0.395894
	|| W ||_1   = 12.5708
	|| W ||_oo  = 0.57522
	|| W ||_F   = 1.41421

	Norms for matrix H: 
	|| H ||_max = 0.861319
	|| H ||_1   = 0.884251
	|| H ||_oo  = 1018.43
	|| H ||_F   = 23.6581

	Elapsed wall clock time: 0.031 sec.

	Writing output files...
	NMF W matrix test passed
	NMF H matrix test passed
	*****************************************************
	*                                                   *
	*                Testing hierclust.                 *
	*                                                   *
	*****************************************************
	WARNING: Could not achieve THREAD_MULTIPLE support.
	loading dictionary...
	loading matrix...
	loading W initializers...
	loading H initializers...

     	Command line options: 

                height: 12411
                 width: 7984
            matrixfile: data/reuters.mtx
              infile_W: data/hierclust_init_w.csv
              infile_H: data/hierclust_init_h.csv
              dictfile: data/reuters_dictionary.txt
            assignfile: assignments_5.csv
                format: XML
              treefile: tree_5.xml
              clusters: 5
                   tol: 0.0001
                outdir: 
               miniter: 1
               maxiter: 5000
              maxterms: 5
            maxthreads: 8
            unbalanced: 0.1
       	trial_allowance: 3
                  flat: 0
               verbose: 1

	[1] [2] [3] [4] 

	Elapsed wall clock time: 583 ms.
	9/9 factorizations converged.

	Writing output files...
	hierclust cluster file test passed
	hierclust assignment file test passed
	*****************************************************
	*                                                   *
	*              Testing flatclust.                   *
	*                                                   *
	*****************************************************
	WARNING: Could not achieve THREAD_MULTIPLE support.
	loading dictionary...
	loading matrix...
	Initializing matrix W...
	Initializing matrix H...

     	Command line options: 

                height: 256
                 width: 256
            matrixfile: data/rnd_256_256.csv
              infile_W: data/flatclust_init_w.csv
              infile_H: data/flatclust_init_h.csv
              dictfile: data/reuters_dictionary.txt
            assignfile: assignments_16.csv
                format: XML
             clustfile: clusters_16.xml
             algorithm: HALS
              clusters: 16
                   tol: 0.0001
                outdir: 
               miniter: 1
               maxiter: 5000
              maxterms: 5
            maxthreads: 8
               verbose: 1

	1:  progress metric:    (min_iter)
	2:  progress metric:    0.635556
	3:  progress metric:    0.490817
	4:  progress metric:    0.479135
	5:  progress metric:    0.474986
	6:  progress metric:    0.44968
	7:  progress metric:    0.422542
	8:  progress metric:    0.407662
	9:  progress metric:    0.395145
	10: progress metric:    0.379238
	11: progress metric:    0.36705
	12: progress metric:    0.35758
	13: progress metric:    0.350583
	14: progress metric:    0.343709
	15: progress metric:    0.336702
	16: progress metric:    0.328778
	17: progress metric:    0.318239
	18: progress metric:    0.302184
	19: progress metric:    0.286608
	20: progress metric:    0.272868
	< many lines omitted>
	2040:   progress metric:    0.000157751
	2060:   progress metric:    0.000147217
	2080:   progress metric:    0.000137148
	2100:   progress metric:    0.000127922
	2120:   progress metric:    0.000119548
	2140:   progress metric:    0.000111997
	2160:   progress metric:    0.000104838

	Solution converged after 2175 iterations.

	Norms for matrix W: 
	|| W ||_max = 0.228042
	|| W ||_1   = 12.0591
	|| W ||_oo  = 0.806898
	|| W ||_F   = 4

	Norms for matrix H: 
	|| H ||_max = 3.56479
	|| H ||_1   = 12.3864
	|| H ||_oo  = 193.987
	|| H ||_F   = 61.6609

	Norms for the residual: 
	||A - W H||_max = 0.762511
	||A - W H||_1   = 63.9005
	||A - W H||_oo  = 62.4803
	||A - W H||_F   = 66.4552

	Elapsed wall clock time: 0.997 sec.

	XML file test passed
	assignment file test passed
[--back to top--](#top)

