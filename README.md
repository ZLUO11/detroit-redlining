# Detroit Redlining Legacy Analysis

**Does a neighborhood's 1939 HOLC grade still predict its residents' income and racial composition today?**

This project quantifies the lasting economic and demographic footprint of the federal redlining policy that systematically denied mortgage access to Detroit neighborhoods based on race, using geospatial data, two U.S. Census APIs, and statistical analysis.

---

## Key Findings

| HOLC Grade | Designation | Avg 2018 Median Income | Avg % Black Residents |
|:---:|---|---:|---:|
| A | Best | ~$65,000 | ~8% |
| B | Still Desirable | ~$52,000 | ~18% |
| C | Definitely Declining | ~$38,000 | ~45% |
| D | Hazardous (Redlined) | ~$30,000 | ~72% |

> **Grade A neighborhoods earn roughly 2× more than Grade D neighborhoods — 80 years after the maps were drawn.**  
> Districts graded D in 1939 have nearly 9× higher Black population share than Grade A districts today.

---

## Project Structure

```
detroit-redlining/
├── detroit_redlining_portfolio.py   # Main pipeline (ETL + analysis + visualisation)
├── redlines_data.json               # Source: University of Richmond DSL
├── redlines_cache_portfolio.json    # Cached enriched data (run once, reuse forever)
├── outputs/
│   ├── 01_holc_map.png              # 1939 HOLC district map
│   ├── 02_income_by_grade.png       # Income distribution boxplot by grade
│   ├── 03_race_income_scatter.png   # % Black residents vs. median income scatter
│   ├── 04_income_stats_bar.png      # Mean/median income bar chart by grade
│   ├── 05_unique_words.png          # Distinctive words in 1939 appraisal descriptions
│   ├── 06_interactive_map.html      # Click-to-explore Folium map
│   └── 07_interactive_scatter.html  # Plotly scatter with trendlines
└── README.md
```

---

## Data Sources

| Source | What | How Accessed |
|---|---|---|
| [University of Richmond DSL](https://dsl.richmond.edu/panorama/redlining/) | 1939 HOLC district boundaries + appraisal text | GeoJSON file |
| [FCC Area API](https://geo.fcc.gov/api/census/) | Census tract lookup by lat/lon | REST API |
| [U.S. Census Bureau ACS 5-Year 2018](https://api.census.gov/data/2018/acs/acs5/) | Median household income (B19013), racial composition (B01003, B02009) | REST API |

---

## Methodology

### 1. Extract
- Parsed 238 HOLC district polygons from GeoJSON, handling both `Polygon` and `MultiPolygon` geometries
- Extracted qualitative appraisal descriptions written by HOLC surveyors in 1939

### 2. Enrich (ETL pipeline)
- **Geographic sampling**: Generated a mesh grid across Detroit (lon: −83.5 to −82.8, lat: 42.1 to 42.6 at 0.004° resolution) and used `matplotlib.path.Path.contains_points()` to sample a random interior point per district
- **Census tract lookup**: Mapped each point to a 2010 census tract via the FCC Area API (238 API calls, with retry logic)
- **Income data**: Pulled all Michigan census tract incomes in a single ACS API call, mapped by FIPS code
- **Racial composition**: Pulled total + Black/AA population per tract, computed percentage

### 3. Cache
- All enriched data serialized to JSON (`redlines_cache_portfolio.json`) — subsequent runs skip API calls entirely

### 4. Analyse
- Income statistics (mean, median) grouped by HOLC grade
- Racial composition averages by grade
- Correlation between % Black residents and median income (linear regression)
- NLP: grade-exclusive distinctive vocabulary from 1939 appraisal text

### 5. Visualise
- Static charts (matplotlib): district map, income boxplot, scatter plot, bar chart, word frequency
- Interactive (Folium + Plotly): hover-enabled map and scatter with per-district drill-down

---

## How to Run

```bash
# Install dependencies
pip install numpy pandas matplotlib requests folium plotly

# First run: fetches Census data (~5 min), caches results
python detroit_redlining_portfolio.py

# Subsequent runs: loads from cache, generates charts in seconds
python detroit_redlining_portfolio.py
```

---

## Business Relevance

This type of analysis has direct applications in:

- **Fintech / Lending**: Detecting modern algorithmic redlining in mortgage approval models
- **Real estate analytics**: Quantifying neighbourhood investment gaps from historical policy
- **Policy & ESG reporting**: Measuring legacy discrimination for fair housing compliance
- **Urban planning**: Identifying areas where targeted investment could close income gaps

---

## Skills Demonstrated

`Python` · `REST API integration` · `JSON parsing` · `ETL pipeline design` · `Geospatial data` · `Census data` · `Statistical analysis` · `Data visualization` · `matplotlib` · `Plotly` · `Folium` · `Pandas` · `NumPy` · `Text analysis` · `Caching strategy`
