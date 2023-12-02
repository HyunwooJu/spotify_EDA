# 2023 Top Spotify Songs Analysis
```
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

## Dataset
```
file_path = 'spotify-2023.csv'

data = pd.read_csv(file_path, encoding='ISO-8859-1')
```
## 1. 전체 노래 취향 분석
```
def calculate_stats(column):
    """Calculate statistics"""
    count = len(column)
    mean = column.sum() / count
    std = ((column - mean) ** 2).sum() / count
    std = std ** 0.5
    min_val = column.min()
    max_val = column.max()
    return {
        'mean': round(mean,2),
        'std': round(std,2),
        'min': min_val,
        'max': max_val
    }
```

```
music_attributes = ['danceability_%', 'valence_%', 'energy_%', 'acousticness_%', 'instrumentalness_%', 'liveness_%', 'speechiness_%']

music_genre = {attr: calculate_stats(data[attr]) for attr in music_attributes}
```

```
music_genre
```
### 1-1. 장르분석

```
# Creating a DataFrame from the provided dictionary
music_genre_df = pd.DataFrame(music_genre).T

# Plotting the data as a table
fig, ax = plt.subplots(figsize=(6, 4))
ax.axis('tight')
ax.axis('off')
ax.table(cellText=music_genre_df.values, colLabels=music_genre_df.columns, rowLabels=music_genre_df.index, cellLoc = 'center', loc='center')

plt.show()
```

![image](https://github.com/HyunwooJu/spotify_EDA/assets/37990421/16793a95-39d2-46d9-9d80-60b17276268a)

### 1-2. 출시시기 분석

```
# Set the aesthetic style of the plots
sns.set(style="whitegrid")

# Plotting the distribution of 'released_month'
plt.figure(figsize=(12, 6))
sns.countplot(data=data, x='released_month')
plt.title('Distribution of Song Releases by Month')
plt.xlabel('Month')
plt.ylabel('Number of Songs')
plt.xticks(range(0, 12), ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'])
plt.show()
```
![image](https://github.com/HyunwooJu/spotify_EDA/assets/37990421/15e5bc74-df71-4802-8fdc-9f21f064419c)

## 곡 mode 분석

```
# Plotting the distribution of 'key'
plt.figure(figsize=(12, 6))
sns.countplot(data=data, x='key')
plt.title('Distribution of Song Keys')
plt.xlabel('Musical Key')
plt.ylabel('Number of Songs')
plt.xticks(rotation=45)
plt.show()
```

![image](https://github.com/HyunwooJu/spotify_EDA/assets/37990421/d0be6d37-8698-446d-8911-cd55da0433f5)

## 2. 많이 들은 노래(Top 100) 분석

```
# Sorting the top 100 songs based on the streams value
top100 = data.sort_values(by='streams', ascending=False).head(100)

top100_music_genre = {attr: calculate_stats(top100[attr]) for attr in music_attributes}
```

```
top100_music_genre
```

### 2-1. (전체 vs 인기곡) 장르 비교

```
# Preparing data for the graph
attributes = list(music_genre.keys())
means_overall = [music_genre[attr]['mean'] for attr in attributes]
means_top100 = [top100_music_genre[attr]['mean'] for attr in attributes]

# Creating the comparison graph
plt.figure(figsize=(6, 4))
x = range(len(attributes))
plt.bar(x, means_overall, width=0.4, label='Overall Mean', align='center', color='blue', alpha=0.6)
plt.bar(x, means_top100, width=0.4, label='Top 100 Mean', align='edge', color='green', alpha=0.6)

# Adding labels and title
plt.xticks(x, attributes, rotation=45)
plt.ylabel('Mean Value')
plt.title('Comparison of Mean Values of Music Attributes: Overall vs. Top 100')
plt.legend()
plt.show()
```

![image](https://github.com/HyunwooJu/spotify_EDA/assets/37990421/96220380-14c6-4d34-ad07-668a876be53b)

### 2-2. (전체 vs 인기곡) 출시시기 비교

```
# Preparing the percentage data
overall_month_percentage = (data['released_month'].value_counts(normalize=True).sort_index()) * 100
top100_month_percentage = (top100['released_month'].value_counts(normalize=True).sort_index()) * 100

# Creating the comparison graph
plt.figure(figsize=(6, 4))
x = range(1, 13)  # Months from 1 to 12
plt.bar(x, overall_month_percentage, width=0.4, label='Overall', align='center', color='blue', alpha=0.6)
plt.bar(x, top100_month_percentage, width=0.4, label='Top 100', align='edge', color='green', alpha=0.6)

# Adding labels and title
plt.xticks(x, ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'])
plt.ylabel('Percentage of Songs (%)')
plt.title('Comparison of Release Month Distribution (Percentage): Overall vs. Top 100')
plt.legend()
plt.show()
```

![image](https://github.com/HyunwooJu/spotify_EDA/assets/37990421/246c489c-a9ec-4e4a-b65b-4a8428a8d531)

### 2-3. (전체 vs 인기곡) 곡 mode 비교

```
# Calculating the percentage distribution of musical keys for both overall dataset and top 100 streamed songs

# Preparing the percentage data for keys
overall_key_percentage = (data['key'].value_counts(normalize=True).sort_index()) * 100
top100_key_percentage = (top100['key'].value_counts(normalize=True).sort_index()) * 100

# Creating the comparison graph
plt.figure(figsize=(6, 4))
x = overall_key_percentage.index
plt.bar(x, overall_key_percentage, width=0.4, label='Overall', align='center', color='blue', alpha=0.6)
plt.bar(x, top100_key_percentage, width=0.4, label='Top 100', align='edge', color='green', alpha=0.6)

# Adding labels and title
plt.xticks(x, rotation=45)
plt.ylabel('Percentage of Songs (%)')
plt.title('Comparison of Key Distribution (Percentage): Overall vs. Top 100')
plt.legend()
plt.show()
```

![image](https://github.com/HyunwooJu/spotify_EDA/assets/37990421/9f274fe3-6639-413a-98d7-9f6069e9fcb8)
