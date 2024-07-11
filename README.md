# Comparative Analysis script of RTStruct Segmentations for Radiophysical Assessment

## Objective:
This notebook is dedicated to the systematic analysis of segmentation results, comparing machine-generated RTStruct files against reference RTStructs created manually by medical professionals. The goal is to quantify the similarity and precision of automated segmentations relative to the gold standard of human expertise, specifically within the context of radiophysics.


**Methodology**:
I use a comprehensive suite of spatial and statistical metrics to assess segmentation accuracy and similarity:

- **Spatial Overlap and Distance Metrics**:
  - **Dice Similarity Coefficient (DSC)**: Measures the overlap between two volumes, providing a statistical validation of segmentation precision.
  - **Jaccard Index**: Quantifies the similarity and diversity between sample sets, indicating the proportionate size of the intersection divided by the union of the sample sets.
  - **Mean Surface Distance (MSD)**: Calculates the average Euclidean distance between the surfaces of two volumes, offering insights into the contour accuracy.
  - **Volume Overlap Error (VOE)**: Represents the proportion of the total volume that is over-segmented or under-segmented relative to the reference, complementing the Dice coefficient by providing error rates.
  - **Hausdorff Distance**: Evaluates the maximum distance of the dataset boundary points between the predicted and reference segmentations, highlighting the worst-case scenario of boundary prediction.
  - **Variation of Information (VI)**: Measures the amount of information lost and gained in the segmentation process, reflecting the complexity and precision of the information captured by the segmentation.
  - **Cosine Similarity**: Assesses the cosine angle between the multidimensional representations of the segmented volumes, useful for understanding the orientation and agreement in the segmented shapes.

- **Statistical Analysis of Volumes and Diameters**:
  - The volumes and maximum diameters of segmented ROIs (Regions of Interest) are extracted and statistically analyzed. Descriptive statistics (mean, median, standard deviation, minimum, maximum, and quartiles) are calculated to provide a comprehensive overview of the data distribution and central tendencies.
  - Hypothesis testing will be conducted to compare the distributions of volumes and diameters between the machine-generated and manual segmentations, using t-tests or non-parametric tests depending on the data distribution.

**Data Handling**:
- The segmentation results, including both reference and generated RTStructs, are first converted from DICOM format into voxel arrays for computational analysis. Metrics are computed at varying tolerance levels (gap errors), allowing us to evaluate the robustness of segmentations against small positional discrepancies.


## Explanation of Similarity Metrics

### 1. **Dice Similarity Coefficient (DSC)**
The **Dice Similarity Coefficient**, also known as Sørensen-Dice index, measures the similarity between two sets. It is formulated as twice the number of pixels (or voxels) in the intersection divided by the sum of pixels in each image. This metric is widely used in image analysis to compare the spatial similarity between two segmented images.

```markdown
DSC = (2 * |X ∩ Y|) / (|X| + |Y|)
```
- **X** and **Y** represent the binary volumes of the two segmentations.
- The intersection (X ∩ Y) indicates the pixels where both segmentations coincide.
- Values close to 1 indicate high similarity, while values close to 0 indicate little or no similarity.

### 2. **Mean Surface Distance (MSD)**
**Mean Surface Distance** calculates the average Euclidean distance between the boundaries of two segmented volumes. This measure is particularly useful for assessing the accuracy of object contours, a crucial aspect in medical applications where the precision of contouring can impact diagnosis or treatment.

```markdown
MSD = (Σ d(p1, P2) + Σ d(p2, P1)) / (n1 + n2)
```
- **d(p1, P2)** is the minimum distance from each point p1 on the contour of the first volume to the set of points P2 of the second volume.
- **n1** and **n2** are the numbers of points on each contour.
- Low MSD values indicate high accuracy of contours between the compared segmentations.

### 3. **Volume Overlap Error (VOE)**
**Volume Overlap Error** measures the degree of overlap between two segmented volumes. It is a complementary metric to DSC and is often used to quantify the overlap error, helping to understand under-segmentation or over-segmentation errors.

```markdown
VOE = 1 - (|X ∩ Y| / |X ∪ Y|)
```
- **X ∪ Y** represents the union of the volumes of the two segmentations.
- Values close to 0 indicate almost perfect overlap, while higher values indicate less overlap.

### 4. **Hausdorff Distance (HD)**
**Hausdorff Distance** measures the greatest of all the distances from a point in one segmented contour to the closest point in the other segmentation, providing a measure of the worst-case scenario of boundary prediction. This metric is crucial for identifying cases where the segmentation is generally accurate but contains extreme errors.

```markdown
HD = max(h(X, Y), h(Y, X))
```
- **h(X, Y)** is the Hausdorff distance from X to Y, calculated as the maximum of the minimum distances from each point of X to Y.
- A high value may indicate significant errors, even if other similarity measures appear acceptable.

### 5. **Jaccard Index**
The **Jaccard Index** is a measure of similarity between two sets, calculated as the size of the intersection divided by the size of the union of the sets. Similar to DSC, but without the factor of two, making it more sensitive to differences.

```markdown
Jaccard = |X ∩ Y| / |X ∪ Y|
```
- Values close to 1 indicate strong similarity, while values close to 0 indicate dissimilarity.

### 6. **Variation of Information (VI)**
**Variation of Information** is an information theory metric that quantifies the amount of information lost and gained when transitioning from one segmentation to another. It evaluates how much exclusive information each segmentation holds and their redundancy.

```markdown
VI = H(X) + H(Y) - 2I(X, Y)
```
- **H(X)** and **H(Y)** are the entropies of the segmentations, and **I(X, Y)** is the mutual information between X and Y.
- Low values indicate that the segmentations share a lot of information, while high values indicate significant divergence.

### 7. **Cosine Similarity**
**Cosine Similarity** measures the cosine of the angle between two attribute vectors in a multi-dimensional space. In the context of segmentation, it compares the similarity in direction between vectors formed from segmented images.

```markdown
Cosine Similarity = (X • Y) / (||X|| ||Y||)
```
- **X • Y** is the dot product of the vectors, and **||X||**, **||Y||** are the norms of these vectors.
- Values close to 1 indicate high similarity.

These metrics provide a comprehensive evaluation of the quality of segmentations by accounting for various aspects of similarity and accuracy in segmented shapes.
