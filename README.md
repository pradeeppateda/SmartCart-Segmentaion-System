# SmartCart Segmentation System

SmartCart Segmentation System is a customer analytics project implemented in a Jupyter Notebook. It prepares customer demographic, purchasing, engagement, and campaign-response data for customer segmentation using K-Means clustering. The notebook includes elbow method and silhouette score analysis to determine the optimal number of customer segments.

The notebook works with the SmartCart customer dataset and transforms raw customer records into analysis-ready features such as age, customer tenure, total spending, and number of children. It also includes visual exploration using pair plots and correlation heatmaps, along with categorical encoding, feature standardization, and unsupervised machine-learning clustering for downstream customer segmentation.

## Project goals

- Understand customer demographics and purchasing behavior through exploratory analysis.
- Clean and prepare customer data for machine-learning analysis.
- Engineer meaningful customer-level features (age, tenure, spending, children).
- Explore relationships between income, spending, recency, engagement, and campaign response.
- Standardize and encode features for clustering.
- Determine the optimal number of customer segments using the **Elbow Method** and **Silhouette Score**.
- Apply K-Means clustering to segment customers into homogeneous groups.
- Profile each segment by demographic, behavioral, and spending characteristics.
- Create a reusable foundation for data-driven customer segmentation and targeted marketing strategies.

## Repository contents

```text
.
└── SmartCart Segmentation System.ipynb
```

The notebook expects a CSV file named `smartcart_customers.csv` in the notebook's working directory. That file is not currently included in the repository, so add the dataset locally before running the notebook.

## Dataset overview

The notebook initially works with 2,240 customer records and 22 source columns, including:

- `ID` and `Year_Birth`
- `Education` and `Marital_Status`
- `Income`
- `Kidhome` and `Teenhome`
- `Dt_Customer` and `Recency`
- Product spending fields such as `MntWines`, `MntFruits`, `MntMeatProducts`, `MntFishProducts`, `MntSweetProducts`, and `MntGoldProds`
- Purchase-channel activity: web, catalog, store, and deal purchases
- `NumWebVisitsMonth`, `Complain`, and `Response`

## Analysis workflow

### 1. Load and inspect the data

The notebook loads the CSV with pandas and examines the first rows, dataset dimensions, and missing-value counts.

### 2. Clean missing values

Missing values in `Income` are replaced with the column mean. The notebook then verifies that the dataset contains no remaining missing values in the inspected fields.

### 3. Engineer customer features

The following derived features are created:

- `Age`: calculated from the birth year using the notebook's 2026 reference year.
- `total days`: number of days since the customer joined, measured relative to the latest customer date in the dataset.
- `total_spending`: sum of spending across wine, fruit, meat, fish, sweet, and gold products.
- `children`: sum of `Kidhome` and `Teenhome`.

### 4. Normalize categorical values

Education categories are consolidated into three groups:

- `undergraduate`
- `graduate`
- `postgraduate`

Marital-status values are consolidated into:

- `partner`
- `alone`

### 5. Select analysis features

Identifier and redundant source columns are removed, including the original customer ID, birth year, household-child fields, customer date, and individual product-spending columns. The aggregated `total_spending` feature is retained.

### 6. Explore relationships and remove outliers

The notebook uses Seaborn and Matplotlib to generate pair plots and a numerical correlation heatmap. It also filters extreme observations using the following rules:

- `Income < 600000`
- `Age < 90`

After this filtering step, the notebook reports 2,236 rows.

### 7. Encode categorical features

`Education` and `Marital_Status` are converted to numerical columns with scikit-learn's `OneHotEncoder`, producing a modeling-ready dataframe.

### 8. Standardize numerical features

All numerical features are standardized using scikit-learn's `StandardScaler` to ensure equal weighting in distance-based clustering algorithms. Standardization is essential for K-Means, which uses Euclidean distance.

### 9. Determine optimal number of clusters: Elbow Method

The notebook applies K-Means clustering for a range of cluster counts (typically 1–10) and computes the **inertia** (within-cluster sum of squared distances) for each value of *k*. The inertia curve is plotted to identify the "elbow point" where the rate of decrease slows significantly. This elbow typically indicates a good trade-off between model complexity and goodness of fit.

### 10. Evaluate clustering quality: Silhouette Score

For each value of *k*, the notebook calculates the **Silhouette Score**, which measures how similar each customer is to their own cluster compared to other clusters. The score ranges from –1 to +1:

- **+1**: Customer is very similar to their cluster and dissimilar to other clusters.
- **0**: Customer is on the boundary between clusters.
- **–1**: Customer may be assigned to the wrong cluster.

The average silhouette score across all customers provides a single metric to select the optimal *k*. Higher scores indicate better-defined, more cohesive clusters.

### 11. Apply final K-Means clustering

Using the optimal cluster count determined by elbow method and silhouette score analysis, the notebook fits a K-Means model to the standardized data and assigns each customer to a cluster segment.

### 12. Profile and interpret segments

The notebook computes summary statistics (mean income, spending, age, recency, campaign response, etc.) for each cluster to understand and interpret segment characteristics.

## Requirements

Install Python 3.9 or newer and the notebook dependencies:

```bash
pip install jupyter pandas matplotlib seaborn scikit-learn numpy
```

Alternatively, install the packages in a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
# Windows PowerShell: .venv\Scripts\Activate.ps1
pip install jupyter pandas matplotlib seaborn scikit-learn numpy
```

## Usage

1. Clone the repository:

   ```bash
   git clone https://github.com/pradeeppateda/SmartCart-Segmentaion-System.git
   cd SmartCart-Segmentaion-System
   ```

2. Place `smartcart_customers.csv` in the repository root, or update the path in the notebook.

3. Start Jupyter:

   ```bash
   jupyter notebook
   ```

4. Open `SmartCart Segmentation System.ipynb`.

5. Run the cells from top to bottom.

## Clustering methodology

### Elbow Method

The elbow method identifies the "knee" in the inertia curve. As *k* increases, inertia decreases, but beyond the elbow, improvements are marginal. By plotting inertia vs. *k*, you can visually identify the optimal cluster count.

### Silhouette Score

The silhouette score quantifies cluster quality. For each point, it measures:

```
silhouette = (b - a) / max(a, b)
```

where:
- **a** = average distance from the point to other points in its cluster
- **b** = average distance from the point to points in the nearest other cluster

The silhouette score for all points is averaged to yield a single metric. Scores closer to +1 indicate well-separated, compact clusters.

### K-Means Algorithm

K-Means partitions customers into *k* clusters by:

1. Initializing *k* random centroids.
2. Assigning each customer to the nearest centroid.
3. Recomputing centroid positions as the mean of assigned customers.
4. Repeating steps 2–3 until convergence or max iterations reached.

The algorithm minimizes within-cluster variance, making it ideal for discovering natural customer groups by purchasing and demographic behavior.

## Important notes

- The notebook currently calculates age using the fixed expression `2026 - Year_Birth`. For repeatable production analysis, replace this with a configurable reference date or the current year.
- **Feature standardization is critical for K-Means.** Unstandardized features with large ranges (e.g., income in thousands) will dominate distance calculations. Always standardize before applying K-Means.
- The elbow method is subjective; there may not be a sharp elbow. Use silhouette scores alongside visual inspection to validate the chosen *k*.
- Silhouette scores of 0.5 or higher generally indicate good clustering quality.
- The dataset contains customer demographic and purchasing information. Review licensing, privacy, and redistribution requirements before publishing the CSV.
- Clusters should be interpreted using their feature profiles and business context rather than by cluster number alone.
- Results depend on random initialization. Set `random_state` in K-Means for reproducibility.

## Possible extensions

- Add the dataset and document its source and license.
- Compare K-Means with other clustering methods (hierarchical clustering, DBSCAN, Gaussian Mixture Models).
- Apply dimensionality reduction (PCA) to visualize clusters in 2D or 3D.
- Implement automated elbow detection using the "knee point" detection algorithm.
- Profile each segment in detail: segment size, median income, spending patterns, recency, channel preference, and campaign response rate.
- Export customer segment assignments to CSV for downstream analysis.
- Validate cluster stability using bootstrap or cross-validation approaches.
- Refactor notebook logic into reusable Python modules and add automated tests.
- Build a dashboard for marketing and customer-retention analysis by segment.
- Develop targeted retention or upsell campaigns based on segment characteristics.

## License

No license has been provided in this repository. Unless a license is added, the repository contents remain under the copyright of their respective owner.
