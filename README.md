# Comparative Analysis script of RTStruct Segmentations for Radiophysical Assessment

## Objective:
This project focuses on the systematic evaluation of automated and manual segmentations of brain metastases, utilizing RTStruct files. The primary objective is to assess the precision and similarity of machine-generated segmentations in comparison to those created manually by medical experts, with the goal of quantifying the accuracy of automated segmentations relative to expert standards.

Although not directly implementing RANO-BM criteria, this analysis complements the clinical use of these criteria by leveraging a suite of computational metrics to evaluate the segmentation quality. Metrics such as the Dice Similarity Coefficient, Jaccard Index, and 95th percentile Hausdorff Distance are employed to measure the overlap and boundary precision between the automated and expert segmentations. This project is part of a broader effort to standardize and improve radiological workflows, facilitating more efficient patient follow-ups in clinical practice and potentially reducing the time required for segmentation, particularly for brain metastases, which are a complex and heterogeneous challenge in oncology.

By addressing the limitations of traditional bidimensional measurement approaches in the RANO-BM criteria (which rely on the product of the largest height and width), this project aims to propose more robust volumetric methods for evaluating tumor response to treatment. The ultimate goal is to refine the segmentation processes used in both clinical trials and routine care, contributing to a more accurate and standardized tumor assessment workflow.

**Methodology**:
I use a comprehensive suite of spatial and statistical metrics to assess segmentation accuracy and similarity:

- **Spatial Overlap and Distance Metrics**:
  - **Dice Similarity Coefficient (DSC)**: Measures the overlap between two volumes, providing a statistical validation of segmentation precision.
  - **Jaccard Index**: Quantifies the similarity and diversity between sample sets, indicating the proportionate size of the intersection divided by the union of the sample sets.
  - **Mean Surface Distance (MSD)**: Calculates the average Euclidean distance between the surfaces of two volumes, offering insights into the contour accuracy.
  - **Volume Overlap Error (VOE)**: Represents the proportion of the total volume that is over-segmented or under-segmented relative to the reference, complementing the Dice coefficient by providing error rates.
	- **Hausdorff Distance (95th percentile)**: Measures the distance between the surface points of the segmented volumes, excluding the top 5% of the largest errors. This metric provides a more robust evaluation of the segmentation quality by focusing on the majority of points and reducing the influence of outliers, giving a more accurate representation of the model’s overall performance.
  - **Variation of Information (VI)**: Measures the amount of information lost and gained in the segmentation process, reflecting the complexity and precision of the information captured by the segmentation.
  - **Cosine Similarity**: Assesses the cosine angle between the multidimensional representations of the segmented volumes, useful for understanding the orientation and agreement in the segmented shapes.

- **Statistical Analysis of Volumes and Diameters**:
  - The volumes and maximum diameters of segmented ROIs (Regions of Interest) are extracted and statistically analyzed. Descriptive statistics (mean, median, standard deviation, minimum, maximum, and quartiles) are calculated to provide a comprehensive overview of the data distribution and central tendencies.

**Data Handling**:
- The segmentation results, including both reference and generated RTStructs, are first converted from DICOM format into voxel arrays for computational analysis.


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
**Hausdorff Distance (95th percentile)** measures the distance between a point on one segmented contour to the closest point on the other, but considers only the 95th percentile of these distances. This provides a robust measure of segmentation accuracy by focusing on the majority of the points and reducing the impact of outliers. It offers a better representation of typical segmentation performance compared to the maximum distance, which is more sensitive to extreme errors.

```markdown
HD_95 = max(h_95(X, Y), h_95(Y, X))
```
- h_95(X, Y) is the 95th percentile of the distances from each point of X to Y.
- This approach helps to ignore extreme outliers and focuses on overall segmentation accuracy.

A high value in the 95th percentile Hausdorff Distance may still indicate significant discrepancies between the predicted and reference segmentations, but it focuses on typical errors rather than outliers. Even when other similarity measures appear acceptable, a high value in this metric suggests there are notable differences in the majority of the boundary points. And a low value reflects that most of the segmented points are well-aligned between the prediction and reference, indicating a more accurate segmentation.”

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
