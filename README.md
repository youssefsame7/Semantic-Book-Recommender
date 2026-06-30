# Semantic Book Recommender

## Overview

This project is a semantic book recommendation app built with Gradio. It lets users type a natural-language description of the kind of book they want, choose a broad category and an emotional tone, then returns book cover recommendations that match both the meaning and mood of the request.

## What it does

- Reads a book metadata dataset from `books_with_emotions.csv`.
- Uses precomputed text data from `tagged_description.txt` to build a semantic search index.
- Converts a user query into embeddings using `sentence-transformers/all-MiniLM-L6-v2`.
- Searches for the most semantically similar book descriptions in a vector store.
- Filters results by category and ranks them by emotional tone when requested.
- Displays recommended book covers and short captions in a Gradio gallery.

## How it works

1. **Data loading**
   - `gradio-dashboard.py` loads `books_with_emotions.csv` into a Pandas DataFrame.
   - It also loads `tagged_description.txt` as raw documents where each record is tagged with an ISBN.

2. **Embedding and vector store**
   - The app uses `langchain_huggingface.HuggingFaceEmbeddings` with the `all-MiniLM-L6-v2` model.
   - Documents are split by newline and added to a Chroma vector store.

3. **Semantic similarity search**
   - When the user enters a query, the app performs a similarity search in the Chroma store.
   - It extracts ISBN values from the matching document contents and uses them to look up the corresponding rows in the CSV.

4. **Filtering and emotional ranking**
   - Users can restrict recommendations to a specific category.
   - They can also choose an emotional tone such as `Happy`, `Surprising`, `Angry`, `Suspenseful`, or `Sad`.
   - The app sorts candidate books by the matching emotion column (`joy`, `surprise`, `anger`, `fear`, or `sadness`).

5. **Presentation**
   - The app returns a gallery of book cover images and captions.
   - Captions include the title, author(s), and a short excerpt from the description.

## Development workflow

1. Data Processing & Cleaning
* **Source:** Book dataset sourced from Kaggle.
* **Exploration:** Handled missing values, formatted textual data, and optimized the dataset for natural language processing.

2. Semantic Vector Database (ChromaDB)
* Generated text embeddings using the open-source `sentence-transformers/all-MiniLM-L6-v2` model.
* Text chunking and heavy embedding generation were processed using a **Google Colab T4 GPU** to circumvent local hardware limitations and dependency conflicts.
* Persistent database files were serialized, downloaded, and integrated locally into the project directory for low-latency retrieval.

3. Zero-Shot Genre Classification
* The original dataset contained high amounts of unique subcategories. 
* Applied a **Zero-Shot Classification pipeline** to clean and standardize the `subtitle` data, accurately mapping books into two primary macro-categories: **Fiction** and **Non-Fiction**.

4. Sentiment Analysis Integration
* Utilized a Hugging Face pipeline to analyze the emotional tone and sentiment of book descriptions.
* Integrated sentiment insights to refine recommendations and elevate the overall user experience.

5. Interactive UI Deployment
* Deployed as a web application using **Gradio**, providing users with an intuitive interface to input semantic queries (e.g., *"A gripping mystery set in London"*) and instantly receive matching books.


## Usage

1. Enter a short description of the type of book you want.
2. Select a category or leave it set to `All`.
3. Select an emotional tone or leave it set to `All`.
4. Click `Find recommendations`.

