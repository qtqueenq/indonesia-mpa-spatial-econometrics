# Fishing Vessel Detections in Indonesian Marine Protected Areas (R)

A spatial analysis in R that joins satellite vessel detections to Indonesian Marine Protected Areas (MPAs) and regresses detection counts on reserve characteristics.

> **Course project for ECON 425 (Fall 2025).** The research question, datasets and overall workflow were provided by the instructor as a guided assignment. I ran the point-in-polygon analysis and regression in R, interpreted the results, and wrote up the limitations below. [Add anything you extended or changed.]

**Report:** `index.html` | **Code:** `script/` | **Data:** see Data and sources

## Question

Do some kinds of Marine Protected Areas see fewer fishing vessels than others? Specifically, how do reserve size, age and strict protection status relate to the number of fishing vessels detected inside each reserve?

## Data and sources

- **Vessel detections:** satellite radar (SAR) detections for May 2025, 213,368 observations in the monthly file. Source: [add source, e.g. Global Fishing Watch, and license].
- **Marine Protected Areas:** a geopackage of 102 Indonesian MPAs with 32 attributes, including area, year established and IUCN management category. Source: [add source].
- Check each source's license before committing raw files to a public repo, and link to the original if you can't redistribute.

## Method

1. Filtered detections to Indonesia's bounding box (latitude -14 to 8, longitude 92 to 142) and to vessels with a machine-learning fishing score above 0.5, meaning the model considers them likely fishing vessels.
2. Converted the points to simple-features objects (`sf`, WGS 84).
3. Point-in-polygon join with `st_intersects()` to count detected vessels inside each MPA (`pt_count`).
4. Coded `strict.protection = 1` for IUCN categories Ia, II and III, and 0 otherwise.
5. Estimated OLS:

```
pt_count = b0 + b1*GIS_M_AREA + b2*STATUS_YR + b3*strict.protection + error
```

## Results (N = 102)

| Variable | Coefficient | Std. error | p-value |
|---|---|---|---|
| Intercept | 4.059 | 1.680 | 0.018 |
| Area (`GIS_M_AREA`) | 0.00097 | 0.00017 | < 0.001 |
| Year established (`STATUS_YR`) | -0.0014 | 0.0009 | 0.118 |
| Strict protection | -4.532 | 2.197 | 0.042 |

Adjusted R-squared = 0.235, residual standard error = 6.71, F = 11.37 (p < 0.001).

**Takeaways**

- Larger reserves have significantly more detected fishing vessels, which is consistent with larger areas being harder to patrol (and simply having more room for boats).
- Strictly protected reserves have about 4.5 fewer detected vessels on average (p = 0.042). This is an association and does not show that strict protection causes the difference.
- Year established is not statistically significant at conventional levels (p = 0.118), so I do not draw a conclusion about older reserves.

## Limitations

- The outcome is detected vessels classified as likely fishing boats, not confirmed illegal fishing. Some reserve categories allow fishing.
- One month of data, so seasonality and one-off events matter.
- Raw counts rise mechanically with area; a density measure (vessels per square km) would be cleaner.
- Counts are skewed, and the largest residual is 34 vessels, so OLS may be a poor fit. A count model (Poisson or negative binomial) and robust standard errors are better choices.
- Nearby reserves are not independent, and I did not test for spatial autocorrelation (e.g., Moran's I).
- Strictness and age are correlated with location and management, so omitted variables are likely.

## Possible extensions

- Add more months and use a vessel-density outcome.
- Compare OLS with a negative binomial model.
- Test the residuals for spatial autocorrelation.

## Tools

R: `sf`, `terra`, `rnaturalearth`, `tidyverse` (`dplyr`, `ggplot2`, `readr`).
