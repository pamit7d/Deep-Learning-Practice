# Byte Pair Encoding (BPE) & WordPiece Tokenization
> Beginner Friendly Notes (Based on IIT Madras Lecture)

---

# Table of Contents

1. Why do we need better tokenization?
2. Character vs Word vs Subword Tokenization
3. The Goal of Subword Tokenization
4. Overall Tokenizer Training Pipeline
5. Text Preprocessing
6. Pre-tokenization
7. Learning the Vocabulary
8. Byte Pair Encoding (BPE)
9. BPE Algorithm
10. BPE Example
11. Merge Operations
12. Number of Merges
13. WordPiece Tokenizer
14. Difference between BPE and WordPiece
15. WordPiece Score Formula
16. Why WordPiece Score Works
17. BPE vs WordPiece Comparison
18. Vocabulary Size
19. Key Takeaways

---

# 1. Why do we need better tokenization?

In the previous lecture we learned that the tokenizer converts

```
Sentence

↓

Tokens

↓

Token IDs
```

The next question is

> **What should be considered as a token?**

Possible choices are

```
Character

or

Word

or

Subword
```

Choosing the correct token type is extremely important because it directly affects

- Vocabulary size
- Embedding table size
- Softmax computation
- Ability to understand unknown words
- Model performance

---

# 2. Character vs Word vs Subword Tokenization

## Character Level

Sentence

```
Knowing
```

becomes

```
K
n
o
w
i
n
g
```

Vocabulary

```
A-Z
a-z
0-9
Symbols
```

Very small vocabulary

Around

```
100 tokens
```

### Advantage

Very small vocabulary.

### Problem

Sentence becomes very long.

Example

```
transformer
```

becomes

```
t r a n s f o r m e r
```

11 tokens

instead of

1.

---

## Word Level

Sentence

```
I enjoyed the movie
```

Tokens

```
I

enjoyed

the

movie
```

Vocabulary contains every word.

Example

```
apple

orange

computer

beautiful

running

...
```

Vocabulary may become

```
500,000+

or

Millions
```

### Advantage

Short sequence.

### Problems

Huge vocabulary.

Unknown words.

Misspellings.

New names.

Different languages.

---

## Subword Level

Middle ground.

Example

```
enjoyed
```

↓

```
enjoy

#ed
```

Example

```
running
```

↓

```
run

#ning
```

Vocabulary

```
30K – 50K
```

This is what modern LLMs usually use.

---

# Summary

| Method    | Vocabulary | Sequence Length |
| --------- | ---------: | --------------: |
| Character | Very Small |       Very Long |
| Word      | Very Large |      Very Short |
| Subword   |     Medium |          Medium |

Subword tokenization gives the best balance.

---

# 3. Goal of Subword Tokenization

An ideal tokenizer should

✔ Have a moderate vocabulary.

✔ Handle unknown words.

✔ Work for multiple languages.

✔ Avoid gigantic embedding matrices.

✔ Reduce softmax computation.

---

# 4. Overall Tokenizer Training Pipeline

```
Raw Text Corpus

↓

Preprocessing

↓

Pre-tokenization

↓

Learn Vocabulary

↓

Subword Tokenization

↓

Language Model
```

Notice something important.

The tokenizer is **trained before the language model**.

The language model never decides the vocabulary.

The tokenizer does.

---

# 5. Text Preprocessing

Before learning tokens,

we first clean the text.

Example

Original

```
Hmm.., I KNOW I Don't know
```

After preprocessing

```
hmm.., i know i don't know
```

Typical preprocessing includes

- lowercase conversion
- removing extra spaces
- removing HTML
- removing accents
- fixing Unicode
- normalizing punctuation

---

# 6. Pre-tokenization

This is simply

Whitespace splitting.

Example

```
I don't know
```

↓

```
["I","don't","know"]
```

Notice

```
don't
```

is still one word.

Later,

subword tokenization may split it into

```
do

#n't
```

So

```
Pre-tokenization

≠

Subword Tokenization
```

---

# 7. Learning the Vocabulary

This is the most important part.

Suppose we have

```
I enjoyed the movie
```

Possible vocabularies

Option 1

```
I

enjoyed

the

movie
```

Option 2

```
I

enjoy

#ed

the

movie
```

Option 3

```
I

en

joy

#ed

movie
```

Which vocabulary is best?

That is exactly what BPE and WordPiece try to learn.

---

# 8. Byte Pair Encoding (BPE)

BPE starts with

Only characters.

Example

Corpus

```
Knowing

Something

Movie
```

Initial vocabulary

```
a

b

c

...

z
```

Nothing else.

Then

BPE repeatedly merges

the most frequent adjacent pair.

---

# Main Idea

Suppose

```
i n
```

appears

1000 times.

Then instead of storing

```
i

n
```

separately every time,

we create a new token

```
in
```

This is called

Merge.

---

# 9. BPE Algorithm

Step 1

Start with characters.

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

Step 2

Count every adjacent pair.

Example

```
k n

n o

o w

w i

i n

n g
```

Count frequency.

---

Step 3

Choose the pair with highest frequency.

Suppose

```
i n
```

appears

7 times.

Highest.

Merge

```
i

n

↓

in
```

---

Step 4

Vocabulary becomes

```
a

b

...

z

in
```

---

Step 5

Repeat again.

Now

```
in

g
```

may become

```
ing
```

Repeat thousands of times.

---

# 10. BPE Example

Corpus

```
Knowing

Knowing

Knowing

Something

Everything
```

Initially

```
k n o w i n g
```

After first merge

```
in
```

Vocabulary

```
a

b

...

z

in
```

Next merge

```
ing
```

Vocabulary

```
a

b

...

z

in

ing
```

Later

```
know
```

may also become a token.

Eventually

```
knowing
```

might become

```
know

#ing
```

instead of

```
k n o w i n g
```

---

# 11. Merge Operations

Every merge creates

one new token.

Example

Merge 1

```
i

n

↓

in
```

Merge 2

```
in

g

↓

ing
```

Merge 3

```
know

ing

↓

knowing
```

Every merge increases vocabulary size by

1.

---

# 12. Number of Merges

Suppose

Initial vocabulary

```
100
```

characters.

Allowed merges

```
32,000
```

Final vocabulary

≈

```
32,100
```

That is why

BPE implementations usually ask

```
num_merges
```

instead of

```
vocabulary_size
```

More merges

↓

Larger vocabulary.

---

# 13. Problem with BPE

BPE chooses

Highest Frequency.

Example

| Pair | Frequency |
| ---- | --------: |
| in   |         7 |
| ng   |         7 |

Tie.

Which one should be merged?

Different choices produce

different vocabularies.

WordPiece improves this.

---

# 14. WordPiece Tokenizer

WordPiece also starts from characters.

It also performs merging.

But

instead of

Highest Frequency,

it chooses

Highest Score.

---

# 15. WordPiece Score

Formula

```
Score

=

Count(pair)

----------------------

Count(first) × Count(second)
```

or

```
Score(pair)

=

Frequency(pair)

/

(Frequency(first token)

×

Frequency(second token))
```

Example

```
Pair

in

Count(pair)

7

Count(i)

20

Count(n)

15
```

Score

```
7

------

20 × 15
```

Another pair

```
ad

Count(pair)

1

Count(a)

3

Count(d)

2
```

Score

```
1

----

3 × 2

=

0.166
```

Suppose

```
in

↓

0.023

ad

↓

0.166
```

WordPiece chooses

```
ad
```

even though

its frequency is smaller.

---

# 16. Why does this score work?

Think carefully.

Suppose

```
a
```

almost always appears together with

```
d
```

Then

```
ad
```

is a strong combination.

But

Suppose

```
i
```

appears everywhere.

```
n
```

appears everywhere.

Then

```
in
```

appearing often

is less surprising.

WordPiece prefers

tokens that naturally belong together.

Instead of

tokens that simply occur frequently.

---

# Simple Intuition

BPE asks

```
Which pair appears most?
```

WordPiece asks

```
Which pair is most strongly connected?
```

That is the biggest difference.

---

# 17. BPE vs WordPiece

| BPE                     | WordPiece                   |
| ----------------------- | --------------------------- |
| Uses frequency          | Uses score                  |
| Highest occurrence wins | Highest association wins    |
| Tie handled arbitrarily | Better statistical decision |
| Simpler                 | Smarter                     |

---

# Example

Suppose

```
Pair

Frequency

in

7

ad

1
```

BPE

↓

Chooses

```
in
```

WordPiece computes

```
in

↓

7/(20×15)

=

0.023

ad

↓

1/(3×2)

=

0.166
```

WordPiece chooses

```
ad
```

because

A and D almost always appear together.

---

# 18. Vocabulary Size

Typical LLM

Character Vocabulary

```
≈100
```

Subword Vocabulary

```
30,000–50,000
```

Large multilingual models

```
100,000+
```

because they must support many languages.

---

# 19. Key Takeaways

- **Character tokenization** → Tiny vocabulary but very long sequences.
- **Word tokenization** → Huge vocabulary and OOV (out-of-vocabulary) problems.
- **Subword tokenization** → Best compromise used by modern LLMs.
- **BPE** starts with characters and repeatedly merges the **most frequent adjacent pair**.
- Every merge creates **one new token** and increases the vocabulary size by one.
- The number of allowed merges largely determines the final vocabulary size.
- **WordPiece** improves BPE by using a statistical score instead of raw frequency.
- **BPE asks:** "Which pair appears most often?"
- **WordPiece asks:** "Which pair has the strongest association?"
- Modern models like **GPT** use BPE-style tokenization, while **BERT** uses WordPiece (or closely related approaches depending on the implementation).

---

# Memory Trick

```
Character Tokenization
↓

Very Small Vocabulary
↓

Long Sentences

----------------------------

Word Tokenization
↓

Huge Vocabulary
↓

Short Sentences

----------------------------

Subword Tokenization
↓

Balanced Vocabulary
↓

Balanced Sentence Length

----------------------------

BPE

↓

Merge Most Frequent Pair

----------------------------

WordPiece

↓

Merge Highest Score Pair

Score

=

Count(pair)

/

Count(first) × Count(second)
```


# Byte Pair Encoding (BPE) Implementation in Python

This is one of the simplest implementations of the Byte Pair Encoding (BPE) training algorithm.

```python
import re
import collections


def get_stats(vocab):
    pairs = collections.defaultdict(int)

    for word, freq in vocab.items():
        symbols = word.split()

        for i in range(len(symbols) - 1):
            pairs[symbols[i], symbols[i + 1]] += freq

    return pairs


def merge_vocab(pair, v_in):
    v_out = {}

    bigram = re.escape(' '.join(pair))
    p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')

    for word in v_in:
        w_out = p.sub(''.join(pair), word)
        v_out[w_out] = v_in[word]

    return v_out


vocab = {
    'l o w </w>'      : 5,
    'l o w e r </w>'  : 2,
    'n e w e s t </w>': 6,
    'w i d e s t </w>': 3
}

num_merges = 10

for i in range(num_merges):

    pairs = get_stats(vocab)

    best = max(pairs, key=pairs.get)

    vocab = merge_vocab(best, vocab)

    print(best)
```

---

# Overall Working

The algorithm repeatedly performs the following steps:

```
Vocabulary
      ↓
Count all adjacent symbol pairs
      ↓
Find most frequent pair
      ↓
Merge that pair
      ↓
Update vocabulary
      ↓
Repeat
```

---

# Line-by-Line Explanation

## Import Libraries

```python
import re
import collections
```

### `collections`

Used for storing frequencies conveniently.

### `re`

Used for Regular Expressions.

It helps replace character pairs safely inside words.

---

# Function 1 : `get_stats()`

```python
def get_stats(vocab):
```

This function **counts how many times every adjacent pair appears** in the vocabulary.

Input

```
Vocabulary
```

Output

```
Dictionary of pair frequencies
```

---

### Step 1

```python
pairs = collections.defaultdict(int)
```

Creates an empty dictionary.

Unlike a normal dictionary,

```
pairs["ab"]
```

automatically starts with value

```
0
```

instead of producing an error.

Example

```
pairs = {}

↓

pairs[("a","b")] = 0
```

---

### Step 2

```python
for word, freq in vocab.items():
```

Loop through every word in the vocabulary.

Example

```
Word = "l o w </w>"

Frequency = 5
```

---

### Step 3

```python
symbols = word.split()
```

Split the word into symbols.

Example

Before

```
"l o w </w>"
```

After

```
["l","o","w","</w>"]
```

---

### Step 4

```python
for i in range(len(symbols)-1):
```

Move through every adjacent pair.

Example

```
l
o
w
</w>
```

Pairs are

```
(l,o)

(o,w)

(w,</w>)
```

---

### Step 5

```python
pairs[symbols[i], symbols[i+1]] += freq
```

Increase the frequency of that pair.

Suppose

```
"l o w </w>"
```

appears

```
5
```

times.

Then

```
(l,o)

(o,w)

(w,</w>)
```

are each increased by

```
5
```

instead of only 1.

Why?

Because the entire word occurs 5 times.

---

### Step 6

```python
return pairs
```

Returns something like

| Pair  | Count |
| ----- | ----- |
| (l,o) | 7     |
| (o,w) | 7     |
| (e,s) | 9     |
| (s,t) | 9     |

The algorithm now knows which pair is most frequent.

---

# Function 2 : `merge_vocab()`

```python
def merge_vocab(pair, v_in):
```

This function merges the most frequent pair.

Suppose

```
pair

↓

('e','s')
```

We want

```
e s

↓

es
```

inside every word.

---

### Step 1

```python
v_out = {}
```

Create a new vocabulary.

The old vocabulary will remain unchanged.

---

### Step 2

```python
bigram = re.escape(' '.join(pair))
```

Suppose

```
pair

↓

('e','s')
```

Then

```
' '.join(pair)

↓

"e s"
```

`re.escape()` makes sure special characters are handled safely.

---

### Step 3

```python
p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
```

This creates a Regular Expression.

Purpose:

Only replace

```
e s
```

when they are **complete adjacent symbols**.

It prevents accidental replacement inside larger strings.

---

### Step 4

```python
for word in v_in:
```

Visit every word.

Example

```
l o w </w>

l o w e r </w>

n e w e s t </w>
```

---

### Step 5

```python
w_out = p.sub(''.join(pair), word)
```

Replace

```
e s
```

with

```
es
```

Example

Before

```
n e w e s t </w>
```

After

```
n e w es t </w>
```

---

### Step 6

```python
v_out[w_out] = v_in[word]
```

Store the updated word with the same frequency.

Example

Before

```
n e w e s t </w> : 6
```

After

```
n e w es t </w> : 6
```

Frequency does **not** change.

---

### Step 7

```python
return v_out
```

Return the updated vocabulary.

---

# Initial Vocabulary

```python
vocab = {

'l o w </w>'       : 5,

'l o w e r </w>'   : 2,

'n e w e s t </w>' : 6,

'w i d e s t </w>' : 3

}
```

Meaning

| Word   | Frequency |
| ------ | --------- |
| low    | 5         |
| lower  | 2         |
| newest | 6         |
| widest | 3         |

Notice every word is stored as **characters**, not as complete words.

---

# Number of Merges

```python
num_merges = 10
```

The algorithm will perform

```
10
```

merge operations.

---

# Main Loop

```python
for i in range(num_merges):
```

Repeat the merge process.

---

### Count pairs

```python
pairs = get_stats(vocab)
```

Example

```
(e,s) = 9

(s,t) = 9

(l,o) = 7

(o,w) = 7
```

---

### Find best pair

```python
best = max(pairs, key=pairs.get)
```

This finds the pair with the highest frequency.

Example

```
(e,s)
```

---

### Merge

```python
vocab = merge_vocab(best, vocab)
```

Example

Before

```
n e w e s t </w>
```

After

```
n e w es t </w>
```

---

### Print

```python
print(best)
```

Example Output

```
('e','s')

('es','t')

('l','o')

('lo','w')

('low','</w>')
```

These are exactly the merges learned by the BPE algorithm.

---

# Complete Algorithm

```
Start with character vocabulary

        │

        ▼

Count adjacent pairs

        │

        ▼

Find most frequent pair

        │

        ▼

Merge pair

        │

        ▼

Update vocabulary

        │

        ▼

Repeat N times

        │

        ▼

Final learned subword vocabulary
```

---

# Time Complexity

Let

- **N** = number of words
- **L** = average word length
- **M** = number of merges

Then

```
Pair Counting  → O(N × L)

One Merge      → O(N × L)

Training

≈ O(M × N × L)
```

Since pair counting is repeated after every merge, training becomes expensive for very large corpora, which is why optimized implementations (e.g., Hugging Face Tokenizers, SentencePiece, TikToken) use specialized data structures.