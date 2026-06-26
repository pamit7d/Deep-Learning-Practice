# SentencePiece Tokenizer (Part 1)
## Motivation and Why SentencePiece Was Introduced

---

# What is SentencePiece?

SentencePiece is a **subword tokenization algorithm** developed by Google.

Unlike **BPE** and **WordPiece**, SentencePiece treats **tokenization as a probabilistic optimization problem** rather than a greedy merging process.

Instead of asking:

> "Which pair should I merge next?"

SentencePiece asks:

> "Among all possible ways to split this word, which segmentation is the most probable?"

This is the biggest idea behind SentencePiece.

---

# Why was SentencePiece introduced?

Let's first understand the limitation of BPE.

Recall that BPE performs these steps:

1. Start with characters.
2. Count pair frequencies.
3. Merge the most frequent pair.
4. Repeat.

The important point is:

**Once a merge is made, it can never be changed later.**

This makes BPE a **greedy algorithm**.

---

# What is a Greedy Algorithm?

A greedy algorithm makes the **best decision at the current step** without checking whether that decision is the best globally.

Example:

Imagine you are climbing a hill.

At every step you choose the highest nearby rock.

Sometimes this reaches the mountain peak.

Sometimes you become stuck on a smaller hill because you never looked ahead.

This is exactly how BPE works.

It always performs the best merge **at the current moment**.

---

# The Main Problem

A single word can often be split into subwords in **many different ways**.

Example:

```
hello
```

Suppose our vocabulary contains

```
h
e
l
o
he
lo
ll
hello
```

There are several valid segmentations.

---

## Segmentation 1

```
he + ll + o
```

---

## Segmentation 2

```
h + e + ll + o
```

---

## Segmentation 3

```
he + l + lo
```

---

## Segmentation 4

```
hello
```

All four segmentations are valid because every token exists in the vocabulary.

---

# Which One is Correct?

This is the important question.

BPE simply follows the merge order that was learned during training.

It does **not** compare all possible segmentations.

It simply says

```
Merge what I already know.
```

Therefore the final segmentation depends on the order in which merges happened during training.

---

# Example

Suppose BPE learned merges in this order

```
1. he
2. lo
3. ll
```

Then

```
hello

↓

he + l + lo
```

because

- "he" is merged first.
- Later "lo" is merged.
- Now "ll" cannot be formed because one of the L's already belongs to "lo".

---

Suppose the merge order was

```
1. ll
2. he
3. lo
```

Then the same word becomes

```
hello

↓

he + ll + o
```

Notice something interesting.

The vocabulary is exactly the same.

Only the merge order changed.

Yet the tokenization changed.

---

# Why is this a Problem?

Ideally, tokenization should depend on

```
Which segmentation best represents the word?
```

instead of

```
Which merge happened first?
```

This is exactly what SentencePiece tries to solve.

---

# SentencePiece Idea

SentencePiece says

Instead of greedily choosing merges,

let us examine **all possible segmentations**.

Example

```
hello
```

Possible segmentations

```
hello

he + l + lo

he + ll + o

h + e + ll + o

h + e + l + l + o
```

Now instead of picking one greedily,

SentencePiece computes

```
Probability of Segmentation 1

Probability of Segmentation 2

Probability of Segmentation 3

...

```

Finally,

it chooses the segmentation with the **highest probability**.

---

# Visual Difference

## BPE

```
Characters

↓

Merge

↓

Merge

↓

Merge

↓

Final Segmentation
```

Only one path is explored.

---

## SentencePiece

```
Characters

          ↙
         ↙
Segmentation 1

         ↘

Characters ----> Segmentation 2

         ↗

Segmentation 3

         ↖

Segmentation 4

↓

Choose Highest Probability
```

Many possibilities are explored before selecting the best one.

---

# Why is SentencePiece Better?

SentencePiece

- does not depend only on merge order.
- considers multiple valid segmentations.
- chooses the statistically most probable segmentation.
- works well even for languages without spaces (Japanese, Chinese, Korean, etc.).

---

# Key Takeaways

✔ BPE is a greedy algorithm.

✔ A word may have many valid segmentations.

✔ Different merge orders produce different tokenizations.

✔ SentencePiece considers multiple segmentations.

✔ It selects the segmentation having the highest probability instead of simply following merge order.

---

# What's Next?

In Part 2, we'll answer the most important question:

> **How does SentencePiece calculate the probability of a segmentation?**

We'll learn:

- Unigram Language Model
- Probability of a word
- Probability of a segmentation
- Why we multiply probabilities
- How SentencePiece chooses the best segmentation mathematically.
# SentencePiece Tokenizer (Part 2)
# Probability Model & Choosing the Best Segmentation

---

# Recap

In Part 1, we learned that a word can have multiple valid segmentations.

Example:

```
hello

↓

he + ll + o

↓

h + e + ll + o

↓

he + l + lo

↓

hello
```

The question is:

> Which segmentation should we choose?

SentencePiece answers this by assigning a **probability** to every segmentation and selecting the one with the highest probability.

---

# The Main Idea

SentencePiece assumes that every subword (token) has a probability.

Example Vocabulary

| Token | Probability |
| ----- | ----------- |
| he    | 0.40        |
| ll    | 0.20        |
| o     | 0.50        |
| h     | 0.10        |
| e     | 0.15        |
| lo    | 0.30        |
| hello | 0.05        |

These probabilities are learned from the training corpus.

---

# What is a Unigram Language Model?

SentencePiece uses a **Unigram Language Model**.

The word **Unigram** means:

> Every token is considered independent of every other token.

Suppose we have

```
he + ll + o
```

The probability is simply

```
P(he) × P(ll) × P(o)
```

We assume

```
he
```

does not depend on

```
ll
```

and

```
ll
```

does not depend on

```
o
```

This assumption makes probability calculations much simpler.

---

# Probability Formula

Suppose a segmentation contains

```
x1

x2

x3

...

xn
```

Then

\[
P(X)=P(x_1)\times P(x_2)\times ... \times P(x_n)
\]

or

\[
P(X)=\prod_{i=1}^{n} P(x_i)
\]

where

- **X** = one complete segmentation
- **xi** = one subword

---

# Example 1

Suppose

```
hello

↓

he + ll + o
```

Probabilities

```
P(he)=0.40

P(ll)=0.20

P(o)=0.50
```

Then

```
P(X)

=

0.40 × 0.20 × 0.50

=

0.04
```

---

# Example 2

Another segmentation

```
hello

↓

h + e + ll + o
```

Suppose

```
P(h)=0.10

P(e)=0.15

P(ll)=0.20

P(o)=0.50
```

Then

```
P(X)

=

0.10 × 0.15 × 0.20 × 0.50

=

0.0015
```

---

# Which Segmentation Wins?

Compare

```
he + ll + o

Probability = 0.04
```

and

```
h + e + ll + o

Probability = 0.0015
```

Since

```
0.04 > 0.0015
```

SentencePiece chooses

```
he + ll + o
```

because it is more probable.

---

# Another Example (Knowing)

Suppose the word is

```
knowing
```

Possible segmentations are

### Candidate 1

```
k + now + ing
```

### Candidate 2

```
know + ing
```

### Candidate 3

```
knowing
```

SentencePiece computes

```
P(Candidate 1)

P(Candidate 2)

P(Candidate 3)
```

Then selects the largest one.

---

# Why Multiply?

Suppose

```
he + ll + o
```

contains three independent tokens.

The probability that all three occur together is

```
P(he)

×

P(ll)

×

P(o)
```

This is a basic probability rule for independent events.

Therefore SentencePiece multiplies token probabilities.

---

# Why Use Log Probability?

Suppose

```
0.40 × 0.20 × 0.50 × 0.15 × 0.12 × 0.30
```

After many multiplications, the number becomes extremely small.

Example

```
0.000000000034
```

Computers may lose precision.

To avoid this, SentencePiece converts everything into **log probabilities**.

Using the logarithm,

```
log(a × b)

=

log(a) + log(b)
```

So multiplication becomes addition.

Instead of

```
0.40 × 0.20 × 0.50
```

we compute

```
log(0.40)

+

log(0.20)

+

log(0.50)
```

This is much faster and numerically stable.

---

# Formula Using Log

Instead of

\[
P(X)=\prod_{i=1}^{n}P(x_i)
\]

SentencePiece computes

\[
\log P(X)=\sum_{i=1}^{n}\log P(x_i)
\]

This is mathematically equivalent but easier for computers.

---

# Objective of SentencePiece

Suppose

```
hello
```

has four possible segmentations.

SentencePiece calculates

```
Probability(S1)

Probability(S2)

Probability(S3)

Probability(S4)
```

Finally,

```
Choose

Segmentation

having

Maximum Probability
```

Mathematically,

\[
X^*=\arg\max P(X)
\]

where

- **X** = any possible segmentation
- **X\*** = best segmentation

---

# Flow of Probability Computation

```
Word

↓

Generate Possible Segmentations

↓

Compute Probability of Each Segmentation

↓

Multiply Token Probabilities

↓

(Use Log to Convert Multiplication into Addition)

↓

Choose Highest Probability

↓

Final Tokenization
```

---

# Example Summary

Suppose

```
Candidate A

↓

he + ll + o

↓

0.40 × 0.20 × 0.50

↓

0.040
```

Suppose

```
Candidate B

↓

h + e + ll + o

↓

0.10 × 0.15 × 0.20 × 0.50

↓

0.0015
```

Since

```
0.040

>

0.0015
```

SentencePiece selects

```
he + ll + o
```

---

# Key Takeaways

✔ SentencePiece assigns a probability to every token.

✔ Probability of a segmentation is the product of the probabilities of all its tokens.

✔ In practice, log probabilities are used instead of direct multiplication.

✔ The segmentation with the highest probability is selected.

✔ This probabilistic approach makes SentencePiece more flexible than BPE and WordPiece.

---

# What's Next?

In **Part 3**, we'll learn how SentencePiece is actually trained using the **Expectation-Maximization (EM) algorithm**, how it starts with a very large vocabulary, repeatedly removes bad tokens, and finally reaches the desired vocabulary size.
```

# SentencePiece Tokenizer (Part 3)
# Training SentencePiece using the EM Algorithm

---

# Recap

In Part 2, we learned that SentencePiece chooses the segmentation with the **highest probability**.

The next question is:

> **Where do these probabilities come from?**

SentencePiece learns these probabilities automatically using the **Expectation-Maximization (EM) Algorithm**.

---

# What is the EM Algorithm?

EM (Expectation-Maximization) is an optimization algorithm used when some information is **hidden** or **unknown**.

In SentencePiece:

- We know the words in the corpus.
- We **do not know** the correct segmentation of each word.

Therefore,

```
Words = Known (Observed)

Segmentation = Unknown (Hidden)
```

The hidden segmentation is called a **latent variable**.

---

# What is a Latent Variable?

A latent variable is something that exists but is **not directly observed**.

Example

Suppose we see

```
playing
```

Possible segmentations are

```
play + ing

playing

pla + ying

p + laying
```

The dataset only contains

```
playing
```

It never tells us which segmentation is correct.

Therefore,

```
Observed Variable

↓

playing

Hidden Variable

↓

play + ing
```

SentencePiece tries to discover this hidden segmentation.

---

# Basic Idea of SentencePiece Training

Unlike BPE,

SentencePiece **does not start with characters and keep merging**.

Instead it does the opposite.

```
Start with a very large vocabulary

↓

Remove unnecessary tokens

↓

Repeat

↓

Obtain desired vocabulary
```

This is why SentencePiece is often called a **vocabulary pruning algorithm**.

---

# Step 1 : Choose Desired Vocabulary Size

Suppose we want

```
Vocabulary Size = 30,000
```

or

```
Vocabulary Size = 50,000
```

This is our target.

---

# Step 2 : Build a Large Initial Vocabulary

SentencePiece first builds a **much larger vocabulary**.

Example

Desired vocabulary

```
30,000
```

Initial vocabulary

```
250,000
```

This initial vocabulary can be obtained from

- BPE
- WordPiece
- All unique words
- Character combinations

The important idea is

```
Initial Vocabulary

>>

Desired Vocabulary
```

---

# Why Start Large?

Suppose we eventually want

```
30K tokens
```

Instead of guessing which 30K are useful,

SentencePiece first collects almost everything.

Then it gradually removes bad tokens.

This is similar to

```
Write every possible answer.

↓

Delete the wrong ones.

↓

Keep only the best.
```

---

# EM Training Process

The training repeats two steps:

```
E-Step

↓

M-Step

↓

Repeat
```

until the desired vocabulary size is reached.

---

# Step 3 : E-Step (Expectation)

In this step,

SentencePiece estimates the probability of every token.

Suppose the vocabulary contains

| Token  | Frequency |
| ------ | --------- |
| play   | 500       |
| ing    | 300       |
| player | 100       |
| playe  | 20        |

Total frequency

```
920
```

Then

```
P(play)

=

500 / 920
```

Similarly

```
P(ing)

=

300 / 920
```

Every token receives a probability.

These probabilities are exactly the values used in Part 2 when computing segmentation probabilities.

---

# Step 4 : M-Step (Maximization)

Now consider every word in the training corpus.

Example

```
something
```

Possible segmentations

```
something

some + thing

some + th + ing

s + om + et + hing

...
```

SentencePiece computes the probability of **every segmentation**.

Example

```
P(something)

=

P(something)
```

```
P(some + thing)

=

P(some)

×

P(thing)
```

```
P(some + th + ing)

=

P(some)

×

P(th)

×

P(ing)
```

Finally,

it chooses the segmentation with the **highest probability**.

---

# Why Does This Change the Vocabulary?

Suppose earlier the word

```
something
```

was segmented as

```
some + thing
```

Now suppose the algorithm decides

```
som + et + hing
```

Then

```
Frequency(some)

↓

decreases
```

while

```
Frequency(som)

↓

increases
```

Similarly

```
Frequency(thing)

↓

decreases
```

and

```
Frequency(hing)

↓

increases
```

Thus,

changing the segmentation automatically changes token frequencies.

---

# Step 5 : Recompute Token Probabilities

Since token frequencies changed,

their probabilities also change.

Again compute

```
P(play)

P(ing)

P(some)

P(thing)

...
```

These updated probabilities are used in the next iteration.

---

# Step 6 : Remove Bad Tokens

Now rank all tokens by probability.

Example

| Token | Probability |
| ----- | ----------- |
| play  | 0.08        |
| ing   | 0.06        |
| er    | 0.05        |
| xyz   | 0.00001     |
| pq    | 0.000004    |

The least useful tokens are removed.

Example

```
Bottom 20%

↓

Delete
```

Only useful tokens remain.

---

# Repeat

The entire process repeats.

```
Large Vocabulary

↓

Compute Token Probabilities

↓

Find Best Segmentation

↓

Update Frequencies

↓

Update Probabilities

↓

Remove Low Probability Tokens

↓

Repeat
```

Each iteration reduces the vocabulary.

Eventually,

```
250K

↓

180K

↓

120K

↓

70K

↓

50K

↓

30K
```

Training stops.

---

# Why Does This Work?

Initially,

many useless tokens exist.

Example

```
xyab

qmn

abcdeq

...
```

These tokens are rarely selected in the best segmentation.

Therefore,

their probabilities become extremely small.

Eventually,

SentencePiece removes them automatically.

The vocabulary gradually contains only useful subwords.

---

# Complete Training Flow

```
Large Initial Vocabulary

↓

Estimate Token Probabilities (E-Step)

↓

Find Best Segmentation for Every Word (M-Step)

↓

Update Token Frequencies

↓

Update Token Probabilities

↓

Remove Lowest Probability Tokens

↓

Repeat

↓

Desired Vocabulary Size
```

---

# Difference from BPE

### BPE

```
Characters

↓

Merge

↓

Merge

↓

Merge

↓

Vocabulary Grows
```

---

### SentencePiece

```
Large Vocabulary

↓

Remove Weak Tokens

↓

Remove Weak Tokens

↓

Remove Weak Tokens

↓

Vocabulary Shrinks
```

---

# Advantages

- Doesn't depend on greedy merges.
- Learns token probabilities automatically.
- Produces a statistically better vocabulary.
- Can work even for languages without spaces.
- Removes unnecessary tokens automatically.

---

# Key Takeaways

✔ SentencePiece starts with a **large vocabulary**.

✔ It uses the **Expectation-Maximization (EM) algorithm** to learn token probabilities.

✔ During each iteration:
- Estimate token probabilities (E-Step)
- Find the best segmentation for every word (M-Step)
- Update token frequencies
- Remove low-probability tokens

✔ The vocabulary gradually shrinks until the desired vocabulary size is reached.

---

# What's Next?

In **Part 4**, we'll learn the most challenging part of SentencePiece:

**How does it efficiently find the best segmentation without checking millions of possibilities?**

We'll study the **Viterbi Algorithm**, a dynamic programming technique that avoids brute-force search and finds the highest-probability segmentation efficiently.


# SentencePiece Tokenizer (Part 4)
# Viterbi Algorithm (Dynamic Programming)

---

# Recap

In Part 3, we learned that during the **M-Step**, SentencePiece must find the **best segmentation** for every word.

Example

```
something
```

Possible segmentations are

```
something

some + thing

some + th + ing

som + et + hing

s + om + et + h + ing

...
```

SentencePiece computes the probability of every segmentation and chooses the one with the highest probability.

---

# The Problem

Suppose a word contains

```
8 characters
```

There can be many possible segmentations.

Example

```
something
```

Possible segmentations

```
something

some + thing

some + th + ing

som + et + hing

so + meth + ing

s + om + et + h + ing

...
```

As the word becomes longer, the number of possible segmentations increases rapidly.

Checking every segmentation one by one becomes **very slow**.

---

# Brute Force Solution

A simple approach is

```
Generate every segmentation

↓

Compute probability

↓

Compare probabilities

↓

Choose the largest
```

Although correct,

this is computationally expensive.

---

# Better Solution

SentencePiece uses the **Viterbi Algorithm**.

The Viterbi algorithm is a **Dynamic Programming (DP)** algorithm.

Instead of checking every possible segmentation,

it remembers the best result obtained so far and never recomputes it.

---

# What is Dynamic Programming?

Dynamic Programming (DP) is a technique used when

- the same calculation is repeated many times.
- we can reuse previous results.

Instead of solving the same problem repeatedly,

DP stores the answer once and reuses it.

---

# Simple Example

Suppose you want to reach step 10.

Without DP

```
Step 10

↓

Compute Step 9

↓

Compute Step 8

↓

Compute Step 7

...

Again and Again
```

With DP

```
Step 1 ✔

↓

Store Result

↓

Step 2 ✔

↓

Store Result

↓

...

↓

Reuse Stored Results
```

This avoids repeated work.

---

# Applying DP to SentencePiece

Suppose the word is

```
WHERE
```

Vocabulary

```
W

WH

HE

HER

RE

WHERE
```

Our goal is

```
Find the highest probability segmentation.
```

---

# Step 1

Start with the first character.

```
W
```

Probability

```
P(W)
```

This becomes our best segmentation so far.

---

# Step 2

Now consider

```
WH
```

Two possibilities exist.

Option 1

```
W + H
```

Option 2

```
WH
```

Compute both probabilities.

Suppose

```
P(W)+P(H)

=

-7.63
```

(Log probability)

and

```
P(WH)

=

-5.99
```

Since

```
-5.99

>

-7.63
```

(Remember: larger log probability means better.)

The best segmentation becomes

```
WH
```

---

# Important Observation

Once

```
WH
```

is better than

```
W + H
```

there is **no reason to continue extending**

```
W + H
```

because it is already worse.

SentencePiece simply discards it.

This process is called

```
Pruning
```

---

# What is Pruning?

Pruning means

```
Stop exploring bad choices early.
```

Instead of checking every path,

we remove paths that can never become the best solution.

---

# Example

Suppose we have

```
WH
```

Next character

```
E
```

Possible segmentations

Option 1

```
WH + E
```

Option 2

```
W + HE
```

Option 3

```
W + H + E
```

If

```
WH + E
```

already has the highest probability,

SentencePiece ignores

```
W + H + E
```

from now on.

This dramatically reduces computation.

---

# Visual Example

Suppose

```
WORD
```

Possible paths

```
W

├── O

│    ├── R

│    │    └── D

│

└── WO

      ├── R

      └── RD
```

Brute Force explores

```
Every path.
```

Viterbi explores

```
Only the best path so far.
```

---

# Example from the Lecture

Suppose

```
WH
```

can be segmented as

Option 1

```
W + H
```

Probability

```
-7.63
```

Option 2

```
WH
```

Probability

```
-5.99
```

Since

```
WH
```

is better,

SentencePiece stores

```
Best till position 2

↓

WH
```

The path

```
W + H
```

is discarded forever.

---

# Continue Forward

Now consider

```
WHE
```

Instead of exploring every previous possibility,

SentencePiece only extends

```
WH
```

because

```
WH
```

was already the best path.

Again compare

```
WH + E

W + HE

...
```

Choose the best.

Discard the others.

Continue.

---

# Why is This Faster?

Suppose

```
8 characters
```

Brute Force

```
Checks

↓

Every segmentation
```

Viterbi

```
Keeps

↓

Only the best segmentation
at every position.
```

This reduces the search space enormously.

---

# Complete Flow

```
Word

↓

Start from first character

↓

Compute Best Segmentation

↓

Store Best Result

↓

Extend Only Best Result

↓

Discard Worse Paths

↓

Repeat

↓

Final Best Segmentation
```

---

# Why Viterbi Works

The key idea is

```
If one partial segmentation is already worse than another,

it can never become the best later.
```

Therefore,

it is safe to remove it immediately.

This saves a huge amount of computation.

---

# Dynamic Programming Table (Concept)

Imagine we have

```
WHERE
```

| Position | Best Segmentation | Best Log Probability |
| -------- | ----------------- | -------------------- |
| W        | W                 | -4.29                |
| WH       | WH                | -5.99                |
| WHE      | WH + E            | -7.12                |
| WHER     | WH + ER           | -8.40                |
| WHERE    | WHERE             | -9.10                |

Instead of storing every possible segmentation,

the algorithm stores only the **best segmentation** ending at each position.

---

# Advantages of Viterbi

- Much faster than brute force.
- Avoids checking every segmentation.
- Uses Dynamic Programming.
- Finds the optimal segmentation.
- Makes SentencePiece practical for very large datasets.

---

# Overall SentencePiece Training Pipeline

```
Large Vocabulary

↓

Estimate Token Probabilities (E-Step)

↓

For Every Word

↓

Use Viterbi Algorithm

↓

Find Best Segmentation

↓

Update Token Frequencies

↓

Update Token Probabilities

↓

Remove Low Probability Tokens

↓

Repeat

↓

Final Vocabulary
```

---

# Key Takeaways

✔ A word may have thousands of possible segmentations.

✔ Brute-force search is computationally expensive.

✔ SentencePiece uses the **Viterbi Algorithm** (Dynamic Programming) to efficiently find the best segmentation.

✔ It stores the best segmentation at each position and **prunes** worse paths.

✔ This avoids unnecessary computation while still guaranteeing the optimal segmentation.

---

# What's Next?

In **Part 5 (Final Part)**, we'll summarize the complete SentencePiece algorithm from start to finish and compare **BPE vs WordPiece vs SentencePiece** with an easy comparison table, flowchart, advantages, limitations, and interview notes.



# SentencePiece Tokenizer (Part 5)
# Complete Algorithm, Comparison & Summary

---

# Complete SentencePiece Algorithm

The complete training process of SentencePiece can be summarized in the following steps.

---

## Step 1 : Prepare the Corpus

Suppose our training corpus is

```
I love NLP

I love Machine Learning

Machine Learning is interesting

Natural Language Processing
```

The corpus is simply a collection of text used for learning the tokenizer.

---

## Step 2 : Choose Desired Vocabulary Size

Before training, decide the final vocabulary size.

Example

```
Vocabulary Size = 32,000
```

or

```
Vocabulary Size = 50,000
```

Training will stop once this size is reached.

---

## Step 3 : Build a Large Initial Vocabulary

Instead of starting with only characters,

SentencePiece creates a **large initial vocabulary**.

Example

```
Initial Vocabulary

↓

250,000 tokens
```

This vocabulary may contain

- Characters
- Words
- Subwords
- Frequent substrings

The initial vocabulary is intentionally much larger than required.

---

## Step 4 : Estimate Token Probabilities (E-Step)

Compute the probability of every token.

Formula

```
P(Token)

=

Token Frequency
-----------------------
Total Token Frequency
```

Example

| Token    | Frequency | Probability |
| -------- | --------- | ----------- |
| machine  | 100       | 100 / Total |
| learning | 80        | 80 / Total  |
| ing      | 200       | 200 / Total |

---

## Step 5 : Find Best Segmentation (M-Step)

For every word,

generate the segmentation having the highest probability.

Example

```
learning
```

Possible segmentations

```
learning

learn + ing

lea + rning

learn + i + ng
```

Compute

```
Probability of each segmentation

↓

Choose the largest one
```

The Viterbi Algorithm is used here to efficiently find the best segmentation.

---

## Step 6 : Update Token Frequencies

After choosing the best segmentation,

every selected token gets its frequency updated.

Example

Suppose

```
learning

↓

learn + ing
```

Then

```
Frequency(learn)

↑ increases

Frequency(ing)

↑ increases
```

If another segmentation is no longer used,

its token frequencies decrease.

---

## Step 7 : Update Probabilities

Since frequencies changed,

probabilities also change.

Again compute

```
P(machine)

P(learning)

P(ing)

...
```

---

## Step 8 : Remove Low Probability Tokens

Sort all tokens by probability.

Example

```
machine      0.08

learning     0.07

ing          0.05

xyz          0.00002

abcd         0.00001
```

Remove the least useful tokens.

Example

```
Bottom 20%

↓

Delete
```

Vocabulary becomes smaller.

---

## Step 9 : Repeat

Repeat

```
E-Step

↓

M-Step

↓

Update Frequencies

↓

Update Probabilities

↓

Remove Low Probability Tokens
```

until the desired vocabulary size is reached.

---

# Overall Training Flow

```
Training Corpus

        │

        ▼

Create Large Initial Vocabulary

        │

        ▼

Estimate Token Probabilities

        │

        ▼

Find Best Segmentation
(Viterbi Algorithm)

        │

        ▼

Update Frequencies

        │

        ▼

Update Probabilities

        │

        ▼

Remove Low Probability Tokens

        │

        ▼

Repeat

        │

        ▼

Final Vocabulary
```

---

# BPE vs WordPiece vs SentencePiece

| Feature           | BPE                  | WordPiece                  | SentencePiece                                    |
| ----------------- | -------------------- | -------------------------- | ------------------------------------------------ |
| Basic Idea        | Merge frequent pairs | Merge highest scoring pair | Keep best segmentation                           |
| Decision Rule     | Highest frequency    | Highest score              | Highest probability                              |
| Vocabulary        | Starts small → grows | Starts small → grows       | Starts large → shrinks                           |
| Training Method   | Greedy merges        | Greedy merges with score   | Probabilistic optimization                       |
| Search Method     | Greedy               | Greedy                     | Viterbi + Dynamic Programming                    |
| Probability Model | No                   | Partial scoring            | Yes (Unigram Model)                              |
| Used In           | GPT-2, GPT-3         | BERT                       | T5, ALBERT, XLNet, mT5, many multilingual models |

---

# Advantages of SentencePiece

- Learns statistically meaningful subwords.
- Does not depend on merge order.
- Finds the most probable segmentation.
- Works for languages with or without spaces.
- Uses Dynamic Programming for efficient search.
- Produces high-quality vocabularies.

---

# Limitations

- Training is slower than BPE.
- More mathematically complex.
- Requires probability estimation.
- Needs EM and Viterbi algorithms.

---

# Complete Comparison

## BPE

```
Characters

↓

Merge Most Frequent Pair

↓

Repeat

↓

Final Vocabulary
```

---

## WordPiece

```
Characters

↓

Compute Score

↓

Merge Highest Score Pair

↓

Repeat

↓

Final Vocabulary
```

---

## SentencePiece

```
Large Vocabulary

↓

Estimate Probabilities

↓

Best Segmentation

↓

Update Probabilities

↓

Remove Bad Tokens

↓

Repeat

↓

Final Vocabulary
```

---

# Which Models Use Which Tokenizer?

| Model      | Tokenizer     |
| ---------- | ------------- |
| GPT-2      | BPE           |
| GPT-3      | BPE           |
| RoBERTa    | BPE           |
| BERT       | WordPiece     |
| DistilBERT | WordPiece     |
| T5         | SentencePiece |
| ALBERT     | SentencePiece |
| XLNet      | SentencePiece |
| mT5        | SentencePiece |
| ByT5       | SentencePiece |

---

# Interview Questions

### Q1. Why is BPE called a greedy algorithm?

Because it always merges the most frequent pair without considering future merges.

---

### Q2. Why is SentencePiece better?

Because it evaluates multiple segmentations and chooses the one with the highest probability instead of relying on greedy merge order.

---

### Q3. Why is the Viterbi Algorithm used?

To efficiently find the highest-probability segmentation without checking every possible segmentation.

---

### Q4. What is the role of the EM Algorithm?

It repeatedly:

- estimates token probabilities (E-Step),
- finds the best segmentation (M-Step),
- updates probabilities,
- removes weak tokens.

---

### Q5. Why are log probabilities used?

Because multiplying many small probabilities leads to numerical underflow.

Using logarithms converts

```
Multiplication

↓

Addition
```

making computation faster and more stable.

---

# Final Summary

## BPE

```
Start with Characters

↓

Merge Frequent Pairs

↓

Vocabulary Grows
```

---

## WordPiece

```
Start with Characters

↓

Merge Highest Score Pair

↓

Vocabulary Grows
```

---

## SentencePiece

```
Start with Large Vocabulary

↓

Estimate Probabilities

↓

Find Best Segmentation

↓

Update Token Probabilities

↓

Remove Low Probability Tokens

↓

Repeat

↓

Vocabulary Shrinks

↓

Final Vocabulary
```

---

# One-Line Memory Trick

- **BPE** → **Merge by Frequency**
- **WordPiece** → **Merge by Score**
- **SentencePiece** → **Choose Segmentation by Probability**
- **Viterbi** → **Fast Dynamic Programming Search**
- **EM Algorithm** → **Estimate → Optimize → Repeat**

---

# Final Takeaway

SentencePiece is a **probabilistic subword tokenization algorithm**. Unlike BPE and WordPiece, which build the vocabulary by greedily merging tokens, SentencePiece starts with a **large vocabulary**, repeatedly finds the **most probable segmentation** of every word using the **Viterbi Algorithm**, updates token probabilities through the **EM algorithm**, removes low-probability tokens, and continues this process until the desired vocabulary size is reached.