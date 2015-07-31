---
layout: page
title: Pysmallk

baselink: /documentation/
permalink: /documentation/pysmallk/
---

<h1 id="top">Contents</h1>
---------------------
*   [Introduction](#intro)
*   [Examples of Pysmallk Usage](#examples)
*   [Pysmallk Functions](#functions)
    *   [Preprocessor](#preprocessor)
    *   [Matrixgen](#matrixgen)
    *   [SmallK API](#smallkapi)
    *   [Flatclust](#flatclust)
    *   [Heirclust](#hierclust)

<h2 id="intro"> Introduction </h2>
Why Python? Although it’s perfectly fine to run SmallK from the command line, Python provides a great deal more flexibility that augments the C++ code with other tasks that are much more easily accomplished with a very high level language. Python distributions can be easily extended with open source libraries from third party sources as well, two examples being numpy and scipy, well-known standards for scientific computing in the Python community. There are numerous packages available that extend these scientific libraries into the data analytics domain as well, such as [scikit-learn](http://scikit-learn.org/stable/index.html).

Pysmallk has five classes, each of which represents one of the SmallK tools: SmallkAPI (the simplistic Smallk API), Flatclust, Hierclust, Matrixgen, and Preprocessor. These tools can be strung together into various kind of applications. Examples of such applications can be found in examples/pysmallk_example.py and in the pysmallk/tests/ subdirectory.

The smallk_data repository contains several files (articles_matrix.mtx, articles_documents.txt, articles_dictionary.txt) that contain the matrix and associated text files created from 2,424 news articles. 

First, we will need to import numpy and the shared libary:

	import numpy as np
	import pysmallk
	
We then should apply the preprocessor to our data:

	p = pysmallk.Preprocessor()
	p.load_matrix(filepath='smallk_data/articles_matrix.mtx')
	p.load_dictionary(filepath='smallk_data/articles_dictionary.txt')
	p.load_documents(filepath='smallk_data/articles_documents.txt')
	
We will begin with the default inputs and run preprocess:
	
	p.preprocess()
	
Instead of writing the results to files, we can get the outputs from the Preprocessor class and pass them directly as inputs to the SmallkAPI class.

	reduced_docs = p.get_reduced_documents()
	reduced_dict = p.get_reduced_dictionary()
	reduced_scores = p.get_reduced_scores()
	reduced_row_indices = p.get_reduced_row_indices()
	reduced_col_offsets = p.get_reduced_col_offsets()
	reduced_height = len(reduced_dict)
	reduced_width = len(reduced_docs)

Now let's instantiate the SmallkAPI object that we will use to do further computations.

	sk = pysmallk.SmallkAPI()

One of the options for matrix loading is to pass in the appropriate fields for a sparse matrix, as so.

	sk.load_matrix(buffer=reduced_scores, row_indices=reduced_row_indices, col_offsets=reduced_col_offsets, height=reduced_height, width=reduced_width, nz=len(reduced_scores))

The input matrix alone is sufficient to run NMF and compute the factor matricies.

	sk.nmf(5, 'BPP')

This will compute the W and H factor matrices and subsequently write them to the files w.csv and h.csv, respectively.

We can continue with further calcuations using the same input matrix. For example, we can extract topic models from the input matrix if we also provide a dictionary.

	sk.load_dictionary(dictionary=reduced_dict)
	sk.hiernmf2(5)

This will use Hierarchical NMF to determine the final leaf nodes to use for the topic models and will output assignments_5.csv (cluster labels) and tree_5.xml.

Now let's say we want to create our own random matrix and pass that as a numpy matrix into SmallK.
	
	a = np.random.random((256, 256))

In order to run the Hierclust or Flatclust applications, we will need to provide a dictionary file from which to select the top terms.

	pathtodict = args.indir + 'reuters_dictionary.txt'
	with open(pathtodict) as dictionary:
    	terms = dictionary.read().split("\n")
	    
For illustration, let's use the Flatclust object and extract the resulting assignments from running NMF.

	f = pysmallk.Flatclust()

	f.load_matrix(matrix=a)
	f.load_dictionary(dictionary=terms)
	f.cluster(16, algorithm='HALS')
	a = f.get_assignments()

Now the variable 'a' holds a list of the computed assignment labels for each of the 256 elements in our original matrix.

When we are finished, we should clean up the environment before exiting:

	sk.finalize()
	f.finalize()


<h2 id="functions"> Pysmallk Functions </h2>
Pysmallk has five classes, each of which represents one of the SmallK tools: SmallkAPI (the simplistic Smallk API), Flatclust, Hierclust, Matrixgen, and Preprocessor. Each of these classes can be imported as follows:
<h3 id="preprocessor"> Preprocessor </h3>

	def parser()



* maxiter:      The maximum number of iterations (optional)

<h3 id="matrixgen"> Matrixgen </h3>

	def parser()









<h3 id="smallkapi"> SmallkAPI </h3>

	def parser()

	* width:         The width of the dense matrix








<h3 id="flatclust"> Flatclust </h3>

	def parser()

	* width:         The width of the dense matrix







	def parser()

	* width:         The width of the dense matrix


* min_iter:    Minimum number of iterations (optional)




