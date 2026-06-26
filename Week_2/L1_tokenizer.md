# Tokenization in Large Language Models (LLMs)
> Beginner Friendly Notes (Based on Lecture + Diagram)

---

# Table of Contents

1. Why do LLMs need Tokenization?
2. Overall Pipeline
3. What is a Token?
4. What is a Tokenizer?
5. Encoder vs Decoder of Tokenizer
6. Token IDs
7. Vocabulary
8. Embeddings
9. Embedding Matrix
10. Transformer / Language Model
11. Output Prediction
12. Softmax
13. Decoder Process
14. Complete Example
15. Why don't we use words directly?
16. Why Vocabulary Size Matters
17. Special Tokens
18. Complete Flow Summary

---

# 1. Why do LLMs need Tokenization?

Computers **do not understand English, Hindi or any human language.**

For example,

```
How are you?
```

For humans, this is just a sentence.

For a computer, it is only a sequence of numbers.

Therefore, before giving text to an AI model, we must convert it into numbers.

This conversion process is called

> **Tokenization**

---

# Overall Pipeline

```
Input Sentence
      │
      ▼
Tokenizer
      │
      ▼
Tokens
      │
      ▼
Token IDs
      │
      ▼
Embeddings
      │
      ▼
Transformer / Language Model
      │
      ▼
Predicted Token IDs
      │
      ▼
Tokenizer Decoder
      │
      ▼
Words
      │
      ▼
Final Output Sentence
```

---

# 2. What is a Token?

A token is the **smallest unit** that the model works with.

Many beginners think

```
Token = Word
```

This is **NOT always true.**

Sometimes a token is

- one word
- part of a word
- punctuation
- symbol
- number

Examples

Example 1

```
How are you
```

Tokens

```
["How", "are", "you"]
```

Example 2

```
running
```

May become

```
["run", "ning"]
```

Example 3

```
unbelievable
```

May become

```
["un", "believ", "able"]
```

So,

**Word ≠ Token**

---

# 3. What is Tokenization?

Tokenization means

> Breaking a sentence into tokens.

Example

Sentence

```
How are you?
```

After tokenization

```
["How", "are", "you", "?"]
```

Notice that

```
?
```

can also be a token.

---

# 4. What is a Tokenizer?

Tokenizer is a program (or library) that performs two important jobs.

## Job 1

Convert

```
Text
```

↓

```
Tokens
```

↓

```
Token IDs
```

This is called

**Encoding**

---

## Job 2

Convert

```
Token IDs
```

↓

```
Tokens
```

↓

```
Sentence
```

This is called

**Decoding**

---

Therefore,

A tokenizer has two components:

```
Tokenizer

├── Encoder
└── Decoder
```

---

# 5. Encoder (Tokenizer Encoder)

Do not confuse this with the Transformer Encoder.

These are different things.

Tokenizer Encoder simply converts text into numbers.

Example

Input

```
How are you
```

↓

Split into tokens

```
["How","are","you"]
```

↓

Assign IDs

```
How → 1
are → 2
you → 3
```

Output

```
[1,2,3]
```

This numerical sequence is what the AI model receives.

---

# 6. Token IDs

A computer cannot understand

```
How
```

It understands only numbers.

Therefore every token gets a unique ID.

Example

| Token | ID  |
| ----- | --- |
| How   | 1   |
| are   | 2   |
| you   | 3   |
| I     | 4   |
| love  | 5   |
| AI    | 6   |

Now consider another sentence

```
How I love AI
```

After tokenization

```
["How","I","love","AI"]
```

Converted into IDs

```
[1,4,5,6]
```

Notice

```
How
```

always remains

```
1
```

Its ID never changes.

---

# Important Rule

Each token has exactly one ID.

```
How → 1

always
```

No matter where it appears.

---

# 7. Vocabulary

Vocabulary means

> The complete list of all tokens known by the model.

Example

Vocabulary

```
How
are
you
I
love
AI
India
computer
...
```

Every token inside this vocabulary has

- one unique ID
- one embedding vector

---

# Why can't we include every word in the world?

Imagine collecting

- all English words
- all Hindi words
- all names
- medicines
- diseases
- cities
- websites
- programming languages
- scientific terms

The vocabulary could become

Millions of words.

That creates two problems.

1. Huge embedding matrix
2. Huge output prediction layer

Both become expensive.

Therefore modern LLMs use

Subword Tokenization

instead of full-word tokenization.

---

# 8. Embeddings

Token IDs are still just numbers.

Example

```
How

↓

1
```

Does the number

```
1
```

contain any meaning?

No.

It is only an index.

Therefore we convert IDs into vectors.

These vectors are called

**Embeddings**

---

Example

```
ID = 1
```

Embedding

```
[0.18, -0.74, 0.93, ...]
```

Another

```
ID = 2

↓

[0.52, 0.11, -0.30, ...]
```

Each embedding may contain

768

or

1024

or

4096

numbers.

These numbers capture semantic meaning.

---

# Why Embeddings?

Suppose

```
King
Queen
Man
Woman
```

After training

Their embeddings become mathematically related.

Similar words have similar vectors.

That is why

```
Cat
Dog
Tiger
Lion
```

will usually have embeddings close to each other.

---

# 9. Embedding Matrix

Suppose

Vocabulary Size

```
V = 50,000
```

Embedding Dimension

```
D = 768
```

Then the embedding table looks like

```
50000 × 768
```

Every row corresponds to one token.

Example

```
Token ID 1

↓

[0.1 0.4 0.2 ...]

Token ID 2

↓

[0.6 0.8 0.3 ...]

Token ID 3

↓

[0.2 0.9 0.7 ...]
```

When the input ID is

```
3
```

the model simply picks

Row 3

from the embedding matrix.

---

# 10. Transformer / Language Model

Now the embeddings enter the Transformer.

```
Sentence

↓

Tokens

↓

IDs

↓

Embeddings

↓

Transformer
```

The Transformer performs

- Attention
- Pattern learning
- Context understanding
- Next-token prediction

This is where the actual intelligence exists.

The tokenizer is **not intelligent**; it simply converts between text and numbers. The Transformer is the part that learns language patterns and relationships.

---

# Example

Input

```
I love
```

The Transformer predicts

```
Python
```

or

```
you
```

depending on context.

But internally it predicts

Token IDs,

not words.

---

# 11. Output Prediction

Suppose vocabulary size

```
50,000
```

The model predicts

```
50,000 probabilities
```

Example

```
How

0.02

are

0.75

you

0.15

today

0.04

...
```

One probability for every token.

---

# 12. Softmax

Before prediction

The model outputs raw numbers called **logits**.

Example

```
How

4.1

are

8.2

you

5.7
```

These are **not probabilities** because:

- They can be any real number (positive or negative).
- They do not add up to 1.

We need a way to convert them into probabilities.

That is the job of the **Softmax** function.

### What Softmax Does

It takes all logits and converts them into values between **0 and 1**, such that:

- Every value represents a probability.
- All probabilities together sum to **1**.

Example

Raw logits

| Token | Logit |
| ----- | ----: |
| How   |   4.1 |
| are   |   8.2 |
| you   |   5.7 |

After Softmax

| Token | Probability |
| ----- | ----------: |
| How   |        0.02 |
| are   |        0.85 |
| you   |        0.13 |

Notice

```
0.02 + 0.85 + 0.13 = 1.00
```

The model then usually selects the token with the highest probability (or samples from the distribution during text generation).

---

# 13. Decoder (Tokenizer Decoder)

After the model predicts IDs,

The tokenizer decoder converts

```
ID

↓

Token

↓

Sentence
```

Example

Predicted IDs

```
[4,2,6]
```

Lookup table

```
4 → I

2 → am

6 → fine
```

Output

```
I am fine
```

If tokens are subwords

```
run

ning
```

Decoder joins them

↓

```
running
```

---

# 14. Complete Example

Input

```
How are you?
```

Step 1

Tokenization

```
["How","are","you","?"]
```

Step 2

Token IDs

```
[1,2,3,4]
```

Step 3

Embeddings

```
ID 1

↓

[0.2,0.6,...]

ID 2

↓

[0.1,0.3,...]

...
```

Step 4

Transformer

Processes embeddings.

Step 5

Predict next token

Suppose

```
fine
```

Token ID

```
25
```

Step 6

Decoder

```
25

↓

fine
```

Output

```
How are you? Fine.
```

---

# 15. Why don't we directly use words?

Because computers only understand numbers.

```
Hello
```

means nothing to the CPU.

But

```
742
```

can be stored in memory.

Therefore

```
Word

↓

ID

↓

Embedding
```

---

# 16. Why Vocabulary Size Matters

Suppose

Vocabulary

```
10 million words
```

Embedding Dimension

```
768
```

Embedding Matrix

```
10,000,000 × 768
```

This requires an enormous amount of memory.

Similarly, the model would have to compute probabilities over **10 million** possible output tokens every time it predicts the next token, making training and inference extremely slow.

Hence, LLMs usually keep vocabulary sizes around **30k–100k tokens** (depending on the tokenizer and language coverage).

---

# 17. Special Tokens

Besides normal tokens,

LLMs also use special tokens.

Examples

| Token    | Purpose                                       |
| -------- | --------------------------------------------- |
| `<PAD>`  | Padding shorter sequences                     |
| `<BOS>`  | Beginning of sentence                         |
| `<EOS>`  | End of sentence                               |
| `<MASK>` | Used in masked language modeling (e.g., BERT) |
| `<CLS>`  | Classification token                          |
| `<SEP>`  | Separator between sentences                   |
| `<UNK>`  | Unknown token                                 |

These tokens are added by the tokenizer when required.

---

# 18. Complete Flow Summary

```
Human Sentence

↓

Tokenizer

↓

Tokens

↓

Token IDs

↓

Embedding Lookup

↓

Embedding Vectors

↓

Transformer / Language Model

↓

Logits

↓

Softmax

↓

Predicted Token ID

↓

Tokenizer Decoder

↓

Words

↓

Final Sentence
```

---

# Key Takeaways

- **Token** → Smallest unit processed by the model (word, subword, punctuation, etc.).
- **Tokenizer** → Converts text ↔ token IDs.
- **Encoder (Tokenizer)** → Text → Tokens → IDs.
- **Token ID** → Unique integer assigned to each token.
- **Vocabulary** → Collection of all known tokens.
- **Embedding** → Dense vector representation of a token that captures semantic meaning.
- **Embedding Matrix** → Lookup table storing embeddings for every token in the vocabulary.
- **Transformer / Language Model** → Learns context and predicts the next token.
- **Logits** → Raw scores produced by the model before probability conversion.
- **Softmax** → Converts logits into a probability distribution over the vocabulary.
- **Decoder (Tokenizer)** → Converts predicted IDs back into readable text.
- **Special Tokens** → Reserved tokens used for tasks like padding, sentence boundaries, masking, and classification.

---

# One-Line Memory Trick

```
Text
   ↓
Tokenizer
   ↓
Tokens
   ↓
Token IDs
   ↓
Embeddings
   ↓
Transformer
   ↓
Logits
   ↓
Softmax
   ↓
Predicted Token ID
   ↓
Tokenizer Decoder
   ↓
Final Text
```

This is the complete journey of a sentence through a modern Large Language Model.