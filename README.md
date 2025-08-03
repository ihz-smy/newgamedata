# Game market data EDA 2013-2019
## Project Background and Overview
This analysis examines the game market in the 2013-2019 period.  
The dataset contains detailed information on 100,000 video games. It includes metadata like ratings, release dates, platforms, genres, and publishers.  
Top platforms are 'PC', 'Web', 'macOS', 'Linux', 'Android'. Top genres are 'Action', 'Platformer', 'Adventure', 'Puzzle', 'Shooter'.  
The KPIs we are investigating are these metrics: games released, average metacritic, average rating, average suggested count. Recommendations will be used by R&D team to better understand trends, market gaps and to better allocate resources for future projects.  

## Project Goals  
1. Evaluate the trends to see if there are any market gaps
2. Identify patterns in higher rated games to imrpove future projects
3. Provide recommendations for business growth through strategic R&D decisions

## Data Cleaning
1. Check the actual dataset
```
df.head()
```

2. Dropping null columns and unused columns
```
df.drop(df.columns[df.columns.str.contains('unnamed', case=False)], axis=1, inplace=True)
df.drop(columns = ['slug', 'website', 'image', 'about'], inplace=True)
```

3. Dropping null id and duplicated id
```
df.dropna(subset='id', inplace=True)
df = df.drop_duplicates(subset='id', keep='first')
```

4. Check the dtype of columns and convert so the format is consistent
```
df['metacritic'].apply(type).value_counts()
df['metacritic'] = pd.to_numeric(df['metacritic'], errors='coerce')
```

5. Convert columns 'platforms', 'genres', 'developers', 'publishers' to list and explode to a new bridge table
```
df['platforms'] = df['platforms'].astype(str)
df['platforms'] = df['platforms'].apply(lambda x: x.split('||'))
platforms_bridge = df[['id', 'platforms']].explode('platforms')
platforms_bridge['platforms'] = platforms_bridge['platforms'].replace('nan',pd.NA)
platforms_bridge.dropna(subset = 'platforms', inplace=True)
platforms_dim = platforms_bridge['platforms'].drop_duplicates().reset_index(drop=True).to_frame()
platforms_dim['PlatformID'] = platforms_dim.index + 1
```

## Data structure overview
- Records: The dataset includes metadata like ratings, release dates, platforms, genres, and publishers
- Genres: The unique genres dimension table, each game can have more than 1 genres
- Platforms: The unique platforms dimension table, each game can have more than 1 platform
- Developers: The unique developers dimension table, each game can have more than 1 developer
- Publishers: The unique publishers dimension table, each game can have more than 1 publisher
<img width="1720" height="791" alt="image" src="https://github.com/user-attachments/assets/cf150570-92f7-47f2-95c7-6f006a515f72" />  

## Executive Summary
From 2013 to 2019, all top platforms 'PC', 'Web', 'macOS', 'Linux', 'Android' had a positive trend line as the game market expanded significantly with PC dominating the market with 70k+ games released followed by Web while macOS, Linux and Android did not expand as much, ranging from 5k to 25.5k. For genres, Action was the lead at all times with 20k games released while Adventure, Platformer, Puzzle and Shooter were more mixed with each other, ranging from 9k to 12.5k. The ratings of each genres were not too different but the average suggestions count of Adventure at 140 was significantly higher than the rest, ranging from 70 to 110.  

## Insights deep dive
### Correlation of metrics
<img width="656" height="548" alt="image" src="https://github.com/user-attachments/assets/4e1ced6d-0de9-4eb9-a70d-c2212e0fec29" />\
No metrics are significantly correlated, including metacritic and rating, implying that the mass audience didn't necessarily agree with the score of experts. 

### Rating
<img width="623" height="463" alt="image" src="https://github.com/user-attachments/assets/4862cb49-c3d8-4d90-bea6-1ecd148946c1" />\
Nevertheless, the distribution of metacritic and mass rating is still quite similar, with the distribution of metacritic being more left-skewed, which may mean this rating is more lenient

### Publishers and developers
<img width="623" height="463" alt="image" src="https://github.com/user-attachments/assets/3f5c5a4e-274e-4f1d-8dfa-87d0da312c2b" />\
Plug In Digital was both a top publisher and a top developer, however, the dataset has too many different publishers and developers, making the data of each too diluted (140 games released by 1 developer was the highest) and hard to compare with each other to find out big competitor.

### Esrb rating
The same goes for Esrb, most games were for Everyone 10+ at 400+ followed by games for Teen at 400. The dataset is too small to draw any conclusion

### Platforms
<img width="563" height="437" alt="image" src="https://github.com/user-attachments/assets/4d8027de-db2c-4dcd-834d-ecc96e921269" />\
PC dominated the market and in 2018, it almost doubled Web's number. 
<img width="1182" height="784" alt="image" src="https://github.com/user-attachments/assets/15633f6d-f220-414c-aae9-2fa3ab61ff02" />\
Regarding the ratings, Web was the highest in both metacritic and mass ratings, but this may be due to its lowest rating count of 5 for each game. Funnily enough, Web was the 2nd biggest platform but each game was only rated by 5 people on average, which may mean that people who played on the web didn't have the tendency to rate and playmore casually because Web also had the lowest suggestions count at less than 40. PC was the lowest in both metacritic and mass ratings, which may mean PC players were actually harder to pleased and were more critical about the games they played.

### Genres
<img width="563" height="437" alt="image" src="https://github.com/user-attachments/assets/61378ffb-c99a-4ac6-956f-7758ff7367db" />\
Action was the biggest genre at all times. But in 2019, Platformer and Puzzle almost caught up to it. 
<img width="1183" height="784" alt="image" src="https://github.com/user-attachments/assets/7d4bdb40-2e4d-4f69-9725-08fcc308790e" />\
Regarding the ratings, Platformer did best in both metacritic and mass ratings, it and Shooter also had the most people rated for each game at 70 people. However, Adventure is the one suggested by people the most on average at 140 and Platformer was the least. Action and Adventure didn't do well for the number of ratings at only less than 30 for each game, but Action also had more than 100 people suggested for each game. This may mean that while Platform seems to be the genre with the highest quality, people were less inclined to suggest it, maybe the content and gameplay was niche. And while people didn't normally rate Action and Adventure genre, it was still very popular and suggested by the majority

## Recommendations
1. PC dominance but lowest ratings suggests a critical user base.
- PC gamers may have higher expectations and spend longer in games before rating.

=> Design PC games with depth and high replay value to meet this audience’s standards.
2. Web ratings are inflated, but based on minimal user input.
- Possibly due to casual play patterns and lower engagement.
=> For Web, explore lightweight, hyper-casual game models with optional rating incentives or gameplay features that encourage feedback.

3. Genre Evolution & Quality vs. Popularity
- Platformer and Puzzle genres gained popularity in 2019 and had high ratings, but low suggestion counts. => Indicates niche appeal with strong quality perception.
- Action and Adventure are consistently suggested, yet receive fewer ratings. => Reflects mass appeal but potentially lower depth or complexity.

=> Investigate hybrid genres: Action–Puzzle or Adventure–Platformers might blend popularity and quality. & Deep-dive into why Platformer isn’t widely suggested: limited replayability? niche themes?
