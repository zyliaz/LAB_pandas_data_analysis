# D7 Visualization Cheatsheet

## 0.0 Figure Template
```python
plt.figure(figsize=(12, 6))
# ... plot ...
plt.title('...')
plt.xlabel('...')
plt.ylabel('...')
plt.xticks(rotation=45) # for aesthetic
plt.tight_layout()
plt.savefig('path/to/output.png')   # savefig BEFORE show
plt.show()
```
> **Caveat:** Always call `plt.savefig()` before `plt.show()`.

---
## 0.1 Top-N Filtering Before Plotting
```python
# top N by sum
top_n = df.groupby('col')['value'].sum().nlargest(10).reset_index()

# top N rows
top_n = df.sort_values('value', ascending=False).head(20)
```
---

## 1. Line Plot -> Trend over Time
```python
sns.lineplot(data=df, x='date_col', y='value')
```
---

## 2. Bar Plot -> Categorical Comparison
```python
# pre-sort for correct bar order
order = df.sort_values('value', ascending=False)['category_col']

sns.barplot(data=df, x='category_col', y='value', order=order)
```
> **Caveat:** `order=` controls bar sequence.

---

## 3. Histogram + KDE —> Distribution
```python
sns.histplot(df['value'], bins=50, kde=True)
```
