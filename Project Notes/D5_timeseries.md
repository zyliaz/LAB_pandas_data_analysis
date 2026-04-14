# D5 Time Series Cheatsheet

## 1. Periodic Sum with `.resample`
```python
series_day   = df.resample('D',  on='date_col')['value'].sum()
# can replace D with ME or W
```
> **Caveat:** Use `'ME'` (month-end) not `'M'`.

---

## 2. MOM Growth with `pct_change`
```python
series_month.pct_change(periods=1)
# 
```
> **Caveat:** First row is always `NaN`. Result is a decimal.

---

## 3. Rolling Mean & Std
```python
df.sort_values('date_col', inplace=True)          # must sort first

df['mean_7'] = df['value'].rolling(window=7).mean()
df['std_7']  = df['value'].rolling(window=7).std()
```
> **Caveat:** First 6 rows produce `NaN`. Always sort by date before rolling, Error otherwise.

---

## 4. Spike Detection
```python
spike = df['value'] > df['mean_7'] + 2 * df['std_7']
df = df.assign(spike=spike)

# filter spike rows
df[df['spike'] == True]
```
> **Caveat:** The first 6 rows (for a 7-day window) will never be flagged as spikes since `mean_7`/`std_7` are `NaN`.

---

## 5. Plot with Spike Markers
```python
plt.figure(figsize=(12, 6))
plt.plot(df['date_col'], df['value'], label='Daily Revenue')
plt.plot(df['date_col'], df['mean_7'], label='7-Day Rolling Mean', linestyle='--')

spike_dates   = df[df['spike']]['date_col']
spike_values  = df[df['spike']]['value']
plt.scatter(spike_dates, spike_values, color='red', label='Spike Days', zorder=5)

plt.xlabel('Date')
plt.ylabel('Value')
plt.title('...')
plt.legend()
plt.savefig('path/to/output.png', dpi=300, bbox_inches='tight')
plt.show()
```
> **Caveat:** `zorder=5` ensures scatter points render on top of line plots. Call `plt.savefig()` before `plt.show()` since `show()` clears the figure.