# Spatial-Temporal Real Estate Analysis using Pandas & GeoPandas

### Exploratory analysis of New York City real-estate transactions combining tabular and geospatial data.
Focus: data loading, cleaning, spatial mapping, and temporal trend visualization using pandas, geopandas, and matplotlib.

Project Workflow
Step 1 – Fetch the Data

Download NYC rolling sales datasets (multiple Excel/CSV files) and zipcode shapefiles via urllib or manual download.

Step 2 – Clean the Format

Standardize column names

Convert date fields (SALE DATE) to datetime

Remove non-numeric or zero sale prices

Step 3 – Merge All Years

Concatenate all borough-level sales files into a single DataFrame for unified analysis.

Step 4 – Descriptive Analysis & Data Cleaning

Check missing values and duplicates

Handle outliers (SALE PRICE > 0, GROSS SQ FT > 0)

Compute summary statistics for validation

Step 5 – Category Analysis (Single Family Homes)

Filter residential properties by category (BUILDING CLASS CATEGORY == 'SINGLE FAMILY') and analyze size / price distributions.

Step 6 – Price-per-SqFt Calculation

Step 7 – Spatial Filtering with GeoPandas

Convert DataFrame to GeoDataFrame (geometry = Point(longitude, latitude))
Join with NYC ZIP code shapefile for spatial visualization.

Step 8 – Temporal Analysis I

Step 9 – Temporal Analysis II

Step 10 – Spatial Analysis

Step 11 – Merge with ZIP Code Shapefile
