# ✈️ Global Aviation Network — Geopolitical Disruption Analysis

> **Network Science Mini-Project | IIIT-Delhi**
> Modeling the Impact of Iran/Middle East Airspace Closures on Global Connectivity

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Research Questions](#research-questions)
- [Dataset](#dataset)
- [Project Structure](#project-structure)
- [Deliverables](#deliverables)
  - [D1 — Network Construction](#d1--network-construction--structural-analysis)
  - [D2 — Scale-Free Analysis](#d2--scale-free-topology--degree-distribution)
  - [D3 — Centrality Analysis](#d3--centrality-analysis--hub-identification)
  - [D4 — Robustness Analysis](#d4--robustness--failure-analysis)
  - [D5 — Disruption Simulation](#d5--iran-region-disruption-simulation)
- [Key Findings Summary](#key-findings-summary)
- [Installation & Usage](#installation--usage)
- [Dependencies](#dependencies)
- [Team](#team)
- [References](#references)

---

## Project Overview

The global aviation network carries over **4 billion passengers annually** across approximately **100,000 scheduled flights per day** (pre-COVID baseline). Its highly centralised hub-and-spoke topology makes it acutely sensitive to disruptions at strategically positioned airports — particularly in the Middle East, which serves as the critical transit bridge connecting Europe, South Asia, and East Asia.

The **Iranian airspace corridor** alone handles approximately **1,500–2,000 commercial transits per day**, making it one of the most heavily used overfly routes in the world. The April 2024 Iran–Israel escalation — which triggered immediate NOTAMs across Iranian, Iraqi, Jordanian, and Israeli airspace — forced carriers including Air India, Lufthansa, British Airways, and Singapore Airlines to add 2–4 hours to Europe–Asia itineraries.

This project models the global aviation network as a **weighted directed graph** using the OpenFlights dataset and conducts a rigorous five-part network science analysis to quantify how geographically localised disruptions propagate through the entire global system.

**Central Research Question:**
> *How do geographically localised disruptions in the Middle East propagate through the global aviation network, and which structural properties govern the magnitude of that propagation?*

---

## Research Questions

| # | Research Question |
|---|---|
| RQ1 | Does the global aviation network exhibit scale-free topology? What are the quantitative resilience implications? |
| RQ2 | Which airports are most critical to global connectivity, as measured by betweenness centrality, PageRank, and total degree? |
| RQ3 | How does the network respond to random failures vs. targeted attacks? Where is the percolation transition threshold? |
| RQ4 | What is the quantitative global impact of simulated Iran/Middle East airspace disruptions on connectivity, path length, and route availability? |
| RQ5 | Which secondary hubs gain the most strategic importance as rerouting nodes after Iranian airspace closure? |

---

## Dataset

**Source:** [OpenFlights](https://openflights.org/data.html)

| File | Description | Raw Size |
|------|-------------|----------|
| `airports.dat` | Airport metadata — IATA codes, GPS coordinates, country, altitude, timezone | ~14,000 airports |
| `routes.dat` | Directed airline routes — source airport, destination airport, operating airline(s) | ~67,000 routes |

### After Preprocessing

| Metric | Value |
|--------|-------|
| Airports (nodes) | **3,257** |
| Routes (edges) | **37,042** |
| Filter criteria | Valid 3-char IATA code · type = `airport` · `stops = 0` (direct routes only) · valid GPS |

**Preprocessing Steps:**
1. Retain airports with valid 3-character IATA codes only
2. Keep only `type = "airport"` records (heliports and seaplane bases excluded)
3. Retain only direct, non-stop routes (`stops = 0`)
4. Remove isolated nodes (airports with zero routes after filtering)
5. Compute edge weights as the number of distinct airlines per route pair

---

## Project Structure

```
global-aviation-network/
│
├── data/
│   ├── airports.dat              # Raw airport metadata (OpenFlights)
│   └── routes.dat                # Raw route data (OpenFlights)
│
├── deliverables/
│   ├── d1_network_construction.py    # Graph building & structural stats
│   ├── d2_scale_free_analysis.py     # Power-law fitting, Gini, k-core
│   ├── d3_centrality_analysis.py     # Betweenness, PageRank, HITS
│   ├── d4_robustness_analysis.py     # Percolation & failure simulations
│   └── d5_disruption_simulation.py   # Iran/ME geopolitical scenarios
│
├── outputs/
│   ├── figures/                  # All generated plots (PNG/PDF)
│   └── tables/                   # CSV exports of key metrics
│
├── NS_mini_project_57.pdf        # Final presentation slides
├── Report.pdf                    # Full technical report
└── README.md
```

---

## Deliverables

### D1 — Network Construction & Structural Analysis

**Objective:** Build a weighted directed graph G = (V, E, w) representing the global civil aviation network.

**Graph Formulation:**
- **Nodes V:** Each airport identified by its IATA code; attributes include GPS coordinates, country, altitude, city
- **Edges E:** Each directed edge (u → v) represents at least one scheduled non-stop route from airport u to airport v
- **Edge weight w(u,v):** Number of distinct airlines operating that route — used as a proxy for traffic intensity

**Key Network Statistics:**

| Metric | Value |
|--------|-------|
| Nodes (airports) | 3,257 |
| Edges (routes) | 37,042 |
| Network Density | 0.00349 |
| Average In-Degree | 11.37 |
| Average Out-Degree | 11.37 |
| Maximum In-Degree | 238 |
| Maximum Out-Degree | 239 |
| Average Clustering Coefficient | 0.488 |
| LCC Size (Weakly Connected) | 3,231 (99.2%) |
| Average Edge Weight | 1.81 |
| Maximum Edge Weight | 20 |

**Key Observations:**
- **Sparse yet globally connected:** Density of only 0.00349, yet 99.2% of all airports belong to a single weakly connected component
- **Symmetric connectivity:** Average in-degree = out-degree = 11.37 — well-balanced directed network
- **Moderate clustering (0.488):** Local regional clusters exist, but less dense than social networks — reflects geographic and airline routing constraints
- **Small-world signature:** Low density + high clustering + near-complete connectivity
- **Edge weight insight:** Average weight = 1.81 (most routes are single-carrier); maximum weight = 20 (highly competitive corridors)

**Geographic Clusters Identified:**
- Western Europe: LHR, FRA, AMS, CDG — densely interconnected mega-hubs
- US East & West Coast: DFW, LAX, ORD — major transatlantic and transpacific hubs
- Middle East (highlighted): IST, DXB, DOH — conspicuously positioned between European and Asian clusters
- East Asia: PEK, HKG, SIN — critical geographic bridge

---

### D2 — Scale-Free Topology & Degree Distribution

**Objective:** Test whether the aviation network follows a power-law degree distribution and quantify inequality in connectivity.

**Methodology:**
- Fit power-law to degree distribution via log-log OLS regression (for k ≥ kmin = 5)
- Compute Gini coefficient and Lorenz curve for degree inequality
- Plot Complementary CDF (CCDF) to visualise tail behaviour
- Perform k-core decomposition to reveal hierarchical core-periphery structure

**Results:**

| Metric | Value |
|--------|-------|
| Power-law exponent α | 1.068 |
| Goodness of fit R² | 0.7294 |
| p-value | 1.16 × 10⁻⁶² |
| Gini coefficient | 0.722 |
| Nodes with k ≤ 10 | 67.1% |
| Nodes with k ≥ 100 | 5.6% |
| Maximum k-core | 31 |

**Key Finding:**
> α = 1.068 lies **outside** the canonical scale-free range [2, 3]. The network has a heavy-tailed, hub-dominated structure, but is best described as a **heterogeneous hub-dominated system** rather than a pure scale-free network.

**Degree Inequality:**
- Gini = 0.722 → very high inequality; the top 1% of airports hold approximately 40% of all connections
- The Lorenz curve lies far below the perfect-equality diagonal
- Majority of airports (67.1%) have degree ≤ 10, while a small number of mega-hubs exceed degree 200

**K-Core Structure:**
- Maximum k-core = 31, indicating a deeply embedded structural core
- Hierarchical, nested "onion-like" core-periphery structure
- Innermost k-core: tightly interconnected hub airports that remain mutually connected even after removing all peripheral nodes

---

### D3 — Centrality Analysis & Hub Identification

**Objective:** Identify which airports are most critical to global connectivity using four complementary centrality measures.

**Centrality Measures:**

| Measure | Formula | Interpretation |
|---------|---------|----------------|
| Degree Centrality | C_D(v) = deg(v) / (N−1) | Proportion of all airports directly connected to v |
| Betweenness Centrality | C_B(v) = Σ σ_st(v) / σ_st | Fraction of all shortest paths passing through v; measures bridge/bottleneck role |
| PageRank | PR(v) = (1−d)/N + d·Σ PR(u)/deg⁺(u) | Iterative authority score; d = 0.85; weighted by incoming link quality |
| In-Degree Centrality | C_in(v) = k_in(v) / (N−1) | Number of airports that fly directly to v (demand-side importance) |

> HITS (Hyperlink-Induced Topic Search) hub and authority scores are also computed.

**Implementation Notes:**
- Betweenness centrality computed on the Largest Strongly Connected Component (SCC) with k = 500 pivot-node approximation for tractability
- PageRank uses `weight='weight'` so higher-traffic routes contribute proportionally more authority

**Top 10 — Betweenness Centrality:**

| # | IATA | City | Country | Score |
|---|------|------|---------|-------|
| 1 | **DXB** | Dubai | UAE | 0.0658 |
| 2 | CDG | Paris | France | 0.0654 |
| 3 | LAX | Los Angeles | USA | 0.0592 |
| 4 | PEK | Beijing | China | 0.0549 |
| 5 | YYZ | Toronto | Canada | 0.0538 |
| 6 | ANC | Anchorage | USA | 0.0505 |
| 7 | FRA | Frankfurt | Germany | 0.0493 |
| 8 | ORD | Chicago | USA | 0.0481 |
| 9 | IST | Istanbul | Turkey | 0.0466 |
| 10 | AMS | Amsterdam | Netherlands | 0.0423 |

**Top Rankings — Other Measures:**
- **Degree Centrality:** FRA, CDG, AMS, IST
- **PageRank:** ATL, ORD, LAX, DFW
- **In-Degree Centrality:** FRA, CDG, AMS, IST

**Key Observations:**
- **Dubai ranks #1 globally in betweenness** — 6.58% of all optimal paths pass through it, higher than any European or American hub
- **Middle East hubs DXB, DOH, AUH all rank in the global top 20** despite not being among the highest-degree airports — they are intercontinental bridges, not merely regional hubs
- **Degree ↔ Betweenness Mismatch:** FRA has the highest total degree in the entire network yet ranks only #7 in betweenness. ANC (Anchorage) has a fraction of FRA's connections but ranks #6 — it sits on a unique geographic chokepoint across the North Pacific
- Iranian airports cluster in the low-to-medium degree zone but occupy non-trivial betweenness positions — not mega-hubs by traffic volume, but critical through-route nodes

---

### D4 — Robustness & Failure Analysis

**Objective:** Quantify the network's resilience to random failures vs. targeted attacks using percolation theory.

**Framework:**

The Largest Connected Component (LCC) is tracked as nodes are progressively removed:

```
LCC fraction(f) = |S_max(G_f)| / N
```

where f is the fraction of nodes removed and the critical threshold f_c is the smallest f at which LCC falls below 50%.

**Three Attack Strategies:**

| Strategy | Description | Real-World Analog |
|----------|-------------|-------------------|
| Random failure | Nodes removed uniformly at random | Equipment failures, weather closures |
| Degree-targeted | Nodes removed by descending degree (recomputed after each removal) | Adversary targeting most-connected airports |
| Betweenness-targeted | Nodes removed by descending betweenness centrality | Most strategically optimal adversarial attack |

**Results:**

| Metric | Value |
|--------|-------|
| Critical threshold (random) | f_c ≈ 0.40 |
| Critical threshold (targeted) | f_c ≈ 0.10 |
| Critical threshold (betweenness) | f_c ≈ 0.08 |
| Resilience ratio | ~4× |

**Key Observations:**
- Network maintains connectivity until ~40% of airports are removed under random failures — majority of low-degree nodes are effectively expendable
- Just 10% targeted removal causes rapid fragmentation. The betweenness strategy is most devastating: **only 8% of airports need to be removed to cripple the network**
- The network is ~4× more resilient to random failures than targeted attacks — a direct consequence of the hub-dominated, heavy-tailed degree distribution
- Random failures produce gradual, roughly linear LCC decay; targeted attacks produce a steep cliff after a small plateau
- **Single-node criticality:** ANC (Anchorage) causes the largest individual LCC drop on removal, due to its unique geographic chokepoint position

---

### D5 — Iran Region Disruption Simulation

**Objective:** Simulate four geopolitical disruption scenarios of increasing severity and quantify their global network impact.

**Geopolitical Motivation:**

| Event | Year | Impact |
|-------|------|--------|
| Iran–Iraq War | 1980–1988 | Iranian airspace closed to international traffic for 8 years |
| Soleimani Crisis | Jan 2020 | Iran shuttered airspace; ~500 flights/day affected |
| Israel Conflict | Oct 2023– | Israeli airspace restrictions, Gulf carrier route suspensions |
| Iran Strikes Israel | Apr 2024 | ~300 drones launched; NOTAMs across Iran, Iraq, Jordan, Israel. Air India, Lufthansa, BA, Singapore Airlines add 2–4 hours to Europe–Asia itineraries |
| Renewed Tensions | 2026 | Temporary closures, rising congestion |

**Disruption Scenarios:**

| ID | Name | Implementation | Real-World Parallel |
|----|------|----------------|---------------------|
| A | Iran Closure | Remove all 43 Iranian airport nodes | April 2024 NOTAM closure |
| B | Extended Closure | Remove Iran + Iraq + Afghanistan + Syria nodes (62 airports) | Worst-case regional conflict |
| C | ME Restriction | Reduce edge weights by 80% on all ME-adjacent routes | Partial flight restrictions |
| D | Hub Removal | Remove top-5 Iranian airports by degree | Targeted infrastructure disruption |

**Haversine Distance** is used to compute the geographic scale of disruption:
```
d = 2R · arctan(√a, √(1−a))
a = sin²(Δφ/2) + cos(φ₁)·cos(φ₂)·sin²(Δλ/2)
where R = 6,371 km
```

**Disruption Impact Summary:**

| Scenario | LCC (%) | ΔPaths (%) | Lost Nodes | Lost Edges |
|----------|---------|------------|------------|------------|
| Baseline | 99.20 | — | — | — |
| A: Iran Closure | 99.19 | +0.08% | 43 | 382 |
| B: Extended Closure | 99.19 | +0.38% | 62 | 636 |
| C: ME Restriction | 99.20 | +0.00% | 0 | 0 |
| D: Top-5 Iranian Hubs | 98.59 | −0.35% | 5 | 299 |

**Route-Level Impact on Major Europe–Asia Corridors (Scenario A):**

| Route | Corridor | Before | After | Δ hops | Status |
|-------|----------|--------|-------|--------|--------|
| LHR→DEL | London – Delhi | 2 | 2–3 | 0–1 | Same/longer |
| FRA→BOM | Frankfurt – Mumbai | 2 | 2–3 | 0–1 | Same/longer |
| FRA→SIN | Frankfurt – Singapore | 2 | 3 | +1 | Longer |
| ZRH→DEL | Zurich – Delhi | 2 | 3 | +1 | Longer |
| MUC→DEL | Munich – Delhi | 2 | 3 | +1 | Longer |
| LHR→HYD | London – Hyderabad | 2 | 3 | +1 | Longer |
| FRA→CCU | Frankfurt – Kolkata | 2 | 3 | +1 | Longer |
| VIE→DEL | Vienna – Delhi | 2 | 3 | +1 | Longer |

**Real-World Cost Translation:**
- Additional flight time: **+1 to +3 hours per flight**
- Extra fuel usage: **5–30 tonnes per widebody aircraft per leg**
- Additional cost: **$5,000–$20,000 per flight**
- Across hundreds of affected flights per day → **millions of dollars in daily global operational costs**

**The LCC Paradox:**
> The LCC drop appears negligible (<1%) — but this masks the real-world impact entirely. The removed nodes and edges lie on the highest-traffic Europe–Asia corridors, so even removing <2% of edges affects hundreds of flights daily.

**Rerouting Effects & Non-Local Disruption:**

When Iranian airspace closes, traffic does not simply detour through adjacent airports. The **entire global shortest-path structure reorganises**. Airports gaining the most strategic importance post-closure:

| Airport | Betweenness Gain |
|---------|-----------------|
| GRU (São Paulo) | Highest — long-haul rerouting redistributes traffic to South America |
| CGB (Cuiabá) | High — South American hub absorbed into global paths |
| DEN (Denver) | High — North American hub |
| YYC (Calgary) | High |
| LIM (Lima) | Moderate |
| FRA (Frankfurt) | Moderate — absorbs additional European segment traffic |

> **Closure of 43 Iranian airports shifts strategic importance to airports in Canada, Brazil, and Peru — thousands of kilometres away.**

**Progressive Removal Analysis:**
- When Iranian airports are removed one by one in descending degree order, the impact is **front-loaded**
- **IKA (Tehran Imam Khomeini International)** causes by far the largest single drop in LCC and the largest spike in average path length
- Each subsequent Iranian airport removed has progressively smaller marginal impact — reflecting their lower degree and betweenness

---

## Key Findings Summary

| D# | Deliverable | Core Finding |
|----|-------------|--------------|
| D1 | Network Construction | 3,257 airports, 37,042 routes, density 0.00349, avg. clustering ≈ 0.488, LCC = 99.2%. Sparse but highly connected small-world network. |
| D2 | Scale-Free Analysis | Power-law exponent α = 1.068 (R² = 0.729) — heavy-tailed but non-canonical scale-free. Gini = 0.722 confirms strong inequality in connectivity. |
| D3 | Centrality | DXB, CDG, LAX, and PEK dominate betweenness. Low-degree nodes (e.g., ANC) act as key geographic bridges. Centrality is not strictly correlated with degree. |
| D4 | Robustness | Random f_c ≈ 0.40; targeted f_c ≈ 0.10. Network is ~4× more resilient to random failures. Strong hub-based structural dependency confirmed. |
| D5 | Disruption Simulation | Iran closure removes 43 airports and 382 routes with negligible LCC drop (99.20% → 99.19%). However, rerouting increases travel distance, fuel usage, and congestion — small structural change leads to large operational impact. |

**Overarching Conclusion:**
> The global aviation network is a **sparse, small-world, hub-dominated system** that is **structurally robust but operationally fragile**. A disruption affecting less than 2% of the network's nodes can impose millions of dollars in daily global operational costs.

---

## Implications for Aviation Resilience

1. **Hub-Centric Security Strategy** — Protecting a small number of high-betweenness nodes (DXB, CDG, IST, FRA, LAX) provides 4× the resilience benefit compared to uniform resource distribution
2. **Structural vs. Operational Robustness** — High LCC does not imply operational efficiency. Disruptions must be evaluated in terms of fuel cost, travel time, and congestion — not just connectivity
3. **Pre-Computed Contingency Routing** — Airlines should maintain pre-filed alternative routing strategies (northern polar routes via IST, GYD, TBS; southern bypass via Arabian Sea) for major geopolitical corridors
4. **Capacity Planning for Rerouting Corridors** — Alternative bypass corridors must be capable of handling sudden traffic surges; invest in slot capacity and ATC bandwidth at IST, GYD, DOH
5. **Graduated Restriction Policies** — Partial airspace operations (Scenario C: 80% weight reduction) are significantly less disruptive than full NOTAMs. Advocate for partial operational windows over full closures

---

## Installation & Usage

### Prerequisites

```bash
Python 3.8+
```

### Install Dependencies

```bash
pip install networkx pandas numpy scipy matplotlib
```

### Run the Analysis

```bash
# D1 — Network Construction
python deliverables/d1_network_construction.py

# D2 — Scale-Free Analysis
python deliverables/d2_scale_free_analysis.py

# D3 — Centrality Analysis (computationally intensive — allow ~10–15 min)
python deliverables/d3_centrality_analysis.py

# D4 — Robustness Analysis
python deliverables/d4_robustness_analysis.py

# D5 — Disruption Simulation
python deliverables/d5_disruption_simulation.py
```

### Data Setup

Download the OpenFlights data files and place them in the `data/` directory:
- https://raw.githubusercontent.com/jpatokal/openflights/master/data/airports.dat
- https://raw.githubusercontent.com/jpatokal/openflights/master/data/routes.dat

---

## Dependencies

| Library | Version | Purpose |
|---------|---------|---------|
| `networkx` | ≥ 2.8 | Graph construction, centrality, percolation simulations |
| `pandas` | ≥ 1.4 | Data preprocessing and tabular output |
| `numpy` | ≥ 1.21 | Numerical computation |
| `scipy` | ≥ 1.8 | Statistical analysis and power-law fitting |
| `matplotlib` | ≥ 3.5 | All visualisations and figure generation |

---

## Limitations & Future Work

**Current Limitations:**
- **Dataset vintage:** OpenFlights primarily reflects pre-2014 data. Post-COVID route restructuring, carrier consolidations, and new hub developments (DXB Al Maktoum, DOH Hamad expansion) are not captured
- **Edge weight proxy:** Airline count is a coarse traffic proxy. A production-grade analysis would use IATA OAG data with seat capacity, load factors, and frequency
- **Static disruption model:** Real disruptions are dynamic — congestion propagates, airlines reschedule, slots are reallocated. A dynamic model would yield more realistic impact estimates
- **Intermodal connections absent:** High-speed rail (e.g., European rail as a substitute during Middle East disruptions affecting connecting passengers) is not modelled

**Future Work:**
- **Graph Neural Networks (GNNs)** to predict which airports become critical bridges under novel disruption scenarios — enabling proactive resilience planning
- **Time-evolving graph analysis** to study how the network's scale-free properties and critical hubs have shifted seasonally and in response to geopolitical events
- **Dynamic congestion modelling** with real-time NOTAM integration and airline schedule optimisation
- **Intermodal network extension** incorporating rail and shipping to assess full multimodal substitution capacity

---

## References

1. Barabási, A.-L., & Albert, R. (1999). Emergence of scaling in random networks. *Science, 286*(5439), 509–512.
2. Albert, R., Jeong, H., & Barabási, A.-L. (2000). Error and attack tolerance of complex networks. *Nature, 406*, 378–382.
3. Guimerà, R., & Amaral, L. A. N. (2004). Modeling the world-wide airport network. *European Physical Journal B, 38*(2), 381–385.
4. Barrat, A., Barthélemy, M., Pastor-Satorras, R., & Vespignani, A. (2004). The architecture of complex weighted networks. *PNAS, 101*(11), 3747–3752.
5. Watts, D. J., & Strogatz, S. H. (1998). Collective dynamics of "small-world" networks. *Nature, 393*, 440–442.
6. Newman, M. E. J. (2010). *Networks: An Introduction.* Oxford University Press.
7. Brandes, U. (2001). A faster algorithm for betweenness centrality. *Journal of Mathematical Sociology, 25*(2), 163–177.
8. Kleinberg, J. M. (1999). Authoritative sources in a hyperlinked environment. *Journal of the ACM, 46*(5), 604–632.
9. Page, L., Brin, S., Motwani, R., & Winograd, T. (1999). The PageRank citation ranking. *Stanford InfoLab Technical Report.*
10. OpenFlights.org (2023). OpenFlights Airport, Airline, and Route Database. https://openflights.org/data.html
11. Hagberg, A., Swart, P., & Schult, D. (2008). Exploring network structure, dynamics, and function using NetworkX. *Proceedings of the 7th Python in Science Conference*, 11–15.
12. Clauset, A., Shalizi, C. R., & Newman, M. E. J. (2009). Power-law distributions in empirical data. *SIAM Review, 51*(4), 661–703.

---

*Tools used: Python · NetworkX · Matplotlib · SciPy · Pandas*
*Dataset: OpenFlights (airports.dat, routes.dat)*
