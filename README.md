# GIS Project: Optimizing Commercial & Industrial Rooftop Solar Potential in Trier

---

## 1. Project Overview

This GIS (Geographic Information System) project aims to identify, analyze, and visualize optimal rooftop locations for photovoltaic (PV) solar panel installations on commercial and industrial buildings within **Trier, Germany**. Developed as a proof-of-concept for an internship application at Strrom GmbH, this project leverages geospatial data and analysis techniques to support sustainable energy development and reduce energy costs for businesses.

## 2. Project Goals

* **Identify High-Potential Rooftops:** Pinpoint commercial and industrial buildings with significant estimated annual solar energy generation potential based on factors like usable roof area and average global horizontal irradiation.
* **Quantify Sustainability Impact:** Provide estimations of potential annual electricity production (in kWh) for identified optimal sites, directly linking to Strrom's goals of reducing energy costs and promoting sustainability.
* **Inform Strategic Planning:** Offer geospatial insights that could assist Strrom GmbH in targeting new clients and prioritizing project development within the Trier region.

## 3. Study Area

The primary study area for this proof-of-concept is the city of **Trier**, located in Rhineland-Palatinate, Germany.

## 4. Methodology & Workflow

This project followed a structured GIS workflow, broken down into five main phases:

### Phase 1: Project Setup & Initial Planning

1.  **Define Study Area:** Established **Trier, Germany** as the specific area of interest.
2.  **Choose GIS Software:** Utilized **QGIS** as the primary software for all spatial processing and mapping.
3.  **Set up Project Directory:** Created a structured folder system (`Data/Raw_Data`, `Data/Processed_Data`, `Maps_Layouts`, `Reports_Notes`, `GIS_Project_Files`) for efficient data management.

### Phase 2: Data Acquisition

1.  **Building Footprints:**
    * **Source:** OpenStreetMap (OSM) via Overpass Turbo.
    * **Method:** Queried and downloaded all `building=*` geometries within the Trier bounding box.
    * **Output:** `trier_buildings.geojson`.
2.  **Digital Surface Model (DSM):**
    * **Source:** State Office for Surveying and Geobasisinformation Rhineland-Palatinate (LVermGeo RLP) Open Data Portal.
    * **Method:** Downloaded high-resolution Digital Surface Model (DOM) tiles covering Trier.
    * **Output:** `.tif` raster files.
3.  **Solar Irradiance Data:**
    * **Source:** PVGIS (Photovoltaic Geographical Information System) from the European Commission.
    * **Method:** Configured PVGIS to provide **Monthly Averages** of `H(h)_m` (Irradiation on horizontal plane) in kWh/m²/month for Trier, covering years 2005-2023.
    * **Output:** `PVGIS_monthly_trier.csv`.
4.  **Commercial/Industrial Land Use Data (for Filtering):**
    * **Source:** OpenStreetMap (OSM) via Overpass Turbo.
    * **Method:** Queried `landuse=commercial` and `landuse=industrial` polygons within Trier.
    * **Output:** `trier_landuse_commercial_industrial.geojson`.

### Phase 3: Data Pre-processing & Preparation in QGIS

1.  **Load Raw Data:** Imported all downloaded `.geojson` and `.tif` files into the QGIS project.
2.  **Coordinate Reference System (CRS) Management:**
    * Set QGIS project CRS to **EPSG:25832 (ETRS89 / UTM zone 32N)**.
    * Explicitly reprojected all vector and raster layers to `EPSG:25832` to ensure accurate alignment and calculations.
3.  **Fix Geometries:** Applied the **"Fix geometries"** tool (Processing Toolbox) to the reprojected buildings layer to correct any topological errors, ensuring valid polygons for analysis.
    * **Output:** `trier_buildings_FIXED.geojson`.
4.  **Filter Commercial & Industrial Buildings:**
    * **Method:** Used the **"Select by location"** tool (Processing Toolbox) to select buildings from `trier_buildings_FIXED` that `are within` the `trier_landuse_commercial_industrial` polygons.
    * **Output:** Saved selected features as `trier_ci_buildings_final.geojson`.

### Phase 4: Core Analysis: Simplified Solar Potential Calculation (in QGIS)

1.  **Calculate Building Footprint Area:**
    * **Method:** Used the **Field Calculator** to add `Footprint_Area_sqm` (in m²) to `trier_ci_buildings_final`.
2.  **Derive Annual Global Horizontal Irradiation (GHI):**
    * **Method:** Summed the `H(h)_m` column from the PVGIS CSV (all 12 months) to get total annual GHI in kWh/m²/year. The average from 2005-2023 was calculated as **`1146.55 kWh/m²/year`**.
    * **Integration:** Converted this to Wh/m²/year (`1,146,550 Wh/m²/year`) and added it as a new attribute field (`Annual_GHI_Wh_m2`) to every building using the Field Calculator.
3.  **Estimate Usable Roof Area:**
    * **Method:** Used the **Field Calculator** to create a new field `Usable_Roof_Area_sqm`.
    * **Expression:** `"Footprint_Area_sqm" * 0.7` (assuming 70% of footprint is usable).
4.  **Calculate Estimated Annual Energy Potential:**
    * **Method:** Used the **Field Calculator** to create the final output field `Annual_Energy_kWh`.
    * **Expression:** `"Usable_Roof_Area_sqm" * "Annual_GHI_Wh_m2" * 0.18 / 1000` (incorporating 18% panel efficiency and converting Wh to kWh).
    * **Output:** The final processed layer, `annual_energy_commercial.gpkg`, containing the `Annual_Energy_kWh` attribute for each building.

### Phase 5: Visualization & Map Production in QGIS

1.  **Symbolize Results:** Applied **"Graduated"** symbology to `annual_energy_commercial.gpkg` using `Annual_Energy_kWh`, with clear class breaks and a sequential color ramp (light reddish-orange to dark purple).
2.  **Add Basemap:** Integrated an **OSM Standard basemap** for geographic context.
3.  **Create Print Layout:** Designed a professional map layout in QGIS's Print Layout Manager.
4.  **Cartographic Elements:** Added essential map elements including a main map view, multiple inset maps, title ("Trier Solar Potential Map: Commercial & Industrial Rooftops"), legend ("Estimated Annual Solar Energy Potential (kWh/year)"), scale bar, North arrow, and full attribution (Data Sources, Analysis by).
5.  **Export Map:** Exported the final map layout as a high-resolution image.

## 5. Data Sources

The following types of data were utilized in this project:

* **Building Footprints & Land Use:** OpenStreetMap (OSM) via Overpass Turbo
* **Digital Surface Model (DSM):** State Office for Surveying and Geobasisinformation Rhineland-Palatinate (LVermGeo RLP)
* **Solar Irradiance Data:** PVGIS (Photovoltaic Geographical Information System) - European Commission

## 6. Tools & Technologies

* **GIS Software:** QGIS (including its integrated Processing Toolbox)
* **Data Sourcing Tools:** Overpass Turbo, PVGIS website
* **Version Control:** Git / GitHub

## 7. How to Explore This Project

To explore this project:
1.  Clone this repository to your local machine.
2.  Download the raw data as described in **Phase 2** (the `.tif` files are too large for GitHub).
3.  Open the QGIS project file (will be added here upon project finalization) located in `GIS_Project_Files/`.
4.  Load the processed layers from `Data/Processed_Data/` to view the analysis results.

## 8. Contact

Feel free to reach out with any questions or feedback!

Enes Gültekin
www.linkedin.com/in/enes-gültekin
