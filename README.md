# netflix_titles_TDI
import pandas as pd

# Load the CSV file (Make sure to provide the correct path to the file)
file_path = r'C:\Users\Dell\Desktop\netflix_titles.csv'  # Update the path if needed
netflix_data = pd.read_csv(file_path)

# Display the first few rows of the data
print(netflix_data.head())
# Assuming `netflix_data` is your loaded dataframe
dataframe = netflix_data.drop(['show_id', 'description', 'director'], axis=1)

# View the updated dataframe
print(dataframe.head())
dataframe = dataframe.dropna()

# View the updated dataframe
print(dataframe.head())
# Convert columns to datetime
dataframe['date_added'] = pd.to_datetime(dataframe['date_added'], errors='coerce')
dataframe['release_year'] = pd.to_datetime(dataframe['release_year'], format='%Y', errors='coerce')

# Extract year, month, day from 'date_added'
dataframe['year_added'] = dataframe['date_added'].dt.year
dataframe['month_added'] = dataframe['date_added'].dt.month
dataframe['day_added'] = dataframe['date_added'].dt.day

# Extract year from 'release_year'
dataframe['release_year'] = dataframe['release_year'].dt.year

# View the updated dataframe
print(dataframe[['date_added', 'year_added', 'month_added', 'day_added', 'release_year']].head())
# Count the number of cast members
dataframe['cast_count'] = dataframe['cast'].apply(lambda x: len(str(x).split(',')) if pd.notna(x) else 0)

# Extract the lead actor (first actor listed)
dataframe['lead_actor'] = dataframe['cast'].apply(lambda x: str(x).split(',')[0] if pd.notna(x) else None)

# View the updated dataframe
print(dataframe[['cast', 'cast_count', 'lead_actor']].head())
# Convert 'duration' to numeric, assuming it's in "90 min" or "3 Seasons" format
dataframe['duration_numeric'] = dataframe['duration'].apply(lambda x: int(x.split(' ')[0]) if pd.notna(x) else None)

# View the updated dataframe
print(dataframe[['duration', 'duration_numeric']].head())
# Clean and capitalize country names
dataframe['country'] = dataframe['country'].str.strip().str.title()

# View the updated dataframe
print(dataframe[['country']].head())
# Clean and capitalize country names
dataframe['country'] = dataframe['country'].str.strip().str.title()

# View the updated dataframe
print(dataframe[['country']].head())
# Convert 'listed_in', 'country', and 'cast' into list format
dataframe['genre_list'] = dataframe['listed_in'].apply(lambda x: str(x).split(',') if pd.notna(x) else [])
dataframe['country_list'] = dataframe['country'].apply(lambda x: str(x).split(',') if pd.notna(x) else [])
dataframe['actor_list'] = dataframe['cast'].apply(lambda x: str(x).split(',') if pd.notna(x) else [])

# View the updated dataframe
print(dataframe[['genre_list', 'country_list', 'actor_list']].head())
# Explode 'genre_list', 'country_list', and 'actor_list'
dataframe_exploded = dataframe.explode('genre_list').explode('country_list').explode('actor_list')

# Remove any null values
dataframe_exploded = dataframe_exploded.dropna()

# View the exploded dataframe
print(dataframe_exploded.head())

# Step 11: Filter out rows with empty 'country' fields and drop columns
dataframe_filtered = dataframe_exploded[dataframe_exploded['country'].notna()]
dataframe_filtered = dataframe_filtered.drop(['date_added', 'cast', 'listed_in'], axis=1)

# Step 12: Preview the first few rows
print(dataframe_filtered.head())
# Assuming 'listed_in' contains genres
if 'listed_in' in dataframe.columns:
    # Split the 'listed_in' column and explode it into separate rows
    popular_genre = dataframe['listed_in'].str.split(', ').explode()
    
    # Count occurrences of each genre
    genre_counts = popular_genre.value_counts()
    
    # Get the most popular genre
    most_popular_genre = genre_counts.idxmax()
    most_popular_count = genre_counts.max()
    
    print(f"The most popular genre is: {most_popular_genre} with {most_popular_count} occurrences.")
else:
    print("'listed_in' column is not found in the DataFrame.")
    # Count the number of listings by country
country_counts = dataframe['country'].value_counts()
most_listed_country = country_counts.idxmax()
most_listed_count = country_counts.max()

print(f"The country with the highest number of movies or TV shows listed is: {most_listed_country} with {most_listed_count} listings.")
# 3. Which movie or TV show has the most listings?
title_counts = dataframe['title'].value_counts()
most_listed_show = title_counts.idxmax()
most_listed_show_count = title_counts.max()
print(f"The movie or TV show with the most listings is: {most_listed_show} with {most_listed_show_count} listings.")

# 4. Which movie or TV show has the largest cast?
dataframe['cast_count'] = dataframe['cast'].str.split(', ').str.len()
largest_cast_show = dataframe.loc[dataframe['cast_count'].idxmax()]
print(f"The movie or TV show with the largest cast is: {largest_cast_show['title']} with {largest_cast_show['cast_count']} actors.")

# 5. Which movie or TV show has been broadcast the most times?
# Assuming 'broadcast_count' is a column in your dataset
# most_broadcast_show = dataframe.loc[dataframe['broadcast_count'].idxmax()]
# print(f"The movie or TV show that has been broadcast the most times is: {most_broadcast_show['title']}.")

# 6. Which movie or TV show is listed in the most unique countries?
unique_country_counts = dataframe.groupby('title')['country'].nunique()
most_unique_country_show = unique_country_counts.idxmax()
most_unique_country_count = unique_country_counts.max()
print(f"The movie or TV show listed in the most unique countries is: {most_unique_country_show} with {most_unique_country_count} unique countries.")

# 7. Which actor has had the most lead roles?
lead_actor_counts = dataframe['cast'].str.split(', ').explode().value_counts()
most_lead_roles_actor = lead_actor_counts.idxmax()
most_lead_roles_count = lead_actor_counts.max()
print(f"The actor with the most lead roles is: {most_lead_roles_actor} with {most_lead_roles_count} lead roles.")

# 8. Which actor has appeared in the most movies or TV shows?
most_appearances_actor = lead_actor_counts.idxmax()
most_appearances_count = lead_actor_counts.max()
print(f"The actor who has appeared in the most movies or TV shows is: {most_appearances_actor} with {most_appearances_count} appearances.")

# 9. What is the most common rating assigned to movies or TV shows?
rating_counts = dataframe['rating'].value_counts()
most_common_rating = rating_counts.idxmax()
most_common_rating_count = rating_counts.max()
print(f"The most common rating assigned to movies or TV shows is: {most_common_rating} with {most_common_rating_count} occurrences.")

# 10. Are there more TV shows or movies listed on Netflix?
movie_count = dataframe[dataframe['type'] == 'Movie'].shape[0]
tv_show_count = dataframe[dataframe['type'] == 'TV Show'].shape[0]
if movie_count > tv_show_count:
    print(f"There are more movies listed on Netflix: {movie_count} movies vs {tv_show_count} TV shows.")
else:
    print(f"There are more TV shows listed on Netflix: {tv_show_count} TV shows vs {movie_count} movies.")

# 11. On which day of the week are the most movies or TV shows added to Netflix?
dataframe['date_added'] = pd.to_datetime(dataframe['date_added'])
dataframe['day_added'] = dataframe['date_added'].dt.day_name()
most_added_day = dataframe['day_added'].value_counts().idxmax()
print(f"The day of the week on which the most movies or TV shows are added is: {most_added_day}.")

# 12. In which year were the most movies or TV shows added to Netflix?
most_added_year = dataframe['date_added'].dt.year.value_counts().idxmax()
print(f"The year with the most movies or TV shows added to Netflix is: {most_added_year}.")

# 13. Which month sees the highest number of movies or TV shows added to Netflix?
most_added_month = dataframe['date_added'].dt.month_name().value_counts().idxmax()
print(f"The month with the highest number of movies or TV shows added is: {most_added_month}.")

# 14. Which release year had the most movies or TV shows?
most_released_year = dataframe['release_year'].value_counts().idxmax()
print(f"The release year with the most movies or TV shows is: {most_released_year}.")
