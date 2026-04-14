# D3 Feature Engineering Cheatsheet

## 1. Vectorized Arithmetic
```python
df['new_col'] = df['col_a'] * df['col_b']
```
> run on the whole column as vector transformation.

---

## 2. Conditional Column (`np.where`)
```python
df['flag'] = np.where(df['col'] > threshold, 1, 0)
```
---

## 3. Datetime Extraction
```python
df['date_col'] = pd.to_datetime(df['date_col'])
df['month'] = df['date_col'].dt.month
```
> **Caveat:** Always call `pd.to_datetime()` first. `.dt` accessor raises `AttributeError` on object/string dtype columns.

---

## 4. Binning: `cut` vs `qcut`

### `cut` — fixed numerical boundaries
```python
df['bins'] = pd.cut(df['col'], bins=4, labels=['a','b','c','d'], duplicates='drop')
```

### `qcut` — quantile-based (equal-frequency)
```python
df['bins'] = pd.qcut(df['col'], q=4, labels=['I','II','III','IV'])
```

| | `cut` | `qcut` |
|---|---|---|
| Splits by | value range | frequency |
| Skewed data | uneven bucket sizes | even bucket sizes |
| Use when | domain thresholds known | want balanced bins |

> **Caveat:** `qcut` raises `ValueError` on duplicate bin edges (common with many zeros). Use `duplicates='drop'` to suppress.

---

## 5. `map` — Element-wise Custom Function
```python
df['new_col'] = df['col'].map(lambda x: some_func(x))
```
> **Caveat:** Use only when vectorization is impossible.
