# ENGR 5785G Real-Time Stream Processing Assignment

## Scenario Chosen
Scenario B: Hospital Patient Monitoring

## Dataset
I used the IoMT patient dataset `patients_data_with_alerts.xlsx` from Kaggle. The dataset contains patient health measurements including `Heart Rate (bpm)`.

## Streaming Simulation
Since the dataset is a static Excel file, I converted the cleaned rows into small CSV batch files. These batch files are copied one by one into a watched directory. Spark reads this directory using `readStream`, which simulates a real-time stream.

The dataset does not include a true timestamp column, so event timestamps are simulated at one-second intervals. The heart-rate values are the actual values from the Kaggle dataset.

## Window Type
I used a 2-minute tumbling window. This is appropriate because the goal is to detect sustained abnormal heart rate, not a single spike. Each event belongs to one fixed 2-minute window, which makes it easy to compare consecutive windows for the same patient.

## State Requirement
The pipeline requires state in two places. First, Spark keeps aggregation state for each patient and each 2-minute event-time window to compute average heart rate while using `withWatermark` to handle late data. Second, the alert function keeps the previous high-heart-rate window for each patient so it can detect whether the patient exceeds 100 bpm in two consecutive windows.

## Alert Condition
The alert condition is:

Average heart rate > 100 bpm in two consecutive 2-minute windows.

## Alert Output Example
`CLINICAL ALERT: Sustained elevated HR across 2 consecutive windows | patient_id=P007 | avg_hr=101.5 bpm | window_start=2026-01-01 10:10:00 | window_end=2026-01-01 10:12:00`

## How to Run
1. Open the notebook in Kaggle.
2. Add the dataset containing `patients_data_with_alerts.xlsx`.
3. Run all cells from top to bottom.
