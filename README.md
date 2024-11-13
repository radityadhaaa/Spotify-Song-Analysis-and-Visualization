
# Spotify Song Analysis and Visualization

This project performs an analysis of popular songs on Spotify, examining their attributes and popularity across multiple countries. The dataset used contains information about top Spotify songs in 73 countries, including features like danceability, explicit content, acousticness, and instrumentalness. This analysis explores the relationships between these features and song popularity, as well as the distribution of certain attributes by country.

## Table of Contents
- [Project Structure](#project-structure)
- [Dataset](#dataset)
- [Requirements](#requirements)
- [Data Cleaning and Preprocessing](#data-cleaning-and-preprocessing)
- [Analysis and Visualizations](#analysis-and-visualizations)
  - [Popularity Based on Explicit Content](#popularity-based-on-explicit-content)
  - [Feature Correlation Analysis](#feature-correlation-analysis)
  - [Danceability Distribution by Country](#danceability-distribution-by-country)
- [Conclusion](#conclusion)

## Project Structure

This project is structured as a Jupyter Notebook that walks through data cleaning, analysis, and visualization steps.

## Dataset

The dataset used is a CSV file named `universal_top_spotify_songs.csv`, which includes the top Spotify songs across 73 countries with various song attributes. Key columns include:
- `name`: Song name
- `artists`: Artists of the song
- `country`: Country where the song is popular
- `is_explicit`: Whether the song is explicit (True/False)
- `popularity`: Popularity score of the song
- `danceability`, `acousticness`, `instrumentalness`: Audio features of the song
- `daily_rank`: Daily rank of the song
- `snapshot_date`, `album_release_date`: Dates related to the song's ranking and release

## Requirements

This project requires the following Python libraries:
- `pandas`
- `seaborn`
- `matplotlib`
- `scikit-learn`

Install them using:
```bash
pip install pandas seaborn matplotlib scikit-learn
```

## Data Cleaning and Preprocessing

1. **Remove Duplicate Rows**: We start by removing duplicate rows to ensure unique data, which is crucial for accurate analysis.

   ```python
   spotify_data = spotify_data.drop_duplicates()
   ```

2. **Convert Date Columns to DateTime**: The `snapshot_date` and `album_release_date` columns are converted to `datetime` format to facilitate time-based analysis.

   ```python
   spotify_data['snapshot_date'] = pd.to_datetime(spotify_data['snapshot_date'], errors='coerce')
   spotify_data['album_release_date'] = pd.to_datetime(spotify_data['album_release_date'], errors='coerce')
   ```

3. **Remove Rows with Missing Essential Data**: We remove rows with missing values in critical columns such as `name`, `artists`, and `country`.

   ```python
   spotify_data = spotify_data.dropna(subset=['name', 'artists', 'country'])
   ```

4. **Fill Missing Album Names**: Missing values in the `album_name` column are filled with "Unknown Album".

   ```python
   spotify_data['album_name'] = spotify_data['album_name'].fillna("Unknown Album")
   ```

5. **Fill Missing Album Release Dates**: For rows with missing values in `album_release_date`, we use the average release date per artist to approximate the missing dates.

   ```python
   avg_release_date = spotify_data.groupby('artists')['album_release_date'].transform(lambda x: x.fillna(x.mean()))
   spotify_data['album_release_date'] = spotify_data['album_release_date'].fillna(avg_release_date)
   ```

## Analysis and Visualizations

### Popularity Based on Explicit Content

We analyze if explicit content has any effect on a song's popularity. We calculate the average popularity of explicit vs. non-explicit songs and visualize it with a bar chart.

```python
explicitness_popularity = spotify_data.groupby('is_explicit')['popularity'].mean()
explicitness_popularity.plot(kind='bar', color=['blue', 'orange'])
plt.title("Popularity Based on Explicitness")
plt.xlabel("Is Explicit")
plt.ylabel("Average Popularity")
plt.show()
```

This chart reveals any trends in popularity based on whether a song is explicit or not.

### Feature Correlation Analysis

We calculate the correlation between song popularity and two audio features: `acousticness` and `instrumentalness`. This helps us understand if these features have any significant impact on popularity.

```python
feature_correlation = spotify_data[['popularity', 'acousticness', 'instrumentalness']].corr()
sns.heatmap(feature_correlation, annot=True, cmap='viridis')
plt.title("Correlation between Popularity and Song Features")
plt.show()
```

The heatmap visualizes the strength and direction of the correlations, revealing any significant relationships.

### Danceability Distribution by Country

Finally, we examine the danceability of the top 10 songs in each country and visualize it using a boxplot. We filter for countries with at least 10 top-ranked songs to ensure meaningful representation.

```python
top_songs = spotify_data[spotify_data['daily_rank'] <= 10]
top_songs = top_songs.dropna(subset=['country', 'danceability'])
country_counts = top_songs['country'].value_counts()
top_countries = country_counts[country_counts >= 10].index
top_songs_filtered = top_songs[top_songs['country'].isin(top_countries)]

plt.figure(figsize=(14, 7))
sns.boxplot(data=top_songs_filtered, x='country', y='danceability')
plt.title("Danceability of Top Songs by Country")
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.show()
```

This boxplot displays the range and variation in danceability for popular songs across different countries, highlighting regional differences in music preferences.

## Conclusion

This analysis explores various aspects of popular songs on Spotify, focusing on the relationships between features like explicit content, acousticness, instrumentalness, and popularity. The key findings include:
- **Explicit Content**: Whether a song is explicit has only a minor impact on its popularity.
- **Feature Correlations**: Acousticness and instrumentalness do not have a significant effect on song popularity.
- **Danceability by Country**: Danceability preferences vary across countries, with some countries having more consistent preferences than others.

This project provides valuable insights into the characteristics that influence a song’s popularity and reveals interesting patterns in global music preferences.
