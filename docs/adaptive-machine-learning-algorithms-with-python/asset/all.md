![](images/978-1-4842-8017-1_CoverFigure.jpg)

ISBN 978-1-4842-8016-4e-ISBN 978-1-4842-8017-1 [https://doi.org/10.1007/978-1-4842-8017-1](https://doi.org/10.1007/978-1-4842-8017-1) © Chanchal Chatterjee 2022 Apress Standard The use of general descriptive names, registered names, trademarks, service marks, etc. in this publication does not imply, even in the absence of a specific statement, that such names are exempt from the relevant protective laws and regulations and therefore free for general use. The publisher, the authors and the editors are safe to assume that the advice and information in this book are believed to be true and accurate at the date of publication. Neither the publisher nor the authors or the editors give a warranty, expressed or implied, with respect to the material contained herein or for any errors or omissions that may have been made. The publisher remains neutral with regard to jurisdictional claims in published maps and institutional affiliations.

This Apress imprint is published by the registered company APress Media, LLC part of Springer Nature.

The registered company address is: 1 New York Plaza, New York, NY 10004, U.S.A.

*I dedicate this book to my father, Basudev Chatterjee, and all my teachers and mentors who have guided and inspired me.*

Preface

This book presents several categories of streaming data problems that have significant value in machine learning, data visualization, and data analytics. It offers many adaptive algorithms for solving these problems on streaming data vectors or matrices. Complex neural network-based applications are commonplace and computing power is growing exponentially, so why do we need adaptive computation?

Adaptive algorithms are critical in environments where the data volume is large, data has high dimensions, data is time-varying and has changing underlying statistics, and we do not have sufficient storage, computing power, and bandwidth to process the data with low latency. One such environment is computation on edge devices.

Due to the rapid proliferation of billions of devices at the cellular edge and the exponential growth of machine learning and data analytics applications on these devices, there is an urgent need to manage the following on these devices:

*   Power usage for computation at scale

*   Non-stationarity of inputs and drift of the incoming data

*   Latency of computation on devices

*   Memories and bandwidth of devices

The [2021 Gartner report on Edge computation](https://www.itweb.co.za/content/lwrKx73KXLL7mg1o) [Stratus Technologies, 2021] suggests that device-based computation growth propelled by the adoption of cloud and 5G will require us to prioritize and build a framework for edge computation.

In these environments are the following constraints:

*   The data cannot be batched immediately and needs to be used instantaneously. We have a streaming sequence of vectors or matrices as inputs to the algorithms.

*   The data changes with time. In other words, the data is non-stationary, causing significant drift of input features whereby the machine learning models are no longer effective over time.

*   The data volume and dimensionality are large, and we do not have the device memory, bandwidth, or power to store or upload the data to the cloud.

In these applications, we use adaptive algorithms to manage the device’s power, memory, and bandwidth so that we can maintain accuracy of the pretrained models. Some use cases are the following:

1.  Calculate feature drift of incoming data and detect training-serving skew [Kaz et al*.* 2021] ahead of time.

2.  Adapt to incoming data drift and calculate features that best fit the data.

3.  Calculate anomalies in incoming data so that good, clean data is used by the models.

4.  Compress incoming data into features for use in new model creation.

In Chapter [8](#522202_1_En_8_Chapter.xhtml), I present solutions to these problems with adaptive algorithms discussed with real-world data.

## Detecting Feature Drift

See the example where the real-time data [Vinicius Souza et al. 2020] has a gradual drift of features. In real-time environments there are changes in the underlying distribution of the observed data. These changes in the statistical properties of the data are called *drift*. When the changes in statistical properties are smooth, it is called *gradual drift*. Figure [1](#522202_1_En_BookFrontmatter_OnlinePDF.xhtml#Fig1) shows a slow change in the magnitude of the components of a multivariate data over time, showing a gradual drift.

It’s important to detect these drift components early in the process so that we can update the machine learning model to maintain performance. Note that the baseline statistics of the data is not known ahead of time.

![](images/522202_1_En_BookFrontmatter_Fig1_HTML.png)

Figure 1

Data components show gradual drift over time

I used an adaptive algorithm (see Chapter [5](#522202_1_En_5_Chapter.xhtml)) to compute the [principal components](https://en.wikipedia.org/wiki/Principal_component_analysis) [principal component analysis, Wikipedia] of the data and derive a metric from them. Figure [2](#522202_1_En_BookFrontmatter_OnlinePDF.xhtml#Fig2) shows that the metric does not converge to its statistical value (ideally 1) and diverges towards 0\. This detects the feature drift quickly so that the edge device can update the machine learning model.

![](images/522202_1_En_BookFrontmatter_Fig2_HTML.png)

Figure 2

An adaptive principal component-based metric detects drift in data early in the real-time process

The downward slope of the detection metric in the graph indicates the gradual drift of the features.

## Adapting to Drift

In another type of drift, the data changes its statistical properties abruptly. Figure [3](#522202_1_En_BookFrontmatter_OnlinePDF.xhtml#Fig3) shows simulated multi-dimensional data that abruptly changes to a different underlying statistic after 500 samples.

![](images/522202_1_En_BookFrontmatter_Fig3_HTML.png)

Figure 3

Simulated data that abruptly changes statistical properties after 500 samples

The adaptive algorithms help us adapt to this abrupt change and recalculate the underlying statistics, in this case, the first two principal eigenvectors of the data (see Chapter [6](#522202_1_En_6_Chapter.xhtml)). The ideal values in Figure [4](#522202_1_En_BookFrontmatter_OnlinePDF.xhtml#Fig4) are 1\. As the data changes abruptly after 500 samples, the value falls and quickly recovers back to 1.

![](images/522202_1_En_BookFrontmatter_Fig4_HTML.png)

Figure 4

Principal eigenvectors adapt to abruptly changing data

## My Approach

### Adaptive Algorithms and Best Practices

In this book, I offer over 50 examples of adaptive algorithms to solve real-world problems. I also offer best practices to select the right algorithms for different use cases. I formulate these problems as matrix computations, where the underlying matrices are unknown. I assume that the entire data is not available at once. Instead, I have a sequence of random matrices or vectors from which I compute the matrix functions without knowing the matrices. The matrix functions are computed adaptively as each sample is presented to the algorithms.

1.  My algorithms process each incoming sample **x**[*k*] such that at any instant *k* all of the currently available data is taken into consideration.

2.  For each sample, the algorithm estimates the desired matrix functions, and its estimates have known statistical properties.

3.  The algorithms have the ability to adapt to variations in the underlying systems so that for each sample **x**[*k*] I obtain the current state of the process and not an average over all samples (i.e., I can handle both stationary and non-stationary data).

### Problems with Conventional Non-Adaptive Approaches

The conventional approach for evaluating matrix functions requires the computation of the matrix after collecting all samples and then the application of a numerical procedure. There are two problems with this approach.

1.  The dimension of the samples may be large so that even if all the samples are available, performing the matrix algebra may be difficult or may take a prohibitively large number of computational resources and memory.

2.  The matrix functions evaluated by conventional schemes cannot adapt to small changes in the data (e.g., a few incoming samples). If the matrix functions are estimated by conventional methods from *K* (finite) samples, then for each additional sample, all of the computation must be repeated.

These deficiencies make the conventional schemes inefficient for real-time applications.

### Computationally Simple

My approach is to use computationally simple adaptive algorithms. For example, given a sequence of random vectors {**x**[*k*]}, a well-known algorithm for the principal eigenvector evaluation uses the update rule **w**[*k*+1] = **w**[*k*]+*η* (**x**[*k*]**x**[*k*]^(*T*)–**w**[*k*]**w**[*k*]^(*T*)**x**[*k*]**x**[*k*]^(*T*)**w**[*k*]), where *η* is a small positive constant. In this algorithm, for each sample **x**[*k*] the update procedure requires simple matrix-vector multiplications, yet the vector **w**[*k*] converges quickly to the principal eigenvector of the data correlation matrix. Clearly, this can be easily implemented in CPUs on devices with low memory and power usage.

The objective of this book is to present a variety of [neuromorphic](https://en.wikipedia.org/wiki/Neuromorphic_engineering) [neuromorphic engineering, Wikipedia] adaptive algorithms [Wikipedia] for matrix algebra problems. Neuromorphic algorithms work by mimicking the physics of the human neural systems using networks, where activation by neurons propagate to other neurons in a cascading chain.

### Matrix Functions I Solve

The matrix algebra functions that I compute adaptively include

*   Normalized mean, median

*   LU decomposition (square root), inverse square root

*   Eigenvector/eigenvalue decomposition (EVD)

*   Generalized EVD

*   Singular value decomposition (SVD)

*   Generalized SVD

For each matrix function, I will discuss practical use cases in machine learning and data analytics and support them with experimental results.

### Common Methodology to Derive Adaptive Algorithms

Another contribution of this book is the presentation of a **common methodology** to derive each adaptive algorithm. For each matrix function and every adaptive algorithm, I present a scalar unconstrained objective function *J*(*W*;*A*) whose minimizer *W*^(***) is the desired matrix function of *A*. From this objective function *J*(*W*;*A*), I derive the adaptive algorithm such as *W*[*k* + 1] = *W*[*k*] − *η*∇[*W*]*J*(*W*[*k*]; *A*) by using standard techniques of optimization (for example, gradient descent). I then speed up these adaptive algorithms by using statistical methods. Note that this helps practitioners create new adaptive algorithms for their use cases.

In summary, the book starts with a common framework to derive adaptive algorithms and then uses the framework for each category of streaming data problems starting with the adaptive median to complex principal components and discriminant analysis. I present practical problems in each category and derive the algorithms to solve them. The final chapter solves critical edge computation problems for time-varying, non-stationary data with minimal compute, memory, latency, and bandwidth. I also provide the code [Chatterjee GitHub] for all algorithms discussed here.

## GitHub

All simulations and implementation code by chapters are published in the public GitHub:

[`https://github.com/cchatterj0/AdaptiveMLAlgorithms`](https://github.com/cchatterj0/AdaptiveMLAlgorithms)

The GitHub page contains the following items:

*   Python code for all chapters starting with Chapter [2](#522202_1_En_2_Chapter.xhtml)

*   MATLAB simulation code in a separate directory

*   Data for all implementation code

*   Proofs of convergence for some of the algorithms

Acknowledgments

I want to thank my professor and mentor Vwani Roychowdhury for guiding me through my Ph.D. thesis, where I first created much of the research presented in this book. Vwani taught me how to research, write, and present this material in the many papers we wrote together. He also inspired me to continue this research and eventually write this book. I could not have done it without his inspiration, help, and guidance. I sincerely thank Vwani for being my teacher and mentor.

About the Author About the Technical Reviewer

# 1. Introduction

In this chapter, I present the adaptive computation of important features for data representation and classification. I demonstrate the importance of these features in machine learning, data visualization, and data analytics. I also show the importance of these algorithms in multiple disciplines and present how these algorithms are obtained there. Finally, I present a common methodology to derive these algorithms. This methodology is of high practical value since practitioners can use this methodology to derive their own features and algorithms for their own use cases.

For these data features, I assume that the data arrives as a sequence, has to be used instantaneously, and the entire batch of data cannot be stored in memory.

In machine learning and data analysis problems such as regression, classification, enhancement, or visualization, effective representation of data is key. When this data is multi-dimensional and time varying, the computational challenges are more formidable. Here we not only need to compute the represented data in a timely manner, but also adapt to the changing input in a fast, efficient, and robust manner.

A well-known method of data compression/representation is the Karhunen-Loeve theorem [Karhunen–Loève theorem, Wikipedia] or eigenvector orthonormal expansion [Fukunaga 90]. This method is also known as principal component analysis (PCA) [principal component analysis, Wikipedia]. Since each eigenvector can be ranked by its corresponding eigenvalue, a subset of the “best” eigenvectors can be chosen as the most relevant features.

Figure [1-1](#522202_1_En_1_Chapter.xhtml#Fig1) shows two-class, two-dimensional data in which the best feature for data representation is the projection of the data on vector **e**[1], which captures the most significant properties of the data from the two classes.

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig1_HTML.png)

Figure 1-1

Data representation feature **e**[1] that best represents the data from the two classes [Source: Chatterjee et al. IEEE Transactions on Neural Networks, Vol. 8, No. 3, pp. 663-678, May 1997]

In classification, however, you generally want to extract features that are effective for preserving *class separability* . Simply stated, the goal of classification feature extraction is to find a transform that maps the raw measurements into a smaller set of features, which contain all the discriminatory information needed to solve the overall pattern recognition problem.

Figure [1-2](#522202_1_En_1_Chapter.xhtml#Fig2) shows the same two-class, two-dimensional data in which the best feature for classification is vector **e**[2], whereby projection of the data on **e**[2] leads to best class separability.

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig2_HTML.jpg)

Figure 1-2

Data classification feature **e**[2] that leads to best class separability [Source: Chatterjee et al. IEEE Transactions on Neural Networks, Vol. 8, No. 3, pp. 663-678, May 1997]

Before I present the adaptive algorithms for streaming data in the following chapters, I need to discuss the following:

1.  Commonly used features that are obtained by a linear transform of the data and are used with streaming data for edge applications

2.  Historical relevance of these features and how we derive them from different disciplines

3.  Why we want to use adaptive algorithms to compute these features

4.  How to create a common mathematical framework to derive adaptive algorithms for these features and many more

## 1.1 Commonly Used Features Obtained by Linear Transform

In this section, I discuss four commonly used features for data analytics and machine learning. These features are effective in data classification and representation, and can be easily obtained by a simple linear transform of the data. The simplicity and effectiveness of these features makes them useful for streaming data and edge applications.

In mathematical terms, let {**x**[*k*]} be an *n*-dimensional (zero mean) sequence that represents the data. We are seeking a matrix sequence {*W*[*k*]} and a transform:

![$$ {\mathbf{y}}_k={W}_k^T{\mathbf{x}}_k $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq1.png),     (1.1)

such that the linear transform **y**[*k*] has properties of data representation and is our desirable feature. I discuss a few of these features later.

**Definition:** Define the data correlation matrix *A* of {**x**[*k*]} as

![$$ A=\underset{k\to \infty }{\lim }E\left[{\mathbf{x}}_k{\mathbf{x}}_k^T\right] $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq2.png).     (1.2)

### Data Whitening

Data whitening is a process of decorrelating the data such that all components have unit variance. It is a data preprocessing step in machine learning and data analysis to “normalize” the data so that it is easier to model. Here the linear transform **y**[*k*] of the data has the property ![$$ E\left[{\mathbf{y}}_k{\mathbf{y}}_k^T\right] $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq3.png)=*I*[*n*] (identity). I discuss in Chapter [3](#522202_1_En_3_Chapter.xhtml) that the optimal value of *W*[*k*]=*A*^(–½).

Figure [1-3](#522202_1_En_1_Chapter.xhtml#Fig3) shows the correlation matrices of the original and whitened data. The original random normal data is highly correlated as shown by the colors on all axes. The whitened data is fully uncorrelated with no correlation between components since only diagonal values exist.

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig3_HTML.jpg)

Figure 1-3

Original correlated data is uncorrelated by the data whitening algorithm

The Python code to generate the whitened data from original dataset `X[nDim, nSamples]` is

```
from scipy.linalg import eigh
# Compute Correlation matrix and eigen vectors of the original data
corX = (X @ X.T) / nSamples
# generate the whitened data
eigvals, eigvecs = eigh(corX)
V = np.fliplr(eigvecs)
D  = np.diag(np.sqrt(1/eigvals[::-1]))
Y = V @ D @ V.T @ X
corY = (Y @ Y.T)/nSamples
# Plot the original and whitened correlation matrices
import seaborn as sns
plt.figure(figsize=(10, 4))
plt.rcParams.update({'font.size': 16})
plt.subplot(1, 2, 1)
sns.heatmap(corX, linewidth=0.5, linecolor="green", cmap='RdBu', cbar=False)
plt.title("Original data")
plt.subplot(1, 2, 2)
sns.heatmap(corY, linewidth=0.5, linecolor="green", cmap='hot', cbar=False)
plt.title("Whitened data")
plt.show()
```

### Principal Components

[Principal component analysis](https://en.wikipedia.org/wiki/Principal_component_analysis) (PCA) is a well-studied example of the data representation model. From the perspective of *classical statistics*, PCA is an analysis of the covariance structure of multivariate data {**x**[*k*]}. Let **y**[*k*]=[*y*[*k*1],…,*y*[*kp*]]^(*T*) be the components of the PCA-transformed data. In this representation, the first principal component *y*[*k1*] is a one-dimensional linear subspace where the variance of the projected data is maximal. The second principal component *y*[*k*2] is the direction of maximal variance in the space orthogonal to the *y*[*k*1] and so on.

It has been shown that the optimal weight matrix *W*[*k*] is the eigenvector matrix of the correlation of the zero-mean input process {**x**[*k*]}. Let *A*Φ=ΦΛ be the *eigenvector decomposition (EVD)* of *A*, where Φ and Λ are respectively the eigenvector and eigenvalue matrices. Here Λ=*diag*(*λ*[1],…,*λ*[*n*]) is the diagonal eigenvalue matrix with *λ*[1]≥…≥*λ*[*n*]>0 and Φ is orthonormal. We denote Φ[*p*]∈ℜ^(*n*X*p*) as the matrix whose columns are the first *p* principal eigenvectors. Then optimal *W*[*k*]=Φ[*p*].

There are three variations of PCA that are useful in applications.

1.  When *p*=*n*, the *n* components of **y**[*k*] are ordered according to maximal to minimal variance. This is the component analyzer that is used for *data analysis*.

2.  When *p*<*n*, the *p* components of **y**[*k*] have maximal information for *data compression*.

3.  For *p*<*n*, the *n*–*p* components of **y**[*k*] with minimal variance can be regarded as abnormal signals and reconstructed as (*I*[*n*]–Φ[*p*]Φ[*p*]^(*T*))**x**[*k*] to obtain a *novelty filter*.

Figure [1-4](#522202_1_En_1_Chapter.xhtml#Fig4) shows the correlation matrices of the original and PCA-transformed random normal data. The original data is highly correlated as shown by the colors on all axes. The PCA-transformed data is uncorrelated with diagonal blocks only and highest representation value for component 1 (top left corner block) and decreasing thereafter.

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig4_HTML.jpg)

Figure 1-4

Original correlated data is uncorrelated by PCA projection

The Python code for the PCA projected data from original dataset `X[nDim, nSamples]` is

```
from scipy.linalg import eigh
# Compute data correlation matrix
corX = (X @ X.T) / nSamples
# generate the PCA transformed data
eigvals, eigvecs = eigh(corX)
EstV = np.fliplr(eigvecs)
Y = EstV.T @ X
corY = (Y @ Y.T)/nSamples
# plot the PCA transformed data
import seaborn as sns
plt.figure(figsize=(10, 5))
plt.rcParams.update({'font.size': 16})
plt.subplot(1, 2, 1)
sns.heatmap(corX, linewidth=0.5, linecolor="green", cmap='RdBu', cbar=False)
plt.title("Original data")
plt.subplot(1, 2, 2)
sns.heatmap(corY, linewidth=0.5, linecolor="green", cmap='hot', cbar=False)
plt.title("PCA Transformed")
plt.show()
```

### Linear Discriminant Features

Linear discriminant analysis (LDA) [linear discriminant analysis, Wikipedia] creates features from data from multiple classes such that the transformed representation **y**[*k*] has the most class separability. Given a data **x**[*k*] and the corresponding classes **c**[*k*], we can calculate the following matrices:

*   data correlation matrix *B*=*E*(**x**[*k*]**x**[*k*]^(*T*)),

*   cross correlation matrix *M*=*E*(**x**[*k*]**c**[*k*]^(*T*)),

*   and *A*=*MM*^(*T*).

It is well known that the linear transform *W*[*k*] (Eq [1.1](#522202_1_En_1_Chapter.xhtml#Par16)) is the *generalized eigen-decomposition* (GEVD) [generalized eigenvector, Wikipedia] of *A* with respect to *B*. Here *A*Ψ=*B*ΨΔ where Ψ and Δ are respectively the generalized eigenvector and eigenvalue matrices. Furthermore, Ψ[*p*]∈ℜ^(*n*X*p*) is the matrix whose columns are the first *p≤n* principal generalized eigenvectors.

Figure [1-5](#522202_1_En_1_Chapter.xhtml#Fig5) shows a two-class classification problem where the real-world data correlation matrix on the left has values on all axes and it is hard to distinguish the two classes. On the right is the LDA-transformed data, which clearly shows the two classes and is easy to classify.

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig5_HTML.jpg)

Figure 1-5

Original correlated data is uncorrelated by linear discriminant analysis

The Python code to adaptively generate the LDA transformed correlation matrix from a two-class, multi-dimensional dataset `[nDim, nSamples]` is

```
# Adaptively compute matrices A and B
from numpy import linalg as la
dataset2 = dataset.drop(['Class'],1)
nSamples = dataset2.shape[0]
nDim = dataset2.shape[1]
classes = np.array(dataset['Class']-1)
classes_categorical = tf.keras.utils.to_categorical(classes, num_classes=2)
M = np.zeros(shape=(nDim,2)) # stores adaptive correlation matrix
B = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
for iter in range(nSamples):
cnt = iter + 1
# generate matrices A and B from current sample x
x = np.array(dataset2.iloc[iter])
x = x.reshape(nDim,1)
B = B + (1.0/cnt)*((np.dot(x, x.T)) - B)
y = classes_categorical[iter].reshape(2,1)
M = M + (1.0/cnt)*((np.dot(x, y.T)) - M)
A = M @ M.T
# generate the LDA transformed data
from scipy.linalg import eigh
from sklearn.preprocessing import normalize
eigvals, eigvecs = eigh(A, B)
V = np.fliplr(eigvecs)
VTAV = np.around(V.T @ A @ V, 2)
VTBV = np.around(V.T @ B @ V, 2)
# plot the LDA transformed data
import seaborn as sns
plt.figure(figsize=(8, 8))
plt.rcParams.update({'font.size': 16})
plt.subplot(2, 2, 1)
sns.heatmap(A, linewidth=0.5, linecolor="green", cmap='RdBu', cbar=False)
plt.title("Original data")
plt.subplot(2, 2, 2)
sns.heatmap(VTBV, linewidth=0.5, linecolor="green", cmap='hot', cbar=False)
plt.title("LDA Transformed")
plt.subplot(2, 2, 3)
sns.heatmap(A, linewidth=0.5, linecolor="green", cmap='RdBu', cbar=False)
plt.subplot(2, 2, 4)
sns.heatmap(VTAV, linewidth=0.5, linecolor="green", cmap='hot', cbar=False)
plt.show()
```

### Singular Value Features

Singular value decomposition (SVD) [singular value decomposition, Wikipedia] is a special case of a EVD problem as follows. Given the cross-correlation (n-by-m real) matrix *M*=*E*(**x**[*k*]**c**[*k*]^(*T*)) ∈ℜ^(*nXm*), SVD computes two matrices *U*[*k*]∈ℜ^(*nXn*) and *V*[*k*]∈ℜ^(*mXm*) such that *U*[*k*]^(*T*)*MV*[*k*]=*S*[*nxm*], where *U*[*k*] and *V*[*k*] are orthonormal and *S*=diag(*s*[1],...*s*[*r*]), *r*=min(*m*, *n*), with *s*[1]>=...>=*s*[*r*]>=0. By rearranging the vectors **x**[*k*] and **c**[*k*] we can make a *n*x*m* dimensional SVD problem into a (*n*+*m*)x(*n*+*m*) dimensional EVD problem.

### Summary

Table [1-1](#522202_1_En_1_Chapter.xhtml#Tab1) summarizes the discussion in this section. Note that given a sequence of vectors {**x**[*k*]}, we are seeking a matrix sequence {*W*[*k*]} and a linear transform ![$$ {\mathbf{y}}_k={W}_k^T{\mathbf{x}}_k $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq4.png).

Table 1-1.

Statistical Property of ***y***[***k***] and Matrix Property of ***W***[***k***]

| Computation | Statistical Property of y[*k*] | Matrix Property of *W*[*k*] |
| --- | --- | --- |
| Whitening | ![$$ E\left[{\mathbf{y}}_k{\mathbf{y}}_k^T\right]={I}_n $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq5.png) | *W*[*k*] = *A*^(–½) = ΦΛ^(–½Φ*T*) |
| PCA/EVD | Max ![$$ E\left[{\mathbf{y}}_k^T{\mathbf{y}}_k\right] $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq6.png) subj. to ![$$ {W}_k^T{W}_k={I}_p $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq7.png) | *W*[*k*] = Φ[*p*] and ![$$ E\left[{\mathbf{y}}_k{\mathbf{y}}_k^T\right]={I}_p $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq8.png) |
| LDA/GEVD | Max ![$$ E\left[{\mathbf{y}}_k^T{\mathbf{y}}_k\right] $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq9.png) subj. to ![$$ {W}_k^T{BW}_k={I}_p $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq10.png) | *W*[*k*] = Ψ[*p*] and ![$$ E\left[{\mathbf{y}}_k{\mathbf{y}}_k^T\right]={I}_p $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq11.png) |

## 1.2 Multi-Disciplinary Origin of Linear Features

In this section, I further discuss the importance of data representation and classification features by showing how multiple disciplines derive these features and compute them adaptively for streaming data. For each discipline, I demonstrate the use of these features on real data.

### Hebbian Learning or Neural Biology

Hebb’s postulate of learning is the oldest and most famous of all learning rules. Hebb proposed that when an axon of cell *A* excites cell *B*, the synaptic weight *W* is adjusted based on *f*(**x**,**y**), where *f*(⋅) is a function of presynaptic activity **x** and postsynaptic activity **y**. As a special case, we may write the weight adjustment Δ*W*=η**xy**^(*T*), where η>0 is a small constant.

Given an input sequence {**x**[*k*]}, we can construct its linear transform ![$$ {y}_k={\mathbf{w}}_k^T{\mathbf{x}}_k $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq12.png), where **w**[*k*∈]ℜ^(*n*) is the weight vector. We can describe a Hebbian rule [Haykin 94] for adjusting **w**[*k*] as **w**[*k*+1]=**w**[*k*]+η***x***[*k*]*y*[*k*]. Since this rule leads to exponential growth, Oja [Oja 89] introduced a rule to limit the growth of **w**[*k*] by normalizing **w**[*k*] as follows:

![$$ {\mathbf{w}}_{k+1}=\frac{{\mathbf{w}}_k+\eta {\mathbf{x}}_k{y}_k}{\left\Vert {\mathbf{w}}_k+\eta {\mathbf{x}}_k{y}_k\right\Vert }, $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq13.png)     (1.3)

Assuming small η and ‖**w**[*k*]‖=1, ([1.3](#522202_1_En_1_Chapter.xhtml#Par42)) can be expanded as a power series in η, yielding

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k+\eta \left({\mathbf{x}}_k{y}_k-{y}_k^2{\mathbf{w}}_k\right)+O\left({\eta}^2\right), $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq14.png)     (1.4)

Eliminating the *O*(η²) term, we get the *constrained Hebbian algorithm* (*CHA*) or *Oja’s one-unit rule* (Chapter [4](#522202_1_En_4_Chapter.xhtml), Sec [4.​3](#522202_1_En_4_Chapter.xhtml#Sec6)). Indeed, this algorithm converges to the first principal eigenvector of *A*.

Note that adaptive PCA algorithms are commonly used on streaming data and some GitHubs exist. I create here a comprehensive repository of these algorithms and also present new ones in this book and the associated GitHub.

For example, algorithm ([1.4](#522202_1_En_1_Chapter.xhtml#Par44)) has been widely used on streaming data to derive the strongest representation feature from the data for analytics and machine learning. Figure [1-6](#522202_1_En_1_Chapter.xhtml#Fig6) shows multivariate non-stationary data with seasonality. Using the adaptive update rule ([1.4](#522202_1_En_1_Chapter.xhtml#Par44)), we derive the first principal component effectively in 0.2% of the streaming data. Figure [1-6](#522202_1_En_1_Chapter.xhtml#Fig6) shows the seasonal time-varying streaming data on the left and the rapid convergence (ideal value is 1) of the algorithm on the right to the first principal eigenvector leading to the strongest data representation feature.

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig6_HTML.png)

Figure 1-6

Adaptive PCA algorithm used on seasonal streaming data

The Python code to implement the Hebbian adaptive algorithm on real 33-dimensional dataset `[nDim][nSamples]` is

```
# Adaptive Hebbian/OJA algorithms
from numpy import linalg as la
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
w = 0.1 * np.ones(shape=(nDim)) # weight vectors of all algorithms
for iter in range(nSamples):
# update the data correlation matrix with latest data vector x
x = np.array(dataset1.iloc[iter]).reshape(nDim,1)
A = A + (1.0/(1 + iter))*((np.dot(x, x.T)) - A)
# Hebbian/OJA Algorithm
v = w[:].reshape(nDim, 1)
v = v + (1/(100+iter))*(A @ v - v @ (v.T @ A @ v))
w[:] = v.reshape(nDim)
```

### Auto-Associative Networks

Auto-association is a neural network structure in which the desired output is same as the network input **x**[*k*]. This is also known as the linear autoencoder [autoencoder, Wikipedia]. Let’s consider a two-layer linear network with weight matrices *W*[1] and *W*[2] for the input and output layers, respectively, and *p* (≤*n*) nodes in the hidden layer. The mean square error (MSE) at the network output is given by

![$$ e=E\left[{\left\Vert {\mathbf{x}}_k-{W}_2^T{W}_1^T{\mathbf{x}}_k\right\Vert}^2\right] $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq15.png).     (1.5)

Due to *p* nodes in the hidden layer, a minimum MSE produces outputs that represent the best estimates of **x**[*k*∈]ℜ^(*n*) in the ℜ[*p*] subspace. Since projection onto Φ[*p*] minimizes the error in the ℜ[*p*] subspace, we expect the first layer weight matrix *W*[1] to be rotations of Φ[*p*]. The second layer weight matrix *W*[2] is the inverse of *W*[1] to finally represent the “best” identity transform. This intuitive argument has been proven [Baldi and Hornik 89,95, Bourland and Kamp 88], where the optimum weight matrices are

*W*[1] = Φ[*p*]*R* and ![$$ {W}_2={R}^{-1}{\Phi}_p^T $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq16.png),     (1.6)

where *R* is a non-singular *n*X*n* matrix. Note that if we further impose the constraint ![$$ {W}_2={W}_1^T $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq17.png), then *R* is a unitary matrix and the input layer weight matrix *W*[1] is orthonormal and spans the space defined by Φ[*p*], the *p* principal eigenvectors of the input correlation matrix *A*. See Figure [1-7](#522202_1_En_1_Chapter.xhtml#Fig7).

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig7_HTML.jpg)

Figure 1-7

Auto-associative neural network

Note that if we have a single node in the hidden layer (i.e., *p*=1), then we obtain *e* as the output sum squared error for a two-layer linear auto-associative network with input layer weight vector **w** and output layer weight vector **w**^(*T*). The optimal value of **w** is the first principal eigenvector of the input correlation matrix *A*[*k*].

The result in ([1.6](#522202_1_En_1_Chapter.xhtml#Par52)) suggests the possibility of a PCA algorithm by using a gradient correction only to the input layer weights, while the output layer weights are modified in a symmetric fashion, thus avoiding the backpropagation of errors in one of the layers. One possible version of this idea is

![$$ {W}_1\left(k+1\right)={W}_1(k)-\eta \frac{\mathit{\partial e}}{\mathit{\partial}{W}_1} $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq18.png) and *W*[2](*k* + 1) = *W*[1](*k* + 1)^(*T*).     (1.7)

Denoting *W*[1](*k*) by *W*[*k*], we obtain an algorithm that is the same as Oja’s *subspace learning algorithm* (SLA).

Algorithm ([1.7](#522202_1_En_1_Chapter.xhtml#Par56)) has been used widely on multivariate streaming data to extract the significant principal components so that we can instantaneously find the important data representation features. Figure [1-8](#522202_1_En_1_Chapter.xhtml#Fig8) shows multidimensional streaming data on the left and the rapid convergence of the first two principal eigenvectors on the right by the adaptive algorithm ([1.7](#522202_1_En_1_Chapter.xhtml#Par56)), which is further described in Chapter [5](#522202_1_En_5_Chapter.xhtml).

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig8_HTML.png)

Figure 1-8

Convergence of the first two principal eigenvectors computed from multidimensional streaming data. Data is on the left and feature convergence (ideal value = 1) is on the right

The Python code to generate the first 4 principal eigenvectors from 10-dimensional synthetic dataset is

```
from numpy import linalg as la
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W2 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W3 = W2
c = [2-0.3*k for k in range(nEA)]
C = np.diag(c)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrix A with current data sample x
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - W2 @ np.triu(W2.T @ A @ W2))
# Weighted Gradient Descent
W3 = W3 + (1/(220 + cnt))*(A @ W3 @ C - W3 @ C @ (W3.T @ A @ W3))
```

### Hetero-Associative Networks

Let’s consider a hetero-associative network , which differs from the auto-associative case in the output layer, which is **d** instead of **x**. Here **d** denotes the categorical classes the data belongs to. One example of **d**=**e**[*i*] where **e**[*i*] is the *i*^(*th*) standard basis vector [standard basis, Wikipedia] for class *i*. In a two-class problem, **d**=[1 0]^(*T*) for class 1 and **d**=[0 1]^(*T*) for class 2\. Let’s denote *B*=*E*(**xx**^(*T*)), *M*=*E*(**xd**^(*T*)), and *A*=*MM*^(*T*). See Figure [1-9](#522202_1_En_1_Chapter.xhtml#Fig9).

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig9_HTML.jpg)

Figure 1-9

Hetero-associative neural network

We consider a two-layer linear hetero-associative neural network with just a single neuron in the hidden layer and *m≤n* output units. Let **w∈**ℜ^(*n*) be the weight vector for the input layer and **v∈**ℜ^(*m*) be the weight vector for the output layer. The MSE at the network output is

*e* = *E*{‖**d** − **vw**^(*T*)**x**‖²}.     (1.8)

We further assume that the network has limited power, so **w**^(*T*)*B***w**=1. Hence, we impose this constraint on the MSE criterion in ([1.8](#522202_1_En_1_Chapter.xhtml#Par62)) as

*J*(**w**, **v**) = *E*{‖**d** − **vw**^(*T*)**x**‖²} + *μ*(**w**^(*T*)*B***w** − 1),     (1.9)

where μ is the Lagrange multiplier. This equation has a unique global minimum where **w** is the first principal eigenvector of the matrix pencil (*A*,*B*) and **v**=*M*^(*T*)**w**. Furthermore, from the gradient of ([1.9](#522202_1_En_1_Chapter.xhtml#Par64)) with respect **w**, we obtain the update equation for **w** as

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k-\eta \frac{\mathit{\partial J}}{\mathit{\partial}\mathbf{w}}\left({\mathbf{w}}_k,{\mathbf{v}}_k\right)={\mathbf{w}}_k+\eta \left(I-{B}_k{\mathbf{w}}_k{\mathbf{w}}_k^T\right)\kern0.2em {M}_k{\mathbf{v}}_k $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq19.png).     (1.10)

We can substitute the convergence value of **v** ([1.10](#522202_1_En_1_Chapter.xhtml#Par66)) and avoid the back-propagation of errors in the second layer to obtain

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k+\eta \left({A}_k{\mathbf{w}}_k-{B}_k{\mathbf{w}}_k\left({\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k\right)\right). $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq20.png)     (1.11)

This algorithm can be used effectively to adaptively compute classification features from streaming data.

Figure [1-10](#522202_1_En_1_Chapter.xhtml#Fig10) shows the multivariate e-shopping clickstream dataset [Apczynski M., et al*.*] belonging to two classes determining buyer’s pricing sentiments. We use the adaptive algorithm ([1.11](#522202_1_En_1_Chapter.xhtml#Par68)) to compute the class separability feature **w**. Figure [1-10](#522202_1_En_1_Chapter.xhtml#Fig10) shows the following:

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig10_HTML.png)

Figure 1-10

e-Shopping clickstream data on the left and uncorrelated class separable data on the right

*   The original multi-dimensional e-shopping data on the left 2 figures.

*   The original data correlation on the right (3rd figure). The original data correlation matrix shows that the classes are indistinguishable in the original data.

*   The correlation matrix of the data transformed by algorithm ([1.11](#522202_1_En_1_Chapter.xhtml#Par68)) on the far right. The white and red blocks on the far right matrix show the two classes are clearly separated in the transformed data by the algorithm ([1.11](#522202_1_En_1_Chapter.xhtml#Par68)).

The Python code to generate the class separable transformed correlation matrix from two-class multi-dimensional dataset[nDim, nSamples] is

```
# Adaptively compute matrices A and B
from numpy import linalg as la
nSamples = dataset1.shape[0]
nDim = dataset1.shape[1]
classes = np.array(dataset['price2']-1)
classes_categorical = tf.keras.utils.to_categorical(classes, num_classes=2)
M = np.zeros(shape=(nDim,2)) # stores adaptive correlation matrix
B = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
for iter in range(nSamples):
cnt = iter + 1
x = np.array(dataset1.iloc[iter])
x = x.reshape(nDim,1)
B = B + (1.0/cnt)*((np.dot(x, x.T)) - B)
y = classes_categorical[iter].reshape(2,1)
M = M + (1.0/cnt)*((np.dot(x, y.T)) - M)
A = M @ M.T
# generate the transformed data
from scipy.linalg import eigh
eigvals, eigvecs = eigh(A, B)
V = np.fliplr(eigvecs)
VTAV = np.around(V.T @ A @ V, 2)
VTBV = np.around(V.T @ B @ V, 2)
# plot the LDA transformed data
import seaborn as sns
plt.figure(figsize=(12, 12))
plt.rcParams.update({'font.size': 16})
plt.subplot(2, 2, 1)
sns.heatmap(A, linewidth=0.5, linecolor="green", cmap='RdBu', cbar=False)
plt.title("Original Correlated Data")
plt.subplot(2, 2, 2)
sns.heatmap(VTBV, linewidth=0.5, linecolor="green", cmap='hot', cbar=False)
plt.title("Transformed Class Separable Data")
plt.subplot(2, 2, 3)
sns.heatmap(A, linewidth=0.5, linecolor="green", cmap='RdBu', cbar=False)
plt.title("Original Correlated Data")
plt.subplot(2, 2, 4)
sns.heatmap(VTAV, linewidth=0.5, linecolor="green", cmap='hot', cbar=False)
plt.title("Transformed Class Separable Data")
plt.show()
```

### Statistical Pattern Recognition

One special case of linear hetero-association is a network performing one-from-*m* classification, where input **x** to the network is classified into one out of *m* classes ω[1],...,ω[*m*]. If **x∈**ω[*i*] then **d**=**e**[*i*] where **e**[*i*] is the *i*^(*th*) standard basis vector. Unlike auto-associative learning, which is unsupervised, this network is supervised. In this case, *A* and *B* are scatter matrices. Here *A* is the *between-class scatter matrix S*[*b*], the scatter of the class means around the mixture mean, and *B* is the *mixture scatter matrix S*[*m*], the covariance of all samples regardless of class assignments. The generalized eigenvector decomposition of (*A*,*B*) is known as *linear discriminant analysis* (LDA), which was discussed in Section [1.1](#522202_1_En_1_Chapter.xhtml#Sec1).

### Information Theory

Another viewpoint of the data model ([1.1](#522202_1_En_1_Chapter.xhtml#Sec1)) is due to Linsker [1988] and Plumbley [1993]. According to Linsker’s *Infomax* principle, the optimum value of the weight matrix *W* is when the information *I*(**x**,**y**) transmitted to its output **y** about its input **x** is *maximized*. This is equivalent to information in input **x** about output **y** since *I*(**x**,**y**)=*I*(**y**,**x**). However, a noiseless process like ([1.1](#522202_1_En_1_Chapter.xhtml#Sec1)) has infinite information about input **x** in **y** and vice versa since **y** perfectly represents **x**. In order to proceed, we assume that input **x** contains some noise **n**, which prevents **x** from being measured accurately by **y**. There are two variations of this model, both inspired by Plumbley [1993].

In the first model, we assume that the output **y** is corrupted by noise due to the transform *W*. We further assume that average power available for transmission is limited, the input **x** is zero-mean Gaussian with covariance *A*, the noise **n** is zero-mean uncorrelated Gaussian with covariance *N*, and the transform noise **n** is independent of **x**. The noisy data model is

**y** = *W*^(*T*)**x** + **n**.     (1.12)

The mutual information *I*(**y**,**x**) (with Gaussian assumptions) is

*I*(**y**, **x**) = 0.5 log  det(*W*^(*T*)*AW* + *N*) − 0.5 log  det(*N*).     (1.13)

We define an objective function with power constraints as

*J*(*W*) = *I*(**y**, **x**) − 0.5*tr*(Λ(*W*^(*T*)*AW* + *N*)),     (1.14)

where Λ is a diagonal matrix of Lagrange multipliers. This function is maximized when *W*=Φ*R*, where Φ is the principal eigenvector matrix of *A* and *R* is a non-singular rotation matrix.

### Optimization Theory

In optimization theory , various matrix functions are computed by evaluating the maximums and minimums of objective functions. Given a symmetric positive definite matrix pencil (*A*,*B*), the first principal generalized eigenvector is obtained by maximizing the well-known Raleigh quotient:

![$$ J\left(\mathbf{w}\right)=\frac{{\mathbf{w}}^TA\mathbf{w}}{{\mathbf{w}}^TB\mathbf{w}} $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq21.png).     (1.15)

There are three common modifications of this objective function based on the method of optimization [Luenberger]. They are

*   Lagrange multiplier:

*J*(**w**) =  − **w**^(*T*)*A***w** + *α*(**w**^(*T*)*B***w** − 1),     (1.16)

where *α* is a Lagrange multiplier.

*   Penalty function:

*J*(**w**) =  − **w**^(*T*)*A***w** + *μ*(**w**^(*T*)*B***w** − 1)²,     (1.17)

where *μ* is a non-negative scalar constant.

*   Augmented Lagrangian:

*J*(**w**) =  − **w**^(*T*)*A***w** + *α*(**w**^(*T*)*B***w** − 1) + *μ*(**w**^(*T*)*B***w** − 1)²,     (1.18)

where *α* is a Lagrange multiplier and *μ* > 0 is the penalty constant. Several algorithms based on this objective function are given in Chapters [4](#522202_1_En_4_Chapter.xhtml)-[7](#522202_1_En_7_Chapter.xhtml).

We obtain adaptive algorithms from these objective functions by using instantaneous values of the gradients and a gradient ascent technique, as discussed in the following chapters. One advantage of these objective functions is that we can use accelerated convergence methods such as steepest descent, conjugate direction, and Newton-Raphson. Chapter [6](#522202_1_En_6_Chapter.xhtml) discusses several such methods. The recursive least squares method has also been applied to adaptive matrix computations by taking various approximations of the inverse Hessian of the objective functions.

## 1.3 Why Adaptive Algorithms?

We observed that data representation and classification problems lead to matrix algebra problems, which have two solutions depending on the nature of the inputs.

1.  The first set of solutions requires the relevant matrices to be known in advance.

2.  The second set of solutions requires a sequence of samples from which the matrix can be computed.

In this section, I explain the difference between batch and adaptive processing, benefits of adaptive processing on streaming data, and the requirements for adaptive algorithms.

### Iterative or Batch Processing of Static Data

When the data is available in advance and the underlying matrices are known, we can use the batch processing approach. These algorithms have been used to solve a large variety of matrix algebra problems such as matrix inversion, EVD, SVD, and PCA [Cichocki et al*.* 92, 93]. These algorithms are solved in two steps: (1) using the pooled data to estimate the required matrices, and (2) using a numerical matrix algebra procedure to solve the necessary matrix functions.

For example, consider a simple matrix inversion problem for the correlation matrix *A* of a data sequence {**x**[*k*]}. We can calculate the matrix inverse *A*^(-1) after all of the data have been collected and *A* has been calculated. This approach works in a batch fashion. When a new sample **x** is added, it is not difficult to get the inverse of the new matrix *A*[*new*]=(*nA*+**xx**^(*T*))/(*n*+1), where *n* is the total number of samples used to compute *A*. Although the computation for *A* is simple, all the computations for solving ![$$ {A}_{new}^{-1} $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq22.png) need to be repeated.

There are two problems with this batch processing approach.

1.  First, the dimension of the samples may be large so that even if all the samples are available, performing the matrix algebra may be difficult or may take a prohibitively large amount of computational time. For example, eigenvector evaluation requires *O*(*n*³) computation, which is infeasible for samples of a large dimension (say 1,000) which occurs commonly in image processing and automatic control applications.

2.  Second, the matrix functions evaluated by conventional schemes cannot adapt to small changes in the data (e.g., a few incoming samples). If the matrix functions are estimated by conventional methods from *K* samples, then for each additional sample all of the computation has to be repeated.

These deficiencies make the batch schemes inefficient for real-time applications where the data arrives incrementally or in an *online* fashion.

### My Approach: Adaptive Processing of Streaming Data

When we have a continuous sequence of data samples, batch algorithms are no longer useful. Instead, adaptive algorithms are used to solve matrix algebra problems. An immediate advantage of these algorithms is that they can be used on real-time problems such as edge computation, adaptive data compression [Le Gall 91], antenna array processing for noise analysis and source location [Owsley 78], and adaptive spectral analysis for frequency estimation [Pisarenko 73].

My approach is to offer computationally simple adaptive algorithms for matrix computations from streaming data. Note that adaptive algorithms are critical in environments where the data volume is large, the data has high dimensions, the data is time varying and has changing underlying statistics, and we do not have sufficient storage, compute, and bandwidth to process the data with low latency. One such environment is edge devices and computation.

For example, given streaming samples {**x**[*k*]} of customer e-shopping data, we need to calculate a key customer sentiment **w**[*k*] from the data stream. For that, we need to design a **simple update rule** to change the customer sentiment **w** as new data **x** is available. The simple update rule will change the sentiment **w**[*k*] to its latest value **w**[*k*+1] as new data **x**[*k*] is available. It is of the format **w**[*k*+1]= **w**[*k*] + *f*(**w**[*k*],**x**[*k*]), where

*   **w**[*k*] is last value of the sentiment,

*   **w**[*k*+1] is the latest updated value of the sentiment,

*   **x**[*k*] is the newest data used to calculate **w**[*k*+1], and

*   *f*(.) is a simple function of **w**[*k*] and **x**[*k*].

An example of this update rule is the well-known algorithm [Oja 82] to compute the first principal eigenvector of a streaming sequence {**x**[*k*]}:

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k+\eta \left({\mathbf{x}}_k{\mathbf{x}}_k^T-{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{x}}_k{\mathbf{x}}_k^T\right){\mathbf{w}}_k $$](images/522202_1_En_1_Chapter/522202_1_En_1_Chapter_TeX_IEq23.png),     (1.19)

where η>0 is a small gain constant. In this algorithm, for each sample **x**[*k*] the update procedure requires simple matrix-vector multiplications, and the vector **w**[*k*] converges to the principal eigenvector of the data correlation matrix *A*. Clearly, this can be easily implemented in small CPUs.

Figure [1-11](#522202_1_En_1_Chapter.xhtml#Fig11) shows a multivariate e-shopping clickstream dataset [Apczynski M. et al*.*]. The adaptive update rule ([1.19](#522202_1_En_1_Chapter.xhtml#Par115)) is used to compute buyer pricing sentiments. The data is shown on the left and sentiments computed adaptively are shown on the right (ideal value is 1). The sentiments are updated adaptively as new data arrives and the sentiment value converges quickly to its ideal value of 1\.

![](images/522202_1_En_1_Chapter/522202_1_En_1_Fig11_HTML.png)

Figure 1-11

e-Shopping clickstream data on the left and buyer sentiments computed by the update rule ([1.19](#522202_1_En_1_Chapter.xhtml#Par115)) on the right (ideal value = 1)

There are several advantages and disadvantages of adaptive algorithms over conventional iterative batch solutions.

The advantages are

*   While conventional solutions find all eigenvectors and eigenvalues, adaptive solutions compute the desired eigenvectors only. In many applications, we do not need eigenvalues. Hence, they should be more efficient.

*   Furthermore, computational complexity of adaptive algorithms depends on the efficiency of the matrix-vector product *A***x**. These methods can be even more efficient when the matrix *A* has a certain structure such as sparse, Hankel, or Toeplitz for which FFT can be used to speed up the computation.

*   Adaptive algorithms easily fit the framework of time-varying multidimensional processes such as adaptive signal processing, where the input process is continuously updated.

The disadvantages are

*   Adaptive algorithms produce an approximate value of the features for the current dataset whereas batch algorithms provide an exact value.

*   The adaptive approach requires a “ramp up” time to reach high accuracy as evidenced by the curves in Figure [1-10](#522202_1_En_1_Chapter.xhtml#Fig10).

### Requirements of Adaptive Algorithms

It is clear from the previous discussion that adaptive algorithms for matrix computation need to be inexpensive for the computational process to keep pace with the input data stream. We also require that the estimates converge strongly to their actual values. We therefore expect our adaptive algorithms to satisfy the following constraints:

*   The algorithms should adapt to small changes in data, which is useful for real-time applications.

*   The estimates obtained from the algorithms should have known statistical properties.

*   The network architectures associated with the adaptive algorithms consist of linear units in one- or two-layer networks, such that the networks can be easily implemented with simple CPUs and no special hardware.

*   The computation involved in the algorithms is inexpensive such that the statistical procedure can process every data sample as they arrive.

*   The estimates obtained from the algorithms converge strongly to their actual values.

With these requirements, we proceed to design adaptive algorithms that solve the matrix algebra problems considered in this book. The objective of this book is to develop a variety of neuromorphic adaptive algorithms to solve matrix algebra problems.

Although I will discuss many adaptive algorithms to solve the matrix functions, most of the algorithms are of the following general form:

*W*[*k* + 1] = *W*[*k*] + *ηH*(**x**[*k*], *W*[*k*]),     (1.20)

where *H*(**x**[*k*],*W*[*k*]) follows certain continuity and regularity properties [Ljung 77,78,84,92], and η > 0 is a small gain constant. These algorithms satisfy the requirements outlined before.

### Real-World Use of Adaptive Matrix Computation Algorithms and GitHub

In Chapter [8](#522202_1_En_8_Chapter.xhtml), I discuss several real-world applications of these adaptive matrix computation algorithms. I also published the code for these applications in a public GitHub [Chanchal Chatterjee GitHub].

## 1.4 Common Methodology for Derivations of Algorithms

My contributions in this book are two-fold:

1.  I present a common methodology to derive and analyze each adaptive algorithm.

2.  I present adaptive algorithms to a number of matrix algebra problems.

The literature for adaptive algorithms for matrix computation offers a wide range of techniques (including ad hoc methods) and various types of convergence procedures. In this book, I present a *common methodology* to derive and prove the convergence of the adaptive algorithms (all proofs are in the GitHub).

**The** **advantage** **of adopting this is to allow the reader to follow the methodology and derive new adaptive algorithms for their use cases.**

In the following chapters, I follow the following steps to derive each algorithm:

1.  **Objective function**

I first present an *objective function* *J*(*W*;*A*[*k*]) such that the minimizer *W*^(***) of *J* is the desired matrix function of the data matrix *A*.

1.  **Derive the adaptive algorithm**

I derive an *adaptive update rule* for matrix *W* by applying the gradient descent technique on the objective function *J*(*W*;*A*[*k*]). The adaptive gradient descent update rule is

*W*[*k* + 1] = *W*[*k*] − *η*[*k*]∇[*W*]*J*(*W*[*k*], *A*[*k*]) = *W*[*k*] + *η*[*k*]*h*(*W*[*k*], *A*[*k*]),     (1.21)

where the function *h*(*W*[*k*],*A*[*k*]) follows certain continuity and regularity properties and *η*[*k*] is a decreasing gain sequence.

1.  **Speed up the adaptive algorithm**

The availability of the objective function *J*(*W*;*A*) allows us to speed up the adaptive algorithm by applying speedup techniques in optimization theory such as steepest descent, conjugate direction, Newton-Raphson, and recursive least squares. Details of these methods for principal component analysis are given in Chapter [6](#522202_1_En_6_Chapter.xhtml).

1.  **Show the algorithms converge to the matrix functions**

In this book, I provide numerical experiments to demonstrate the convergence of these algorithms. The mathematical proofs are provided in a separate document in the GitHub [Chatterjee Github].

An important benefit of following this methodology is to allow practitioners to derive new algorithms for their own use cases.

### Matrix Algebra Problems Solved Here

In the applications, I consider the data as arriving in temporal succession, such as in vector sequences {**x**[*k*]} or {**y**[*k*]}, or in matrix sequences {*A*[*k*]} or {*B*[*k*]}. Note that the algorithms given here can be used for non-stationary input streams.

In the following chapters, I present novel adaptive algorithms to estimate the following matrix functions:

*   Mean, correlation, and normalized mean of {**x**[*k*]}

*   Square root (*A*^½) and inverse of the square root (*A*^(–½)) of *A* from {**x**[*k*]} or {*A*[*k*]}

*   Principal eigenvector of *A* from {**x**[*k*]} or {*A*[*k*]}

*   Principal and minor eigenvector of *A* from {**x**[*k*]} or {*A*[*k*]}

*   Generalized eigenvectors of *A* with respect to *B* from {**x**[*k*]} and {**z**[*k*]} or {*A*[*k*]} and {*B*[*k*]}

*   Singular value decomposition (SVD) of *C* from {**x**[*k*]} and {**z**[*k*]} or {*C*[*k*]}

Besides these algorithms, I also discuss

*   Adaptive computation of mean, covariance, and matrix inversion

*   Methods to accelerate the adaptive algorithms by techniques of nonlinear optimization

## 1.5 Outline of The Book

In the following chapters, I discuss in detail the formulation, derivation, convergence, and experimental results of many adaptive algorithms for various matrix algebra problems.

In Chapter [2](#522202_1_En_2_Chapter.xhtml), I discuss basic terminologies and methods used throughout the remaining chapters. This chapter also discusses adaptive algorithms mean, median, correlation, covariance, and inverse correlation/covariance computation for both stationary and non-stationary data. I further discusses novel adaptive algorithms for normalized mean computation.

In Chapter [3](#522202_1_En_3_Chapter.xhtml), I discuss three adaptive algorithms for the computation of the square root of a matrix sequence. I next discuss three algorithms for the inverse square root of the same. I offer objective functions and convergence proofs for these algorithms.

In Chapter [4](#522202_1_En_4_Chapter.xhtml), I discuss 11 algorithms, some of them new algorithms, for the adaptive computation of the first principal eigenvector of a matrix sequence or the online correlation matrix of a vector sequence. I offer best practices to choose the algorithm for a given application. I follow the common methodology of deriving and analyzing the convergence of each algorithm, supported by experimental results.

In Chapter [5](#522202_1_En_5_Chapter.xhtml), I present 21 adaptive algorithms for the computation of principal and minor eigenvectors of a matrix sequence or the online correlation matrix of a vector sequence. These algorithms are derived from 7 different types of objective functions, each under 3 different conditions. Each algorithm is derived, discussed, and shown to converge analytically and experimentally. I offer best practices to choose the algorithm for a given application.

Since I have objective functions for all of the adaptive algorithms, in Chapter [6](#522202_1_En_6_Chapter.xhtml), I deviate from the traditional gradient descent method of deriving the algorithms. Here I derive new computationally faster algorithms by using steepest descent, conjugate direction, Newton-Raphson, and recursive least squares on the objective functions. Experimental results and comparison with state-of-the-art algorithms show the faster convergence of these adaptive algorithms.

In Chapter [7](#522202_1_En_7_Chapter.xhtml), I discuss 21 adaptive algorithms for generalized eigen-decomposition from two matrix or vector sequences. Once again, I follow the common methodology and derive all algorithms from objective functions, followed by experimental results.

In Chapter [8](#522202_1_En_8_Chapter.xhtml), I present real-world applications of these algorithms with examples and code.

The bibliography is in Chapter [9](https://doi.org/10.1007/978-1-4842-8017-1_9).

# 2. General Theories and Notations

## 2.1 Introduction

In this chapter, I present algorithms for the adaptive solutions of matrix algebra problems from a sequence of matrices. The streams or sequences can be random matrices {*A*[*k*]} or {*B*[*k*]}, or the correlation matrices of random vector sequences {**x**[*k*]} or {**y**[*k*]}. Examples of matrix algebra are matrix inversion, square root, inverse square root, eigenvectors, generalized eigenvectors, singular vectors, and generalized singular vectors.

This chapter additionally covers the basic terminologies and methods used throughout the remaining chapters. I also present well-known adaptive algorithms to compute the mean, median, covariance, inverse covariance, and correlation matrices from random matrix or vector sequences. Furthermore, I present a new algorithm to compute the normalized mean of a random vector sequence.

For the sake of simplicity, let’s assume that the multidimensional data {**x**[*k*]∈ℜ^(*n*)} arrives as a sequence. From this data sequence, we can derive a matrix sequence {![$$ {A}_k={\mathbf{x}}_k{\mathbf{x}}_k^T $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq1.png)}. We define the data correlation matrix *A* as follows:

![$$ A=\underset{k\to \infty }{\lim }E\left[{\mathbf{x}}_k{\mathbf{x}}_k^T\right] $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq2.png).     (2.1)

## 2.2 Stationary and Non-Stationary Sequences

In practical implementations, we face two types of sequences: stationary and non-stationary . A sequence {**x**[*k*]} is considered *asymptotically* (*weak*) *stationary* if ![$$ {\lim}_{k\to \infty }E\left[{\mathbf{x}}_k{\mathbf{x}}_k^T\right] $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq3.png) is a constant. For a *non-stationary* sequence {**x**[*k*]}, ![$$ E\left[{\mathbf{x}}_k{\mathbf{x}}_{k+m}^T\right] $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq4.png) remains a function of both *k* and *m*, and ![$$ E\left[{\mathbf{x}}_k{\mathbf{x}}_k^T\right] $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq5.png) is a function of *k*. Examples of non-stationary data are given in *Publicly Real-World Datasets to Evaluate Stream Learning Algorithms* [Vinicius Souza et al*.* 20]. Figure [2-1](#522202_1_En_2_Chapter.xhtml#Fig1) shows examples of stationary and non-stationary data.

![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig1a_HTML.png)![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig1b_HTML.png)

Figure 2-1

Examples of stationary and non-stationary data

Non-stationarity in data can be detected by well-known techniques described in this reference [Shay Palachy 19].

## 2.3 Use Cases for Adaptive Mean, Median, and Covariances

Adaptive mean computation is important in real-world applications even though it is one of the simplest algorithms.

### Handwritten Character Recognition

Consider the problem of detecting handwritten number 8. Figure [2-2](#522202_1_En_2_Chapter.xhtml#Fig2) shows six instances of the number 8 from the Keras dataset MNIST images [Keras, MNIST].

![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig2_HTML.jpg)

Figure 2-2

Examples of variations in handwritten number 8

One algorithm to recognize the number 8 with all its variations is to find the mean set of pixels that represent it. Due to random variations in handwriting, it is difficult to compile all variations of each character ahead of time. It is important to design an adaptive learning scheme whereby instantaneous variations in these characters are immediately represented in the machine learning algorithm.

The adaptive mean detection algorithm is helpful in finding the common pattens that represent the number 8\. Figure [2-3](#522202_1_En_2_Chapter.xhtml#Fig3) shows a template for the number 8 obtained by the adaptive mean algorithm given in Eq. ([2.2](#522202_1_En_2_Chapter.xhtml#Par15)).

![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig3a_HTML.png)![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig3b_HTML.png)

Figure 2-3

Template for number 8\. 2D representation on the left and 3D on the right

### Anomaly Detection of Streaming Data

A simple yet powerful algorithm to detect anomalies in data is to calculate the median and compare the current value against the median of the data. Figure [2-4](#522202_1_En_2_Chapter.xhtml#Fig4) shows streaming data [Yahoo Research Webscope S5 Data] containing occasional anomalous values. We adaptively computed the median with algorithm ([2.20](#522202_1_En_2_Chapter.xhtml#Par57)) and compared it against the data to detect anomalies. Here the data samples are in blue, the adaptive median is in green, and anomalies are in red.

![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig4_HTML.png)

Figure 2-4

Anomalies detected with an adaptive median algorithm on time series data

More on this topic is discussed in Chapter [8](#522202_1_En_8_Chapter.xhtml).

## 2.4 Adaptive Mean and Covariance of Nonstationary Sequences

In the stationary case, given a sequence {**x**[*k*]∈ℜ^(*n*)}, we can compute the adaptive mean **m**[*k*] as follows:

![$$ {\mathbf{m}}_k=\frac{1}{k}\sum \limits_{i=1}^k{\mathbf{x}}_i={\mathbf{m}}_{k-1}+\frac{1}{k}\left({\mathbf{x}}_k-{\mathbf{m}}_{k-1}\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq6.png).     (2.2)

Similarly, the adaptive correlation *A*[*k*] is

![$$ {A}_k=\frac{1}{k}\sum \limits_{i=1}^k{\mathbf{x}}_i{\mathbf{x}}_i^T={A}_{k-1}+\frac{1}{k}\left({\mathbf{x}}_k{\mathbf{x}}_k^T-{A}_{k-1}\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq7.png).     (2.3)

Here we use all samples up to time instant *k*.

If, however, the data is non-stationary, we use a *forgetting factor* 0<*β*≤1 to implement an effective window of size 1/(1–*β*) as

![$$ {\mathbf{m}}_k=\frac{1}{k}\sum \limits_{i=1}^k{\beta}^{k-i}{\mathbf{x}}_i=\beta {\mathbf{m}}_{k-1}+\frac{1}{k}\left({\mathbf{x}}_k-\beta {\mathbf{m}}_{k-1}\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq8.png)     (2.4)

and

![$$ {A}_k=\frac{1}{k}\sum \limits_{i=1}^k{\beta}^{k-i}{\mathbf{x}}_i{\mathbf{x}}_i^T=\beta {A}_{k-1}+\frac{1}{k}\left({\mathbf{x}}_k{\mathbf{x}}_k^T-\beta {A}_{k-1}\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq9.png).     (2.5)

This effective window ensures that the past data samples are downweighted with an exponentially fading window compared to the recent ones in order to afford the tracking capability of the adaptive algorithm. The exact value of *β* depends on the specific application. Generally speaking, for slow time-varying {**x**[*k*]}, *β* is chosen close to 1 to implement a large effective window, whereas for fast time-varying {**x**[*k*]}, *β* is chosen near zero for a small effective window [Benveniste et al*.* 90].

The following is Python code to adaptively compute a mean vector and correlation matrix with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
# Eq.2.4
m = beta * m  + (1.0/(1 + cnt)) * (x - beta * m)
# Eq.2.5
A = beta * A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - beta * A)
```

## 2.5 Adaptive Covariance and Inverses

Given the mean and correlations discussed before, the adaptive covariance matrix *B*[*k*] can also be computed as follows:

![$$ {B}_k=\frac{1}{k}\sum \limits_{i=1}^k{\beta}^{k-i}\left({\mathbf{x}}_i-{\mathbf{m}}_i\right){\left({\mathbf{x}}_i-{\mathbf{m}}_i\right)}^T=\beta {B}_{k-1}+\frac{1}{k}\left(\left({\mathbf{x}}_k-{\mathbf{m}}_k\right){\left({\mathbf{x}}_k-{\mathbf{m}}_k\right)}^T-\beta {B}_{k-1}\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq10.png)     (2.6)

From the adaptive correlation matrix *A*[*k*] in ([2.5](#522202_1_En_2_Chapter.xhtml#Par22)), the inverse correlation matrix ![$$ {A}_k^{-1} $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq11.png) can be obtained adaptively by the Sherman-Morrison formula [Sherman–Morrison, Wikipedia] as

![$$ {A}_k^{-1}=\frac{k}{\beta \left(k-1\right)}\left({A}_{k-1}^{-1}-\frac{A_{k-1}^{-1}{\mathbf{x}}_k{\mathbf{x}}_k^T{A}_{k-1}^{-1}}{\beta \left(k-1\right)+{\mathbf{x}}_k^T{A}_{k-1}^{-1}{\mathbf{x}}_k}\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq12.png).     (2.7)

Similarly, the inverse covariance matrix ![$$ {B}_k^{-1} $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq13.png) can be obtained adaptively as

![$$ {B}_k^{-1}=\frac{k}{\beta \left(k-1\right)}\left({B}_{k-1}^{-1}-\frac{B_{k-1}^{-1}\left({\mathbf{x}}_k-{\mathbf{m}}_k\right){\left({\mathbf{x}}_k-{\mathbf{m}}_k\right)}^T{B}_{k-1}^{-1}}{\beta \left(k-1\right)+{\left({\mathbf{x}}_k-{\mathbf{m}}_k\right)}^T{B}_{k-1}^{-1}\left({\mathbf{x}}_k-{\mathbf{m}}_k\right)}\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq14.png).     (2.8)

The following is Python code to adaptively compute inverse correlation and inverse covariance matrices with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
y = Y[:,iter]
y = x.reshape(nDim,1)
# Eq.2.7
k = cnt+2
AW = (k/(beta*(k-1))) * (AW - (AW * (x @ x.T) * AW) \
/ (beta*(k-1) + x.T @ AW @ x))
# Eq.2.8
BW = (k/(beta*(k-1))) * (BW - (BW * (y @ y.T) * BW) \
/ (beta*(k-1) + y.T @ BW @ y))
```

## 2.6 Adaptive Normalized Mean Algorithm

The most obvious choice for an adaptive normalized mean algorithm is to use ([2.2](#522202_1_En_2_Chapter.xhtml#Par15)) and normalize each **m**[*k*]. However, a more efficient algorithm can be obtained from the following cost function whose minimizer **w**^* is the asymptotic normalized mean **m**/‖**m**‖, where **m** = lim[*k* → ∞]*E*[**x**[*k*]]:

![$$ J\left({\mathbf{w}}_k;{\mathbf{x}}_k\right)={\left\Vert {\mathbf{x}}_k-{\mathbf{w}}_k\right\Vert}^2+\alpha \left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq15.png),     (2.9)

where α is a Lagrange multiplier that enforces the constraint that the mean is normalized. The gradient of *J*(**w**[*k*];**x**[*k*]) with respect to **w**[*k*] is

![$$ \left(1/2\right){\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{\mathbf{x}}_k\right)=-\left({\mathbf{x}}_k-{\mathbf{w}}_k\right)+\alpha {\mathbf{w}}_k $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq16.png).     (2.10)

Multiplying ([2.10](#522202_1_En_2_Chapter.xhtml#Par35)) by ![$$ {\mathbf{w}}_k^T $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq17.png) and applying the constraint ![$$ {\mathbf{w}}_k^T{\mathbf{w}}_k=1 $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq18.png), we obtain

![$$ \alpha ={\mathbf{w}}_k^T{\mathbf{x}}_k-1 $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq19.png).     (2.11)

Using this *α* in ([2.11](#522202_1_En_2_Chapter.xhtml#Par37)), we obtain the adaptive gradient descent algorithm for normalized mean:

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k+{\eta}_k\left({\mathbf{x}}_k-{\mathbf{w}}_k^T{\mathbf{x}}_k{\mathbf{w}}_k\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq20.png),     (2.12)

where *η*[*k*] is a small decreasing constant, which follows assumption A1.2 in the Proofs of Convergence in GitHub [Chatterjee GitHub].

### Variations of the Adaptive Normalized Mean Algorithm

There are several variations of the objective function ([2.9](#522202_1_En_2_Chapter.xhtml#Par33)) that lead to many other adaptive algorithms for normalized mean computation. One variation of ([2.9](#522202_1_En_2_Chapter.xhtml#Par33)) is to place the value of *α* in ([2.11](#522202_1_En_2_Chapter.xhtml#Par37)) in the objective function ([2.9](#522202_1_En_2_Chapter.xhtml#Par33)) to obtain the following objective function:

![$$ J\left({\mathbf{w}}_k;{\mathbf{x}}_k\right)={\left\Vert {\mathbf{x}}_k-{\mathbf{w}}_k\right\Vert}^2+\left({\mathbf{w}}_k^T{\mathbf{x}}_k-1\right)\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq21.png).     (2.13)

Unlike ([2.9](#522202_1_En_2_Chapter.xhtml#Par33)), this objective function is unconstrained and has the constraint ![$$ {\mathbf{w}}_k^T{\mathbf{w}}_k=1 $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq22.png) built into it. It leads to the following adaptive algorithm:

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k+{\eta}_k\left(2{\mathbf{x}}_k-{\mathbf{w}}_k^T{\mathbf{x}}_k{\mathbf{w}}_k-{\mathbf{w}}_k^T{\mathbf{w}}_k{\mathbf{x}}_k\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq23.png).     (2.14)

Another variation is the use of a penalty function method of nonlinear optimization that enforces the constraint ![$$ {\mathbf{w}}_k^T{\mathbf{w}}_k=1 $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq24.png). This objective function is

![$$ J\left({\mathbf{w}}_k;{\mathbf{x}}_k\right)={\left\Vert {\mathbf{x}}_k-{\mathbf{w}}_k\right\Vert}^2+\frac{\mu }{2}{\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)}^2 $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq25.png),     (2.15)

where *μ* is a positive penalty constant. This objective function is also unconstrained and leads to the following adaptive algorithm:

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k+{\eta}_k\left({\mathbf{x}}_k-{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq26.png).     (2.16)

The following is the Python code to adaptively compute a normalized mean by algorithms ([2.12](#522202_1_En_2_Chapter.xhtml#Par39)), ([2.14](#522202_1_En_2_Chapter.xhtml#Par44)), and ([2.16](#522202_1_En_2_Chapter.xhtml#Par48)) with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
# Eq.2.12
w1 = w1 + (1.0/(100+cnt))*(x - (w1.T @ x)*w1)
# Eq.2.14
w2 = w2 + (1.0/(100+cnt))*(2*x-(w2.T @ x)*w2 - (w2.T @ w2)*x)
# Eq.2.16
w3 = w3 + (1.0/(100+cnt))*(x - w3- mu* w3 @ ((w3.T @ w3)-1))
```

## 2.7 Adaptive Median Algorithm

Given a sequence {**x**[*k*]}, its asymptotic median *μ* satisfies the following:

![$$ \underset{k\to \infty }{\lim}\mathrm{P}\left({\mathbf{x}}_k\ge \mu \right)=\underset{k\to \infty }{\lim}\mathrm{P}\left({\mathbf{x}}_k&lt;\mu \right)=0.5 $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq27.png)     (2.17)

where P(*E*) is the probability measure of event *E* and 0 ≤ P(*E*) ≤ 1\. The objective function *J*(**w**[*k*];**x**[*k*]) whose minimizer **w**^* is the asymptotic median *μ* is

*J*(**w**[*k*]; **x**[*k*]) = ‖**x**[*k*] − **w**[*k*]‖.     (2.18)

The gradient of *J*(**w**[*k*];**x**[*k*]) with respect to **w**[*k*] is

![$$ {\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{\mathbf{x}}_k\right)=-\operatorname{sgn}\left({\mathbf{x}}_k-{\mathbf{w}}_k\right) $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq28.png),     (2.19)

where sgn(.) is the sign operator (sgn(*x*)=1 if *x*≥0 and –1 if *x*<0). From the gradient in ([2.19](#522202_1_En_2_Chapter.xhtml#Par55)), we obtain the adaptive gradient descent algorithm:

**w**[*k* + 1] = **w**[*k*] + *η*[*k*] sgn (**x**[*k*] − **w**[*k*]).     (2.20)

The following is the Python code to adaptively compute the median by algorithm ([2.20](#522202_1_En_2_Chapter.xhtml#Par57)) with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
# Eq.2.20
md = md + (3.0/(1 + cnt)) * np.sign(x - md)
```

## 2.8 Experimental Results

The purpose of these experiments is to demonstrate the performance and accuracies of the adaptive algorithms for mean, correlation, inverse correlation, inverse covariance, normalized mean, and median.

I generated 1,000 sample vectors {**x**[*k*]} of five-dimensional Gaussian data (i.e., *n*=5) with the following mean and covariance:

Mean = [10 7 6 5 1],

Covariance = ![$$ \left[\begin{array}{ccccc}2.091&amp; 0.038&amp; \hbox{-} 0.053&amp; \hbox{-} 0.005&amp; 0.010\\ {}0.038&amp; 1.373&amp; 0.018&amp; \hbox{-} 0.028&amp; \hbox{-} 0.011\\ {}\hbox{-} 0.053&amp; 0.018&amp; 1.430&amp; 0.017&amp; 0.055\\ {}\hbox{-} 0.005&amp; \hbox{-} 0.028&amp; 0.017&amp; 1.084&amp; \hbox{-} 0.005\\ {}0.010&amp; \hbox{-} 0.011&amp; 0.055&amp; \hbox{-} 0.005&amp; 1.071\end{array}\right] $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_IEq29.png).

For each algorithm, I computed the error as the Frobenius norm [Frobenius norm, Wikipedia] of the estimated value at each iteration of the algorithm and the actual computed value from all of the 1,000 samples.

![$$ error(k)={\left\Vert Estimated\ Value\ (k)- Actual\ Value\right\Vert}_F. $$](images/522202_1_En_2_Chapter/522202_1_En_2_Chapter_TeX_Equa.png)

I computed the mean vector and correlation matrices by the adaptive algorithms ([2.4](#522202_1_En_2_Chapter.xhtml#Par20)) and ([2.5](#522202_1_En_2_Chapter.xhtml#Par22)), respectively. Figure [2-5](#522202_1_En_2_Chapter.xhtml#Fig5) shows the results.

![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig5_HTML.png)

Figure 2-5

Convergence of the mean vector and correlation matrices with algorithms ([2.4](#522202_1_En_2_Chapter.xhtml#Par20)) and ([2.5](#522202_1_En_2_Chapter.xhtml#Par22)), respectively

I computed the inverses of the correlation and covariance matrices with algorithms ([2.7](#522202_1_En_2_Chapter.xhtml#Par28)) and ([2.8](#522202_1_En_2_Chapter.xhtml#Par30)), respectively. Figure [2-6](#522202_1_En_2_Chapter.xhtml#Fig6) shows the results.

![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig6_HTML.png)

Figure 2-6

Convergence of the inverse correlation and inverse covariance matrices with algorithms ([2.7](#522202_1_En_2_Chapter.xhtml#Par28)) and ([2.8](#522202_1_En_2_Chapter.xhtml#Par30)), respectively

I computed the normalized mean by adaptive algorithms ([2.12](#522202_1_En_2_Chapter.xhtml#Par39)) and ([2.14](#522202_1_En_2_Chapter.xhtml#Par44)), and the median by algorithm ([2.20](#522202_1_En_2_Chapter.xhtml#Par57)). For each value of *k* in these algorithms, I computed the errors between the **w**[*k*] (estimate) and the actual values of normalized mean and median obtained from the entire 1,000 sample data. I used the 5X1 zero vector as the starting values (**w**[0]) for all algorithms. The results are shown in Figure [2-7](#522202_1_En_2_Chapter.xhtml#Fig7).

![](images/522202_1_En_2_Chapter/522202_1_En_2_Fig7_HTML.png)

Figure 2-7

Convergence of the normalized mean of {**x**[k]} by adaptive algorithms ([2.12](#522202_1_En_2_Chapter.xhtml#Par39)), ([2.14](#522202_1_En_2_Chapter.xhtml#Par44)), and ([2.16](#522202_1_En_2_Chapter.xhtml#Par48)) and the median by adaptive algorithm ([2.20](#522202_1_En_2_Chapter.xhtml#Par57))

All adaptive algorithms converged rapidly. After the 1,000 samples were processed, the errors were 0.0043 for algorithms ([2.14](#522202_1_En_2_Chapter.xhtml#Par44)) and ([2.16](#522202_1_En_2_Chapter.xhtml#Par48)), and 0.0408 for algorithm ([2.20](#522202_1_En_2_Chapter.xhtml#Par57)). See the code in the [GitHub](https://github.com/cchatterj0/AdaptiveMLAlgorithms/tree/master/Chapter2-Cov-Mean-Median) repository [Chatterjee GitHub].

# 3. Square Root and Inverse Square Root

## 3.1 Introduction and Use Cases

Adaptive computation of the square root and inverse square root of the real-time correlation matrix of a streaming sequence {**x**[*k*]∈ℜ^(*n*)} has numerous applications in machine learning, data analysis, and image/signal processing. They include data whitening, classifier design, and data normalization [Foley and Sammon 75; Fukunaga 90].

Data whitening is a process of decorrelating the data such that all components have unit variance. It is a data preprocessing step in machine learning and data analysis to “normalize” the data so that it is easier to model. Prominent applications are

*   To transform correlated noise in a signal to independent and identically distributed (iid) noise, which is easier to classify

*   Generalized eigenvector computation [Chatterjee et al*.* Mar 97] (see Chapter [7](#522202_1_En_7_Chapter.xhtml))

*   Linear discriminant analysis computation [linear discriminant analysis, Wikipedia]

*   Gaussian classifier design and computation of distance measures [Chatterjee et al*.* May 97]

Figure [3-1](#522202_1_En_3_Chapter.xhtml#Fig1) shows the correlation matrices of the original and whitened data. The original data is highly correlated as shown by the colors on all axes. The whitened data is fully uncorrelated with no correlation between components since only diagonal values exist.

![](images/522202_1_En_3_Chapter/522202_1_En_3_Fig1_HTML.jpg)

Figure 3-1

Original correlated data on the left and the uncorrelated “whitened” data on the right

Figure [3-2](#522202_1_En_3_Chapter.xhtml#Fig2) shows a handwritten number 0 obtained from the Keras MNIST dataset [Keras, MNIST]. The correlation matrix on the right shows that the data pixels are highly correlated for all pixels.

![](images/522202_1_En_3_Chapter/522202_1_En_3_Fig2a_HTML.jpg)![](images/522202_1_En_3_Chapter/522202_1_En_3_Fig2b_HTML.jpg)

Figure 3-2

Handwritten MNIST number 0 and the correlation matrix of all characters

The following Python code whitens the data samples `X[nDim,nSamples]`:

```
from scipy.linalg import eigh
nDim = X.shape[0]
corX = (X @ X.T) / nSamples
eigvals, eigvecs = eigh(corX)
V  = np.fliplr(eigvecs)
D = np.zeros(shape=(nDim,nDim))
for i in range(nDim):
if (eigvals[::-1][i] < 10):
D[i,i] = 0
else:
D[i,i] = np.sqrt(1/eigvals[::-1][i])
Z = V @ D @ V.T @ X
```

Next let’s see the transformed data and the new correlation matrix. Figure [3-3](#522202_1_En_3_Chapter.xhtml#Fig3) shows that the differentiated features of the character are accentuated and the correlation matrix is diagonal and not distributed along all pixels, showing that the data is whitened with the identity correlation matrix.

![](images/522202_1_En_3_Chapter/522202_1_En_3_Fig3a_HTML.jpg)![](images/522202_1_En_3_Chapter/522202_1_En_3_Fig3b_HTML.jpg)

Figure 3-3

Handwritten MNIST number 0 after whitening. The correlation matrix of all characters is diagonal

We define the data correlation matrix *A* as

![$$ {A}_k=\frac{1}{k}\sum \limits_{i=1}^k{\mathbf{x}}_i{\mathbf{x}}_i^T={A}_{k-1}+\frac{1}{k}\left({\mathbf{x}}_k{\mathbf{x}}_k^T-{A}_{k-1}\right). $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_Equa.png)

The square root of *A*, also called the Cholesky decomposition [Cholesky decomposition, Wikipedia], is denoted by *A*^½. Similarly, *A*^(–½) denotes the inverse square root of *A*. However, as explained below, there is no unique solution for both of these matrix functions, and various adaptive algorithms can be obtained where each algorithm leads to a different solution. Even though there are numerous solutions for these matrix functions, there are unique solutions under some restrictions, such as a unique symmetric positive definite solution.

In this chapter, I present three adaptive algorithms for each matrix function *A*^½ and *A*^(–½). Out of the three, one adaptive algorithm leads to the symmetric positive definite solution and two lead to more general solutions.

### Various Solutions for A^½ and A^(–½)

Let *A*=ΦΛΦ^(*T*) be the eigen-decomposition of the real, symmetric, positive definite *n*X*n* matrix *A*, where Φ and Λ are respectively the eigenvector and eigenvalue matrices of *A*. Here Λ=diag(*λ*[1],…,*λ*[*n*]) is the diagonal eigenvalue matrix with *λ*[1]≥…≥*λ*[*n*]>0, and Φ∈ℜ^(*nXn*) is orthonormal. A solution for *A*^½ is *L*=Φ*D*, where *D*=![$$ \operatorname{diag}\left(\pm {\lambda}_1^{\frac{1}{2}},\dots, \pm {\lambda}_n^{\frac{1}{2}}\right) $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq1.png). However, in general this is not a symmetric solution, and for any orthonormal^([¹](#fn1)) matrix *U*, Φ*DU* is also a solution. We can show that *A*^½ is symmetric if, and only if, it is of the form Φ*DΦ*^(*T*), and there are 2^(*n*) symmetric solutions for *A*^½. When *D* is positive definite, we obtain the unique symmetric positive definite solution for *A*^½ as ΦΛ^½Φ^(*T*), where Λ^½ = ![$$ \operatorname{diag}\left({\lambda}_1^{\frac{1}{2}},\dots, {\lambda}_n^{\frac{1}{2}}\right) $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq2.png). Similarly, a general solution for the inverse square root *A*^(–½) of *A* is Φ*D*^(–1)*U*, where *D* is defined before and *U* is any orthonormal matrix. The unique symmetric positive definite solution for *A*^(–½) is ΦΛ^(–½)Φ^(*T*), where Λ^(–½) =![$$ \operatorname{diag}\left({\lambda}_1^{-\frac{1}{2}},\dots, {\lambda}_n^{-\frac{1}{2}}\right) $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq3.png).

### Outline of This Chapter

In sections [3.2](#522202_1_En_3_Chapter.xhtml#Sec4), [3.3](#522202_1_En_3_Chapter.xhtml#Sec7), and [3.4](#522202_1_En_3_Chapter.xhtml#Sec10), I discuss three algorithms for the adaptive computation of *A*^½. Section [3.4](#522202_1_En_3_Chapter.xhtml#Sec10) discusses the unique symmetric positive definite solution for *A*^½. In Sections [3.5](#522202_1_En_3_Chapter.xhtml#Sec12), [3.6](#522202_1_En_3_Chapter.xhtml#Sec15), and [3.7](#522202_1_En_3_Chapter.xhtml#Sec18), I discuss three algorithms for the adaptive computation of *A*^(–½). Section [3.7](#522202_1_En_3_Chapter.xhtml#Sec18) describes the unique symmetric positive definite solution for *A*^(–½). Section [3.8](#522202_1_En_3_Chapter.xhtml#Sec20) presents experimental results for the six algorithms with 10-dimensional Gaussian data. Section [3.9](#522202_1_En_3_Chapter.xhtml#Sec23) concludes the chapter.

## 3.2 Adaptive Square Root Algorithm: Method 1

Let {**x**[*k*∈]ℜ^(*n*)} be a sequence of data vectors whose online data correlation matrix *A*[*k*∈]ℜ^(*nXn*) is given by

![$$ {A}_k=\frac{1}{k}\sum \limits_{i=1}^k{\beta}^{k-i}{\mathbf{x}}_i{\mathbf{x}}_i^T $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq4.png).     (3.1)

Here **x**[*k*] is an observation vector at time *k* and 0<*β*≤1 is a forgetting factor used for non-stationary sequences. If the data is stationary, the asymptotic correlation matrix *A* is

![$$ A=\underset{k\to \infty }{\lim }E\left[{A}_k\right] $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq5.png).     (3.2)

### Objective Function

Following the methodology described in Section [1.​4](#522202_1_En_1_Chapter.xhtml#Sec19), I present the algorithm by first showing an objective function *J*, whose minimum with respect to matrix *W* gives us the square root of the asymptotic data correlation matrix *A*. The objective function is

![$$ J(W)={\left\Vert A-{W}^TW\right\Vert}_F^2 $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq6.png).     (3.3)

The gradient of *J*(*W*) with respect to *W* is

∇[*W*]*J*(*W*) =  − 4*W*(*A* − *W*^(*T*)*W*).     (3.4)

### Adaptive Algorithm

From the gradient in ([3.4](#522202_1_En_3_Chapter.xhtml#Par25)), we obtain the following adaptive gradient descent algorithm:

![$$ {W}_{k+1}={W}_k-{\eta}_k\left(1/4\right){\nabla}_WJ\left({W}_k;{A}_k\right)={W}_k+{\eta}_k\left({W}_k{A}_k-{W}_k{W}_k^T{W}_k\right) $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq7.png),     (3.5)

where *η*[*k*] is a small decreasing constant and follows assumption A1.2 in the Proofs of Convergence in the GitHub [Chatterjee GitHub].

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
etat1 = 1.0/(50 + cnt)
# Algorithm 1
W1 = W1 + etat1 * (W1 @ A - W1 @ W1.T @ W1)
```

## 3.3 Adaptive Square Root Algorithm: Method 2

### Objective Function

The objective function *J*(*W*), whose minimum with respect to *W* gives us the square root of *A*, is

![$$ J(W)={\left\Vert A-{WW}^T\right\Vert}_F^2 $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq8.png).     (3.6)

The gradient of *J*(*W*) with respect to *W* is

∇[*W*]*J*(*W*) =  − 4(*A* − *WW*^(*T*) )*W*.     (3.7)

### Adaptive Algorithm

We obtain the following adaptive gradient descent algorithm for square root of *A*:

![$$ {W}_{k+1}={W}_k-{\eta}_k\left(1/4\right){\nabla}_WJ\left({W}_k;{A}_k\right)={W}_k+{\eta}_k\left({A}_k{W}_k-{W}_k{W}_k^T{W}_k\right) $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq9.png),     (3.8)

where *η*[*k*] is a small decreasing constant.

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
etat1 = 1.0/(50 + cnt)
# Algorithm 2
W2 = W2 + etat1 * (A @ W2 - W2 @ W2.T @ W2)
```

## 3.4 Adaptive Square Root Algorithm: Method 3

### Adaptive Algorithm

Following the adaptive algorithms ([3.5](#522202_1_En_3_Chapter.xhtml#Par27)) and ([3.8](#522202_1_En_3_Chapter.xhtml#Par35)), I now present an algorithm for the computation of a symmetric positive definite square root of *A*:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k-{W}_k^2\right) $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq10.png),     (3.9)

where *η*[*k*] is a small decreasing constant and *W*[*k*] is symmetric.

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
etat2 = 1.0/(50 + cnt)
# Algorithm 3
W3 = W3 + etat2 * (A - W3 @ W3)
```

## 3.5 Adaptive Inverse Square Root Algorithm: Method 1

### Objective Function

The objective function *J*(*W*), whose minimizer *W*^(***) gives us the inverse square root of *A*, is

![$$ J(W)={\left\Vert I-{W}^T AW\right\Vert}_F^2 $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq11.png).     (3.10)

The gradient of *J*(*W*) with respect to *W* is

∇[*W*]*J*(*W*) =  − 4*AW*(*I* − *W*^(*T*)*AW*).     (3.11)

### Adaptive Algorithm

From the gradient in ([3.11](#522202_1_En_3_Chapter.xhtml#Par45)), we obtain the following adaptive gradient descent algorithm:

![$$ {W}_{k+1}={W}_k-{\eta}_k\left(1/4\right){A}_k^{-1}{\nabla}_WJ\left({W}_k;{A}_k\right)={W}_k+{\eta}_k\left({W}_k-{W}_k{W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq12.png)     (3.12)

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
etat1 = 1.0/(100 + cnt)
# Algorithm 1
W1 = W1 + etat1 * (W1 - W1 @ W1.T @ A @ W1)
```

## 3.6 Adaptive Inverse Square Root Algorithm: Method 2

### Objective Function

The objective function *J*(*W*), whose minimum with respect to *W* gives us the inverse square root of *A*, is

![$$ J(W)={\left\Vert I-{WAW}^T\right\Vert}_F^2 $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq13.png).     (3.13)

The gradient of *J*(*W*) with respect to *W* is

∇[*W*]*J*(*W*) =  − 4(*I* − *WAW*^(*T*))*WA*.     (3.14)

### Adaptive Algorithm

We obtain the following adaptive algorithm for the inverse square root of *A*:

![$$ {W}_{k+1}={W}_k-{\eta}_k\left(1/4\right){\nabla}_WJ\left({W}_k;{A}_k\right){A}_k^{-1}={W}_k+{\eta}_k\left({W}_k-{W}_k{A}_k{W}_k^T{W}_k\right) $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq14.png),     (3.15)

where *η*[*k*] is a small decreasing constant.

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
etat1 = 1.0/(100 + cnt)
# Algorithm 2
W2 = W2 + etat1 * (W2 - W2 @ A @ W2.T @ W2)
```

## 3.7 Adaptive Inverse Square Root Algorithm: Method 3

### Adaptive Algorithm

By extending the adaptive algorithms ([3.12](#522202_1_En_3_Chapter.xhtml#Par47)) and ([3.15](#522202_1_En_3_Chapter.xhtml#Par54)), I now present an adaptive algorithm for the computation of a symmetric positive definite inverse square root of *A*:

*W*[*k* + 1] = *W*[*k*] + *η*[*k*](*I* − *W*[*k*]*A*[*k*]*W*[*k*]),     (3.16)

where *η*[*k*] is a small decreasing constant and *W*[*k*] is symmetric.

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
etat2 = 1.0/(100 + cnt)
# Algorithm 3
W3 = W3 + etat2 * (I - W3 @ A @ W3)
```

## 3.8 Experimental Results

I generated 500 samples {**x**[*k*]} of 10-dimensional (i.e., *n*=10) Gaussian data with mean zero and covariance, given below. The covariance matrix is obtained from the first covariance matrix in [Okada and Tomita 85] multiplied by 3\. The covariance matrix is

![](images/522202_1_En_3_Chapter/522202_1_En_3_Figa_HTML.png)

The eigenvalues of the covariance matrix are

[17.699, 8.347, 5.126, 3.088, 1.181, 0.882, 0.261, 0.213, 0.182, 0.151].

### Experiments for Adaptive Square Root Algorithms

I used adaptive algorithms ([3.5](#522202_1_En_3_Chapter.xhtml#Par27)), ([3.8](#522202_1_En_3_Chapter.xhtml#Par35)), and ([3.9](#522202_1_En_3_Chapter.xhtml#Par39)) for Methods 1, 2, and 3, respectively, to compute the square root *A*, where *A* is the correlation matrix computed from all collected samples as

![$$ A=\frac{1}{500}\sum \limits_{i=1}^{500}{\mathbf{x}}_i{\mathbf{x}}_i^T. $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_Equb.png)

I started the algorithms with *W*[0]=*I* (10X10 identity matrix). At *k*^(*th*) update of each algorithm, I computed the Frobenius norm [Frobenius norm, Wikipedia] of the error between the actual correlation matrix *A* and the square of *W*[*k*] that is appropriate for each method. I denote this error by *e*[*k*] as follows:

![$$ {e}_k^{\mathrm{Method}1}={\left\Vert A-{W}_k^T{W}_k\right\Vert}_F $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq15.png),     (3.17)

![$$ {e}_k^{\mathrm{Method}2}={\left\Vert A-{W}_k{W}_k^T\right\Vert}_F $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq16.png),     (3.18)

![$$ {e}_k^{\mathrm{Method}3\hbox{-} 1}={\left\Vert A-{W}_k^2\right\Vert}_F $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq17.png),     (3.19)

![$$ {e}_k^{\mathrm{Method}3\hbox{-} 2}={\left\Vert {\varPhi \varLambda}^{\frac{1}{2}}{\Phi}^T-{W}_k\right\Vert}_F $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq18.png).     (3.20)

For each algorithm, I generated *A*[*k*] from **x**[*k*] by (2.5) with *β*=1\. In ([3.9](#522202_1_En_3_Chapter.xhtml#Par39)) I computed the error between the unique positive definite solution of *A*^½ and its estimate *W*[*k*]. Figure [3-4](#522202_1_En_3_Chapter.xhtml#Fig4) shows the convergence plots for all three methods by plotting the four errors *e*[*k*] against *k*. The final values of *e*[*k*] after 500 samples were *e*[500] = 0.451 for Methods 1 and 2, *e*[500] = 0.720 for Method 3-1 ([3.19](#522202_1_En_3_Chapter.xhtml#Par68)), and *e*[500] = 0.250 for Method 3-2 ([3.20](#522202_1_En_3_Chapter.xhtml#Par69)).

![](images/522202_1_En_3_Chapter/522202_1_En_3_Fig4_HTML.png)

Figure 3-4

Convergence of the A^½ algorithms ([3.5](#522202_1_En_3_Chapter.xhtml#Par27)), ([3.8](#522202_1_En_3_Chapter.xhtml#Par35)), and ([3.9](#522202_1_En_3_Chapter.xhtml#Par39))

It is clear from Figure [3-4](#522202_1_En_3_Chapter.xhtml#Fig4) that the errors are all close to zero. The small differences compared to the actual values are due to random fluctuations in the elements of *W*[*k*] caused by the varying input data.

### Experiments for Adaptive Inverse Square Root Algorithms

I used adaptive algorithms ([3.12](#522202_1_En_3_Chapter.xhtml#Par47)), ([3.15](#522202_1_En_3_Chapter.xhtml#Par54)), and ([3.16](#522202_1_En_3_Chapter.xhtml#Par58)) for Methods 1, 2, and 3, respectively, to compute the inverse square root *A*. I started the algorithms with *W*[0]=*I* (10X10 identity matrix). At *k*^(*th*) update of each algorithm, I computed the Frobenius norm of the error between the actual correlation matrix *A* and the inverse square of *W*[*k*] that is appropriate for each method. I denoted this error by *e*[*k*] as shown:

![$$ {e}_k^{\mathrm{Method}1}={\left\Vert I-{W}_k^T{AW}_k\right\Vert}_F $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq19.png),     (3.21)

![$$ {e}_k^{\mathrm{Method}2}={\left\Vert I-{W}_k{AW}_k^T\right\Vert}_F $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq20.png),     (3.22)

![$$ {e}_k^{\mathrm{Method}3\hbox{-} 1}\kern0.5em ={\left\Vert I-{W}_k{AW}_k\right\Vert}_F $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq21.png),     (3.23)

![$$ {e}_k^{\mathrm{Method}3\hbox{-} 2}={\left\Vert {\varPhi \varLambda}^{-\frac{1}{2}}{\Phi}^T-{W}_k\right\Vert}_F $$](images/522202_1_En_3_Chapter/522202_1_En_3_Chapter_TeX_IEq22.png),     (3.24)

In ([3.24](#522202_1_En_3_Chapter.xhtml#Par76)) I computed the error between the unique positive definite solution of *A*^(–½) and its estimate *W*[*k*]. Figure [3-5](#522202_1_En_3_Chapter.xhtml#Fig5) shows the convergence plot for all three methods by plotting the four errors *e*[*k*] against *k*. The final values of *e*[*k*] after 500 samples were *e*[500] = 0.419 for Methods 1 and 2, *e*[500] = 0.630 for Method 3-1 ([3.23](#522202_1_En_3_Chapter.xhtml#Par75)), and *e*[500] = 0.823 for Method 3-2 ([3.24](#522202_1_En_3_Chapter.xhtml#Par76)).

![](images/522202_1_En_3_Chapter/522202_1_En_3_Fig5_HTML.png)

Figure 3-5

Convergence of the A^(–½) algorithms ([3.12](#522202_1_En_3_Chapter.xhtml#Par47)), ([3.15](#522202_1_En_3_Chapter.xhtml#Par54)), and ([3.16](#522202_1_En_3_Chapter.xhtml#Par58))

It is clear from Figure [3-5](#522202_1_En_3_Chapter.xhtml#Fig5) that the errors are all close to zero. As before, experiments with higher epochs show an improvement in the estimation accuracy.

## 3.9 Concluding Remarks

I presented six adaptive algorithms for the computation of the square root and inverse square root of the correlation matrix of a random vector sequence. In four cases, I presented an objective function and, in all cases, I discussed the convergence properties of the algorithms. Note that although I applied the gradient descent technique on these objective functions, I could have applied any other technique of nonlinear optimization such as steepest descent, conjugate direction, Newton-Raphson, or recursive least squares. The availability of the objective functions allows us to derive new algorithms by using new optimization techniques on them, and also to perform convergence analyses of the adaptive algorithms.

Footnotes [1](#522202_1_En_3_Chapter.xhtml#Fn1_source)  

# 4. First Principal Eigenvector

## 4.1 Introduction and Use Cases

In this chapter, I present a unified framework to derive and discuss ten adaptive algorithms (some well-known) for principal eigenvector computation, which is also known as principal component analysis (PCA) or the Karhunen-Loeve [Karhunen–Loève theorem, Wikipedia] transform. The first *principal eigenvector* of a symmetric positive definite matrix *A*∈ℜ^(*nXn*) is the eigenvector **ϕ**[1] corresponding to the largest eigenvalue λ[1] of *A*. Here *A***ϕ**[*i*]= λ[*i*]**ϕ**[*i*] for *i*=1,…,*n*, where λ[1]>λ[2]≥...≥λ[*n*]>0 are the *n* largest eigenvalues of *A* corresponding to eigenvectors **ϕ**[1],…,**ϕ**[*n*].

An important problem in machine learning is to extract the most significant feature that represents the variations in the multi-dimensional data. This reduces the multi-dimensional data into one dimension that can be easily modeled. However, in real-world applications, the data statistics change over time (non-stationary). Hence it is challenging to design a solution that adapts to changing data on a low-memory and low-computation edge device.

Figure [4-1](#522202_1_En_4_Chapter.xhtml#Fig1) shows an example of streaming 10-dimensional non-stationary data that abruptly changes statistical properties after 500 samples. The overlaid red curve shows the principal eigenvector estimated by the adaptive algorithm. The adaptive estimate of the principal eigenvector converges to its true value within 50 samples. As the data changes abruptly after 500 samples, it readapts to the changed data and converges back to its true value within 100 samples. All of this is achieved with low computation, low memory, and low latency.

![](images/522202_1_En_4_Chapter/522202_1_En_4_Fig1_HTML.png)

Figure 4-1

Rapid convergence of the first principal eigenvector is computed by an adaptive algorithm in spite of abrupt changes in data

Besides this example, there are several applications in machine learning, pattern analysis, signal processing, cellular communications, and automatic control [Haykin 94, Owsley 78, Pisarenko 73, Chatterjee et al*.* 97-99, Chen et al*.* 99, Diamantaras and Strintzis 97], where an online (i.e., real-time) solution of principal eigen-decomposition is desired. As discussed in Chapter [2](#522202_1_En_2_Chapter.xhtml), in these real-time situations, the underlying correlation matrix *A* is unknown. Instead, we have a sequence of random vectors {**x**[*k*]∈ℜ^(*n*)} from which we obtain an instantaneous matrix sequence {*A*[*k*]∈ℜ^(*nxn*)}, such that *A* = lim[*k→*∞]*E*[*A*[*k*]]. For every incoming sample **x**[*k*], we need to obtain the current estimate **w**[*k*] of the principal eigenvector **ϕ**[1], such that **w**[*k*] converges strongly to its true value **ϕ**[1].

A common method of computing the online estimate **w**[*k*] of **ϕ**[1] is to maximize the *Rayleigh quotient* (RQ) [Golub and VanLoan 83] criterion *J*(**w**[*k*];*A*[*k*]), where

![$$ J\left({\mathbf{w}}_k;{A}_k\right)=\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k} $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq1.png).     (4.1)

The signal **x**[*k*] can be compressed to a single value by projecting it onto **w**[*k*] as ![$$ {\mathbf{w}}_k^T{\mathbf{x}}_k $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq2.png).

The literature for PCA algorithms is very diverse and practitioners have approached the problem from a variety of backgrounds including signal processing, neural learning, and statistical pattern recognition. Within each discipline, adaptive PCA algorithms are derived from their own perspectives, which may include ad hoc methods. Since the approaches and solutions to PCA algorithms are distributed along disciplinary lines, a unified framework for deriving and analyzing these algorithms is necessary.

In this chapter, I offer a common framework for derivation, convergence, and rate analyses for the ten adaptive algorithms in four steps outlined in Section [1.​4](#522202_1_En_1_Chapter.xhtml#Sec19). For each algorithm, I present the results for each of these steps. The unified framework helps in conducting a comparative study of the ten algorithms. In the process, I offer fresh perspectives on known algorithms and present two new adaptive algorithms for PCA. For known algorithms, if results exist from prior implementations, I state them; otherwise, I provide the new results. For the new algorithms, I prove my results.

### Outline of This Chapter

In Section [4.2](#522202_1_En_4_Chapter.xhtml#Sec3), I list the adaptive PCA algorithms that I derive and discuss in this chapter. I also list the objective functions from which I derive these algorithms and the necessary assumptions. Section [4.3](#522202_1_En_4_Chapter.xhtml#Sec6) presents the Oja PCA algorithm and describes its convergence properties. In Section [4.4](#522202_1_En_4_Chapter.xhtml#Sec10), I analyze three algorithms based on the Rayleigh quotient criterion ([4.1](#522202_1_En_4_Chapter.xhtml#Par7)). In Section [4.5](#522202_1_En_4_Chapter.xhtml#Sec14), I discuss PCA algorithms based on the information theoretic criterion. Section [4.6](#522202_1_En_4_Chapter.xhtml#Sec19) describes the mean squared error objective function and algorithms. In Section [4.7](#522202_1_En_4_Chapter.xhtml#Sec24), I discuss penalty function-based algorithms. Sections [4.8](#522202_1_En_4_Chapter.xhtml#Sec29) and [4.9](#522202_1_En_4_Chapter.xhtml#Sec33) present new PCA algorithms based on the augmented Lagrangian criteria. Section [4.10](#522202_1_En_4_Chapter.xhtml#Sec38) presents the summary of convergence results. Section [4.11](#522202_1_En_4_Chapter.xhtml#Sec39) discusses the experimental results. Finally, section [4.12](#522202_1_En_4_Chapter.xhtml#Sec44) concludes the chapter.

## 4.2 Algorithms and Objective Functions

### Adaptive Algorithms

[Chatterjee *Neural Networks*, Vol. 18, No. 2, pp. 145-149, March 2005].

I have itemized the algorithms based on their inventors or on the objective functions from which they are derived. All algorithms are of the form

**w**[*k* + 1] = **w**[*k*] + *η*[*k*]*h*(**w**[*k*], *A*[*k*]),     (4.2)

where the function *h*(**w**[*k*],*A*[*k*]) follows certain continuity and regularity properties [Ljung 77,92], and *η*[*k*] is a decreasing gain sequence. The term *h*(**w**[*k*];*A*[*k*]) for various adaptive algorithms are

*   OJA: ![$$ {A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq3.png).

*   RQ: ![$$ \frac{1}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k\left(\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\right)\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq4.png).

*   OJAN: ![$$ {A}_k{\mathbf{w}}_k-{\mathbf{w}}_k\left(\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\right)={\mathbf{w}}_k^T{\mathbf{w}}_k\cdot RQ $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq5.png).

*   LUO: ![$$ {\mathbf{w}}_k^T{\mathbf{w}}_k\left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k\left(\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\right)\right)={\left({\mathbf{w}}_k^T{\mathbf{w}}_k\right)}^2\cdot RQ $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq6.png).

*   IT: ![$$ \frac{A_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}-{\mathbf{w}}_k=\frac{1}{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}\cdot OJA $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq7.png)

*   XU: ![$$ 2{A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-{A}_k{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{w}}_k= OJA-{A}_k{\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq8.png).

*   PF: ![$$ {A}_k{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq9.png).

*   OJA+: ![$$ {A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-{\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)= OJA-{\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq10.png)

*   AL1: ![$$ {A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq11.png).

*   AL2: ![$$ 2{A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-{A}_k{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq12.png).

Here IT denotes information theory, and AL denotes augmented Lagrangian. Although most of these algorithms are known, the new AL1 and AL2 algorithms are derived from an augmented Lagrangian objective function discussed later in this chapter.

### Objective Functions

Conforming to my proposed methodology in Chapter 2.2, all algorithms mentioned before are derived from objective functions. Some of these objective functions are

*   Objective function for the OJA algorithm,

*   Least mean squared error criterion,

*   Rayleigh quotient criterion,

*   Penalty function method,

*   Information theory criterion, and

*   Augmented Lagrangian method.

## 4.3 OJA Algorithm

This algorithm was given by Oja et al*.* [Oja 85, 89, 92]. Intuitively, the OJA algorithm is derived from the Rayleigh quotient criterion by representing it as a Lagrange function, which minimizes ![$$ -{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq13.png) under the constraint ![$$ {\mathbf{w}}_k^T{\mathbf{w}}_k=1 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq14.png).

### Objective Function

In terms of the data samples **x**[*k*], the objective function for the OJA algorithm can be written as

![$$ J\left({\mathbf{w}}_k;{\mathbf{x}}_k\right)=-{\left\Vert {\mathbf{x}}_k^T\left({\mathbf{x}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{x}}_k\right)\right\Vert}^2 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq15.png)     (4.3)

If we represent the data correlation matrix *A*[*k*] by its instantaneous value ![$$ {\mathbf{x}}_k{\mathbf{x}}_k^T $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq16.png), then ([4.3](#522202_1_En_4_Chapter.xhtml#Par36)) is equivalent to the following objective function:

![$$ J\left({\mathbf{w}}_k;{A}_k\right)=-{\left\Vert {A}_k^{\frac{1}{2}}\left(I-{\mathbf{w}}_k{\mathbf{w}}_k^T\right){A}_k^{\frac{1}{2}}\right\Vert}_F^2 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq17.png).     (4.4)

We see from ([4.4](#522202_1_En_4_Chapter.xhtml#Par38)) that the objective function *J*(**w**[*k*];**x**[*k*]) represents the difference between the sample **x**[*k*] and its transformation due to a matrix ![$$ {\mathbf{w}}_k{\mathbf{w}}_k^T $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq18.png). In neural networks, this transform is called *auto-association*^([²](#fn2)) [Haykin 94]. Figure [4-2](#522202_1_En_4_Chapter.xhtml#Fig2) shows a two-layer auto-associative network.

![](images/522202_1_En_4_Chapter/522202_1_En_4_Fig2_HTML.jpg)

Figure 4-2

Two-layer linear auto-associative neural network for the first principal eigenvector

### Adaptive Algorithm

The gradient of ([4.4](#522202_1_En_4_Chapter.xhtml#Par38)) with respect to **w**[*k*] is

![$$ {\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)=-4{A}_k\left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k\right). $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equa.png)

The adaptive gradient descent OJA algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k-{\eta}_k{A}_k^{-1}{\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k+{\eta}_k\left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq19.png),     (4.5)

where *η*[*k*] is a small decreasing constant.

The Python code for this algorithm with multidimensional data `X[nDim,nSamples]` is

```
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
w = 0.1 * np.ones(shape=(nDim,11)) # weight vectors of all algorithms
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# OJA Algorithm
v = w[:,0].reshape(nDim, 1)
v = v + (1/(100+cnt))*(A @ v - v @ (v.T @ A @ v))
w[:,0] = v.reshape(nDim)
```

### Rate of Convergence

The convergence time constant for the principal eigenvector **ϕ**[1] is 1/*λ*[1] and for the minor eigenvectors **ϕ**[*i*] is 1/(*λ*[1]–*λ*[*i*]) for *i*=2,…,*n*. The time constants are dependent on the eigen-structure of the data correlation matrix *A*.

## 4.4 RQ, OJAN, and LUO Algorithms

### Objective Function

These three algorithms are different derivations of the following Rayleigh quotient objective function:

![$$ J\left({\mathbf{w}}_k;{A}_k\right)=-\left(\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq20.png).     (4.6)

These algorithms were initially presented by Luo et al*.* [Luo et al*.* 97; Taleb et al*.* 99; Cirrincione et al*.* 00] and Oja et al*.* [Oja et al*.* 92]. Variations of the RQ algorithm have been presented by many practitioners [Chauvin 89; Sarkar et al*.* 89; Yang et al*.* 89; Fu and Dowling 95; Taleb et al*.* 99; Cirrincione et al*.* 00].

### Adaptive Algorithms

The gradient of ([4.7](#522202_1_En_4_Chapter.xhtml#Par52)) with respect to **w**[*k*] is

![$$ {\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)=\frac{-1}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\right). $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equb.png)

The adaptive gradient descent RQ algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k-{\eta}_k{\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k+{\eta}_k\frac{1}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq21.png).     (4.7)

The adaptive gradient descent OJAN algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k-{\eta}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k\right){\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k+{\eta}_k\left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq22.png).     (4.8)

The adaptive gradient descent LUO algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k-{\eta}_k{\left({\mathbf{w}}_k^T{\mathbf{w}}_k\right)}^2{\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k+{\eta}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k\right)\kern0.5em \left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k\frac{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{\mathbf{w}}_k}\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq23.png).     (4.9)

The Python code for these algorithms with multidimensional data `X[nDim,nSamples]` is

```
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
w = 0.1 * np.ones(shape=(nDim,11)) # weight vectors of all algorithms
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# OJAN Algorithm
v = w[:,1].reshape(nDim, 1)
v = v + (1/(10+cnt))*(A @ v - v @ ((v.T @ A @ v) / (v.T @ v)) )
w[:,1] = v.reshape(nDim)
# LUO Algorithm
v = w[:,2].reshape(nDim, 1)
v = v + (1/(20+cnt))*(A @ v * (v.T @ v) - v @ (v.T @ A @ v))
w[:,2] = v.reshape(nDim)
# RQ Algorithm
v = w[:,3].reshape(nDim, 1)
v = v + (1/(100+cnt))*(A @ v - v @ ((v.T @ A @ v) / (v.T @ v)) )
w[:,3] = v.reshape(nDim)
```

### Rate of Convergence

The convergence time constants for *principal eigenvector* **ϕ**[1] are

RQ: ‖**w**[0]‖²/*λ*[1].

OJAN: 1/*λ*[1].

LUO: ‖**w**[0]‖^(–2)/*λ*[1].

The convergence time constants for the minor eigenvectors **ϕ**[*i*] (*i*=2,…,*n*) are

RQ: ‖**w**[0]‖²/(*λ*[1]–*λ*[*i*]) for *i*=2,…,*n*.

OJAN: 1/(*λ*[1]–*λ*[*i*]) for *i*=2,…,*n*.

LUO: ‖**w**[0]‖^(–2)/(*λ*[1]–*λ*[*i*]) for *i*=2,…,*n*.

The time constants are dependent on the eigen-structure of *A*.

## 4.5 IT Algorithm

### Objective Function

The objective function for the information theory (IT) criterion is

![$$ J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k^T{\mathbf{w}}_k-\ln \left({\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq24.png).     (4.10)

Plumbley [Pumbley 95] and Miao and Hua [Miao and Hua 98] have studied this objective function.

### Adaptive Algorithm

The gradient of ([4.12](#522202_1_En_4_Chapter.xhtml#Par77)) with respect to **w**[*k*] is

![$$ {\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k-\frac{A_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}. $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equc.png)

The adaptive gradient descent IT algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k-{\eta}_k{\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k+{\eta}_k\left(\frac{A_k{\mathbf{w}}_k}{{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k}-{\mathbf{w}}_k\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq25.png).     (4.11)

The Python code for this algorithm with multidimensional data `X[nDim,nSamples]` is

```
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
w = 0.1 * np.ones(shape=(nDim,11)) # weight vectors of all algorithms
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# IT Algorithm
v = w[:,5].reshape(nDim, 1)
v = v + (4/(1+cnt))*((A @ v / (v.T @ A @ v)) - v)
w[:,5] = v.reshape(nDim)
```

### Rate of Convergence

A unique feature of this algorithm is that the time constant for ‖**w**(*t*)‖ is 1 and it is *independent* of the eigen-structure of *A*.

### Upper Bound of η[k]

I have proven that there exists a uniform upper bound for η[k] such that **w**[k] is uniformly bounded. Furthermore, if ‖**w**[*k*]‖² ≤ α+1, then ‖**w**[*k*+1]‖² ≤ ‖**w**[*k*]‖² if

![$$ {\eta}_k&lt;\frac{2\left(\alpha +1\right)}{\alpha }. $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equd.png)

## 4.6 XU Algorithm

### Objective Function

Originally presented by Xu [Xu 91, 93], the objective function for XU algorithm is

![$$ J\left({\mathbf{w}}_k;{A}_k\right)=-{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k+{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k\left({\mathbf{w}}_k^T\mathbf{w}-1\right)=-2{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k+{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{w}}_k $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq26.png)     (4.12)

The objective function *J*(**w**[*k*];*A*[*k*]) represents the mean squared error between the sample **x**[*k*] and its transformation due to a matrix ![$$ {\mathbf{w}}_k{\mathbf{w}}_k^T $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq27.png). This transform, also known as *auto-association*, is shown in Figure [4-1](#522202_1_En_4_Chapter.xhtml#Fig1). We define ![$$ {A}_k=\left(1/k\right){\sum}_{t=1}^k{\mathbf{x}}_t{\mathbf{x}}_t^T $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq28.png). Then, the mean squared error objective function is

![$$ J\left({\mathbf{w}}_k;{A}_k\right)=\frac{1}{k}\sum \limits_{i=1}^k{\left\Vert {\mathbf{x}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{x}}_k\right\Vert}^2={trA}_k-2{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k+{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{w}}_k, $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Eque.png)

which is the same as ([4.12](#522202_1_En_4_Chapter.xhtml#Par77)).

### Adaptive Algorithm

The gradient of ([4.12](#522202_1_En_4_Chapter.xhtml#Par77)) with respect to **w**[*k*] is

![$$ {\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)=-\left(2{A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-{A}_k{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{w}}_k\right). $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equf.png)

The adaptive gradient descent XU algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k-{\eta}_k{\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k+{\eta}_k\left(2{A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-{A}_k{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{w}}_k\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq29.png).     (4.13)

The Python code for this algorithm with multidimensional data `X[nDim,nSamples]` is

```
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
w = 0.1 * np.ones(shape=(nDim,11)) # weight vectors of all algorithms
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# XU Algorithm
v = w[:,6].reshape(nDim, 1)
v = v + (1/(50+cnt))*(2*A@ v - v@(v.T @ A @ v) - A@ v@ (v.T @ v))
w[:,6] = v.reshape(nDim)
```

### Rate of Convergence

The convergence time constant for the principal eigenvector **ϕ**[1] is 1/*λ*[1] and for the minor eigenvectors **ϕ**[*i*] is 1/(*λ*[1]–*λ*[*i*]) for *i*=2,…,*n*. The time constants are dependent on the eigen-structure of the data correlation matrix *A*.

### Upper Bound of η[k]

There exists a uniform upper bound for η[*k*] such that **w**[*k*] is uniformly bounded w.p.1\. If ‖**w**[k]‖² ≤ α+1 and *θ* is the largest eigenvalue of *A*[*k*], then ‖**w**[*k*+1]‖² ≤ ‖**w**[*k*]‖² if

![$$ {\eta}_k&lt;\frac{1}{\theta \alpha}. $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equg.png)

## 4.7 Penalty Function Algorithm

### Objective Function

Originally given by Chauvin [Chauvin 89], the objective function for the penalty function (PF) algorithm is

![$$ J\left({\mathbf{w}}_k;{A}_k\right)=-{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k+\frac{\mu }{2}{\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)}^2 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq30.png), *μ* > 0.     (4.14)

The objective function *J*(**w**[*k*];*A*[*k*]) is an implementation of the Rayleigh quotient criterion ([4.1](#522202_1_En_4_Chapter.xhtml#Par7)), where the constraint ![$$ {\mathbf{w}}_k^T{\mathbf{w}}_k=1 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq31.png) is enforced by the penalty function method of nonlinear optimization, and *μ* is a positive penalty constant.

### Adaptive Algorithm

The gradient of ([4.14](#522202_1_En_4_Chapter.xhtml#Par87)) with respect to **w**[*k*] is

![$$ \left(1/2\right){\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)=-\left({A}_k{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)\right). $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equh.png)

The adaptive gradient descent PF algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k-{\eta}_k{\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)={\mathbf{w}}_k+{\eta}_k\left({A}_k{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq32.png),     (4.15)

where *μ* > 0.

The Python code for this algorithm with multidimensional data `X[nDim,nSamples]` is

```
mu = 10
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
w = 0.1 * np.ones(shape=(nDim,11)) # weight vectors of all algorithms
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# PF Algorithm
v = w[:,7].reshape(nDim, 1)
v = v + (1/(50+cnt)) * (A @ v - mu * v @ (v.T @ v - 1))
w[:,7] = v.reshape(nDim)
```

### Rate of Convergence

The convergence time constant for the principal eigenvector **ϕ**[1] is 1/(*λ*[1] + *μ*) and for the minor eigenvectors **ϕ**[*i*] is 1/(*λ*[1]–*λ*[*i*]) for *i*=2,…,*n*. The time constants are dependent on the eigen-structure of the data correlation matrix *A*.

### Upper Bound of η[k]

Then there exists a uniform upper bound for η[k] such that **w**[k] is uniformly bounded. If ‖**w**[k]‖² ≤ α+1 and *θ* is the largest eigenvalue of *A*[k], then ‖**w**[k+1]‖² ≤ ‖**w**[k]‖² if

![$$ {\eta}_k&lt;\frac{1}{\mu \alpha -\theta } $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq33.png), assuming μα>θ.

## 4.8 Augmented Lagrangian 1 Algorithm

### Objective Function and Adaptive Algorithm

The objective function for the augmented Lagrangian 1 (AL1) algorithm is obtained by applying the augmented Lagrangian method of nonlinear optimization to minimize ![$$ -{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq34.png) under the constraint ![$$ {\mathbf{w}}_k^T{\mathbf{w}}_k=1 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq35.png):

![$$ J\left({\mathbf{w}}_k;{A}_k\right)=-{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k+{\alpha}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)+\frac{\mu }{2}{\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)}^2 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq36.png),     (4.16)

where *α*[*k*] is a Lagrange multiplier and *μ* is a positive penalty constant. The gradient of *J*(**w**[*k*];*A*[*k*]) with respect to **w**[*k*] is

![$$ {\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)=-2\left({A}_k{\mathbf{w}}_k-{\alpha}_k{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)\right). $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equi.png)

By equating the gradient to **0** and using the constraint ![$$ {\mathbf{w}}_k^T{\mathbf{w}}_k=1 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq37.png), we obtain *α*[*k*]=**w**[*k*]^(*T*)*A*[*k*]**w**[*k*]. Replacing this *α*[*k*] in the gradient, we obtain the AL1 algorithm

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k+{\eta}_k\left({A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq38.png),     (4.17)

where *μ* > 0\. Note that ([4.17](#522202_1_En_4_Chapter.xhtml#Par101)) is the same as OJA+ algorithm for *μ* =1.

The Python code for this algorithm with multidimensional data `X[nDim,nSamples]` is

```
mu = 10
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
w = 0.1 * np.ones(shape=(nDim,11)) # weight vectors of all algorithms
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# AL1 Algorithm
v = w[:,8].reshape(nDim, 1)
v = v + (1/(50+cnt))*(A@v - v@(v.T @A @ v) - mu* v@ (v.T@v - 1))
w[:,8] = v.reshape(nDim)
```

### Rate of Convergence

The convergence time constant for the principal eigenvector **ϕ**[1] is 1/(*λ*[1] + *μ*) and for the minor eigenvectors **ϕ**[*i*] is 1/(*λ*[1]–*λ*[*i*]) for *i*=2,…,*n*. The time constants are dependent on the eigen-structure of the data correlation matrix *A*.

### Upper Bound of η[k]

There exists a uniform upper bound for η[k] such that **w**[k] is uniformly bounded. If ‖**w**[k]‖² ≤ α+1 and *θ* is the largest eigenvalue of A[k], then ‖**w**[k+1]‖ ² ≤ ‖**w**[k]‖² if

![$$ {\eta}_k&lt;\frac{1}{\left(\mu +\theta \right)\alpha }. $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equj.png)

## 4.9 Augmented Lagrangian 2 Algorithm

### Objective Function

The objective function for the augmented Lagrangian 2 (AL2) algorithm is

![$$ J\left({\mathbf{w}}_k;{A}_k\right)=-{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k+{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)+\frac{\mu }{2}{\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)}^2 $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq39.png)     (4.18)

The objective function *J*(**w**[*k*];*A*[*k*]) is an application of the augmented Lagrangian method on the Rayleigh quotient criterion ([4.1](#522202_1_En_4_Chapter.xhtml#Par7)). It uses the XU objective function and also uses the penalty function method ([4.14](#522202_1_En_4_Chapter.xhtml#Par87)), where *μ* is a positive penalty constant.

### Adaptive Algorithm

The gradient of ([4.18](#522202_1_En_4_Chapter.xhtml#Par107)) with respect to **w**[*k*] is

![$$ \left(1/2\right){\nabla}_{{\mathbf{w}}_k}J\left({\mathbf{w}}_k;{A}_k\right)=-\left(2{A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-{A}_k{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)\right). $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equk.png)

The adaptive gradient descent AL2 algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}={\mathbf{w}}_k+{\eta}_k\left(2{A}_k{\mathbf{w}}_k-{\mathbf{w}}_k{\mathbf{w}}_k^T{A}_k{\mathbf{w}}_k-{A}_k{\mathbf{w}}_k{\mathbf{w}}_k^T{\mathbf{w}}_k-\mu {\mathbf{w}}_k\left({\mathbf{w}}_k^T{\mathbf{w}}_k-1\right)\right) $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq40.png),    (4.19)

where *μ* > 0.

The Python code for this algorithm with multidimensional data `X[nDim,nSamples]` is

```
mu = 10
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
w = 0.1 * np.ones(shape=(nDim,11)) # weight vectors of all algorithms
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# AL2 Algorithm
v = w[:,9].reshape(nDim, 1)
v = v + (1/(50+cnt))*(2* A @ v - v @ (v.T @ A @ v) –
A@ v @ (v.T @ v) - mu* v @ (v.T @ v -1))
w[:,9] = v.reshape(nDim)
```

### Rate of Convergence

The convergence time constant for the principal eigenvector **ϕ**[1] is 1/(*λ*[1] + (*μ*/2)) and for the minor eigenvectors **ϕ**[*i*] is 1/(*λ*[1]–*λ*[*i*]) for *i*=2,…,*n*. The time constants are dependent on the eigen-structure of the data correlation matrix *A*.

### Upper Bound of η[k]

There exists a uniform upper bound for η[k] such that **w**[*k*] is uniformly bounded. Furthermore, if ‖**w**[k]‖² ≤ α+1 and *θ* is the largest eigenvalue of *A*[*k*], then ‖**w**[k+1]‖² ≤ ‖**w**[k]‖² if

![$$ {\eta}_k&lt;\frac{2}{\left(2\theta +\mu \right)\alpha }. $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equl.png)

## 4.10 Summary of Algorithms

Table [4-1](#522202_1_En_4_Chapter.xhtml#Tab1) summarizes the convergence results of the algorithms. It also shows the upper bounds of *η*[*k*], when available. Here *τ* denotes the time constant, **w**[0] denotes the initial value of **w**[*k*], α+1 the upper bound of ‖**w**[*k*]‖² (i.e., ‖**w**[*k*]‖² ≤ α+1), and *θ* denotes the first principal eigenvalue of *A*[*k*].

Table 4-1

Summary of Convergence Results

| Algorithm | Convergence Time Constants | Upper Bounds of *η*[*k*] |
| --- | --- | --- |
| OJA | 1/*λ*[1] | 2/*αθ* |
| OJAN | 1/*λ*[1] | Not Available |
| LUO | ‖**w**[0]‖^(–2)/*λ*[1] | Not Available |
| RQ | ‖**w**[0]‖²/*λ*[1] | Not Available |
| OJA+ | 1/(*λ*[1]+1) | 1/(*α*–*θ*) |
| IT | 1 | 2(*α*+1)/*α* |
| XU | 1/*λ*[1] | 1/*αθ* |
| PF | 1/(*λ*[1]+*μ*) | 1/(*μα*–*θ*) |
| AL1 | 1/(*λ*[1]+*μ*) | 1/(*μ*+*θ*)*α* |
| AL2 | 1/(*λ*[1]+(*μ*/2)) | 2/(*μ*+2*θ*)*α* |

Note that a smaller time constant yields faster convergence. The conclusions are

1.  For all algorithms, except IT, convergence of **ϕ**[1] improves for larger values of *λ*[1].

2.  For LUO, the time constant decreases for larger ‖**w**[0]‖, which implies that convergence improves for larger initial weights.

3.  For RQ, convergence deteriorates for larger ‖**w**[0]‖.

4.  For the PF, AL1, and AL2 algorithms, the time constant decreases for larger *μ*, although very large values of *μ* will make the algorithm perform poorly due excessive emphasis on the constraints.

## 4.11 Experimental Results

I did three sets of experiments.

1.  In the first experiment, I used the adaptive algorithms described before on a single data set with various starting vectors **w**[0] [Chatterjee, *Neural Networks*, Vol. 18, No. 2, pp. 145-149, March 2005].

2.  In the second experiment, I generated several data samples and used the adaptive algorithms with the same starting vector **w**[0] [Chatterjee, *Neural Networks*, Vol. 18, No. 2, pp. 145-149, March 2005].

3.  In the third experiment, I used a **real-world non-stationary** data set from a public dataset [V. Souza et al*.* 2020] to demonstrate the fast convergence of the adaptive algorithms to the first principal eigenvector of the ensemble correlation matrix.

### Experiments with Various Starting Vectors *w*[*0*]

[Chatterjee, *Neural Networks*, Vol. 18, No. 2, pp. 145-149, March 2005].

I generated 1,000 samples of 10-dimensional Gaussian data (i.e., *n*=10) with the mean zero and covariance given below. The covariance matrix is obtained from the second covariance matrix in [Okada and Tomita 85] multiplied by 3, which is

![](images/522202_1_En_4_Chapter/522202_1_En_4_Figa_HTML.gif)     (4.20)

The eigenvalues of the covariance matrix are

17.9013, 10.2212, 8.6078, 6.5361, 2.2396, 1.8369, 1.1361, 0.7693, 0.2245, 0.1503.

I computed the principal eigenvector (i.e., the eigenvector corresponding to the largest eigenvalue = 17.9013) by the adaptive algorithms described before from different starting vectors **w**[0]. I obtained **w**[0]=*c****r**, where **r∈ℜ**^(10) is a *N*(0,1) random vector and *c*∈[0.05,2.0]. This causes a variation in ‖**w**[0]‖ from 0.1 to 5.0.

In order to compute the online data sequence {*A*[*k*]}, I generated random data vectors {**x**[*k*]} from the covariance matrix ([4.20](#522202_1_En_4_Chapter.xhtml#Par128)). I generated {*A*[*k*]} from {**x**[*k*]} by using the algorithm (2.5) with *β*=1\. I compute the correlation matrix *A* after collecting all 500 samples **x**[*k*] as

![$$ A=\frac{1}{500}\sum \limits_{i=1}^{500}{\mathbf{x}}_i{\mathbf{x}}_i^T. $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_Equm.png)

I refer to the eigenvectors and eigenvalues computed from this *A* by a standard numerical analysis method [Golub and VanLoan 83] as the *actual values* .

In order to measure the convergence and accuracy of the algorithms, I computed the percentage direction cosine at *k*^(*th*) update of each adaptive algorithm as

Percentage Direction Cosine (*k*) = ![$$ \frac{100\left|{\mathbf{w}}_k^T{\boldsymbol{\upvarphi}}_1\right|}{\left\Vert {\mathbf{w}}_k\right\Vert } $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq41.png),     (4.21)

where **w**[*k*] is the estimated first principal eigenvector of *A*[*k*] at *k*^(*th*) update and **ϕ**[1] is the actual first principal eigenvector computed from all collected samples by a conventional numerical analysis method. For all algorithms, I used *η*[*k*]=1/(200+*k*). For the PF, AL1, and AL2 algorithms, I used *μ*=10\. The results are summarized in Table [4-2](#522202_1_En_4_Chapter.xhtml#Tab2). I reported the percentage direction cosines after sample values *k*=*N*/2 and *N* (i.e., *k*=250 and 500) for each algorithm.

Table 4-2

Convergence of the Principal Eigenvector of A by Adaptive Algorithms at Sample Values k={250,500} for Different Initial Values **w**[0]

| ‖**w**[0]‖ | *k* | OJA | OJAN | LUO | RQ | OJA+ | IT | XU | PF | AL1 | AL2 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0.1355 | 250 | 97.18 | 97.18 | 60.78 | 98.44 | 97.18 | 84.53 | 97.22 | 97.17 | 97.18 | 97.20 |
| 500 | 99.58 | 99.58 | 63.15 | 99.96 | 99.58 | 89.67 | 99.58 | 99.58 | 99.58 | 99.58 |
| 0.4065 | 250 | 97.18 | 97.18 | 82.44 | 98.54 | 97.18 | 84.96 | 97.18 | 97.18 | 97.18 | 97.16 |
| 500 | 99.58 | 99.58 | 90.88 | 99.96 | 99.58 | 90.35 | 99.58 | 99.58 | 99.58 | 99.58 |
| 0.6776 | 250 | 97.18 | 97.18 | 94.63 | 97.85 | 97.18 | 82.55 | 97.17 | 97.18 | 97.18 | 97.15 |
| 500 | 99.58 | 99.58 | 98.50 | 99.88 | 99.58 | 88.85 | 99.58 | 99.58 | 99.58 | 99.58 |
| 0.9486 | 250 | 97.18 | 97.18 | 97.05 | 97.28 | 97.18 | 79.60 | 97.18 | 97.18 | 97.18 | 97.17 |
| 500 | 99.58 | 99.58 | 99.52 | 99.63 | 99.58 | 86.90 | 99.58 | 99.58 | 99.58 | 99.58 |
| 1.2196 | 250 | 97.18 | 97.18 | 97.60 | 96.35 | 97.18 | 76.67 | 97.21 | 97.18 | 97.18 | 97.21 |
| 500 | 99.58 | 99.58 | 99.80 | 99.19 | 99.58 | 84.80 | 99.58 | 99.58 | 99.58 | 99.58 |
| 1.4906 | 250 | 97.18 | 97.18 | 97.97 | 94.43 | 97.18 | 73.99 | 97.26 | 97.18 | 97.17 | 97.27 |
| 500 | 99.58 | 99.58 | 99.90 | 98.41 | 99.58 | 82.68 | 99.59 | 99.58 | 99.58 | 99.59 |
| 1.7617 | 250 | 97.17 | 97.18 | 98.31 | 91.53 | 97.18 | 71.63 | 97.33 | 97.18 | 97.16 | 97.35 |
| 500 | 99.58 | 99.58 | 99.95 | 97.08 | 99.58 | 80.61 | 99.59 | 99.58 | 99.58 | 99.59 |
| 2.0327 | 250 | 97.17 | 97.18 | 98.57 | 88.04 | 97.17 | 69.61 | 97.44 | 97.17 | 97.15 | 97.51 |
| 500 | 99.58 | 99.58 | 99.96 | 95.08 | 99.58 | 78.63 | 99.60 | 99.58 | 99.58 | 99.60 |
| 2.3037 | 250 | 97.17 | 97.18 | 98.75 | 84.43 | 97.17 | 67.90 | 97.62 | 97.17 | 97.14 | 97.89 |
| 500 | 99.58 | 99.58 | 99.97 | 92.51 | 99.58 | 76.78 | 99.61 | 99.58 | 99.58 | 99.63 |
| 2.5748 | 250 | 97.16 | 97.18 | 98.89 | 81.00 | 97.16 | 66.46 | 97.96 | 97.16 | 97.11 | 98.55 |
| 500 | 99.58 | 99.58 | 99.98 | 89.59 | 99.58 | 75.07 | 99.63 | 99.58 | 99.58 | 99.77 |
| 2.8458 | 250 | 97.15 | 97.18 | 99.00 | 77.92 | 97.16 | 65.26 | 98.64 | 97.15 | 97.06 | 94.08 |
| 500 | 99.58 | 99.58 | 99.99 | 86.56 | 99.58 | 73.50 | 99.70 | 99.58 | 99.57 | 99.42 |
| 3.1168 | 250 | 97.14 | 97.18 | 99.06 | 75.25 | 97.15 | 64.24 | 16.90 | 97.14 | 96.91 | 95.92 |
| 500 | 99.58 | 99.58 | 99.99 | 83.61 | 99.58 | 72.08 | 60.47 | 99.58 | 99.57 | 99.51 |

Table [4-2](#522202_1_En_4_Chapter.xhtml#Tab2) demonstrates

*   Convergence of all adaptive algorithms is similar except for the RQ and IT algorithms.

*   Other than IT, all algorithms converge with a time constant ∝ 1/*λ*[1].

*   For the IT algorithm, the time constant of the principal eigenvector is 1\. Since *λ*[1]=17.9, the convergence of all algorithms is faster than IT.

*   For the RQ and LUO algorithms, the time constants are ‖**w**[0]‖²/*λ*[1] and ‖**w**[0]‖^(–2)/*λ*[1,] respectively.

*   Clearly, for larger ‖**w**[0]‖, RQ converges at a slower rate than other algorithms and LUO converges faster than other algorithms whose time constants are 1/*λ*[1].

*   For very large ‖**w**[0]‖ such as ‖**w**[0]‖=10.0, the LUO algorithm fails to converge for *η*[*k*]=1/(200+*k*) because the convergence becomes unstable.

*   For smaller ‖**w**[0]‖, the convergence of RQ is better than other algorithms since its time constant ‖**w**[0]‖²/*λ*[1] is smaller than other algorithms whose time constants are 1/*λ*[1].

### Experiments with Various Data Sets: Set 1

[Chatterjee, *Neural Networks*, Vol. 18, No. 2, pp. 145-149, March 2005].

Here I used the covariance matrix ([4.20](#522202_1_En_4_Chapter.xhtml#Par128)) and added a symmetric matrix *c*R*, where *R* is a uniform (0,1) random symmetric matrix and *c*∈[0.05,2.0]. I generated 12 sets of 1,000 samples of 10-dimensional Gaussian data with mean zero and random covariance matrix described in ([4.20](#522202_1_En_4_Chapter.xhtml#Par128)). I chose the starting vector **w**[0]= 0.5*****r**, where **r∈ℜ**^(10) is a *N*(0,1) random vector. I used *η*[*k*]=1/(200+*k*), and for the PF, AL1, and AL2 algorithms, I chose *μ*=10\. I generated the percentage direction cosines ([4.21](#522202_1_En_4_Chapter.xhtml#Par135)) for all algorithms on each data set and reported the results in Table [4-3](#522202_1_En_4_Chapter.xhtml#Tab3). For each data set, I stated the largest 2 eigenvalues *λ*[1] and *λ*[2].

Table 4-3

Convergence of the Principal Eigenvector of A by Adaptive Algorithms at Sample Values k={250,500} for Different Data Sets

| *λ*[1], *λ*[2] | *k* | OJA | OJAN | LUO | RQ | OJA+ | IT | XU | PF | AL1 | AL2 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 11.58,   6.32 | 250 | 95.66 | 95.67 | 97.61 | 91.70 | 95.66 | 72.19 | 95.97 | 95.66 | 95.65 | 95.82 |
| 500 | 99.50 | 99.50 | 99.86 | 98.14 | 99.50 | 80.97 | 99.51 | 99.50 | 99.50 | 99.51 |
| 11.63,   6.49 | 250 | 95.50 | 95.54 | 96.93 | 91.65 | 95.51 | 70.57 | 96.34 | 95.51 | 95.46 | 96.18 |
| 500 | 99.51 | 99.51 | 99.87 | 98.28 | 99.51 | 80.39 | 99.54 | 99.51 | 99.51 | 99.54 |
| 11.73,   6.92 | 250 | 87.62 | 86.61 | 97.10 | 56.91 | 87.54 | 47.80 | 46.00 | 86.78 | 88.08 | 36.39 |
| 500 | 98.94 | 98.89 | 99.80 | 91.38 | 98.93 | 60.20 | 96.82 | 98.90 | 98.96 | 98.06 |
| 11.84,   7.18 | 250 | 96.72 | 96.71 | 96.53 | 95.83 | 96.72 | 73.84 | 96.54 | 96.72 | 96.73 | 96.60 |
| 500 | 99.30 | 99.30 | 99.75 | 98.69 | 99.30 | 82.76 | 99.28 | 99.30 | 99.30 | 99.29 |
| 12.14,   7.64 | 250 | 96.23 | 96.22 | 95.66 | 95.62 | 96.23 | 71.62 | 96.01 | 96.22 | 96.24 | 96.06 |
| 500 | 99.10 | 99.10 | 99.67 | 98.54 | 99.10 | 81.39 | 99.08 | 99.10 | 99.10 | 99.08 |
| 12.52,   8.08 | 250 | 95.10 | 95.13 | 95.63 | 94.45 | 95.10 | 60.71 | 95.78 | 95.10 | 95.06 | 95.67 |
| 500 | 98.91 | 98.91 | 99.62 | 99.23 | 98.91 | 73.59 | 98.96 | 98.91 | 98.91 | 98.95 |
| 12.87,   8.67 | 250 | 95.38 | 95.37 | 93.65 | 96.51 | 95.38 | 68.84 | 95.08 | 95.37 | 95.39 | 95.14 |
| 500 | 98.57 | 98.57 | 99.39 | 98.60 | 98.57 | 79.91 | 98.53 | 98.57 | 98.57 | 98.54 |
| 13.57,   9.33 | 250 | 94.83 | 94.82 | 92.88 | 97.00 | 94.82 | 64.05 | 94.56 | 94.82 | 94.84 | 94.60 |
| 500 | 98.35 | 98.35 | 99.30 | 98.66 | 98.35 | 76.67 | 98.32 | 98.35 | 98.35 | 98.32 |
| 14.09,   9.88 | 250 | 95.95 | 95.97 | 92.21 | 94.23 | 95.94 | 40.40 | 96.05 | 95.99 | 95.97 | 95.86 |
| 500 | 98.33 | 98.33 | 99.17 | 99.82 | 98.33 | 50.26 | 98.34 | 98.33 | 98.33 | 98.31 |
| 17.97,  11.66 | 250 | 67.18 | 72.82 | 94.87 | 58.69 | 67.39 | 2.78 | 88.60 | 74.24 | 66.40 | 86.49 |
| 500 | 98.20 | 98.45 | 99.83 | 90.90 | 98.21 | 1.22 | 99.05 | 98.50 | 98.16 | 98.97 |
| 21.54,  12.72 | 250 | 95.14 | 95.19 | 97.25 | 84.30 | 95.14 | 0.35 | 95.66 | 95.19 | 95.12 | 95.57 |
| 500 | 99.79 | 99.79 | 99.96 | 98.49 | 99.79 | 3.94 | 99.80 | 99.79 | 99.79 | 99.79 |
| 25.66,  12.92 | 250 | 98.23 | 98.26 | 99.04 | 93.90 | 98.23 | 2.53 | 98.42 | 98.26 | 98.23 | 98.37 |
| 500 | 99.96 | 99.96 | 99.99 | 99.79 | 99.96 | 7.62 | 99.96 | 99.96 | 99.96 | 99.96 |

Once again, we observe that all algorithms converge in a similar manner except for the RQ and IT algorithms. Out of these two, RQ converges much better than IT, where IT fails to converge for some data sets. Of the remaining algorithms, LUO converges better than the rest for all data sets.

### Experiments with Various Data Sets: Set 2

[Chatterjee, *Neural Networks*, Vol. 18, No. 2, pp. 145-149, March 2005].

I further generated 12 sets of 500 samples of 10-dimensional Gaussian data with a mean zero and random covariance matrix ([4.20](#522202_1_En_4_Chapter.xhtml#Par128)). Here I computed the eigenvectors and eigenvalues of the covariance matrix ([4.20](#522202_1_En_4_Chapter.xhtml#Par128)). Next, I changed the first two principal eigenvalues of ([4.20](#522202_1_En_4_Chapter.xhtml#Par128)) to *λ*[1]=25 and *λ*[2]=*λ*[1]/*c*, where *c*∈[1.1,10.0], and generated the data sets with the new eigenvalues and eigenvectors computed before. For all adaptive algorithms I used **w**[0], *η*[*k*], and *μ* as described in Section [4.11.2](#522202_1_En_4_Chapter.xhtml#Sec41). See Table [4-4](#522202_1_En_4_Chapter.xhtml#Tab4).

Observe the following:

*   The convergences are similar to dataset 1.

*   The convergence improves for larger values of *k* and for larger ratios of *λ*[1]/*λ*[2] as supported by Table [4-1](#522202_1_En_4_Chapter.xhtml#Tab1) and the experimental results in Table [4-4](#522202_1_En_4_Chapter.xhtml#Tab4).

Table 4-4

Convergence of the Principal Eigenvector of A by Adaptive Algorithms at Sample Values k={50,100} for Different Data Sets with Varying λ[1]/λ[2]

| *λ*[1]/*λ*[2] | *k* | OJA | OJAN | LUO | RQ | OJA+ | IT | XU | PF | AL1 | AL2 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1.1 | 50 | 87.75 | 87.64 | 94.14 | 67.29 | 87.75 | 7.60 | 85.95 | 87.52 | 87.78 | 86.37 |
| 100 | 96.65 | 96.64 | 97.40 | 90.38 | 96.65 | 5.57 | 96.53 | 96.64 | 96.65 | 96.55 |
| 1.5 | 50 | 86.07 | 86.06 | 91.10 | 75.70 | 86.08 | 39.67 | 86.29 | 85.99 | 86.03 | 86.37 |
| 100 | 96.29 | 96.29 | 97.53 | 92.37 | 96.29 | 47.73 | 96.30 | 96.28 | 96.28 | 96.31 |
| 2.0 | 50 | 92.43 | 92.39 | 94.56 | 83.52 | 92.43 | 48.30 | 91.99 | 92.34 | 92.42 | 92.19 |
| 100 | 98.04 | 98.04 | 98.60 | 96.00 | 98.04 | 55.39 | 98.00 | 98.04 | 98.04 | 98.01 |
| 2.5 | 50 | 93.28 | 93.24 | 95.03 | 86.76 | 93.29 | 55.77 | 93.02 | 93.16 | 93.23 | 93.53 |
| 100 | 98.49 | 98.49 | 98.95 | 96.85 | 98.49 | 62.59 | 98.49 | 98.49 | 98.49 | 98.50 |
| 3.0 | 50 | 94.39 | 94.37 | 96.02 | 89.18 | 94.39 | 60.94 | 94.50 | 94.34 | 94.36 | 94.53 |
| 100 | 98.78 | 98.78 | 99.08 | 97.69 | 98.78 | 67.92 | 98.77 | 98.78 | 98.78 | 98.78 |
| 4.0 | 50 | 96.00 | 95.99 | 96.66 | 90.91 | 96.01 | 62.67 | 95.87 | 95.96 | 95.99 | 96.03 |
| 100 | 98.96 | 98.96 | 99.15 | 98.40 | 98.96 | 69.62 | 98.97 | 98.96 | 98.96 | 98.96 |
| 5.0 | 50 | 94.55 | 94.55 | 96.64 | 89.99 | 94.55 | 65.33 | 94.89 | 94.52 | 94.52 | 94.84 |
| 100 | 98.93 | 98.93 | 99.16 | 97.85 | 98.93 | 71.30 | 98.94 | 98.93 | 98.93 | 98.93 |
| 6.0 | 50 | 98.73 | 98.74 | 97.62 | 96.32 | 98.73 | 65.37 | 98.75 | 98.76 | 98.74 | 98.69 |
| 100 | 99.19 | 99.19 | 99.26 | 99.38 | 99.19 | 72.96 | 99.19 | 99.19 | 99.19 | 99.19 |
| 7.0 | 50 | 99.36 | 99.37 | 98.00 | 96.29 | 99.36 | 64.25 | 99.41 | 99.39 | 99.37 | 99.33 |
| 100 | 99.26 | 99.26 | 99.29 | 99.71 | 99.26 | 73.32 | 99.27 | 99.26 | 99.26 | 99.26 |
| 8.0 | 50 | 97.12 | 97.11 | 97.97 | 92.96 | 97.12 | 62.88 | 97.12 | 97.09 | 97.11 | 97.17 |
| 100 | 99.25 | 99.25 | 99.34 | 98.71 | 99.25 | 70.05 | 99.25 | 99.25 | 99.25 | 99.25 |
| 9.0 | 50 | 97.32 | 97.31 | 98.18 | 92.85 | 97.32 | 63.17 | 97.27 | 97.28 | 97.31 | 97.34 |
| 100 | 99.33 | 99.33 | 99.41 | 98.87 | 99.33 | 70.33 | 99.33 | 99.33 | 99.33 | 99.33 |
| 10.0 | 50 | 97.82 | 97.81 | 98.43 | 94.38 | 97.82 | 66.24 | 97.70 | 97.79 | 97.81 | 97.79 |
| 100 | 99.43 | 99.43 | 99.49 | 99.04 | 99.43 | 73.12 | 99.43 | 99.43 | 99.43 | 99.43 |

### Experiments with Real-World Non-Stationary Data

In these experiments I use real-world non-stationary data from Publicly Real-World Datasets to Evaluate Stream Learning Algorithms [Vinicius Souza *et al.* 2020], `INSECTS-incremental-abrupt_balanced_norm.arff`. It is important to demonstrate the performance of these algorithms on real non-stationary data since in practical edge applications the data is usually time varying and changes over time.

The data has 33 components and 80,000 samples. It contains periodic abrupt changes. Figure [4-3](#522202_1_En_4_Chapter.xhtml#Fig3) shows the components.

![](images/522202_1_En_4_Chapter/522202_1_En_4_Fig3_HTML.png)

Figure 4-3

Non-stationary real-world data with abrupt periodic changes

The first eight eigenvalues of the correlation matrix of all samples are

[18.704, 10.473, 8.994, 7.862, 7.276 6.636 5.565 4.894, …].

I used the adaptive algorithms discussed in this chapter and plotted the percentage direction cosine ([4.21](#522202_1_En_4_Chapter.xhtml#Par135)). Figure [4-4](#522202_1_En_4_Chapter.xhtml#Fig4) shows that all algorithms converged well in spite of the non-stationarity in the data.

![](images/522202_1_En_4_Chapter/522202_1_En_4_Fig4_HTML.png)

Figure 4-4

Convergence of the adaptive algorithms for first principal eigenvector on real-world non-stationary data (ideal value=100)

## 4.12 Concluding Remarks

The RQ, OJAN, and LUO algorithms require ‖**w**[0]‖=1 for the principal eigenvector **w**[*k*] to converge to unit norm (i.e., ‖**w**[*k*]‖→1). All other algorithms (except PF) converged to the unit norm for various non-zero values for **w**[0]. As per Theorem 4.8, the PF algorithm successfully converged to ![$$ \left\Vert {\mathbf{w}}_k\right\Vert \to \sqrt{1+{\lambda}_1/\mu } $$](images/522202_1_En_4_Chapter/522202_1_En_4_Chapter_TeX_IEq42.png).

The unified framework and the availability of the objective functions allows me to derive and analyze each algorithm on its convergence performance. The results for each algorithm are summarized in Table [4-5](#522202_1_En_4_Chapter.xhtml#Tab5). For each method, I computed the approximate MATLAB flop^([³](#fn3)) count for one iteration of the adaptive algorithm for *n*=10 and show them in Table [4-5](#522202_1_En_4_Chapter.xhtml#Tab5).

Table 4-5

Comparison of Different Adaptive Algorithms [Chatterjee, Neural Networks 2005]

| Alg. | Pros | Cons |
| --- | --- | --- |
| OJA | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Upper bound of *η*[*k*] can be determined.Fewer computations per iteration (Flops=460). | Convergence cannot be improved by larger ‖**w**[0]‖. |
| OJAN | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Fewer computations per iteration (Flops=481). | Upper bound of *η*[*k*] not available.Convergence cannot be improved by larger ‖**w**[0]‖.Require ‖**w**[0]‖=1 for **w**[*k*] to converge to unit norm. |
| LUO | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Convergence increases for larger ‖**w**[0]‖.Fewer computations per iteration (Flops=502). | Upper bound of *η*[*k*] not available.Convergence decreases for smaller ‖**w**[0]‖.Requires ‖**w**[0]‖=1 for **w**[*k*] to converge to unit norm. |
| RQ | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Convergence increases for smaller ‖**w**[0]‖.Fewer computations per iteration (Flops=503). | Upper bound of *η*[*k*] not available.Convergence decreases for larger v.Requires ‖**w**[0]‖=1 for **w**[*k*] to converge to unit norm. |
| OJA+ | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Upper bound of *η*[*k*] can be determined.Fewer computations per iteration (Flops=501). | Convergence cannot be improved by larger ‖**w**[0]‖. |
| IT | Upper bound of *η*[*k*] can be determined.Fewer computations per iteration (Flops=460). | Convergence independent of *λ*[1].Experimental results show poor convergence. |
| XU | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Upper bound of *η*[*k*] can be determined. | Convergence cannot be improved by larger ‖**w**[0]‖.More computations per iteration (Flops=800). |
| PF | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Convergence increases with *μ*.Upper bound of *η*[*k*] can be determined.Smallest computations per iteration (Flops=271). | Convergence cannot be improved by larger ‖**w**[0]‖.**w**[*k*] does not converge to unit norm. |
| AL1 | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Convergence increases with *μ*.Upper bound of *η*[*k*] can be determined.Fewer computations per iteration (Flops=511). | Convergence cannot be improved by larger ‖**w**[0]‖. |
| AL2 | Convergence increases with larger *λ*[1] and *λ*[1]/*λ*[2].Convergence increases with *μ*.Upper bound of *η*[*k*] can be determined. | Convergence cannot be improved by larger ‖**w**[0]‖.Largest computations per iteration (Flops=851). |

In summary, I discussed ten adaptive algorithms for PCA, some of them new, from a common framework with an objective function for each. Note that although I applied the gradient descent technique on these objective functions, I could have applied any other technique of nonlinear optimization such as steepest descent, conjugate direction, Newton-Raphson, or recursive least squares.

Footnotes [1](#522202_1_En_4_Chapter.xhtml#Fn1_source)   [2](#522202_1_En_4_Chapter.xhtml#Fn2_source)  

# 5. Principal and Minor Eigenvectors

## 5.1 Introduction and Use Cases

In Chapter [4](#522202_1_En_4_Chapter.xhtml), I discussed adaptive algorithms for the computation of the principal eigenvector of the online correlation matrix *A*[*k*]∈ℜ^(*n*X*n*). However, in some applications, it is not enough to just compute the principal eigenvector; we also need to compute the minor eigenvectors of *A*[*k*]. One such application is multi-dimensional data compression or data dimensionality reduction in multimedia video transmission [Le Gall 91]. For example, in still video compression by the JPEG technique, the image is divided into 8X8 blocks. This high-dimensional video data can be reduced to lower dimensions by projecting it onto the principal eigenvector subspace of its online correlation matrix. The process of data projection onto the eigenvector subspace by a linear transform is known as principal component analysis (PCA) and is closely related to the Karhunen-Loeve Transform (KLT) [Fukunaga 90]. We approximate the eigenvectors by fixed transform vectors given by the discrete cosine transform (DCT), which is the central compression method of the MPEG standard [Le Gall 91]. It can be shown that DCT is asymptotically equivalent to PCA for signals coming from a first-order Markov model, which is a reasonable model for digital images.

Figure [5-1](#522202_1_En_5_Chapter.xhtml#Fig1) shows the original 128-dimensional signal on the left. On the right, using adaptive algorithms, I reconstructed the signal with just 16 principal components with an 8x compression. Results show that the reconstructed data is near identical.

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig1_HTML.png)

Figure 5-1

Original signal on the left and reconstructed signal on the right after 8x compression with principal components

More details on this application and code are given in Chapter [8](#522202_1_En_8_Chapter.xhtml).

Figure [5-2](#522202_1_En_5_Chapter.xhtml#Fig2) shows the number 9 from the Keras MNIST dataset [Keras, MNIST]. The original data is 28x28=784 dimensional. I used the first 100 principal components and reconstructed the images. This is 7.8x reduction in data. The reconstructed images look similar to the original.

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig2_HTML.jpg)

Figure 5-2

Original MNIST number 9 on the left and reconstructed number on the right after 7.8x compression with principal components

The following Python code can be used to PCA compress the data `X[nDim,nSamples]`:

```
# Compute the first 100 principal components and PCA compress the data from scipy.linalg import eigh
nSamples = X.shape[1]
nDim = X.shape[0]
corX = (X @ X.T) / nSamples
eigvals, eigvecs = eigh(corX)
V  = np.fliplr(eigvecs)
# PCA Transformed data
Y = V[:,:100] @ V[:,:100].T @ X
```

Since PCA is optimal in the mean-squares error sense, it has been widely used in signal and image processing, data analysis, pattern recognition, communications, and control engineering. Applications such as the separation of signal subspace and noise subspace are important in digital communications and digital image processing. Applications of PCA in signal processing include temporal and spatial domain spectral analyses. Examples include multiple signal classification (MUSIC) techniques, minimum-norm methods, ESPIRIT estimators, and weighted subspace fitting (WSF) methods for estimating frequencies of sinusoids or direction of arrival (DOA) of plane waves impinging on an antenna array. More recently, PCA is applied to blind digital communications and speech processing. For example, in [Chatterjee et al. 97-99; Chen et al. 99], PCA is employed in a blind two-dimensional RAKE receiver for DS-CDMA-based space-frequency processing to solve the near-far problem. In [Diamantaras and Strintzis 97], PCA is used for optimal linear vector coding and decoding in the presence of noise.

### Unified Framework

In this chapter, I present a unified framework to derive and analyze several algorithms (some well-known) for adaptive eigen-decomposition. The steps consist of the following:

1.  Description of an objective function from which I derive the adaptive algorithm by using the gradient descent method of nonlinear optimization.

2.  For each objective function, I offer three methods of deriving the adaptive algorithms, which lead to three sets of adaptive algorithms, each with its own computational cost, convergence property, and implementation simplicity:
    1.  *Homogeneous Adaptive Rule:* These algorithms do not compute the true normalized eigenvectors with decreasing eigenvalues. Instead, they produce a linear combination of the unit eigenvectors. However, these algorithms can be computed quickly and implemented in parallel networks.

    2.  *Deflation Adaptive Rule:* Here, we lose the homogeneity of the algorithms, but produce unit eigenvectors with decreasing eigenvalues, thereby satisfying many application requirements. However, the training is sequential, and the learning rule for the weights of the *n*^(*th*) neuron depends on the training of the previous *n*–1 neurons, thereby making the training process difficult for parallel implementations.

    3.  *Weighted Adaptive Rule:* These algorithms are obtained by breaking the symmetry of the homogeneous algorithms by using a different scalar weight for each eigenvector. The unit eigenvectors are obtained explicitly and in the order of decreasing eigenvalues. It is also possible to implement these algorithms in parallel networks. However, the algorithms require extra computation.

It is interesting to observe that many algorithms presented here were independent discoveries over more than a decade. For example, Oja et al*.* [Oja and Karhunen 85] first presented the Oja homogeneous algorithm (5.5) in 1985\. Various practitioners extensively analyzed this algorithm in separate publications from 1989 to 1998 (see Section [5.3.1](#522202_1_En_5_Chapter.xhtml#Sec7)). Sanger [Sanger 89] showed the deflation version (5.8) of this algorithm in 1989\. Subsequently, Oja, Brockett, Xu and Chen et al*.* presented the weighted variation (5.10) of this algorithm during 1992-98 (see Sec. [5.3.2](#522202_1_En_5_Chapter.xhtml#Sec8)). This chapter unifies these algorithms and many others, including several new algorithms, in a single framework with uncomplicated derivations. Note that although these algorithms were discovered from various perspectives, I presented them on a single foundation.

Given an asymptotically stationary sequence {**x**[*k*]∈ℜ^(*n*)} that has been centered to zero mean, the asymptotic data correlation matrix is given by ![$$ A=\underset{k\to \infty }{\lim }E\left[{\mathbf{x}}_k{\mathbf{x}}_k^T\right] $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq1.png). The *p*≤*n* orthogonal unit principal eigenvectors **ϕ**[1],...,**ϕ**[*p*] of *A* are given by

![$$ {\boldsymbol{\upvarphi}}_i^TA{\boldsymbol{\upvarphi}}_j={\lambda}_i{\delta}_{ij} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq2.png), and ![$$ {\boldsymbol{\upvarphi}}_i^T{\boldsymbol{\upvarphi}}_j={\delta}_{ij} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq3.png)for *i*=1,…,*p*, (5.1)

where λ[1]> ... >λ[*p*]>λ[*p*+1]≥ ... ≥λ[*n*]>0 are the *p* largest eigenvalues of *A* in descending order of magnitude. If the sequence {**x**[*k*]} is non-stationary, we compute the online data correlation matrix *A*[*k*]∈ℜ^(*n*X*n*) by (2.3) or (2.5).

In my analyses of the algorithms, I follow the methodology outlined in Section [1.​4](#522202_1_En_1_Chapter.xhtml#Sec19). For the algorithms, I describe an *objective function J*(**w**[*i*]; *A*) and an updated rule of the form

*W*[*k* + 1] = *W*[*k*] + *η*[*k*]*h*(*W*[*k*], *A*[*k*]), (5.2)

where *h*(*W*[*k*],*A*[*k*]) follows certain continuity and regularity properties [Ljung 77, 92] and *η*[*k*] is a decreasing gain sequence.

### Outline of This Chapter

In Section [5.2](#522202_1_En_5_Chapter.xhtml#Sec4), I discuss the objective functions and their variations that are analyzed in this chapter. In Section [5.3](#522202_1_En_5_Chapter.xhtml#Sec6), I present adaptive algorithms for the homogeneous, deflation, and weighted variations for the OJA objective function with convergence results. In Section [5.4](#522202_1_En_5_Chapter.xhtml#Sec11), I analyze the same three variations for the mean squared error (XU) objective function. In Section [5.5](#522202_1_En_5_Chapter.xhtml#Sec16), I discuss algorithms derived from the penalty function (PF) objective function and prove the convergence results. In Section [5.6](#522202_1_En_5_Chapter.xhtml#Sec21), I consider the augmented Lagrangian 1 (AL1) objective function; in Section [5.7](#522202_1_En_5_Chapter.xhtml#Sec26), I present the augmented Lagrangian 2 (AL2) objective function. In Section [5.8](#522202_1_En_5_Chapter.xhtml#Sec31), I present the information theory (IT) criterion; in Section [5.9](#522202_1_En_5_Chapter.xhtml#Sec36), I describe the Rayleigh quotient (RQ) criterion. In Section [5.10](#522202_1_En_5_Chapter.xhtml#Sec41), I present a summary of all algorithms discussed here. In Section [5.11](#522202_1_En_5_Chapter.xhtml#Sec42), I discuss the experimental results and I conclude the chapter with Section [5.12](#522202_1_En_5_Chapter.xhtml#Sec43).

## 5.2 Algorithms and Objective Functions

Conforming to my proposed methodology in Chapter [2.​2](#522202_1_En_2_Chapter.xhtml#Sec2), for each algorithm, I describe an objective function and derive the adaptive algorithm for it. I have itemized the algorithms based on their inventors or on the objective functions from which they are derived. The objective functions are

1.  Oja’s objective function [Oja 92; Oja et al*.* 92],

2.  Mean squared error objective function for Xu’s Algorithm [Xu 91, 93],

3.  Penalty function method [Chauvin 89; Mathew et al*.* 95],

4.  Augmented Lagrangian Method 1,

5.  Augmented Lagrangian Method 2,

6.  Information theory criterion [Pumbley 95; Miao and Hua 98],

7.  Rayleigh quotient criterion [Luo 97; Sarkar and Yang 89; Yang et al*.* 89; Fu and Dowling 94, 95].

### Summary of Objective Functions for Adaptive Algorithms

Each algorithm is of the form (5.2), which we derive from an objective function *J*(**w**[*i*]; *A*), where *W* = [**w**[1], …, **w**[*p*]] (*p*≤*n*). The objective function *J*(**w**[*i*]; *A*) for each adaptive algorithm is given in Table [5-1](#522202_1_En_5_Chapter.xhtml#Tab1).

Table 5-1

Objective Functions for Adaptive Eigen-Decomposition Algorithms Discussed Here

| Alg. | Type | Objective Function *J*(·) |
| --- | --- | --- |
| OJA | Homogeneous | ![$$ -{\mathbf{w}}_i^T{A}^2{\mathbf{w}}_i+\frac{1}{2}{\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)}^2+\sum \limits_{j=1,j\ne i}^p{\left({\mathbf{w}}_i^TA{\mathbf{w}}_j\right)}^2 $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq4.png) |
| Deflation | ![$$ -{\mathbf{w}}_i^T{A}^2{\mathbf{w}}_i+\frac{1}{2}{\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)}^2+\sum \limits_{j=1}^{i-1}{\left({\mathbf{w}}_i^TA{\mathbf{w}}_j\right)}^2 $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq5.png) |
| Weighted | ![$$ -{c}_i{\mathbf{w}}_i^T{A}^2{\mathbf{w}}_i+\frac{c_i}{2}{\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)}^2+\sum \limits_{j=1,j\ne i}^p{c}_j{\left({\mathbf{w}}_i^TA{\mathbf{w}}_j\right)}^2 $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq6.png) |
| XU | Homogeneous | ![$$ -{\mathbf{w}}_i^TA{\mathbf{w}}_i+\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\mathbf{w}}_i^TA{\mathbf{w}}_j{\mathbf{w}}_i^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq7.png) |
| Deflation | ![$$ -{\mathbf{w}}_i^TA{\mathbf{w}}_i+\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1}^{i-1}{\mathbf{w}}_i^TA{\mathbf{w}}_j{\mathbf{w}}_j^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq8.png) |
| Weighted | ![$$ -{c}_i{\mathbf{w}}_i^TA{\mathbf{w}}_i+{c}_i\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{c}_j{\mathbf{w}}_i^TA{\mathbf{w}}_j{\mathbf{w}}_j^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq9.png) |
| PF | Homogeneous | ![$$ -{\mathbf{w}}_i^TA{\mathbf{w}}_i+\mu {\Lambda}_H\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq10.png) |
| Deflation | ![$$ -{\mathbf{w}}_i^TA{\mathbf{w}}_i+\mu {\Lambda}_D\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq11.png) |
| Weighted | ![$$ -{c}_i{\mathbf{w}}_i^TA{\mathbf{w}}_i+\mu {\Lambda}_W\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq12.png) |
| AL1 | Homogeneous | ![$$ -{\mathbf{w}}_i^TA{\mathbf{w}}_i+\alpha \left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{\mathbf{w}}_j^T{\mathbf{w}}_i+\mu {\Lambda}_H\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq13.png) |
| Deflation | ![$$ -{\mathbf{w}}_i^TA{\mathbf{w}}_i+\alpha \left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1}^{i-1}{\beta}_j{\mathbf{w}}_j^T{\mathbf{w}}_i+\mu {\Lambda}_D\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_i\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq14.png) |
| Weighted | ![$$ -{c}_i{\mathbf{w}}_i^TA{\mathbf{w}}_i+\alpha {c}_i\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{c}_j{\mathbf{w}}_j^T{\mathbf{w}}_i+\mu {\Lambda}_W\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq15.png) |
| AL2 | Homogeneous | ![$$ -{\mathbf{w}}_i^TA{\mathbf{w}}_i+{\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)}_i\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\mathbf{w}}_i^TA{\mathbf{w}}_j{\mathbf{w}}_j^T{\mathbf{w}}_i++\mu {\Lambda}_H\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq16.png) |
| Deflation | ![$$ -{\mathbf{w}}_i^TA{\mathbf{w}}_k^i+\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1}^{i-1}{\mathbf{w}}_i^TA{\mathbf{w}}_j{\mathbf{w}}_j^T{\mathbf{w}}_i++\mu {\Lambda}_D\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_i\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq17.png) |
| Weighted | ![$$ -{c}_i{\mathbf{w}}_i^TA{\mathbf{w}}_i+{c}_i\left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{c}_j{\mathbf{w}}_i^TA{\mathbf{w}}_j{\mathbf{w}}_j^T{\mathbf{w}}_i++\mu {\Lambda}_W\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq18.png) |
| IT | Homogeneous | ![$$ {\mathbf{w}}_i^T{\mathbf{w}}_i-\log \left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)+\alpha \left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{\mathbf{w}}_j^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq19.png) |
| Deflation | ![$$ {\mathbf{w}}_i^T{\mathbf{w}}_i-\log \left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)+\alpha \left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1}^{i-1}{\beta}_j{\mathbf{w}}_j^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq20.png) |
| Weighted | ![$$ {c}_i{\mathbf{w}}_i^T{\mathbf{w}}_i-{c}_i\log \left({\mathbf{w}}_i^TA{\mathbf{w}}_i\right)+\alpha {c}_i\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{c}_j{\mathbf{w}}_j^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq21.png) |
| RQ | Homogeneous | ![$$ -\left({\mathbf{w}}_i^TA{\mathbf{w}}_i/{\mathbf{w}}_i^T{\mathbf{w}}_i\right)+\alpha \left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{\mathbf{w}}_j^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq22.png) |
| Deflation | ![$$ -\left({\mathbf{w}}_i^TA{\mathbf{w}}_i/{\mathbf{w}}_i^T{\mathbf{w}}_i\right)+\alpha \left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1}^{i-1}{\beta}_j{\mathbf{w}}_j^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq23.png) |
| Weighted | ![$$ -{c}_i\left({\mathbf{w}}_i^TA{\mathbf{w}}_i/{\mathbf{w}}_i^T{\mathbf{w}}_i\right)+\alpha {c}_i\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{c}_j{\mathbf{w}}_j^T{\mathbf{w}}_i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq24.png) |

In these expressions, Λ[*H*], Λ[*D*], Λ[*W*] are defined as

![$$ {\Lambda}_H\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right)=\sum \limits_{j=1,j\ne i}^p{\left({\mathbf{w}}_j^T{\mathbf{w}}_i\right)}^2+\frac{1}{2}{\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)}^2, $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_Equa.png)

![$$ {\Lambda}_D\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_i\right)=\sum \limits_{j=1}^{i-1}{\left({\mathbf{w}}_j^T{\mathbf{w}}_i\right)}^2+\frac{1}{2}{\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)}^2, $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_Equb.png)

and

![$$ {\Lambda}_W\left({\mathbf{w}}_1,\dots, {\mathbf{w}}_p\right)=\sum \limits_{j=1,j\ne i}^p{c}_j{\left({\mathbf{w}}_j^T{\mathbf{w}}_i\right)}^2+\frac{c_i}{2}{\left({\mathbf{w}}_i^T{\mathbf{w}}_i-1\right)}^2\. $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_Equc.png)

## 5.3 OJA Algorithms

### OJA Homogeneous Algorithm

The objective function for the OJA homogeneous algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k^2{\mathbf{w}}_k^i+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)}^2+\sum \limits_{j=1,j\ne i}^p{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j\right)}^2 $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq25.png),     (5.3)

for *i*=1,…,*p* (*p*≤*n*). From the gradient of ([5.3](#522202_1_En_5_Chapter.xhtml#IEq25)) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq26.png) we obtain the following adaptive algorithms:

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i-{\eta}_k{A}_k^{-1}{\nabla}_{{\mathbf{w}}_k^i}J\left({\mathbf{w}}_k^i;{A}_k\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq27.png) for *i*=1,…,*p*,

or

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i+{\eta}_k\left({A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^p{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq28.png)     (5.4)

for *i*=1,…,*p*, where *η*[*k*] is a small decreasing constant. We define a matrix ![$$ {W}_k=\left[{\mathbf{w}}_k^1\dots {\mathbf{w}}_k^p\right] $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq29.png) (*p*≤*n*), for which the columns are the *p* weight vectors that converge to the *p* principal eigenvectors of *A* respectively. We can represent (5.4) as

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{W}_k{W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq30.png)     (5.5)

This is the matrix form of the *principal subspace learning algorithm* given by Oja [Oja 85,89,92].

*W*[*k*] converges *W*^(***)=Φ*DU*, where *D=*[*D*[1]|0]^(*T*)∈ℜ^(*n*X*p*)*, D*[1]*=diag*(*d*[1]*,...,d*[p])∈ℜ^(*p*X*p*), *d*[*i*]*=*±1 for *i=*1,...,*p*, and *U*∈ℜ^(*p*X*p*) is an arbitrary rotation matrix (i.e., *U*^(*T*)*U=UU*^(*T*)*=I*[*p*]*).*

### OJA Deflation Algorithm

The objective function for the OJA deflation adaptive PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k^2{\mathbf{w}}_k^i+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)}^2+\sum \limits_{j=1}^{i-1}{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j\right)}^2 $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq31.png) for *i*=1,…,*p*. (5.6)

From the gradient of ([5.6](#522202_1_En_5_Chapter.xhtml#IEq31)), we obtain the OJA deflation adaptive gradient descent algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq32.png),     (5.7)

where UT[⋅] sets all elements below the diagonal of its matrix argument to zero, thereby making it upper triangular. This algorithm is also known as the *generalized Hebbian algorithm* [Sanger 89]. Sanger proved that *W*[*k*] converges to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### OJA Weighted Algorithm

The objective function for the OJA weighted adaptive PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{c}_i{{\mathbf{w}}_k^i}^T{A}_k^2{\mathbf{w}}_k^i+\frac{c_i}{2}{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)}^2+\sum \limits_{j=1,j\ne i}^p{c}_j{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j\right)}^2 $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq33.png)     (5.8)

for *i*=1,…,*p* and *c*[1],…,*c*[*p*] are small positive numbers satisfying *c*[1]>*c*[2]>…>*c*[*p*]>0, *p*≤*n*. From (5.8), we obtain the OJA weighted adaptive gradient descent algorithm for PCA as

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq34.png)     (5.9)

where *C*=*diag*(*c*[1],…,*c*[*p*]). This algorithm is also known as *Brockett’s subspace* algorithm [Xu 93, Chen, Amari, Lin 98]. Xu [Xu 93, Theorems 5 and 6] proved that *W*[*k*] converges to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### OJA Algorithm Python Code

The following Python code implements the OJA PCA algorithms with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
nEA = 4 # number of PCA components computed
nEpochs  = 2
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
W3 = W1
c = [2-0.3*k for k in range(nEA)]
C = np.diag(c)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrix A with current data sample x
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# Homogeneous Gradient Descent
W1 = W1 + (1/(100 + cnt))*(A @ W1 - W1 @ (W1.T @ A @ W1))
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - W2 @ np.triu(W2.T @ A @ W2))
# Weighted Gradient Descent
W3 = W3 + (1/(220 + cnt))*(A @ W3 @ C - W3 @ C @ (W3.T @ A @ W3))
```

## 5.4 XU Algorithms

### XU Homogeneous Algorithm

The objective function for the XU homogeneous adaptive PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq35.png)     (5.10)

for *i*=1,…,*p*. From the gradient of (5.10) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq36.png), we obtain the XU homogeneous adaptive gradient descent algorithm for PCA as

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{A}_k{W}_k{W}_k^T{W}_k-{W}_k{W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq37.png).     (5.11)

This algorithm is also known as the *least mean squared error reconstruction* (*LMSER*) *algorithm* [Xu 91,93] and was derived from a least mean squared error criterion of a feed-forward neural network. Xu [Xu 93, Theorems 2, 3] proved that *W*[*k*] converges to *W*^(***)=Φ*DU*, where *D=*[*D*[1]|0]^(*T*)∈ℜ^(*n*X*p*), *D*[1]*=diag*(*d*[1]*,...,d*[*p*])∈ℜ^(*p*X*p*), *d*[*i*]*=*±1 for *i=*1,...,*p*, and *U*∈ℜ^(*p*X*p*) is an arbitrary rotation matrix.

### XU Deflation Algorithm

The objective function for the XU deflation adaptive PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^{i-1}{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq38.png)     (5.12)

for *i*=1,…,*p*. The XU deflation adaptive gradient descent algorithm for PCA is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{A}_k{W}_k UT\left({W}_k^T{W}_k\right)-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq39.png),     (5.13)

where UT[⋅] sets all elements below the diagonal of its matrix argument to zero. Chatterjee et al*.* [Mar 00, Theorems 1,2] proved that *W*[*k*] converges to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞*.*

### XU Weighted Algorithm

The objective function for the XU weighted adaptive PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{c}_i{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+{c}_i\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{c}_j{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq40.png)     (5.14)

for *i*=1,…,*p* and *c*[1]>*c*[2]>…>*c*[*p*]>0. From ([5.14](#522202_1_En_5_Chapter.xhtml#IEq40)), the XU weighted adaptive gradient descent algorithm for PCA is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k{C}_k-{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{W}_k\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq41.png)     (5.15)

where *C*=*diag*(*c*[1],…,*c*[*p*]). Xu [Xu 93, Theorems 5, 6] proved that *W*[*k*] converges to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### XU Algorithm Python Code

The following Python code implements the XU PCA algorithms with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
nEA = 4 # number of PCA components computed
nEpochs  = 2
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
W3 = W1
c = [2-0.3*k for k in range(nEA)]
C = np.diag(c)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# Homogeneous Gradient Descent
W1 = W1 + (1/(100 + cnt))*(A @ W1 - 0.5 * W1 @ (W1.T @ A @ W1) -0.5 * A @ W1 @ (W1.T @ W1))
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - 0.5 * W2 @ np.triu(W2.T @ A @ W2) -0.5 * A @ W2 @ np.triu(W2.T @ W2))
# Weighted Gradient Descent
W3 = W3 + (1/(100 + cnt))*(A @ W3 @ C - 0.5 * W3 @ C @ (W3.T @ A @ W3) -0.5 * A @ W3 @ C @ (W3.T @ W3))
```

## 5.5 PF Algorithms

### PF Homogeneous Algorithm

We obtain the objective function for the penalty function homogeneous PCA algorithm by expressing the Rayleigh quotient criterion as the following penalty function:

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\mu \left(\sum \limits_{j=1,j\ne i}^p{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq42.png),  (5.16)

where *μ*>0 and *i*=1,…,*p*. From the gradient of ([5.16](#522202_1_En_5_Chapter.xhtml#IEq42)) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq43.png), we obtain the PF homogeneous adaptive gradient descent algorithm for PCA as

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-\mu {W}_k\left({W}_k^T{W}_k-{I}_p\right)\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq44.png),     (5.17)

where *I*[*p*] is a *p*X*p* identity matrix.

*W*[k] converges to *W*^*=Φ*DU*, where *D*=[*D*[1]|0]^T∈ℜ^(nXp), *D*[1]=diag(*d*[1],...,*d*[*p*])∈ ℜ^(*pXp*), *d*[*i*]=±![$$ \sqrt{1+\left({\lambda}_i/\mu \right)} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq45.png) for i=1,...,*p*, and *U*∈ℜ^(*pXp*) is an arbitrary rotation matrix. Recall that *λ*[1]>*λ*[2]>...>*λ*[*p*]>*λ*[*p*+1]≥...≥*λ*[*n*] >0 are the eigenvalues of *A*, and **ϕ**[*i*] as the eigenvector corresponding to *λ*[*i*] such that Φ=[**ϕ**[1] ... **ϕ**[*n*]] are orthonormal.

### PF Deflation Algorithm

The objective function for the PF deflation PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\mu \left(\sum \limits_{j=1}^{i-1}{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq46.png),  (5.18)

where *μ* > 0 and *i*=1,…,*p*. The PF deflation adaptive gradient descent algorithm for PCA is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-\mu {W}_k UT\left({W}_k^T{W}_k-{I}_p\right)\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq47.png),     (5.19)

where UT[⋅] sets all elements below the diagonal of its matrix argument to zero. *W*[k] converges to *W*^*=[*d*[1]ϕ[1] *d*[2]ϕ[2] … *d*[*p*]ϕ[*p*]]*, where d*[*i*] *= ±*![$$ \sqrt{1+\left({\lambda}_i/\mu \right)} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq48.png).

### PF Weighted Algorithm

The objective function for the PF weighted PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{c}_i{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\mu \left(\sum \limits_{j=1,j\ne i}^p{c}_j{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{c_i}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq49.png) (5.20)

where *c*[1]>*c*[2]>…>*c*[*p*]>0, *μ* > 0, and *i*=1,…,*p*. The PF weighted adaptive gradient descent algorithm for PCA is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_kC-\mu {W}_kC\left({W}_k^T{W}_k-{I}_p\right)\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq50.png),     (5.21)

where *C=diag*(*c*[1],…,*c*[*p*]). *W*[*k*] converges to *W*^(***)=[*d*[1]ϕ[1] *d*[2]ϕ[2] … *d*[*p*]ϕ[*p*]], *whered*[*i*] = ±![$$ \sqrt{1+\left({\lambda}_i/\mu \right)} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq51.png).

### PF Algorithm Python Code

The following Python code implements the PF PCA algorithms with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
nEA = 4 # number of PCA components computed
nEpochs  = 2
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
W3 = W1
c = [2-0.3*k for k in range(nEA)]
C = np.diag(c)
I  = np.identity(nEA)
mu = 2
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# Homogeneous Gradient Descent
W1 = W1 + (1/(100 + cnt))*(A @ W1 - mu * W1 @ ((W1.T @ W1) - I))
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - mu * W2 @ np.triu((W2.T @ W2) - I))
# Weighted Gradient Descent
W3 = W3 + (1/(100 + cnt))*(A @ W3 @ C - mu * W3 @ C @ ((W3.T @ W3) - I))
```

## 5.6 AL1 Algorithms

### AL1 Homogeneous Algorithm

We obtain the objective function for the augmented Lagrangian Method 1 homogeneous PCA algorithm by applying the augmented Lagrangian method of nonlinear optimization to the Rayleigh quotient criterion as follows:

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\alpha \left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq52.png),

![$$ +\mu \left(\sum \limits_{j=1,j\ne i}^p{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq53.png),     (5.22)

for *i*=1,…,*p*, and (*α*,*β*[1],*β*[2],…,*β*[p]) are Lagrange multipliers and *μ* is a positive penalty constant. The objective function ([5.22](#522202_1_En_5_Chapter.xhtml#IEq53)) is equivalent to ![$$ - tr\left({W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq54.png) under the constraint ![$$ {W}_k^T{W}_k={I}_p $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq55.png), which also serves as the energy function for the AL1 algorithms.

Equating the gradient of ([5.22](#522202_1_En_5_Chapter.xhtml#IEq53)) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq56.png) to **0**, and using the constraint ![$$ {{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i={\delta}_{ij} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq57.png), we obtain

![$$ \alpha ={{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq58.png) and ![$$ {\beta}_j={{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq59.png) for *j*=1,…,*p*, *j*≠*i*.     (5.23)

Replacing (*α*,*β*[1],*β*[2],…,*β*[*p*]) in the gradient of ([5.22](#522202_1_En_5_Chapter.xhtml#IEq53)), we obtain the AL1 homogeneous adaptive gradient descent algorithm for PCA:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(\left({A}_k{W}_k-{W}_k{W}_k^T{A}_k{W}_k-\mu {W}_k\left({W}_k^T{W}_k-{I}_p\right)\right)\right. $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq60.png),     (5.24)

where *μ*>0 and *I*[*p*] is a *p*X*p* identity matrix. This algorithm is the same as the OJA algorithm (5.5) for *μ*=0\. We can prove that *W*[*k*] converges *W*^(***)=Φ*DU*, where *D=*[*D*[1]|0]^(*T*)∈ℜ^(*n*X*p*)*, D*[1]*= diag*(*d*[1]*,...,d*[*p*])∈ℜ^(*p*X*p*), *d*[*i*]*=*±1 for *i=*1,...,*p*, and *U*∈ℜ^(*p*X*p*) is an arbitrary rotation matrix.

### AL1 Deflation Algorithm

The objective function for the AL1 Deflation PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\alpha \left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^{i-1}{\beta}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i+\mu \left(\sum \limits_{j=1}^{i-1}{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq61.png)     (5.25)

for *i*=1,…,*p*. By solving for (*α*,*β*[1],*β*[2],…,*β*[*i*–1]), and replacing them in the gradient of ([5.25](#522202_1_En_5_Chapter.xhtml#IEq61)), we obtain

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(\left({A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-\mu {W}_k UT\left({W}_k^T{W}_k-{I}_p\right)\right)\right. $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq62.png),     (5.26)

where *μ*>0 and UT[⋅] sets all elements below the diagonal of its matrix argument to zero. *W*[*k*] converges to *W*^(***)=[±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### AL1 Weighted Algorithm

The objective function for the AL1 weighted PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{c}_i{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\alpha {c}_i\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{c}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq63.png),

![$$ +\mu \left(\sum \limits_{j=1,j\ne i}^p{c}_j{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{c_i}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq64.png)     (5.27)

for *i*=1,…,*p*, where (*α*,*β*[1],*β*[2],…,*β*[*p*]) are Lagrange multipliers and *μ* is a positive penalty constant. By solving for (*α*,*β*[1],*β*[2],…,*β*[*p*]) and replacing them in the gradient of ([5.27](#522202_1_En_5_Chapter.xhtml#IEq64)), we obtain the AL1 weighted adaptive gradient descent algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(\left({A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k-\mu {W}_kC\left({W}_k^T{W}_k-{I}_p\right)\right)\right. $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq65.png),     (5.28)

where *μ*>0, *c*[1]>*c*[2]>…>*c*[*p*]>0, and *C*=*diag*(*c*[1],…,*c*[*p*]). We can prove that *W*[*k*] converges to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### AL1 Algorithm Python Code

The following Python code implements the AL1 PCA algorithms with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
nEA = 4 # number of PCA components computed
nEpochs  = 2
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
W3 = W1
c = [2-0.3*k for k in range(nEA)]
C = np.diag(c)
I  = np.identity(nEA)
mu = 2
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# Homogeneous Gradient Descent
W1 = W1 + (1/(100 + cnt))*(A @ W1 - W1 @ (W1.T @ A @ W1) -mu * W1 @ ((W1.T
@ W1) - I))
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - W2 @ np.triu(W2.T @ A @ W2) -mu * W2 @ np.triu
((W2.T @ W2) - I))
# Weighted Gradient Descent
W3 = W3 + (1/(300 + cnt))*(A @ W3 @ C - W3 @ C @ (W3.T @ A @ W3) -mu * W3 @ C @
((W3.T @ W3) - I))
```

## 5.7 AL2 Algorithms

### AL2 Homogeneous Algorithm

The AL2 objective function can be derived from the AL1 homogeneous objective function ([5.22](#522202_1_En_5_Chapter.xhtml#IEq53)) by replacing *α*,*β*[1],*β*[2],…,*β*[*p*] from ([5.23](#522202_1_En_5_Chapter.xhtml#IEq54)) into ([5.22](#522202_1_En_5_Chapter.xhtml#IEq53)) as

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i+ $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq66.png)

![$$ \mu \left(\sum \limits_{j=1,j\ne i}^p{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq67.png),     (5.29)

for *i*=1,…,*p* and *μ*>0\. As seen with the XU objective function ([5.10](#522202_1_En_5_Chapter.xhtml#IEq35)), ([5.29](#522202_1_En_5_Chapter.xhtml#IEq67)) also has the constraints ![$$ {{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i={\delta}_{ij} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq68.png) built into it. The AL2 homogeneous adaptive gradient descent algorithm for PCA is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{W}_k{W}_k^T{A}_k{W}_k-{A}_k{W}_k{W}_k^T{W}_k-\mu {W}_k\left({W}_k^T{W}_k-{I}_p\right)\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq69.png)     (5.30)

where *I*[*p*] is a *p*X*p* identity matrix. *W*[*k*] converges to *W*^(***)*=ΦDU*, where *D*=[*D*[*1*]|0]^(*T*)∈ℜ^(*nXp*), *D*[1]= diag(*d*[*1*],...,*d*[*p*])∈ℜ^(*pXp*), *d*[*i*]=±1 for *i*=1,...,*p*, and *U*∈ℜ^(*pXp*) is an arbitrary rotation matrix.

### AL2 Deflation Algorithm

The objective function for the AL2 deflation PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^{i-1}{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i+ $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq70.png)

![$$ \mu \left(\sum \limits_{j=1}^{i-1}{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq71.png),     (5.31)

for *i*=1,…,*p* and *μ* > 0\. Taking the gradient of ([5.31](#522202_1_En_5_Chapter.xhtml#IEq71)) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq72.png) we obtain the AL2 deflation adaptive gradient descent algorithm for PCA as

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-{A}_k{W}_k UT\left({W}_k^T{W}_k\right)-\mu {W}_k UT\left({W}_k^T{W}_k-{I}_p\right)\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq73.png)     (5.32)

where *μ* > 0, and UT[⋅] sets all elements below the diagonal of its matrix argument to zero. *W*[*k*] converges to *W*^*= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### AL2 Weighted Algorithm

The objective function for the AL2 weighted PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{c}_i{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+{c}_i\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{c}_j{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i+ $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq74.png)

![$$ \mu \left(\sum \limits_{j=1,j\ne i}^p{c}_j{\left({{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i\right)}^2+\frac{c_i}{2}{\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq75.png),     (5.33)

where *i*=1,…,*p*, *μ*>0, *c*[1]>*c*[2]>…>*c*[*p*]>0. The AL2 weighted adaptive gradient descent algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{W}_k-\mu {W}_kC\left({W}_k^T{W}_k-{I}_p\right)\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq76.png)     (5.34)

where *C*=*diag*(*c*[1],…,*c*[*p*]). *W*[*k*] converges to *W*^*= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### AL2 Algorithm Python Code

The following Python code implements the AL2 PCA algorithms with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
nEA = 4 # number of PCA components computed
nEpochs  = 2
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
W3 = W1
c = [2-0.3*k for k in range(nEA)]
C = np.diag(c)
I  = np.identity(nEA)
mu = 2
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# Homogeneous Gradient Descent
W1 = W1 + (1/(100 + cnt))*(A @ W1 - 0.5 * W1 @ (W1.T @ A @ W1) -0.5 * A @ W1
@(W1.T @ W1) -0.5 * mu * W1
@((W1.T @ W1) - I))
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - 0.5 * W2 @ np.triu(W2.T @ A @ W2) -0.5 * A
@ W2 @ np.triu(W2.T @ W2) -
0.5 * mu * W2 @ np.triu((W2.T @ W2) - I))
# Weighted Gradient Descent
W3 = W3 + (1/(100 + cnt))*(A @ W3 @ C - 0.5 * W3 @ C @ (W3.T @ A @ W3) -
0.5 * A @ W3 @ C @ (W3.T @ W3) -0.5 * mu * W3 @ C
@((W3.T @ W3) - I))
```

## 5.8 IT Algorithms

### IT Homogeneous Function

The objective function for the information theory homogeneous PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)={{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-\log \left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)+\alpha \left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq77.png)     (5.35)

where (*α*,*β*[1],*β*[2],…,*β*[*p*]) are Lagrange multipliers and *i*=1,…,*p*. By equating the gradient of ([5.35](#522202_1_En_5_Chapter.xhtml#IEq77)) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq78.png) to **0**, and using the constraint ![$$ {{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i={\delta}_{ij} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq79.png), we obtain

*α* = 0 and ![$$ {\beta}_j={{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i/{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq80.png)for *j*=1,…,*p*, *j*≠*i*.     (5.36)

Replacing (*α*,*β*[1],*β*[2],…,*β*[*p*]) in the gradient of ([5.36](#522202_1_En_5_Chapter.xhtml#IEq80)), we obtain the IT homogeneous adaptive gradient descent algorithm for PCA:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{W}_k{W}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq81.png),     (5.37)

where DIAG[⋅] sets all elements *except* the diagonal of its matrix argument to zero, thereby making the matrix diagonal. *W*[*k*] converges to *W*^*= Φ*DU*, where *D=*[*D*[1]|0]^(*T*)∈ℜ^(*n*X*p*), *D*[1]*=diag*(*d*[1]*,...,d*[*p*])∈ ℜ^(*p*X*p*), *d*[*i*]*=*±1 for *i=*1,...,*p*, and *U*∈ℜ^(*p*X*p*) is an arbitrary rotation matrix*.*

### IT Deflation Algorithm

The objective function for the information theory deflation PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)={{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-\log \left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)+\alpha \left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^{i-1}{\beta}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq82.png),     (5.38)

where (*α*,*β*[1],*β*[2],…,*β*[*i*–1]) are Lagrange multipliers and *i*=1,…,*p*. By solving for (*α*,*β*[1],*β*[2],…,*β*[*i*–1]) and replacing them in the gradient of ([5.38](#522202_1_En_5_Chapter.xhtml#IEq82)), we obtain the IT deflation adaptive gradient descent algorithm for PCA:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq83.png).     (5.39)

*W*[*k*] converges with probability one to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### IT Weighted Algorithm

The objective function for the IT weighted PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)={c}_i{{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-{c}_i\log \left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)+\alpha {c}_i\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{c}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq84.png)     (5.40)

for *i*=1,…,*p* and (*α*,*β*[1],*β*[2],…,*β*[*p*]) are Lagrange multipliers. The IT weighted adaptive gradient descent algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq85.png),     (5.41)

where *C*=*diag*(*c*[1],…,*c*[*p*]) and *c*[1]>*c*[2]>…>*c*[*p*]>0. Here *W*[*k*] converges to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### IT Algorithm Python Code

The following Python code implements the IT PCA algorithms with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
nEA = 4 # number of PCA components computed
nEpochs  = 3
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
W3 = W1
c = [2-0.3*k for k in range(nEA)]
C = np.diag(c)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# Homogeneous Gradient Descent
W1 = W1 + (1/(50 + cnt))*(A @ W1 - W1 @ (W1.T @ A @ W1)) @ \inv(np.diag(
np.diagonal(W1.T @ A @ W1)))
# Deflated Gradient Descent
W2 = W2 + (1/(20 + cnt))*(A @ W2 - W2 @ np.triu(W2.T @ A @ W2)) @ \inv(np.diag(
np.diagonal(W2.T @ A @ W2)))
# Weighted Gradient Descent
W3 = W3 + (1/(10 + cnt))*(A @ W3 @ C - W3 @ C @ (W3.T @ A @ W3)) @ \inv(np.diag(
np.diagonal(W3.T @ A @ W3)))
```

## 5.9 RQ Algorithms

### RQ Homogeneous Algorithm

We obtain the objective function for the Rayleigh quotient homogeneous PCA algorithm from the Rayleigh quotient as follows:

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-\frac{{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i}+\alpha \left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq86.png),     (5.42)

for *i*=1,…,*p* where (*α*,*β*[1],*β*[2],…, *β*[*p*]) are Lagrange multipliers. By equating the gradient of ([5.42](#522202_1_En_5_Chapter.xhtml#Par146)) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq87.png) to **0**, and using the constraint ![$$ {{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i={\delta}_{ij} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq88.png), we obtain

*α* = 0 and ![$$ {\beta}_j={{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i/{{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq89.png) for *j*=1,…,*p*, *j*≠*i*.     (5.43)

Replacing (*α*,*β*[1],*β*[2],…,*β*[*p*]) in the gradient of ([5.42](#522202_1_En_5_Chapter.xhtml#Par146)) and making an approximation, we obtain the RQ homogeneous adaptive gradient descent algorithm for PCA:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{W}_k{W}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq90.png),     (5.44)

where DIAG[⋅] sets all elements except the diagonal of its matrix argument to zero. Here *W*[*k*] converges to *W*^(***)= Φ*DU*, where *D=*[*D*[1]|0]^(*T*)∈ℜ^(*n*X*p*), *D*[1]*=diag*(*d*[1]*,...,d*[*p*])∈ℜ^(*p*X*p*), *d*[*i*]*=*±1 for *i=*1,...,*p*, and *U*∈ℜ^(*p*X*p*) is an arbitrary rotation matrix*.*

### RQ Deflation Algorithm

The objective function for the RQ deflation PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-\frac{{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i}+\alpha \left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^{i-1}{\beta}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq91.png)     (5.45)

for *i*=1,…,*p* where (*α*,*β*[1],*β*[2],…, *β*[*i*–1]) are Lagrange multipliers. By solving for (*α*,*β*[1],*β*[2],…,*β*[*i*–1]) and replacing them in the gradient of ([5.45](#522202_1_En_5_Chapter.xhtml#Par153)), we obtain the adaptive gradient descent algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq92.png).     (5.46)

*W*[*k*] converges with probability one to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### RQ Weighted Algorithm

The objective function for the RQ weighted PCA algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-{c}_i\frac{{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i}+\alpha {c}_i\left({{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{c}_j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq93.png),     (5.47)

for *i*=1,…,*p* and (*α*,*β*[1],*β*[2],…,*β*[*p*]) are Lagrange multipliers. By solving for (*α*,*β*[1],*β*[2],…,*β*[*p*]) and replacing them in the gradient of ([5.47](#522202_1_En_5_Chapter.xhtml#Par158)), we obtain the algorithm

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq94.png),     (5.48)

where *C*=*diag*(*c*[1],…,*c*[*p*]) and *c*[1]>*c*[2]>…>*c*[*p*]>0. *W*[*k*] converges with probability one to *W*^(***)= [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞.

### RQ Algorithm Python Code

The following Python code implements the RQ PCA algorithms with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
nEA = 4 # number of PCA components computed
nEpochs  = 2
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA))# weight vectors of all algorithms
W2 = W1
W3 = W1
c = [2-0.3*k for k in range(nEA)]
C = np.diag(c)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
# Homogeneous Gradient Descent
W1 = W1 + (1/(50 + cnt))*(A @ W1 - W1 @ (W1.T @ A @ W1)) @ \
inv(np.diag(np.diagonal(W1.T @ W1)))
# Deflated Gradient Descent
W2 = W2 + (1/(20 + cnt))*(A @ W2 - W2 @ np.triu(W2.T @ A @ W2)) @ \inv(np.diag(
np.diagonal(W2.T @ W2)))
# Weighted Gradient Descent
W3 = W3 + (1/(200 + cnt))*(A @ W3 @ C - W3 @ C @ (W3.T @ A @ W3)) @ \inv(np.diag(
np.diagonal(W3.T @ W3)))
```

## 5.10 Summary of Adaptive Eigenvector Algorithms

I summarize the algorithms discussed here in Table [5-2](#522202_1_En_5_Chapter.xhtml#Tab2). Each algorithm is of the form given in ([5.1](#522202_1_En_5_Chapter.xhtml#Par16)). The term *h*(*W*[*k*],*A*[*k*]) in ([5.1](#522202_1_En_5_Chapter.xhtml#Par16)) for each adaptive algorithm is given in Table [5-2](#522202_1_En_5_Chapter.xhtml#Tab2). Note the following:

Table 5-2

List of Adaptive Eigen-Decomposition Algorithms

| Alg | Type | Adaptive Algorithm *h*(*W*[*k*],*A*[*k*]) | Comment |
| --- | --- | --- | --- |
| OJA | Deflation | ![$$ {A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq95.png) | n³p⁶, 6 |
| Weighted | ![$$ {A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq96.png) | n⁴p⁶, 6 |
| XU | Deflation | ![$$ 2{A}_k{W}_k-{A}_k{W}_k UT\left({W}_k^T{W}_k\right)-{W}_k UT\left({W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq97.png) | 2n³p⁶, 8 |
| Weighted | ![$$ 2{A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{W}_k $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq98.png) | 2n⁴p⁶, 8 |
| PF | Deflation | ![$$ {A}_k{W}_k-\mu {W}_k UT\left({W}_k^T{W}_k-{I}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq99.png) | n²p⁴, 7 |
| Weighted | ![$$ {A}_k{W}_kC-\mu {W}_kC\left({W}_k^T{W}_k-{I}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq100.png) | n³p⁴, 7 |
| AL1 | Deflation | ![$$ {A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-\mu {W}_k UT\left({W}_k^T{W}_k-{I}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq101.png) | n³p⁶+ n²p⁴, 9 |
| Weighted | ![$$ {A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k-\mu {W}_kC\left({W}_k^T{W}_k-{I}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq102.png) | n⁴p⁶+ n³p⁴, 9 |
| AL2 | Deflation | ![$$ 2{A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-{A}_k{W}_k UT\left({W}_k^T{W}_k\right)-\mu {W}_k UT\left({W}_k^T{W}_k-{I}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq103.png) | 2n³p⁶+ n²p⁴, 10 |
| Weighted | ![$$ 2{A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{W}_k-\mu {W}_kC\left({W}_k^T{W}_k-{I}_p\right) $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq104.png) | 2n⁴p⁶+ n³p⁴, 10 |
| IT | Deflation | ![$$ \left({A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq105.png) | Not applicable |
| Weighted | ![$$ \left({A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq106.png) | Not applicable |
| RQ | Deflation | ![$$ \left({A}_k{W}_k-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq107.png) | Not applicable |
| Weighted | ![$$ \left({A}_k{W}_kC-{W}_k{CW}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{W}_k\right)}^{-1} $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq108.png) | Not applicable |

1.  For each algorithm, I rate the compute and convergence performance.

2.  I skip the homogeneous algorithms because they not useful for practical applications since they produce arbitrary rotations of the eigenvectors.

3.  Note that *A*[*k*]∈ℜ^(*n*X*n*) and *W*[*k*]∈ℜ^(*n*X*p*). I present the computation complexity of each algorithm in terms of the matrix dimensions *n* and *p*.

4.  The convergence performance is determined based on the speed of convergence of the principal and the minor components. I rate convergence on a scale of 1-10 where 10 is the best performing.

5.  I skip the IT and RQ algorithms because they did not perform well compared to the remaining algorithms and the matrix inversion increases computational complexity.

Observe the following:

1.  The OJA algorithm has the least complexity and good performance.

2.  The AL2 algorithm has the most complexity and best performance.

3.  AL1 is the next best after AL2, and PF and Xu are the next best.

The complexity and accuracy tradeoffs will determine the algorithm to use in real-world scenarios. If you can afford the computation, the AL2 algorithm is the best. The XU algorithm is a good balance of complexity and speed of convergence.

## 5.11 Experimental Results

I generated 500 samples **x**[*k*] of 10-dimensional Gaussian data (i.e., *n*=10) with the mean zero and covariance given below. The covariance matrix is obtained from the second covariance matrix in [Okada and Tomita 85] multiplied by 3\. The covariance matrix is

![](images/522202_1_En_5_Chapter/522202_1_En_5_Figa_HTML.png)

The eigenvalues of the covariance matrix are

17.9013, 10.2212, 8.6078, 6.5361, 2.2396, 1.8369, 1.1361, 0.7693, 0.2245, 0.1503.

I computed the first four principal eigenvectors (i.e., the eigenvector corresponding to the largest four eigenvalues) (i.e., *p*=4) by the adaptive algorithms described here. In order to compute the online data sequence {*A*[*k*]}, I generated random data vectors {**x**[*k*]} from the above covariance matrix. I generated {*A*[*k*]} from {**x**[*k*]} by using algorithm (2.5 in Section [2.​4](#522202_1_En_2_Chapter.xhtml#Sec6)) with *β*=1\. I computed the correlation matrix *A* after collecting all 500 samples **x**[*k*] as

![$$ A=\frac{1}{500}\sum \limits_{i=1}^{500}{\mathbf{x}}_i{\mathbf{x}}_i^T. $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_Equd.png)

I referred to the eigenvectors and eigenvalues computed from this *A* by a standard numerical analysis method [Golub and VanLoan 83] as the *actual values*.

I started all algorithms with **w**[0] = 0.1*ONE, where ONE is a 10X4 matrix whose all elements are ones. In order to measure the convergence and accuracy of the algorithms, I computed the direction cosine at *k*^(*th*) update of each adaptive algorithm as

Direction cosine (*k*) = ![$$ \left|{{\mathbf{w}}_k^i}^T{\boldsymbol{\upvarphi}}_i\right|/\left\Vert {\boldsymbol{\upvarphi}}_i\right\Vert \left\Vert {\mathbf{w}}_k^i\right\Vert $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq109.png)     (5.49)

where ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_5_Chapter/522202_1_En_5_Chapter_TeX_IEq110.png) is the estimated eigenvector of *A*[*k*] at *k*^(*th*) update and **ϕ**[*i*] is the actual *i*^(*th*) principal eigenvector computed from all collected samples by a conventional numerical analysis method.

Figure [5-3](#522202_1_En_5_Chapter.xhtml#Fig3) shows the iterates of the OJA algorithms (deflated and weighted) to compute the first four principal eigenvectors of *A*. Figure [5-4](#522202_1_En_5_Chapter.xhtml#Fig4) shows the same for the XU algorithms. Figure [5-5](#522202_1_En_5_Chapter.xhtml#Fig5) shows the same for the PF algorithms. Figure [5-6](#522202_1_En_5_Chapter.xhtml#Fig6) shows the iterates of the AL1 algorithms (deflated and weighted) to compute the first four principal eigenvectors of *A*. Figure [5-7](#522202_1_En_5_Chapter.xhtml#Fig7) shows the same for the AL2 algorithms. Figure [5-8](#522202_1_En_5_Chapter.xhtml#Fig8) shows the same for the IT algorithms. Figure [5-9](#522202_1_En_5_Chapter.xhtml#Fig9) shows the same for the RQ algorithms.

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig9_HTML.png)

Figure 5-9

Convergence of the first four principal eigenvectors of A by the RQ deflation (5.46) and RQ weighted (5.48) adaptive algorithms

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig8_HTML.png)

Figure 5-8

Convergence of the first four principal eigenvectors of A by the IT deflation (5.39) and IT weighted (5.41) adaptive algorithms

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig7_HTML.png)

Figure 5-7

Convergence of the first four principal eigenvectors of A by the AL2 deflation (5.32) and AL2 weighted (5.34) adaptive algorithms

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig6_HTML.png)

Figure 5-6

Convergence of the first four principal eigenvectors of A by the AL1 deflation (5.26) and AL1 weighted (5.28) adaptive algorithms

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig5_HTML.png)

Figure 5-5

Convergence of the first four principal eigenvectors of A by the PF deflation (5.19) and PF weighted (5.21) adaptive algorithms

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig4_HTML.png)

Figure 5-4

Convergence of the first four principal eigenvectors of A by the XU deflation (5.13) and XU weighted (5.15) adaptive algorithms

![](images/522202_1_En_5_Chapter/522202_1_En_5_Fig3_HTML.png)

Figure 5-3

Convergence of the first four principal eigenvectors of A by the OJA deflation (5.7) and OJA weighted (5.9) adaptive algorithms

The diagonal weight matrix *C* used for the weighted algorithms is DIAG(2.0, 1.7, 1.4, 1.1). I ran all algorithms for *three epochs* of the data, where one epoch means presenting all training data once in random order. I did not show the results for the homogeneous algorithms since the homogeneous method produces a linear combination of the actual eigenvectors of *A*. Thus, the direction cosines are not indicative of the performance of the algorithms for the homogeneous case.

## 5.12 Concluding Remarks

In this chapter, I discussed 21 different algorithms for adaptive PCA and viewed their convergence results. For each algorithm, I presented a common framework including an objective function from which I derived the adaptive algorithm. The deflation and weighted algorithms converged for all four principal eigenvectors, although the performance for the first principal eigenvector is the best. The convergences of all algorithms are similar, except for the IT algorithm, which did not perform as well as the rest.

Note that although I applied the gradient descent technique on the objective functions, I could have applied any other technique of nonlinear optimization such as steepest descent, conjugate direction, Newton-Raphson, or recursive least squares. The availability of the objective functions allows us to derive new algorithms by using new optimization techniques on them and also to perform convergence analyses of the adaptive algorithms.

# 6. Accelerated Computation of Eigenvectors

## 6.1 Introduction

In Chapter [5](#522202_1_En_5_Chapter.xhtml), I discussed several adaptive algorithms for computing principal and minor eigenvectors of the online correlation matrix *A*[*k*]∈ℜ^(*n*X*n*) from a sequence of vectors {**x**[*k*]∈ℜ^(*n*)}. I derived these algorithms by applying the gradient descent on an objective function. However, it is well known [Baldi and Hornik 95, Chatterjee et al*.* Mar 98, Haykin 94] that *principal component analysis* (PCA) algorithms based on gradient descents are slow to converge. Furthermore, both analytical and experimental studies show that convergence of these algorithms depends on appropriate selection of the gain sequence {*η*[*k*]}. Moreover, it is proven [Chatterjee et al*.* Nov 97; Chatterjee et al*.* Mar 98; Chauvin 89] that if the gain sequence exceeds an upper bound, then the algorithms may diverge or converge to a false solution.

Since most of these algorithms are used for real-time (i.e., online) processing, it is especially difficult to determine an appropriate choice of the gain parameter at the start of the online process. Hence, it is important for wider applicability of these algorithms to

*   Speed up the convergence of the algorithms, and

*   Automatically select the gain parameter based on the current data sample.

### Objective Functions for Gradient-Based Adaptive PCA

Some of the objective functions discussed in Chapters [4](#522202_1_En_4_Chapter.xhtml) and [5](#522202_1_En_5_Chapter.xhtml) have been used by practitioners to derive accelerated adaptive PCA algorithms by using advanced nonlinear optimization techniques such as

*   Steepest descent (SD),

*   Conjugate direction (CD),

*   Newton-Raphson (NR), and

*   Recursive least squares (RLS).

These optimization methods lead to faster convergence of the algorithms compared to the gradient descent methods discussed in Chapters [4](#522202_1_En_4_Chapter.xhtml) and [5](#522202_1_En_5_Chapter.xhtml). They also help us compute a value of the gain sequence {*η*[*k*]}, which is not available in the gradient descent method. The only drawback is the additional computation needed by these improved algorithms. Note that each optimization method when applied to a different objective function leads to a new algorithm for adaptive PCA.

The first objective function that is used extensively in signal processing applications for accelerated PCA is the Rayleigh quotient (RQ) objective function described in Sections [4.​4](#522202_1_En_4_Chapter.xhtml#Sec10) and [5.​9](#522202_1_En_5_Chapter.xhtml#Sec36). Sarkar et al*.* [Sarkar et al*.* 89; Yang et al*.* 89] applied the steepest descent and conjugate direction methods to this objective function to compute the extremal (largest or smallest) eigenvectors. Fu and Dowling [Fu and Dowling 94, 95] generalized the conjugate direction algorithm to compute all eigenvectors. Zhu and Wang [Zhu and Wang 97] also used a conjugate direction method on a regularized total least squares version of this objective function. A survey of conjugate direction-based algorithms on the RQ objective function is found in [Yang et al*.* 89].

The second objective function that is used for accelerated PCA is the penalty function (PF) objective function discussed in Sections [4.​7](#522202_1_En_4_Chapter.xhtml#Sec24) and [5.​5](#522202_1_En_5_Chapter.xhtml#Sec16). Chauvin [Chauvin 97] presents a gradient descent algorithm based on the PF objective function and analyzes the landscape of this function. Mathew et al*.* [Mathew et al*.* 94, 95, 96] also use this objective function to offer a Newton-type algorithm for adaptive PCA.

The third objective function used for accelerated PCA is the information theoretic (IT) objective function discussed in Sections [4.​5](#522202_1_En_4_Chapter.xhtml#Sec14) and [5.​8](#522202_1_En_5_Chapter.xhtml#Sec31). Miao and Hua [Miao and Hua 98] present gradient descent and RLS algorithms for adaptive principal sub-space analysis.

The fourth objective function used for accelerated PCA is the XU objective function given in Sections [4.​6](#522202_1_En_4_Chapter.xhtml#Sec19) and [5.​4](#522202_1_En_5_Chapter.xhtml#Sec11). For example, Xu [Xu 93], Yang [Yang 95], Fu and Dowling [Fu and Dowling 94], Bannour and Azimi-Sadjadi [Bannour and Azimi-Sadjadi 95], and Miao and Hua [Miao and Hua 98] used variations of this objective function. As discussed in Section [5.​4](#522202_1_En_5_Chapter.xhtml#Sec11), there are several variations of this objective function including the mean squared error at the output of a two-layer linear auto-associative neural network. Xu derives an algorithm for adaptive principal sub-space analysis by using gradient descent. Yang uses gradient descent and recursive least squares optimization methods. Bannour and Azimi-Sadjadi also describe a recursive least squares-based algorithm for adaptive PCA with this objective function. Fu and Dowling reduce this objective function to one similar to the RQ objective function, which can be minimized by the conjugate direction methods due to Sarkar et al*.* [Sarkar et al*.* 89; Yang et al*.* 89]. They also compute the minor components by using an approximation and by employing the deflation technique.

### Outline of This Chapter

Any of the objective functions discussed in Chapters [4](#522202_1_En_4_Chapter.xhtml) and [5](#522202_1_En_5_Chapter.xhtml) can be used to obtain accelerated adaptive PCA algorithms by using nonlinear optimization techniques (on this objective function) such as

*   Gradient descent,

*   Steepest descent,

*   Conjugate direction,

*   Newton-Raphson, and

*   Recursive least squares.

I shall, however, use only one of these objective functions for the discussion in this chapter. I note that these analyses can be extended to the other objective functions in Chapters [4](#522202_1_En_4_Chapter.xhtml) and [5](#522202_1_En_5_Chapter.xhtml). My choice of objective function for this chapter is the XU deflation objective function discussed in Section [5.​4](#522202_1_En_5_Chapter.xhtml#Sec11).

Although gradient descent on the XU objective function (see Section [5.​4](#522202_1_En_5_Chapter.xhtml#Sec11)) produces the well-known Xu’s least mean square error reconstruction (LMSER) algorithm [Xu 93], the steepest descent, conjugate direction, and Newton-Raphson methods produce new adaptive algorithms for PCA [Chatterjee et al. Mar 00]. The penalty function (PF) deflation objective (see Section [5.​5](#522202_1_En_5_Chapter.xhtml#Sec16)) function has also been accelerated by the steepest descent, conjugate direction, and quasi-Newton methods of optimization by Kang et al*.* [Kang et al*.* 00].

I shall apply these algorithms to stationary and **non-stationary multi-dimensional** Gaussian data sequences. I experimentally show that the adaptive steepest descent, conjugate direction, and Newton-Raphson algorithms converge much faster than the traditional gradient descent technique due to Xu [Xu 93]. Furthermore, the new algorithms automatically select the gain sequence {*η*[*k*]} based on the current data sample. I further compare the steepest descent algorithm with state-of-the-art methods such as Yang’s Projection Approximation Subspace Tracking (PASTd) [Yang 95], Bannour and Sadjadi’s recursive least squares (RLS) [Bannour et al*.* 95], and Fu and Dowling’s conjugate gradient eigenstructure tracking (CGET1) [Fu and Dowling 94, 95] algorithms.

The XU deflation objective function for adaptive PCA algorithms is given in the Section [5.​4](#522202_1_En_5_Chapter.xhtml#Sec11) equation (5.12) as

![$$ J\left({\mathbf{w}}_k^i;{A}_k\right)=-2{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i+2\sum \limits_{j=1}^{i-1}{{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq1.png),     (6.1)

for *i*=1,…,*p*, where *A*[*k*]∈ℜ^(*n*X*n*) is the online observation matrix. I now apply different methods of nonlinear minimization to the objective function ![$$ J\left({\mathbf{w}}_k^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq2.png) in (6.1) to obtain various algorithms for adaptive PCA.

In Sections [6.2](#522202_1_En_6_Chapter.xhtml#Sec4), [6.3](#522202_1_En_6_Chapter.xhtml#Sec5), [6.4](#522202_1_En_6_Chapter.xhtml#Sec8), and [6.5](#522202_1_En_6_Chapter.xhtml#Sec10), I apply the gradient descent, steepest descent, conjugate direction, and Newton-Raphson optimization methods to the unconstrained XU objective function for PCA given in (6.1). Here I obtain new algorithms for adaptive PCA. In Section [6.6](#522202_1_En_6_Chapter.xhtml#Sec12), I present experimental results with stationary and non-stationary Gaussian sequences, thereby showing faster convergence of the new algorithms over traditional gradient descent adaptive PCA algorithms. I also compare the steepest descent algorithm with state-of-the-art algorithms. Section [6.7](#522202_1_En_6_Chapter.xhtml#Sec16) concludes the chapter.

## 6.2 Gradient Descent Algorithm

[Chatterjee et al. *IEEE Trans. on Neural Networks*, Vol. 11, No. 2, pp. 338-355, March 2000.]

The gradient of (6.1) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq3.png) is

![$$ {\mathbf{g}}_k^i=\left(1/2\right){\nabla}_{{\mathbf{w}}_k^i}J\left({\mathbf{w}}_k^i;{A}_k\right)=-2{A}_k{\mathbf{w}}_k^i+\sum \limits_{j=1}^i{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i+\sum \limits_{j=1}^i{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq4.png) for *i*=1,…,*p*.     (6.2)

Thus, the XU deflation adaptive gradient descent algorithm for PCA (see Section 5.4.2) is

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i-{\eta}_k{\mathbf{g}}_k^i={\mathbf{w}}_k^i+{\eta}_k\left(2{A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^i{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{\mathbf{w}}_k^i-\sum \limits_{j=1}^i{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq5.png) for *i*=1,…,*p*,      (6.3)

where *η*[*k*] is a decreasing gain constant. We can represent *A*[*k*] simply by its instantaneous value ![$$ {\mathbf{x}}_k{\mathbf{x}}_k^T $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq6.png) or by its recursive formula in Chapter [2](#522202_1_En_2_Chapter.xhtml) (Eq. 2.3 or 2.4). It is convenient to define a matrix ![$$ {W}_k=\left[{\mathbf{w}}_k^1\dots {\mathbf{w}}_k^p\right] $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq7.png) (*p*≤*n*), for which the columns are the *p* weight vectors that converge to the *p* principal eigenvectors of *A*[*k*] respectively. Then, (6.2) can be represented as (same as ([5.​14](#522202_1_En_5_Chapter.xhtml#Par65)) in Section [5.​4.​2](#522202_1_En_5_Chapter.xhtml#Sec13)):

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{A}_k{W}_k UT\left({W}_k^T{W}_k\right)-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq8.png),     (6.4)

where UT[⋅] sets all elements below the diagonal of its matrix argument to zero, thereby making it upper triangular. Note that (6.2) is the LMSER algorithm due to Xu [Xu 93] that was derived from a least mean squared error criterion of a feed-forward neural network (see Section [5.​4](#522202_1_En_5_Chapter.xhtml#Sec11)).

## 6.3 Steepest Descent Algorithm

[Chatterjee et al. *IEEE Trans. on Neural Networks*, Vol. 11, No. 2, pp. 338-355, March 2000.]

The adaptive steepest descent algorithm for PCA is obtained from ![$$ J\left({\mathbf{w}}_k^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq9.png) in (6.1) as

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i-{\alpha}_k^i{\mathbf{g}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq10.png),     (6.5)

where ![$$ {\mathbf{g}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq11.png) is given in (6.2) and ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq12.png) is a non-negative scalar minimizing ![$$ J\left({\mathbf{w}}_k^i-\alpha {\mathbf{g}}_k^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq13.png). Since we have an expression for ![$$ J\left({\mathbf{w}}_k^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq14.png) in (6.1), we minimize the function ![$$ J\left({\mathbf{w}}_k^i-\alpha {\mathbf{g}}_k^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq15.png) with respect to *α* and obtain the following cubic equation:

*c*[3]*α*³ + *c*[2]*α*² + *c*[1]*α* + *c*[0] = 0,     (6.6)

where

![$$ {c}_0=-{{\mathbf{g}}_k^i}^T{\mathbf{g}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq16.png), ![$$ {c}_1={{\mathbf{g}}_k^i}^T{H}_k^i{\mathbf{g}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq17.png), ![$$ {c}_2=-3\left({{\mathbf{g}}_k^i}^T{A}_k{\mathbf{g}}_k^i{{\mathbf{w}}_k^i}^T{\mathbf{g}}_k^i+{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{g}}_k^i{{\mathbf{g}}_k^i}^T{\mathbf{g}}_k^i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq18.png), ![$$ {c}_3=2{{\mathbf{g}}_k^i}^T{A}_k{\mathbf{g}}_k^i{{\mathbf{g}}_k^i}^T{\mathbf{g}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq19.png).

Here ![$$ {H}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq20.png) is the Hessian of ![$$ J\left({\mathbf{w}}_k^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq21.png) as follows:

![$$ {H}_k^i=-2{A}_k+2{A}_k{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T+{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T{A}_k+{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^iI+\sum \limits_{j=1}^i{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T+\sum \limits_{j=1}^i{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq22.png)     (6.7)

With known values of ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq23.png) and ![$$ {\mathbf{g}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq24.png), this cubic equation can be solved to obtain *α* that minimizes ![$$ J\left({\mathbf{w}}_k^i-\alpha {\mathbf{g}}_k^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq25.png). A description of the computation of *α* is given in next section.

We now represent the adaptive PCA algorithm (6.5) in the matrix form. We define the matrices:

![$$ {W}_k=\left[{\mathbf{w}}_k^1,\dots, {\mathbf{w}}_k^p\right] $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq26.png), ![$$ {G}_k=\left[{\mathbf{g}}_k^1,\dots, {\mathbf{g}}_k^p\right] $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq27.png), and ![$$ {\Gamma}_k=\mathrm{Diag}\left({\alpha}_k^1,\dots, {\alpha}_k^p\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq28.png).

Then, the adaptive steepest descent PCA algorithm is

![$$ {G}_k=-2{A}_k{W}_k+{W}_k UT\left({W}_k^T{A}_k{W}_k\right)+{A}_k{W}_k UT\left({W}_k^T{W}_k\right), $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq29.png)

*W*[*k* + 1] = *W*[*k*] − *G*[*k*]Γ[*k*].     (6.8)

Here UT[·] is the same as in (6.4).

### Computation of ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq30.png) for Steepest Descent

From *J*(**w**[*i*];*A*) in (6.1), we compute *α* that minimizes *J*(**w**[*i*] − *α***g**[*i*]; *A*), where

![$$ {\mathbf{g}}_i=-2A{\mathbf{w}}_i+{\mathbf{w}}_i{{\mathbf{w}}_i}^TA{\mathbf{w}}_i+\sum \limits_{j=1}^{i-1}{\mathbf{w}}_j{{\mathbf{w}}_j}^TA{\mathbf{w}}_i+A{\mathbf{w}}_i{{\mathbf{w}}_i}^T{\mathbf{w}}_i+\sum \limits_{j=1}^{i-1}A{\mathbf{w}}_j{{\mathbf{w}}_j}^T{\mathbf{w}}_i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq31.png).

We have

![$$ \frac{d J\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)}{d\alpha}=\frac{1}{2} tr\left[{\nabla}_{{\mathbf{w}}_i-\alpha {\mathbf{g}}_i}J\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)\frac{d{\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)}^T}{d\alpha}\right]=-\frac{1}{2}{\mathbf{g}}_i^T{\nabla}_{{\mathbf{w}}_i-\alpha {\mathbf{g}}_i}J\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right), $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_Equa.png)

where

![$$ \left(1/2\right){\nabla}_{{\mathbf{w}}_i-\alpha {\mathbf{g}}_i}J\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)=-2A\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)+\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right){\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)}^TA\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_Equb.png)

![$$ +A\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right){\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)}^T\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)+\sum \limits_{j=1}^{i-1}{\mathbf{w}}_j{{\mathbf{w}}_j}^TA\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right)+\sum \limits_{j=1}^{i-1}A{\mathbf{w}}_j{{\mathbf{w}}_j}^T\left({\mathbf{w}}_i-\alpha {\mathbf{g}}_i\right). $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_Equc.png)

Simplifying this equation, we obtain the following cubic equation:

![$$ {c}_3{\alpha}^3+{c}_2{\alpha}^2+{c}_1\alpha +{c}_0=0, $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_Equd.png)

where

![$$ {c}_0=-{\mathbf{g}}_i^T{\mathbf{g}}_i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq32.png), ![$$ {c}_1={\mathbf{g}}_i^T{H}_i{\mathbf{g}}_i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq33.png), ![$$ {c}_2=-3\left({\mathbf{g}}_i^TA{\mathbf{g}}_i{\mathbf{w}}_i^T{\mathbf{g}}_i+{\mathbf{w}}_i^TA{\mathbf{g}}_i{\mathbf{g}}_i^T{\mathbf{g}}_i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq34.png), ![$$ {c}_3=2{\mathbf{g}}_i^TA{\mathbf{g}}_i{\mathbf{g}}_i^T{\mathbf{g}}_i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq35.png).

Here *H*[*i*] is the Hessian of *J*(**w**[*i*]; *A*) given in (6.7).

It is well known that a cubic polynomial has at least one real root (two complex conjugate roots with a real root or three real roots). The roots can also be computed in closed form as shown in [Artin 91]. If a root is complex, then **w**[*i*] − *α***g**[*i*] is complex, and clearly this is not the root we are looking for. If we have three real roots, then we can either take the root corresponding to minimum *J*(**w**[*i*] − *α***g**[*i*]; *A*) or the one corresponding to 3*c*[3]*α*² + 2*c*[2]*α* + *c*[1] > 0.

### Steepest Descent Algorithm Code

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
I  = np.identity(nDim)
Weight = 1
nEpochs = 1
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrix A with current sample x
x = X1[:,iter]
x = x.reshape(nDim,1)
A = Weight * A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - Weight * A)
# Steepest Descent
G = -2 * A @ W2 + A @ W2 @ np.triu(W2.T @ W2) + \
W2 @ np.triu(W2.T @ A @ W2)
for i in range(nEA):
M = np.zeros(shape=(nDim,nDim))
for k in range(i):
wk = W2[:,k].reshape(nDim,1)
M = M + (A @ (wk @ wk.T) + (wk @ wk.T) @ A)
wi = W2[:,i].reshape(nDim,1)
F = - 2*A + 2*A @ (wi @ wi.T) + 2 * (wi @ wi.T) @ A + \
A * (wi.T @ wi) + (wi.T @ A @ wi) * I  +  M
gi = G[:,i].reshape(nDim,1)
a0 = np.asscalar(gi.T @ gi)
a1 = np.asscalar(- gi.T @ F @ gi)
a2 = np.asscalar(3 * ((wi.T @ A @ gi) @ (gi.T @ gi) + \
(gi.T @ A @ gi)*(wi.T @ gi)))
a3 = np.asscalar(- 2 * (gi.T @ A @ gi) @ (gi.T @ gi))
c  = np.array([a3, a2, a1, a0])
rts = np.roots(c)
rs = np.zeros(3)
r  = np.zeros(3)
J  = np.zeros(3)
cnt1 = 0
for k in range(3):
if np.isreal(rts[k]):
re = np.real(rts[k])
rs[cnt1] = re
r = W2[:,i] - re * G[:,i]
J[cnt1] = np.asscalar(-2*(r.T @ A @ r) + (r.T @ A @ r) * \
(r.T @ r) + (r.T @ M @ r))
cnt1 = cnt1 + 1
yy = min(J)
iyy = np.argmin(J)
alpha = rs[iyy]
W2[:,i] = (W2[:,i] - alpha * G[:,i]).T
```

## 6.4 Conjugate Direction Algorithm

[Chatterjee et al. *IEEE Trans. on Neural Networks*, Vol. 11, No. 2, pp. 338-355, March 2000.]

The adaptive conjugate direction algorithm for PCA can be obtained as follows:

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i+{\alpha}_k^i{\mathbf{d}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq36.png)

![$$ {\mathbf{d}}_{k+1}^i=-{\mathbf{g}}_{k+1}^i+{\beta}_k^i{\mathbf{d}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq37.png),     (6.9)

where ![$$ {\mathbf{g}}_{k+1}^i=\left(1/2\right){\nabla}_{{\mathbf{w}}_i}J\left({\mathbf{w}}_{k+1}^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq38.png). The gain constant ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq39.png) is chosen as *α* that minimizes ![$$ J\left({\mathbf{w}}_k^i+\alpha {\mathbf{d}}_k^i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq40.png). Similar to the steepest descent case, we obtain the following cubic equation:

*c*[3]*α*³ + *c*[2]*α*² + *c*[1]*α* + *c*[0] = 0,     (6.10)

where

![$$ {c}_0={{\mathbf{g}}_k^i}^T{\mathbf{d}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq41.png), ![$$ {c}_1={{\mathbf{d}}_k^i}^T{H}_k^i{\mathbf{d}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq42.png), ![$$ {c}_2=3\left({{\mathbf{d}}_k^i}^T{A}_k{\mathbf{d}}_k^i{{\mathbf{w}}_k^i}^T{\mathbf{d}}_k^i+{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{d}}_k^i{{\mathbf{d}}_k^i}^T{\mathbf{d}}_k^i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq43.png), ![$$ {c}_3=2{{\mathbf{d}}_k^i}^T{A}_k{\mathbf{d}}_k^i{{\mathbf{d}}_k^i}^T{\mathbf{d}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq44.png).

Here, ![$$ {\mathbf{g}}_k^i=\left(1/2\right){\nabla}_{{\mathbf{w}}_i}J\left({\mathbf{w}}_k^i;{A}_k\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq45.png) as given in (6.2). Equation (6.10) is solved to obtain *α* that minimizes ![$$ J\left({\mathbf{w}}_k^i+\alpha {\mathbf{d}}_k^i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq46.png). For the choice of ![$$ {\beta}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq47.png), we can use a number of methods such as Hestenes-Stiefel, Polak-Ribiere, Fletcher-Reeves, and Powell (described on Wikipedia).

We now represent the adaptive conjugate direction PCA algorithm (6.9) in the matrix form. We define the following matrices:

![$$ {W}_k=\left[{\mathbf{w}}_k^1,\dots, {\mathbf{w}}_k^p\right] $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq48.png), ![$$ {G}_k=\left[{\mathbf{g}}_k^1,\dots, {\mathbf{g}}_k^p\right] $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq49.png), ![$$ {D}_k=\left[{\mathbf{d}}_k^1,\dots, {\mathbf{d}}_k^p\right] $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq50.png),

![$$ {\Gamma}_k=\operatorname{diag}\left({\alpha}_k^1,\dots, {\alpha}_k^p\right), $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq51.png) and ![$$ {\Pi}_k=\operatorname{diag}\left({\beta}_k^1,\dots, {\beta}_k^p\right). $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq52.png)

Then, the adaptive conjugate direction PCA algorithm is

*W*[*k* + 1] = *W*[*k*] + *D*[*k*]Γ[*k*],

![$$ {G}_{k+1}=-2{A}_k{W}_{k+1}+{W}_{k+1} UT\left({W}_{k+1}^T{A}_k{W}_{k+1}\right)+{A}_k{W}_{k+1} UT\left({W}_{k+1}^T{W}_{k+1}\right), $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq53.png)

*D*[*k* + 1] =  − *G*[*k* + 1] + *D*[*k*]Π[*k*].     (6.11)

Here UT[·] is the same as in (6.4).

### Conjugate Direction Algorithm Code

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
I  = np.identity(nDim)
Weight = 1
nEpochs = 1
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrix A with current sample x
x = X1[:,iter]
x = x.reshape(nDim,1)
A = Weight * A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - Weight * A)
# Conjugate Direction Method
# Initialize D
G = -2 * A @ W2 + A @ W2 @ np.triu(W2.T @ W2) +
\W2 @ np.triu(W2.T @ A @ W2)
if (iter == 0):
D = G
# Update W
for i in range(nEA):
gi = G[:,i].reshape(nDim,1)
wi = W2[:,i].reshape(nDim,1)
di = D[:,i].reshape(nDim,1)
M = np.zeros(shape=(nDim,nDim))
for k in range(i):
wk = W2[:,k].reshape(nDim,1)
M = M + (A @ (wk @ wk.T) + (wk @ wk.T) @ A)
F = - 2*A + 2*A @ (wi @ wi.T) + 2 * (wi @ wi.T) @ A + A * \
(wi.T @ wi) + (wi.T @ A @ wi) * I  +  M
a0 = np.asscalar(gi.T @ di)
a1 = np.asscalar(- di.T @ F @ di)
a2 = np.asscalar(3 * ((wi.T @ A @ di) * (di.T @ di) + \
(di.T @ A @ di) * (wi.T @ di)))
a3 = np.asscalar(- 2 * (di.T @ A @ di) * (di.T @ di))
c  = np.array([a3, a2, a1, a0])
rts = np.roots(c)
rs = np.zeros(3)
r  = np.zeros(3)
J  = np.zeros(3)
cnt1 = 0
for k in range(3):
if np.isreal(rts[k]):
re = np.real(rts[k])
rs[cnt1] = re
r = (W2[:,i] - re * di.T).reshape(nDim,1)
J[cnt1] = np.asscalar(-2*(r.T @ A @ r) + (r.T @ A @ r) * \
(r.T @ r) + (r.T @ M @ r))
cnt1 = cnt1 + 1
yy = min(J)
iyy = np.argmin(J)
alpha = rs[iyy]
W2[:,i] = W2[:,i] - alpha * di.T
# Update d
gi = G[:,i].reshape(nDim,1)
wi = W2[:,i].reshape(nDim,1)
di = D[:,i].reshape(nDim,1)
M = np.zeros(shape=(nDim,nDim))
for k in range(i):
wk = W2[:,k].reshape(nDim,1)
M = M + (A @ (wk @ wk.T) + (wk @ wk.T) @ A)
F = - 2*A + 2*A @ (wi @ wi.T) + 2 * (wi @ wi.T) @ A + \
A * (wi.T @ wi) + (wi.T @ A @ wi) * I  +  M
beta = (gi.T @ F @ di) / (di.T @ F @ di)
di = gi + 1*beta*di
D[:,i] = di.T
```

## 6.5 Newton-Raphson Algorithm

[Chatterjee et al. *IEEE Trans. on Neural Networks*, Vol. 11, No. 2, pp. 338-355, March 2000.]

The adaptive Newton-Raphson algorithm for PCA is

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i-{\alpha}_k^i{\left({H}_k^i\right)}^{-1}{\mathbf{g}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq54.png),     (6.12)

where ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq55.png) is a non-negative scalar and ![$$ {H}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq56.png) is the online Hessian given in (6.7). The search parameter ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq57.png) is commonly selected: (1) by minimizing ![$$ J\left({\mathbf{w}}_k^i+\alpha {\mathbf{d}}_k^i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq58.png) where ![$$ {\mathbf{d}}_k^i=-{\left({H}_k^i\right)}^{-1}{\mathbf{g}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq59.png); (2) as a scalar constant; or (3) as a decreasing sequence {![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq60.png)} such that ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq61.png)→0 as *k*→∞.

The main concerns in this algorithm are that ![$$ {H}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq62.png) should be positive definite, and that we should adaptively obtain an estimate of ![$$ {\left({H}_k^i\right)}^{-1} $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq63.png) in order to make the algorithm computationally efficient. These two concerns are addressed if we approximate the Hessian by dropping the term ![$$ \left(-{A}_k+{A}_k{{\mathbf{w}}_k^i}^T{\mathbf{w}}_k^i\right) $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq64.png), which is close to 0 for ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq65.png)close to the solution. The new Hessian is

![$$ {H}_k^i\approx {{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^iI-{\tilde{A}}_k^i+2{A}_k{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T+2{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T{A}_k, $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq66.png)     (6.13)

where

![$$ {\tilde{A}}_k^i={A}_k-\sum \limits_{j=1}^{i-1}{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k-\sum \limits_{j=1}^{i-1}{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T. $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_Eque.png)

We can compute ![$$ {A}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq67.png) by an iterative equation in *i* as follows:

![$$ {\tilde{A}}_k^{i+1}={\tilde{A}}_k^i-{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T{A}_k-{A}_k{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T. $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_Equf.png)

Inverting this Hessian consists of inverting the matrix ![$$ {B}_k^i={{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^iI-{\tilde{A}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq68.png) and two rank-one updates. An approximate inverse of this matrix ![$$ {B}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq69.png) is given by

![$$ {\left({B}_k^i\right)}^{-1}={\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^iI-{\tilde{A}}_k^i\right)}^{-1}\approx \frac{I+{\tilde{A}}_k^i/{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i} $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq70.png).     (6.14)

An adaptive algorithm for inverting the Hessian ![$$ {H}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq71.png) in (6.13) can be obtained by two rank-one updates. Let’s define

![$$ {C}_k^i={B}_k^i+2{A}_k{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T. $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq72.png)     (6.15)

Then from (6.13), an update formula for ![$$ {\left({H}_k^i\right)}^{-1} $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq73.png) is

![$$ {\left({H}_k^i\right)}^{-1}={\left({C}_k^i\right)}^{-1}-\frac{2{\left({C}_k^i\right)}^{-1}{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T{A}_k{\left({C}_k^i\right)}^{-1}}{1+2{{\mathbf{w}}_k^i}^T{A}_k{\left({C}_k^i\right)}^{-1}{\mathbf{w}}_k^i}, $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq74.png)     (6.16)

where ![$$ {\left({C}_k^i\right)}^{-1} $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq75.png) is obtained from (6.15) as

![$$ {\left({C}_k^i\right)}^{-1}={\left({B}_k^i\right)}^{-1}-\frac{2{\left({B}_k^i\right)}^{-1}{A}_k{\mathbf{w}}_k^i{{\mathbf{w}}_k^i}^T{\left({B}_k^i\right)}^{-1}}{1+2{{\mathbf{w}}_k^i}^T{\left({B}_k^i\right)}^{-1}{A}_k{\mathbf{w}}_k^i} $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq76.png)     (6.17)

and ![$$ {\left({B}_k^i\right)}^{-1} $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq77.png) is given in (6.14).

### Newton-Raphson Algorithm Code

The following Python code implements this algorithm with data `X[nDim,nSamples]`:

```
from numpy import linalg as la
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W1 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W2 = W1
I  = np.identity(nDim)
Weight = 1
nEpochs = 1
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrix A with current sample x
x = X1[:,iter]
x = x.reshape(nDim,1)
A = Weight * A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - Weight * A)
# Newton Rhapson
G = -2* A @ W4 + A@ W4@ np.triu(W4.T @ W4) + W4@ np.triu(W4.T @ A @ W4)
# Update W
for i in range(nEA):
M = np.zeros(shape=(nDim,nDim))
for k in range(i):
wk = W4[:,k].reshape(nDim,1)
M = M + (A @ (wk @ wk.T) + (wk @ wk.T) @ A)
wi = W4[:,i].reshape(nDim,1)
F = - 2*A + 2*A @ (wi @ wi.T) + 2 * (wi @ wi.T) @ A + \
A * (wi.T @ wi) + (wi.T @ A @ wi) * I  +  M
lam = wi.T @ A @ wi
Atilde = A
if (iter > 0):
invB = (I + (Atilde/lam))/lam
invC = invB- (2*invB@ A @wi @wi.T @ invB)/(1 + 2*wi.T@ invB@ A @ wi)
invF = invC- (2*invC@ wi @wi.T @A @ invC)/(1 + 2*wi.T@ A @invC @ wi)
gi = G[:,i].reshape(nDim,1)
di = -invF @ gi
a0 = np.asscalar(gi.T @ di)
a1 = np.asscalar(di.T @ F @ di)
a2 = np.asscalar(3* ((wi.T @ A @ di) @ (di.T @ di) + \
(di.T @ A @ di)*(wi.T @ di)))
a3 = np.asscalar(2 * (di.T @ A @ di) @ (di.T @ di))
c  = np.array([a3, a2, a1, a0])
rts = np.roots(c)
rs = np.zeros(3)
r  = np.zeros(3)
J  = np.zeros(3)
cnt1 = 0
for k in range(3):
if np.isreal(rts[k]):
re = np.real(rts[k])
rs[cnt1] = re
r = W4[:,i] + re * di.reshape(nDim)
J[cnt1] = np.asscalar(-2*(r.T @ A @ r) + (r.T @ A @ r) * \
(r.T @ r) + (r.T @ M @ r))
cnt1 = cnt1 + 1
iyy = np.argmin(J)
alpha = rs[iyy]
W4[:,i] = (W4[:,i] + alpha * di.reshape(nDim)).T
```

## 6.6 Experimental Results

I did two sets of experiments to test the performance of the accelerated PCA algorithms. I did the first set of experiments on *stationary* Gaussian data and the second set on *non*-*stationary* Gaussian data. I then compared the steepest descent algorithm against state-of-the-art adaptive PCA algorithms like Yang’s Projection Approximation Subspace Tracking, Bannour and Sadjadi’s Recursive Least Squares, and Fu and Dowling’s Conjugate Gradient Eigenstructure Tracking algorithms.

### Experiments with Stationary Data

I generated 2000 samples of 10-dimensional Gaussian data (i.e., *n*=10) with mean zero and covariance given below. Note that this covariance matrix is obtained from the first covariance matrix in [Okada and Tomita 85] multiplied by 2\. The covariance matrix is

![](images/522202_1_En_6_Chapter/522202_1_En_6_Figa_HTML.png)

The eigenvalues of the covariance matrix are

11.7996, 5.5644, 3.4175, 2.0589, 0.7873, 0.5878, 0.1743, 0.1423, 0.1213, 0.1007.

Clearly, the first four eigenvalues are significant and I adaptively compute the corresponding eigenvectors (i.e., *p*=4). See Figure [6-1](#522202_1_En_6_Chapter.xhtml#Fig1) for the plots of the 10-dimensional random stationary data.

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig1_HTML.png)

Figure 6-1

10-dimensional stationary random normal data

In order to compute the online data sequence {*A*[*k*]}, I generated random data vectors {**x**[*k*]} from the above covariance matrix. I generated {*A*[*k*]} from {**x**[*k*]} by using algorithm (2.3) in Chapter [2](#522202_1_En_2_Chapter.xhtml). I computed the correlation matrix *A* after collecting all 500 samples **x**[*k*] as

![$$ A=\frac{1}{2000}\sum \limits_{i=1}^{2000}{\mathbf{x}}_i{\mathbf{x}}_i^T. $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_Equg.png)

I refer to the eigenvectors and eigenvalues computed from this *A* by a standard numerical analysis method [Golub and VanLoan 83] as the *actual values*.

I used the adaptive gradient descent (6.4), steepest descent (6.8), conjugate direction (6.11), and Newton-Raphson (6.12) algorithms on the random data sequence {*A*[*k*]}. I started all algorithms with *W*[0] = 0.1*ONE, where ONE is a 10 X 4 matrix whose all elements are ones. In order to measure the convergence and accuracy of the algorithms, I computed the direction cosine at *k*^(*th*) update of each adaptive algorithm as

Direction cosine (*k*) = ![$$ \frac{\left|{{\mathbf{w}}_k^i}^T{\boldsymbol{\upvarphi}}_i\right|}{\left\Vert {\mathbf{w}}_k^i\left\Vert \right\Vert {\boldsymbol{\upvarphi}}_i\right\Vert } $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq78.png),     (6.18)

where ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq79.png) is the estimated eigenvector of *A*[*k*] at *k*^(*th*) update and **ϕ**[*i*] is the actual eigenvector computed from all collected samples by a conventional numerical analysis method.

Figures [6-2](#522202_1_En_6_Chapter.xhtml#Fig2) through [6-4](#522202_1_En_6_Chapter.xhtml#Fig4) show the iterates of the four algorithms to compute the first four principal eigenvectors of *A*. For the gradient descent (6.4) algorithm, I used *η*[*k*]=1/(400+*k*). For the conjugate direction method, I used the Hestenes-Stiefel [Nonlinear conjugate gradient method, Wikipedia] method (see Section [6.4](#522202_1_En_6_Chapter.xhtml#Sec8)) to compute ![$$ {\beta}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq80.png). For the steepest descent, conjugate direction, and Newton-Raphson methods, I chose ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq81.png) by solving a cubic equation as described in Sections [6.3](#522202_1_En_6_Chapter.xhtml#Sec5), [6.4](#522202_1_En_6_Chapter.xhtml#Sec8), and [6.5](#522202_1_En_6_Chapter.xhtml#Sec10), respectively.

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig4_HTML.png)

Figure 6-4

Convergence of the first four principal eigenvectors of A by the gradient descent (6.4) and Newton-Raphson (6.12) algorithms for stationary data

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig3_HTML.png)

Figure 6-3

Convergence of the first four principal eigenvectors of A by the gradient descent (6.4) and conjugate direction (6.11) algorithms for stationary data

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig2_HTML.png)

Figure 6-2

Convergence of the first four principal eigenvectors of A by the gradient descent (6.4) and steepest descent (6.8) algorithms for stationary data

It is clear from Figures [6-2](#522202_1_En_6_Chapter.xhtml#Fig2) through [6-4](#522202_1_En_6_Chapter.xhtml#Fig4) that the steepest descent, conjugate direction, and Newton-Raphson algorithms converge faster than the gradient descent algorithm in spite of a careful selection of *η*[*k*] for the gradient descent algorithm. Besides, the new algorithms do not require ad-hoc selections of *η*[*k*]. Instead, the gain parameters ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq82.png) and ![$$ {\beta}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq83.png) are computed from the online data sequence.

Comparison between the four algorithms show small differences between them for the first four principal eigenvectors of *A*. Among the three faster converging algorithms, the steepest descent algorithm (6.8) requires the smallest amount of computation per iteration. Therefore, these experiments show that the steepest descent adaptive algorithm (6.8) is most suitable for optimum speed and computation among the four algorithms presented here.

### Experiments with Non-Stationary Data

In order to demonstrate the tracking ability of the algorithms with non-stationary data, I generated 500 samples of zero-mean 10-dimensional Gaussian data (i.e., *n*=10) with the covariance matrix stated before. I then abruptly changed the data sequence by generating 1,000 samples of zero-mean 10-dimensional Gaussian data with the covariance matrix below (the fifth covariance matrix from [Okada and Tomita 85] multiplied by 4):

![](images/522202_1_En_6_Chapter/522202_1_En_6_Figb_HTML.png)

The eigenvalues of this covariance matrix are

23.3662, 16.5698, 6.8611, 1.8379, 1.5452, 0.7010, 0.3851, 0.3101, 0.2677, 0.2278,

which are drastically different from the previous eigenvalues. Figure [6-5](#522202_1_En_6_Chapter.xhtml#Fig5) plots the 10-dimensional non-stationary data.

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig5_HTML.png)

Figure 6-5

10-dimensional non-stationary random data with abrupt changes after 500 samples

I generated {*A*[*k*]} from {**x**[*k*]} by using the algorithm (2.5 in Chapter [2](#522202_1_En_2_Chapter.xhtml)) with *β*=0.995\. I used the adaptive gradient descent (6.4), steepest descent (6.8), conjugate direction (6.11), and Newton-Raphson (6.12) algorithms on the random observation sequence {*A*[*k*]} and measured the convergence accuracy of the algorithms by computing the direction cosine at *k*^(*th*) update of each adaptive algorithm as shown in (6.18). I started all algorithms with *W*[0] = 0.1*ONE, where ONE is a 10 X 4 matrix whose all elements are ones. Here again I computed the first four eigenvectors (i.e., *p*=4).

Figures [6-6](#522202_1_En_6_Chapter.xhtml#Fig6) through [6-8](#522202_1_En_6_Chapter.xhtml#Fig8) show the iterates of the four algorithms to compute the first four principal eigenvectors of the two covariance matrices described before. For the conjugate direction method, I used the Hestenes-Stiefel [nonlinear conjugate gradient method, Wikipedia] method to compute ![$$ {\beta}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq84.png). For the steepest descent, conjugate direction, and Newton-Raphson methods, I chose ![$$ {\alpha}_k^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq85.png) by solving a cubic equation as described in Sections [6.3](#522202_1_En_6_Chapter.xhtml#Sec5) through [6.5](#522202_1_En_6_Chapter.xhtml#Sec10).

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig8_HTML.jpg)

Figure 6-8

Convergence of the first four principal eigenvectors of two covariance matrices by the gradient descent (6.4) and Newton-Raphson (6.12) algorithms for non-stationary data

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig7_HTML.jpg)

Figure 6-7

Convergence of the first four principal eigenvectors of two covariance matrices by the gradient descent (6.4) and conjugate direction (6.11) algorithms for non-stationary data

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig6_HTML.jpg)

Figure 6-6

Convergence of the first four principal eigenvectors of two covariance matrices by the gradient descent (6.4) and steepest descent (6.8) algorithms for non-stationary data

Once again, it is clear from Figures [6-6](#522202_1_En_6_Chapter.xhtml#Fig6) through [6-8](#522202_1_En_6_Chapter.xhtml#Fig8) that the steepest descent, conjugate direction, and Newton-Raphson algorithms converge faster and track the changes in data much better than the traditional gradient descent algorithm. In some cases, such as Figure [6-6](#522202_1_En_6_Chapter.xhtml#Fig6) for the third principal eigenvector, the gradient descent algorithm fails as the data sequence changes, but the new algorithms perform correctly.

Comparison between the four algorithms in Figure [6-8](#522202_1_En_6_Chapter.xhtml#Fig8) show small differences between them for the first four principal eigenvectors. Once again, among the three faster converging algorithms, since the steepest descent algorithm (6.8) requires the smallest amount of computation per iteration, it is most suitable for optimum speed and computation.

### Comparison with State-of-the-Art Algorithms

I compared the steepest descent algorithm (6.8) with Yang’s PASTd algorithm, Bannour and Sadjadi’s RLS algorithm, and Fu and Dowling’s CGET1 algorithm. I first tested the four algorithms on the stationary data described in Section 6.6.1.

I define ONE as a 10X4 matrix whose all elements are ones. The initial values for each algorithm are as follows:

1.  The steepest descent algorithm:

*W*[0] = 0.1*ONE.

1.  Yang’s PASTd algorithm:

*W*[0] = 0.1*ONE, *β*=0.997 and ![$$ {d}_0^i=0.2 $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq86.png) for *i* = 1, 2, …, *p* (*p*≤*n*).

1.  Bannour and Sadjadi’s RLS algorithm:

*W*[0] = 0.1*ONE and *P*[0] = ONE.

1.  Fu and Dowling’s CGET1 algorithm:

*W*[0] = 0.1*ONE and ![$$ {A}_0={\mathbf{x}}_k{\mathbf{x}}_k^T $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq87.png).

I found that the performance of the PASTd and RLS algorithms depended considerably on the initial choices of ![$$ {d}_0^i $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq88.png) and *P*[0] respectively. I, therefore, chose the initial values that gave the best results for most experiments. The results of this experiment are shown in Figure [6-9](#522202_1_En_6_Chapter.xhtml#Fig9).

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig9_HTML.jpg)

Figure 6-9

Convergence of the first four principal eigenvectors of A by steepest descent (6.8), PASTd, RLS, and CGET1 algorithms for stationary data

Observe from Figure [6-9](#522202_1_En_6_Chapter.xhtml#Fig9) that the steepest descent and CGET1 algorithms perform quite well for all four principal eigenvectors. The RLS performed a little better than the PASTd algorithm for the minor eigenvectors. For the major eigenvectors, all algorithms performed well. The differences between the algorithms were evident for the minor (third and fourth) eigenvectors.

I next applied the four algorithms on non-stationary data described in Section 6.6.2 with *β*=0.995 in eq. (2.5, Chapter [2](#522202_1_En_2_Chapter.xhtml)). The results of this experiment are shown in Figure [6-10](#522202_1_En_6_Chapter.xhtml#Fig10).

![](images/522202_1_En_6_Chapter/522202_1_En_6_Fig10_HTML.jpg)

Figure 6-10

Convergence of the first four principal eigenvectors of two covariance matrices by the steepest descent (6.8), PASTd, RLS, and CGET1 algorithms for non-stationary data

Observe that the steepest descent and CGET1 algorithms perform quite well for all four principal eigenvectors. The PASTd algorithm performs better than the RLS algorithm in handling non-stationarity. This is expected since the PASTd algorithm accounts for non-stationarity with a forgetting factor of *β*=0.995, whereas the RLS algorithm has no such option.

## 6.7 Concluding Remarks

I presented an unconstrained objective function to obtain various new adaptive algorithms for PCA by using nonlinear optimization methods such as gradient descent, steepest descent, conjugate gradient, and Newton-Raphson. Comparison among these algorithms with stationary and non-stationary data show that the SD, CG, and NR algorithms have faster tracking abilities compared to the GD algorithm.

Further consideration should be given to the computational complexity of the algorithms. SD, CG, and NR algorithms have computational complexity of *O*(*pn*²). If, however, we use the estimate ![$$ {A}_k={\mathbf{x}}_k{\mathbf{x}}_k^T $$](images/522202_1_En_6_Chapter/522202_1_En_6_Chapter_TeX_IEq89.png) instead of (6.4) in the GD algorithm, then the computational complexity drops to *O*(*pn*), although the convergence gets slower. The CGET1 algorithm has complexity *O*(*pn*²). The PASTd and RLS algorithms have complexity *O*(*pn*). However, their convergence is slower than the SD and CGET1 algorithms as shown in Figures [6-9](#522202_1_En_6_Chapter.xhtml#Fig9) and [6-10](#522202_1_En_6_Chapter.xhtml#Fig10). Further note that the GD algorithm can be implemented by parallel architecture as shown by the examples in [Cichocki and Unbehauen 93].

# 7. Generalized Eigenvectors

## 7.1 Introduction and Use Cases

This chapter is concerned with the adaptive solution of the generalized eigenvalue problems *A*Φ=*B*ΦΛ, *AB*Φ=ΦΛ, and *BA*Φ=ΦΛ, where *A* and *B* are real, symmetric, *n*X*n* matrices and *B* is positive definite. In particular, we shall consider the problem *A*Φ=*B*ΦΛ, although the remaining two problems are similar. The matrix pair (pencil) (*A*,*B*) is commonly referred to as a *symmetric-definite pencil* [Golub and VanLoan 83].

As seen before, the conventional (numerical analysis) method for evaluating Φ and Λ requires the computation of (*A*,*B*) after collecting all of the samples, and then the application of a numerical procedure [Golub and VanLoan 83]; in other words, the approach works in a *batch* fashion. In contrast, for the online case, matrices (*A*,*B*) are unknown. Instead, there are available two sequences of random matrices {*A*[*k*],*B*[*k*]} with lim[*k→∞*]*E*[*A*[*k*]]=*A* and lim[*k→∞*]*E*[*B*[*k*]]=*B*. For every sample (*A*[*k*],*B*[*k*]), we need to obtain the current estimates (Φ[*k*],Λ[*k*]) of (Φ,Λ) respectively, such that (Φ[*k*],Λ[*k*]) converge strongly to (Φ,Λ).

### Application of GEVD in Pattern Recognition

In pattern recognition, there are problems where we are given samples (**x∈**ℜ^(*n*)) from different populations or pattern classes. The well-known problem of linear discriminant analysis (LDA) [Chatterjee et al. Nov 97, May 97, Mar 97] seeks a transform *W∈*ℜ^(*n*X*p*) (*p≤n*), such that the interclass distance (measured by the scatter of the patterns around their mixture mean) is maximized, while at the same time the intra-class distance (measured by the scatter of the patterns around their respective class means) is as small as possible. The objective of this transform is to group the classes into well-separated clusters. The former scatter matrix, known as the mixture scatter matrix, is denoted by *A*, and the latter matrix, known as the within-class scatter matrix, is denoted by *B* [Fukunaga 90]. When the first column **w** of *W* is needed (i.e., *p*=1), the problem can be formulated in the constrained optimization framework as

Maximize **w**^(*T*)*A***w** subject to **w**^(*T*)*B***w** = 1.     (7.1)

A twin problem to (7.1) is to maximize the Rayleigh quotient criterion [Golub and VanLoan 83] with respect to **w**:

![$$ J\left(\mathbf{w};A,B\right)=\frac{{\mathbf{w}}^TA\mathbf{w}}{{\mathbf{w}}^TB\mathbf{w}} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq1.png).     (7.2)

A solution to (7.1) or (7.2) leads to the generalized eigen-decomposition problem *A***w**=*λB***w**, where *λ* is the largest generalized eigenvalue of *A* with respect to *B*. In general, the *p* columns of *W* are the *p*≤*n* orthogonal unit generalized eigenvectors **ϕ**[1],...,**ϕ**[*p*] of *A* with respect to *B* where

![$$ {\boldsymbol{\upvarphi}}_i^TA{\boldsymbol{\upvarphi}}_j={\lambda}_i{\delta}_{ij} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq2.png), and ![$$ {\boldsymbol{\upvarphi}}_i^TB{\boldsymbol{\upvarphi}}_j={\delta}_{ij} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq3.png) for *i*=1,…,*p*,     (7.3)

where λ[1]> ... >λ[*p*]>λ[*p*+1]≥ ... ≥λ[*n*]>0 are the *p* largest generalized eigenvalues of *A* with respect to *B* in descending order of magnitude. In summary, LDA is a powerful feature extraction tool for the class separability feature [Chatterjee May 97], and our adaptive algorithms are suited to this.

### Application of GEVD in Signal Processing

Next, let’s discuss an analogous problem of detecting a desired signal in the presence of interference. Here, we seek the optimum linear transform *W* for weighting the signal plus interference such that the desired signal is detected with maximum power and minimum interference. Given the matrix pair (*A*,*B*), where *A* is the correlation matrix of the signal plus interference plus noise and *B* is the correlation matrix of interference plus noise, we can formulate the signal detection problem as the constrained maximization problem in (7.1). Here, we maximize the signal power and minimize the power of the interference. The solution for *W* consists of the *p*≤*n* largest generalized eigenvectors of the matrix pencil (*A*,*B*). Adaptive generalized eigen-decomposition algorithms also allow the tracking of slow changes in the incoming data [Chatterjee et al. Nov 97, Mar 97; Chen et al. 2000].

### Methods for Generalized Eigen-Decomposition

We first define the problem for the non-adaptive case. Each of the three generalized eigenvalue problems (*A*Φ=*B*ΦΛ, *AB*Φ=ΦΛ, and *BA*Φ=ΦΛ, where *A* and *B* are real, symmetric, *n*X*n* matrices and *B* is positive definite) can be reduced to a standard symmetric eigenvalue problem using a Cholesky factorization of *B* as either *B*=*LL*^(*T*) or *B*=*U*^(*T*)*U*. With *B* = *LL*^(*T*), we can write *A*Φ=*B*ΦΛ as

(*L*^(−1)*AL*^(−*T*))(*L*^(*T*)Φ) = (*L*^(*T*)Φ)Λ or *C*Ψ = ΨΛ.     (7.4)

Here *C* is the symmetric matrix *C* = *L*^(–1) *A L*^(–*T*) and Ψ = *L*^(*T*) Φ. Table [7-1](#522202_1_En_7_Chapter.xhtml#Tab1) summarizes how each of the three types of problems can be reduced to the standard form *C*Ψ=ΨΛ, and how the eigenvectors Φ of the original problem may be recovered from the eigenvectors Ψ of the reduced problem.

Table 7-1

Types of Generalized Eigen-Decomposition problems and their solutions

| Type of Problem | Factorization of *B* | Reduction | Generalized Eigenvectors |
| --- | --- | --- | --- |
| *AΦ*=*BΦ*Λ | *B* = *LL*^(*T*) | *C* = *L*^(–1) *A L*^(–*T*) | Φ = *L*^(–*T*) Ψ |
| *B* = *U*^(*T*)*U* | *C* = *U*^(–*T*) *A U*^(–1) | Φ = *U*^(–1) Ψ |
| *ABΦ*=ΦΛ | *B* = *LL*^(*T*) | *C* = *L*^(*T*) *A L* | Φ= *L*^(–*T*) Ψ |
| *B* = *U*^(*T*)*U* | *C* = *U A U*^(*T*) | Φ = *U*^(–1) Ψ |
| *BAΦ*=ΦΛ | *B* = *LL*^(*T*) | *C* = *L*^(*T*) *A L* | Φ = *L* Ψ |
| *B* = *U*^(*T*)*U* | *C* = *U A U*^(*T*) | Φ = *U*^(*T*) Ψ |

In the adaptive case, we can extend these techniques by first (adaptively) computing a matrix *W*[*k*] for each sample *B*[*k*], where *W*[*k*] tends to the inverse Cholesky factorization *L*^(–1) of *B* with probability one (w.p.1) as *k→*∞. Any of the algorithms in Chapter [3](#522202_1_En_3_Chapter.xhtml) can be considered here. Next, we consider a sequence {![$$ {C}_k={W}_{k-1}{A}_k{W}_{k-1}^T $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq4.png)}, which is used to adaptively compute a matrix *V*[*k*], where *V*[*k*] tends to the eigenvector matrix of lim[*k→∞*]*E*[*C*[*k*]] w.p.1 as *k→*∞. Any of the algorithms in Chapters [5](#522202_1_En_5_Chapter.xhtml) and [6](#522202_1_En_6_Chapter.xhtml) can be considered for this purpose. In conjunction, the two steps yield *W*[*k*]*V*[*k*], which is proven to converge w.p.1 to Φ as *k→*∞. Thus, the two steps can proceed *simultaneously* and converge strongly to the eigenvector matrix Φ. A full description of this method is given in [Chatterjee et al. May 97, Mar 97]. In this chapter, I offer a variety of new techniques to solve the generalized eigen-decomposition problem for the adaptive case.

### Outline of This Chapter

In Section [7.2](#522202_1_En_7_Chapter.xhtml#Sec6), I list the objective functions from which I derive the adaptive generalized eigen-decomposition algorithms. In Sections [7.3](#522202_1_En_7_Chapter.xhtml#Sec9), [7.4](#522202_1_En_7_Chapter.xhtml#Sec14), and [7.5](#522202_1_En_7_Chapter.xhtml#Sec19), I present adaptive algorithms for the homogeneous, deflation, and weighted variations, respectively, from the OJA objective function. In Sections [7.6](#522202_1_En_7_Chapter.xhtml#Sec24), [7.7](#522202_1_En_7_Chapter.xhtml#Sec29), and [7.8](#522202_1_En_7_Chapter.xhtml#Sec34), I analyze the same three variations for the mean squared error (XU) objective function and convergence proofs for the deflation case. In Sections [7.9](#522202_1_En_7_Chapter.xhtml#Sec39), [7.10](#522202_1_En_7_Chapter.xhtml#Sec44), and [7.11](#522202_1_En_7_Chapter.xhtml#Sec45), I discuss algorithms derived from the penalty function (PF) objective function. In Sections 7.12, 7.13, and 7.14, I consider the augmented Lagrangian 1 (AL1) objective function, and in Sections 7.15, 7.16, and 7.17, I present the augmented Lagrangian 2 (AL2) objective function. In Sections 7.18, 7.19, and 7.20, I present the information theory (IT) criterion, and in Sections 7.21, 7.22, and 7.23, I describe the Rayleigh quotient (RQ) criterion. In Section 7.24, I discusses the experimental results, and in Section 7.25, I present conclusions.

## 7.2 Algorithms and Objective Functions

Similar to the PCA algorithms (Chapter [5](#522202_1_En_5_Chapter.xhtml)), in this chapter, I present several adaptive algorithms for generalized eigenvector computation. I consider two asymptotically stationary sequences {**x**[*k*]∈ℜ^(*n*)} and {**y**[*k*]∈ℜ^(*n*)} that have been centered to zero mean. We can represent the corresponding online correlation matrices {*A*[*k*],*B*[*k*]} of {**x**[*k*],**y**[*k*]} either by their instantaneous values {![$$ {\mathbf{x}}_k{\mathbf{x}}_k^T $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq5.png),![$$ {\mathbf{y}}_k{\mathbf{y}}_k^T $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq6.png)} or by their running averages by (2.3). If, however, {**x**[*k*],**y**[*k*]} are non-stationary, we can construct correlation matrices {*A*[*k*],*B*[*k*]} out of data samples {**x**[*k*],**y**[*k*]} by (2.5).

### Summary of Objective Functions for Adaptive GEVD Algorithms

Conforming to the methodology in Section [2.​3](#522202_1_En_2_Chapter.xhtml#Sec3), for each algorithm, I describe objective functions and derive the adaptive algorithms for them. The objective functions are

*   Oja’s objective function (OJA),

*   Xu’s mean squared error objective function (XU),

*   Penalty function method (PF),

*   Augmented Lagrangian Method 1 (AL1),

*   Augmented Lagrangian Method 2 (AL2),

*   Information theory criterion (IT), and

*   Rayleigh quotient criterion (RQ).

Same as the PCA case, there are three variations of algorithms derived from each objective function. They are

1.  *Homogeneous Adaptive Rule:* These algorithms do not compute the true normalized generalized eigenvectors with decreasing eigenvalues.

2.  *Deflation Adaptive Rule:* Here, we produce unit generalized eigenvectors with decreasing eigenvalues. However, the training is sequential, thereby making the training process harder for parallel implementations.

3.  *Weighted Adaptive Rule:* These algorithms are obtained by using a different scalar weight for each generalized eigenvector, making them normalized and in the order of decreasing eigenvalues.

### Summary of Generalized Eigenvector Algorithms

For all algorithms, I describe an *objective function J*(**w**[*i*]; *A*, *B*) and an update rule of the form

![$$ {W}_{k+1}={W}_k+{\eta}_kh\left({W}_k,{A}_k,{B}_k\right), $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_Equa.png)

where *h*(*W*[*k*],*A*[*k*],*B*[*k*]) follows certain continuity and regularity properties [Ljung 77,92] and are given in Table [7-2](#522202_1_En_7_Chapter.xhtml#Tab2).

Table 7-2

List of Adaptive Generalized Eigen-Decomposition Algorithms

| Alg. | Type | Adaptive Algorithm *h*(*W*[*k*],*A*[*k*]) |
| --- | --- | --- |
| OJA | Homogeneous | ![$$ {A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq7.png) |
| Deflation | ![$$ {A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq8.png) |
| Weighted | ![$$ {A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq9.png) |
| XU | Homogeneous | ![$$ 2{A}_k{W}_k-{A}_k{W}_k{W}_k^T{B}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq10.png) |
| Deflation | ![$$ 2{A}_k{W}_k-{A}_k{W}_k UT\left({W}_k^T{B}_k{W}_k\right)-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq11.png) |
| Weighted | ![$$ 2{A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{B}_k{W}_k $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq12.png) |
| PF | Homogeneous | ![$$ {A}_k{W}_k-\mu {B}_k{W}_k\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq13.png) |
| Deflation | ![$$ {A}_k{W}_k-\mu {B}_k{W}_k UT\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq14.png) |
| Weighted | ![$$ {A}_k{W}_kC-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq15.png) |
| AL1 | Homogeneous | ![$$ {A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k-\mu {B}_k{W}_k\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq16.png) |
| Deflation | ![$$ {A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-\mu {B}_k{W}_k UT\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq17.png) |
| Weighted | ![$$ {A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq18.png) |
| AL2 | Homogeneous | ![$$ 2{A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k-{A}_k{W}_k{W}_k^T{B}_k{W}_k-\mu {B}_k{W}_k\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq19.png) |
| Deflation | ![$$ 2{A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-{A}_k{W}_k UT\left({W}_k^T{B}_k{W}_k\right)-\mu {B}_k{W}_k UT\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq20.png) |
| Weighted | ![$$ 2{A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{B}_k{W}_k-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq21.png) |
| IT | Homogeneous | ![$$ \left({A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq22.png) |
| Deflation | ![$$ \left({A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq23.png) |
| Weighted | ![$$ \left({A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq24.png) |
| RQ | Homogeneous | ![$$ \left({A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{B}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq25.png) |
| Deflation | ![$$ \left({A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{B}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq26.png) |
| Weighted | ![$$ \left({A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{B}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq27.png) |

In the following discussions, I denote Φ=[**ϕ**[1] ... **ϕ**[*n*]]∈ℜ^(*n*X*n*) as the orthonormal generalized eigenvector matrix of *A* with respect to *B*, and Λ= *diag*(*λ*[1],...,*λ*[*n*]) as the generalized eigenvalue matrix, such that *λ*[1]>*λ*[2]>...>*λ*[*p*]>*λ*[*p*+1]≥...≥*λ*[*n*]>0. I use the subscript (*i*) to denote the *i*^(*th*) permutation of the indices {1,2,…,*n*}.

## 7.3 OJA GEVD Algorithms

### OJA Homogeneous Algorithm

The objective function for the OJA homogeneous algorithm can be written as

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{B}_k^{-1}{A}_k{\mathbf{w}}_k^i+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)}^2+\sum \limits_{j=1,j\ne i}^p{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j\right)}^2 $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq28.png),     (7.5)

for *i*=1,…,*p* (*p*≤*n*). From the gradient of (7.5) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq29.png) we obtain the following adaptive algorithm:

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i-{\eta}_k{B}_k{A}_k^{-1}{\nabla}_{{\mathbf{w}}_k^i}J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq30.png) for *i*=1,…,*p*,     (7.6)

where *η*[*k*] is a decreasing gain constant. Defining ![$$ {W}_k=\left[{\mathbf{w}}_k^1\dots {\mathbf{w}}_k^p\right] $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq31.png), from (7.6) we get

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq32.png).     (7.7)

### OJA Deflation Algorithm

The objective function for the OJA deflation adaptive GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{B}_k^{-1}{A}_k{\mathbf{w}}_k^i+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)}^2+\sum \limits_{j=1}^{i-1}{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j\right)}^2 $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq33.png),     (7.8)

for *i*=1,…,*p*. From the gradient of (7.8) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq34.png), we obtain the OJA deflation adaptive gradient descent algorithm as

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i-{\eta}_k\left({A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^i{B}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq35.png),     (7.9)

for *i*=1,…,*p* (*p*≤*n*). The matrix form of the algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq36.png),     (7.10)

where UT[⋅] sets all elements below the diagonal of its matrix argument to zero.

### OJA Weighted Algorithm

The objective function for the OJA weighted adaptive GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{c}_i{{\mathbf{w}}_k^i}^T{A}_k{B}_k^{-1}{A}_k{\mathbf{w}}_k^i+\frac{c_i}{2}{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)}^2+\sum \limits_{j=1,j\ne i}^p{c}_j{\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j\right)}^2 $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq37.png)     (7.11)

for *i*=1,…,*p*, where *c*[1],…,*c*[*p*] (*p*≤*n*) are small positive numbers satisfying

*c*[1] > *c*[2] > … > *c*[*p*] > 0, *p* ≤ *n*.     (7.12)

Given a diagonal matrix *C* =  *diag* (*c*[1], …, *c*[*p*]), *p* ≤ *n*, the OJA weighted adaptive algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq38.png).     (7.13)

### OJA Algorithm Python Code

The following Python code works with multidimensional data `X[nDim,nSamples]` and `Y[nDim,nSamples]`:

```
from numpy import linalg as la
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
B = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W2 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W3 = W2
c = [2.6-0.3*k for k in range(nEA)]
C = np.diag(c)
I  = np.identity(nDim)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrices A,B with current data vectors x,y
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
y = Y[:,iter]
y = y.reshape(nDim,1)
B = B + (1.0/(1 + cnt))*((np.dot(y, y.T)) - B)
# Deflated Gradient Descent
W2 = W2 + (1/(150 + cnt))*(A @ W2 - B @ W2 @ np.triu(W2.T @ A @ W2))
# Weighted Gradient Descent
W3 = W3 + (1/(500 + cnt))*(A @ W3 @ C - B @ W3 @ C @ (W3.T @ A @ W3))
```

## 7.4 XU GEVD Algorithms

### XU Homogeneous Algorithm

The objective function for the XU homogeneous adaptive GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-2{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i\right)+2\sum \limits_{j=1,j\ne i}^p{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq39.png)     (7.14)

for *i*=1,…,*p* (*p*≤*n*). From the gradient of (7.14) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq40.png), we obtain the XU homogeneous adaptive gradient descent algorithm as

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i+{\eta}_k\left(2{A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^p{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^p{B}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq41.png)     (7.15)

for *i*=1,…,*p*, whose matrix form is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{A}_k{W}_k{W}_k^T{B}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq42.png).     (7.16)

### XU Deflation Algorithm

The objective function for the XU deflation adaptive GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-2{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i\right)+2\sum \limits_{j=1}^{i-1}{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq43.png)     (7.17)

for *i*=1,…,*p* (*p*≤*n*). From the gradient of (7.17) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq44.png), we obtain

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{A}_k{W}_k UT\left({W}_k^T{B}_k{W}_k\right)-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq45.png), (7.18)

where UT[⋅] sets all elements below the diagonal of its matrix argument to zero. Chatterjee et al*.* [Mar 00, Thms 1, 2] proved that *W*[*k*] converges with probability one to [±**ϕ**[1] ±**ϕ**[2] … ±**ϕ**[*p*]] as *k*→∞*.*

### XI Weighted Algorithm

The objective function for the XU weighted adaptive GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-2{c}_i{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+{c}_i\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i\right)+2\sum \limits_{j=1,j\ne i}^p{c}_j{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq46.png)     (7.19)

for *i*=1,…,*p* (*p*≤*n*), where *c*[1],…,*c*[*p*] are small positive numbers satisfying (7.12). The adaptive algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{B}_k{W}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq47.png),  (7.20)

where *C* = *diag*(*c*[1],…,*c*[*p*]).

### XU Algorithm Python Code

The following Python code works with multidimensional data `X[nDim,nSamples]` and `Y[nDim,nSamples]`:

```
from numpy import linalg as la
A  np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
B  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W2 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W3 = W2
c = [2.6-0.3*k for k in range(nEA)]
C = np.diag(c)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrices A,B with current data vectors x,y
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
y = Y[:,iter]
y = y.reshape(nDim,1)
B = B + (1.0/(1 + cnt))*((np.dot(y, y.T)) - B)
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - 0.5 * B @ W2 @ \
np.triu(W2.T @ A @ W2)- 0.5* A@ W2 @ np.triu(W2.T@ B @ W2))
# Weighted Gradient Descent
W3 = W3 + (1/(300 + cnt))*(A @ W3 @ C - 0.5 * B @ W3 @ C @ \(W3.T @ A @ W3) - 0.5 *
A @ W3 @ C @ (W3.T @ B @ W3))
```

## 7.5 PF GEVD Algorithms

### PF Homogeneous Algorithm

We obtain the objective function for the PF homogeneous generalized eigenvector algorithm by writing the Rayleigh quotient criterion (7.2) as the following penalty function:

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\mu \left(\sum \limits_{j=1,j\ne i}^p{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq48.png),     (7.21)

where *μ* > 0 and *i*=1,…,*p* (*p*≤*n*). From the gradient of (7.21) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq49.png), we obtain the PF homogeneous adaptive algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-\mu {B}_k{W}_k\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq50.png),     (7.22)

where *I*[*p*] is a *p*X*p* identity matrix.

### PF Deflation Algorithm

The objective function for the PF deflation GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\mu \left(\sum \limits_{j=1}^{i-1}{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq51.png), (7.23)

where *μ* > 0 and *i*=1,…,*p*. The adaptive algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-\mu {B}_k{W}_k\mathrm{U}T\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq52.png),     (7.24)

where UT[⋅] sets all elements below the diagonal of its matrix argument to zero.

### PF Weighted Algorithm

The objective function for the PF weighted GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{c}_i{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\mu \left(\sum \limits_{j=1,j\ne i}^p{c}_i{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{c_j}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq53.png), (7.25)

where *c*[1] > *c*[2] > … > *c*[*p*] > 0 (*p* ≤ *n*), *μ* > 0, and *i*=1,…,*p*. The corresponding adaptive algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_kC-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq54.png),     (7.26)

where *C* =  *diag* (*c*[1], …, *c*[*p*]).

### PF Algorithm Python Code

The following Python code works with multidimensional data `X[nDim,nSamples]` and `Y[nDim,nSamples]`:

```
from numpy import linalg as la
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
B  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W2 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W3 = W2
c = [2.6-0.3*k for k in range(nEA)]
C = np.diag(c)
I  = np.identity(nDim)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrices A,B with current data vectors x,y
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
y = Y[:,iter]
y = y.reshape(nDim,1)
B = B + (1.0/(1 + cnt))*((np.dot(y, y.T)) - B)
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - mu * B @ W2 @ \
np.triu((W2.T @ B @ W2) - I))
# Weighted Gradient Descent
W3 = W3 + (1/(500 + cnt))*(A @ W3 @ C - mu * B @ W3 @ C @ \
((W3.T @ B @ W3) - I))
```

## 7.6 AL1 GEVD Algorithms

### AL1 Homogeneous Algorithm

We apply the augmented Lagrangian method of nonlinear optimization to the Rayleigh quotient criterion (7.4) to obtain the objective function for the AL1 homogeneous GEVD algorithm as

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\alpha \left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq55.png),

![$$ +\mu \left(\sum \limits_{j=1,j\ne i}^p{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq56.png),     (7.27)

for *i*=1,…,*p* (*p*≤*n*), where (*α*, *β*[1], *β*[2], …, *β*[*p*]) are Lagrange multipliers and *μ* is a positive penalty constant. Taking the gradient of ![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq57.png) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq58.png) and equating the gradient to **0** and using the constraint ![$$ {{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i={\delta}_{ij} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq59.png), we obtain

![$$ \alpha ={{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq60.png) and ![$$ {\beta}_j={{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq61.png)for *j*=1,…,*p*.     (7.28)

Replacing (*α*, *β*[1], *β*[2], …, *β*[*p*]) in the gradient of (7.27), we obtain the AL1 homogeneous adaptive gradient descent generalized eigenvector algorithm:

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i+{\eta}_k\left({A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^p{B}_k{\mathbf{w}}_k^j\left({{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i\right)\right.\left.-\mu \sum \limits_{j=1}^p{B}_k{\mathbf{w}}_k^j\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i-{\delta}_{ij}\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq62.png) (7.29)

where *μ* > 0\. Defining ![$$ {W}_k=\left[{\mathbf{w}}_k^1\dots {\mathbf{w}}_k^p\right] $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq63.png), we obtain

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(\left({A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k-\mu {B}_k{W}_k\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right)\right. $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq64.png), (7.30)

where *I*[*p*] is a *p*X*p* identity matrix.

### AL1 Deflation Algorithm

The objective function for the AL1 deflation GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\alpha \left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^{i-1}{\beta}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq65.png)

![$$ +\mu \left(\sum \limits_{j=1}^{i-1}{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq66.png),     (7.31)

for *i*=1,…,*p* (*p*≤*n*). Following the steps in Section 7.6.1 we obtain the adaptive algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(\left({A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-\mu {B}_k{W}_k UT\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right)\right. $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq67.png). (7.32)

### AL1 Weighted Algorithm

The objective function for the AL1 weighted GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{c}_i{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\alpha {c}_i\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{c}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq68.png),

![$$ +\mu \left(\sum \limits_{j=1,j\ne i}^p{c}_j{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{c_i}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq69.png),     (7.33)

for *i*=1,…,*p* (*p*≤*n*), where (*α*, *β*[1], *β*[2], …, *β*[*p*]) are Lagrange multipliers, *μ* is a positive penalty constant, and *c*[1] > *c*[2] > … > *c*[*p*] > 0. The adaptive algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(\left({A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right)\right. $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq70.png), (7.34)

where *C* =  *diag* (*c*[1], …, *c*[*p*]).

### AL1 Algorithm Python Code

The following Python code works with multidimensional data `X[nDim,nSamples]` and `Y[nDim,nSamples]`:

```
from numpy import linalg as la
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
B  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W2 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W3 = W2
c = [2.6-0.3*k for k in range(nEA)]
C = np.diag(c)
I  = np.identity(nDim)
mu = 2
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrices A,B with current data vectors x,y
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
y = Y[:,iter]
y = y.reshape(nDim,1)
B = B + (1.0/(1 + cnt))*((np.dot(y, y.T)) - B)
# Deflated Gradient Descent
W2 = W2 + (1/(500 + cnt))*(A @ W2 - B @ W2 @ np.triu(W2.T @ A @ W2) \
- mu * B @ W2 @ np.triu((W2.T @ B @ W2) - I))
# Weighted Gradient Descent
W3 = W3 + (1/(1000 + cnt))*(A @ W3 @ C - B @ W3 @ C @ (W3.T @ A @ W3) \
- mu * B @ W3 @ C @ ((W3.T @ B @ W3) - I))
```

## 7.7 AL2 GEVD Algorithms

### AL2 Homogeneous Algorithm

The unconstrained objective function for the AL2 homogeneous GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-2{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i\right)+2\sum \limits_{j=1,j\ne i}^p{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i+ $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq71.png)

![$$ \mu \left(\sum \limits_{j=1,j\ne i}^p{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq72.png),     (7.35)

for *i*=1,…,*p*, where *μ* is a positive penalty constant. From (7.35), we obtain the adaptive gradient descent algorithm:

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i+{\eta}_k\left(2{A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^p{B}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^p{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i-\mu \sum \limits_{j=1}^p{B}_k{\mathbf{w}}_k^j\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i-{\delta}_{ij}\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq73.png), (7.36)

for *i*=1,…,*p*, the matrix version of which is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k-{A}_k{W}_k{W}_k^T{B}_k{W}_k-\mu {B}_k{W}_k\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq74.png).     (7.37)

### AL2 Deflation Algorithm

The objective function for the AL2 deflation GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-2{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i\right)+2\sum \limits_{j=1}^{i-1}{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i+ $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq75.png)

![$$ \mu \left(\sum \limits_{j=1}^{i-1}{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{1}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq76.png), (7.38)

for *i*=1,…,*p*. The adaptive GEVD algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-{A}_k{W}_k UT\left({W}_k^T{B}_k{W}_k\right)-\mu {B}_k{W}_k UT\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq77.png). (7.39)

### AL2 Weighted Algorithm

The objective function for the AL2 weighted generalized eigenvector algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-2{c}_i{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i+{c}_i\left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i\right)+2\sum \limits_{j=1}^{i-1}{c}_j{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i+ $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq78.png)

![$$ \mu \left(\sum \limits_{j=1}^{i-1}{c}_j{\left({{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i\right)}^2+\frac{c_i}{2}{\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)}^2\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq79.png),     (7.40)

for *i*=1,…,*p*, where *c*[1] > *c*[2] > … > *c*[*p*] > 0 (*p* ≤ *n*). The adaptive algorithm is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{B}_k{W}_k-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right)\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq80.png), (7.41)

where *C* =  *diag* (*c*[1], …, *c*[*p*]), *p* ≤ *n*.

### AL2 Algorithm Python Code

The following Python code works with data `X[nDim,nSamples]` and `Y[nDim,nSamples]`:

```
from numpy import linalg as la
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
B  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W2 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W3 = W2
c = [2.6-0.3*k for k in range(nEA)]
C = np.diag(c)
I  = np.identity(nDim)
mu = 1
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrices A,B with current data vectors x,y
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
y = Y[:,iter]
y = y.reshape(nDim,1)
B = B + (1.0/(1 + cnt))*((np.dot(y, y.T)) - B)
# Deflated Gradient Descent
W2 = W2 + (1/(100 + cnt))*(A @ W2 - 0.5 * B @ W2 @ np.triu(W2.T @ A \
@ W2) - 0.5 * A @ W2 @ np.triu(W2.T @ B @ W2) - \
0.5 * mu * B @ W2 @ np.triu((W2.T @ B @ W2) - I))
# Weighted Gradient Descent
W3 = W3 + (1/(300 + cnt))*(A @ W3 @ C - 0.5 * B @ W3 @ C @ (W3.T @ A \
@ W3) - 0.5 * A @ W3 @ C @ (W3.T @ B @ W3) - \
0.5 * mu * B @ W3 @ C @ ((W3.T @ B @ W3) - I))
```

## 7.8 IT GEVD Algorithms

### IT Homogeneous Algorithm

The objective function for the information theory homogeneous GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)={{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-\ln \left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)+\alpha \left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq81.png)     (7.42)

for *i*=1,…,*p*, where (*α*, *β*[1], *β*[2], …, *β*[*p*]) are Lagrange multipliers and *ln*(.) is logarithm base *e*. By equating the gradient of ([7.42](#522202_1_En_7_Chapter.xhtml#Par128)) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq82.png) to **0** and using the constraint ![$$ {{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i={\delta}_{ij} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq83.png), we obtain

*α* = 0 and ![$$ {\beta}_j=\frac{{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq84.png),     (7.43)

for *j*=1,…,*p*. Replacing (*α*, *β*[1], *β*[2], …, *β*[*p*]) in the gradient of ([7.42](#522202_1_En_7_Chapter.xhtml#Par128)), we obtain the IT homogeneous adaptive gradient descent algorithm for the generalized eigenvector:

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i+{\eta}_k\left({A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^p{B}_k{\mathbf{w}}_k^j\left({{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i\right)\right)/{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq85.png),     (7.44)

for *i*=1,…,*p*, whose matrix version is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq86.png),     (7.45)

where DIAG[⋅] sets all elements *except* the diagonal of its matrix argument to zero.

### IT Deflation Algorithm

The objective function for the IT deflation GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)={{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-\ln \left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)+\alpha \left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^{i-1}{\beta}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq87.png)     (7.46)

for *i*=1,…,*p*, where (*α*, *β*[1], *β*[2], …, *β*[*p*]) are Lagrange multipliers. From ([7.43](#522202_1_En_7_Chapter.xhtml#Par130)), we obtain the adaptive gradient algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern0.6em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq88.png) (7.47)

### IT Weighted Algorithm

The objective function for the IT weighted GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i\right)={c}_i{{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-{c}_i\ln \left({{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i\right)+\alpha {c}_i\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{c}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq89.png)     (7.48)

for *i*=1,…,*p*, where (*α*, *β*[1], *β*[2], …, *β*[*p*]) are Lagrange multipliers. By solving (*α*, *β*[1], *β*[2], …, *β*[*k*]) and replacing them in the gradient of ([7.48](#522202_1_En_7_Chapter.xhtml#Par141)), we obtain the adaptive algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq90.png).     (7.49)

### IT Algorithm Python Code

The following Python code works with data `X[nDim,nSamples]` and `Y[nDim,nSamples]`:

```
from numpy import linalg as la
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
B  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W2 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W3 = W2
c = [2.6-0.3*k for k in range(nEA)]
C = np.diag(c)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrices A,B with current data vectors x,y
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
y = Y[:,iter]
y = y.reshape(nDim,1)
B = B + (1.0/(1 + cnt))*((np.dot(y, y.T)) - B)
# Deflated Gradient Descent
W2 = W2 + (1/(50 + cnt))*(A @ W2 - B @ W2 @ np.triu(W2.T @ A @ W2)) \
@ inv(np.diag(np.diagonal(W2.T @ A @ W2)))
# Weighted Gradient Descent
W3 = W3 + (1/(100 + cnt))*(A @ W3 @ C – B @ W3 @ C @ (W3.T @ A @ W3)) \
@ inv(np.diag(np.diagonal(W3.T @ A @ W3)))
```

## 7.9 RQ GEVD Algorithms

### RQ Homogeneous Algorithm

We obtain the objective function for the Rayleigh quotient homogeneous GEVD algorithm from the Rayleigh quotient criterion (7.2) as follows:

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-\frac{{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i}+\alpha \left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1,j\ne i}^p{\beta}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq91.png) (7.50)

for *i*=1,…,*p*, where (*α*, *β*[1], *β*[2], …, *β*[*p*]) are Lagrange multipliers. By equating the gradient (7.50) with respect to ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq92.png) to **0**, and using the constraint ![$$ {{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i={\delta}_{ij} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq93.png), we obtain

*α* = 0 and ![$$ {\beta}_j=\frac{{{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq94.png) for *j*=1,…,*p*.     (7.51)

Replacing (*α*, *β*[1], *β*[2], …, *β*[*p*]) in the gradient of (7.50) and making a small approximation, we obtain the RQ homogeneous adaptive gradient descent algorithm for the generalized eigenvector:

![$$ {\mathbf{w}}_{k+1}^i={\mathbf{w}}_k^i+{\eta}_k\left({A}_k{\mathbf{w}}_k^i-\sum \limits_{j=1}^p{B}_k{\mathbf{w}}_k^j\left({{\mathbf{w}}_k^j}^T{A}_k{\mathbf{w}}_k^i\right)\right)/{{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq95.png),     (7.52)

for *i*=1,…,*p*, whose matrix version is

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{B}_k{W}_k{W}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{B}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq96.png).     (7.53)

### RQ Deflation Algorithm

The objective function for the RQ deflation GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-\frac{{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i}+\alpha \left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^i{\beta}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq97.png), (7.54)

for *i*=1,…,*p*, where (*α*, *β*[1], *β*[2], …, *β*[*p*]) are Lagrange multipliers. By solving (*α*, *β*[1], *β*[2], …, *β*[*k*]) and replacing them in the gradient of (7.54), we obtain the adaptive algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{B}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq98.png). (7.55)

### RQ Weighted Algorithm

The objective function for the RQ weighted GEVD algorithm is

![$$ J\left({\mathbf{w}}_k^i;{A}_k,{B}_k\right)=-{c}_i\frac{{{\mathbf{w}}_k^i}^T{A}_k{\mathbf{w}}_k^i}{{{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i}+\alpha {c}_i\left({{\mathbf{w}}_k^i}^T{B}_k{\mathbf{w}}_k^i-1\right)+2\sum \limits_{j=1}^i{\beta}_j{c}_j{{\mathbf{w}}_k^j}^T{B}_k{\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq99.png),     (7.56)

for *i*=1,…,*p*, where (*α*, *β*[1], *β*[2], …, *β*[*p*]) are Lagrange multipliers. By solving (*α*, *β*[1], *β*[2], …, *β*[*k*]) and replacing them in the gradient of (7.56), we obtain the adaptive algorithm:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left({A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k\right)\kern0.5em \mathrm{DIAG}{\left({W}_k^T{B}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq100.png).     (7.57)

### RQ Algorithm Python Code

The following Python code works with data `X[nDim,nSamples]` and `Y[nDim,nSamples]`:

```
from numpy import linalg as la
A  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
B  = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
W2 = 0.1 * np.ones(shape=(nDim,nEA)) # weight vectors of all algorithms
W3 = W2
c = [2.6-0.3*k for k in range(nEA)]
C = np.diag(c)
I  = np.identity(nDim)
for epoch in range(nEpochs):
for iter in range(nSamples):
cnt = nSamples*epoch + iter
# Update data correlation matrices A,B with current data vectors x,y
x = X[:,iter]
x = x.reshape(nDim,1)
A = A + (1.0/(1 + cnt))*((np.dot(x, x.T)) - A)
y = Y[:,iter]
y = y.reshape(nDim,1)
B = B + (1.0/(1 + cnt))*((np.dot(y, y.T)) - B)
# Deflated Gradient Descent
W2 = W2 + (1/(20 + cnt))*(A @ W2 - B @ W2 @ np.triu(W2.T @ A @ W2)) \
inv(np.diag(np.diagonal(W2.T @ B @ W2)))
# Weighted Gradient Descent
W3 = W3 + (1/(300 + cnt))*(A @ W3 @ C - B @ W3 @ C @ (W3.T @ A @ W3)) \
@ inv(np.diag(np.diagonal(W3.T @ B @ W3)))
```

## 7.10 Experimental Results

I generated 1,000 samples (of {**x**[*k*]} and {**y**[*k*]}) from 10-dimensional Gaussian data (i.e., *n*=10) with the mean zero and covariance given below. The covariance matrix *A* for {**x**[*k*]} is obtained from the second covariance matrix in [Okada and Tomita 85] multiplied by 3 as follows:

![](images/522202_1_En_7_Chapter/522202_1_En_7_Figa_HTML.png)

The covariance matrix *B* for {**y**[*k*]} is obtained from the third covariance matrix in [Okada and Tomita 85] multiplied by 2 as follows:

![](images/522202_1_En_7_Chapter/522202_1_En_7_Figb_HTML.png)

The generalized eigenvalues of (*A*,*B*) are

107.9186, 49.0448, 8.3176, 5.1564, 2.8814, 2.3958, 1.9872, 1.2371, 0.9371, 0.1096.

I computed the first four principal generalized eigenvectors (i.e., eigenvectors corresponding to the largest four eigenvalues) (i.e., *p*=4) by the adaptive algorithms described before. In order to compute the online data sequence {*A*[*k*]}, I generated random data vectors {**x**[*k*]} from the above covariance matrix *A*. I generated {*A*[*k*]} from {**x**[*k*]} by using algorithm (2.21) in Chapter [2](#522202_1_En_2_Chapter.xhtml). Similarly, I generated random data vectors {**y**[*k*]} from the covariance matrix *B* and then generated {*B*[*k*]} from {**y**[*k*]}. I computed the correlation matrix *A*[*computed*] and *B*[*computed*] after collecting all 500 samples **x**[*k*] and **y**[*k*] respectively as

![$$ {A}_{computed}=\frac{1}{1000}\sum \limits_{i=1}^{1000}{\mathbf{x}}_i{\mathbf{x}}_i^T $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq101.png) and ![$$ {B}_{computed}=\frac{1}{1000}\sum \limits_{i=1}^{1000}{\mathbf{y}}_i{\mathbf{y}}_i^T $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq102.png).

I referred to the generalized eigenvectors and eigenvalues computed from this *A* and *B* by a standard numerical analysis method [Golub and VanLoan 83] as the *actual values* .

I started all algorithms with **w**[0] = 0.1*ONE, where ONE is a 10 X 4 matrix whose all elements are ones. In order to measure the convergence and accuracy of the algorithms, I computed the direction cosine at *k*^(*th*) update of each adaptive algorithm as

Direction cosine (*k*) = ![$$ \frac{\left|{{\mathbf{w}}_k^i}^T{\boldsymbol{\upvarphi}}_i\right|}{\left\Vert {\mathbf{w}}_k^i\left\Vert \right\Vert {\boldsymbol{\upvarphi}}_i\right\Vert } $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq103.png),     (7.58)

where ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq104.png) is the estimated generalized eigenvector of (*A*[*k*],*B*[*k*]) at *k*^(*th*) update and **ϕ**[*i*] is the actual *i*^(*th*) generalized eigenvector computed from all collected samples by a conventional numerical analysis method.

Figure [7-1](#522202_1_En_7_Chapter.xhtml#Fig1) shows the iterates of the OJA algorithms (deflated and weighted) to compute the first two principal generalized eigenvectors of (*A*[*k*],*B*[*k*]). Figure [7-2](#522202_1_En_7_Chapter.xhtml#Fig2) shows the same for the XU algorithms, Figure [7-3](#522202_1_En_7_Chapter.xhtml#Fig3) for the PF algorithms, Figure [7-4](#522202_1_En_7_Chapter.xhtml#Fig4) for the AL1 algorithms, Figure [7-5](#522202_1_En_7_Chapter.xhtml#Fig5) for the AL2 algorithms, Figure [7-6](#522202_1_En_7_Chapter.xhtml#Fig6) for the IT algorithms, and Figure [7-7](#522202_1_En_7_Chapter.xhtml#Fig7) for the RQ algorithms.

![](images/522202_1_En_7_Chapter/522202_1_En_7_Fig7_HTML.png)

Figure 7-7

Convergence of the first two principal generalized eigenvectors of (A,B) by the RQ deflation (7.55) and RQ weighted (7.57) adaptive algorithms

![](images/522202_1_En_7_Chapter/522202_1_En_7_Fig6_HTML.png)

Figure 7-6

Convergence of the first two principal generalized eigenvectors of (A,B) by the IT deflation (7.47) and IT weighted (7.49) adaptive algorithms

![](images/522202_1_En_7_Chapter/522202_1_En_7_Fig5_HTML.png)

Figure 7-5

Convergence of the first two principal generalized eigenvectors of (A,B) by the AL2 deflation (7.39) and AL2 weighted (7.41) adaptive algorithms

![](images/522202_1_En_7_Chapter/522202_1_En_7_Fig4_HTML.png)

Figure 7-4

Convergence of the first two principal generalized eigenvectors of (A,B) by the AL1 deflation (7.32) and AL1 weighted (7.34) adaptive algorithms

![](images/522202_1_En_7_Chapter/522202_1_En_7_Fig3_HTML.png)

Figure 7-3

Convergence of the first two principal generalized eigenvectors of (A,B) by the PF deflation (7.24) and PF weighted (7.26) adaptive algorithms

![](images/522202_1_En_7_Chapter/522202_1_En_7_Fig2_HTML.png)

Figure 7-2

Convergence of the first two principal generalized eigenvectors of (A,B) by the XU deflation (7.18) and XU weighted (7.20) adaptive algorithms

![](images/522202_1_En_7_Chapter/522202_1_En_7_Fig1_HTML.png)

Figure 7-1

Convergence of the first two principal generalized eigenvectors of (A,B) by the OJA deflation (7.10) and OJA weighted (7.13) adaptive algorithms

For all algorithms, I used *η*[*k*]=1/(140+*k*) for the deflation algorithms and *η*[*k*]=1/(500+*k*) for the weighted algorithms. The diagonal weight matrix *C* used for the weighted algorithms is DIAG(2.6,2.3,2.0,1.7). I ran all algorithms for *three epochs* of the data, where one epoch means presenting all training data once in random order. I did not show the results for the homogeneous algorithms since the homogeneous method produces a linear combination of the actual generalized eigenvectors of (*A*,*B*). Thus, the direction cosines are not indicative of the performance of the algorithms for the homogeneous case.

## 7.11 Concluding Remarks

Observe that the convergence of all algorithms decreases progressively for the minor generalized eigenvectors and is best for principal generalized eigenvector. This is expected since the convergence of these adaptive algorithms is a function of the relative generalized eigenvalue. Furthermore, the weighted algorithms, which can be implemented in parallel hardware, performed very similarly to the deflation algorithms.

I did the following:

Table 7-3

List of Adaptive GEVD Algorithms, Complexity, and Performance

| Alg | Type | Adaptive Algorithm ***h***(***W***[***k***],***A***[***k***]) | Comments |
| --- | --- | --- | --- |
| OJA | Deflation | ![$$ {A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq105.png) | n³p⁶, 6 |
| Weighted | ![$$ {A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq106.png) | n⁴p⁶, 6 |
| XU | Deflation | ![$$ 2{A}_k{W}_k-{A}_k{W}_k UT\left({W}_k^T{B}_k{W}_k\right)-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq107.png) | 2n³p⁶, 8 |
| Weighted | ![$$ 2{A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{B}_k{W}_k $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq108.png) | 2n⁴p⁶, 8 |
| PF | Deflation | ![$$ {A}_k{W}_k-\mu {B}_k{W}_k UT\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq109.png) | n²p⁴, 7 |
| Weighted | ![$$ {A}_k{W}_kC-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq110.png) | n³p⁴, 7 |
| AL1 | Deflation | ![$$ {A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-\mu {B}_k{W}_k UT\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq111.png) | n³p⁶+ n²p⁴, 9 |
| Weighted | ![$$ {A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq112.png) | n⁴p⁶+ n³p⁴, 9 |
| AL2 | Deflation | ![$$ 2{A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)-{A}_k{W}_k UT\left({W}_k^T{B}_k{W}_k\right)-\mu {B}_k{W}_k UT\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq113.png) | 2n³p⁶+ n²p⁴, 10 |
| Weighted | ![$$ 2{A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k-{A}_k{W}_k{CW}_k^T{B}_k{W}_k-\mu {B}_k{W}_kC\left({W}_k^T{B}_k{W}_k-{I}_p\right) $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq114.png) | 2n⁴p⁶+ n³p⁴, 10 |
| IT | Deflation | ![$$ \left({A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq115.png) | Not applicable |
| Weighted | ![$$ \left({A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{A}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq116.png) | Not applicable |
| RQ | Deflation | ![$$ \left({A}_k{W}_k-{B}_k{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{B}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq117.png) | Not applicable |
| Weighted | ![$$ \left({A}_k{W}_kC-{B}_k{W}_k{CW}_k^T{A}_k{W}_k\right)\kern1em \mathrm{DIAG}{\left({W}_k^T{B}_k{W}_k\right)}^{-1} $$](images/522202_1_En_7_Chapter/522202_1_En_7_Chapter_TeX_IEq118.png) | Not applicable |

1.  For each algorithm, I rated the compute and convergence performance.

2.  I skipped the homogeneous algorithms because they are not useful for practical applications since they produce arbitrary rotations of the eigenvectors.

3.  Note that *A*[*k*]∈ℜ^(*n*X*n*), *B*[*k*]∈ℜ^(*n*X*n*) and *W*[*k*]∈ℜ^(*n*X*p*). I presented the computation complexity of each algorithm in terms of the matrix dimensions *n* and *p*.

4.  The convergence performance is determined based on the speed of convergence of the principal and the minor components. I rated convergence in a scale of 1-10 where 10 is the fastest converging algorithm.

5.  I skipped the IT and RQ algorithms because they did not perform well compared to the remaining algorithms and the matrix inversion increases computational complexity. See Table [7-3](#522202_1_En_7_Chapter.xhtml#Tab3).

Observe the following:

1.  The OJA algorithm has the least complexity and good performance.

2.  The AL2 algorithm has the most complexity and best performance.

3.  The AL1 algorithm is the next best after AL2, and PF and XU follow.

The complexity and accuracy tradeoffs will determine the algorithm to use in real-world scenarios. If you can afford the computation, the AL2 algorithm is the best. The XU algorithm is a good balance of complexity and performance.

In summary, I showed 21 algorithms, many of them new, from a common framework with an objective function for each. Note that although I applied the gradient descent technique on these objective functions, I could have applied any other technique of nonlinear optimization such as steepest descent, conjugate direction, Newton-Raphson, or recursive least squares. The availability of the objective functions allows us to derive new algorithms by using new optimization techniques on them and also to perform convergence analyses of the adaptive algorithms.

# 8. Real-World Applications of Adaptive Linear Algorithms

In this chapter, I consider real-world examples of linear adaptive algorithms. Some of the best needs for these algorithms arise due to edge computation on devices, which require managing the following:

*   Power usage for device-based computation at scale

*   Non-stationarity of inputs

*   Latency of computation on devices

*   Memory and bandwidth of devices

In these cases, there are the following constraints:

*   The data arrives as a sequence of random vectors {**x**[*k*]} or random matrices {*A*[*k*]}.

*   The data changes with time, causing significant drift of input features whereby the models are no longer effective over time.

*   The data volume is large and we do not have the device storage, bandwidth, or power to store or upload the data.

*   Data dimensionality can be large.

In these circumstances, I will demonstrate how to use the linear adaptive algorithms to manage the device’s power, memory, and bandwidth in order to maintain accuracy of the pretrained models. The examples I will cover are the following:

*   Calculating feature drift of incoming data and detecting training-serving skew [Kaz Sato et al*.* 21] ahead of time

*   Adapting to incoming data drift and calculating features that best fit the data

*   Compressing incoming data into features for use in new model creation

*   Calculating anomalies in incoming data so that good clean data is used by the models

In these examples, I used data from the following repository: Publicly Real-World Datasets to Evaluate Stream Learning Algorithms. This dataset represents real-world streaming non-stationarity data [Vinicius Souza et al*.* 20].

Note that besides the examples discussed in this chapter, I have considered many other practical examples of adaptive examples throughout the book, such as

*   Handwritten character recognition with adaptive mean

*   Anomaly detection with adaptive median

*   Data representation feature computation

*   Data classification feature computation

## 8.1 Detecting Feature Drift

As the underlying statistical properties of the incoming data changes with time, the models used for machine learning decay in performance. Early detection of feature drift and retraining the models maintains the accuracy of the machine learning solution.

### INSECTS-incremental_balanced_norm Dataset: Eigenvector Test

The dataset name is `INSECTS-incremental_balanced_norm.arff` . This dataset has 33 components. It has gradually increasing components causing the feature drift shown in Figure [8-1](#522202_1_En_8_Chapter.xhtml#Fig1).

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig1_HTML.png)

Figure 8-1

Non-stationary multi-dimensional real-world data with incremental drift

#### Adaptive EVD of Semi-Stationary Components

I dropped the drift components and used the EVD linear adaptive algorithm (5.13) from Chapter [5](#522202_1_En_5_Chapter.xhtml), shown below, on the remaining components:

![$$ {W}_{k+1}={W}_k+{\eta}_k\left(2{A}_k{W}_k-{A}_k{W}_k UT\left({W}_k^T{W}_k\right)-{W}_k UT\left({W}_k^T{A}_k{W}_k\right)\right). $$](images/522202_1_En_8_Chapter/522202_1_En_8_Chapter_TeX_Equ1.png)

(5.13)

Note that the remaining components are a lot more stable, but some non-stationarity still exists. For each input sample of the sequence, I plotted the norms of the first four principal eigenvectors to demonstrate the quality of convergence of these eigenvectors; see Figure [8-2](#522202_1_En_8_Chapter.xhtml#Fig2).

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig2_HTML.jpg)

Figure 8-2

Norms of the first four eigenvectors for the adaptive EVD algorithm (5.13) on stationary data

The first four eigenvector norms converge rapidly to stable values with streaming samples. The upward horizontal slopes of the curves indicate stable convergence. The slight downward slopes of the third and fourth principal eigenvectors show a slight non-stationarity in the data. But the data is largely stable and stationary. We can conclude that the features are consistent with the current machine learning model and no model changes are necessary.

The following Python code works on multidimensional data `dataset2 [nDim, nSamples]`:

```
# Adaptive algorithm
from numpy import linalg as la
nSamples = dataset2.shape[0]
nDim = dataset2.shape[1]
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
N = np.zeros(shape=(1,nDim)) # stores eigen norms
W = 0.1 * np.ones(shape=(nDim,nDim)) # stores adaptive eigenvectors
for iter in range(nSamples):
cnt = iter + 1
# Update data correlation matrix A with current data vector x
x = np.array(dataset2.iloc[iter])
x = x.reshape(nDim,1)
A = A + (1.0/cnt)*((np.dot(x, x.T)) - A)
etat = 1.0/(25 + cn t)
# Deflated Gradient Descent
W = W + etat*(A @ W - 0.5*W @ np.triu(W.T @ A @ W) - \
0.5*A @ W @ np.triu(W.T @ W))
newnorm = la.norm(W, axis=0)
N = np.vstack([N, newnorm])
```

#### Adaptive EVD of Non-Stationary Components

I next used the non-stationary components of the data to detect feature drift with the adaptive algorithms. I used the same adaptive EVD algorithm (5.13) and plotted the norms of the first four principal eigenvectors for each data sample, as shown in Figure [8-3](#522202_1_En_8_Chapter.xhtml#Fig3).

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig3_HTML.jpg)

Figure 8-3

Norms of the first four eigenvectors for the adaptive EVD algorithm (5.13) on non-stationary data

You can clearly see that the second through fourth eigenvectors diverge, indicated by the downward slopes of the graphs, showing the feature drift early in the sequence. The downward slope of the second through fourth eigenvectors indicates the gradual drift of the features. This result shows that the features are drifting from the original ones used to build the machine learning model.

I used the same Python code I used on the stationary data in Section [8.1.1](#522202_1_En_8_Chapter.xhtml#Sec2).

### INSECTS-incremental-abrupt_balanced_norm Dataset

The dataset name is `INSECTS-incremental_abrupt_balanced_norm.arff` . This dataset has repeated abrupt changes in features, as shown in Figure [8-4](#522202_1_En_8_Chapter.xhtml#Fig4).

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig4_HTML.png)

Figure 8-4

Non-stationary multi-dimensional real-world data with periodic abrupt drift

I used the same adaptive EVD algorithm (5.13) and observed the norms of the first four principal eigenvectors and plotted them for each data sample, as shown in Figure [8-5](#522202_1_En_8_Chapter.xhtml#Fig5).

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig5_HTML.jpg)

Figure 8-5

Norms of the first four eigenvectors for the adaptive EVD algorithm (5.13) on non-stationary data

Once again, the second through fourth eigenvectors diverge, indicating feature drift early in the data sequence. The downward slope of the second through fourth eigenvectors detects drift of the features early in the sequence.

I used the same Python code I used on the stationary data in Section [8.1.1](#522202_1_En_8_Chapter.xhtml#Sec2).

### Electricity Dataset

The dataset name is `elec.arff`. This dataset has a variety of non-stationary components, as shown in Figure [8-6](#522202_1_En_8_Chapter.xhtml#Fig6).

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig6_HTML.png)

Figure 8-6

Non-stationary multi-dimensional real-world data with abrupt drifts and trends

The adaptive EVD algorithm (5.13) gave us the first two principal eigenvectors shown in Figure [8-7](#522202_1_En_8_Chapter.xhtml#Fig7), indicating non-stationarity early in the sequence.

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig7_HTML.jpg)

Figure 8-7

Norms of the first four eigenvectors for the adaptive EVD algorithm (5.13) on non-stationary data

Figure [8-7](#522202_1_En_8_Chapter.xhtml#Fig7) shows the rapid drop in norms of the second through fourth eigenvectors computed by the adaptive algorithm (5.13). This example shows that large non-stationarity in the data is signaled very quickly by massive drops in norms right at the start of the data sequence.

I used the same Python code I used on the stationary data in Section [8.1.1](#522202_1_En_8_Chapter.xhtml#Sec2).

## 8.2 Adapting to Incoming Data Drift

While it is important to detect drift of non-stationary data, it is also important for our algorithms to adapt to data drift quickly. See the example of *simulated data* in Figure [8-8](#522202_1_En_8_Chapter.xhtml#Fig8) that abruptly changes to a different underlying statistic after 500 samples.

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig8_HTML.png)

Figure 8-8

Non-stationary multi-dimensional simulated data with an abrupt change after 500 samples

I used the adaptive steepest descent algorithm (6.8) to compute the principal eigenvectors. The adaptive algorithm helps us adapt to this abrupt change and recalculate the underlying PCA statistics—in this case, the first two principal eigenvectors of the data. See Figure [8-9](#522202_1_En_8_Chapter.xhtml#Fig9).

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig9_HTML.jpg)

Figure 8-9

The adaptive steepest descent algorithm (6.8) rapidly adapts to abrupt non-stationary data for principal eigenvector computation.

The Python code for this algorithm is given in Section [6.3](#522202_1_En_8_Chapter.xhtml#Sec5).

## 8.3 Compressing High Volume and High Dimensional Data

When the incoming data volume is large, it can be prohibitively difficult to store such data on the device for training machine learning models. The problem is further complicated if the data is high dimensional, like 100+ dimensions. In such circumstances, we want to compress the data into batches and store the sequence of feature vectors for future use for machine learning training.

In this example, I used the open source `gassensor.arff` data. The dataset has 129 components/dimensions and 13,910 samples. I used the adaptive EVD algorithm (5.13) to compute the first 16 principal components [**ϕ**[1] **ϕ**[2] … **ϕ**[16]]. I reconstructed the data back from these 16-dimensional principal components. In Figure [8-10](#522202_1_En_8_Chapter.xhtml#Fig10), the left column is the original data and the right column is the reconstructed data. Clearly, they look quite similar and there is an 8x data compression.

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig10_HTML.jpg)

Figure 8-10

Original (left) and reconstructed (right) data with 8x compression using the adaptive EVD algorithm (5.13)

The Python code is given in Section [5.4](#522202_1_En_8_Chapter.xhtml#Sec11) and below for `dataset[nDim,nSamples]:`

```
from numpy import linalg as la
nSamples = dataset.shape[0]
nDim = dataset.shape[1]
nEA = 16
A = np.zeros(shape=(nDim,nDim)) # stores adaptive correlation matrix
N = np.zeros(shape=(1,nEA)) # stores eigen norms
W = 0.1 * np.ones(shape=(nDim,nEA)) # stores adaptive eigenvectors
for iter in range(nSamples):
cnt = iter + 1
# Update data correlation matrix A with current sample x
x = np.array(dataset.iloc[iter])
x = x.reshape(nDim,1)
A = A + (1.0/cnt)*((np.dot(x, x.T)) - A)
etat = 1.0/(500 + cnt)
# Deflated Gradient Descent
W = W + etat*(A @ W - 0.5*W @ np.triu(W.T @ A @ W) - \
0.5*A @ W @ np.triu(W.T @ W))
```

### Data Representation (PCA) Features

Let’s demonstrate the success of the adaptive algorithms by comparing the eigenvalues and eigenvectors of the batch correlation matrix computed by conventional method with the adaptive algorithm (5.13) at each adaptive step for the first four principal eigenvectors.

In order to measure the convergence and accuracy of the adaptive algorithm, I computed the direction cosine at *k*^(*th*) update eigenvector of each adaptive algorithm as

Direction cosine (*k*) = ![$$ \left|{{\mathbf{w}}_k^i}^T{\boldsymbol{\upvarphi}}_i\right|/\left\Vert {\boldsymbol{\upvarphi}}_i\right\Vert \left\Vert {\mathbf{w}}_k^i\right\Vert $$](images/522202_1_En_8_Chapter/522202_1_En_8_Chapter_TeX_IEq1.png),

where ![$$ {\mathbf{w}}_k^i $$](images/522202_1_En_8_Chapter/522202_1_En_8_Chapter_TeX_IEq2.png) is the estimated eigenvector of *A*[*k*] at *k*^(*th*) update and **ϕ**[*i*] is the actual *i*^(*th*) principal eigenvector computed from all collected samples by a conventional numerical analysis method. I measured the error of the eigenvalues at the *k*^(*th*) update as follows:

Abs error (*k*) = ![$$ \left|{d}_k^i-{\lambda}_k^i\right| $$](images/522202_1_En_8_Chapter/522202_1_En_8_Chapter_TeX_IEq3.png),

where ![$$ {d}_k^i $$](images/522202_1_En_8_Chapter/522202_1_En_8_Chapter_TeX_IEq4.png) is the estimated eigenvalue of *A*[*k*] at *k*^(*th*) update and ![$$ {\lambda}_k^i $$](images/522202_1_En_8_Chapter/522202_1_En_8_Chapter_TeX_IEq5.png) is the actual *i*^(*th*) principal eigenvalue computed from all collected samples by a conventional numerical analysis method.

I did the following:

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig12_HTML.png)

Figure 8-12

Absolute error of the first four principal eigenvalues with the adaptive algorithm (5.13)

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig11_HTML.png)

Figure 8-11

Direction cosines of the first four principal eigenvectors with the adaptive algorithm (5.13) (ideal value = 1)

1.  I plotted the direction cosines of the eigenvectors with the batch eigenvectors for each adaptive step. See Figure [8-11](#522202_1_En_8_Chapter.xhtml#Fig11).

2.  I plotted the errors of the eigenvalues with the batch eigenvalues for each adaptive step. See Figure [8-12](#522202_1_En_8_Chapter.xhtml#Fig12).

The final results are

*   Actual eigenvalues: [57.865, 36.259, 27.087, 21.833]

*   Adaptive eigenvalues: [57.857, 36.251, 27.054, 21.881]

These results demonstrate that the adaptive algorithm accurately computed the PCA features, which will create an 8x data compression in place of the voluminous raw data.

The Python code used here is same as in the section before this one.

## 8.4 Detecting Feature Anomalies

Here I used adaptive linear algorithms to detect anomalies in data.

### Yahoo Real Dataset

The dataset is `real_data.csv` and is derived from Yahoo Research Webscope: S5 - A Labeled Anomaly Detection Dataset [Yahoo Research Webscope]. Detecting anomalies in machines is an important machine learning task for the manufacturing market. I collected 8-dimensional data from machines, which have occasional anomalous readings. One method of detecting anomalies is to calculate the deviation of the current sample from its running median and compare it against a statistic.

I used the adaptive median algorithm (2.20) to calculate the running median and a simple statistic to detect anomalies. See the results in Figure [8-13](#522202_1_En_8_Chapter.xhtml#Fig13). I plotted the data in blue, the adaptive median in green, and the anomalies in red.

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig13_HTML.png)

Figure 8-13

Yahoo Webscope multidimensional real dataset with anomalies in several components detected by the adaptive median algorithm (2.20)

The following Python code is used on multidimensional dataset `X[nDim, nSamples]`:

```
from numpy import linalg as la
nSamples = X.shape[0]
nDim = X.shape[1]
w = np.zeros(shape=(nDim,1)) # stores adaptive eigenvectors
anam = np.zeros(shape=(nDim,nSamples))
mdks = np.zeros(shape=(nDim,nSamples))
for iter in range(nSamples):
cnt = iter + 1
# current data vector x
x = np.array(X.iloc[iter])
x = x.reshape(nDim,1)
#Eq.2.20
w = w + (1/(1 + iter)) * np.sign(x - w)
mdks[:,iter] = w.T
y = (np.abs(x-w) > 0.5*w) # Anomaly detection threshold
anam[:,iter] = y.T
```

### NOAA Dataset

The dataset name is `NOAA.arff`. The data sequence has eight components. Component F3 has an anomalous spike, as shown in Figure [8-14](#522202_1_En_8_Chapter.xhtml#Fig14).

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig14_HTML.png)

Figure 8-14

NOAA real-world dataset with an anomaly in one component only, detected by the adaptive median algorithm (2.20)

I used the adaptive median detection algorithm (2.20) on all components of this dataset. I used a simple algorithm of testing to determine an anomaly:

Anomaly = ABS (Sample_Value – Adaptive_Median) > 4 x Adaptive_Median.

Figure [8-15](#522202_1_En_8_Chapter.xhtml#Fig15) shows the data in blue, the adaptive median in green, and the anomalies in red. Clearly the adaptive median algorithm (2.20) detects the anomaly accurately.

![](images/522202_1_En_8_Chapter/522202_1_En_8_Fig15_HTML.png)

Figure 8-15

Anomaly in the NOAA dataset detected with the adaptive median algorithm (2.20)

The Python code used here is same as the previous example except for the detection threshold:

```
y = (np.abs(x-w) > 4*w) # Anomaly detection threshold.
```

Index A, B Adaptive algorithms batch processing approach learning process methodology advantage contributions matrix functions objective function real-world applications requirements solutions streaming data advantages conventional solutions disadvantages e-shopping data principal eigenvector Adaptive computation class separability data classification data features data representation machine learning/data analysis pattern recognition streaming data Adaptive linear algorithms constraints electricity dataset feature drift detection EVD non-stationary EVD semi-stationary INSECTS-incremental_balanced_norm.arff high volume/dimensional data compression data representation feature vectors Python code incoming data drift INSECTS-incremental_abrupt_balanced_norm.arff NOAA dataset non-stationary non-stationary data pretrained models repository requirement Yahoo real dataset Augmented Lagrangian 1 (AL1) algorithm Augmented Lagrangian 2 (AL2) algorithm Auto-association C Cholesky decomposition Computation/minor eigenvectors conjugate direction algorithm correlation matrix experiments gradient descent algorithm Newton-Raphson algorithm nonlinear optimization techniques non-stationary data non-stationary multi-dimensional objective functions random data vectors state-of-the-art algorithms stationary data steepest descent algorithm wider applicability Conjugate direction algorithm D, E, F Direction of arrival (DOA) Discrete cosine transform (DCT) G Generalized eigenvalue problems AL1 algorithms deflation homogeneous Python code weighted GEVD algorithm AL2 GEVD algorithms deflation homogeneous Python code weighted algorithm algorithms/objective functions continuity/regularity properties conventional method experimental results actual values AL1/AL2 algorithms algorithms/complexity/performance covariance matrix eigenvectors IT algorithm OJA algorithms PF algorithm RQ algorithm XU algorithm IT GEVD algorithm deflation homogeneous Python code weighted algorithm objective functions OJA GEVD algorithms deflation adaptive homogeneous algorithm Python code weighted algorithm XU GEVD algorithms pattern recognition penalty function Rayleigh quotient(RQ) signal processing symmetric-definite pencil symmetric matrix variations Gradient descent algorithm H Hebbian learning/Neural biology Hestenes-Stiefel method Hetero-associative network I, J Information theory (IT) adaptive algorithm convergence GEVD algorithm deflation homogeneous Python code weighted minor eigenvectors deflation homogeneous Python code weighted algorithm objective function uniform upper bound for symbol 104 f “Symbol”k Inverse square root adaptive algorithm experiments objective function Python code K Karhunen-Loeve theorem Karhunen-Loeve Transform (KLT) L Least mean squared error reconstruction (LMSER) Linear discriminant analysis (LDA) Linear transform data whitening definition features linear discriminant analysis multi-disciplines SeeMulti-disciplines derive principal component analysis singular value summarization M Matrix algebra problems adaptive mean/covariance asymptotic median correlation matrices covariance/inverses data correlation experiments handwritten character recognition inverse correlation normalized mean stationary and non-stationary streaming data 2D representation variations vector and correlation Minor eigenvectors adaptive algorithm AL2 algorithm deflation homogeneous Python code weighted algorithms/objective functions augmented Lagrangian method 1 deflation homogeneous Python code weighted covariance matrix decomposition algorithms experimental results information theory mean-squares error multimedia video transmission objective functions OJA algorithm deflation algorithm homogeneous algorithm Python code weighted algorithm original dimensional signal penalty function (PF) deflation homogeneous Python code weighted algorithm Python code Rayleigh quotient reconstructed images unified framework XU algorithm deflation homogeneous Python code weighted algorithm Multi-disciplines derive auto-association features Hebb learning hetero-associative network information theory optimization theory statistical pattern recognition Multiple signal classification (MUSIC) N Newton-Raphson algorithm Normalized mean algorithm cost function gradient descent algorithm objective function variations O Optimization theory P, Q Penalty function (PF) adaptive algorithm convergence time objective function uniform upper bound Principal component analysis (PCA) Eigenvector SeePrincipal eigenvector computation minor eigenvectors SeeMinor eigenvectors nonlinear optimization techniques Principal eigenvector computation actual values adaptive algorithm algorithms augmented Lagrangian 1 method augmented Lagrangian 2 method convergence results covariance matrix data sets diverse and practitioners experiments framework information theory (IT) multi-dimensional data objective functions Oja algorithm adaptive gradient algorithm auto-associative convergence time objective function penalty function (PF) rapid convergence Rayleigh quotient (RQ) real-world non-stationary data RQ/OJAN/LUO algorithms adaptive algorithms convergence objective function symmetric matrix unified framework Vectors *w*[*0*] XU algorithm adaptive algorithm convergence time objective function upper bound for symbol 104 f “Symbol”k Principal subspace learning algorithm R Rayleigh quotient (RQ) deflation homogeneous Python code weighted S, T, U, V, W, X, Y, Z Singular value decomposition (SVD) Square root/inverse square root adaptive algorithms Cholesky decomposition convergence properties correlation matrices covariance matrix data vectors experiments gradient descent algorithm handwritten MNIST number inverse SeeInverse square root objective function prominent applications Python code real-time matrix solution (A^½ and A^(–½)) State-of-the-art algorithms Stationary and non-stationary sequences Statistical pattern recognition Steepest descent algorithm computation cubic equation matrices Python code