# Demand Forecasting with Uncertainty Quantification

Forecasts daily product demand with prediction intervals (10th/50th/90th percentile) using LightGBM quantile regression, then uses the intervals for an inventory decision.

## Dataset
Store Item Demand Forecasting Challenge (Kaggle): https://www.kaggle.com/c/demand-forecasting-kernels-only
Download `train.csv` and place it in the same folder as the notebook. The data is not included in this repo.

## Setup
pip install -r requirements.txt

## How to reproduce
1. Open the notebook in Jupyter or Google Colab.
2. Make sure `train.csv` is in the same folder (in Colab, upload it).
3. Run all cells from top to bottom.

## Re-running the evaluation
The evaluation cells train on data before 2017-10-01 and test on 2017-10-01 to 2017-12-31. Re-running all cells recomputes MAE, RMSE, interval coverage, the inventory cost comparison and the overconfidence analysis.

## Approach
- Features: calendar (day of week, month, day of year, week of year, weekend), US federal holidays, lags (7, 14, 28, 365 days) and rolling mean/std (7 and 28 days). Lags and rolling stats use a 7-day gap so the model never sees the future.
- Model: three LightGBM models with quantile loss (alpha 0.1, 0.5, 0.9).
- Time-based train/test split (no shuffling).

## Results
- MAE 5.95, RMSE 7.71 (median forecast)
- 10-90 interval coverage: 78.1% (nominal 80%)
- Inventory (assumed costs: stockout 5 per unit, overstock 1 per unit): stocking at p90 costs about 557K vs about 801K at p50 (about 30% lower), with stockouts on 8.7% of days vs 44.5%.
- Overconfidence: coverage is 80.2% on normal days but only 41.1% on demand spikes (sales more than 1.3x the 28-day average).

## Limitations
- The dataset has no promotion data, so holidays and seasonality are the only event features.
- Cost figures are assumptions and should be replaced with real business costs.
- Spikes are defined using actual sales, so some misses are unavoidable.
