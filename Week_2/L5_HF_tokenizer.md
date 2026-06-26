# Hugging Face Tokenizers Library

In the previous sections, we learned the theory behind **BPE, WordPiece, and SentencePiece**. Now, instead of implementing these algorithms from scratch, we use the **Hugging Face `tokenizers` library**, which provides an easy-to-use interface for building, training, and using tokenizers.

The tokenizer library encapsulates the complete tokenization pipeline into a single **Tokenizer** object.

---

# Complete Tokenization Pipeline

The tokenization process consists of four major stages.

```
Raw Input Text
      │
      ▼
Normalization
      │
      ▼
Pre-tokenization
      │
      ▼
Tokenization
(using learned vocabulary)
      │
      ▼
Post-processing
      │
      ▼
Final Tokens
```

Example:

```
Input

"Hmm,, I know I don't know"

        │

Normalization
(lowercase)

"hmm,, i know i don't know"

        │

Pre-tokenization

["hmm", ",", ",", "i", "know", "i", "don't", "know"]

        │

Tokenization

["hmm", ",", ",", "i", "know", "i", "don", "'", "t", "know"]

        │

Post Processing

[CLS] hmm , , i know i don't know [SEP]
```

---

# The Tokenizer Class

The Hugging Face `Tokenizer` object combines all stages into one class.

```
Tokenizer
│
├── Normalizer
├── Pre-tokenizer
├── Tokenization Algorithm
└── Post Processor
```

Instead of writing separate code for each stage, we configure the tokenizer once.

---

# Components of a Tokenizer

## 1. Normalizer

The **Normalizer** cleans and standardizes the text before tokenization.

Common operations include:

- Convert text to lowercase
- Remove accents
- Unicode normalization
- Remove unnecessary characters

Example

Input

```
"Café IS GOOD"
```

After Lowercase

```
"café is good"
```

After Removing Accent

```
"cafe is good"
```

Purpose:

Different spellings of the same word should be treated similarly.

---

## 2. Pre-tokenizer

The pre-tokenizer performs an initial split of the text.

Examples:

- Split by whitespace
- Split by punctuation
- Regular-expression based splitting

Example

Input

```
I don't know.
```

Whitespace Pre-tokenizer

```
["I", "don't", "know."]
```

Whitespace + Punctuation

```
["I", "don", "'", "t", "know", "."]
```

The actual tokenizer algorithm (BPE, WordPiece, SentencePiece) will further process these pieces.

---

## 3. Tokenization Algorithm

This is the core algorithm that converts text into subword tokens.

Possible algorithms include:

- BPE
- WordPiece
- SentencePiece
- Unigram

Example

```
playing

↓

play + ing
```

or

```
playing

↓

playing
```

depending on the learned vocabulary.

---

## 4. Post Processor

After tokenization, additional tokens may be inserted.

Common examples:

- `[CLS]`
- `[SEP]`
- `<bos>`
- `<eos>`
- `<pad>`

Example

Before

```
I love NLP
```

After

```
[CLS] I love NLP [SEP]
```

These special tokens are required by many transformer models.

---

# Configuring a Tokenizer

The tokenizer can be customized by setting each component.

```
Tokenizer

↓

Normalizer

↓

Pre-tokenizer

↓

Algorithm

↓

Post Processor
```

Example choices

| Component      | Example                             |
| -------------- | ----------------------------------- |
| Normalizer     | LowerCase, StripAccents             |
| Pre-tokenizer  | Whitespace, Regex, BertPreTokenizer |
| Algorithm      | BPE, WordPiece, SentencePiece       |
| Post Processor | Add [CLS], [SEP], BOS, EOS          |

---

# Example Configuration

```python
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.normalizers import Lowercase
from tokenizers.pre_tokenizers import Whitespace

tokenizer = Tokenizer(BPE())

tokenizer.normalizer = Lowercase()

tokenizer.pre_tokenizer = Whitespace()
```

Explanation:

```python
Tokenizer(BPE())
```

Creates a tokenizer that will use the **BPE algorithm**.

---

```python
tokenizer.normalizer = Lowercase()
```

Every input sentence is converted to lowercase.

Example

```
HELLO

↓

hello
```

---

```python
tokenizer.pre_tokenizer = Whitespace()
```

Splits text based on spaces before applying BPE.

Example

```
I love AI

↓

["I","love","AI"]
```

---

# Learning the Vocabulary

Before encoding text, the tokenizer must **learn its vocabulary** from a dataset.

The learning process is called **training the tokenizer**.

```
Training Dataset

↓

Learn Vocabulary

↓

Vocabulary Created

↓

Ready for Encoding
```

Example

```python
tokenizer.train(files, trainer)
```

During training, the tokenizer learns

- Vocabulary
- Token frequencies
- Merge rules (BPE)
- Subword units

---

# Common Methods

The `Tokenizer` class provides many useful methods.

### `train()`

Learns the vocabulary from a dataset.

```python
tokenizer.train(files, trainer)
```

---

### `encode()`

Converts a sentence into token IDs.

```python
encoding = tokenizer.encode("Hello World")
```

Output

```
["hello","world"]

↓

[1543,287]
```

---

### `encode_batch()`

Encodes multiple sentences together.

```python
tokenizer.encode_batch(sentences)
```

Example

Input

```
["Hello","Good Morning","How are you"]
```

Output

```
[
 [154],
 [321,874],
 [876,456,982]
]
```

Useful for processing many sentences efficiently.

---

### `decode()`

Converts token IDs back into text.

```python
tokenizer.decode(ids)
```

Example

```
[1543,287]

↓

Hello World
```

---

### `add_tokens()`

Adds custom tokens.

Example

```python
tokenizer.add_tokens(["<URL>"])
```

---

### `add_special_tokens()`

Adds model-specific tokens.

Example

```python
tokenizer.add_special_tokens(
["[CLS]","[SEP]"]
)
```

---

# Training Workflow

Building a tokenizer usually follows these steps.

```
Create Tokenizer

        │

Set Normalizer

        │

Set Pre-tokenizer

        │

Choose Algorithm

        │

Train Vocabulary

        │

Vocabulary Ready

        │

Encode Text

        │

Decode Predictions
```

Notice that we **do not manually call each stage every time**.

Once the tokenizer is configured and trained,

```
encode()

↓

automatically performs

Normalization

↓

Pre-tokenization

↓

Tokenization

↓

Post-processing
```

---

# Encoding Output

The output of `encode()` is not just token IDs.

It is an **Encoding object** containing useful information.

```
Encoding

├── ids
├── tokens
├── attention_mask
├── type_ids
├── offsets
└── special_tokens_mask
```

Example

```
Input

↓

"I love NLP"

↓

Encoding Object

↓

ids

↓

[101,1054,542,102]
```

Later, this encoding is directly fed into transformer models.

---

# Customization

Every stage can be customized.

For example,

Normalizers

```
LowerCase

StripAccents

Unicode Normalization
```

Pre-tokenizers

```
Whitespace

Regex

BertPreTokenizer
```

Algorithms

```
BPE

WordPiece

SentencePiece
```

Post Processors

```
CLS

SEP

BOS

EOS
```

Thus, the Hugging Face tokenizer library is highly flexible and supports building tokenizers for different languages and models.

---

# Key Takeaways

- The `Tokenizer` class combines all stages of tokenization.
- The four main stages are:
  - Normalization
  - Pre-tokenization
  - Tokenization
  - Post-processing
- The tokenizer must first **learn the vocabulary** before it can encode text.
- Important methods include:
  - `train()`
  - `encode()`
  - `encode_batch()`
  - `decode()`
  - `add_tokens()`
  - `add_special_tokens()`
- Once trained, the tokenizer automatically performs the complete pipeline whenever `encode()` is called.