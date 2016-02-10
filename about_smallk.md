### SMALLK
SmallK is a high performance software package for low rank matrix approximation via the nonnegative matrix factorization (NMF). Algorithms for NMF compute the low rank factors of a matrix producing 
two nonnegative matrices whose product approximates the original matrix. 
The role of NMF in data analytics has been as significant as the singular value decomposition (SVD). However, due to 
nonnegativty constraints, NMF has far superior interpretability of its results for many practical problems such as image processing, chemometrics, bioinformatics, topic modeling for text analytics and many more.
Our approach to solving the NMF nonconvex optimization
problem has proven convergence properties and is one of the most efficient 
methods developed to date.

### SMALLK Code
High-dimensional data sets are ubiquitous in data science, and they often present serious problems for researchers. Our work in dimensionality reduction focuses on, but is not limited to, low-rank approximations via nonnegative matrix factorization (NMF) [see [Publications [1,2]](http://smallk.github.io/publications/)]. NMF is a non-convex optimization problem with important applications in data and interactive visual analytics of high-dimensional data. 

The impetus for our website and documentation is to provide a step-by-step procedure for the application of the theory to real-world large-scale data analytics problems. We have instantiated our research efforts in a software framework that includes high-level driver code via Python and a simple command line interface, SmallK, which hides most of the details of the input parameters. Our low-level code, also usable from the command line, is written in C++, which provides efficient NMF algorithms. The algorithms discussed herein have numerous practical applications; this document and the [tutorials](http://smallk.github.io/documentation/tutorials/) will provide the information required to quickly begin real work.
