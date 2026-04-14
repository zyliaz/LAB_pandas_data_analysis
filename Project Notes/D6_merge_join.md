# D6 Merge / Join Cheatsheet

## 1. Four Merge Types
```python
df_merge = pd.merge(df1, df2, on='key', how='inner')
# can replace inner with left, right, outer
```
| Join | Keeps |
|---|---|
| `inner` | only matching keys in both |
| `left` | all rows from left, NaN for non-matches |
| `right` | all rows from right, NaN for non-matches |
| `outer` | all rows from both, NaN for non-matches |

> **Caveat:** Many-to-many keys cause row explosion: a key appearing M times on the left and N times on the right produces M×N rows. Always check row counts after merging.

---

## 2. `indicator=True` — Show Row Source
```python
pd.merge(df1, df2, on='key', how='left', indicator=True)
```
> Adds a `_merge` column with values `'left_only'`, `'right_only'`, or `'both'`. Useful for diagnosing unmatched rows.

---

## 3. `validate` — Catch Duplicate Key Bugs
```python
pd.merge(df1, df2, on='key', how='right', validate='m:1')  # many left : one right
pd.merge(df1, df2, on='key', validate='1:1')               # both unique
```
> **Caveat:** Raises `MergeError` if the assertion fails. Use this to avoid accidental row explosion.

---

## 4. `suffixes` — Resolve Column Name Conflicts
```python
pd.merge(df1, df2, on='key', suffixes=('', '_aov'))
```
> When both DataFrames share a non-key column name, pandas appends suffixes (`_x`/`_y` by default). Set explicit suffixes to keep names readable.

---

## 5. Anti-explosion Diagnostics (before merging)
```python
print(df_left['key'].duplicated().sum())    # expect > 0 for many-side
print(df_right['key'].duplicated().sum())   # expect 0 for one-side
```
> Always run before a merge. If the "one" side has duplicates, `validate='m:1'` will errort.