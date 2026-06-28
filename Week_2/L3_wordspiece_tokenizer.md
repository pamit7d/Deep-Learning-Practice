# WordPiece Tokenizer

## What is WordPiece?

**WordPiece** is a **subword tokenization algorithm** introduced by Google and used in models such as **BERT**.

Like **Byte Pair Encoding (BPE)**, WordPiece starts with a vocabulary of individual characters and gradually merges them into larger subwords.

The main difference is **how it chooses which pair to merge.**

---

# BPE vs WordPiece

### BPE

BPE merges the **most frequent adjacent pair**.

```
Best Pair = Pair having highest frequency
```

Example

| Pair | Frequency |
| ---- | --------- |
| in   | 7         |
| ng   | 7         |
| ow   | 5         |

BPE chooses

```
in
```

because it appears first (or whichever tie-breaking rule is used).

---

### Problem with BPE

Suppose

```
in = 7

ng = 7
```

Both have the same frequency.

Choosing either one changes the future merges.

So simply selecting the first pair may not always produce the best vocabulary.

---

# WordPiece Idea

Instead of looking only at **frequency**, WordPiece computes a **score** for every candidate pair.

The pair having the **highest score** is merged.

---

# WordPiece Score

The score is

$$
\boxed{
\text{Score}(A, B) = \frac{\text{Count}(AB)}{\text{Count}(A) \times \text{Count}(B)}
}
$$

where

- Count(AB) = frequency of the pair
- Count(A) = frequency of first token
- Count(B) = frequency of second token

---

# Why this Formula?

The numerator tells us

```
How often the pair occurs.
```

The denominator tells us

```
How often the two symbols occur individually.
```

If two symbols mostly appear **together**, their score becomes high.

If they often appear separately, their score becomes low.

So WordPiece prefers pairs that have a **strong association**, not just a high frequency.

---

# Example

Suppose

| Token | Frequency |
| ----- | --------- |
| I     | 20        |
| N     | 18        |
| IN    | 7         |

Score

```
7
----------------
20 × 18

= 0.0194
```

Now consider

| Token | Frequency |
| ----- | --------- |
| A     | 3         |
| D     | 2         |
| AD    | 1         |

Score

```
1
------------
3 × 2

= 0.1667
```

Although

```
IN
```

appears **7 times** and

```
AD
```

appears only **1 time**, WordPiece prefers

```
AD
```

because **A** and **D** rarely occur separately and usually appear together.

This indicates a stronger relationship.

---

# Algorithm

### Step 1

Initialize vocabulary with characters.

Example

```
k
n
o
w
i
n
g
```

---

### Step 2

Count

- Pair frequency
- Individual token frequency

Example

| Pair | Pair Count | First Count | Second Count |
| ---- | ---------- | ----------- | ------------ |
| in   | 7          | 20          | 18           |
| ng   | 7          | 18          | 10           |
| ad   | 1          | 3           | 2            |

---

### Step 3

Compute score

```
Score =
Pair Count
------------------------
First Count × Second Count
```

Example

| Pair | Score |
| ---- | ----- |
| in   | 0.019 |
| ng   | 0.039 |
| ad   | 0.167 |

---

### Step 4

Merge the pair having the highest score.

```
a + d

↓

ad
```

---

### Step 5

Update vocabulary.

Recalculate all frequencies.

Repeat until the desired vocabulary size is reached.

---

# WordPiece Flow

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
Count Individual Frequencies
   │
   ▼
Compute Score
   │
   ▼
Merge Highest Score Pair
   │
   ▼
Update Vocabulary
   │
   ▼
Repeat
```

---

# Example

Initial Vocabulary

```
k
n
o
w
i
n
g
```

Suppose

```
Knowing

↓

k n o w i n g
```

Possible pairs

```
kn

no

ow

wi

in

ng
```

Instead of selecting the pair with the highest frequency, WordPiece computes the score for every pair.

Suppose

```
ad

↓

Highest Score
```

Then

```
a d

↓

ad
```

is merged first, even if its frequency is lower than other pairs.

---

# Vocabulary Growth

```
Characters

↓

Characters + ad

↓

Characters + ad + ing

↓

Characters + ad + ing + know

↓

Final Vocabulary
```

---

# Stopping Criteria

Training stops when

- Desired vocabulary size is reached (e.g., 30K–50K tokens)
- Required number of merges is completed

---

# Advantages

- Learns meaningful subwords.
- Better merge decisions than BPE.
- Reduces Out-of-Vocabulary (OOV) words.
- Widely used in **BERT** and related models.

---

# BPE vs WordPiece

| Feature        | BPE               | WordPiece                          |
| -------------- | ----------------- | ---------------------------------- |
| Merge Rule     | Highest Frequency | Highest Score                      |
| Formula        | Frequency         | Pair Count / (Count(A) × Count(B)) |
| Merge Decision | Frequent pair     | Strongly associated pair           |
| Used In        | GPT-2, GPT-3      | BERT                               |

---

# Summary

- **BPE** merges the most frequent pair.
- **WordPiece** merges the pair with the highest association score.
- The score favors pairs whose symbols mostly occur together.
- This generally produces a better and more meaningful subword vocabulary.