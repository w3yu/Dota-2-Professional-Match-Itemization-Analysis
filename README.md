# Dota 2 Itemization Analysis

## Overview
This project investigates how itemization decisions—especially Black King Bar (BKB)—affect win rates in professional Dota 2 matches.

Rather than looking at global win rates, we focus on context-aware analysis, examining how item effectiveness varies depending on:
- Hero
- Enemy hero matchup
- Game conditions (implicitly captured through item availability)

---

## Data

The raw CSV files are not included in this repository because they exceed GitHub's file size limit.

Dataset source:
https://www.kaggle.com/datasets/bwandowando/dota-2-pro-league-matches-2023

Required files:
- 202603/players.csv
- 202603/picks_bans.csv
- 202603/radiant_gold_adv.csv

---

## Methodology

### 1. Data Processing (PySpark)
- Cleaned player-level match data
- Derived:
  - win (binary outcome)
  - has_bkb (whether BKB was purchased)
  - purchase (item purchase dictionary)
- Constructed team labels using player_slot

---

### 2. Matchup Construction
- Self-join on match_id
- Paired each hero with all enemy heroes
- Result:
  - Each row = (hero, enemy hero, match)

---

### 3. BKB Analysis

#### (1) Raw Win Rate with BKB
We computed:

win_rate_with_bkb = avg(win | has_bkb = True)

Limitation:
- Some heroes already have high win rates regardless of BKB
- Support heroes rarely buy BKB, introducing selection bias

---

#### (2) BKB Impact (Key Metric)
To address bias:

bkb_impact = win_rate_with_bkb - win_rate_without_bkb

We also filtered:
- >= 10 samples for both groups

This isolates relative improvement instead of absolute strength.

---

### 4. Matchup-Level Analysis
We extended analysis to:

(hero, enemy_hero)

Allowing us to study:
- When BKB is most valuable
- Matchup-dependent effectiveness

---

### 5. Item-Level Extension
Beyond BKB, we analyzed:

(hero, enemy_hero, item)

- Exploded item purchase dictionary
- Computed item-level win rates

---

## Key Findings

### 1. BKB win rate alone is misleading
- Many top heroes already have high baseline win rates
- High BKB win rate does not imply causation

---

### 2. BKB impact varies by matchup
- Some matchups show strong improvement (> +0.5)
- Others show minimal or even negative impact

---

### 3. Sample size matters
- Small samples produce extreme values (e.g., 100% win rate)
- Filtering is necessary for reliability

---

### 4. Item effectiveness is context-dependent
- Certain items are optimal only in specific matchups
- Itemization is situational rather than globally optimal

---

## Limitations

### 1. Correlation vs Causation
We cannot claim BKB causes higher win rate because:
- Players with more gold are more likely to buy BKB
- Winning teams naturally have better item progression

This introduces economic confounding bias.

---

### 2. Missing Features
We did not explicitly control for:
- Net worth / gold advantage
- Game duration
- Player skill

---

### 3. Sparse Matchups
- Many hero–enemy combinations have limited data
- Requires threshold filtering

---

## Future Work

- Control for economic factors (e.g., net worth bins)
- Analyze item combinations
- Compare across patches or months
- Apply machine learning models to estimate causal effects

---

## How to Run

1. Load dataset into Spark
2. Run notebook:
   notebook/dota_item_analysis.ipynb
3. Generate figures from processed data

---

## Motivation
Itemization in Dota 2 is highly strategic and context-dependent.  
This project aims to provide data-driven insights into when specific items are truly effective.
