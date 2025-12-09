# AutoInsight Services

A microservices-based data analysis platform that provides automated insights for datasets with numerical and categorical data. Built with FastAPI and powered by machine learning libraries.

## Overview

AutoInsight Services consists of two independent microservices:

- **Categorical Service** - Analyzes datasets with categorical variables
- **Numerical Service** - Analyzes datasets with numerical variables

Each service accepts CSV or XLSX files, performs automated analysis, generates visualizations, and provides AI-generated summaries.

## Features

### Categorical Service
- Automatic detection of categorical columns
- Value distribution analysis
- Missing value detection
- Bar chart visualizations
- AI-generated insights about category distributions
- Identifies majority categories and rare occurrences

### Numerical Service
- Automatic detection of numerical columns
- Statistical summary (mean, median, std, quartiles)
- Correlation matrix analysis
- Outlier detection using IQR method
- Multiple visualizations:
  - Correlation heatmap
  - Distribution histograms with KDE
  - Box plots
- AI-generated insights about data characteristics

## Project Structure

```
Services/
├── services/
│   ├── categorical/
│   │   ├── main.py
│   │   ├── requirements.txt
│   │   └── runtime.txt
│   └── numerical/
│       ├── main.py
│       ├── requirements.txt
│       └── runtime.txt
└── README.md
```

## Requirements

- Python >= 3.9
- Dependencies (see `requirements.txt` in each service):
  - pandas, numpy, scipy, scikit-learn
  - matplotlib, seaborn
  - fastapi, uvicorn
  - transformers, torch, accelerate
  - openpyxl

## Installation

### 1. Clone the repository
```bash
cd Services
```

### 2. Install dependencies for each service

**Categorical Service:**
```bash
cd services/categorical
pip install -r requirements.txt
```

**Numerical Service:**
```bash
cd services/numerical
pip install -r requirements.txt
```

## Usage

### Running the Services

**Categorical Service (Port 8002):**
```bash
cd services/categorical
python main.py
```

Or with uvicorn:
```bash
python3 -m uvicorn main:app --reload --port 8002
```

**Numerical Service (Port 8001):**
```bash
cd services/numerical
python main.py
```

Or with uvicorn:
```bash
python3 -m uvicorn main:app --reload --port 8001
```

### Environment Variables

- `PORT` - Custom port number (defaults: 8001 for numerical, 8002 for categorical)
- `ALLOWED_ORIGINS` - CORS allowed origins (default: "*", comma-separated for multiple)

Example:
```bash
PORT=8003 ALLOWED_ORIGINS="http://localhost:3000,https://example.com" python main.py
```

## API Endpoints

### Categorical Service (http://localhost:8002)

#### `GET /`
Web interface for file upload

#### `POST /analyze`
Analyze categorical data
- **Content-Type:** multipart/form-data
- **Parameter:** file (CSV or XLSX)
- **Response:**
  ```json
  {
    "dataset_preview": [...],
    "column_types": {"categorical": [...]},
    "analysis": {
      "value_counts": {...},
      "missing_values": {...}
    },
    "plots": {...},
    "summary": "..."
  }
  ```

#### `GET /health`
Health check endpoint

### Numerical Service (http://localhost:8001)

#### `GET /`
Web interface for file upload

#### `POST /analyzes`
Analyze numerical data
- **Content-Type:** multipart/form-data
- **Parameter:** file (CSV or XLSX)
- **Response:**
  ```json
  {
    "dataset_preview": [...],
    "column_types": {"numerical": [...]},
    "analysis": {
      "summary_stats": {...},
      "missing_values": {...},
      "correlation_matrix": {...},
      "outliers": {...}
    },
    "plots": {...},
    "summary": "..."
  }
  ```

#### `GET /health`
Health check endpoint

## Example Usage

### Using cURL

**Categorical Analysis:**
```bash
curl -X POST "http://localhost:8002/analyze" \
  -H "accept: application/json" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@dataset.csv"
```

**Numerical Analysis:**
```bash
curl -X POST "http://localhost:8001/analyzes" \
  -H "accept: application/json" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@dataset.xlsx"
```

### Using Python

```python
import requests

# Categorical analysis
with open('data.csv', 'rb') as f:
    response = requests.post(
        'http://localhost:8002/analyze',
        files={'file': f}
    )
    result = response.json()
    print(result['summary'])

# Numerical analysis
with open('data.xlsx', 'rb') as f:
    response = requests.post(
        'http://localhost:8001/analyzes',
        files={'file': f}
    )
    result = response.json()
    print(result['summary'])
```

## Features in Detail

### Visualization Outputs
All plots are returned as base64-encoded PNG images in the response:
- **Categorical**: Bar charts showing value distributions
- **Numerical**: Heatmaps, histograms, box plots, and outlier charts

### Column Detection
- **Categorical**: Detects object types or columns with < 20 unique values
- **Numerical**: Detects int64, float64, or numeric string types

### Summary Generation
Each service generates human-readable summaries describing:
- Dataset size and structure
- Key statistics and patterns
- Data quality issues (missing values, outliers)
- Recommendations for further analysis

## CORS Configuration

Both services support CORS and can be configured for specific origins:

```bash
# Allow all origins (default)
ALLOWED_ORIGINS="*"

# Allow specific origins
ALLOWED_ORIGINS="http://localhost:3000,https://myapp.com"
```

## Development

### Running in Development Mode
Both services support auto-reload for development:

```bash
python3 -m uvicorn main:app --reload --port 8001
```

## License

This project is provided as-is for data analysis purposes.

## Support

For issues or questions, please check the API documentation at:
- Categorical: http://localhost:8002/docs
- Numerical: http://localhost:8001/docs
