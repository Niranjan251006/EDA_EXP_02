**Netflix Shows & Movies**

**Aim**

To analyze Netflix dataset and compare movies vs TV shows, top producing countries, and release year trends.

**Procedure / Algorithm**

  1)Load dataset (netflix_titles.csv).
  2)Count movies vs TV shows.
  3)Group by country → top contributors.
  4)Create pivot table (release year vs type).
  5)Visualize with bar & line charts.

**Program**
 ```
import pandas as pd
# 1. Load dataset directly from GitHub
url = "https://raw.githubusercontent.com/allenkong221/netflix-titles-dataset/main/netflix_titles.csv"
df = pd.read_csv(url)
# Basic inspection
print("Shape:", df.shape)
print("Columns:", df.columns, "\n")
print("Type counts:\n", df['type'].value_counts(), "\n")
# 2. Clean 'date_added' and extract year/month
df['date_added'] = df['date_added'].str.strip()   # remove extra spaces
df['date_added'] = pd.to_datetime(df['date_added'], errors='coerce')  # invalid -> NaT
df['year_added'] = df['date_added'].dt.year
df['month_added'] = df['date_added'].dt.month_name()
# 3. Movies vs TV Shows
count_by_type = df.groupby('type')['title'].count()
print("Count by Type:\n", count_by_type, "\n")
# 4. Country vs Type Pivot Table
pivot_country_type = df.pivot_table(index='country', columns='type',values='title', aggfunc='count', fill_value=0 )
Add "Total" column and find max country
pivot_country_type['Total'] = pivot_country_type.sum(axis=1)
max_country = pivot_country_type['Total'].idxmax()  # country with most titles
max_count = pivot_country_type['Total'].max()       
# number of titles
print("Pivot Table (Country vs Type):\n", pivot_country_type.head(), "\n")
print(f"Largest Overall: {max_country} with {max_count} titles\n")
# 5. Top 5 Directors
top_directors = df['director'].value_counts().head(5)
print("Top 5 Directors:\n", top_directors, "\n")
# 6. Yearly Trend of Additions (Movies vs TV Shows)
trend = df.groupby(['year_added', 'type']).size().unstack(fill_value=0)
print("Yearly Trend by Type:\n", trend.head(), "\n")
# 7. Expand Genres
df_genre = (
df[['show_id','listed_in']].dropna().assign(listed_in=df['listed_in'].str.split(', ')).explode('listed_in'))
# Merge expanded genres back
df_expanded = df.merge(df_genre, on='show_id', how='left')
# Check merged columns
print("Columns after merge:\n", df_expanded.columns)
# Show sample of expanded genres
print("\nExpanded Genre Sample:\n",
df_expanded[['title','listed_in_y']].head())
#Top 5 Genres (extra useful for insights)
top_genres = df_expanded['listed_in_y'].value_counts().head(5)
print("\nTop 5 Genres:\n", top_genres, "\n")
```

**Ouptut**
<img width="1066" height="522" alt="eda exp 2 d" src="https://github.com/user-attachments/assets/6882708f-dd43-42b3-94f8-6c899ad6529c" />
<img width="1062" height="592" alt="eda exp 2 c" src="https://github.com/user-attachments/assets/7d315811-18b0-406a-b5b3-75681f805923" />
<img width="1062" height="870" alt="eda exp 2 b" src="https://github.com/user-attachments/assets/93341aa8-41d6-457e-9687-cc66596026e5" />
<img width="1065" height="818" alt="eda exp 2 a" src="https://github.com/user-attachments/assets/bc7ed4a9-a8cc-4a87-a321-26e6756969cb" />

**Result**
Helps Netflix in content planning & investments.
