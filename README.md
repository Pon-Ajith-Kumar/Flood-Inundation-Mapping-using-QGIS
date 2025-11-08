
---
# **Spatio-Temporal Flood Inundation Simulation and 3D Visualization using QGIS & PostGIS**

*A comprehensive, end-to-end geospatial workflow that integrates Digital Elevation Models, raster-based flood simulation, temporal 2D and 3D visualization in QGIS, and vector-based spatial analysis in PostGIS to model, animate, quantify, and evaluate flood inundation patterns across the Brahmaputra Basin.*

---

# **1. Introduction**

Flooding is one of the most destructive natural disasters worldwide.
Accurate simulation and visualization of flood propagation helps in:

* Risk assessment
* Disaster preparedness
* Infrastructure planning
* Terrain vulnerability analysis

This project presents a **complete spatio-temporal GIS workflow** for simulating flood levels using elevation data, animating flood rise in **2D and 3D**, and performing analytical vector-based flood assessment using **PostGIS**.

The workflow includes:

✅ **Workflow 1 — QGIS Flood Simulation (Raster-based)** <br />
✅ **Workflow 2 — PostGIS Flood Analysis (Vector-based)**

These combined methods enable both **visual** and **analytical** flooding insights.

# Directory Structure

```plaintext
📦 Flood-Simulation-3D-PostGIS
│
├── data/
│   ├── dem/
│   │   └── assam_dem.tif                # Raw DEM downloaded from OpenTopography
│   │
│   ├── rasters/
│   │   ├── flood_30.tif
│   │   ├── flood_45.tif
│   │   ├── flood_50.tif
│   │   ├── ...                          # All raw flood rasters
│   │   └── flood_2930.tif
│   │
│   ├── smooth_rasters/
│   │   ├── flood_30_smooth.tif
│   │   ├── flood_45_smooth.tif
│   │   ├── ...                          # All smoothed flood rasters
│   │   └── flood_2930_smooth.tif
│   │
│   ├── vectors/
│   │   ├── flood_30.gpkg
│   │   ├── flood_45.gpkg
│   │   ├── flood_50.gpkg
│   │   ├── ...                          # All polygonized flood layers
│   │   └── flood_2930.gpkg
│   │
│   └── qgis_project/
│       └── flood_simulation.qgz         # Main QGIS .qgz project file
│
├── animations/
│   ├── 2d/
│   │   └── flood_2d_animation.mp4
│   ├── 3d/
│   │   └── flood_3d_animation.mp4
│   └── frames/
        └── frame_0001.png               # Optional: animation export frames

```

---

# **2. Software and Data Used**

| Component                | Description                                                                   |
| ------------------------ | ----------------------------------------------------------------------------- |
| **QGIS**                 | Used for raster handling, flood mapping, temporal animation, 3D visualization |
| **PostgreSQL + PostGIS** | Spatial database for storing and querying flood polygons                      |
| **GRASS / GDAL Plugins** | Used for raster smoothing and conversions                                     |
| **DEM Source**           | OpenTopography (Brahmaputra Basin)                                            |
| **DEM Elevation Range**  | 25 m (min) to 3055 m (max)                                                    |

---

# **3. Workflow 1 — QGIS Flood Simulation (Raster-Based)**

This section explains the complete raster workflow implemented in QGIS.

---

## **3.1 Select Area of Interest (Brahmaputra Basin)**

1. Open QGIS → New Project.
2. In **Browser → XYZ Tiles**, drag **OpenStreetMap**.
3. Locate the **Brahmaputra River Basin**.
4. Use this as visual reference for DEM loading.

---

## **3.2 Download + Load DEM**

1. Open: [https://opentopography.org](https://opentopography.org)
2. Select DEM (SRTM / ALOS / NASADEM).
3. Download in **GeoTIFF** format.
4. Import via:

```
Layer → Add Layer → Add Raster Layer
```

You will now see the terrain grid.

---

## **3.3 Check DEM Elevation Range + Choose Flood Levels**

Using:

```
Raster → Miscellaneous → Information
```

or Identify tool.

Elevation range observed:

* **Min = ~25 m**
* **Max = ~3055 m**

Flood simulation levels selected (examples):

```
30, 45, 50, 55, 60, …, 130, 230, 330, …, 2930 m
```

Each represents a hypothetical flood stage.

---

## **3.4 Create Binary Flood Rasters**

For each level:

1. Open **Raster Calculator**
2. Use expression:

```
"dem@1" <= LEVEL
```

Example:

```
"dem@1" <= 30
```

This produces:

```
1 = flooded
0 = dry
```

Repeat for all levels → **flood_30.tif**, **flood_45.tif**, etc.

---

## **3.5 Smooth Raster Edges for Realism**

Use GRASS GIS:

```
Processing Toolbox → GRASS → r.neighbors
```

Parameters:

* Method: **average**
* Size: **5×5**
* Output: `flood_30_smooth.tif`

Repeat for all flood levels.

---

## **3.6 Apply Flood Styling**

Per layer:

* Symbology → **Singleband pseudocolor**
* Color ramp → **Blues**
* Darker blue for deeper water

Arrange layers bottom → top in ascending water levels.

---

## **3.7 Setup 3D Flood Visualization**

Steps:

1. View → **New 3D Map View**
2. Base Elevation → **DEM**
3. Vertical exaggeration → **15–20**
4. Add flood rasters in order
5. Adjust light, camera, rotation

Save `.qgz` file.

---

## **3.8 Add Temporal Controls (Flood Animation)**

1. Right-click flood raster → **Properties → Temporal**
2. Enable:

```
Dynamic Temporal Control
```

3. Assign:

* Start time
* End time
* Duration

4. Use **Temporal Controller** (toolbar) to animate layers.

---

# **4. Temporal Allocation Table**

This is the exact temporal schedule for flood layer animation.

| Flood Layer | Elevation (m) | Start Time | End Time | Duration (s) |
| ----------- | ------------- | ---------- | -------- | ------------ |
| flood_30    | 30            | 00:00:00   | 00:00:08 | 8            |
| flood_45    | 45            | 00:00:08   | 00:00:16 | 8            |
| flood_50    | 50            | 00:00:16   | 00:00:24 | 8            |
| flood_55    | 55            | 00:00:24   | 00:00:32 | 8            |
| flood_60    | 60            | 00:00:32   | 00:00:40 | 8            |
| flood_65    | 65            | 00:00:40   | 00:00:48 | 8            |
| flood_70    | 70            | 00:00:48   | 00:00:56 | 8            |
| flood_75    | 75            | 00:00:56   | 00:01:04 | 8            |
| flood_80    | 80            | 00:01:04   | 00:01:12 | 8            |
| flood_85    | 85            | 00:01:12   | 00:01:20 | 8            |
| flood_90    | 90            | 00:01:20   | 00:01:28 | 8            |
| flood_95    | 95            | 00:01:28   | 00:01:36 | 8            |
| flood_100   | 100           | 00:01:36   | 00:01:44 | 8            |
| flood_105   | 105           | 00:01:44   | 00:01:52 | 8            |
| flood_110   | 110           | 00:01:52   | 00:02:00 | 8            |
| flood_115   | 115           | 00:02:00   | 00:02:08 | 8            |
| flood_120   | 120           | 00:02:08   | 00:02:16 | 8            |
| flood_125   | 125           | 00:02:16   | 00:02:24 | 8            |
| flood_130   | 130           | 00:02:24   | 00:02:32 | 8            |
| flood_230   | 230           | 00:02:32   | 00:02:37 | 5            |
| flood_330   | 330           | 00:02:37   | 00:02:42 | 5            |
| flood_430   | 430           | 00:02:42   | 00:02:47 | 5            |
| flood_530   | 530           | 00:02:47   | 00:02:52 | 5            |
| flood_630   | 630           | 00:02:52   | 00:02:57 | 5            |
| flood_730   | 730           | 00:02:57   | 00:03:02 | 5            |
| flood_830   | 830           | 00:03:02   | 00:03:07 | 5            |
| flood_930   | 930           | 00:03:07   | 00:03:12 | 5            |
| flood_1030  | 1030          | 00:03:12   | 00:03:14 | 2            |
| flood_1130  | 1130          | 00:03:14   | 00:03:16 | 2            |
| flood_1230  | 1230          | 00:03:16   | 00:03:18 | 2            |
| flood_1330  | 1330          | 00:03:18   | 00:03:20 | 2            |
| flood_1430  | 1430          | 00:03:20   | 00:03:22 | 2            |
| flood_1530  | 1530          | 00:03:22   | 00:03:24 | 2            |
| flood_1630  | 1630          | 00:03:24   | 00:03:26 | 2            |
| flood_1730  | 1730          | 00:03:26   | 00:03:28 | 2            |
| flood_1830  | 1830          | 00:03:28   | 00:03:30 | 2            |
| flood_1930  | 1930          | 00:03:30   | 00:03:32 | 2            |
| flood_2030  | 2030          | 00:03:32   | 00:03:34 | 2            |
| flood_2130  | 2130          | 00:03:34   | 00:03:36 | 2            |
| flood_2230  | 2230          | 00:03:36   | 00:03:38 | 2            |
| flood_2330  | 2330          | 00:03:38   | 00:03:40 | 2            |
| flood_2430  | 2430          | 00:03:40   | 00:03:42 | 2            |
| flood_2530  | 2530          | 00:03:42   | 00:03:44 | 2            |
| flood_2630  | 2630          | 00:03:44   | 00:03:46 | 2            |
| flood_2730  | 2730          | 00:03:46   | 00:03:48 | 2            |
| flood_2830  | 2830          | 00:03:48   | 00:03:50 | 2            |
| flood_2930  | 2930          | 00:03:50   | 00:03:52 | 2            |
---

# **5. Exporting 2D and 3D Animations**

## **5.1 Export 2D Flood Animation**

1. Open **Temporal Controller**
2. Click **Play**
3. Export:

```
Temporal Controller → Export Animation
```

Outputs:

* `.mp4`
* Image sequence (`.png`)

---

## **5.2 Export 3D Flood Animation**

1. Open **3D Map View**
2. Activate temporal playback
3. Export:

```
3D View → Capture → Export Animation
```

---

# **6. Preparing Vector Layers for PostGIS (Polygonization)**

Convert each smoothed raster to vector polygons:

```
Raster → Conversion → Polygonize
```

Save as:

```
flood_30.gpkg, flood_45.gpkg, ..., flood_2930.gpkg
```

---

# **7. Workflow 2 — PostGIS Flood Analysis**

---

## **7.1 Import Flood Layers into PostGIS**

In QGIS:

1. Open **DB Manager**
2. Select PostgreSQL → your database
3. Click **Import Layer/File**
4. Select each `.gpkg`

Assign:

```
Table name: flood_30, flood_45, flood_50, ..., flood_2930
Geometry column: geom
```

Each flood stage becomes a separate table.

---

## **7.2 Table Structure**

Each table has:

| Field | Type    | Meaning              |
| ----- | ------- | -------------------- |
| fid   | Integer | Unique polygon ID    |
| water | Integer | 1 = flooded, 0 = dry |
| geom  | Polygon | Flood geometry       |

---

## **7.3 Add Water Level Attribute**

Example:

```sql
ALTER TABLE flood_30 ADD COLUMN water_level INTEGER;
UPDATE flood_30 SET water_level = 30;
```

Repeat for all tables.

---

## **7.4 Merge All Tables into One**

```sql
CREATE TABLE flood_all AS
SELECT fid, water, geom, water_level FROM flood_30
UNION ALL
SELECT fid, water, geom, water_level FROM flood_45
UNION ALL
...
SELECT fid, water, geom, water_level FROM flood_2930;
```

Now you have **one master table**.

---

## **7.5 Create Spatial Index**

```sql
CREATE INDEX flood_all_geom_idx ON flood_all USING GIST (geom);
```

Improves speed dramatically.

---

## **7.6 Key SQL Queries**

---

### **7.6.1 Flooded Area per Water Level**

```sql
SELECT 
  water_level,
  ROUND(SUM(ST_Area(geom)) / 1000000.0, 2) AS flooded_area_sqkm
FROM flood_all
WHERE water = 1
GROUP BY water_level
ORDER BY water_level;
```

---

### **7.6.2 Total Flooded Area**

```sql
SELECT 
  ROUND(SUM(ST_Area(geom)) / 1000000.0, 2) AS total_flooded_area_sqkm
FROM flood_all
WHERE water = 1;
```

---

### **7.6.3 Overlap Between Two Flood Levels**

```sql
SELECT 
  a.water_level AS low_level,
  b.water_level AS high_level,
  ROUND(ST_Area(ST_Intersection(a.geom, b.geom)) / 10000.0, 2) AS overlap_area_ha
FROM flood_30 a, flood_130 b
WHERE ST_Intersects(a.geom, b.geom);
```

---

# **8. Visualizing PostGIS Flood Layers in QGIS**

### **8.1 Load Table**

* Open QGIS
* Browser → PostgreSQL
* Drag `flood_all` into canvas

### **8.2 Style by Water Level**

1. Symbology → **Graduated**
2. Column → `water_level`
3. Color ramp → **Blues**

### **8.3 Filtering Examples**

Small floods:

```
"water_level" < 200
```

Medium:

```
water_level BETWEEN 200 AND 1000
```

Large:

```
water_level > 1000
```

---

# **9. Running SQL from QGIS**

1. Tools → **DB Manager**
2. Open **SQL Window**
3. Paste spatial queries
4. Execute
5. Export results as:

* CSV
* GeoPackage
* New PostGIS tables

---

