# Germany Political Heatmap

An interactive dashboard for visualizing citizen complaints across Germany. Built for **Hackathon 2025 (Challenge 2: Politische Heatmap)**, it helps policymakers explore where issues occur, which topics dominate a region, and how demographics relate to reported concerns.

The app combines official German administrative boundaries with complaint data to produce choropleth heatmaps at **state** or **municipality** level, optional point markers, and rich sidebar filters.

---

## Features

- **Choropleth heatmap** — Color-coded regions by complaint volume (yellow → orange → red)
- **Two zoom levels** — Federal states (16) or municipalities (~11,000 boundaries)
- **Interactive filters** — Date range, category, age group, gender, origin, state, and responsible entity level
- **Issue markers** — Clustered map pins with category icons and detailed popups (toggle on/off)
- **Municipality search** — Find any municipality directly on the map
- **Drill-down panel** — Select a state or municipality, then browse issues by category in the sidebar
- **Most common issue overlay** — Optional tooltip layer showing the dominant category per state
- **Static export** — Generate standalone HTML maps without running Streamlit

---

## Tech Stack

| Layer | Tools |
|-------|-------|
| Dashboard | [Streamlit](https://streamlit.io/) |
| Mapping | [Folium](https://python-visualization.github.io/folium/) / Leaflet |
| Geospatial data | [GeoPandas](https://geopandas.org/) |
| Data processing | [Pandas](https://pandas.pydata.org/) |
| Streamlit ↔ Folium bridge | [streamlit-folium](https://github.com/randyzwitch/streamlit-folium) |

---

## Dataset Overview

The project ships with sample data in the `Data/` folder:

| File | Description |
|------|-------------|
| `complete_issues_data.csv` | Full complaint records (~993 issues, 2023–2025) |
| `daily_aggregated.csv` | Pre-aggregated counts by day and location |
| `weekly_aggregated.csv` | Pre-aggregated counts by calendar week |
| `monthly_aggregated.csv` | Pre-aggregated counts by month |
| `VG5000_LAN.shp` | Official German federal state boundaries |
| `VG5000_GEM.shp` | Official German municipality boundaries |
| `VG5000_KRS.shp` | Official German district (Kreis) boundaries |

**Complaint categories:** Umwelt, Bildung, Verkehr, Digitalisierung, Sicherheit, Gesundheit, Wirtschaft, Migration

**Key fields in the complete dataset:**

- **Location:** `latitude`, `longitude`, `municipality`, `district`, `state`
- **Time:** `timestamp`, `date`, plus derived fields (`hour`, `day_of_week`, etc.)
- **Demographics:** `age_group`, `gender`, `origin`
- **Content:** `issue_id`, `description` (German text), `category`

Administrative boundary shapefiles are sourced from the official German VG5000 dataset and reprojected to WGS84 (`EPSG:4326`) for web mapping.

---

## Prerequisites

Before you begin, make sure you have:

- **Python 3.10 or newer** (3.11+ recommended)
- **pip** (Python package manager)
- **Git** (to clone the repository)
- A modern web browser (Chrome, Firefox, Safari, or Edge)

GeoPandas depends on native libraries for reading shapefiles. On most systems, `pip install geopandas` is enough. If installation fails, see [Troubleshooting](#troubleshooting) below.

---

## Local Setup

### 1. Clone the repository

```bash
git clone https://github.com/habibaloai/Politech-Heatmap.git
cd Politech-Heatmap
```

### 2. Create a virtual environment (recommended)

Using a virtual environment keeps project dependencies isolated from your system Python.

**macOS / Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

**Windows (Command Prompt):**

```cmd
python -m venv .venv
.venv\Scripts\activate.bat
```

**Windows (PowerShell):**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

You should see `(.venv)` at the start of your terminal prompt when the environment is active.

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Run the interactive dashboard

```bash
streamlit run finalapp.py
```

Streamlit will print a local URL in the terminal, typically:

```
Local URL: http://localhost:8501
```

Open that URL in your browser. The dashboard loads automatically.

To stop the server, press `Ctrl+C` in the terminal.

---

## Using the Dashboard

### Sidebar filters

| Control | What it does |
|---------|--------------|
| **Heatmap Granularity** | Switch between **State** and **Municipality** choropleth views |
| **Date range** | Limit complaints to a specific period |
| **Category** | Filter by topic (e.g. Verkehr, Bildung) |
| **Age Group / Gender / Origin** | Demographic filters |
| **State** | Restrict data to selected federal states |
| **Entity Level** | Filter by responsible administrative level |
| **Show Markers** | Toggle clustered issue pins on the map |
| **Show Most Common Issue per State** | Add a tooltip layer with the top category per state |

### Map interactions

- **Hover** over a region to see its name and issue count
- **Search** for a municipality using the search box on the map
- **Click markers** to read the full complaint description in a popup
- Use the **layer control** (top-right of the map) to show or hide layers

### View Issues panel

1. Choose **State** or **Municipality** granularity
2. Select a location from the dropdown
3. Pick an issue category to list matching complaints in the sidebar

Filters apply everywhere — the map, markers, and issue list all update together.

---

## Static HTML Maps (no server)

If you only need a shareable map file and do not want to run Streamlit:

```bash
python holy.py
```

This generates two files in the project root:

- `germany_issues_choropleth.html` — State-level heatmap with tooltips
- `germany_issues_markers.html` — State boundaries with clustered issue markers

Open either file directly in your browser (double-click or drag into a browser window).

---

## Project Structure

```
Hackathon25/
├── finalapp.py              # Main Streamlit dashboard (start here)
├── holy.py                  # Static HTML map generator
├── basicmapp.py             # Minimal Folium example
├── datat.py                 # Data exploration / inspection script
├── requirements.txt         # Python dependencies
├── README.md                # This file
├── Data/
│   ├── complete_issues_data.csv
│   ├── daily_aggregated.csv
│   ├── weekly_aggregated.csv
│   ├── monthly_aggregated.csv
│   ├── VG5000_LAN.shp       # State boundaries (+ .dbf, .shx, .prj, .cpg)
│   ├── VG5000_GEM.shp       # Municipality boundaries
│   ├── VG5000_KRS.shp       # District boundaries
│   └── README_challenge_2.md  # Original hackathon brief
├── germany_issues_choropleth.html   # Generated (after running holy.py)
└── germany_issues_markers.html      # Generated (after running holy.py)
```

---

## Troubleshooting

### `ModuleNotFoundError` when running the app

Make sure your virtual environment is activated and dependencies are installed:

```bash
source .venv/bin/activate          # macOS / Linux
python -m pip install -r requirements.txt
```

### GeoPandas / shapefile install errors

Try installing GeoPandas with bundled dependencies:

```bash
python -m pip install geopandas pyogrio pyproj shapely
```

On **macOS** with Homebrew, you may also need:

```bash
brew install gdal
```

On **Ubuntu/Debian**:

```bash
sudo apt-get install libgdal-dev
```

### Map is blank or regions have no color

- Run the app from the **project root** so relative paths like `Data/VG5000_LAN.shp` resolve correctly
- Confirm all shapefile companion files (`.dbf`, `.shx`, `.prj`) are present alongside each `.shp`

### Port 8501 already in use

Run Streamlit on a different port:

```bash
streamlit run finalapp.py --server.port 8502
```

### Slow load on municipality view

The municipality layer contains thousands of polygons. Initial load may take a few seconds depending on your machine. Use **State** granularity for faster performance during development.

---

## Development Notes

- Shapefile datetime columns (`BEGINN`, `WSK`) are converted to strings before GeoJSON export to avoid serialization errors
- Municipality names are matched case-insensitively between the CSV and shapefile `GEN` field
- Marker icons use Folium's named color palette; popup badges use custom hex colors per category

---

## Hackathon Context

This project was built for **Challenge 2: Politische Heatmap – Problemvisualisierung mit Filterfunktion**. The goal is to give politicians and analysts a visual tool to:

- Spot regional hotspots of citizen concern
- Compare issue types across states and municipalities
- Filter by time and demographics to uncover patterns
- Drill into individual complaints for context

See `Data/README_challenge_2.md` for the original challenge description and evaluation criteria.

---

## License

Data shapefiles follow the terms of the original VG5000 source. Application code is provided as-is for hackathon and educational use. Add your preferred license file (e.g. MIT) before public release if required.

---

## Contributing

Issues and pull requests are welcome. To contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-improvement`)
3. Commit your changes with a clear message
4. Push to your fork and open a pull request

Please keep changes focused and test locally with `streamlit run finalapp.py` before submitting.
