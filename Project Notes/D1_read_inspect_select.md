# Pandas D1 Cheatsheet — Read, Inspect, Select

## Load & Basic Inspection

```python
df = pd.read_csv("file.csv")

df.shape        # (rows, cols)
df.dtypes       # Series of dtype for each column
df.info()       # dtypes + non-null counts + memory
df.describe()   # numeric cols summary stats
df.head(n)      # first n rows
```

## Column Selection

```python
df["col"]            # → Series
df[["col1","col2"]]  # → DataFrame (double brackets!)
```

## loc vs iloc

| | `loc` | `iloc` |
|---|---|---|
| Based on | Labels | Integer position |
| Row end inclusive? | Yes | No (like Python slicing) |
| Use case | index has meaningful labels （eg: timestamp) | Positional slices (eg: top 5) |

```python
df.loc[0:3, "Country"]        # rows labeled 0–3 (INCLUSIVE), col by name
df.iloc[0:3, 4]               # rows 0,1,2 (EXCLUSIVE end), col at position 4
df.loc[df["Qty"] > 0, "Col"]  # boolean indexing
```
**Note**: After `reset_index()`, label == position; but after filtering, they are not. 
After filtering rows (eg: `df[df["x"] > 0]`), original integer labels are preserved. Meaning that `iloc[0]` ≠ label 0 anymore. Always `reset_index(drop=True)` after filtering for new positional access.


## Index Awareness

```python
df.index               # RangeIndex(start=0, stop=N, step=1) by default
type(df.index)         # RangeIndex / Int64Index / etc.

df.reset_index()            # moves index → column, resets to 0,1,2...
df.reset_index(drop=True)   # discard old index entirely (usually what you want)
```


## Missing Data — Quick Counts

```python
df.isna().sum()                               # missing count per column
df.isna().mean()                              # missing rate per column (0.0–1.0)
df.isna().mean().sort_values(ascending=False) # top columns by missing rate

df["CustomerID"].isna().mean()                # missing rate for one column
(df.isna().mean() * 100).round(2)             # as percentage
```


## Value Counts & Unique

```python
df["Country"].value_counts()                 # count per value, sorted desc
df["Country"].value_counts(normalize=True)   # proportions instead of counts
df["InvoiceNo"].nunique()                    # count of unique values
```

**Note**: `value_counts()` excludes NaN by default, use `dropna=False` to include it


## Column Profiling Template

```python
def profile(df):
    return pd.DataFrame({
        "dtype":        df.dtypes,
        "n_missing":    df.isna().sum(),
        "missing_rate": df.isna().mean().round(4),
        "n_unique":     df.nunique(),
        "sample_values": [",".join(df[c].unique()[:3])
                          for c in df.columns]
    })
```