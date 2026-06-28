# 📘 Deep Learning Practice (DLP) — Notes & Assignments

> My personal revision notes for the **Deep Learning Practice** course at **IIT Madras B.S. Degree Programme**.  
> I study each topic, practice the code, and push notes weekly. Feel free to use these for your own revision!

---

## 📂 What's in this repo?

| Type                        | Format            | How to use                         |
| --------------------------- | ----------------- | ---------------------------------- |
| 📝 Markdown Notes            | `.md` files       | Read directly on GitHub            |
| 🌐 Rich HTML Notes           | `.html` files     | Download and open in any browser   |
| 📓 Practice Notebooks        | `.ipynb` files    | Run in Jupyter / Google Colab      |
| 📋 Graded Assignment Answers | `Ga*.ipynb` files | Detailed answers with explanations |

---

## 🗂️ Folder Structure

```
DLP/
├── README.md
├── Week_1/
│   ├── DlpL3_dataset.ipynb
│   ├── DlpL4_dataset.ipynb
│   ├── DlpL5_commonMethods.ipynb
│   ├── Ga1.ipynb
│   ├── pyarrow_dataset/
│   ├── test.csv
│   └── train.csv
│
└── Week_2/
    ├── L1_tokenizer.md
    ├── L2_BPE_tokenizer.md
    ├── L3_wordspiece_tokenizer.md
    ├── L4_sentence_tokenizer.html
    ├── L4_Viterbi.md
    ├── L5_HF_tokenizer.html
    ├── L6_DLP_HF_tokenizer.ipynb
    ├── L7_Bert_tokenizer.html
    ├── L8_pretrained_tokenizer.html
    ├── bpe.md
    ├── Ga2.ipynb
    ├── hopper.json
    └── model/
```

---

## 📖 Week 1 — HuggingFace Datasets

**Topics covered:**
- Intro to the course and recap of Neural Network Models
- Why HuggingFace? and roadmap to the course
- HF `datasets` module — loading datasets (L3)
- Accessing samples from a dataset (L4)
- Common methods on HF datasets (L5)

| Resource                   | Link                                                            |
| -------------------------- | --------------------------------------------------------------- |
| HF Datasets Practice       | [DlpL3_dataset.ipynb](./Week_1/DlpL3_dataset.ipynb)             |
| Accessing Samples Practice | [DlpL4_dataset.ipynb](./Week_1/DlpL4_dataset.ipynb)             |
| Common Methods Practice    | [DlpL5_commonMethods.ipynb](./Week_1/DlpL5_commonMethods.ipynb) |
| Graded Assignment 1 ✅      | [Ga1.ipynb](./Week_1/Ga1.ipynb)                                 |

---

## 📖 Week 2 — Tokenization

**Topics covered:**
- Introduction to Tokenization
- Byte Pair Encoding (BPE)
- WordPiece Tokenizer
- Sentence-Piece Tokenizer + Viterbi Algorithm
- HuggingFace Tokenizer
- Building and Training a Tokenizer
- Encoder and Decoder
- Wrapping it with `PreTrainedTokenizerFast`

| Lecture | Topic                            | Format     | Link                                                                  |
| ------- | -------------------------------- | ---------- | --------------------------------------------------------------------- |
| L1      | Tokenization — Introduction      | 📝 Markdown | [L1_tokenizer.md](./Week_2/L1_tokenizer.md)                           |
| L2      | Byte Pair Encoding               | 📝 Markdown | [L2_BPE_tokenizer.md](./Week_2/L2_BPE_tokenizer.md)                   |
| L3      | WordPiece Tokenizer              | 📝 Markdown | [L3_wordspiece_tokenizer.md](./Week_2/L3_wordspiece_tokenizer.md)     |
| L4      | Sentence-Piece Tokenizer         | 🌐 HTML     | [L4_sentence_tokenizer.html](./Week_2/L4_sentence_tokenizer.html)     |
| L4      | Viterbi Algorithm                | 📝 Markdown | [L4_Viterbi.md](./Week_2/L4_Viterbi.md)                               |
| L5      | HuggingFace Tokenizer            | 🌐 HTML     | [L5_HF_tokenizer.html](./Week_2/L5_HF_tokenizer.html)                 |
| L6      | Building & Training a Tokenizer  | 📓 Notebook | [L6_DLP_HF_tokenizer.ipynb](./Week_2/L6_DLP_HF_tokenizer.ipynb)       |
| L7      | BERT Tokenizer                   | 🌐 HTML     | [L7_Bert_tokenizer.html](./Week_2/L7_Bert_tokenizer.html)             |
| L8      | Wrap it with PreTrainedTokenizer | 🌐 HTML     | [L8_pretrained_tokenizer.html](./Week_2/L8_pretrained_tokenizer.html) |
| —       | Graded Assignment 2              | 📓 Notebook | [Ga2.ipynb](./Week_2/Ga2.ipynb)                                       |

---

## 📝 Note Formats Explained

**📝 Markdown** — Read directly on GitHub. Concept explanations, code snippets, professor quotes.

**🌐 HTML** — Download and open in browser. Syntax-highlighted code, callout boxes, color-coded outputs.  
*To use: Click file on GitHub → `Download raw file` → open in Chrome/Firefox.*

**📓 Notebooks** — Runnable code. Open in Jupyter or [Google Colab](https://colab.research.google.com/).

**📋 Graded Assignments** — My answers with step-by-step explanations. Attempt yourself first!

---

## 🤝 How to use

1. **Browse on GitHub** — markdown notes render directly
2. **Download HTML files** — for formatted, syntax-highlighted notes
3. **Run notebooks** — in Jupyter or Colab
4. **Star ⭐** if it helped you

---

## ⚠️ Disclaimer

Personal study notes — not official IIT Madras material. Always refer to the official course content.

---

*Updated weekly · IIT Madras B.S. Degree Programme · Deep Learning Practice*