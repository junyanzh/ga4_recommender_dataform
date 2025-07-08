# GA4-Recommender (Dataform + BQML) 📦

A minimal, end-to-end recommender-system pipeline that:  
- Ingests **Google Analytics 4 public ecommerce** events  
- Orchestrates transformations in **Dataform Core 3.x**  
- Trains a **BigQuery ML Logistic-Regression** ranking model  
- Writes **Top-N recommendations** & **Precision@K** back to BigQuery  

Everything is pure SQL (SQLX); no external Spark or Airflow needed.

---

## Repository Layout
.
├── workflow_settings.yaml      # Dataform project config (Core v3)
└── definitions/                # All SQLX actions
    ├── staging/
    │   └── user_item_interactions_raw.sqlx
    ├── features/
    │   ├── user_features.sqlx
    │   └── item_features.sqlx
    └── ml_pipeline/
        ├── training_dataset.sqlx
        ├── train_user_item_logreg_model.sqlx
        ├── model_evaluation.sqlx
        ├── predicted_scores.sqlx
        ├── topn_recommendations.sqlx
        └── precision_at_k.sqlx


---

## Prerequisites

- **GCP project** with BigQuery enabled (on-demand billing is fine).  
- Dataform Web UI (BigQuery console → “Dataform” in the left nav).  
- BigQuery service-account or user credentials with roles:  
  `BigQuery Data Editor`, `BigQuery Job User`, `BigQuery User`.

---

## Quick Start (5 minutes)

1. **Clone** the repo & open Dataform Web.  
2. **Create workspace → Upload** the repo files (or connect via Git).  
3. Edit `workflow_settings.yaml`:
   ```yaml
   defaultProject:  <your-gcp-project>
   defaultDataset:  ga4_recommender
   defaultLocation: US        # change if GA4 sample is mirrored elsewhere
4. Click **Refresh** → verify **0 errors**.  
5. Hit **Run → Execute**:  
   - Choose a service-account or personal creds  
   - Leave all nine actions selected  
   - Click **OK**  
6. Wait ~5 minutes. You’ll see:  
   - Tables ✅ in `ga4_recommender.*`  
   - Model ✅ `ga4_recommender.train_user_item_logreg_model`  
   - Metrics ✅ `model_evaluation` & `precision_at_k`  

---

## Why Logistic Regression?

Matrix-factorization requires **reserved slots** in BigQuery; on-demand jobs often exceed CPU-hour limits.  
Logistic-regression ranking trains in minutes, leverages BQML’s auto one-hot & standardisation, and makes a solid baseline for implicit-feedback data.

---

## Next Steps

- Add a **candidate-generation** stage (ANN recall, popularity by segment).  
- Schedule daily runs or trigger on GA4 export arrival.  
- Serve predictions via Cloud Run + Cloud Functions API.

---

## License

MIT — free to copy, adapt, and deploy. Pull requests welcome!

