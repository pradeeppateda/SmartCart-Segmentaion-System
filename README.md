# SmartCart Segmentation System

SmartCart Segmentation System is a customer analytics project implemented in a Jupyter Notebook. It prepares customer demographic, purchasing, engagement, and campaign-response data for customer segmentation and exploratory analysis.

The notebook works with the SmartCart customer dataset and transforms raw customer records into analysis-ready features such as age, customer tenure, total spending, and number of children. It also includes visual exploration using pair plots and correlation heatmaps, along with categorical encoding for downstream machine-learning analysis.

## Project goals

- Understand customer demographics and purchasing behavior.
- Clean and prepare customer data for analysis.
- Engineer meaningful customer-level features.
- Explore relationships between income, spending, recency, engagement, and campaign response.
- Create a reusable foundation for data-driven customer segments and targeted marketing.

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

## Requirements

Install Python 3.9 or newer and the notebook dependencies:

```bash
pip install jupyter pandas matplotlib seaborn scikit-learn
```

Alternatively, install the packages in a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
# Windows PowerShell: .venv\Scripts\Activate.ps1
pip install jupyter pandas matplotlib seaborn scikit-learn
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

## Important notes

- The notebook currently calculates age using the fixed expression `2026 - Year_Birth`. For repeatable production analysis, replace this with a configurable reference date or the current year.
- The dataset contains customer demographic and purchasing information. Review licensing, privacy, and redistribution requirements before publishing the CSV.
- The notebook is designed for analysis and experimentation; it does not currently provide a deployed application or prediction API.
- Clusters should be interpreted using their feature profiles and business context rather than by cluster number alone.

## Possible extensions

- Add the dataset and document its source and license.
- Standardize numerical features before distance-based clustering.
- Compare clustering methods and select the number of segments with metrics such as silhouette score.
- Profile each segment by spending, recency, channel preference, income, and campaign response.
- Export customer segment assignments to CSV.
- Refactor notebook logic into reusable Python modules and add automated tests.
- Add a dashboard for marketing and customer-retention analysis.

## License

No license has been provided in this repository. Unless a license is added, the repository contents remain under the copyright of their respective owner.
