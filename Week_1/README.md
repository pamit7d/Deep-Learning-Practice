# Deep-Learning-Practice
# Deep Learning Practice – Dataset Fundamentals with Hugging Face 🤗

This repository contains my hands-on practice notebooks from the Deep Learning course, focused on learning the Hugging Face `datasets` library.

The notebooks are organized progressively, starting from dataset basics and moving toward dataset transformations, streaming, filtering, mapping, concatenation, and interleaving.

---

# Repository Structure

```text
.
├── DlpL3_dataset.ipynb
├── DlpL4_dataset.ipynb
├── DlpL5_commonMethods.ipynb
├── README.md
└── .gitignore
```

---

# Learning Path

Follow the notebooks in the given order.

---

# Notebook 1: DlpL3_dataset.ipynb

## Topics Covered

### 1. Introduction to Hugging Face

* What is Hugging Face?
* Why Hugging Face is widely used in NLP and Deep Learning
* Understanding the `datasets` library

### 2. Loading Datasets

Learn how to load datasets from Hugging Face Hub.

Example:

```python
from datasets import load_dataset

imdb = load_dataset("imdb")
```

### 3. DatasetDict vs Dataset

Understanding:

```python
DatasetDict
```

containing:

```python
train
test
unsupervised
```

and how each split is represented.

### 4. Accessing Splits

```python
imdb["train"]
imdb["test"]
```

### 5. Dataset Features

Understanding dataset schema:

```python
dataset.features
```

### 6. Dataset Metadata

Learning:

```python
dataset.column_names
len(dataset)
```

### 7. Removing Splits

Example:

```python
imdb.pop("unsupervised")
```

### 8. Downloading Specific Splits

Example:

```python
load_dataset(
    "imdb",
    split="train"
)
```

---

# Notebook 2: DlpL4_dataset.ipynb

## Topics Covered

### 1. Accessing Samples

Getting individual samples:

```python
dataset[0]
```

### 2. Random Samples

Exploring datasets randomly.

### 3. Using select()

Selecting subsets:

```python
dataset.select(range(100))
```

### 4. Translation Datasets

Working with:

```python
wmt/wmt14
```

Configuration:

```python
hi-en
```

### 5. Dataset Configurations

Understanding:

```python
get_dataset_config_names()
```

### 6. Dataset Splits

Understanding:

```python
get_dataset_split_names()
```

### 7. Dataset Information

Using:

```python
get_dataset_config_info()
```

### 8. Machine Translation Datasets

Learning the structure:

```python
{
    "translation": {
        "hi": "...",
        "en": "..."
    }
}
```

### 9. Understanding Features

Example:

```python
{
    "translation":
    Translation(
        languages=["hi", "en"]
    )
}
```

### 10. GLUE Benchmark

Learning:

* What GLUE is
* Why GLUE is important
* Difference between GLUE and WMT14

Tasks explored:

* MRPC
* Sentence similarity
* Text classification

---

# Notebook 3: DlpL5_commonMethods.ipynb

## Topics Covered

### 1. Dataset Filtering

Removing unwanted samples.

Example:

```python
dataset.filter(...)
```

Use cases:

* Remove short texts
* Remove noisy samples
* Clean datasets

---

### 2. Dataset Mapping

Transform every sample.

Example:

```python
dataset.map(...)
```

Use cases:

* Add prefixes
* Lowercase text
* Tokenization
* Feature engineering

---

### 3. Concatenating Datasets

Combining datasets:

```python
concatenate_datasets(...)
```

Example:

* IMDb
* Rotten Tomatoes

---

### 4. Interleaving Datasets

Balancing datasets with different sizes.

Example:

```python
interleave_datasets(...)
```

Probabilities:

```python
[0.6, 0.4]
```

Meaning:

```text
60% IMDb
40% Rotten Tomatoes
```

---

### 5. Streaming Datasets

Loading large datasets efficiently.

Example:

```python
load_dataset(
    "imdb",
    streaming=True
)
```

Benefits:

* Lower memory usage
* Faster startup
* Suitable for huge datasets

---

### 6. Lazy Evaluation

Understanding why:

```python
dataset.map(...)
```

on streaming datasets does not process everything immediately.

Processing happens only when samples are requested.

---

### 7. Loading Datasets from External Sources

Learning how to load:

* CSV files
* JSON files
* ZIP files
* URLs

Example:

```python
load_dataset(
    "csv",
    data_files="file.csv"
)
```

---

# Datasets Explored

## IMDb

Task:

```text
Sentiment Analysis
```

Labels:

```text
0 → Negative
1 → Positive
```

---

## Rotten Tomatoes

Task:

```text
Sentiment Analysis
```

Labels:

```text
0 → Negative
1 → Positive
```

---

## WMT14

Task:

```text
Machine Translation
```

Languages:

```text
Hindi ↔ English
```

---

## GLUE

Task:

```text
Language Understanding Benchmark
```

Includes:

* MRPC
* SST-2
* CoLA
* QQP
* RTE
* STS-B

---

# Key Learnings

After completing these notebooks, you should be able to:

✅ Load datasets from Hugging Face Hub

✅ Understand Dataset and DatasetDict

✅ Explore dataset features and schema

✅ Access and inspect samples

✅ Work with translation datasets

✅ Understand the GLUE benchmark

✅ Filter datasets

✅ Transform datasets using map()

✅ Concatenate datasets

✅ Interleave datasets

✅ Stream large datasets

✅ Load datasets from external files and URLs

---

# Requirements

```bash
pip install datasets
pip install transformers
pip install huggingface_hub
```

---

# Author

**Amit Kumar Pandey**

B.S. Data Science, IIT Madras

Documenting my Deep Learning learning journey one notebook at a time 🚀
