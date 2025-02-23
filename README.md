
# Google BaseMap Download

This repository provides a step-by-step workflow for **downloading satellite basemap tiles** as GeoTIFF files from Google Maps using Python. It demonstrates how to:

1. **Initialize the working environment** (including authentication if on Google Colab).  
2. **Visualize a base map** and **draw a Region of Interest (ROI)** interactively.  
3. **Download the ROI** as a GeoTIFF file.  
4. **Display the downloaded GeoTIFF** on a map.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Usage](#usage)
  - [Block 1: Import Libraries & Setup Authentication](#block-1-import-libraries--setup-authentication)
  - [Block 2: Visualize Base Map for ROI](#block-2-visualize-base-map-for-roi)
  - [Block 3: Save Tiff File of ROI](#block-3-save-tiff-file-of-roi)
  - [Block 4: Display the Downloaded Tiff on Map](#block-4-display-the-downloaded-tiff-on-map)
- [Libraries](#Libraries)

---

## Prerequisites

- **Python 3.x**
- **leafmap** and **localtileserver** libraries
- **geemap** library (for certain GIS functions)
- **Google Drive** access if you are working in Google Colab and wish to store downloaded files there

If you are running in **Google Colab**, the script will automatically attempt to install missing libraries and mount your Google Drive.

If you are running in a **local Jupyter Notebook**, ensure the libraries are installed via `pip install leafmap localtileserver geemap` before running the script.

---

## Usage

### Block 1: Import Libraries & Setup Authentication

```python
# Block 1: Import all libraries and Setup Authentication & Environment

import geemap
import os

# Ask user if they are working in Google Colab or local Jupyter
env_choice = input("\nAre you working in Google Colab or local Jupyter? (colab/jupyter): ").strip().lower()
if env_choice == "colab":
    try:
        from google.colab import drive
        drive.mount('/content/drive')
        print("Google Drive mounted successfully.")
    except Exception as e:
        print("Error mounting Google Drive:", e)
    # Install necessary packages in Colab if not already installed.
    try:
        !pip install leafmap localtileserver
    except Exception as e:
        print("Error installing required packages:", e)
else:
    print("Proceeding with local Jupyter environment.")

import leafmap
import localtileserver

print("\n===============================================")
print("= Block 1: Setup and Authentication completed =")
print("===============================================")
```

**What it does:**

- Imports necessary Python libraries (`geemap`, `leafmap`, `localtileserver`, `os`).
- Checks if you’re running in **Google Colab** or **local Jupyter**.
- If in **Google Colab**:
  - Mounts Google Drive.
  - Installs any missing dependencies.
- Prints a confirmation message when done.

### Block 2: Visualize Base Map for ROI

```python
# Block 2: Visualize Google Base Map to Draw Polygon on ROI

m = leafmap.Map(center=[38.5449, -121.7405], zoom=12, height="800px")
m.add_basemap("SATELLITE")
m
```

**What it does:**

- Creates an interactive map centered on a specific location (`[38.5449, -121.7405]`).
- Sets the zoom level to **12** and the map height to **800px**.
- Adds a **Google Satellite** basemap.
- Displays the map for you to draw a **Polygon** (or any other geometry) as your **ROI** using the map’s drawing tools (usually found on the left side of the map in leafmap).

### Block 3: Save Tiff File of ROI

```python
# Block 3: Saving Tiff File of ROI

# Saving drawn bounding box
bbox = m.user_roi_bounds()

# Prompt the user to input the base directory in Google Drive
base_dir = input("Enter your Google Drive base directory (e.g., '/content/drive/MyDrive'): ")

# Define the download folder path
download_folder = os.path.join(base_dir, "EarthEngineDownload")

# Create the folder if it does not exist
if not os.path.exists(download_folder):
    os.makedirs(download_folder)
    print(f"Created folder: {download_folder}")
else:
    print(f"Folder already exists: {download_folder}")

# Define the output file path within the EarthEngineDownload folder
output_path = os.path.join(download_folder, "Google_Map_Image.tif")

# Use leafmap to download the satellite tiles as a GeoTIFF image
leafmap.map_tiles_to_geotiff(
    output=output_path, 
    bbox=bbox, 
    zoom=19, 
    source="Satellite", 
    overwrite=True
)

print(f"GeoTIFF saved to: {output_path}")
```

**What it does:**

- Retrieves the bounding box of the **ROI** you drew on the map (`m.user_roi_bounds()`).
- Asks you for a **Google Drive base directory** (e.g., `/content/drive/MyDrive`).
- Creates an `EarthEngineDownload` folder if it doesn’t exist already.
- Constructs a file path (`output_path`) for the GeoTIFF file.
- Uses `leafmap.map_tiles_to_geotiff` to **download** the satellite basemap **tiles** within the ROI as a **GeoTIFF**.
- Saves the GeoTIFF in the newly created (or existing) folder.
- Prints the saved file path.

### Block 4: Display the Downloaded Tiff on Map

```python
# Block 4: Show the Final Tiff File on Map

m.layers[-1].visible = False
m.add_raster(output_path, layer_name="Image")
m
```

**What it does:**

- **Hides** the previously visible basemap layer (the last layer in `m.layers`).
- **Adds** the downloaded GeoTIFF as a **new raster layer**.
- Displays the updated map, showing only your **GeoTIFF** image layer.

---

## Libraries

- **[leafmap](https://github.com/giswqs/leafmap)** providing an easy way to download and visualize geospatial data.  
- **[geemap](https://github.com/giswqs/geemap)** interactive mapping with Earth Engine.  
- **[localtileserver](https://github.com/banesullivan/localtileserver)** serving local raster data tiles in a notebook environment.

---
