# Viterbi Algorithm in SentencePiece — Finding the Best Tokenization

## The Problem

Given a word like **"where"**, there are many ways to split it into vocab tokens:

```
w + h + e + r + e
wh + e + r + e
wh + er + e
wh + ere
where
... and many more
```

Enumerating all possibilities is exponential. Viterbi solves this efficiently using **dynamic programming + pruning**.

---

## Vocab & Log Probabilities (Example)

| Token | Log Probability  |
| ----- | ---------------- |
| `w`   | -4.29            |
| `h`   | -3.34            |
| `wh`  | -5.99            |
| `e`   | -2.11            |
| `whe` | *(not in vocab)* |
| `r`   | -3.56            |
| `er`  | -4.01            |
| `e`   | -2.11            |
| `re`  | -4.50            |

> **Why log probabilities?** Multiplying many small probabilities causes underflow. Log turns multiplication into addition: `log(P(A) × P(B)) = log P(A) + log P(B)`

> **Why negative?** Log of a probability (0 to 1) is always negative. **Higher (less negative) = better.**

---

## Step-by-Step: Tokenizing "where"

### Character positions

```
Index:  0   1   2   3   4
Char:   w   h   e   r   e
```

---

### Position 1 — After `w`

Only one option:

| Segmentation | Score        |
| ------------ | ------------ |
| `w`          | -4.29 ✅ Best |

**Best path so far:** `[w]` → score = **-4.29**

---

### Position 2 — After `wh`

Two options to consider:

| Segmentation | Calculation     | Score        |
| ------------ | --------------- | ------------ |
| `w` + `h`    | -4.29 + (-3.34) | -7.63        |
| `wh`         | (single token)  | -5.99 ✅ Best |

**Best path so far:** `[wh]` → score = **-5.99**

> ✂️ **Pruned:** The path `w, h` is discarded. Any extension like `w, h, e, ...` will never beat `wh, ...` because it already starts worse.

---

### Position 3 — After `whe`

Extend only from the best path `[wh]`:

| Segmentation | Calculation      | Score        |
| ------------ | ---------------- | ------------ |
| `wh` + `e`   | -5.99 + (-2.11)  | -8.10 ✅ Best |
| `whe`        | *(not in vocab)* | ❌ invalid    |

**Best path so far:** `[wh, e]` → score = **-8.10**

> ✂️ **Pruned:** `w, he` would need `he` in vocab — if not present, invalid. Even if it were, it started from the already-pruned `w, h` path.

---

### Position 4 — After `wher`

Extend from `[wh, e]`:

| Segmentation     | Calculation     | Score            |
| ---------------- | --------------- | ---------------- |
| `wh` + `e` + `r` | -8.10 + (-3.56) | -11.66 ✅ Best    |
| `wh` + `er`      | -5.99 + (-4.01) | -10.00 ✅ Better! |

**Best path so far:** `[wh, er]` → score = **-10.00**

> 💡 Note: We compare **all valid ways** to reach position 4, and keep only the best.

---

### Position 5 — After `where`

Extend from `[wh, er]`:

| Segmentation      | Calculation      | Score            |
| ----------------- | ---------------- | ---------------- |
| `wh` + `er` + `e` | -10.00 + (-2.11) | -12.11 ✅ Best    |
| `wh` + `ere`      | -5.99 + (-4.50)  | -10.49 ✅ Better! |
| `where`           | *(check vocab)*  | depends          |

Assuming `where` is not in vocab and `ere` beats `er + e`:

**Final best path:** `[wh, ere]` → score = **-10.49**

---

## Viterbi as a Graph

```
START
  │
  ▼
[w]──────────────────────────────── score: -4.29
  │
  ├──(+h = -7.63)──[w,h]  ✂️ PRUNED (worse than wh)
  │
  └──(wh = -5.99)──[wh] ◀ BEST ──────────────────┐
                     │                             │
                     ├──(+e = -8.10)──[wh,e]       │
                     │      │                      │
                     │      ├──(+r = -11.66)──[wh,e,r]   ✂️
                     │      └──(er = -10.00)──[wh,er] ◀ BEST
                     │                  │
                     │                  ├──(+e = -12.11)──[wh,er,e]
                     │                  └──(ere = -10.49)──[wh,ere] ◀ BEST
                     │
                     └──(whe = ❌ not in vocab)
```

**Final Answer:** `where` → `[wh, ere]`

---

## Key Insight: Why Pruning Works

At every position, we only keep **one best path** to reach that position.

If path A is better than path B at position `i`, then:
- Any extension of A will **always** beat the same extension of B
- So we can **safely discard B** and never look at it again

This reduces the problem from **exponential** (all paths) to **linear** (one best path per position).

---

## Summary

| Concept                 | Meaning                                                |
| ----------------------- | ------------------------------------------------------ |
| **Log probability**     | How likely a token is; higher (less negative) = better |
| **Dynamic programming** | Build best solution position by position               |
| **Pruning**             | Discard any path that's already worse than the best    |
| **Viterbi**             | The algorithm that does all of the above efficiently   |

> The Viterbi algorithm guarantees finding the **globally optimal** segmentation without checking every possible combination.