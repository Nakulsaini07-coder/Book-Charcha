# End-to-End Book Recommendation System

An end-to-end, collaborative-filtering based **Book Recommender System** built with:

- **Python + scikit-learn** for model training
- **Pandas/Numpy** for data preparation
- **Streamlit** for the interactive web app
- **Modular ML pipeline** (ingestion → validation → transformation → training)

The project trains a nearest-neighbor recommendation model from book ratings data and serves top similar books with cover images in a Streamlit UI.

---

## 🎥 Demo

![Book Charcha Demo](media\ScreenRecording2026-02-26105404-ezgif.com-video-to-gif-converter.gif)

---

## Features

- End-to-end training pipeline with clear stages
- Automated data download and extraction
- Data cleaning and filtering logic for quality recommendations
- Pivot-table creation for user-item matrix
- KNN model training using sparse matrix representation
- Streamlit app for training trigger + recommendation display
- Config-driven paths and pipeline behavior through `config/config.yaml`
- Artifact-based workflow (clean data, transformed data, serialized objects, trained model)

---

## Project Workflow

1. **Data Ingestion**
   - Downloads dataset zip from configured URL
   - Extracts into `artifacts/dataset/ingested_data`

2. **Data Validation / Preprocessing**
   - Loads ratings and books CSV files
   - Renames columns and keeps required fields
   - Filters users with >200 ratings
   - Filters books with at least 50 ratings
   - Removes duplicate `(user_id, title)` rows
   - Saves:
     - `clean_data.csv`
     - `final_rating.pkl`

3. **Data Transformation**
   - Creates user-item pivot table (`title` × `user_id`)
   - Fills missing values with `0`
   - Saves:
     - `transformed_data.pkl`
     - `book_names.pkl`
     - `book_pivot.pkl`

4. **Model Training**
   - Converts pivot table to sparse matrix (`csr_matrix`)
   - Trains `NearestNeighbors(algorithm='brute')`
   - Saves model to `artifacts/trained_model/model.pkl`

5. **Recommendation Serving (Streamlit)**
   - Loads trained model + serialized objects
   - Finds nearest books for selected title
   - Displays top recommendations and book poster URLs

---

## Repository Structure

```text
.
├── app.py                              # Streamlit web app
├── main.py                             # Pipeline run entry script
├── Dockerfile                          # Containerized Streamlit app
├── requirements.txt
├── setup.py
├── config/
│   └── config.yaml                     # Central configuration
├── books_recommender/
│   ├── components/
│   │   ├── stage_00_data_ingestion.py
│   │   ├── stage_01_data_validation.py
│   │   ├── stage_02_data_transformation.py
│   │   └── stage_03_model_trainer.py
│   ├── config/
│   │   └── configuration.py
│   ├── entity/
│   │   └── config_entity.py
│   ├── exception/
│   │   └── exception_handler.py
│   ├── logger/
│   │   └── log.py
│   ├── pipeline/
│   │   └── training_pipeline.py
│   └── utils/
│       └── util.py
├── artifacts/                          # Generated data/models/objects
├── logs/                               # Runtime log files
├── templates/                          # Serialized assets used in UI
└── media/                              # Demo video/media assets
```

---

## Tech Stack

- Python 3.7+
- scikit-learn
- pandas
- numpy
- PyYAML
- streamlit

---

## Installation and Setup (Local)

### 1) Clone repository

```bash
git clone <your-repository-url>
cd <your-project-folder>
```

### 2) Create and activate environment

#### Conda (recommended)

```bash
conda create -n books python=3.7.10 -y
conda activate books
```

#### venv (alternative)

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
```

Linux/macOS:

```bash
source .venv/bin/activate
```

### 3) Install dependencies

```bash
pip install -r requirements.txt
```

---

## How to Run

### Option A: Run complete training pipeline from script

```bash
python main.py
```

This executes all stages:
`DataIngestion -> DataValidation -> DataTransformation -> ModelTrainer`

### Option B: Run Streamlit app

```bash
streamlit run app.py
```

In the app:

- Click **Train Recommender System** (if artifacts/model are not present or need refresh)
- Select a book from dropdown
- Click **Show Recommendation**

Default app URL:

- `http://localhost:8501`

---

## Configuration

Main configuration file: `config/config.yaml`

Important keys:

- `artifacts_config.artifacts_dir`
- `data_ingestion_config.dataset_download_url`
- `data_ingestion_config.ingested_dir`
- `data_validation_config.clean_data_dir`
- `data_validation_config.serialized_objects_dir`
- `data_transformation_config.transformed_data_dir`
- `model_trainer_config.trained_model_dir`
- `model_trainer_config.trained_model_name`

You can customize directories and data source by editing this YAML.

---

## Generated Artifacts

After a successful training run, typical generated files are:

- `artifacts/dataset/raw_data/*.zip`
- `artifacts/dataset/ingested_data/BX-Books.csv`
- `artifacts/dataset/ingested_data/BX-Book-Ratings.csv`
- `artifacts/dataset/clean_data/clean_data.csv`
- `artifacts/dataset/transformed_data/transformed_data.pkl`
- `artifacts/serialized_objects/book_names.pkl`
- `artifacts/serialized_objects/book_pivot.pkl`
- `artifacts/serialized_objects/final_rating.pkl`
- `artifacts/trained_model/model.pkl`

---

## Docker Usage

### Build image

```bash
docker build -t book-recommender:latest .
```

### Run container

```bash
docker run -d -p 8501:8501 --name book-recommender-app book-recommender:latest
```

Open:

- `http://localhost:8501`

### Useful Docker commands

```bash
docker ps
docker logs -f book-recommender-app
docker stop book-recommender-app
docker rm book-recommender-app
```

---

## Logging and Error Handling

- Logs are written to timestamped files in `logs/`
- Custom exception class `AppException` captures script name + line number + error message
- Each pipeline stage logs start and completion events

---

## Troubleshooting

- **`ModuleNotFoundError`**: Ensure your virtual environment is active and dependencies are installed.
- **Artifacts missing / recommendation fails**: Run `python main.py` or use app training button to regenerate artifacts.
- **Port already in use (8501)**: Stop conflicting process or run streamlit on a different port.
- **Slow first run**: Initial dataset download and preprocessing can take time.

---

## Future Improvements

- Add model evaluation metrics/reporting
- Add hybrid recommendation (content + collaborative)
- Add CI checks for pipeline execution
- Add tests for each pipeline stage

---

## Author

**Boktiar Ahmed Bappy**

If you use this project, feel free to fork and improve it for your own recommendation workflows.
