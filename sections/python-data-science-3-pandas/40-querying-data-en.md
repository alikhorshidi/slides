# Querying data

## Querying data (by row numbers / column numbers)

- `df.iloc[5]`: row 5 (returns a `Series` object)
- `df.iloc[:5]`: first 5 rows (returns a `DataFrame` object)
- `df.iloc[10:20]`: rows 10-19
- `df.iloc[5, 1]`: row 5, column 1
- `df.iloc[5, [0, 2]]`: row 5, columns 0 and 2

## Querying data (by index values / column names)

- `df.index`: row index values
- `df.columns`: column names

<!-- list separator -->

- `df.loc["2009-01-02"]`: Row by index value
- `df.loc["2009-01-01":"2009-01-31"]`: Rows in a specified range (both values included)
- `df.loc[:, "rate"]`: column `"rate"`
- `df["rate"]`: column `"rate"` (short version)
- `df.rate`: column `"rate"` (extra short version - only works with no special characters)
- `df.loc[:, ["rate", "maturity_level"]]`: two columns
- `df.loc["2009-01-02", "rate"]`: specific row and column

## Sorting rows

- `df.sort_values(by="rate")`
- `df.loc["2009-01-02" : "2009-12-31"].sort_values(by="rate")`
- `df.sort_index(ascending=False)`

## Sampling data

- `df.sample()` - random entry
- `df.sample(5)` - five entries
- `df.sample(frac=0.1)` - 10% of all entries

## Filtering entries

All values that don't fulfill the criterion are set to _NaN_ or _NA_.

```py
iris[iris > 0]
```

## Filtering rows

via _boolean indexing_ - which is applied by rows:

- `df[df.rate < 0]`
- `df[df.length < 0] = np.nan`
- `df[df.name == "Iris-setosa"]`
- `df[df.name.isin(["Iris-setosa", "Iris-virginica"])])]`

## Filtering rows

SQL template:

```sql
SELECT * FROM df
WHERE a < b AND b < c
```

Pandas:

```py
df[(df.a < df.b) & (df.b < df.c)]
```

or

```py
df.query("a < b < c")
```

## Exercises (Euribor)

- first entry
- last entry
- last 10 entries
- entry from 2009-01-02
- entries from the year 2009
- ...

## Solutions (Euribor)

- first entry: `euribor.iloc[0]`
- last entry: `euribor.iloc[-1]`
- last 10 entries: `euribor.iloc[-10:]`
- entry from 2009-01-02: `euribor.loc["2009-01-02"]`
- entries from the year 2009: `euribor.loc["2009-01-01": "2009-12-31"]`

## Exercises (Iris)

- maximum _petal length_ of _iris setosa_ (without using `.max`)

## Exercises (Titanic)

- percentage of survivors
- percentage of survivors amongst males
- percentage of survivors amongst children

## Exercises (Exchange rates)

- display _date_ and _exchange rate_ for USD-EUR

## Solutions (Exchange rates)

```py
euro_exchange_rates = exchange_rates[
    exchange_rates.Country == "Euro"
]
euro_exchange_rates.loc[:, ["Date", "Exchange rate"]]
```

## Exercises (S&P 500)

- when was the S&P 500 at its highest value? (compute the maximum, then get the corresponding row in the DataFrame)

## Solution (S&P 500)

```py
sp500_max = sp500["SP500"].max()
# returns a DataFrame
sp500_max_row = sp500.loc[sp500["SP500"] == sp500_max]
```

shorter alternative (using `.idxmax()`):

```py
# returns a Series
sp500_max_row = sp500.loc[sp500["SP500"].idxmax()]
```
