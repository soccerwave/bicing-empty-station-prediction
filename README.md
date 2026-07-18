# Predicting Bicing Station Empty Risk Within 60 Minutes

An end-to-end machine learning project that predicts whether a Barcelona Bicing station will become empty within the next 60 minutes.

The project uses more than 24 million historical station-status snapshots and combines current availability, recent station history, temporal variables, and location information.

## Project objective

The task is formulated as a binary classification problem:

- **1:** the station becomes empty at least once during the following 60 minutes while it remains open.
- **0:** the station does not become empty during that period.

Only observations from open stations with at least one available bicycle are considered eligible for prediction.

## Data source

The data comes from the official Barcelona Open Data portal:

https://opendata-ajuntament.barcelona.cat/data/es/dataset/bicing

The analysis covers historical Bicing station-status data from **August 2018 to March 2019**.

The raw files are not included in this repository because their total size is several gigabytes.

## Dataset

The main variables include:

- Station ID
- Timestamp
- Available bicycles
- Free slots
- Station status
- Latitude
- Longitude

After loading and initial cleaning, the dataset contained approximately **24.4 million observations**.

## Data preparation

The workflow includes:

- Timestamp parsing and validation
- Duplicate removal
- Removal of anomalous open-station records with zero bicycles and zero free slots
- Examination of daily observation and station coverage
- Construction of the 60-minute prediction target
- Chronological train, validation, and test splits

## Feature engineering

The models use:

- Current number of available bicycles
- Number of free slots
- Observed station capacity
- Occupancy ratio
- Bicycles available approximately 15 and 30 minutes earlier
- Changes in bicycle availability over the previous 15 and 30 minutes
- Hour of day
- Day of week
- Weekend indicator
- Month
- Latitude and longitude

## Temporal split

A chronological split was used to reduce temporal leakage:

| Dataset | Period | Observations | Positive rate |
|---|---|---:|---:|
| Train | August–December 2018 | 9,521,722 | 9.00% |
| Validation | January 2019 | 2,966,095 | 9.11% |
| Test | February 2019 | 1,711,745 | 7.42% |

March was excluded from the main evaluation because station coverage declined severely and the positive target rate fell to approximately 1.8%.

## Models compared

Six predictive approaches were evaluated:

1. Dummy Classifier
2. Rule-Based Baseline
3. Logistic Regression
4. Decision Tree
5. Random Forest
6. Neural Network

The models were compared using:

- Accuracy
- Precision
- Recall
- Confusion matrix

Because the positive class represents only around 9% of observations, accuracy was not interpreted in isolation.

## Final model

The **Decision Tree** was selected because it provided the most suitable practical balance between precision and recall.

### Test-set performance

| Metric | Result |
|---|---:|
| Accuracy | 94.05% |
| Precision | 63.66% |
| Recall | 46.23% |

Confusion matrix:

- True negatives: 1,551,261
- False positives: 33,502
- False negatives: 68,282
- True positives: 58,700

The model detected approximately 46% of the stations that actually became empty. When it predicted that a station would become empty, approximately 64% of those predictions were correct.

## Error analysis

False positives generally occurred when stations had very few available bicycles and a stronger recent downward trend.

False negatives tended to occur when stations still appeared relatively safe at prediction time, with more available bicycles and a higher occupancy ratio, but later became empty.

## Main limitations

- A data gap exists around late September and early October 2018.
- Station coverage declined during the later months, especially in March.
- The dataset contains station-status snapshots rather than individual trip records.
- The model cannot distinguish user trips from redistribution or maintenance activity.
- Weather, holidays, events, and public transport disruptions are not included.

## Repository structure

```text
bicing-empty-station-prediction/
├── README.md
├── requirements.txt
├── notebooks/
│   └── bicing_prediction.ipynb
├── reports/
│   └── bicing_report.pdf
├── presentation/
│   └── bicing_presentation.pdf
└── images/
    ├── model_comparison.png
    ├── confusion_matrix.png
    └── prediction_map.png
```

## Installation

Create and activate a virtual environment, then install the dependencies:

```bash
pip install -r requirements.txt
```

## Running the notebook

1. Download the historical Bicing files from the official data source.
2. Place the CSV files in the folder expected by the notebook.
3. Update the data path in the loading section when necessary.
4. Open the notebook.
5. Restart the kernel and run all cells from top to bottom.

## Tools

- Python
- pandas
- NumPy
- scikit-learn
- Matplotlib
- Seaborn
- Folium

## Possible future improvements

- Add weather, holiday, event, and public transport data
- Test different decision thresholds
- Combine the Decision Tree with the rule-based baseline to increase recall
- Include information from nearby stations
- Evaluate the model on a more complete and stable historical period

## Author

Created as a final machine learning and neural networks course project.
