---
layout: page
title: Emerging Hot Spot Analysis
description: Tracing cluster life cycles in Japanese innovation using spatiotemporal statistics
img: assets/img/projects_img/2_ehsa_japan/thumbnail.png
importance: 2
category: research
related_publications: false
github: https://github.com/sebaeza/EHSA_Japan
---

This project presents a methodological framework using **Emerging Hot Spot Analysis (EHSA)** to trace the life cycles of innovation clusters in Japan. By combining spatial clustering detection with temporal trend analysis, we identify where and when inventive activity emerges, intensifies, persists, or declines.

## Research Overview

### The Challenge
Evolutionary Economic Geography seeks to understand how spatial patterns of economic activity emerge and change over time. However, translating theoretical cluster life cycle models into testable, comparable evidence has remained methodologically difficult.

### Our Solution
We propose EHSA as a spatial statistical tool that:
1. Identifies **hot spots** (clusters of high activity) and **cold spots** (areas of low activity)
2. Detects **temporal trends** in these patterns
3. Classifies locations into **17 distinct spatiotemporal patterns**
4. Enables **cross-regional and cross-sectoral comparison**

### Key Findings

| Technology | 1980-2000 Pattern | 2000-2020 Pattern | Interpretation |
|------------|-------------------|-------------------|----------------|
| **Chemicals** | Consecutive hot spots (growth) | Sporadic/cold spots | Post-growth decline |
| **Mechanicals** | Consecutive hot spots | Persistent + new hot spots | Stable maturity + expansion |
| **ICT** | Metro-concentrated | Metro-locked | Extended emergence |
| **Electronics** | Headquarters-based | Polycentric but narrow | Bifurcated trajectory |

---

## Methodology

EHSA combines two statistical techniques:

### 1. Getis-Ord Gi* Statistic
Identifies local spatial clusters by comparing values at each location to its neighbors:

$$G_i^* = \frac{\sum_{j=1}^{n} w_{ij} x_j - \bar{X} \sum_{j=1}^{n} w_{ij}}{S \sqrt{\frac{n \sum_{j=1}^{n} w_{ij}^2 - (\sum_{j=1}^{n} w_{ij})^2}{n-1}}}$$

Where:
- $x_j$ = attribute value at location $j$
- $w_{ij}$ = spatial weight between locations $i$ and $j$
- $\bar{X}$ = mean of all values
- $S$ = standard deviation

### 2. Mann-Kendall Trend Test
Detects monotonic trends in time series data:

$$S = \sum_{k=1}^{n-1} \sum_{j=k+1}^{n} \text{sgn}(x_j - x_k)$$

The combination classifies each location's temporal trajectory based on its hot/cold spot history.

---

## Code Highlights

### 1. Creating Spatiotemporal Cubes

The foundation of EHSA is a data structure that maintains spatial consistency across time:

```r
# Load required packages
library(sfdep)      # For EHSA calculations
library(sf)         # Spatial data handling
library(tidyverse)  # Data manipulation
library(tmap)       # Mapping

# Create a spatiotemporal cube from patent data
# Critical: Each municipality must have data for ALL time steps
create_spacetime_cube <- function(patent_data, 
                                   municipalities, 
                                   time_var = "year",
                                   value_var = "n_patents") {
  
  # Identify municipalities with complete time series
  complete_munis <- patent_data %>%
    group_by(muni_code) %>%
    summarise(n_years = n_distinct(.data[[time_var]])) %>%
    filter(n_years == max(n_years)) %>%
    pull(muni_code)
  
  # Filter to complete cases only
  # This is essential - EHSA cannot handle missing values
  filtered_data <- patent_data %>%
    filter(muni_code %in% complete_munis)
  
  # Join with spatial geometry
  spacetime_cube <- municipalities %>%
    filter(muni_code %in% complete_munis) %>%
    left_join(filtered_data, by = "muni_code") %>%
    arrange(muni_code, .data[[time_var]])
  
  return(spacetime_cube)
}
```

**Why complete cases matter**: Unlike simple interpolation, EHSA's trend detection requires genuine temporal continuity. Imputing zeros would conflate "no patents filed" with "no patenting capacity."

### 2. Running EHSA Analysis

The core analysis using the `sfdep` package:

```r
# Calculate EHSA for a technology category
run_ehsa <- function(spacetime_cube, 
                     value_var,
                     n_time_steps = 20,
                     n_simulations = 199,
                     significance_level = 0.05) {
  
  # Create spatial neighbors using queen contiguity
  # Each municipality considers all adjacent municipalities
  neighbors <- spacetime_cube %>%
    st_filter_contiguity() %>%
    st_weights(style = "W")  # Row-standardized weights
  
  # Run EHSA calculation
  # This combines Getis-Ord Gi* with Mann-Kendall
  ehsa_result <- emerging_hotspot_analysis(
    x = spacetime_cube,
    .var = value_var,
    k = n_time_steps,
    nsim = n_simulations,
    threshold = significance_level,
    include_gi = TRUE
  )
  
  return(ehsa_result)
}

# Process multiple technology categories
analyze_all_technologies <- function(patent_data, 
                                      municipalities,
                                      categories) {
  
  results <- map(categories, function(cat) {
    
    cat_data <- patent_data %>%
      filter(nber_category == cat)
    
    cube <- create_spacetime_cube(cat_data, municipalities)
    
    ehsa <- run_ehsa(cube, "n_patents")
    
    ehsa %>%
      mutate(technology = cat)
    
  }) %>%
    bind_rows()
  
  return(results)
}
```

### 3. Interpreting EHSA Patterns

EHSA returns 17 distinct classifications. Here's how to map them to cluster life cycles:

```r
# Define life cycle interpretation for each EHSA pattern
ehsa_lifecycle_mapping <- tribble(
  ~ehsa_pattern,           ~lifecycle_stage,        ~interpretation,
  # Hot Spot Patterns (Growth/Maturity)
  "new_hot_spot",          "Emergence",             "Recent surge in activity",
  "consecutive_hot_spot",  "Early Growth",          "Sustained recent clustering",
  "intensifying_hot_spot", "Growth/Approaching",    "Accelerating concentration",
  "persistent_hot_spot",   "Maturity",              "Stable high activity",
  "diminishing_hot_spot",  "Late Maturity/Decline", "Weakening concentration",
  "sporadic_hot_spot",     "Transitional",          "Inconsistent clustering",
  "oscillating_hot_spot",  "Transitional/Renewal",  "Volatility or adaptation",
  "historical_hot_spot",   "Past Peak",             "No longer significant",
  
  # Cold Spot Patterns (Innovation Deficits)
  "new_cold_spot",          "Emerging Gap",          "Recent activity drop",
  "consecutive_cold_spot",  "Persistent Deficit",    "Sustained low activity",
  "intensifying_cold_spot", "Deepening Gap",         "Accelerating decline",
  "persistent_cold_spot",   "Chronic Deficit",       "Long-term stagnation",
  "diminishing_cold_spot",  "Potential Turnaround",  "Signs of improvement",
  "sporadic_cold_spot",     "Intermittent Deficit",  "Unstable low activity",
  "oscillating_cold_spot",  "Fluctuating",           "Cycles of underperformance",
  "historical_cold_spot",   "Historical Low",        "Past pattern only",
  
  "no_pattern_detected",    "Stable/Average",        "Near mean, no trend"
)

# Add lifecycle interpretation to results
interpret_ehsa <- function(ehsa_results) {
  ehsa_results %>%
    left_join(ehsa_lifecycle_mapping, 
              by = c("classification" = "ehsa_pattern"))
}
```

### 4. Analyzing Temporal Transitions

Compare patterns between two periods to identify evolutionary shifts:

```r
# Identify municipalities that changed status between periods
analyze_transitions <- function(period1_results, 
                                 period2_results) {
  
  transitions <- period1_results %>%
    select(muni_code, classification_p1 = classification) %>%
    inner_join(
      period2_results %>%
        select(muni_code, classification_p2 = classification),
      by = "muni_code"
    ) %>%
    mutate(
      # Categorize the type of transition
      transition_type = case_when(
        # Upgrades (moving toward hot spot)
        str_detect(classification_p1, "cold") & 
          str_detect(classification_p2, "hot") ~ "cold_to_hot",
        
        classification_p1 == "no_pattern_detected" & 
          str_detect(classification_p2, "hot") ~ "neutral_to_hot",
        
        # Intensification
        classification_p1 == "consecutive_hot_spot" & 
          classification_p2 == "persistent_hot_spot" ~ "growth_to_maturity",
        
        # Downgrades
        str_detect(classification_p1, "hot") & 
          str_detect(classification_p2, "cold") ~ "hot_to_cold",
        
        # Stability
        classification_p1 == classification_p2 ~ "stable",
        
        TRUE ~ "other_transition"
      )
    )
  
  return(transitions)
}

# Summary statistics for transitions
summarize_transitions <- function(transitions) {
  transitions %>%
    count(transition_type, classification_p1, classification_p2) %>%
    arrange(desc(n)) %>%
    mutate(
      pct = n / sum(n) * 100,
      label = paste0(classification_p1, " → ", classification_p2)
    )
}
```

### 5. Visualization

Creating maps that reveal spatiotemporal patterns:

```r
# Map EHSA results with custom color scheme
map_ehsa_results <- function(ehsa_sf, 
                              title = "EHSA Classification") {
  
  # Define color palette matching lifecycle stages
  ehsa_colors <- c(
    # Hot spots (reds/oranges)
    "new_hot_spot" = "#8B0000",
    "consecutive_hot_spot" = "#B22222",
    "intensifying_hot_spot" = "#DC143C",
    "persistent_hot_spot" = "#FF6347",
    "diminishing_hot_spot" = "#FF7F50",
    "sporadic_hot_spot" = "#FFA07A",
    "oscillating_hot_spot" = "#FFB6C1",
    "historical_hot_spot" = "#FFC0CB",
    
    # Cold spots (blues)
    "new_cold_spot" = "#E0FFFF",
    "consecutive_cold_spot" = "#ADD8E6",
    "intensifying_cold_spot" = "#87CEEB",
    "persistent_cold_spot" = "#4169E1",
    "diminishing_cold_spot" = "#6495ED",
    "sporadic_cold_spot" = "#1E90FF",
    "oscillating_cold_spot" = "#00BFFF",
    "historical_cold_spot" = "#87CEFA",
    
    # No pattern
    "no_pattern_detected" = "#808080"
  )
  
  # Filter to significant results only
  sig_results <- ehsa_sf %>%
    filter(p_value < 0.05)
  
  # Create map
  tm_shape(ehsa_sf) +
    tm_fill(col = "grey90") +
    tm_borders(col = "white", lwd = 0.2) +
  tm_shape(sig_results) +
    tm_fill(
      col = "classification",
      palette = ehsa_colors,
      title = "EHSA Pattern"
    ) +
    tm_borders(col = "white", lwd = 0.2) +
  tm_layout(
    title = title,
    title.size = 1.2,
    legend.outside = TRUE,
    legend.outside.position = "right",
    frame = FALSE
  ) +
  tm_scale_bar(position = c("left", "bottom")) +
  tm_compass(position = c("right", "top"))
}

# Create comparison panel for two periods
create_period_comparison <- function(results_p1, results_p2, tech_name) {
  
  map1 <- map_ehsa_results(
    results_p1, 
    title = paste(tech_name, "1980-2000")
  )
  
  map2 <- map_ehsa_results(
    results_p2, 
    title = paste(tech_name, "2000-2020")
  )
  
  tmap_arrange(map1, map2, ncol = 2)
}
```

---

## EHSA Pattern Classification

The 17 patterns can be grouped by their implications for cluster evolution:

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        <h4 style="color: #E74C3C;">Hot Spot Patterns</h4>
        <ul>
            <li><strong>New</strong>: Just emerged (final period only)</li>
            <li><strong>Consecutive</strong>: Recent sustained clustering</li>
            <li><strong>Intensifying</strong>: Significant upward trend</li>
            <li><strong>Persistent</strong>: Stable high activity (≥90%)</li>
            <li><strong>Diminishing</strong>: Significant downward trend</li>
            <li><strong>Sporadic</strong>: On-and-off clustering</li>
            <li><strong>Oscillating</strong>: Was cold, now hot</li>
            <li><strong>Historical</strong>: Was hot, not anymore</li>
        </ul>
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        <h4 style="color: #3498DB;">Cold Spot Patterns</h4>
        <ul>
            <li><strong>New</strong>: Just emerged deficit</li>
            <li><strong>Consecutive</strong>: Recent sustained deficit</li>
            <li><strong>Intensifying</strong>: Deepening innovation gap</li>
            <li><strong>Persistent</strong>: Chronic low activity</li>
            <li><strong>Diminishing</strong>: Signs of improvement</li>
            <li><strong>Sporadic</strong>: Intermittent deficit</li>
            <li><strong>Oscillating</strong>: Was hot, now cold</li>
            <li><strong>Historical</strong>: Past deficit only</li>
        </ul>
    </div>
</div>

---

## Key Insights from Japan

### Chemicals: Post-Growth Decline
- 1980-2000: Consecutive hot spots in coastal industrial complexes (Kisarazu, Kurashiki)
- 2000-2020: Shifted to "no pattern" or sporadic cold spots
- **Exception**: Fine ceramics (Arita, Shiojiri, Ōgaki) showed intensifying signals

### Mechanicals: Stable with Expansion
- Toyota heartland (Toyota, Okazaki, Kariya): Consecutive → Persistent hot spots
- **New development**: Tohoku corridor emergence (Hanamaki, Ichinoseki, Ōshū)
- Evidence of dual process: entrenchment + peripheral expansion

### ICT: Metro-Locked
- Highly centralized in Tokyo, Osaka, Kanagawa
- Only 9 municipalities entered hot spot status between periods
- Bunkyō ward upgrade linked to university-industry reforms

### Electronics: Bifurcated Trajectory
- Legacy sites (Kadoma/Panasonic) showed early cooling
- Selective recovery through policy-led diversification
- RTN Park (Ebetsu) and Technopolis sites (Tosu, Kurume) emerged

---

## Repository Structure

```
EHSA_Japan/
├── .github/
│   └── PULL_REQUEST_TEMPLATE.md
├── Data/
│   ├── patent_data.csv           # Aggregated by municipality-year
│   ├── municipalities.shp        # Japanese municipality boundaries
│   └── nber_classification.csv   # Technology categories
├── Results/
│   ├── ehsa_chemical_p1.rds      # Period 1 results
│   ├── ehsa_chemical_p2.rds      # Period 2 results
│   └── ...                       # Other categories
├── Scripts/
│   ├── 01_data_preparation.R     # Build spacetime cubes
│   ├── 02_ehsa_analysis.R        # Run EHSA calculations
│   ├── 03_visualization.R        # Create maps and figures
│   └── 04_transition_analysis.R  # Compare periods
└── README.md
```

---

## Getting Started

```r
# Install required packages
install.packages(c("sfdep", "sf", "tidyverse", "tmap"))

# Clone the repository
# git clone https://github.com/sebaeza/EHSA_Japan.git

# Run the analysis pipeline
source("Scripts/01_data_preparation.R")
source("Scripts/02_ehsa_analysis.R")
source("Scripts/03_visualization.R")
```

---

## Citation

If you use this methodology or code, please cite:

```bibtex
@article{baeza2025ehsa,
  title={A methodological framework for tracing cluster life cycles: 
         emerging hot spot analysis in evolutionary economic geography},
  author={Baeza-Gonz{\'a}lez, Sebasti{\'a}n and Kamakura, Natsuki},
  journal={Evolutionary and Institutional Economics Review},
  year={2025},
  publisher={Springer},
  doi={10.1007/s40844-025-00314-5}
}
```

---

## Links

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
    <a href="https://github.com/sebaeza/EHSA_Japan" class="btn btn-sm z-depth-0" role="button">
        <i class="fab fa-github"></i> GitHub Repository
    </a>
    <a href="https://link.springer.com/article/10.1007/s40844-025-00314-5" class="btn btn-sm z-depth-0" role="button">
        <i class="fas fa-file-pdf"></i> Published Paper
    </a>
</div>
