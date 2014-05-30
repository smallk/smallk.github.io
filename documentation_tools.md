---
layout: page
title: Command Line Tools

baselink: /documentation/
permalink: /documentation/tools/
---

<h1 id="top">Contents</h1>
---------------------

*   [SmallK Command Line Tools](#smallk_tools)
    *   [Preprocessor](#preproc)
		* [Overview](#overview)
		* [Input Files](#input_files)
		* [Command Line Options](#cmd_options)
		* [Sample Run](#sample_run)
    *   [Matrixgen](#matrix_gen)
		* [Overview](#overview_matgen)
		* [Command Line Options](#cmd_options_matgen)
		* [Sample Run](#sample_run_matgen)
    *   [NMF](#nmf)
		* [Overview](#overview_nmf)
		* [Command Line Options](#cmd_options_nmf)
		* [Sample Run](#sample_run_nmf)
    *   [Hierclust](#hier)
		* [Overview](#overview_hier)
		* [Command Line Options](#cmd_options_hier)
		* [Sample Run](#sample_run_hier)
    *   [Flatclust](#flat)
		* [Overview](#overview_flat)
		* [Command Line Options](#cmd_options_flat)
		* [Sample Run](#sample_run_flat)
    *   [Install libFlame](#mac_libflame)
    *   [Install Elemental](#mac_elemental)

<h1 id="smallk_tools"> SmallK Command Line Tools </h1>

<h2 id="preproc"> Preprocessor </h2>

<h3 id="overview"> Overview </h3>

The preprocessor prunes rows and columns from term-frequency matrices, attempting to generate a result matrix that is more suitable for clustering.  It also computes tf-idf weights for the remaining entries.  Therefore the input matrix consists of nonnegative integers, and the output matrix consists of floating point numbers between 0.0 and 1.0.  The MatrixMarket file format is used for the input and output matrices.

Rows (terms) are pruned if a given term appears in fewer than 'DOCS_PER_TERM' documents.  The value of DOCS_PER_TERM is a command-line parameter with a default value of 3.  For a term-frequencyinput matrix, in which the matrix elements represent occurrence counts for the terms, this parameter actually specifies the minimum row sum for each term.  Any rows whose rowsums are less than this value will be pruned.

Columns (docs) are pruned if a given document contains fewer than 'TERMS_PER_DOC' terms.  The value of TERMS_PER_DOC is a command-line parameter with a default value of 5.

Whenever columns (documents) are pruned the preprocessor checks the remaining columns for uniqueness.  Any duplicate columns are identified and a representative column is chosen as the survivor. The code always selects the column with the largest column index in such groups as the survivor. The preprocessor continues to prune rows and columns until it finds no further candidates for pruning. It then computes new tf-idf scores for the resulting entries and writes out the result matrix in MatrixMarket format.

If the preprocessor should prune all rows or columns, it writes an error message to the screen and terminates without generating any output.

<h3 id="input_files"> Input Files </h3>

The preprocessor requires three input files: a matrix file, a dictionary file, and a document file.  The matrix file contains a sparse matrix in MatrixMarket format (.mtx).  This is a term-frequency matrix, and all entries should be positive integers. The preprocessor can also read in matrices containing floating-point inputs, but only if ‘boolean mode’ is enabled; this will be described below.  The preprocessor does not support dense matrices, since the typical matrices encountered in topic modeling problems are extremely sparse, with occupancies generally less than 1%.

The second file required by the preprocessor is a ‘dictionary file’.  This is a simple ASCII text file containing one entry per line.  Entries represent keywords, bigrams, or other general text strings the user is interested in.  Each line of the file is treated as a ‘keyword’, so multi-word keywords are supported as well.  The smallk/data folder contains a sample dictionary file called ‘dictionary.txt’.  The first few entries are:

		triumph
		dey
		canada
		finger
		circuit
		...

The third file required by the preprocessor is a ‘documents file’.  This is another simple ASCII text file containing one entry per line.  Entries represent document names or other unique identifiers.  The smallk/data folder also contains a sample documents file called ‘documents.txt’.  The first few entries of this file are:
<h3 id="cmd_options"> Command Line Options </h3>

<h3 id="sample_run"> Sample Run </h3>

<h2 id="matrix_gen"> Matrixgen </h2>

<h3 id="overview_matgen"> Overview </h3>

<h3 id="cmd_options_matgen"> Command Line Options </h3>

<h3 id="sample_run_matgen"> Sample Run </h3>

<h2 id="nmf"> NMF </h2>

<h3 id="overview_nmf"> Overview </h3>

<h3 id="cmd_options_nmf"> Command Line Options </h3>

<h3 id="sample_run_nmf"> Sample Run </h3>

<h2 id="hier"> Hierclust </h2>

<h3 id="overview_hier"> Overview </h3>

<h3 id="cmd_options_hier"> Command Line Options </h3>

<h3 id="sample_run_hier"> Sample Run </h3>

<h2 id="flat"> Flatclust </h2>

<h3 id="overview_flat"> Overview </h3>

<h3 id="cmd_options_flat"> Command Line Options </h3>

<h3 id="sample_run_flat"> Sample Run </h3>
