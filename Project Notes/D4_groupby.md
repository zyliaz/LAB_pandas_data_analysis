# D4 GroupBy Cheatsheet

## 1. GroupBy Aggregation

### Single key
```python
df.groupby('col')['value'].sum()
```

### Multiple keys
```python
df.groupby(['col_a', 'col_b']).agg(
    result=('value_col', 'sum')
).reset_index()
```
> **Caveat:** Without `.reset_index()`, group keys stay as the index (MultiIndex on multiple keys), making downstream column access awkward.

---

## 2. Period-based Grouping
```python
df['month'] = df['date_col'].dt.to_period('M')   # 'Q' for quarter, 'Y' for year
df.groupby('month')['value'].sum()
```
> **Caveat:** `.to_period()` returns a `Period` dtype — not sortable the same way as datetime. Convert back with `.to_timestamp()` if you need datetime arithmetic later.

---

## 3. `sort_values`
```python
df.sort_values('col', ascending=False)           # single col
df.sort_values(['col_a', 'col_b'], ascending=[False, True])  # multi-col
```
> **Caveat:** Returns a new DataFrame by default. Use `inplace=True`.

---

## 4. Within-group Top N
```python
top_n = (
    df.sort_values('rank_col', ascending=False)
      .groupby('group_col')
      .head(3)
      .reset_index(drop=True)
)
```
> **Caveat:** Use `.reset_index(drop=True)` to flatten the result.