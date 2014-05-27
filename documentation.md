---
layout: page
title: Documentation

baselink: /documentation/
permalink: /documentation/
---

### Introduction

High-dimensional data sets are ubiquitous in data science, and they often present serious problems for researchers. These data sets cannot be directly visualized, and most of the available algorithms used for the analysis and classification of large data sets suffer from the "curse of dimensionality", which sharply reduces the speed and accuracy of these algorithms. In reality, most high-dimensional data sets fill only a subspace of the whole space of possible values. For instance, there are common correlations between the results of various medical diagnostic measurements, and certain values are not likely to occur together. Thus, while the full data space may be high dimensional, the data itself usually occupy regions of much lower intrinsic dimensionality.

Our work in dimensionality reduction focuses on, but is not limited to, low-rank approximations via nonnegative matrix factorization (NMF) [see [Publications](http://smallk.github.io/publications/) 1, 2]. NMF is a non-convex optimization problem with important applications in data and interactive visual analytics of high-dimensional data. 

The impetus for this document is to provide a step-by-step procedure for the application of the theory to real-world large-scale data and visual analytics. We have instantiated our research efforts in a software framework that includes high-level driver code via Python and a simple command line interface, SmallK, which hides most of the details of the input parameters. Our low-level code, also usable from the command line, is written in C++, which provides efficient NMF algorithms. The algorithms discussed herein have numerous practical applications; this document will hopefully provide the information required to quickly begin real work.

Below is a brief description of our fundamental research on NMF algorithms (please see the References section for more detail). Following the brief motivational introduction to the NMF are detailed installation instructions for the NMF software framework.

### Low-rank approximations and NMF

Algorithms that enable dimension reduction and clustering are two critical areas in data analytics and interactive visual analysis of high-dimensional data. A low-rank approximation framework has the ability to facilitate faster processing times and utilize fewer resources. These approximations provide a natural way to compute only what we need for significant dimension reduction, and are analogous to singular value decomposition (SVD) and principal component analysis (PCA). Our algorithm framework also works efficiently for clustering since clustering can be viewed as a specific way of achieving a low-rank approximation so that the cluster structure of the data is well represented in a few basis vectors. 

Matrix factorizations such as the SVD have played a key role as a fundamental tool in machine learning, data mining, and other areas of computational science and engineering. The NMF has recently emerged as an important factorization method as well. A distinguishing feature of the NMF is the requirement of nonnegativity: NMF is considered for high-dimensional and large scale data in which the representation of each element is inherently nonnegative, and it seeks low-rank factor matrices that are constrained to have only nonnegative elements. There are many examples of data with a nonnegative representation. In a standard term-frequency encoding, a text document is represented as a vector of nonnegative numbers since each element represents the number of appearances of each term in each document. In image processing, digital images are represented by pixel intensities, which are nonnegative. In the life sciences, chemical concentrations or gene expression levels are naturally represented as nonnegative data.

Our algorithm framework utilizes various constraints on the non-convex optimization problem that gives rise to the nonnegative factors. With these various constraints NMF is a versatile tool for a large variety of data analytics problems. NMF algorithms have been an active area of research for several years. Since much of the data for many important problems in numerous domains is nonnegative NMF is the correct computational model for mining and/or integrating information from such data. NMF also offers enhanced interpretation of results since nonnegativity of the data is preserved.

### Nonnegative Matrix Factorization Library

The SmallK library provides routines for low-rank matrix approximation via nonnegative matrix factorization (NMF). Given a nonnegative matrix A, the SmallK software computes nonnegative matrices W and H such that

<p style="text-align: center; font-weight: bold;">A &cong; W H</p>

Matrix A has m rows and n columns and can be either sparse or dense.  W has m rows and k columns, and H has k rows and n columns. W and H are always dense, even when A is sparse.  The value of k is an input parameter to the factorization routines; typically k << m and k << n.

NMF algorithms seek to approximate matrix A by the product of two much smaller matrices W and H.  The idea is to choose the smallest value of k (width of W and height of H) that gives an acceptable approximation error.  An exact nonnegative factorization of A is generally not possible, so the factorization proceeds iteratively, attempting to globally minimize an objective function.  As the iterations proceed, the SmallK code computes a metric that estimates the progress of the algorithm.  When the metric falls below a user-specified tolerance the iterations stop and convergence is declared.

The SmallK library provides implementations of several different NMF algorithms.  These algorithms are:

		1. Multiplicative Updating (NMF-MU)
		2. Hierarchical Alternating Least Squares (NMF-HALS)
		3. Block Principal Pivoting (NMF-BPP)
		4. Rank2 Specialization (NMF-RANK2)

SmallK also provides implementations of hierarchical and flat clustering.  These routines are:

		1. Hierarchical Clustering via NMF-RANK2
		2. Flat Clustering via NMF-RANK2
		3. Flat Clustering via NMF-BPP or NMF-HALS

These clustering algorithms are suitable in many applications such as image processing, interactive visual analytics, speckle removal from SAR images, recommender systems, information fusion, outlier detection, chemometrics, and many more.
<p> The SmallK library requires either MacOSX or Linux.  A Windows version may be provided in the future.
### Prerequisites
The following list is the software packages/libraries required to build the SmallK NMF library code:

* A modern, C++11-compliant compiler, such as g++ 4.8.2 or later
* [Elemental](http://libelemental.org/), a high-performance library for dense, distributed linear algebra, which requires:
  * An MPI installation, such as [OpenMPI](http://www.open-mpi.org/software/ompi/v1.6/)
  * BLAS implementation, hopefully optimized/tuned for the local system
  * [libFLAME](http://www.cs.utexas.edu/~flame/web/libFLAME.html): a high-performance library for dense numerical linear algebra
  * [OpenMP](http://openmp.org/wp/): (optional) 
  * CMake

Elemental can make use of OpenMP parallelization if available.  This is generally advantageous for large problems.  The SmallK code is also internally parallelized to take full advantage of multiple CPU cores for maximum performance.  SmallK does not currently support distributed computation, but it is planned for future updates.

<b>The SmallK software supports the latest stable release of Elemental, version 0.83</b>.

Check the documentation links on this page for additional detailed instructions for installation of the NMF library software and dependencies. If desired, Installation instructions for Elemental can be found [here](http://libelemental.org/documentation/0.81/index.html).
