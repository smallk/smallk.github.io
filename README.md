SmallK Start Page
================

Introduction
----------------
SmallK is a high performance software package for low rank matrix approximation via the nonnegative matrix factorization (NMF). NMF is 
a low-rank approximation where a matrix is approximated 
by a product of two nonnegative factors. 
The role of NMF in data analytics has been as significant as the singular value decomposition (SVD). However, due to 
nonnegativity constraints, NMF has far superior interpretability of its results for many practical problems such as image processing, chemometrics, bioinformatics, topic modeling for text analytics and many more.
Our approach to solving the NMF nonconvex optimization
problem has proven convergence properties and is one of the most efficient 
methods developed to date.


SmallK home page
----------------
[SmallK](http://smallk.github.io): http://smallk.github.io

Distributed NMF library
----------------
Recently open sourced: MPI-FAUN!

Both MPI and OPENMP implementations for MU, HALS and ANLS/BPP based NMF algorithms are available. The implementations can run off the shelf or can be easily integrated into other source code. These are very highly tuned NMF algorithms to work on super computers. We have tested this software in NERSC as well OLCF cluster. The openmp implementation is tested on many different linux variants with intel processors. The library works well for both sparse and dense matrices.

Please visit
[MPI-FAUN](https://github.com/ramkikannan/nmflibrary) for more information and source code.

Acknowledgements
----------------

This work was funded in part by the DARPA XDATA program under contract
FA8750-12-2-0309. Our DARPA
program manager is
[Mr. Wade Shen](http://www.darpa.mil/staff/mr-wade-shen) and our
XDATA principal investigator is [Professor Haesun Park](http://www.cc.gatech.edu/~hpark/) of the 
Georgia Institute of Technology.

SmallK Copyright and Software License
======================================
SmallK is under copyright by the Georgia Institute of Technology, 2014, 2015, 2016. 
All source code is released under the 
[Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0) license.
