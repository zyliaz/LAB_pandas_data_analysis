# D8 Rate Calculation Cheatsheet

## 1. Map Categorical to 0/1
```python
df['Churn'] = df['Churn'].map({'Yes': 1, 'No': 0})
```
> **Caveat:** Any value not in the map dict becomes `NaN`. Check for unexpected category values before mapping.

---

## 2. Churn Rate Two Ways
```python
rate1 = df['Churn'].mean()
rate2 = df['Churn'].sum() / len(df)
# rate1 == rate2
```

---

## 3. Churn Rate by Segment
```python
df.groupby('segment_col')['Churn'].mean()
```

---

## 4. Delta vs Overall (Segment Lift)
```python
overall_rate = df['Churn'].mean()

for col in ['col_a', 'col_b', 'col_c']:
    delta = df.groupby(col)['Churn'].mean() - overall_rate
    delta_vs_overall = pd.DataFrame(delta / overall_rate)
    print(delta_vs_overall)
```
> `delta / overall_rate` gives the **relative** lift. e.g. `0.5` means 50% higher churn than average. Sort by `abs(delta)` to find the most impactful segments.