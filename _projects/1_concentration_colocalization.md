---
layout: page
title: Concentration & Co-localization
description: Spatial analysis of technological innovation patterns in Japan using patent data
img: assets/img/concentration_japan.jpg
importance: 1
category: research
related_publications: true
github: https://github.com/sebaeza/Concentration-colocalization-Japan
---

This project examines the spatial patterns of inventive activity in Japan, analyzing how technological innovation concentrates and co-localizes across different fields. Using patent data from the Japan Patent Office (1975-2014), we reveal how Japan's innovation geography shifted from a highly integrated system to more specialized, isolated clusters.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/japan_patents_map.jpg" title="Distribution of patents in Japan" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Point distribution of inventors across Japan for two periods: 1975-1994 and 1995-2014.
</div>

## Research Overview

### Key Questions
- **Concentration**: Is there more or less technological agglomeration than expected?
- **Co-localization**: Which technologies spatially interact with others?
- **Policy Impact**: How did Japan's regionalization policy affect innovation patterns?

### Main Findings
1. **Increased Concentration**: After 1995, technologies became more spatially concentrated at the local level
2. **Decreased Co-localization**: Spatial proximity between different technologies significantly declined
3. **Policy Trade-off**: Regionalization policies successfully promoted local specialization but reduced cross-field interactions

---

## Methodology

The analysis employs the **K<sup>emp</sup> function** (Duranton & Overman, 2005, 2008), a distance-based measure that:
- Treats space as continuous, avoiding the Modifiable Areal Unit Problem (MAUP)
- Provides statistical significance through Monte Carlo simulations
- Enables comparison across technologies and time periods

### Data Pipeline

```
┌─────────────────────┐     ┌──────────────────┐     ┌─────────────────────┐
│   IIP Patent DB     │────▶│   Geocoding      │────▶│  Point Database     │
│   (14.3M patents)   │     │   (Block-level)  │     │  (524,714 points)   │
└─────────────────────┘     └──────────────────┘     └─────────────────────┘
                                                              │
                                                              ▼
                            ┌──────────────────┐     ┌─────────────────────┐
                            │  Visualization   │◀────│  K^emp Analysis     │
                            │  & Mapping       │     │  (Concentration &   │
                            └──────────────────┘     │   Co-localization)  │
                                                     └─────────────────────┘
```

---

## Code Highlights

### 1. Computing the K<sup>emp</sup> Function

The weighted K-density function measures spatial concentration by analyzing the distribution of bilateral distances between patent locations:

```r
# Load required libraries
library(dbmss)
library(sf)
library(tidyverse)

# Create weighted point pattern from patent data
# Each point is weighted by the number of patents at that location
create_weighted_ppp <- function(data, tech_category) {
  data %>%
    filter(category == tech_category) %>%
    mutate(weight = n_patents) %>%
    st_as_sf(coords = c("lon", "lat"), crs = 4326) %>%
    as.ppp()
}

# Calculate K^emp for a technology category
# This identifies whether clustering exceeds random expectation
calculate_kemp <- function(ppp_object, max_distance = 180000) {
  Kest(ppp_object, 
       correction = "best",
       r = seq(0, max_distance, by = 1000))
}
```

**Why this matters**: Unlike traditional measures that rely on administrative boundaries, K<sup>emp</sup> captures the continuous nature of spatial clustering, revealing patterns that polygon-based methods miss.

### 2. Concentration Index (Γ and Ψ)

The gamma (Γ) and psi (Ψ) indices quantify localization and dispersion strength:

```r
# Calculate localization index (Gamma)
# Captures positive deviation from upper confidence band
calculate_gamma <- function(kemp_result, upper_ci) {
  gamma <- sum(pmax(kemp_result$obs - upper_ci, 0))
  return(gamma)
}

# Calculate dispersion index (Psi)
# Only computed when Gamma = 0
calculate_psi <- function(kemp_result, lower_ci, gamma) {
  if (gamma > 0) {
    return(0)
  }
  psi <- sum(pmax(lower_ci - kemp_result$obs, 0))
  return(psi)
}

# Monte Carlo simulation for confidence intervals
# 99 simulations with 0.05 significance level
run_monte_carlo <- function(ppp_object, n_sim = 99) {
  envelope(ppp_object, 
           Kest, 
           nsim = n_sim, 
           nrank = 2,  # For 0.05 confidence
           verbose = FALSE)
}
```

### 3. Co-localization Analysis

Measuring spatial relationships between technology pairs reveals knowledge flow patterns:

```r
# Calculate co-localization between two technology categories
# Uses the same K^emp framework applied to pairs
calculate_colocalization <- function(data, tech_A, tech_B) {
  
  # Combine points from both categories
  combined_data <- data %>%
    filter(category %in% c(tech_A, tech_B))
  
  # Create bivariate point pattern
  biv_ppp <- combined_data %>%
    mutate(mark = factor(category)) %>%
    st_as_sf(coords = c("lon", "lat"), crs = 4326) %>%
    as.ppp()
  
  # Calculate cross-K function
  # Tests whether tech_A points cluster near tech_B points
  # beyond what's expected from their combined distribution
  Kcross(biv_ppp, 
         i = tech_A, 
         j = tech_B,
         correction = "isotropic")
}

# Iterate over all technology pairs (30 categories = 435 pairs)
calculate_all_pairs <- function(data, categories) {
  pairs <- combn(categories, 2, simplify = FALSE)
  
  map_dfr(pairs, function(pair) {
    result <- calculate_colocalization(data, pair[1], pair[2])
    tibble(
      tech_A = pair[1],
      tech_B = pair[2],
      gamma = calculate_gamma(result, upper_ci),
      psi = calculate_psi(result, lower_ci, gamma)
    )
  })
}
```

### 4. Visualization of Results

Creating publication-ready figures that reveal spatial patterns:

```r
# Plot concentration curves by technology
plot_concentration_curves <- function(kemp_results, tech_categories) {
  
  ggplot(kemp_results, aes(x = distance_km, y = kemp_value)) +
    # Confidence envelope (Monte Carlo)
    geom_ribbon(aes(ymin = lower_ci, ymax = upper_ci), 
                alpha = 0.3, fill = "grey70") +
    # Observed K^emp curve
    geom_line(color = "#E74C3C", linewidth = 0.8) +
    # Reference (random expectation)
    geom_line(aes(y = theoretical), 
              linetype = "dashed", color = "grey40") +
    facet_wrap(~technology, scales = "free_y", ncol = 5) +
    labs(
      x = "Distance (km)",
      y = expression(K^emp),
      title = "Concentration Patterns by Technology Category"
    ) +
    theme_minimal(base_family = "Helvetica") +
    theme(
      strip.text = element_text(size = 9, face = "bold"),
      panel.grid.minor = element_blank()
    )
}

# Heatmap for co-localization matrix
plot_colocalization_matrix <- function(coloc_results) {
  
  # Transform psi values to negative for co-dispersion
  coloc_matrix <- coloc_results %>%
    mutate(value = ifelse(gamma > 0, gamma, -psi))
  
  ggplot(coloc_matrix, aes(x = tech_A, y = tech_B, fill = value)) +
    geom_tile() +
    scale_fill_gradient2(
      low = "#3498DB",      # Blue for co-dispersion
      mid = "white",
      high = "#E74C3C",     # Red for co-localization
      midpoint = 0,
      name = "Γ/Ψ"
    ) +
    coord_fixed() +
    theme_minimal() +
    theme(
      axis.text.x = element_text(angle = 45, hjust = 1, size = 7),
      axis.text.y = element_text(size = 7),
      axis.title = element_blank()
    )
}
```

---

## Key Results Summary

| Period | Concentrated Technologies | Dispersed Technologies |
|--------|--------------------------|----------------------|
| 1975-1994 | Electronics, Mining, Nuclear Physics, Clocks/Computers | Agriculture, Non-organic Chemistry |
| 1995-2014 | Same + More categories | Only Agriculture remains |

The shift from 1975-1994 to 1995-2014 shows:
- **More concentrated categories** (strengthening of regional clusters)
- **Fewer dispersed categories** (loss of national integration)
- **Reduced co-localization** (isolation of technological domains)

---

## Repository Structure

```
Concentration-colocalization-Japan/
├── 00_Data/
│   ├── patent_points.csv          # Geocoded inventor locations
│   ├── technology_lookup.csv      # IPC to NBER classification
│   └── japan_boundaries.shp       # Administrative boundaries
├── 01_Scripts/
│   ├── 01_Concentration.R         # K^emp concentration analysis
│   ├── 02_Colocalization.R        # Pairwise co-localization
│   └── 03_Visualization.R         # Figures and maps
└── README.md
```

---

## Citation

If you use this code or methodology, please cite:

```bibtex
@article{baeza2026concentration,
  title={Concentration and co-localization dynamics of technological 
         innovation: The Japanese case},
  author={Baeza-Gonz{\'a}lez, Sebasti{\'a}n and Kamakura, Natsuki},
  journal={Applied Geography},
  volume={186},
  pages={103819},
  year={2026},
  publisher={Elsevier},
  doi={10.1016/j.apgeog.2025.103819}
}
```

---

## Links

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
    <a href="https://github.com/sebaeza/Concentration-colocalization-Japan" class="btn btn-sm z-depth-0" role="button">
        <i class="fab fa-github"></i> GitHub Repository
    </a>
    <a href="https://www.sciencedirect.com/science/article/pii/S0143622825003169" class="btn btn-sm z-depth-0" role="button">
        <i class="fas fa-file-pdf"></i> Published Paper
    </a>
</div>
