 # Data Download Instructions

The raw and processed data files are NOT included in this repository
due to file size (11.4 GB raw, 0.39 GB processed).

## Direct Download Link

Download data automatically by running:
```
notebooks/01_download_data.ipynb
```

This notebook downloads all files directly from:
```
https://transtats.bts.gov/PREZIP/
```

## Manual Download

Download files for years 2022-2025 using this URL pattern:
```
https://transtats.bts.gov/PREZIP/On_Time_Reporting_Carrier_On_Time_Performance_1987_present_{YEAR}_{MONTH}.zip
```

Example:
```
https://transtats.bts.gov/PREZIP/On_Time_Reporting_Carrier_On_Time_Performance_1987_present_2022_1.zip
```

## Steps After Download

1. Place all ZIP files in `data/raw/`
2. Run `notebooks/01_download_data.ipynb` to extract
3. Run `notebooks/02_data_ingestion.ipynb` to convert to Parquet
4. Then run remaining notebooks in order

## Dataset Details

| Property | Value |
|----------|-------|
| Source | Bureau of Transportation Statistics (BTS) |
| Years | 2022, 2023, 2024, 2025 |
| Files | 47 CSV files |
| Raw size | 11.40 GB |
| Processed | 0.39 GB (Parquet) |
| Records | 27,075,400 flights |
