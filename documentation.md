---
layout: page
title: Documentation

baselink: /documentation/
permalink: /documentation/
---

### Introduction

High-dimensional data sets are ubiquitous in data science, and they often present serious problems for researchers. These data sets cannot be directly visualized, and most of the available algorithms used for the analysis and classification of large data sets suffer from the "curse of dimensionality", which sharply reduces the speed and accuracy of these algorithms. In reality, most high-dimensional data sets fill only a subspace of the whole space of possible values. For instance, there are common correlations between the results of various medical diagnostic measurements, and certain values are not likely to occur together. Thus, while the full data space may be high dimensional, the data itself usually occupy regions of much lower intrinsic dimensionality.

Our work in dimensionality reduction focuses on, but is not limited to, low-rank approximations via nonnegative matrix factorization (NMF). NMF is a non-convex optimization problem with important applications in data and interactive visual analytics of high-dimensional data. 

The impetus for this document is to provide a step-by-step procedure for the application of the theory to real-world large-scale data and visual analytics. We have instantiated our research efforts in a software framework that includes high-level driver code via Python and a simple command line interface, SmallK, which hides most of the details of the input parameters. Our low-level code, also usable from the command line, is written in C++, which provides efficient implementation of the NMF algorithms. In this document we focus exclusively on the high performance C++ implementations of our first implementations of NMF algorithms. The algorithm implementations discussed herein have numerous practical applications; this document will hopefully provide the information required to quickly begin real work.

Below is a brief description of our fundamental research on NMF algorithms (please see the References section for more detail). Following the brief motivational introduction to the NMF are detailed installation instructions for the NMF software framework.

### Low-rank approximations and NMF

Algorithms that enable dimension reduction and clustering are two critical areas in data analytics and interactive visual analysis of high-dimensional data. A low-rank approximation framework has the ability to facilitate faster processing times and utilize fewer resources. These approximations provide a natural way to compute only what we need for significant dimension reduction, and are analogous to singular value decomposition (SVD) and principal component analysis (PCA). The algorithm framework also works efficiently for clustering since clustering can be viewed as a specific way of achieving a low-rank approximation so that the clustered structure of the data is well represented in a few basis vectors. 

Matrix factorizations such as the SVD have played a key role as a fundamental tool in machine learning, data mining, and other areas of computational science and engineering. The NMF has recently emerged as an important factorization method as well. A distinguishing feature of the NMF is the requirement of nonnegativity: NMF is considered for high-dimensional and large scale data in which the representation of each element is inherently nonnegative, and it seeks low-rank factor matrices that are constrained to have only nonnegative elements. There are many examples of data with a nonnegative representation. In a standard term-frequency encoding, a text document is represented as a vector of nonnegative numbers since each element represents the number of appearances of each term in each document. In image processing, digital images are represented by pixel intensities, which are nonnegative. In the life sciences, chemical concentrations or gene expression levels are naturally represented as nonnegative data.

Our algorithm framework utilizes various constraints on the non-convex optimization problem that gives rise to the nonnegative factors. With these various constraints NMF is a versatile tool for a large variety of data analytics problems. NMF algorithms have been an active area of research for several years. Since much of the data for many important problems in numerous domains is nonnegative NMF is the correct computational model for mining and/or integrating information from such data. NMF also offers enhanced interpretation of results since nonnegativity of the data is preserved.

### Nonnegative Matrix Factorization Library

This library provides routines for nonnegative matrix factorization. Given a nonnegative mxn matrix A, this software computes nonnegative matrices W and H such that

<p style="text-align: center; font-weight: bold;">A&cong;W H</p>

**All matrices are assumed to be in column-major order.** Matrix A has m rows and n columns, and can be either sparse or dense. Matrix W has m rows and k columns, and matrix H has k rows and n columns. The value of k is an input parameter to the factorization routines.

An exact nonnegative factorization of matrix A is generally not possible, so the factorization proceeds iteratively, attempting to globally minimize an objective function. Several NMF algorithms are provided, since no single NMF algorithm is optimal for all data sets. The supported NMF algorithms are:

1. Rank-2 NMF
2. Rank-2 NMF for Hierarchical Clustering
3. Rank-2 NMF for Flat Clustering
4. Hierarchical Alternating Least Squares (HALS)
5. Block Principal Pivoting (BPP) NMF

The last two, 4 and 5, can be used for flat clustering or for other applications such as image processing, interactive visual analytics, speckle removal from SAR images, recommender systems, information fusion, outlier detection, chemometrics, and many more. 

### Prerequisites

The following list is the software packages/libraries required to build the NMF library code:

* A modern, C++11-compliant compiler, such as g++ 4.8.2 or later
* [Elemental](http://libelemental.org/), a high-performance library for dense, distributed linear algebra, which requires:
  * An MPI installation, such as MPICH2 or [OpenMPI](http://www.open-mpi.org/software/ompi/v1.6/)
  * BLAS implementation, hopefully optimized/tuned for the local system
  * LAPACK implementation, hopefully optimized/tuned for the local system
  * [libFLAME](http://www.cs.utexas.edu/~flame/web/libFLAME.html): high performance numerical libraries
  * [OpenMP](http://openmp.org/wp/): a parallelization API
  * CMake 2.8.5 or later
Check the documentation links on this page for additional detailed instructions for installation of the NMF library software and dependencies. If desired, Installation instructions for Elemental can be found here.

Check the documentation links on this page for additional detailed instructions for installation of the NMF library software and dependencies. If desired, Installation instructions for Elemental can be found [here](http://libelemental.org/documentation/0.81/index.html).