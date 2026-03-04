# LSTM Wind Forecasting — User Manual

This project provides LSTM-based time-series forecasting for wind U/V components and tools for exploring combined weather data. This manual explains how to set up, run, and interpret the results.

---

## Table of Contents

1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Project Structure](#project-structure)
5. [Data Format](#data-format)
6. [Using the LSTM Wind Model](#using-the-lstm-wind-model)
7. [Using the Combined Plots Notebook](#using-the-combined-plots-notebook)
8. [GPU Setup (Optional)](#gpu-setup-optional)
9. [Interpreting Results](#interpreting-results)
10. [Customization](#customization)

---

## Overview

- **LSTM Wind U/V forecasting**: Predicts the next U and V wind components (in knots) from the last 24 time steps using a 2-layer LSTM.
- **Combined weather exploration**: Visualizes time-series of multiple weather parameters (wind speed/direction, temperature, humidity, pressure, precipitation, etc.).

---

## Requirements

- Python 3.8+
- Jupyter (or JupyterLab) for running the notebooks
- Optional: NVIDIA GPU with CUDA for faster LSTM training

---

## Installation

1. **Clone or download** the project to a folder, e.g. `lstm-test`.

2. **Create a virtual environment** (recommended):

   ```bash
   python -m venv venv
   source venv/bin/activate   # Linux/macOS
   # or
   .\venv\Scripts\activate    # Windows
   ```

3. **Install dependencies**:

   ```bash
   pip install -r requirements-lstm.txt
   ```

   This installs: `torch`, `pandas`, `numpy`, `matplotlib`, `openpyxl`.

4. **Start Jupyter**:

   ```bash
   jupyter notebook
   ```

   Or use JupyterLab, VS Code, or Cursor with Jupyter support.

---

## Project Structure

| File | Purpose |
|------|---------|
| `lstm_wind.ipynb` | LSTM model for U/V wind forecasting (main notebook) |
| `combined_plots.ipynb` | Exploration and plotting of combined weather data |
| `wind_uv.csv` | Wind U/V data for LSTM (required for `lstm_wind.ipynb`) |
| `combined_test_clean_noheaders.csv` | Combined weather data for `combined_plots.ipynb` |
| `requirements-lstm.txt` | Python dependencies |
| `MANUAL.md` | This manual |

---

## Data Format

### wind_uv.csv (for LSTM)

Required columns:

| Column | Description |
|--------|-------------|
| `date (yyyy-MM-dd HH:mm:ss)` | Timestamp |
| `U (kt)` | U wind component in knots |
| `V (kt)` | V wind component in knots |

Example:

```
date (yyyy-MM-dd HH:mm:ss),U (kt),V (kt)
2022-09-01 12:12:47,-5.66,-7.25
2022-09-01 12:13:47,-5.79,-7.15
...
```

- Rows must be time-ordered.
- Missing or invalid values in U/V are dropped.
- Column names may contain "U", "kt" and "V", "kt"; the notebook auto-detects them.

### combined_test_clean_noheaders.csv (for combined_plots)

Combined weather dataset with columns for wind, temperature, humidity, pressure, solar radiation, and precipitation. See the notebook output for the exact column list and formats.

---

## Using the LSTM Wind Model

### Step 1: Prepare data

Place `wind_uv.csv` in the project root (same folder as `lstm_wind.ipynb`). Use the format above.

### Step 2: Run the notebook

1. Open `lstm_wind.ipynb` in Jupyter.
2. Run cells **in order** from top to bottom:
   - Cell 1: Imports and device check (CPU/GPU)
   - Cell 2: Load and parse `wind_uv.csv`
   - Cell 3: Normalize U/V and create sequences (sequence length 24)
   - Cell 4: Train/test split (80/20)
   - Cell 5: Define LSTM model
   - Cell 6: Training loop (50 epochs)
   - Cell 7: Evaluation (RMSE)
   - Cell 8: Plot U/V forecast vs actual

3. Wait for training to finish (a few minutes on GPU, longer on CPU).

### Step 3: Read outputs

- Training loss is printed every 10 epochs.
- Test RMSE for U and V (in knots) is printed at the end of evaluation.
- A plot compares predicted vs actual U and V on the test set.

---

## Using the Combined Plots Notebook

1. Open `combined_plots.ipynb`.
2. Ensure `combined_test_clean_noheaders.csv` is in the project root.
3. Run all cells in order.
4. The notebook loads the data and plots time-series for each parameter (wind, temperature, humidity, etc.), with short explanations for each.

---

## GPU Setup (Optional)

For faster LSTM training, use an NVIDIA GPU with CUDA.

1. Check whether CUDA is available:
   ```bash
   python -c "import torch; print(torch.cuda.is_available())"
   ```

2. If it prints `False`, install a CUDA-enabled PyTorch build, e.g. for CUDA 11.8:
   ```bash
   pip install torch --index-url https://download.pytorch.org/whl/cu118
   ```

3. Restart Jupyter and run `lstm_wind.ipynb` again. The first cell will report `Device: cuda` and the GPU name if correctly configured.

---

## Interpreting Results

### RMSE (Root Mean Square Error)

- Reported in knots (kt) for U and V.
- Lower RMSE means better predictions.
- Typical values depend on your data; in the example run, U ≈ 1.8 kt and V ≈ 1.9 kt.

### Forecast vs actual plot

- **U (kt)**: Upper subplot — U component predicted vs actual.
- **V (kt)**: Lower subplot — V component predicted vs actual.
- Closer alignment of lines indicates better performance.

---

## Customization

You can modify the following in `lstm_wind.ipynb`:

| Parameter | Location | Description |
|-----------|----------|-------------|
| `seq_len` | Cell 3 | Number of past steps (default: 24). |
| `split` (0.8) | Cell 4 | Train/test ratio (80% train, 20% test). |
| `batch_size` | Cell 4 | Batch size for training (default: 64). |
| `epochs` | Cell 6 | Number of training epochs (default: 50). |
| `hidden_size`, `num_layers`, `dropout` | Cell 5 | LSTM architecture. |

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `FileNotFoundError: wind_uv.csv` | Ensure `wind_uv.csv` is in the same folder as the notebook. |
| Slow training | Install PyTorch with CUDA (see [GPU Setup](#gpu-setup-optional)). Reduce `epochs` or `batch_size` for quick tests. |
| `ModuleNotFoundError` | Run `pip install -r requirements-lstm.txt`. |
| U/V column not found | Check that the CSV has columns whose names contain "U", "kt" and "V", "kt". |
| Plots not showing | If running headless, add `plt.savefig("forecast.png")` before `plt.show()`. |

---

## Summary

1. Install dependencies: `pip install -r requirements-lstm.txt`
2. Put `wind_uv.csv` in the project folder
3. Run `lstm_wind.ipynb` cells in order for LSTM training and evaluation
4. Run `combined_plots.ipynb` for weather data exploration
5. Use an NVIDIA GPU with CUDA for faster LSTM training
