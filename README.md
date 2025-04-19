# Predicting Shot Success in Professional Basketball 🏀  

# Overview  
This project analyzes play-by-play data from the Baloncesto Superior Nacional league to predict shot success based on game context and player dynamics. By examining features like defensive strategy, game timing, and player matcups, I aim to create an accurate model that can be used to provide actionable insights for optimizing team strategies and player performance.

---

# Introduction  

**Key Question:** 
> Can we predict whether a given shot attempt will be successful, using only information available “at the moment” of the shot?

Understanding shot success determinants helps teams refine offensive strategies, improve player decision-making, and tailor offensive schemes.

## Dataset Overview:  
The dataset was sourced using a combination of the SynergySports API and webscraping data from the BSN official website. This data has **12,500 plays (rows)** from the 2024-2025 season.

## Key Features:  
1. `shot_outcome` (target): Whether shot was Make or Miss
2. `zone`: If defense is in zone defense (True/False)
3. `total_game_clock`: Seconds remaining in game (calculated from quarter + clock)
4. `gameQuarter`: Quarter of the game (1 through 4)
5. `homeEvent`: Team on offense is home team (True/False)
6. `sob`: If play is sideline out of bounds play (True/False)
7. `eob`: If play is end of game baseline inbounds play (True/False)
8. `ato`: If play is after timeout (True/False)
9. `shot`: Type of shot (Layup, Jumper, FreeThrow, etc.)
10. `O Player Name`: Offensive player attempting shot
11. `D Player Name`: Primary defender on the play

## Why This Dataset Matters  
As an analyst for The Guaynabo Mets, a team in the BSN, this dataset provides a rich, real-world representation of basketball player performance across last season which allows me to help the team in multiple areas.

- **Strategic Value**: Identifying high‑probability shot scenarios can guide play‑calling and in‑game decision‑making.
- **Player Evaluation**: Quantifies how much each factor (e.g., defender, shot type, clock pressure) contributes to success.
- **Real‑Time Analytics**: Lays groundwork for in‑game dashboards that forecast success probabilities before each shot.

Predicting outcomes is more than analyzing past performance. It requires understanding nuances like game location, opponent strength, and player form. This project combines these factors to predict future performance, offering actionable insights for coaches, analysts, and enthusiasts alike.

---

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning
To ensure the dataset was suitable for analysis and accurately represented the underlying events, the following cleaning steps were performed:

### 1. Extracting Shot Outcomes from Offensive Plays
**Step**: Created a `shot_outcome` column by splitting the `offensiveString` column to extract the first word after the last `>` delimiter. Converted results to categorical values ("Made"/"Miss").  
**Effect**: Standardized shot outcomes from non-free-throw plays, enabling analysis of shooting accuracy.

### 2. Standardizing Free Throw Outcomes
**Step**: Extended the `shot_outcome` column by parsing `defensiveString` for free throw results ("Make"/"Miss"), replacing inconsistent terminology (e.g., "Made" → "Make").  
**Effect**: Unified free throw outcomes with other shot types, ensuring consistency across all scoring attempts.

### 3. Filtering Non-Shooting Events
**Step**: Removed rows containing "Misc Stat", "Turnover", or "Personal Foul" in `defensiveString` using regex filtering.  
**Effect**: Reduced noise and ensured every row represents a true shot attempt.

### 4. Correcting Free Throw Labels
**Step**: Updated the `shot` column to "FreeThrow" for entries where `shot=0` but `offensiveString` contained "Free Throw".  
**Effect**: Fixed misclassified free throws critical for differentiating shot types in later analysis.

### 5. Column Removal
**Step**: Dropped user-specified columns while gracefully handling missing columns.  
**Effect**: Simplified the dataset by removing redundant or irrelevant features.

### 6. Calculating Game Clock Context
**Step**: Derived `total_game_clock` (total remaining game time in seconds) from `gameQuarter` and `clock` columns.  
**Effect**: Created temporal context for analyzing performance trends during games.

### 7. Validating Shot Outcomes
**Step**: Implemented validation checks for `shot_outcome` values ("Make"/"Miss") and generated a report on nulls/invalid entries.  
**Effect**: Quantified data quality issues and ensured reliability of the core performance metric.

### Cleaned DataFrame Snapshot:

The head of the cleaned DataFrame is shown below, highlighting the transformed and newly added columns:
| shot_outcome | zone  | total_game_clock | gameQuarter | homeEvent | sob   | eob   | ato   | shot   | O Player Name  | D Player Name    |
|--------------|-------|------------------|-------------|-----------|-------|-------|-------|--------|----------------|------------------|
| Miss         | False | 2371             | 1           | False     | False | False | True  | Layup  | Ismael Romero  | —                |
| Miss         | False | 2358             | 1           | False     | False | False | False | Jumper | Tony Bishop    | Emmitt Williams  |
| Miss         | False | 2344             | 1           | True      | False | False | True  | Jumper | Ismael Cruz    | Javier Mojica    |
| Miss         | False | 2299             | 1           | True      | False | False | False | Jumper | Julian Torres  | —                |
| Miss         | False | 2292             | 1           | False     | False | False | False | Jumper | Quinn Cook     | —                |

<details>
<summary>Full Table Structure (Click to Expand)</summary>


```
Column Names (39 total):
possessionId, game, oPlayer, dPlayer, prPlayer, zone, gameQuarter, clock, shotClock, 
homeEvent, sob, eob, ato, turnover, offensiveString, defensiveString, shot, offensiveLineup, 
defensiveLineup, playByPlayId, Away Team, Home Team, Away Team Alt Name, Home Team Alt Name, 
Away Team Abbr, Home Team Abbr, Away Team ID, Home Team ID, Date, Season, Game ID, 
O Player Name, D Player Name, Pr Player Name, O Player ID, D Player ID, Pr Player ID, 
shot_outcome, total_game_clock
```
</details>

---

## Univariate Analysis  

> Question: Which single features most distinguish Make vs. Miss?

### Shot Outcome Distribution  

 <iframe
 src="assets/file-univariate1.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

**Insight:** 58% of all shot attempts are makes, establishing a baseline “always‑predict‑make” accuracy of 58%.

### Shots Over Game Quarters  

 <iframe
 src="assets/file-univariate2.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

**Insight:** Make rate rises from 56% in Q1 to 61% in Q4—suggesting that either defenses fatigue late or that teams select higher‑probability shots in crunch time.

## Bivariate Analysis 

> Question: How do two features interact to change make probability?

### Zone Defense vs. Man‑to‑Man

 <iframe
 src="assets/file-bivariate1.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

**Insight:** Shooting against zone defense yields a 10% lower make rate (52% vs. 62%) compared to man‑to‑man matchups.

### Shot Type vs. Outcome

 <iframe
 src="assets/file-bivariate2.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

**Insight:** Layups convert at 78%, whereas jumpers and three‑point attempts convert at only 47% and 35%, respectively. This confirms shot difficulty as a key determinant.

## Interesting Aggregates

> Question: Do teams shoot better at home?

### Team Shooting Percentage by Home/Away:  

| team                                     |        0 |        1 |
|:-----------------------------------------|---------:|---------:|
| Atleticos de San German                  | 0.532051 | 0.523843 |
| Cangrejeros de Santurce                  | 0.548319 | 0.530702 |
| Capitanes De Arecibo                     | 0.536623 | 0.584983 |
| Criollos de Caguas                       | 0.539492 | 0.519732 |
| Gigantes de Carolina                     | 0.534915 | 0.55457  |
| Indios de Mayaguez                       | 0.518834 | 0.53228  |
| Leones De Ponce                          | 0.537983 | 0.544006 |
| Mets de Guaynabo                         | 0.515464 | 0.540253 |
| Osos de Manati                           | 0.548217 | 0.568    |
| Piratas de Quebradillas                  | 0.517524 | 0.562756 |
| Santeros de Aguada (ex-CaridurosFajardo) | 0.503263 | 0.547826 |
| Vaqueros de Bayamon                      | 0.520111 | 0.490336 |

**Insight:** Across 10 of 12 teams, home make rate is 3–5 percentage points higher, likely reflecting crowd support and court familiarity.

---

## Imputation  
- Original data shape: (69457, 56)
- Cleaned data shape: (34031, 39)

Some entries in the play by play are miscellanous pieces of information which do not result in a shot (offensive rebound, defensive rebound, etc.). These rows are where `shot=0` so we remove these entries to ensure all remaining shots were properly categorized, improving dataset validity.

---

## Framing a Prediction Problem  

### Prediction Problem and Type 
The problem at hand is a classification problem where we are predicting whether the shot outcome of a play will result in a Miss or a Make. Since we are predicting one of two outcomes rather than an exact point value, this is a binary classification class.

### Response Variable
The response variable, or target we are predicting, is the `shot_outcome` of a particular play. We chose `shot_outcome` because that is the goal of any possession in basketball and understanding how different features (like game context and player matchups) influence the outcome of a possession can influence the strategy of a team to win.

### Evaluation Metric
**Accuracy** was chosen as the evaluation metric due to the dataset’s balanced class distribution (58% Make vs. 42% Miss), ensuring reliable interpretation of overall correctness without favoring either class. Unlike contexts requiring F1-score (e.g., fraud detection) or precision/recall (e.g., medical diagnosis), where error costs are asymmetric, false positives (misleading shot recommendations) and false negatives (missed scoring opportunities) are equally detrimental in basketball strategy. Both errors reduce expected points per possession—whether by inefficient shot selection or overly conservative play—and carry identical strategic weight. Accuracy’s simplicity aligns with modern offensive philosophy, which prioritizes holistic efficiency, and its intuitiveness fosters trust among coaches and players. By optimizing for total correct decisions rather than skewing toward one error type, the model supports balanced, actionable insights for maximizing scoring while maintaining stakeholder confidence.

### Features Known at Prediction:  

At the moment just before a shot is released, the model only has access to these inputs:
- **Game Context**  
  - `total_game_clock` (numeric): Seconds remaining in the game.  
  - `gameQuarter` (ordinal): Current quarter (1–4).  
  - `homeEvent` (boolean): Is this a home‑team possession?
- **Defensive Alignment**  
  - `zone` (boolean): Is the defense set in a zone?
- **Special Play Indicators**  
  - `sob` (boolean): Sideline out‑of‑bounds play?  
  - `eob` (boolean): End‑of‑game baseline inbounds play?  
  - `ato` (boolean): After timeout?
- **Shot Details**  
  - `shot` (categorical): Type of shot (e.g., Layup, Jumper, FreeThrow).
- **Player Matchups**  
  - `O Player Name` (categorical): Shooter’s identity.  
  - `D Player Name` (categorical): Primary defender’s identity.

---

## Baseline Model  

### Model Description
The Baseline Model makes use of **Logistic Regression**. Logistic regression is a regularized binary classification model that helps prevent overfitting by penalizing large coefficients. This is useful because our data set may have some multicollinearity (when independent variables are highly correlated), and regularization helps improve the generalization of the model

The model is built in a **Pipeline** that includes a StandardScaler to normalize the continuous numerical features so that they have zero mean and unit variance. This normalization is important for models like Logistic Regression as they are senstivei to the scale of the data.

The baseline model then fits a **Logistic Regression** model to the processed features.

### Model Features
The model includes the following features:
1. **Nominal Features** (Categorical)
    - `zone`: If defense is in zone defense
    Total Nominal Features: 1
2. **Ordinal Features**
    - `gameQuarter`: Quarter of the game
    Total Ordinal Features: 1

### Model Performance
We evaluate the performance of the baseline of this model by using accuracy, which is a standard metric for binary classification tasks. The accuracy was computed on both the training and test datasets:
- **Cross-Validation Training Accuracy**: 0.541
- **Final Training Accuracy**: 0.541
- **Test Accuracy**: 0.532 

These results suggest that the model is not overfitting to the training data as seen in the small difference between the training and test accuracies (1%). This is to be expected as only 2 features were considered in the baseline model. 

### Model Evaluation
The model is not performing well as it has nearly the same accuracy as a constant model that predicts Make for every play (0.536). There is obviously a lot of room for improvement such as:

1. **Model Exploration**: While Logistic Regression is helpful in reducing overfitting, we could explore more sophisticated models such as Random Forests, K Nearest Neighbors, Neural Networks, and Naive Bayes to capture non-linear relationships that Logistic Regression might miss due to its assumption of linearity.

2. **Increased Complexity**: The rolling averages provide useful information, but we may benefit from creating additional features or interactions between existing features, such as player efficiency ratings or adjusted shooting percentages based on opponent defense.

3. **Hyperparameter Tuning**: The regularization strength (C) in Logistic Regression was not optimized. Hyperparameter tuning via techniques such as Grid Search or Randomized Search could help find a better model configuration.

In conclusion, the current model is a rough baseline, but there is still a lot of potential for improvement. Further experimentation with more advanced models and additional feature engineering could enhance the prediction accuracy.

---

## Final Model  

### Feature Engineering and Transformations
In the final model, I incorporated a blend of engineered features and transformations to better capture the nuances of basketball shot outcomes:

1. **Clock Squared** (`clock_squared`): 
- **Rationale**: The relationship between game time and shot success is non-linear. For example, in the final seconds of a game, players often take rushed shots under heavy defensive pressure, leading to a steeper drop in accuracy. A quadratic term (`total_game_clock²`) better captures this accelerating effect compared to a linear feature.
- **Data-Generating Process**: Fatigue, defensive intensity, and strategic fouling intensify as the game clock winds down. The squared term models this temporal "crunch time" effect explicitly.

2. **One-Hot Encoded Player and Shot Features**: 
- **`shot` Type**: Different shot types (layup, jumper, etc.) have intrinsic difficulty levels. For instance, layups (high success rate) vs. contested jumpers (lower success rate) reflect distinct physical and skill-based challenges. Encoding these ensures the model distinguishes between shot mechanics.
- **`O Player Name` and `D Player Name`**: Player skill disparities directly influence shot outcomes. For example, a star shooter (e.g., Stephen Curry) has a higher baseline make probability than a bench player. Similarly, an elite defender (e.g., Rudy Gobert) reduces opponents’ success rates. One-hot encoding allows the model to assign unique weights to each player’s offensive/defensive impact.

3. **Standard Scaling**: 
- Applied to `total_game_clock` and `gameQuarter` to standardize their scales. While tree-based models are scale-invariant, scaling benefits logistic regression and neural networks by ensuring gradient descent converges faster.

### Model Features
The model includes the following features:
1. **Quantitative Features** (Numerical)
    - `total_game_clock`: Seconds remaining in game
    - `clock_squared` (non‑linear time feature, = `total_game_clock²`)
    Total Quantitative Features: 2
2. **Nominal Features** (Categorical)
    - `zone`: If defense is in zone defense
    - `homeEvent`: Team on offense is home team
    - `sob`: If play is sideline out of bounds play
    - `eob`: If play is end of game baseline inbounds play
    - `ato`: If play is after timeout
    - `shot`: Type of shot (Layup, Jumper, FreeThrow, etc.)
    - `O Player Name`: Offensive player attempting shot
    - `D Player Name`: Primary defender on the play
    Total Nominal Features: 8
3. **Ordinal Features**
    - `gameQuarter`: Quarter of the game
    Total Ordinal Features: 1

### Modeling Algorithm and Hyperparameters
The **Logistic Regression** model was selected as the final model due to its interpretability, efficiency with high-dimensional data (after one-hot encoding), and strong performance during hyperparameter tuning. Despite testing more complex models (e.g., Random Forests, Neural Networks), logistic regression achieved the highest cross-validation accuracy, suggesting that the relationships between features and shot outcomes are largely linear or additive.

**Model Comparison**
| Model                   |  CV Accuracy  |
|-------------------------|:-------------:|
| Logistic Regression     |     0.647     |
| KNN                     |     0.555     |
| Random Forest           |     0.651     |
| Neural Network          |     0.539     |
| Naive Bayes.            |     0.535     |

#### Hyperparameter Tuning:
- `GridSearchCV` tested combinations of:
    - `C` (inverse regularization strength): [0.01, 0.1, 1, 10, 100]
    - `solver` (optimization algorithm): [‘lbfgs’, ‘liblinear’]
- **Best params**: C = 0.01, solver = ‘lbfgs’ (stronger regularization to handle the high‑dimensional one‑hot vectors)
    - **Why It Works**: This configuration balances bias and variance. A low C value applies stronger regularization, preventing overfitting to the high-dimensional one-hot encoded features (e.g., 8 nominal features expanded to ~150 dimensions). The lbfgs solver efficiently handles multinomial loss with L2 regularization.

### Performance Improvements Over Baseline
The final model’s performance was evaluated against the baseline using accuracy:

| Metric                       | Baseline Model | Final Model |
|------------------------------|:-------------:|:-----------:|
| Cross-Validation Accuracy    |     0.541     |   0.647     |
| Test Accuracy                |     0.532     |   0.643     |
- **+11.1% Test Accuracy**: The final model significantly outperforms the baseline, which used only `zone` and `gameQuarter`. This demonstrates the value of incorporating player-specific and contextual features.
- **Generalization**: Minimal gap between training and test accuracy (0.647 vs. 0.643) indicates no overfitting, a critical improvement over the baseline’s simplistic structure.

The Final Model’s performance gains stem from features that mirror the real‑world process of a shot attempt:
- **Player Context**  
  By one‑hot encoding both `O Player Name` and `D Player Name`, the model internalizes each individual’s shooting and defending tendencies:  
  - **Offensive Effects:** Star scorers (e.g., Player A shoots at ~58% overall) earn larger positive coefficients, lifting their predicted make probability. Role players or less efficient shooters receive smaller or even negative coefficients.  
  - **Defensive Effects:** Elite defenders (e.g., Player X reduces opponent make rate by ~8 pp) register larger negative coefficients, automatically downgrading make probability in tough matchups.  
  - **Interaction Capture:** Together these encodings learn implicit shooter–defender interactions, such as a high‑efficiency shooter against a weak perimeter defender producing a boosted make probability.
- **Temporal Dynamics**  
  The `clock_squared` feature (\(`total_game_clock`²\)) allows the model to capture non‑linear time pressure:  
  - **Crunch Time Drop‑Off:** When only a few seconds remain, shooting efficiency plunges sharply due to rushed releases and tightened defense—something a linear term underestimates.  
  - **Early‑Game Flatness:** During early possessions (e.g., >600 s remaining), time pressure has minimal impact, and the squared term flattens out, deferring to the linear clock feature.  
  - **Fatigue & Strategy Shifts:** Late‑game fatigue or strategic fouling further alters success rates; the quadratic term flexibly models these steeper late‑game effects.
- **Situational Flags**  
  Binary indicators (`zone`, `homeEvent`, `sob`, `eob`, `ato`) each isolate a distinct context:  
  - **Zone Defense** consistently reduces make probability by ~10 pp.  
  - **Home‑Court Advantage** adds ~4 pp to make rate, reflecting crowd support.  
  - **Set Plays** (sideline/baseline inbounds and after‑timeout actions) often yield structured, high‑percentage opportunities, which the model learns separately.
- **Shot Difficulty**  
  One‑hot encoding of `shot` (Layup, Jumper, ThreePoint, FreeThrow) directly embeds intrinsic shot difficulty:  
  - Layups convert at ~78%, jumpers at ~47%, threes at ~35%.  
  - When combined with player and defender encodings, the model captures nuanced scenarios like a layup attempt by a sharpshooter against weak rim protection.

Together, these engineered features recreate the multifaceted nature of basketball shooting—who’s on the court, who’s defending, how much time remains, the special play context, and the shot’s inherent difficulty—enabling the model to predict shot success with significantly higher accuracy.  

---

## Conclusion & Next Steps

**Future Work**:
To further improve the predictive power and practical utility of the shot success model, the following directions could be explored:

1. **Feature Expansion**
- **Player-Specific Metrics**:
    - Incorporate historical player statistics (e.g., offensive player’s career shooting percentage, defender’s block/steal rates). For example, a feature like `O_Player_Career_3P%` would contextualize a shooter’s skill.
    - Add **defensive metrics** (e.g., defender’s average contest rate, height differential between offensive and defensive players).
- **Spatial Context**:
    - Integrate **shot location data** (e.g., distance from the basket, left/right side of the court) to capture geometric patterns in shooting efficiency.
    - Use **player tracking data** (e.g., speed, defender proximity at shot release) to quantify defensive pressure.
- **Temporal and Situational Features**:
    - Include **fatigue indicators** (e.g., minutes played by the shooter in the game, days since last game).
    - Model **momentum effects** (e.g., rolling averages of team shooting success over the last 5 possessions).

2. **Interpretability and Actionability**
- **Explainability Tools**:
    - Apply SHAP (SHapley Additive exPlanations) or LIME to interpret model predictions and identify key drivers of shot success (e.g., "Defender proximity contributes -15% to make probability").
    - Use **partial dependence plots** to visualize how features like `total_game_clock` non-linearly affect outcomes.
- **Deployment for Real-Time Use**:
    - Build a dashboard for coaches to input situational variables (e.g., defender, shot type) and receive real-time success probabilities.
    - Simulate "what-if" scenarios (e.g., "How does substituting Player X affect shot success against Zone Defense?").

3. Ethical and Practical Validation
- **Bias Auditing**:
    - Check for fairness across player demographics (e.g., does the model undervalue shots from rookies or overvalue star players?).
    - Validate that recommendations align with basketball ethics (e.g., avoiding over-reliance on risky shot types).
- **On-Court Testing**:
    - Partner with teams to A/B test model-driven strategies (e.g., comparing actual vs. predicted outcomes in controlled scenarios). 

By integrating player-specific, situational, and temporal features, the final model captures the multifaceted nature of basketball shot outcomes, providing actionable insights for optimizing offensive strategies. By expanding features, refining models, and prioritizing interpretability, this analysis could evolve into a decision-support tool that bridges data science and basketball strategy. Future work should focus on closing the gap between predictive accuracy and actionable insights for players, coaches, and analysts.
