# Quizapalooza: Fox News’ *The Quiz* - A Fictional NYC Adventure

## Overview
Welcome to *Quizapalooza*! 🚨 This is a silly, data-driven joyride imagining Fox News’ *The Quiz* hitting the streets of all five NYC boroughs—not just Manhattan’s fancy sidewalks. Built with **Python 🐍**, **RStudio 📊**, and a hefty dose of fake data (no real New Yorkers were quizzed or bruised), this project tracks fictional answer accuracy at popular spots. Think of it as a corporate team-building exercise gone wild—except it’s just me, some code, and a dream. No liability waivers needed!

---

## Table of Contents
- [Datasets](#datasets)
- [Code](#code)
  - [Python Script](#python-script)
  - [R Scripts](#r-scripts)
- [Setup Instructions](#setup-instructions)
- [How to Manually Create .csv Files](#how-to-manually-create-csv-files)
- [Why It Matters (Sort Of)](#why-it-matters-sort-of)
- [Conclusion](#conclusion)
- [License](#license)

---

## Datasets
Here are the fake `.csv` files fueling this quiz-tastic farce:

1. **`fox_quiz_data.csv`**:
   - **Columns**: `Episode_ID`, `Date`, `Respondent_ID`, `Correct_Answers`, `Incorrect_Answers`, `Percentage_Correct`
   - **Rows**: 50 (10 episodes, 5 respondents each)
   - **Description**: Simulates a mini-season of *The Quiz* with random New Yorkers. Spoiler: They’re not all Jeopardy champs.

2. **`quiz_opinion.csv`**:
   - **Columns**: `Region`, `Good`, `Dumb`
   - **Rows**: 4 (U.S. regions)
   - **Description**: Fake poll on whether listeners think *The Quiz* questions are “good” or “just plain dumb.” Northeast says “dumb,” South says “yeehaw!”

3. **`quiz_borough_data.csv`**:
   - **Columns**: `Borough`, `Episodes`, `Total_Answers`, `Correct_Answers`, `Incorrect_Answers`, `Percent_Correct`, `Percent_Incorrect`
   - **Rows**: 5 (NYC boroughs)
   - **Description**: Borough-by-borough breakdown of a fake year’s quiz answers. Manhattan shines, Staten Island naps.

4. **`quiz_spots_data.csv`**:
   - **Columns**: `Location`, `Borough`, `Latitude`, `Longitude`, `Episodes`, `Total_Answers`, `Correct_Answers`, `Percent_Correct`
   - **Rows**: 10 (2 spots per borough)
   - **Description**: Pinpoints fake quiz accuracy at NYC hotspots. Times Square aces it, Historic Richmond Town… not so much.

---

## Code

### Python Script

#### `generate_quiz_data.py` (For `fox_quiz_data.csv`)
```python
import pandas as pd
import numpy as np
from faker import Faker
import random

fake = Faker()
np.random.seed(42)

def generate_quiz_data(episodes=260, people_per_episode=5, questions_per_person=5):
    dates = [fake.date_between(start_date='-1y', end_date='today') for _ in range(episodes)]
    data = {
        'Episode_ID': [f"EP{str(i).zfill(3)}" for i in range(1, episodes+1) for _ in range(people_per_episode)],
        'Date': [d for d in dates for _ in range(people_per_episode)],
        'Respondent_ID': [f"R{str(i).zfill(4)}" for i in range(1, episodes * people_per_episode + 1)],
        'Correct_Answers': [random.randint(0, questions_per_person) for _ in range(episodes * people_per_episode)]
    }
    df = pd.DataFrame(data)
    df['Incorrect_Answers'] = questions_per_person - df['Correct_Answers']
    df['Percentage_Correct'] = (df['Correct_Answers'] / questions_per_person) * 100
    return df

quiz_df = generate_quiz_data(episodes=10)  # Short version for README
quiz_df.to_csv('fox_quiz_data.csv', index=False)
print("Generated 'fox_quiz_data.csv'")
```

### R Scripts

#### 1. `quiz_analysis.R` (Pie & Trend for `fox_quiz_data.csv`)
```R
if (!require(ggplot2)) install.packages("ggplot2"); library(ggplot2)
if (!require(dplyr)) install.packages("dplyr"); library(dplyr)
if (!require(plotly)) install.packages("plotly"); library(plotly)

setwd("C:/Users/Veteran")
csv_file <- "fox_quiz_data.csv"
if (!file.exists(csv_file)) stop("Error: fox_quiz_data.csv not found")

quiz <- read.csv("quiz_data.csv")
quiz$Date <- as.Date(quiz$Date, format="%Y-%m-%d")

totals <- quiz %>% 
  summarise(Total_Correct = sum(Correct_Answers), Total_Incorrect = sum(Incorrect_Answers)) %>%
  tidyr::pivot_longer(cols = everything(), names_to = "Type", values_to = "Count")
pie_plot <- ggplot(totals, aes(x = "", y = Count, fill = Type)) +
  geom_bar(stat = "identity", width = 1) +
  coord_polar("y") +
  theme_void() +
  labs(title = "Fox News 'The Quiz': Correct vs Incorrect Answers") +
  scale_fill_manual(values = c("Total_Correct" = "green", "Total_Incorrect" = "red")) +
  theme(plot.title = element_text(hjust = 0.5, size = 16, face = "bold"))

print("Displaying pie chart:")
print(pie_plot)

daily_trend <- quiz %>% 
  group_by(Date) %>% 
  summarise(Avg_Percent_Correct = mean(Percentage_Correct))

trend_plot <- ggplot(daily_trend, aes(x = Date, y = Avg_Percent_Correct)) +
  geom_line(color = "blue") +
  geom_point(color = "blue", size = 1) +
  theme_minimal() +
  labs(title = "Daily Average Accuracy on 'The Quiz'", x = "Date", y = "Average % Correct") +
  scale_x_date(date_breaks = "1 month", date_labels = "%b %Y") +
  theme(plot.title = element_text(hjust = 0.5, size = 16, face = "bold"), axis.text.x = element_text(angle = 45, hjust = 1))

print("Displaying trend plot:")
print(trend_plot)

trend_plotly <- ggplotly(trend_plot, tooltip = c("x", "y"))
print("Displaying interactive trend plot:")
print(trend_plotly)

ggsave("quiz_pie.png", pie_plot, width = 6, height = 6, dpi = 300)
ggsave("quiz_trend.png", trend_plot, width = 10, height = 6, dpi = 300)
htmlwidgets::saveWidget(trend_plotly, "quiz_trend.html")
cat("Saved as 'quiz_pie.png', 'quiz_trend.png', and 'quiz_trend.html'\n")
```

#### 2. `quiz_3d_pie.R` (3D Pie for `fox_quiz_data.csv`)
```R
if (!require(dplyr)) install.packages("dplyr"); library(dplyr)
if (!require(plotrix)) install.packages("plotrix"); library(plotrix)

setwd("C:/Users/Veteran")
csv_file <- "fox_quiz_data.csv"
if (!file.exists(csv_file)) stop("Error: fox_quiz_data.csv not found")

quiz <- read.csv("fox_quiz_data.csv")

totals <- quiz %>% 
  summarise(Total_Correct = sum(Correct_Answers), Total_Incorrect = sum(Incorrect_Answers))

counts <- c(totals$Total_Correct, totals$Total_Incorrect)
labels <- c("Correct", "Incorrect")
colors <- c("green", "red")

total_answers <- sum(counts)
percent_correct <- round((totals$Total_Correct / total_answers) * 100, 1)
percent_incorrect <- round((totals$Total_Incorrect / total_answers) * 100, 1)

pie3D(counts, 
      labels = paste(labels, ": ", c(percent_correct, percent_incorrect), "%", sep=""), 
      col = colors, explode = 0.1, theta = pi/3, main = "Fox News 'The Quiz': Correct vs Incorrect Answers (3D)", 
      height = 0.1, labelcex = 1.2)

png("quiz_3d_pie.png", width = 600, height = 600, res = 100)
pie3D(counts, 
      labels = paste(labels, ": ", c(percent_correct, percent_incorrect), "%", sep=""), 
      col = colors, explode = 0.1, theta = pi/3, main = "Fox News 'The Quiz': Correct vs Incorrect Answers (3D)", 
      height = 0.1, labelcex = 1.2)
dev.off()

cat("Saved as 'quiz_3d_pie.png'\n")
```

#### 3. `quiz_opinion_visual.R` (Stacked Bar for `quiz_opinion.csv`)
```R
if (!require(ggplot2)) install.packages("ggplot2"); library(ggplot2)
if (!require(dplyr)) install.packages("dplyr"); library(dplyr)
if (!require(tidyr)) install.packages("tidyr"); library(tidyr)

setwd("C:/Users/Veteran")
csv_file <- "quiz_opinion.csv"
if (!file.exists(csv_file)) stop("Error: quiz_opinion.csv not found")

opinion <- read.csv("quiz_opinion.csv")
opinion_long <- opinion %>% 
  pivot_longer(cols = c("Good", "Dumb"), names_to = "Opinion", values_to = "Count")

opinion_summary <- opinion_long %>% 
  group_by(Region) %>% 
  mutate(Total = sum(Count), Percent = round((Count / Total) * 100, 1)) %>%
  ungroup()

bar_plot <- ggplot(opinion_summary, aes(x = Region, y = Count, fill = Opinion)) +
  geom_bar(stat = "identity", position = "stack") +
  geom_text(aes(label = paste0(Percent, "%")), position = position_stack(vjust = 0.5), size = 4, color = "white", fontface = "bold") +
  scale_fill_manual(values = c("Good" = "blue", "Dumb" = "orange")) +
  theme_minimal() +
  labs(title = "Public Opinion on 'The Quiz' Questions by U.S. Region", 
       subtitle = "Fake Poll: Are the Questions Good or Just Plain Dumb?", x = "Region", y = "Number of Listeners", fill = "Opinion") +
  theme(plot.title = element_text(hjust = 0.5, size = 16, face = "bold"), plot.subtitle = element_text(hjust = 0.5, size = 12), legend.position = "top")

print("Displaying stacked bar chart:")
print(bar_plot)

ggsave("quiz_opinion_bar.png", bar_plot, width = 8, height = 6, dpi = 300)
cat("Saved as 'quiz_opinion_bar.png'\n")
```

#### 4. `quiz_borough_visual.R` (Grouped Bar for `quiz_borough_data.csv`)
```R
if (!require(ggplot2)) install.packages("ggplot2"); library(ggplot2)
if (!require(dplyr)) install.packages("dplyr"); library(dplyr)
if (!require(tidyr)) install.packages("tidyr"); library(tidyr)

setwd("C:/Users/Veteran")
csv_file <- "quiz_borough_data.csv"
if (!file.exists(csv_file)) stop("Error: quiz_borough_data.csv not found")

boroughs <- read.csv("quiz_borough_data.csv")
boroughs_long <- boroughs %>% 
  select(Borough, Percent_Correct, Percent_Incorrect) %>%
  pivot_longer(cols = c("Percent_Correct", "Percent_Incorrect"), names_to = "Answer_Type", values_to = "Percentage") %>%
  mutate(Answer_Type = recode(Answer_Type, "Percent_Correct" = "Correct", "Percent_Incorrect" = "Incorrect"))

bar_plot <- ggplot(boroughs_long, aes(x = Borough, y = Percentage, fill = Answer_Type)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_text(aes(label = paste0(Percentage, "%")), position = position_dodge(width = 0.9), vjust = -0.5, size = 4, fontface = "bold") +
  scale_fill_manual(values = c("Correct" = "green", "Incorrect" = "red")) +
  theme_minimal() +
  labs(title = "Fox News 'The Quiz': Answer Accuracy by NYC Borough", 
       subtitle = "Fake Data: 1 Year Across All 5 Boroughs", x = "Borough", y = "Percentage (%)", fill = "Answer Type") +
  theme(plot.title = element_text(hjust = 0.5, size = 16, face = "bold"), plot.subtitle = element_text(hjust = 0.5, size = 12), legend.position = "top", axis.text.x = element_text(angle = 45, hjust = 1))

print("Displaying grouped bar chart:")
print(bar_plot)

ggsave("quiz_borough_bar.png", bar_plot, width = 8, height = 6, dpi = 300)
cat("Saved as 'quiz_borough_bar.png'\n")
```

#### 5. `quiz_spots_map.R` (Terrain Map for `quiz_spots_data.csv`)
```R
if (!require(ggplot2)) install.packages("ggplot2"); library(ggplot2)
if (!require(ggmap)) install.packages("ggmap"); library(ggmap)
if (!require(viridis)) install.packages("viridis"); library(viridis)

register_stadiamaps("9c644007-0572-4892-915a-8da356fe40ae")
setwd("C:/Users/Veteran")
csv_file <- "quiz_spots_data.csv"
if (!file.exists(csv_file)) stop("Error: quiz_spots_data.csv not found")

spots <- read.csv("quiz_spots_data.csv")
nyc_bbox <- c(left = -74.25, bottom = 40.5, right = -73.7, top = 40.9)
nyc_terrain <- get_stadiamap(bbox = nyc_bbox, maptype = "stamen_terrain", zoom = 11)
if (is.null(nyc_terrain)) stop("Error: Failed to load Stadia Maps terrain data.")

map_plot <- ggmap(nyc_terrain) +
  geom_point(data = spots, aes(x = Longitude, y = Latitude, size = Percent_Correct, color = Percent_Correct), alpha = 0.8, shape = 16) +
  scale_size_continuous(range = c(5, 15), name = "Correct (%)") +
  scale_color_viridis_c(option = "viridis", direction = -1, name = "Correct (%)") +
  geom_text(data = spots, aes(x = Longitude, y = Latitude, label = Location), vjust = -1.5, size = 3.5, color = "black", fontface = "bold") +
  theme_minimal() +
  labs(title = "Fox News 'The Quiz': Accuracy at NYC Hotspots", subtitle = "Fake Data: Correct Answers by Borough Location", x = "Longitude", y = "Latitude") +
  theme(plot.title = element_text(hjust = 0.5, size = 16, face = "bold"), plot.subtitle = element_text(hjust = 0.5, size = 12), legend.position = "right")

print("Displaying terrain map:")
print(map_plot)

ggsave("quiz_spots_map.png", map_plot, width = 10, height = 8, dpi = 300)
cat("Saved as 'quiz_spots_map.png'\n")
```

---

## Setup Instructions

### Prerequisites
- **Python**: `pip install pandas numpy faker`
- **R**: Install RStudio and packages:
  ```R
  install.packages(c("ggplot2", "dplyr", "plotly", "plotrix", "tidyr", "ggmap", "viridis"))
  ```
- **Stadia Maps API**: Use key `9c644007-0572-4892-915a-8da356fe40ae` (or grab your own at [Stadia Maps](https://stadiamaps.com/)).

### Steps
1. **Clone the Repo**:
   ```bash
   git clone https://github.com/yourusername/quizapalooza.git
   cd quizapalooza
   ```
2. **Generate `fox_quiz_data.csv`**:
   - Run `python generate_quiz_data.py` (or manually create—see below).
3. **Manually Create Other `.csv` Files**:
   - See instructions below.
4. **Visualize**:
   - Run each `.R` script in RStudio (`Ctrl+Alt+R`).
   - Outputs land in `C:/Users/Veteran`.

---

## How to Manually Create .csv Files
No Python? No problem! Channel your inner data entry intern:
1. **Open Notepad** (or any text editor—yes, even that dusty one from 1998).
2. **Copy-Paste** the `.csv` content below.
3. **Save As**: Use `.csv` extension, place in `C:/Users/Veteran`.
4. **Repeat**: For each file. Coffee optional, existential crisis guaranteed.

- **`fox_quiz_data.csv`** (short sample):
  ```csv
  Episode_ID,Date,Respondent_ID,Correct_Answers,Incorrect_Answers,Percentage_Correct
  EP001,2024-03-01,R0001,3,2,60.0
  EP001,2024-03-01,R0002,4,1,80.0
  EP001,2024-03-01,R0003,2,3,40.0
  EP001,2024-03-01,R0004,5,0,100.0
  EP001,2024-03-01,R0005,1,4,20.0
  ```
- **`quiz_opinion.csv`**:
  ```csv
  Region,Good,Dumb
  Northeast,100,150
  South,175,75
  Midwest,125,125
  West,135,115
  ```
- **`quiz_borough_data.csv`**:
  ```csv
  Borough,Episodes,Total_Answers,Correct_Answers,Incorrect_Answers,Percent_Correct,Percent_Incorrect
  Manhattan,52,1300,845,455,65.0,35.0
  Brooklyn,52,1300,780,520,60.0,40.0
  Queens,52,1300,715,585,55.0,45.0
  The Bronx,52,1300,650,650,50.0,50.0
  Staten Island,52,1300,585,715,45.0,55.0
  ```
- **`quiz_spots_data.csv`**:
  ```csv
  Location,Borough,Latitude,Longitude,Episodes,Total_Answers,Correct_Answers,Percent_Correct
  Times Square,Manhattan,40.7580,-73.9855,26,650,455,70.0
  Central Park,Manhattan,40.7812,-73.9654,26,650,390,60.0
  Coney Island,Brooklyn,40.5755,-73.9707,26,650,358,55.0
  Brooklyn Bridge,Brooklyn,40.7061,-73.9969,26,650,423,65.0
  Flushing Meadows,Queens,40.7397,-73.8407,26,650,325,50.0
  Astoria Park,Queens,40.7760,-73.9250,26,650,293,45.0
  Yankee Stadium,The Bronx,40.8296,-73.9262,26,650,390,60.0
  Bronx Zoo,The Bronx,40.8506,-73.8754,26,650,325,50.0
  Staten Island Ferry,Staten Island,40.6437,-74.0719,26,650,260,40.0
  Historic Richmond Town,Staten Island,40.5710,-74.1460,26,650,228,35.0
  ```

---

## Why It Matters (Sort Of)
In the grand corporate scheme, does this matter? Nah—but it’s hilarious:
- **Team Morale**: Pie charts and terrain maps beat TPS reports any day. HR approves!
- **Watercooler Cred**: “Did you see Staten Island’s 35% at Richmond Town? LOL” Instant office legend.
- **Skill Flex**: Python, R, and fake data—proof I can crunch numbers while dodging real work.
- **No Lawsuits**: All fake, no New Yorkers sued us for calling them “dumb.” Legal says “phew!”

---

## Conclusion
*Quizapalooza* is a love letter to data nerds and NYC chaos, wrapped in fake stats and zero stakes. No one was hurt—except maybe my dignity after 17 coffee runs to finish this. Clone it, laugh at it, add your own borough flops. Let’s keep the quiz vibes alive—without the corporate retreat budget!

---

## License
MIT License—because even fake data deserves freedom. See `LICENSE` file.

---

## Suggested Repo Name
**`quizapalooza`**  
- Catchy, fun, and screams “this ain’t serious.” Perfect for GitHub glory!

---

### Notes
- **Humor**: Corporate jabs (TPS reports, HR) keep it light. No real harm emphasized!
- **Manual `.csv`**: Instructions are snarky but clear—Notepad warriors unite.
- **Personalize**: Swap `yourusername` in the clone URL with your GitHub handle (share it if you want it plugged in!).
- **Structure**: Add all `.py`, `.R`, `.csv`, and outputs (`.png`, `.html`) to `/outputs` for polish.

What’s your GitHub username so I can tweak the clone link? Any extra zingers for the README? 😄 Ready to push this to the repo hall of fame?
