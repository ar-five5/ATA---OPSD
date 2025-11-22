# OPSD PowerDesk Time Series Forecasting Project

## Project Structure

```
ATA/
├── src/                          # Source code
│   ├── phase3_model_building.py           # Phase 1-2: STL, ACF/PACF, SARIMA order selection
│   ├── phase3b_lstm_model_building.py     # Phase 2b: LSTM model training
│   ├── phase4_forecasting_backtesting.py  # Phase 3: SARIMA forecasting
│   ├── phase4b_lstm_forecasting_backtesting.py  # Phase 3b: LSTM forecasting
│   ├── phase5_anomaly_detection.py        # Phase 4: Z-score & CUSUM anomaly detection
│   └── phase5b_ml_anomaly_classifier.py   # Phase 4b: ML-based anomaly classifier
│
├── data/                         # Input datasets
│   └── time_series_60min_singleindex.csv  # OPSD hourly load data
│
├── results/                      # All outputs organized by phase
│   ├── phase2_results/          # STL decomposition, ACF/PACF plots (11 files)
│   ├── phase3_results/          # SARIMA model selection (5 files)
│   ├── phase3b_lstm_results/    # LSTM training history (4 files)
│   ├── phase4_results/          # SARIMA forecasts & metrics (9 files)
│   ├── phase4b_lstm_results/    # LSTM forecasts & comparison (9 files)
│   ├── outputs/                 # Anomaly detection results (10 files)
│   └── models/                  # Trained LSTM models (3 .pt files)
│
├── docs/                         # Documentation
│   └── OPSD_PowerDesk_Assignment_v2.pdf   # Assignment specifications
│
├── .venv/                        # Python virtual environment
└── README.md                     # This file
```

## Phases Completed

### ✅ Phase 1: Data Preparation & STL Decomposition
- Sanity check visualization (14 days)
- STL decomposition for AT, BE, BG
- ACF/PACF analysis
- Stationarity testing (ADF, KPSS)
- **Output:** 11 files in `results/phase2_results/`

### ✅ Phase 2: SARIMA Model Selection
- Grid search for optimal (p,d,q)(P,D,Q,s) parameters
- AIC-based model selection
- **Output:** 5 files in `results/phase3_results/`
- **Selected Orders:**
  - AT: SARIMA(2,0,2)(1,1,1,24)
  - BE: SARIMA(2,0,1)(1,1,1,24)
  - BG: SARIMA(3,0,1)(1,1,1,24)

### ✅ Phase 2b: LSTM Model Building
- Sequence-to-sequence LSTM with 168h lookback
- Trained models saved for all countries
- **Output:** 7 files in `results/phase3b_lstm_results/` + `results/models/`

### ✅ Phase 3: SARIMA Forecasting & Backtesting
- 12-day rolling forecast (Sept 19-30, 2020)
- Evaluation metrics: MASE, sMAPE, MAPE, RMSE, MSE, PI Coverage
- **Output:** 9 files in `results/phase4_results/`
- **Performance:**
  - AT: MAPE 7.14%, MASE 0.96
  - BE: MAPE 5.53%, MASE 0.96
  - BG: MAPE 3.31%, MASE 0.95

### ✅ Phase 3b: LSTM Forecasting & Comparison
- Multi-day LSTM forecasting
- SARIMA vs LSTM comparison
- **Output:** 9 files in `results/phase4b_lstm_results/`
- **Performance:**
  - AT: MAPE 5.24%, MASE 0.67 (27% better than SARIMA)
  - BE: MAPE 3.67%, MASE 0.63 (34% better than SARIMA)
  - BG: MAPE 4.50%, MASE 1.25 (worse than SARIMA)

### ✅ Phase 4: Anomaly Detection
- Rolling z-score anomaly detection (336h window, |z|≥3.0)
- CUSUM drift detection (k=0.5, h=5.0)
- 1000 hours analyzed (976 valid points after warm-up)
- **Output:** 7 files in `results/outputs/`
- **Results:**
  - 21 total z-score anomalies (2.15% rate)
  - AT: 7 anomalies (0.72%), BE: 2 (0.20%), BG: 12 (1.23%)

### ✅ Phase 4b: ML-Based Anomaly Classifier
- Silver label generation (601 labeled samples)
- Logistic Regression + LightGBM classifiers
- 27 engineered features
- **Output:** 3 files in `results/outputs/`
- **Performance:**
  - PR-AUC: 1.0 (perfect separation)
  - F1 @ Precision≥0.80: 1.0

## Remaining Work

### ❌ Phase 5: Live Ingestion & Online Adaptation
- Simulate ≥2,000 hours streaming data
- Drift detection
- Model adaptation (Rolling SARIMA refit or Neural fine-tuning)

### ❌ Phase 6: Dashboard
- Streamlit interactive dashboard
- Real-time anomaly alerts
- Performance metrics visualization

## How to Run

```powershell
# Activate virtual environment
.venv\Scripts\Activate.ps1

# Run phases in order
python src/phase3_model_building.py
python src/phase3b_lstm_model_building.py
python src/phase4_forecasting_backtesting.py
python src/phase4b_lstm_forecasting_backtesting.py
python src/phase5_anomaly_detection.py
python src/phase5b_ml_anomaly_classifier.py
```

## Countries Analyzed
- **AT**: Austria
- **BE**: Belgium
- **BG**: Bulgaria

## Key Findings
1. **SARIMA performs well**: 3-7% MAPE across all countries
2. **LSTM excels for AT/BE**: 27-34% improvement in MASE
3. **BG is most predictable**: Lowest MAPE (3.31%) with SARIMA
4. **Minimal anomalies**: 2.15% anomaly rate indicates robust forecasting
5. **Perfect anomaly detection**: ML classifier achieves 100% PR-AUC
