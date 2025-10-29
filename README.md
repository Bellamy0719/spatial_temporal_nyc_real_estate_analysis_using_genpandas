# Spatial-Temporal Real Estate Analysis using Pandas & GeoPandas

### Exploratory analysis of New York City real-estate transactions combining tabular and geospatial data.
Focus: data loading, cleaning, spatial mapping, and temporal trend visualization using pandas, geopandas, and matplotlib.

### Project Workflow

### Step 1 – Fetch the Data

Download NYC rolling sales datasets (multiple Excel/CSV files) and zipcode shapefiles via urllib or manual download.

```
    for boro in ['manhattan','bronx','brooklyn','queens','statenisland']:
        try:
            url = f'https://www1.nyc.gov/assets/finance/downloads/pdf/rolling_sales/annualized-sales/{year}/{year}_{boro}.xls'
            urllib.request.urlretrieve(url, f'Data/RollingSale/{year}{boro}.xls')
```

### Step 2 – Clean the Format

Standardize column names
```
Sales.columns = [col.replace('\n','') for col in Sales.columns]
```

Subset dataframe by selecting columns we're going to use
```
selectedNames = ['BOROUGH','BLOCK','LOT', 'BUILDING CLASS CATEGORY', 'ADDRESS', 'ZIP CODE',
                'GROSS SQUARE FEET', 'YEAR BUILT','SALE PRICE', 'SALE DATE']
Sales = Sales[selectedNames]
```

### Step 3 – Merge All Years

Concatenate all borough-level sales files into a single DataFrame for unified analysis.
```
for file in files[1:]: #for all the files in the folder
    if '.xls' in  file: #just in case take only Excel ones (both xls and xlsx will qualify)
        df = pd.read_excel('./Data/RollingSale/'+file,skiprows=4)
        df.columns = [name.replace('\n','') for name in df.columns]  # fix the columns for all files
        df = df[selectedNames]  # filter the columns for all files
        # pd.concat: Concatenate pandas objects along rows or columns
        Sales = pd.concat([Sales,df],axis=0)
```

### Step 4 – Descriptive Analysis & Data Cleaning

Check missing values and duplicates

```
(Sales['GROSS SQUARE FEET'] == 0).sum() #and almost half of the records have missing size
```

Handle outliers (SALE PRICE > 0, GROSS SQ FT > 0)

```
#take only properties worth up to 10mln; we can see majority is under 1M and vast majority falls under 2M.
plt.hist(Sales['SALE PRICE'][Sales['SALE PRICE']<1e7], bins=100);

#so lets filter the zero values first 
Sales = Sales[(Sales['YEAR BUILT'] >= 1850) & (Sales['GROSS SQUARE FEET'] >=300) & (Sales['GROSS SQUARE FEET'] <1e5)
              & (Sales['SALE PRICE'] >= 1e4) & (Sales['SALE PRICE'] <= 5e8)]
```


### Step 5 – Category Analysis (Single Family Homes)

Unify category names
```
Sales['CATEGORY ID'] = Sales['BUILDING CLASS CATEGORY'].apply(lambda x:x[:2]) # apply a custom function taking first two digits of the category
Sales['BUILDING CLASS NAME'] = Sales['BUILDING CLASS CATEGORY'].apply(lambda x:x.split(' ',1)[1]).apply(lambda x:x.strip()) # apply a custom function taking middle class name of the category
```

Group by different columns to get a list of unique categories
```
#use groupby to get a list of unique categories (building classes) and use a count of any field (e.g. ADDRESS) to see how often those are occuring
Sales[['CATEGORY ID','BUILDING CLASS NAME','ADDRESS']].groupby(['CATEGORY ID','BUILDING CLASS NAME']).count()
```

### Step 6 – Price-per-SqFt Calculation

Calculation
```
SingleSales['PRICE_SQFT'] = SingleSales['SALE PRICE'] / SingleSales['GROSS SQUARE FEET']
```

Filter out the outliers by using quantile
```
SingleSales = SingleSales[(SingleSales['PRICE_SQFT'] >= SingleSales['PRICE_SQFT'].quantile(0.01)) & (SingleSales['PRICE_SQFT'] <= SingleSales['PRICE_SQFT'].quantile(0.99))] #filter out the outliers
```


### Step 7 – Spatial Filtering with GeoPandas

Apply spatial cleaning and only use those zip codes belong to NYC

```
SingleSales = SingleSales[SingleSales['ZIP CODE'].isin(NYC_zipcode)]
```

### Step 8 – Temporal Analysis I

Compute average price per square foot of the one, two and three family houses per borough

```
SalesYear = SingleSales.groupby(['SALE YEAR']).agg({'ADDRESS':'count','SALE PRICE':'sum','GROSS SQUARE FEET':'sum'})
SalesYear['PriceperSQFT'] = SalesYear['SALE PRICE'] / SalesYear['GROSS SQUARE FEET']
```

### Step 9 – Temporal Analysis II

Average House Size per zipcode for Single family

### Step 10 – Spatial Analysis

Spatial analysis Average price per sq.foot per zipcode

```
SalesZipcode = SingleSalesRecent.groupby(['ZIP CODE']).agg({'ADDRESS':'count','SALE PRICE':'sum','GROSS SQUARE FEET':'sum'})
SalesZipcode['PriceperSQFT'] = SalesZipcode['SALE PRICE'] / SalesZipcode['GROSS SQUARE FEET']
```

### Step 11 – Merge with ZIP Code Shapefile


