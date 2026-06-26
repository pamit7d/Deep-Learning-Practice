# Byte Pair Encoding (BPE)

## What is BPE?

**Byte Pair Encoding (BPE)** is a **subword tokenization algorithm** used to build the vocabulary of many Large Language Models (LLMs), such as GPT-2 and GPT-3.

Originally, BPE was designed as a **data compression algorithm**, but it was later adapted for NLP to create an efficient vocabulary automatically.

Instead of treating every **word** or every **character** as a token, BPE learns **frequently occurring subwords** from the training corpus.

Example:

```
playing  →  play + ing
lowest   →  low + est
boys     →  boy + s
```

Thus, BPE lies between **word-level** and **character-level** tokenization.

---

# Why BPE?

Word-level tokenization has problems:

- Very large vocabulary
- Cannot handle unseen (Out-of-Vocabulary) words

Character-level tokenization also has problems:

- Sequence becomes very long
- Word meanings are lost

BPE solves both by learning **common subwords**.

---

# How BPE Works

BPE learns the vocabulary from a training corpus.

The basic idea is:

1. Split every word into characters.
2. Count every adjacent pair.
3. Merge the most frequent pair.
4. Repeat until the desired vocabulary size is reached.

Over time, common character sequences become complete words, while rare words remain split into smaller pieces.

---

# BPE Algorithm

### Step 1 : Prepare the Corpus

Suppose our corpus contains:

```
low
lower
newest
widest
```

Add an end-of-word symbol (`</w>`):

```
l o w </w>
l o w e r </w>
n e w e s t </w>
w i d e s t </w>
```

Initially, every character is a token.

---

### Step 2 : Count Adjacent Pairs

Find the frequency of every adjacent pair.

Example:

| Pair   | Frequency |
| ------ | --------- |
| l o    | 2         |
| o w    | 2         |
| e s    | 2         |
| s t    | 2         |
| w </w> | 1         |

The most frequent pair is merged first.

---

### Step 3 : First Merge

Merge:

```
e + s  →  es
```

Updated corpus:

```
l o w </w>
l o w e r </w>
n e w es t </w>
w i d es t </w>
```

Vocabulary becomes:

```
l
o
w
e
s
t
...
es
```

---

### Step 4 : Second Merge

Now count pair frequencies again.

The most frequent pair becomes:

```
es + t → est
```

Updated corpus:

```
l o w </w>
l o w e r </w>
n e w est </w>
w i d est </w>
```

Vocabulary becomes:

```
l
o
w
e
s
t
es
est
```

---

### Step 5 : Third Merge

Again count frequencies.

Suppose:

```
l + o → lo
```

Updated corpus:

```
lo w </w>
lo w e r </w>
n e w est </w>
w i d est </w>
```

Vocabulary now contains

```
l
o
w
e
s
t
es
est
lo
```

The algorithm continues until the required vocabulary size is reached.

---

# Vocabulary Evolution

| Merge   | New Token  | Vocabulary Added     |
| ------- | ---------- | -------------------- |
| Initial | Characters | l, o, w, e, s, t ... |
| 1       | es         | es                   |
| 2       | est        | est                  |
| 3       | lo         | lo                   |
| ...     | ...        | ...                  |

Notice that the vocabulary **grows**, while the corpus representation becomes shorter.

---

# Final Vocabulary (Example)

```
l
o
w
e
s
t
es
est
lo
low
```

Frequent words may become a single token, while rare words remain combinations of subwords.

---

# Encoding a New Word

Suppose the learned vocabulary contains

```
low
est
```

Now encode

```
lowest
```

BPE always matches the **longest possible token**.

```
lowest
↓

low + est
```

Instead of

```
l + o + w + e + s + t
```

This reduces the number of tokens.

---

# Encoding Process

```
Input Text
      │
      ▼
Split into characters
      │
      ▼
Match longest learned token
      │
      ▼
Replace with token IDs
      │
      ▼
Final Token Sequence
```

Example

```
lowest

↓

low
est

↓

[215, 983]
```

(Token IDs are only illustrative.)

---

# Decoding

Decoding is the reverse process.

```
Token IDs
      │
      ▼
Vocabulary Lookup
      │
      ▼
Subwords
      │
      ▼
Original Text
```

Example

```
[215,983]

↓

low
est

↓

lowest
```

---

# Stopping Criteria

Training stops when one of the following is reached:

- Desired vocabulary size
- Fixed number of merges
- No useful frequent pair remains

Modern LLMs usually choose a target vocabulary size (e.g., GPT-2 ≈ 50K tokens).

---

# Advantages

- Smaller vocabulary than word tokenization.
- Handles unseen words.
- Learns meaningful subwords automatically.
- Reduces sequence length compared to character tokenization.
- Captures prefixes, suffixes, and common roots.

---

# Limitations

- Greedy merging may not always produce linguistically meaningful tokens.
- Vocabulary depends heavily on the training corpus.
- Training requires repeated frequency counting.

---

# BPE Algorithm (Pseudo-code)

```text
Initialize vocabulary with all characters.

Repeat:

    Count frequencies of all adjacent token pairs.

    Find the most frequent pair.

    Merge that pair into a new token.

    Replace every occurrence of that pair.

Until stopping criterion is met.

Return the final vocabulary.
```

---

# Summary

```
Corpus
   │
   ▼
Split into Characters
   │
   ▼
Count Pair Frequencies
   │
   ▼
Merge Most Frequent Pair
   │
   ▼
Update Vocabulary
   │
   ▼
Repeat
   │
   ▼
Final Vocabulary
   │
   ▼
Encode New Words Using Learned Subwords
```

**Key Idea:**  
BPE starts with **characters**, repeatedly merges the **most frequent adjacent pair**, learns **subwords**, builds an efficient vocabulary, and finally tokenizes new text by matching the **longest learned subword**.