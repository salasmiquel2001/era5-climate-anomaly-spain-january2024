# era5-climate-anomaly-spain-january2024
Spatio-temporal analysis of January 2024 temperature anomalies in Spain using Copernicus ERA5-Land data and WMO 1991-2020 baselines.

# 🌍 Spatio-Temporal Climate Anomaly Analysis (Spain)

This repository contains an R script designed to calculate and visualize high-resolution climate anomalies. Specifically, it compares the **January 2024** average temperatures in Spain against the **World Meteorological Organization (WMO) standard 30-year climatological baseline (1991-2020)**.

## Project Overview

The project programmatically connects to the **Copernicus Climate Data Store (CDS)** via its API, retrieves ERA5-Land monthly averaged reanalysis data, processes the NetCDF multidimensional arrays, and exports a publication-ready cartographic map.

### Key Features:
* **Automated Data Retrieval:** Uses the `ecmwfr` package to query and download ERA5-Land data directly from the Copernicus CDS API.
* **Smart File Handling:** Automatically detects and extracts compressed `.zip` files if the API serves large datasets.
* **Geospatial Processing:** Computes the 30-year historical baseline and the target anomaly using `terra`.
* **Spatial Masking:** Automatically downloads national administrative boundaries using `geodata` and masks the raster specifically to Spain's polygon.
* **High-Resolution Cartography:** Renders a customized, high-quality anomaly map (PNG, 300 DPI) with diverging color palettes, scale bars, and a north arrow.

## Prerequisites

To run this script, you will need **R** installed along with the following packages:
```R
install.packages(c("ecmwfr", "terra", "geodata"))

Copernicus API Key
You must have a registered account at the Copernicus Climate Data Store.

Log in and agree to the Terms of Use for the ERA5 dataset.

Get your personal API Key and Email.

Replace the placeholder credentials in the script (user_email and user_key) with your own.
