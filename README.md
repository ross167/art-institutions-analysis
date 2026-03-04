# What Makes a Painting Display-Worthy at the Met?
## Predicting Gallery Display Likelihood at The Metropolitan Museum of Art

This project uses data from The Met Museum's open-access collection to investigate which paintings end up on gallery walls and which remain in storage. It combines data collection via API, data cleaning and feature engineering, exploratory data analysis using SQL, classification modelling, and hypothesis testing to identify the key drivers of display likelihood across 9,005 paintings.

The project was completed as the final project for the Ironhack Data Analytics Bootcamp (February 2026) and presented at the Ironhack Hackshow.

---

## Project Structure

```
met_paintings_display_analysis/
│
├── data/
│   ├── raw/                        # Raw dataset from notebook 01
│   ├── clean/                      # Cleaned dataset from notebook 02
│   └── viz/                        # Exported CSVs for Tableau visualisation
│       ├── tableau_paintings.csv
│       ├── tableau_departments.csv
│       ├── tableau_tags.csv
│       ├── tableau_tags_top20.csv
│       ├── tableau_tags_bottom20.csv
│       ├── tableau_acquisition.csv
│       ├── tableau_size.csv
│       └── tableau_collection_age.csv
│
├── images/                         # Charts and visualisations from EDA
│
├── notebooks/
│   ├── 01_data_collection.ipynb
│   ├── 02_cleaning_and_features.ipynb
│   ├── 03_eda_and_sql.ipynb
│   ├── 04_modelling.ipynb
│   └── 05_hypothesis_testing.ipynb
│
├── met_paintings_display_analysis.twbx   # Tableau workbook
└── README.md
```

---

## Notebooks Overview

### 01_data_collection.ipynb
Downloads The Met Museum's full collection CSV from GitHub (approx 470,000 objects), filters to paintings only, then enriches each painting with structured measurements and subject tags via The Met's Collection API. Creates the `is_on_display` target variable from live API gallery number data. Saves the raw dataset for cleaning.

**Key outputs:** `data/raw/met_paintings_raw.csv`

### 02_cleaning_and_features.ipynb
Takes the raw dataset and prepares it for analysis and modelling. Handles missing values, cleans and standardises existing columns, and engineers new features including painting age, years in collection, area in cm², artist work count, and acquisition type. Encodes categorical variables for modelling. Saves the clean dataset.

**Key outputs:** `data/clean/met_paintings_clean.csv`

### 03_eda_and_sql.ipynb
Loads the clean dataset into SQLite and uses SQL queries alongside pandas and matplotlib to explore display patterns across departments, nationalities, mediums, sizes, and time periods. Identifies the most promising features for modelling and documents key findings. Saves charts to the images folder.

**Key outputs:** `images/` (EDA charts)

### 04_modelling.ipynb
Builds three classification models (decision tree, logistic regression, random forest) to predict whether a painting is on display. Includes statistical feature testing, model comparison, hyperparameter tuning via GridSearchCV, and cross-validation. Documents model performance and feature importances.

**Key outputs:** Model performance metrics, feature importance rankings, odds ratios

### 05_hypothesis_testing.ipynb
Tests five specific hypotheses about display likelihood using Mann-Whitney U tests, chi-squared tests, and tag-level analysis. Constructs a composite profile of the ideal display-worthy painting. Exports clean CSVs for Tableau visualisation.

**Key outputs:** `data/viz/` (all Tableau export files)

---

## Setup and Installation

### Requirements

This project requires Python 3.9 or above. The following packages are needed:

```
pandas
numpy
requests
matplotlib
seaborn
scikit-learn
scipy
sqlite3 (built into Python standard library)
jupyter
```

Install all dependencies with:

```bash
pip install pandas numpy requests matplotlib seaborn scikit-learn scipy jupyter
```

### Running the Notebooks

Run the notebooks in order. Each notebook saves its output for the next one to load, so the sequence matters.

```bash
jupyter notebook
```

Then open and run each notebook from 01 to 05 in sequence.

**Important:** Notebook 01 makes approximately 9,005 API calls to The Met Collection API with a 100ms delay between requests. This takes roughly two hours to complete. The raw CSV download from GitHub is approximately 300MB. Both steps are one-time operations. If you already have the raw dataset saved, you can skip directly to notebook 02.

---

## Data Sources

**The Met Museum Open Access CSV**
Full collection metadata published by The Met on GitHub. Updated regularly. Approximately 470,000 objects covering all departments.
Source: https://github.com/metmuseum/openaccess
Licence: Creative Commons Zero (CC0)

**The Met Collection API**
Used to enrich each painting with structured measurements (height and width in cm) and subject tags. The API also provided live gallery number data used to derive the `is_on_display` target variable.
API documentation: https://metmuseum.github.io/

**Important note on display status:** The `is_on_display` field was derived from live API data at the point of collection, not from the static GitHub CSV. Display status reflects the collection at one moment in time and will have changed since.

---

## Key Findings

**Overall display rate:** Only 15.3% of Met paintings are on display at any given time. The remaining 84.7% are in storage.

**Hypothesis 1 (Proved):** Displayed paintings are statistically significantly larger than stored ones, with a median area of 4,839 cm² versus 3,434 cm². Very small paintings are at the greatest disadvantage.

**Hypothesis 2 (Proved):** Acquisition type is a significant predictor of display status (Cramér's V 0.257). Bequests have a display rate of 29.3%, more than double that of gifts at 9.1%, despite gifts accounting for 55% of the collection.

**Hypothesis 3 (Proved with caveat):** Paintings held longer are more likely to be on display (point-biserial correlation 0.109), but the relationship is non-linear. Display likelihood dips in the 21-40 year band before rising steadily, peaking at 22.2% for paintings held 81-100 years.

**Hypothesis 4 (Not proved as stated):** The nationality group with the highest display rate is Italian (53.6%), not American. French (43.2%) and Flemish (41.3%) follow. A composite profile of the ideal Met painting can be constructed but its predicted display probability is only 19.1%, confirming that display-worthiness is multifactorial.

**Hypothesis 5 (Proved):** Christian religious subjects are displayed at four to five times the overall average rate. Jesus (69.8%), Virgin Mary (61.4%), and multiple saints all exceed 60%. Non-Western religious and cultural subjects including Buddhist and Hindu iconography returned display rates of 0-2.6% despite sufficient sample sizes. This is arguably the strongest qualitative finding in the project.

**Best model:** Tuned random forest achieved 90.2% accuracy and an F1 score of 71.0% on the test set, correctly identifying 77.9% of displayed paintings at a precision of 65.2%.

---

## Tableau Visualisation

The project includes a Tableau workbook (`met_paintings_display_analysis.twbx`) with four dashboards:

**Met Museum Paintings - Interactive Overview**
An interactive master dashboard connecting display rate by department, acquisition type, size quartile, and collection age band. Click any bar to filter all other charts simultaneously.

**What gets displayed at the Met?**
Overview dashboard combining the 15.3% KPI and display rate by department.

**What predicts display?**
Standalone charts for acquisition type, size quartile, and collection age band with reference lines at the 15.3% overall average.

**Which subjects get displayed?**
Side-by-side comparison of the top 20 and bottom 20 subject tags by display rate, showing the dominance of Christian iconography versus the near-zero display rates for non-Western subjects.

To open the workbook, download Tableau Public (free) from https://public.tableau.com and open the `.twbx` file directly. All data sources are embedded within the workbook file.

---

## Limitations

1.The target variable is a snapshot in time. Display status changes regularly as paintings rotate on and off gallery walls.
2.No data was available on physical condition, conservation status, loan activity, or curatorial strategy, all of which are likely significant factors in display decisions.
3.Department may act as a proxy for institutional gallery space rather than painting quality, as some departments have permanent dedicated galleries.
4.Class imbalance (15.3% on display) means the model is better at identifying stored paintings than displayed ones.
5.The tag data has incomplete coverage. The subject matter analysis reflects tagged paintings only and may not be representative of the full collection.
6.Findings apply to paintings only and cannot be generalised to the broader Met collection of sculptures, photographs, drawings, or decorative arts.
7.Label encoding imposes an arbitrary ordinal relationship on categorical features, which is a particular concern for logistic regression.

---

## Author

Ross Coverdale
Data-Driven Growth Consultant and Ironhack Data Analytics Bootcamp Student
Madrid, Spain | February 2026
