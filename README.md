# Movie Recommender

Semantic movie recommender for Russian-language reviews. Describe the kind of film you want in your own words, and the app finds movies whose reviews are closest in meaning, with optional keywords to exclude.

Desktop app (Tkinter) built on the [Kinopoisk reviews dataset](https://huggingface.co/datasets/blinoff/kinopoisk).

## How it works

```
Kinopoisk reviews (Hugging Face datasets)
        │
        ▼
Sentence Transformers (distiluse-base-multilingual-cased) ──▶ review embeddings, cached to embeddings.npy
        │
User query ──▶ query embedding ──▶ k-NN, cosine distance (top 50 reviews)
        │
        ▼
spaCy lemmatization ──▶ drop reviews that mention excluded words (any word form)
        │
        ▼
N recommendations: movie, rating, review text
```

1. **Embeddings** — every review is encoded with the multilingual `distiluse-base-multilingual-cased` model. Embeddings are computed once and cached in `embeddings.npy`; the cache is rebuilt automatically if its size no longer matches the dataset.
2. **Search** — scikit-learn `NearestNeighbors` (cosine metric) returns the 50 reviews closest to the query.
3. **Exclusions** — query keywords and reviews are lemmatized with spaCy (`ru_core_news_sm`), so excluding «ужас» also removes reviews containing «ужасы» or «ужасов».
4. **UI** — Tkinter window: query, number of recommendations, words to exclude; clicking a result shows the full review.

## Stack

Python · Sentence Transformers · scikit-learn · spaCy · Hugging Face `datasets` · pandas · NumPy · Tkinter

## Run locally

Python 3.10+

```bash
pip install -r requirements.txt
python -m spacy download ru_core_news_sm
python app.py
```

The first run downloads the dataset and the model and computes embeddings for all reviews, which takes a while. Later runs load the cached `embeddings.npy`. The cache is not stored in the repository (about 75 MB).

## Limitations and next steps

- Results are reviews, so the same movie can appear more than once: group by movie and average the similarity.
- k-NN is brute force: an approximate index (FAISS) would scale to larger datasets.
- Add a small evaluation set of queries to compare models instead of judging results by eye.

---

## По-русски

Семантический рекомендатель фильмов по рецензиям Кинопоиска: описываете, какой фильм хотите, своими словами, а приложение находит фильмы с наиболее близкими по смыслу рецензиями.

- эмбеддинги рецензий — Sentence Transformers (`distiluse-base-multilingual-cased`), кэшируются в `embeddings.npy`;
- поиск — k ближайших соседей по косинусному расстоянию (scikit-learn);
- исключение нежелательных слов с учётом словоформ — лемматизация spaCy;
- интерфейс — Tkinter.

Запуск — см. раздел **Run locally** выше.
