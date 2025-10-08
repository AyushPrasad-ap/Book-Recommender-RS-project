# Book Recommender System

> Collaborative-filtering based book recommendation project (exploratory notebook + web app).

---

## Project Overview

This repository implements a simple **book recommendation system** based on collaborative filtering and precomputed similarity scores. It contains an exploratory Jupyter notebook to build the recommender, precomputed pickled artifacts used by a lightweight web app, and configuration files for deployment.

The goal is to let users search/select a book and receive similar book suggestions based on user-item interactions and computed similarity between items.

---

## Key Features

* Search for a book and get recommended books similar to it.
* Home page with a list of popular books (precomputed).
* Precomputed similarity matrix for fast recommendations (no heavy computation at runtime).
* Notebook (`book-recommender-system.ipynb`) that shows how the model and artifacts were built step-by-step.

---

## Repository Structure

```
book-recommender-system/
├─ .idea/                # IDE settings (optional)
├─ templates/            # HTML templates (if web app uses Flask)
├─ Procfile              # Heroku deployment config (if used)
├─ app.py                # Web application (serves the recommender UI/API)
├─ book-recommender-system.ipynb  # Notebook: data processing & model
├─ books.pkl             # Pickled dataframe/dict with book metadata
├─ popular.pkl           # Pickled data for popular books (home page)
├─ pt.pkl                # Pivot table (user x book) or lookup table
├─ similarity_scores.pkl # Precomputed similarity matrix (pickled)
├─ requirements.txt      # Python dependencies
```

> Note: The repo includes several pickled artifacts so the app can serve recommendations quickly without recomputing the similarity matrix on each run.

---

## How it Works (high level)

1. **Data preparation** (in the notebook): load book metadata and any user interaction / ratings data.
2. **Pivot table** creation: convert rating or interaction data into a user-by-book matrix (users as rows, books as columns) — saved as `pt.pkl`.
3. **Similarity computation**: compute item-item similarity (commonly via cosine similarity on item vectors derived from the pivot table) and save the matrix to `similarity_scores.pkl`.
4. **Web app**: `app.py` loads `books.pkl`, `popular.pkl`, `pt.pkl`, and `similarity_scores.pkl` on startup and exposes an interface/API to get recommendations quickly.

**Recommendation algorithm (typical approach used here):**

* Find the selected book's index in the pivot table.
* Load the corresponding row from `similarity_scores.pkl` and sort by similarity score.
* Return top N similar books (excluding the queried book itself) and enrich results using `books.pkl` for metadata (title, author, image link, etc.).

---

## Prerequisites

* Python 3.8+ recommended
* Git

---

## Installation & Run Locally

1. **Clone the repo**

```bash
git clone https://github.com/campusx-official/book-recommender-system.git
cd book-recommender-system
```

2. **Create & activate a virtual environment**

* macOS / Linux

```bash
python3 -m venv venv
source venv/bin/activate
```

* Windows (PowerShell)

```powershell
python -m venv venv
.\venv\Scripts\Activate
```

3. **Install dependencies**

```bash
pip install -r requirements.txt
```

4. **Run the web app**

> The repository contains `app.py` and a `Procfile` (deployment config). Depending on the framework used inside `app.py`, run either:

* If it's a Streamlit app:

```bash
streamlit run app.py
```

* If it's a Flask app (typical for Heroku deployments):

```bash
python app.py
# or
gunicorn app:app
```

Check the console output and open the local URL provided (for Streamlit it is usually `http://localhost:8501`; for Flask it is typically `http://127.0.0.1:5000`).

---

## Usage (Typical)

1. Open the app in your browser.
2. On the home page, you may see a list of popular books.
3. Search for a book title (or select from a dropdown).
4. The app returns a list of recommended books with metadata (title, author, cover image link, etc.).

---

## Rebuilding or Extending the Recommender

If you want to rebuild artifacts from scratch (instead of loading the provided pickles), use the notebook:

1. Open `book-recommender-system.ipynb`.
2. Re-run the preprocessing and pivot table creation cells.
3. Recompute similarity matrix, for example:

```python
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

# pt: pivot table (books as columns)
similarity_scores = cosine_similarity(pt.T.values)

# Save
import pickle
pickle.dump(similarity_scores, open('similarity_scores.pkl','wb'))
```

4. Update or recompute `popular.pkl` if you want a new list of trending/popular books.

---

## Notes & Tips

* **Pickles:** The project ships with `.pkl` files to speed up running the demo. If you change the dataset or schema, you'll need to recompute and overwrite these pickles.
* **Scalability:** Precomputing item-item similarity works for small-to-midsize catalogs. For very large catalogs (100k+ items) consider approximate nearest neighbours solutions such as Annoy, Faiss, or building an embedding-based recommender.
* **Cold-start:** This approach is item-based collaborative filtering. It requires user interactions to compute meaningful similarities. For new items with no interactions, consider content-based features (title/description embeddings) to bootstrap recommendations.

---

## Reproducibility

* All the reproducible steps are available inside `book-recommender-system.ipynb`. Re-run the notebook to reproduce pickles if you have the original CSV/dataset.

---

## Contact

If you have any interesting suggestions... Feel free to reach out!
