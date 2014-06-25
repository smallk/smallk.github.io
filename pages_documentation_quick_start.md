---
layout: page
title: Quick Start

baselink: /documentation/
permalink: /documentation/quickstart/
---

Page under construction

<h1 id="top">Contents</h1>
---------------------

*   [Introduction](#intro)
*   [C++ Project Setup](#setup)
*   [Load a Matrix](#load_matrix)
*   [Perform NMF on the Loaded Matrix](#run_nmf)
    *   [NMF-BPP](#bpp)
    *   [Build the SmallK library](#build_smallk)
    *   [Examples of API Usage](#example_api)
    *   [Matrix file formatsI Usage](#matrix_files)
    *   [SmallK APII Usage](#smallk_api)
*   [Contact Information](#contact)



<h1 id="intro"> Introduction </h1>

This document describes how to use the SmallK library to perform nonnegative matrix factorization (NMF), hierarchical clustering, and flat clustering. It is assumed that the library has been installed properly, that all tests have passed, and that the user has created the SMALLK_INSTALL_DIR environment variable as described in the documentation. 
SmallK provides a very simple interface to NMF and clustering algorithms. Examples of how to use this interface are described in this document. The SmallK distribution also provides a suite of command-line tools for NMF and clustering, suitable for advanced users.

<h1 id="setup"> C++ Project Setup </h1>

The SmallK distribution includes an ‘examples’ folder containing two files: smallk_examples.cpp and a Makefile. To build the example CPP file, open a terminal window, cd to the smallk/examples folder, and run the command ‘make’.  If the SmallK library has been installed properly, the project should build and the binary file bin/example will be created.  To run the example, run this command from the smallk/examples folder:
		./bin/example ../data
To experiment with the SmallK library, make a backup copy of smallk_examples.cpp as follows:
		cp smallk_examples.cpp smallk_examples.cpp.bak
The file smallk_examples.cpp can now be used for experimentation. The original file can be restored from the backup at the user’s discretion.

Delete lines 61-255 from smallk_examples.cpp (everything between the opening and closing braces of the ‘try’ block). New code will be added between these braces in the steps below.

All of the examples described in this document use a matrix derived from Reuters articles. This matrix will be referred to as the ‘Reuters’ matrix. It is a sparse matrix with 12411 rows and 7984 columns.

The SmallK documentation contains complete descriptions of all SmallK functions mentioned in this guide.

<h1 id="load_matrix"> Load a Matrix </h1>

Suppose you want to perform NMF or clustering on a matrix. The first action to take is to load the matrix into SmallK using the *LoadMatrix* function. This function accepts either dense matrices in CSV format or sparse matrices in MatrixMarket format.  Since we want to perform NMF and clustering on the Reuters matrix, we need to supply the path to the Reuters matrix file (reuters.mtx) as an argument to LoadMatrix.  This path has already been setup in the code; the appropriate string variable is called ‘filepath_matrix’.  Enter the following line after the opening brace of the try block after line 61:

		smallk::LoadMatrix(filepath_matrix);

Save the file and run the following commands, which should complete without error:
		make clean
		make

Once a matrix is loaded into SmallK it remains loaded until it is replaced with a new call to LoadMatrix. Thus, SmallK makes it easy to experiment with different factorization or clustering parameters, without having to reload a matrix each time.

<h1 id="run_nmf"> Perform NMF on the Loaded Matrix </h1>

Having loaded the Reuters matrix, we can now run different NMF algorithms and factor the matrix in various ways. The SmallK code factors the loaded matrix (denoted by A) as A ~ W*H, where A is mxn, W is mxk, and H is kxn.  The NMF is a low-rank approximation where the value of k, the rank, is an input parameter to the factorization routines, and is generally much smaller than either m or n. Matrix A can be either sparse or dense; matrices W and H are always dense.

[--back to top--](#top)

<h2 id="bpp"> NMF-BPP </h2>


