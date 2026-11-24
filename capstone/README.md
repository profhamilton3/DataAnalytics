# DataAnalytics: Capstone Readme

I have drafted your capstone proposal in [Capstone Study: Board Game Public Datasets & Business Questions](https://docs.google.com/document/d/1uW4x0ZhO0Zkl-KXbhLtV_rSUQWmnGg5p800fPGmnJuk/edit?usp=drivesdk&ouid=105609114454019307452) with verified Kaggle links, dataset commentary, 15 decision-oriented business questions, and a cross-dataset synergy framework.

### Link Verification Notes

* **4 Verified Dataset Links:** The first four Kaggle links (`andrewmvd/board-games`, `gabrio/board-games-dataset`, `mrpantherson/board-game-data`, and `jingking/boardgaming-online-processed-game-records`) are active, public Kaggle datasets.
* **5th Link Clarification & Replacement:** `https://www.kaggle.com/code/jahysama/quick-eda-in-r` points to a **Kaggle Code notebook** (an R exploratory analysis script) rather than a dataset. To give you five full public datasets, I included [Board Game Database from BoardGameGeek (threnjen)](https://www.kaggle.com/datasets/threnjen/board-games-database-from-boardgamegeek) as Dataset 5, adding 19 million user-level ratings to complement the catalog and gameplay datasets.

---

### Recommended Public Datasets & Business Questions

#### 1. [Board Games Dataset (andrewmvd)](https://www.kaggle.com/datasets/andrewmvd/board-games)

* **Commentary:** A clean, 14-column CSV (`bgg_dataset.csv`) covering ~20,300 ranked BoardGameGeek (BGG) titles with mechanics, domains, complexity (weight), player counts, play time, ratings, and ownership counts. Ideal for fast regression modeling and market segmentation of commercially visible games, though it omits unranked long-tail titles and individual user reviews.
* **Business Questions & Decisions:**
1. **Complexity vs. Commercial Reach:** How does rules complexity (weight) correlate with both average rating and copies owned across primary game domains (Strategy, Family, Party), and where is the optimal complexity sweet spot? *(Decision: Set target complexity guardrails during greenlighting for mass-market retail vs. hobbyist releases.)*
2. **Player-Count Flexibility:** Do games supporting scalable player counts (e.g., 1–5+ players, including solo modes) achieve higher ownership and rating stability across short (<45 min), medium (45–90 min), and long (>90 min) play-time tiers than rigid player-count titles? *(Decision: Allocate R&D budget toward solo/2-player modes and modular scenario lengths.)*
3. **Mechanic Pairings:** Which paired board game mechanics (e.g., Deck Building + Worker Placement vs. Area Control + Dice Rolling) generate statistically higher ratings and ownership than single-mechanic games in the same release decade? *(Decision: Prioritize high-synergy mechanic combinations in design briefs.)*



#### 2. [Board Games Dataset (gabrio)](https://www.kaggle.com/datasets/gabrio/board-games-dataset)

* **Commentary:** A relational SQLite database (`database.sqlite`) from BGG containing normalized tables for games, categories, mechanics, designers, publishers, artists, board game families, and expansions. Best suited for SQL joins, creator pedigree analysis, and product-line lifecycle modeling.
* **Business Questions & Decisions:**
1. **Expansion Pipeline ROI:** How do base games with two or more published expansions compare in total owned copies, wishlist demand, and average ratings against standalone games of similar initial complexity and release year? *(Decision: Determine when to fund post-launch expansion pipelines versus shifting studio capacity to new IP.)*
2. **Creator Track Record Lift:** Does partnering with multi-title designers or established artists yield a measurable lift in ownership-to-rating conversion compared to first-time creators within the same publisher tier? *(Decision: Guide talent acquisition, licensing advances, and marketing allocation.)*
3. **Publisher Specialization vs. Generalism:** Do specialized publishers focused on a narrow set of themes/categories achieve higher average BGG rankings per release than generalist publishers? *(Decision: Inform brand positioning and specialized imprint creation.)*



#### 3. [Board Game Data (mrpantherson)](https://www.kaggle.com/datasets/mrpantherson/board-game-data)

* **Commentary:** A broad 20-column catalog dataset (~81,312 entries) from BGG that includes tens of thousands of unranked, indie, and niche games alongside mainstream hits. Eliminates survivorship bias and excels at analyzing market saturation and minimum viability thresholds, though it requires cleaning sparse records.
* **Business Questions & Decisions:**
1. **Commercial Visibility Thresholds:** What structural attributes (minimum age, player-count span, play time, category breadth) separate games that cross into ranked visibility (>30–50 user ratings) from those that stall in obscurity? *(Decision: Establish pre-launch screening criteria and community review-seeding targets.)*
2. **Category Saturation Trends:** How has annual release supply grown across major categories (Wargames, Card Games, Abstract, Party), and in which categories has supply growth compressed average ratings or median ownership? *(Decision: Shift publishing roadmaps away from overcrowded genres toward underserved categories.)*
3. **Age-Rating Positioning:** How do youth-oriented games (ages 6–10) compare against teen/adult titles (ages 12+ and 14+) in median ownership, play duration, and mechanic density? *(Decision: Optimize box age-grading and family vs. hobby retail channel placement.)*



#### 4. [Boardgaming Online Processed Game Records (jingking)](https://www.kaggle.com/datasets/jingking/boardgaming-online-processed-game-records)

* **Commentary:** Empirical playthrough logs from the Boardgaming-Online platform capturing session outcomes, player counts, factions/roles, final scores, turn counts, and match durations. Uniquely captures live gameplay telemetry rather than static catalog metadata.
* **Business Questions & Decisions:**
1. **Faction & Seat-Order Balance:** Do specific starting positions, turn orders, or asymmetric factions exhibit statistically significant win-rate or score advantages across 2-, 3-, and 4-player matches? *(Decision: Issue targeted balance patches, bidding handicaps, or rules errata.)*
2. **Player-Count Scaling & Blowout Risk:** How do session duration and score margin scale from 2 to 5+ players, and at what play-time threshold do lopsided blowouts disproportionately occur? *(Decision: Tune round limits and recommended player-count labels on packaging.)*
3. **Score Closeness & Retention:** How do narrow-margin finishes compare against runaway victories in driving repeat match participation among the same player cohorts? *(Decision: Refine catch-up mechanics and end-game scoring triggers to boost replayability.)*



#### 5. [Board Game Database from BoardGameGeek (threnjen)](https://www.kaggle.com/datasets/threnjen/board-games-database-from-boardgamegeek)

* **Commentary:** A large-scale relational dataset (~22,000 games, 411,000+ users, and 19 million individual user ratings) paired with decomposed lookup tables for mechanics, themes, and subcategories. Unlocks rating-polarization analysis, cohort taste evolution, and collaborative filtering.
* **Business Questions & Decisions:**
1. **Consensus Hits vs. Polarizing Cult Classics:** Which mechanics and themes produce high mean ratings with low variance (mass consensus) versus high-variance/bimodal distributions (niche cult hits)? *(Decision: Route low-variance consensus games to mass retail and high-variance titles to targeted crowdfunding campaigns.)*
2. **User Cohort Taste Progression:** How do complexity, play-time, and theme preferences shift as reviewers progress from casual buyers (<10 rated games) to power hobbyists (100+ rated games)? *(Decision: Build tiered product ladders that onboard entry-level players into heavier titles.)*
3. **Cross-Game Affinity & Bundling:** Which pairs of games exhibit the strongest positive co-rating overlap among the same users across different themes or publishers? *(Decision: Power recommendation engines, retail shelf adjacency, and cross-promotional bundles.)*



---

### Overlapping & Supporting Cross-Dataset Business Decisions

| Combined Datasets | Shared Join Keys / Granularity | Overlapping & Supporting Business Decision |
| --- | --- | --- |
| **mrpantherson + andrewmvd + gabrio** | BGG Game ID / Title across unranked catalog, ranked performers, and creator/expansion tables | **Three-Stage Greenlight Pipeline:** Screen out saturated categories (`mrpantherson`), optimize target complexity and mechanic pairings (`andrewmvd`), and select proven designers with expansion roadmaps (`gabrio`). |
| **jingking + andrewmvd + threnjen** | Title matching linking live match telemetry to aggregate and user-level ratings | **Playtest-to-Market Validation:** Verify whether tighter faction win-rate balance and closer end-game score margins (`jingking`) directly reduce rating polarization (`threnjen`) and increase ownership (`andrewmvd`) before physical print runs. |
| **threnjen + gabrio + andrewmvd** | BGG Game ID linking 19M user ratings to designer/publisher entities and complexity domains | **Targeted Crowdfunding & Recommendation Engine:** Identify exact buyer cohorts likely to back a designer's next release or buy an expansion based on historical rating affinity across mechanics and complexity tiers. |

Would you like to narrow this down to a specific capstone focus area (e.g., recommender systems, publisher portfolio optimization, or game-balance telemetry) and outline the Python/SQL data pipeline and modeling plan?
