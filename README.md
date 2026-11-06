# Serik (Antalya) Hourly Weather: Exploratory Data Analysis

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/CavitDursun07/serik_weather_EDA/blob/main/Weather_Antalya_Serik_EDA.ipynb)

An exploratory data analysis of about seven years of hourly weather observations for Serik, Antalya. The notebook checks data quality, profiles each variable, and then looks at how temperature, humidity, pressure, wind, cloud, visibility, UV and precipitation relate to each other across years, months and hours of the day.

## Dataset

| | |
|---|---|
| Source file | `weather_antalya_serik_2017&2023.csv` (**not included in this repository**) |
| Granularity | One row per hourly observation |
| Size | 60,240 rows × 32 columns |
| Period | January 2017 – 16 November 2023 (2023 is a partial year) |
| Missing values / duplicates | None |

**Column groups**

- **Time:** `time`, `is_day`
- **Temperature:** `temp_c/f`, `feelslike_c/f`, `windchill_c/f`, `heatindex_c/f`, `dewpoint_c/f`
- **Wind:** `wind_mph/kph`, `wind_degree`, `wind_dir`, `gust_mph/kph`
- **Precipitation:** `precip_mm/in`, `will_it_rain`, `will_it_snow`, `chance_of_rain`, `chance_of_snow`
- **Atmosphere:** `humidity`, `cloud`, `pressure_mb/in`, `vis_km/miles`, `uv`
- **Condition:** `condition` (text label such as Sunny, Clear, Light rain)

A full column dictionary is at the top of the notebook.

## Notebook Structure

1. **Import Libraries and Load Data**
2. **Initial Overview:** shape, dtypes, missing values, duplicates, binary column check
3. **Summary Statistics:** `describe()`, plausibility checks, correlation heatmap
4. **Univariate Analysis:** histograms, boxplots, category counts, condition pie chart
5. **Bivariate / Multivariate Analysis:** the strongest correlations checked with scatter plots
6. **Yearly:** year-by-year comparison of temperature, humidity, wind, cloud and precipitation
7. **Monthly:** seasonal cycle, month-by-year boxplots, precipitation by month
8. **Daily:** hour-of-day cycle, hour × month heatmaps (temperature, UV), circular mean of wind direction
9. **Outlier Detection:** IQR method, with a closer look at wind-speed outliers
10. **Relationship Exploration:** wind direction groups, weather conditions, cloud base height, seasonal wind polar plots
11. **Summary**

## Data Cleaning Notes

- `time` is parsed as `datetime64`.
- `is_day` is converted from float to boolean.
- `will_it_rain`, `will_it_snow`, `chance_of_rain` and `chance_of_snow` contain only zeros and are dropped from the working DataFrame. A full copy is kept in `df_backup`.
- Unit-duplicate columns (°C/°F, mph/kph, mb/in) correlate at about 0.99–1.00. They are kept and the analysis uses the metric versions.
- IQR outliers in `precip_mm`, `vis_km` and `cloud` come from skewed or capped distributions, not data errors. Strong-wind outliers are genuine events and are kept.

### Derived features

| Feature | Definition |
|---|---|
| `year`, `month`, `day`, `hour` | Extracted from `time` |
| `wind_dir_group` | 16 compass labels grouped into N / E / S / W |
| `condition_group` | Conditions simplified into Clear / Cloudy / Rainy / Other |
| `temp_dewpoint_diff` | `temp_c - dewpoint_c` (dryness proxy) |
| `gust_wind_ratio` | `gust_kph / wind_kph` |
| `cloud_base_m` | `temp_dewpoint_diff × 125` (rule-of-thumb estimate, **daytime hours only**) |
| `season` | Winter / Spring / Summer / Autumn from month |

Cloud base is restricted to daytime because nighttime surface inversions shrink the temperature–dewpoint gap and produce unrealistically low estimates.

## Key Findings

**Climate.** The profile is a textbook Mediterranean one. Summers are hot and dry (July/August mean about 30 °C, humidity about 49%, essentially no rain June–September in every year). Winters are mild and wet (January mean about 11 °C, humidity about 65%, most rain falling November–March). `Sunny` and `Clear` make up about 79% of all hours. Year-to-year variation is small. 2020 had more cloud, and January 2019 had one extreme rain month (693 mm versus a typical month under 300 mm).

**Diurnal sea/land breeze.** Average wind direction flips by about 180° over the day: northerly (land breeze) overnight and southerly (sea breeze) from about 10:00 to 17:00. Wind speed peaks in the same afternoon window.

**Wind direction matters more than wind speed.** Strong southerly wind is the "wet" regime: humidity rises (56% → 80%), cloud cover rises (20% → 90%) and rain appears. Strong N/E/W wind is the "dry, cold" regime: humidity and cloud stay flat or fall, there is almost no rain, and wind chill is visible. Seven of the ten most extreme wind days were dry northerly events.

**Pressure.**
- Pressure vs dewpoint is the tightest non-duplicate relationship (r ≈ -0.65).
- Pressure vs temperature is r ≈ -0.61.
- Precipitation only occurs below about 1025 mb, and the heaviest rain (>10 mm) clusters at 1000–1013 mb.
- Wind is strongest at both pressure extremes and calmest in the mid-range.

**Ceiling effects, not linear trends.**
- Precipitation and cloud cover limit the *maximum* visibility rather than lowering it linearly.
- Humidity is necessary but not sufficient for cloud.
- Heavy rain needs both high humidity (>85%) and high cloud cover.
- High UV (8–10) occurs only under clear skies.

**Cloud base height.** Summer averages are the highest (about 1,500–2,000 m, peak about 2,176 m). Winter averages are the lowest, and some winter mornings drop to 0–250 m, which indicates fog risk.

**Seasonal wind.** Winter and spring wind is bimodal (N/NNE and S/SSW) and reaches the highest speeds (>40 kph). Summer wind is calmer and concentrated in the southerly sector.

## Techniques and Charts Used

- **Charts:** histogram, boxplot, violin, bar (simple, grouped, stacked), pie, scatter, hexbin, joint plot, line, heatmap, polar
- **Analysis:** correlation analysis, groupby aggregations, IQR outlier detection, circular mean for wind direction, rule-of-thumb derived features

## How to Run

1. Open the notebook in Colab with the badge above, or clone the repo and open it in Jupyter.
2. Install the dependencies:
   ```bash
   pip install pandas numpy matplotlib seaborn
   ```
3. Put `weather_antalya_serik_2017&2023.csv` where the first code cell expects it. The CSV is not in the repository.
4. Use **Runtime → Run all** (or **Kernel → Restart & Run All**). Later cells depend on columns and helper functions created earlier, so running cells out of order or after a kernel restart causes `KeyError`/`NameError`.

## Author

Cavit Konuralp Dursun: [GitHub](https://github.com/CavitDursun07)
