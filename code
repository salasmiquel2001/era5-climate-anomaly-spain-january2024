# ==============================================================================
# PROJECT: High-Resolution Spatio-Temporal Climate Anomaly (WMO Standard)
# DATASET: ERA5-Land Monthly Averaged Data (1991-2020 Baseline vs 2024)
# AUTHOR: [Miquel Salas]
# ==============================================================================

# 1. LOAD REQUIRED LIBRARIES
# ------------------------------------------------------------------------------
library(ecmwfr)   # Programmatic access to Copernicus CDS API
library(terra)    # Core package for spatial raster data processing
library(geodata)  # Administrative boundaries acquisition

# 2. API CREDENTIALS CONFIGURATION
# ------------------------------------------------------------------------------
# IMPORTANT: Replace with your own Copernicus CDS credentials. 
user_email <- "your.email@example.com"
user_key   <- "your-cds-api-key"

wf_set_key(user = user_email, key = user_key)

# 3. CDS API REQUEST DEFINITION (30-year rigor: 1991-2020 + 2024)
# ------------------------------------------------------------------------------
request_senior <- list(
  dataset_short_name = "reanalysis-era5-land-monthly-means",
  variable       = "2m_temperature",
  year           = c(as.character(1991:2020), "2024"),
  month          = "01",
  time           = "00:00",
  data_format    = "netcdf",
  area           = c(44, -10, 36, 4), # Bounding box for Spain
  target         = "era5_final_30y_january.nc"
)

# 4. DOWNLOAD EXECUTION & ZIP HANDLING
# ------------------------------------------------------------------------------
# Check if the file already exists to avoid redundant downloads
if (!file.exists("era5_final_30y_january.nc") && !file.exists("era5_final_30y_january.zip")) {
  message("Connecting to Copernicus CDS... Downloading data (This may take a while).")
  
  wf_request(
    user     = user_email, 
    request  = request_senior, 
    transfer = TRUE, 
    path     = getwd(), # Forces download to the current working directory
    verbose  = TRUE
  )
}

# The new CDS API sometimes compresses large NetCDF files into ZIPs automatically.
# This block handles the extraction if a ZIP file is detected.
if (file.exists("era5_final_30y_january.zip")) {
  message("Extracting compressed data...")
  unzip("era5_final_30y_january.zip", exdir = getwd())
}

# 5. GEOSPATIAL PROCESSING & ANOMALY COMPUTATION
# ------------------------------------------------------------------------------
# Locate the extracted .nc file
nc_file <- list.files(pattern = "\\.nc$")[1] 

if (!is.na(nc_file) && file.exists(nc_file)) {
  
  message(paste("Initializing spatial processing for:", nc_file))
  
  # Load the multidimensional raster and convert temperature to Celsius
  climate_series <- rast(nc_file) - 273.15
  
  # Compute 30-year climatological baseline (Layers 1 to 30)
  baseline_30y <- mean(climate_series[[1:30]]) 
  
  # Extract the target temporal layer (Layer 31: January 2024)
  january_2024 <- climate_series[[31]]
  
  # Compute thermal anomaly
  thermal_anomaly <- january_2024 - baseline_30y
  
  # Fetch national boundaries and mask data to Spain's polygon
  spain_map <- world(resolution = 3, level = 0, path = tempdir())
  spain_map <- spain_map[spain_map$NAME_0 == "Spain", ]
  anomaly_spain <- mask(crop(thermal_anomaly, spain_map), spain_map)
  
  # 6. HIGH-RESOLUTION CARTOGRAPHIC EXPORT
  # ------------------------------------------------------------------------------
  png(filename = "Climate_Anomaly_January2024_Spain.png", 
      width = 2800, height = 2200, res = 300)
  
  # Adjust margins
  par(mar = c(5, 5, 12, 3)) 
  
  # Custom diverging color palette (Blue = Cooler, Red = Warmer)
  div_pal <- rev(colorRampPalette(c("#b2182b", "#d6604d", "#f4a582", "#f7f7f7", 
                                    "#92c5de", "#4393c3", "#2166ac"))(100))
  
  # Render the map
  plot(anomaly_spain, 
       main = "Spatio-Temporal Climate Anomaly: January 2024\nMonthly Mean vs. 1991-2020 WMO Standard Baseline\nERA5-Land Global Reanalysis",
       cex.main = 1.3,
       col = div_pal,
       axes = FALSE, 
       box = FALSE,
       plg = list(title = "Diff (°C)", shrink = 0.8, cex = 1.1))
  
  # Add spatial context (borders, scale bar, north arrow)
  lines(spain_map, lwd = 1.8, col = "gray15")
  sbar(d = 200, type = "bar", divs = 2, below = "km", cex = 1.1)
  north(type = 1, cex = 1)
  
  dev.off()
  
  message("PROJECT COMPLETED! High-resolution cartography successfully exported.")
  
} else {
  warning("Error: NetCDF file not found. Check Copernicus CDS request status.")
}
