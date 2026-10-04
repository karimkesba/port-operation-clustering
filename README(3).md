# Port Operation Clustering

## Overview

This project applies **Unsupervised Learning** techniques to port
operation data in order to investigate whether vessel operations can be
grouped into distinct operational profiles.

The analysis focuses on operational and performance variables such as
container volume, berth time, crane activity, crane efficiency, idle
time, and operational delays.

A key objective of the project was not only to create clusters, but also
to evaluate whether the dataset actually contains a strong and
meaningful cluster structure.

------------------------------------------------------------------------

## Dataset

The dataset contains **810 vessel-operation records** and **23 columns**
before preprocessing.

Important variables include:

-   Vessel information
-   Operator and shipping line
-   Total TEUs
-   Moves
-   Berth Time
-   Berth Working Time
-   Gross Crane Time
-   Crane Ratio
-   Crane Idle
-   BPR
-   BCH
-   Quay Crane Breakdown
-   Waiting Container
-   Waiting Internal Truck
-   Gear Box Handling
-   Bad Weather
-   System Failure

After handling missing values, the final dataset contained **807
records**.

------------------------------------------------------------------------

## Project Workflow

The notebook follows this workflow:

1.  Data loading and inspection
2.  Missing-value analysis
3.  Data cleaning
4.  Exploratory Data Analysis
5.  Feature selection
6.  Skewness analysis
7.  Correlation analysis
8.  Feature scaling
9.  K-Means clustering
10. Hierarchical clustering
11. DBSCAN
12. PCA
13. K-Means clustering after PCA
14. Evaluation and interpretation

------------------------------------------------------------------------

## Data Preprocessing

### Missing Values

Three records contained missing values across several
operational/categorical columns and were removed.

The `BCH (ET) (bph)` column contained 71 missing values and was filled
using its median.

After preprocessing:

-   Rows: **807**
-   Columns: **23**
-   Missing values: **0**

### BCH Inspection

`BCH (ET) (bph)` contained two negative values and several extreme
values, including a maximum above 600.

Because these values were considered problematic for the clustering
feature space, BCH was excluded from the final clustering features.

### Feature Selection

The initial clustering feature set contained 14 numerical operational
variables.

Correlation analysis revealed strong redundancy, especially between:

-   `Total Teus` and `Moves`
-   `Berth Time` and `Berth Working Time`
-   `Moves` and `Gross Crane Time`
-   `Total Teus` and `Gross Crane Time`

To reduce redundancy, `Moves` and `Berth Working Time` were removed from
the final clustering feature set.

The final feature set contained 12 variables:

``` text
Total Teus
Berth Time
Gross Crane Time
Crane Ratio
Crane Idle
BPR (mph)
QUAY CRANE BREAKDOWN
WAITING CONTAINER
WAITING INTERNAL TRUCK
GEAR BOX HANDLING
BAD WEATHER
SYSTEM FAILURE
```

No train/test split was used because this is an unsupervised learning
problem without a target variable.

------------------------------------------------------------------------

## Feature Scaling

The selected features were standardized using `StandardScaler` before
applying distance-based clustering algorithms.

------------------------------------------------------------------------

## K-Means Clustering

K-Means was evaluated for values of `K` from 2 to 10.

The best Silhouette Score was:

``` text
K = 2 → 0.3564
```

The results did not show a very strong separation between clusters.

------------------------------------------------------------------------

## Hierarchical Clustering

Agglomerative Hierarchical Clustering with Ward linkage was evaluated
for `K = 2` through `K = 10`.

The best Silhouette Score was:

``` text
K = 2 → 0.3258
```

A dendrogram was also generated to inspect the hierarchical structure of
the observations.

The results were weaker than the K-Means results.

------------------------------------------------------------------------

## DBSCAN

DBSCAN was tested using several combinations of:

-   `eps`
-   `min_samples`

The highest Silhouette Score obtained was:

``` text
eps = 1.1
min_samples = 5
Silhouette = 0.4394
```

However, this configuration produced:

``` text
Cluster 0 → 567
Cluster 1 → 5
Noise      → 235
```

Because one cluster contained only 5 observations and a large number of
observations were classified as noise, this result was not considered a
meaningful operational segmentation.

Therefore, the highest DBSCAN Silhouette Score was not treated as
evidence that DBSCAN produced the best practical clustering solution.

------------------------------------------------------------------------

## PCA

PCA was used to investigate whether dimensionality reduction could
provide a clearer representation of the data.

The first two principal components explained:

``` text
PC1 = 41.93%
PC2 = 11.20%

Total = 53.14%
```

A PCA representation retaining approximately 90% of the variance
required **7 components**.

K-Means was then applied to the PCA representation.

The best result was:

``` text
K = 2 → Silhouette = 0.3883
```

The cluster sizes were:

``` text
Cluster 0 → 464
Cluster 1 → 343
```

This was the most balanced and interpretable clustering result obtained
in the project.

------------------------------------------------------------------------

## Results and Interpretation

The clustering experiments did not reveal strongly separated natural
groups in the dataset.

The best practical K-Means result after PCA achieved a Silhouette Score
of approximately **0.39**, while the PCA visualization showed
substantial overlap between observations.

The DBSCAN result produced a higher numerical Silhouette Score, but its
cluster structure was not practically useful because of the very small
secondary cluster and large number of noise points.

Therefore, the main conclusion of this project is:

> **The dataset does not exhibit strong natural cluster separation based
> on the selected operational features.**

This is an important result in unsupervised learning. A clustering
algorithm can always assign observations to groups, but that does not
necessarily mean that meaningful natural groups exist in the data.

------------------------------------------------------------------------

## Key Takeaways

-   Scaling is important for distance-based clustering.
-   Correlation analysis helped identify redundant features.
-   K-Means provided more balanced clusters than DBSCAN.
-   Hierarchical clustering produced weaker separation.
-   DBSCAN identified a large amount of noise.
-   PCA slightly improved K-Means clustering performance.
-   The overall feature space remains highly overlapping.
-   The dataset may require additional or more discriminative
    operational features to obtain stronger natural clusters.

------------------------------------------------------------------------

## Technologies

-   Python
-   Pandas
-   NumPy
-   Matplotlib
-   Seaborn
-   Scikit-learn
-   SciPy
-   Jupyter Notebook

------------------------------------------------------------------------

## Project Structure

``` text
Port_Operation_Clustering/
│
├── Port_Operation_Clustering.ipynb
├── README.md
└── dataset/
    └── SimulasiData-edited-051225 (1).xlsx
```

------------------------------------------------------------------------

## Conclusion

This project demonstrates a complete unsupervised learning workflow for
real-world port operation data, including preprocessing, exploratory
analysis, feature selection, scaling, K-Means, hierarchical clustering,
DBSCAN, PCA, and cluster evaluation.

Rather than forcing a strong clustering interpretation, the analysis
highlights an important practical finding: **the available operational
variables do not provide clear natural separation between vessel
operations**.

This suggests that future work could focus on engineering more
discriminative operational features or incorporating additional
information related to vessel type, terminal conditions, operational
causes, or other factors that may better distinguish port-operation
profiles.
