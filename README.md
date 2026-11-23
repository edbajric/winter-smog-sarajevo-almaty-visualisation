# Winter Smog in Sarajevo and Almaty: A Comparative Visual Analysis

A comparison of hourly PM2.5 pollution in Sarajevo (Bosnia and Herzegovina) and Almaty (Kazakhstan) over the 2025–26 winter, looking at **when** smog peaks and **how weather conditions drive it**.

Built as group coursework for **COS7046-B Big Data Visualisation** (MSc Artificial Intelligence and Machine Learning, University of Bradford, November 2026).

## Key findings

- Both cities exceeded the WHO daily PM2.5 guideline (15 µg/m³) on most winter days: **90% of days in Sarajevo and 99% in Almaty**.
- Average pollution was similar (33.4 vs 31.7 µg/m³), but **Sarajevo's peak was almost twice as high** (173.2 vs 95.9 µg/m³).
- The most dangerous hour in both cities is **20:00**. Almaty also has a second, morning peak around 09:00.
- Sarajevo's pollution is much more strongly linked to the weather. PM2.5 drops from about 50 to 10 µg/m³ as the boundary layer rises, and the worst episodes happen in calm wind. Almaty's levels stay closer to constant.
- The worst hour in the dataset was 22:00 on 27 December in Sarajevo, with light winds, near-freezing temperatures and a very low boundary layer.

These are associations, not proof of causation.

## Data

| | Sarajevo | Almaty |
|---|---|---|
| Coordinates | 43.86°N, 18.41°E | 43.24°N, 76.89°E |
| PM2.5 source | CAMS European model (0.1°, ~11 km) | CAMS Global model (0.4°, ~45 km) |
| Period | 1 Nov 2025 – 28 Feb 2026 | 1 Nov 2025 – 28 Feb 2026 |
| Hours | 2,880 | 2,880 |

The data comes from the [Open-Meteo Air Quality API](https://open-meteo.com/en/docs/air-quality-api) for pollutants and the [Open-Meteo Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api) for weather. Boundary layer height was completely empty in the Air Quality API, so it was taken from the Historical Weather API and merged in. The notebook downloads everything directly, so no API key is needed.

## Preprocessing

- **Quality checks:** looked for missing values and duplicate hours, and checked every value against physical limits (for example, no negative concentrations, and dew point never above air temperature). The data passed every check, so no rows were removed.
- **Mostly-zero variables:** snowfall, precipitation, solar radiation and snow depth were mostly zeros. The zeros are real (dry, snow-free or night hours), so these became yes/no flags.
- **Outliers:** extreme PM2.5 hours (1.5 × IQR) were flagged, not removed, because they are real smog episodes.
- **Overlapping variables:** PM10 and the European AQI were almost identical to PM2.5 (r = 0.981 and 0.947). PM10 was kept only as a PM2.5/PM10 ratio, and AQI only as categories.
- **Skew and scale:** five highly skewed features were log1p-transformed, and then every feature was standardised. Before this, CO alone made up 80.6% of the total variance.
- **PCA:** principal component analysis on the scaled data shows the two cities form clearly separate groups (PC1 = 50.3%, PC2 = 14.5%).

## Visualisations

1. Hourly PM2.5 with a 24-hour rolling mean, compared with the WHO guideline
2. Heatmap of PM2.5 by hour and day
3. PM2.5 against weather conditions (boundary layer height and wind speed)
4. A close look at the worst smog episode
5. Features that move with PM2.5 in each city
6. Average PM2.5 by hour of day (the most dangerous times)
7. Summary comparison

The design follows Munzner's effectiveness principle (position for ordered values, colour for categories, small multiples for comparing the cities), Tufte's advice to use matching scales, and Wilke's guidance on colour scales. The notebook also includes **rejected designs** with explanations: both cities on one chart (Sarajevo's spikes hide Almaty's changes), an uncapped heatmap colour scale, and a misleading 3D bar chart.

## Limitations

- PM2.5 and boundary layer height come from CAMS/ERA5 models, not ground monitoring stations, so they can't show differences between neighbourhoods.
- Grouping weather into fixed ranges can put almost identical conditions into different groups.
- Averaging within groups can hide the sudden peaks that matter most for health.

## Running it

1. Open `BigDataCW1_Alnura_Edina.ipynb` in [Google Colab](https://colab.research.google.com/drive/1xIlxxVZwL3pFVVEI1HxoZZ4ki_AYj9LH) or Jupyter.
2. Run all cells. It only needs `pandas`, `numpy`, `matplotlib`, `scikit-learn` and `requests`.

## Tech stack

Python · pandas · NumPy · Matplotlib · scikit-learn (PCA, StandardScaler) · Open-Meteo REST APIs · Google Colab

## Authors

- Edina Bajric
- Alnura Maxyutova
