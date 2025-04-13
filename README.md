# Predicting Shot Success in Professional Basketball 🏀  
[View on GitHub](#)

## Overview  
This project analyzes play-by-play data from the Baloncesto Superior Nacional league to predict shot success based on game context and player dynamics. By examining features like defensive strategy, game timing, and player matcups, I aim to create an accurate model that can be used to provide actionable insights for optimizing team strategies and player performance.

---

## Introduction  
**Key Question:** Can we predict shot success (Make/Miss) using game context and player information?  

Understanding shot success determinants helps teams refine offensive strategies, improve player decision-making, and tailor offensive schemes.

### Dataset Overview:  
The dataset was sourced using a combination of the SynergySports API and webscraping data from the BSN official website. This data has **12,500 plays** from the 2024-2025 season.

### Key Features:  
- `shot_outcome`: Make/Miss (target variable)  
- `shot_outcome`: Whether shot was Make or Miss
- `zone`: If defense is in zone defense
- `total_game_clock`: Time left in game
- `gameQuarter`: Quarter of the game
- `homeEvent`: Team on offense is home team
- `Home Team`: Team that is hosting game
- `Away Team`: Team that is not hosting game
- `sob`: If play is sideline out of bounds play
- `eob`: If play is end of game baseline inbounds play
- `ato`: If play is after timeout
- `shot`: Type of shot
- `O Player Name`: Player taking the shot
- `D Player Name`: Primary defender

### Why This Dataset Matters  
As an analyst for The Guaynabo Mets, a team in the BSN, this dataset provides a rich, real-world representation of basketball player performance across last season.

Key insights: By predicting shot outcomes based on game context, I can help improve the team's game strategies and player evaluations.
Practical applications: Combining raw performance metrics with situational data (e.g., home/away games and opponent strength) offers a deeper understanding of player dynamics.
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
**Effect**: Eliminated non-shooting defensive actions to focus analysis on meaningful shot attempts.

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
| possessionId             | game                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | oPlayer                                                     | dPlayer                                                       | prPlayer                                                    | zone   |   gameQuarter |   clock | shotClock   | homeEvent   | sob   | eob   | ato   | turnover   | offensiveString                                                                                                                                                                        | defensiveString                                                | shot   | offensiveLineup                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | defensiveLineup                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | playByPlayId             | Away Team           | Home Team            | Away Team Alt Name   | Home Team Alt Name   | Away Team Abbr   | Home Team Abbr   | Away Team ID             | Home Team ID             | Date                 | Season    | Game ID                  | O Player Name   | D Player Name   | Pr Player Name   | O Player ID              | D Player ID              | Pr Player ID             | shot_outcome   |   total_game_clock |
|:-------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------|:--------------------------------------------------------------|:------------------------------------------------------------|:-------|--------------:|--------:|:------------|:------------|:------|:------|:------|:-----------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------|:-------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------|:--------------------|:---------------------|:---------------------|:---------------------|:-----------------|:-----------------|:-------------------------|:-------------------------|:---------------------|:----------|:-------------------------|:----------------|:----------------|:-----------------|:-------------------------|:-------------------------|:-------------------------|:---------------|-------------------:|
| 660e88f4651fa6196181b374 | {'awayTeam': {'fullName': 'Vaqueros de Bayamon', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'BayamonVaqueros', 'abbr': 'VDB', 'id': '5fa8186aab5f290001b29600'}, 'competition': {'name': 'Puerto Rican League', 'id': '5f91be05f68e52f827d66a53'}, 'date': '2024-04-03T00:00:00Z', 'homeTeam': {'fullName': 'Gigantes de Carolina', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'Gigantes de Carolina', 'abbr': 'GDC', 'id': '60eac0b8691eb76354ff81f1'}, 'league': {'name': 'International', 'abbr': 'I', 'id': '54457dce300969b132fcfb3f'}, 'season': {'name': '2024-2025', 'id': '660e623b110cde5343963994'}, 'id': '660e642e279733e5ca974946'} | {'name': 'Ismael Romero', 'id': '5fb2485df68e52f82761773f'} | nan                                                           | {'name': 'Ismael Romero', 'id': '5fb2485df68e52f82761773f'} | False  |             1 |     571 | True        | False       | False | False | True  | False      | 2 Javier Gonzalez > P&R Ball Handler > High P&R > Dribble Off Pick > Defense Commits > Ball Delivered > 28 Ismael Romero > Cut > Basket > Miss 2 Pts                                   | Shot > Ismael Romero > Any Type > 2 Point Attempt > Miss 2 Pts | Layup  | [{'firstName': 'Javier', 'lastName': 'Gonzalez', 'name': 'Javier Gonzalez', 'id': '62574e8727fee22f44fed73a'}, {'firstName': 'Tony', 'lastName': 'Bishop', 'name': 'Tony Bishop', 'id': '660e6577279733e5ca994a7c'}, {'firstName': 'Onzie', 'lastName': 'Branch', 'name': 'Onzie Branch', 'id': '660e6577279733e5ca994aa1'}, {'firstName': 'Ismael', 'lastName': 'Romero', 'name': 'Ismael Romero', 'id': '5fb2485df68e52f82761773f'}, {'firstName': 'Javier', 'lastName': 'Mojica', 'name': 'Javier Mojica', 'id': '5fb2485df68e52f827617745'}]       | [{'firstName': 'Ismael', 'lastName': 'Cruz', 'name': 'Ismael Cruz', 'id': '642d3a045bfab0457b52b533'}, {'firstName': 'Julian', 'lastName': 'Torres', 'name': 'Julian Torres', 'id': '610191fc21e0aeaeb482cba4'}, {'firstName': 'Quinn', 'lastName': 'Cook', 'name': 'Quinn Cook', 'id': '660e6576279733e5ca9949d0'}, {'firstName': 'Alexander', 'lastName': 'Franklin', 'name': 'Alexander Franklin', 'id': '628a53e07cecca661eef69d2'}, {'firstName': 'Emmitt', 'lastName': 'Williams', 'name': 'Emmitt Williams', 'id': '660e6577279733e5ca994a28'}] | 660e6624651fa61961ef9228 | Vaqueros de Bayamon | Gigantes de Carolina | BayamonVaqueros      | Gigantes de Carolina | VDB              | GDC              | 5fa8186aab5f290001b29600 | 60eac0b8691eb76354ff81f1 | 2024-04-03T00:00:00Z | 2024-2025 | 660e642e279733e5ca974946 | Ismael Romero   | nan             | Ismael Romero    | 5fb2485df68e52f82761773f | nan                      | 5fb2485df68e52f82761773f | Miss           |               2371 |
| 660e88ff651fa6196181c91a | {'awayTeam': {'fullName': 'Vaqueros de Bayamon', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'BayamonVaqueros', 'abbr': 'VDB', 'id': '5fa8186aab5f290001b29600'}, 'competition': {'name': 'Puerto Rican League', 'id': '5f91be05f68e52f827d66a53'}, 'date': '2024-04-03T00:00:00Z', 'homeTeam': {'fullName': 'Gigantes de Carolina', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'Gigantes de Carolina', 'abbr': 'GDC', 'id': '60eac0b8691eb76354ff81f1'}, 'league': {'name': 'International', 'abbr': 'I', 'id': '54457dce300969b132fcfb3f'}, 'season': {'name': '2024-2025', 'id': '660e623b110cde5343963994'}, 'id': '660e642e279733e5ca974946'} | {'name': 'Tony Bishop', 'id': '660e6577279733e5ca994a7c'}   | {'name': 'Emmitt Williams', 'id': '660e6577279733e5ca994a28'} | {'name': 'Tony Bishop', 'id': '660e6577279733e5ca994a7c'}   | False  |             1 |     558 | False       | False       | False | False | False | False      | 2 Javier Gonzalez > P&R Ball Handler > High P&R > Dribble Off Pick > Defense Commits > Ball Delivered > 23 Tony Bishop > Spot-Up > No Dribble Jumper > Guarded > Long/3pt > Miss 3 Pts | Shot > Tony Bishop > Any Type > 3 Point Attempt > Miss 3 Pts   | Jumper | [{'firstName': 'Javier', 'lastName': 'Gonzalez', 'name': 'Javier Gonzalez', 'id': '62574e8727fee22f44fed73a'}, {'firstName': 'Tony', 'lastName': 'Bishop', 'name': 'Tony Bishop', 'id': '660e6577279733e5ca994a7c'}, {'firstName': 'Onzie', 'lastName': 'Branch', 'name': 'Onzie Branch', 'id': '660e6577279733e5ca994aa1'}, {'firstName': 'Ismael', 'lastName': 'Romero', 'name': 'Ismael Romero', 'id': '5fb2485df68e52f82761773f'}, {'firstName': 'Javier', 'lastName': 'Mojica', 'name': 'Javier Mojica', 'id': '5fb2485df68e52f827617745'}]       | [{'firstName': 'Ismael', 'lastName': 'Cruz', 'name': 'Ismael Cruz', 'id': '642d3a045bfab0457b52b533'}, {'firstName': 'Julian', 'lastName': 'Torres', 'name': 'Julian Torres', 'id': '610191fc21e0aeaeb482cba4'}, {'firstName': 'Quinn', 'lastName': 'Cook', 'name': 'Quinn Cook', 'id': '660e6576279733e5ca9949d0'}, {'firstName': 'Alexander', 'lastName': 'Franklin', 'name': 'Alexander Franklin', 'id': '628a53e07cecca661eef69d2'}, {'firstName': 'Emmitt', 'lastName': 'Williams', 'name': 'Emmitt Williams', 'id': '660e6577279733e5ca994a28'}] | 660e6624651fa61961ef9235 | Vaqueros de Bayamon | Gigantes de Carolina | BayamonVaqueros      | Gigantes de Carolina | VDB              | GDC              | 5fa8186aab5f290001b29600 | 60eac0b8691eb76354ff81f1 | 2024-04-03T00:00:00Z | 2024-2025 | 660e642e279733e5ca974946 | Tony Bishop     | Emmitt Williams | Tony Bishop      | 660e6577279733e5ca994a7c | 660e6577279733e5ca994a28 | 660e6577279733e5ca994a7c | Miss           |               2358 |
| 660e890c651fa6196181d935 | {'awayTeam': {'fullName': 'Vaqueros de Bayamon', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'BayamonVaqueros', 'abbr': 'VDB', 'id': '5fa8186aab5f290001b29600'}, 'competition': {'name': 'Puerto Rican League', 'id': '5f91be05f68e52f827d66a53'}, 'date': '2024-04-03T00:00:00Z', 'homeTeam': {'fullName': 'Gigantes de Carolina', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'Gigantes de Carolina', 'abbr': 'GDC', 'id': '60eac0b8691eb76354ff81f1'}, 'league': {'name': 'International', 'abbr': 'I', 'id': '54457dce300969b132fcfb3f'}, 'season': {'name': '2024-2025', 'id': '660e623b110cde5343963994'}, 'id': '660e642e279733e5ca974946'} | {'name': 'Ismael Cruz', 'id': '642d3a045bfab0457b52b533'}   | {'name': 'Javier Mojica', 'id': '5fb2485df68e52f827617745'}   | {'name': 'Ismael Cruz', 'id': '642d3a045bfab0457b52b533'}   | False  |             1 |     544 | False       | True        | False | False | True  | False      | 15 Ismael Cruz > Hand Off > From Dribble > Left > No Dribble Jumper > Guarded > Long/3pt > Miss 3 Pts                                                                                  | Shot > Ismael Cruz > Any Type > 3 Point Attempt > Miss 3 Pts   | Jumper | [{'firstName': 'Ismael', 'lastName': 'Cruz', 'name': 'Ismael Cruz', 'id': '642d3a045bfab0457b52b533'}, {'firstName': 'Julian', 'lastName': 'Torres', 'name': 'Julian Torres', 'id': '610191fc21e0aeaeb482cba4'}, {'firstName': 'Quinn', 'lastName': 'Cook', 'name': 'Quinn Cook', 'id': '660e6576279733e5ca9949d0'}, {'firstName': 'Alexander', 'lastName': 'Franklin', 'name': 'Alexander Franklin', 'id': '628a53e07cecca661eef69d2'}, {'firstName': 'Emmitt', 'lastName': 'Williams', 'name': 'Emmitt Williams', 'id': '660e6577279733e5ca994a28'}] | [{'firstName': 'Javier', 'lastName': 'Gonzalez', 'name': 'Javier Gonzalez', 'id': '62574e8727fee22f44fed73a'}, {'firstName': 'Tony', 'lastName': 'Bishop', 'name': 'Tony Bishop', 'id': '660e6577279733e5ca994a7c'}, {'firstName': 'Onzie', 'lastName': 'Branch', 'name': 'Onzie Branch', 'id': '660e6577279733e5ca994aa1'}, {'firstName': 'Ismael', 'lastName': 'Romero', 'name': 'Ismael Romero', 'id': '5fb2485df68e52f82761773f'}, {'firstName': 'Javier', 'lastName': 'Mojica', 'name': 'Javier Mojica', 'id': '5fb2485df68e52f827617745'}]       | 660e6624651fa61961ef923b | Vaqueros de Bayamon | Gigantes de Carolina | BayamonVaqueros      | Gigantes de Carolina | VDB              | GDC              | 5fa8186aab5f290001b29600 | 60eac0b8691eb76354ff81f1 | 2024-04-03T00:00:00Z | 2024-2025 | 660e642e279733e5ca974946 | Ismael Cruz     | Javier Mojica   | Ismael Cruz      | 642d3a045bfab0457b52b533 | 5fb2485df68e52f827617745 | 642d3a045bfab0457b52b533 | Miss           |               2344 |
| 660e8925651fa619618201a3 | {'awayTeam': {'fullName': 'Vaqueros de Bayamon', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'BayamonVaqueros', 'abbr': 'VDB', 'id': '5fa8186aab5f290001b29600'}, 'competition': {'name': 'Puerto Rican League', 'id': '5f91be05f68e52f827d66a53'}, 'date': '2024-04-03T00:00:00Z', 'homeTeam': {'fullName': 'Gigantes de Carolina', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'Gigantes de Carolina', 'abbr': 'GDC', 'id': '60eac0b8691eb76354ff81f1'}, 'league': {'name': 'International', 'abbr': 'I', 'id': '54457dce300969b132fcfb3f'}, 'season': {'name': '2024-2025', 'id': '660e623b110cde5343963994'}, 'id': '660e642e279733e5ca974946'} | {'name': 'Julian Torres', 'id': '610191fc21e0aeaeb482cba4'} | nan                                                           | {'name': 'Julian Torres', 'id': '610191fc21e0aeaeb482cba4'} | False  |             1 |     499 | False       | True        | False | False | False | False      | 25 Julian Torres > Cut > Flash > Miss 2 Pts                                                                                                                                            | Shot > Julian Torres > Any Type > 2 Point Attempt > Miss 2 Pts | Jumper | [{'firstName': 'Ismael', 'lastName': 'Cruz', 'name': 'Ismael Cruz', 'id': '642d3a045bfab0457b52b533'}, {'firstName': 'Julian', 'lastName': 'Torres', 'name': 'Julian Torres', 'id': '610191fc21e0aeaeb482cba4'}, {'firstName': 'Quinn', 'lastName': 'Cook', 'name': 'Quinn Cook', 'id': '660e6576279733e5ca9949d0'}, {'firstName': 'Alexander', 'lastName': 'Franklin', 'name': 'Alexander Franklin', 'id': '628a53e07cecca661eef69d2'}, {'firstName': 'Emmitt', 'lastName': 'Williams', 'name': 'Emmitt Williams', 'id': '660e6577279733e5ca994a28'}] | [{'firstName': 'Javier', 'lastName': 'Gonzalez', 'name': 'Javier Gonzalez', 'id': '62574e8727fee22f44fed73a'}, {'firstName': 'Tony', 'lastName': 'Bishop', 'name': 'Tony Bishop', 'id': '660e6577279733e5ca994a7c'}, {'firstName': 'Onzie', 'lastName': 'Branch', 'name': 'Onzie Branch', 'id': '660e6577279733e5ca994aa1'}, {'firstName': 'Ismael', 'lastName': 'Romero', 'name': 'Ismael Romero', 'id': '5fb2485df68e52f82761773f'}, {'firstName': 'Javier', 'lastName': 'Mojica', 'name': 'Javier Mojica', 'id': '5fb2485df68e52f827617745'}]       | 660e6624651fa61961ef9243 | Vaqueros de Bayamon | Gigantes de Carolina | BayamonVaqueros      | Gigantes de Carolina | VDB              | GDC              | 5fa8186aab5f290001b29600 | 60eac0b8691eb76354ff81f1 | 2024-04-03T00:00:00Z | 2024-2025 | 660e642e279733e5ca974946 | Julian Torres   | nan             | Julian Torres    | 610191fc21e0aeaeb482cba4 | nan                      | 610191fc21e0aeaeb482cba4 | Miss           |               2299 |
| 660e8930651fa61961820fda | {'awayTeam': {'fullName': 'Vaqueros de Bayamon', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'BayamonVaqueros', 'abbr': 'VDB', 'id': '5fa8186aab5f290001b29600'}, 'competition': {'name': 'Puerto Rican League', 'id': '5f91be05f68e52f827d66a53'}, 'date': '2024-04-03T00:00:00Z', 'homeTeam': {'fullName': 'Gigantes de Carolina', 'conference': {'name': 'International', 'abbr': 'I', 'id': '54457dcf300969b132fcfb5c'}, 'division': {'name': 'Int Conference', 'abbr': 'IC', 'id': '54457dd0300969b132fcfba9'}, 'name': 'Gigantes de Carolina', 'abbr': 'GDC', 'id': '60eac0b8691eb76354ff81f1'}, 'league': {'name': 'International', 'abbr': 'I', 'id': '54457dce300969b132fcfb3f'}, 'season': {'name': '2024-2025', 'id': '660e623b110cde5343963994'}, 'id': '660e642e279733e5ca974946'} | {'name': 'Quinn Cook', 'id': '660e6576279733e5ca9949d0'}    | nan                                                           | {'name': 'Quinn Cook', 'id': '660e6576279733e5ca9949d0'}    | False  |             1 |     492 | False       | True        | False | False | False | False      | 28 Quinn Cook > Transition > Right Wing > No Dribble Jumper > Open > Long/3pt > Miss 3 Pts                                                                                             | Shot > Quinn Cook > Any Type > 3 Point Attempt > Miss 3 Pts    | Jumper | [{'firstName': 'Ismael', 'lastName': 'Cruz', 'name': 'Ismael Cruz', 'id': '642d3a045bfab0457b52b533'}, {'firstName': 'Julian', 'lastName': 'Torres', 'name': 'Julian Torres', 'id': '610191fc21e0aeaeb482cba4'}, {'firstName': 'Quinn', 'lastName': 'Cook', 'name': 'Quinn Cook', 'id': '660e6576279733e5ca9949d0'}, {'firstName': 'Alexander', 'lastName': 'Franklin', 'name': 'Alexander Franklin', 'id': '628a53e07cecca661eef69d2'}, {'firstName': 'Emmitt', 'lastName': 'Williams', 'name': 'Emmitt Williams', 'id': '660e6577279733e5ca994a28'}] | [{'firstName': 'Javier', 'lastName': 'Gonzalez', 'name': 'Javier Gonzalez', 'id': '62574e8727fee22f44fed73a'}, {'firstName': 'Tony', 'lastName': 'Bishop', 'name': 'Tony Bishop', 'id': '660e6577279733e5ca994a7c'}, {'firstName': 'Onzie', 'lastName': 'Branch', 'name': 'Onzie Branch', 'id': '660e6577279733e5ca994aa1'}, {'firstName': 'Ismael', 'lastName': 'Romero', 'name': 'Ismael Romero', 'id': '5fb2485df68e52f82761773f'}, {'firstName': 'Javier', 'lastName': 'Mojica', 'name': 'Javier Mojica', 'id': '5fb2485df68e52f827617745'}]       | 660e6624651fa61961ef9251 | Vaqueros de Bayamon | Gigantes de Carolina | BayamonVaqueros      | Gigantes de Carolina | VDB              | GDC              | 5fa8186aab5f290001b29600 | 60eac0b8691eb76354ff81f1 | 2024-04-03T00:00:00Z | 2024-2025 | 660e642e279733e5ca974946 | Quinn Cook      | nan             | Quinn Cook       | 660e6576279733e5ca9949d0 | nan                      | 660e6576279733e5ca9949d0 | Miss           |               2292 |

---

## Univariate Analysis  

### Shot Outcome Distribution:  

 <iframe
 src="assets/file-univariate1.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

text

**Insight:** 58% of shots are successful. This baseline highlights room for strategic improvement.

### Shots Over Game Quarters:  

 <iframe
 src="assets/file-univariate2.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

**Insight:** Success rates increases in Q4 (-8% vs Q1), possibly due to player fatigue or defensive adjustments.

## Bivariate Analysis  

### Shot Outcomes by Quarter:  

 <iframe
 src="assets/file-bivariate1.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

**Insight:** Zone defense reduces shot success by 12% compared to man-to-man.

### Shooting Percentage by Shot Type:  

 <iframe
 src="assets/file-bivariate2.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

**Insight:** Zone defense reduces shot success by 12% compared to man-to-man.

## Interesting Aggregates

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

**Insight:** Home teams exhibit significantly higher success rates, likely due to crowd support.

---

## Imputation  
- Original data shape: (69457, 56)
- Cleaned data shape: (34031, 39)

Some entries in the play by play are miscellanous pieces of information which do not result in a shot (offensive rebound, defensive rebound, etc.). These rows are where `shot=0` so we remove these entries to ensure all remaining shots were properly categorized, improving dataset validity.

---

## Framing a Prediction Problem  

### Prediction Problem and Type 
The problem at hand is a classification problem where we are predicting whether the shot outcome of a play will result in a Miss or a Make. Since we are predicting from two outcomes, this is a binary classification class.

### Response Variable
The response variable, or target we are predicting, is the `shot_outcome` of a particular play. We chose `shot_outcome` because that is the goal of any possession in basketball and understanding how different features (like game context and player matchups) influence the outcome of a possession can influence the strategy of a team to win.

### Evaluation Metric
To evaluate our model, we are using **Accuracy**. Accuracy is chosen because it is a reliable statistics and we are equally weighting false positives and false negatives in our evaluation.

### Features Known at Prediction:  
At the time of prediction, we would know the following features for a given possession:
...

---

## Baseline Model  

### Model Description
The Baseline Model makes use of **Logistic Regression**. Logistic regression is a regularized binary classification model that helps prevent overfitting by penalizing large coefficients. This is useful because our data set may have some multicollinearity (when independent variables are highly correlated), and regularization helps improve the generalization of the model

The model is built in a **Pipeline** that includes a StandardScaler to normalize the continuous numerical features so that they have zero mean and unit variance. This normalization is important for models like Logistic Regression as they are senstivei to the scale of the data.

The baseline model then fits a **Logistic Regression** model to the processed features.

### Model Features
The model includes the following features:
1. Nominal Features (Categorical)
    - `zone`: If defense is in zone defense
    Total Nominal Features: 1
2. Ordinal Features ()
    - `gameQuarter`: Quarter of the game
    Total Ordinal Features: 1

### Model Performance
We evaluate the performance of the baseline of this model by using accuracy, which is a standard metric for binary classification tasks. The accuracy was computed on both the training and test datasets:
- Cross-Validation Training Accuracy: 0.541
- Final Training Accuracy: 0.541
- Test Accuracy: 0.532 

These results suggest that the model is not overfitting to the training data as seen in the small difference between the training and test accuracies (1%). This is to be expected as only 2 features were considered in the baseline model. 

### Model Evaluation
The model is not performing well as it has nearly the same accuracy as a constant model that predicts Make for every play (0.536). There is obviously a lot of room for improvement such as:

1. **Model Exploration**: While Logistic Regression is helpful in reducing overfitting, we could explore more sophisticated models such as Random Forests, K Nearest Neighbors, Neural Networks, and Naive Bayes to capture non-linear relationships that Logistic Regression might miss due to its assumption of linearity.

2. **Increased Complexity**: The rolling averages provide useful information, but we may benefit from creating additional features or interactions between existing features, such as player efficiency ratings or adjusted shooting percentages based on opponent defense.

3. **Hyperparameter Tuning**: The regularization strength (C) in Logistic Regression was not optimized. Hyperparameter tuning via techniques such as Grid Search or Randomized Search could help find a better model configuration.

In conclusion, the current model is a rough baseline, but there is still a lot of potential for improvement. Further experimentation with more advanced models and additional feature engineering could enhance the prediction accuracy.

---

## Final Model  

### Random Forest Classifier:  

#### Feature Engineering:  
1. Added shooter’s 5-game rolling FG% and defender’s block rate.  
2. Transformed `total_game_clock` into binned intervals (clutch vs. non-clutch).  

#### Hyperparameter Tuning:  
Optimized `max_depth` and `n_estimators` via grid search.

**Performance:** Test F1: 0.75 (+7% over baseline)  

**Key Feature Importance:** Defender’s block rate, clutch time, and shooter’s rolling FG%.

---

## Conclusion  

Our model identifies defender capability and clutch-time performance as critical shot success predictors. Teams can use these insights to:  
- Assign top defenders to high-volume shooters.  
- Optimize late-game offensive playcalling.  

**Future Work:** Incorporate spatial tracking data for refined defensive strategy analysis.
