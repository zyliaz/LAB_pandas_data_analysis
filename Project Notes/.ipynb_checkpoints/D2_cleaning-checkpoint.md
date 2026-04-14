# Pandas Data Cleaning Cheatsheet

## 1. Handle Missing Values

### Drop Missing Values

```python
df = df_org.copy()

# drop rows with any NA
df = df.dropna()

# drop rows where specific column is NA
df = df.dropna(subset=['Description'])
```

### Fill Missing Values

```python
df = df_org.copy()

# fill with constant
df = df.fillna(0)

# fill with column mean
df = df.fillna(df.mean())

# fill specific column
df['Description'] = df['Description'].fillna('None')
```

### Forward / Backward Fill

```python
df.ffill()      # fill with value above
df.bfill()      # fill with value below
df.ffill(limit=1)   # limit number of rows filled
```

### Interpolation

```python
df.interpolate()                     # linear
df.interpolate(method='time')        # time index
df.interpolate(method='polynomial', order=2)
df.interpolate(method='akima')
```

### Missing Indicator Columns (useful for ML)

```python
df = df_org.copy()

# single column
df['CustomerID_missing'] = df['CustomerID'].isnull().astype(int)

# all columns with missing values
missing_cols = df.columns[df.isnull().any()].tolist()

for col in missing_cols:
    df[f'{col}_missing'] = df[col].isnull().astype(int)
```

### Caveats

- `df.dropna()` removes **any row containing NA** → often too aggressive  
- `fillna(None / np.nan / pd.NA)` **does nothing**  
- For a single column always assign back:

```python
df['col'] = df['col'].fillna(0)
```

---

# 2. Convert to Datetime

```python
df['InvoiceDate'] = pd.to_datetime(
    df['InvoiceDate'],
    format='%Y/%m/%d %H:%M',
    errors='coerce'
)
```

- `errors='coerce'` converts invalid values → `NaT` instead of raising errors.

---

# 3. Handle Duplicates

### When to Check

**Single-column duplicates**
- Fields that should be unique (`user_id`, `order_id`)

**Full-row duplicates**
- Often caused by ingestion issues (retries, reprocessing)

**Acceptable duplicates**
- Business logic allows repetition (e.g., one user → many orders)

---

### Removing Duplicates (Most Common)

```python
df = df.drop_duplicates()
```

---

### If duplicates contain useful information

Keep best record using rules:

- most recent timestamp
```python 
df = df.drop_duplicates(keep='last')
```
- fewest missing values  
- most reliable status  

Or **merge information first**, then deduplicate.

---

# 4. Final Validation

### % of rows dropped

```python
pct_dropped = (len(df_org) - len(df_clean)) / len(df_org)
```

### Verify cleaning worked

```python
df_org.isnull().sum()
df_clean.isnull().sum()

df_org.duplicated().sum()
df_clean.duplicated().sum()
```