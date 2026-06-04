# Introduction

This project uses a Spotify tracks dataset containing song metadata and audio characteristics extracted through Spotify’s analysis pipeline. Each row represents a track and includes both descriptive information (such as genre and release date) and quantitative musical characteristics (such as danceability, energy, loudness, and tempo). The final dataset contains **114000 rows in total.** The analysis selects to focus on five music categories: anime, classical, disney, jazz, and k-pop.

## Research Question

The question of this project is:

**Do different music categories reward different audio characteristics when it comes to popularity?**

In other words, do features such as danceability, energy, loudness, or acousticness relate to popularity differently depending on the type of music?

## Why This Question Matters

This question matters because popularity on streaming platforms is often treated as if there is a universal formula for success, sometimes leading to generic production, but listener preferences may differ substantially across musical categories. Understanding whether relationships between audio characteristics and popularity vary across genres can provide insight into how musical traits influence audience behavior on the platform.

## Relevant Columns

The columns most relevant to this analysis are the `popularity`, `track_genre`, and audio characteristics columns (`duration_ms`, `danceability`, `energy`, `loudness`, `speechiness`, `acousticness`, `instrumentalness`, `liveness`, `valence`, `tempo`, `time_signature`, `release_date`).

# Data Cleaning and Exploratory Data Analysis

Several cleaning steps were performed before analysis to improve consistency while preserving the structure of Spotify’s generated audio features.

First, possible duplicate rows and duplicate `track_id` values were checked before using `track_id` as an observational unit. Duplicate `track_id` groups were inspected to determine which variables changed across repeated entries. Audio characteristics were generally consistent within duplicate track IDs, while metadata fields such as popularity and release date sometimes differed. To verify that `track_id` should be treated as the unit of observation, songs sharing the same artist, album, and track name but having different `track_id` values were also inspected. These observations all showed differences in audio characteristics, suggesting they may correspond to different releases or versions of the same song rather than accidental duplicates. Because of this, rows with the same `track_id` were consolidated so that each observation represented a unique Spotify track.

Next, `release_date` was converted to a datetime format. Spotify stores mix date formats (year-only, year-month, and full dates), so conversion was necessary to support later analysis and addition of a release year feature.

The dataset was then filtered to five selected music categories of interest: anime, classical, disney, jazz, and k-pop. These categories were chosen because they remained reasonably represented after filtering and allowed comparisons between categories with distinct musical characteristics.

Data quality checks were then performed on generated audio features. There was one tempo value below 20, of 0, because it isn't meaningful as a real musical tempo. Similarly, `time_signature` values below 1, of which were 0, were converted to missing values because they have no meaningful musical interpretation.

Extreme observations for variables such as loudness, speechiness, and duration were manually inspected. Although some observations appeared unusual, they generally corresponded to legitimate tracks and were retained to avoid introducing unnecessary manual corrections. It also appeared that some track genre labels by Spotify are imprecise such as labeling a chant as K-pop, which was noted but not addressed. We will concern ourselves with genres as they are labeled by Spotify.

The cleaned dataset produced the final DataFrame shown below.

### Head of Cleaned DataFrame

<iframe
src="assets/music_tracks_clean_head.html"
width="100%"
height="350"
frameborder="0">
</iframe>

## Univariate Analysis

<iframe
src="assets/popularity_distribution.html"
width="100%"
height="500"
frameborder="0">
</iframe>
<iframe
src="assets/log_popularity_distribution.html"
width="100%"
height="500"
frameborder="0">
</iframe>

Popularity is concentrated at low values, indicating that most tracks in the selected categories are not highly popular on Spotify. After applying a log transform, the distribution suggests a group of consistently low-popularity songs separated from a smaller group of substantially more popular tracks.

## Bivariate Analysis

<iframe
src="assets/popularity_by_genre.html"
width="100%"
height="500"
frameborder="0">
</iframe>

Popularity distributions differ across the selected music categories. This suggests that music category may be associated with popularity and motivates later analysis of whether audio characteristics relate to popularity differently across categories.

## Interesting Aggregates

<iframe
src="assets/genre_audio_summary.html"
width="100%"
height="600"
frameborder="0">
</iframe>

This grouped table shows the average audio characteristics for each selected music category. The differences across categories suggest that anime, classical, disney, jazz, and k-pop have different defining audio characteristics, which supports the project’s focus on whether different categories reward different musical traits.

## Assessment of Missingness

After cleaning, tempo was the only column with meaningful missing values. Since Spotify tempo values are generated from the audio itself, missingness does not seem likely to be MCAR and may instead depend on observed audio characteristics (MAR) or the true underlying tempo value itself (NMAR).

Permutation tests comparing rows with missing and observed tempo values showed extremely small p-values (less than 0.00001) for danceability, energy, loudness, speechiness, acousticness, instrumentalness, liveness, and valence, suggesting tempo missingness depends on these observed variables. In contrast, key produced a large p-value (greater than 0.1), meaning there was insufficient evidence that tempo missingness depends on the musical key of the music.

<iframe
src="assets/tempo_missing_energy.html"
width="100%"
height="400"
frameborder="0">
</iframe>

This plot compares energy levels for tracks with missing and non-missing tempo values. Tracks with missing tempo appear to have a different energy distribution, supporting the permutation test results that tempo missingness depends on observed audio characteristics rather than occurring completely at random.

Because tempo missingness appears related to observed audio characteristics, MAR appears more likely than NMAR. This is also supported by the fact that observed tempo values already span a broad range of reasonable musical tempos rather than appearing concentrated near particular values. However, if certain rhythmic structures or extraction difficulty directly cause tempo to become missing, then tempo would instead be NMAR. Additional data such as Spotify extraction confidence or rhythm metadata could help distinguish these possibilities. Thus, I do not believe there is a column in the dataset that is NMAR.

## Hypothesis Testing

To relate audio characteristics to popularity, I tested whether danceability is associated with popularity.

**Null Hypothesis:** Tracks with above-median danceability have the same popularity distribution as tracks with below-median danceability.

**Alternative Hypothesis:** Tracks with above-median danceability have a different popularity distribution than tracks with below-median danceability.

**Test Statistic:** The absolute difference in mean popularity between above-median and below-median danceability tracks.

**Significance Level:** 0.05.

Using 10,000 permutations, the p-value was approximately 0, meaning that none of the shuffled trials produced a test statistic as large as the observed one. At the 0.05 significance level, we reject the null hypothesis. The observed difference is statistically significant and provides evidence of an association between danceability and popularity in the selected music categories overall.

## Framing a Prediction Problem

I will predict a track’s Spotify popularity score using its audio characteristics and music category. This is a **regression** problem because popularity is a continuous numerical variable ranging from 0 to 100.

The response variable is `popularity`. I chose popularity because the central question of this project is whether different music categories reward different audio characteristics when it comes to popularity. Predicting popularity directly allows me to evaluate how well a track’s measurable musical characteristics explain its success on Spotify.

At the time of prediction, I assume that a track’s audio characteristics have already been extracted. Therefore, features such as `duration_ms`, `danceability`, `energy`, `loudness`, `speechiness`, `acousticness`, `instrumentalness`, `liveness`, `valence`, `tempo`, `time_signature`, and `track_genre` are available. Popularity itself is not available at prediction time and will not be used as a predictor.

Model performance will be evaluated using **Mean Absolute Error (MAE)**. MAE measures the average absolute difference between predicted and actual popularity scores, making it directly interpretable in the original popularity scale. I chose MAE over classification metrics such as accuracy because popularity is continuous, and over metrics such as RMSE because MAE is less sensitive to extreme prediction errors and will not tend towards the extreme values like RMSE.

## Baseline Model

My baseline model uses **linear regression** to predict Spotify popularity from audio characteristics and music category. All preprocessing and model fitting were implemented in a single sklearn pipeline.

The model uses **9 quantitative features**, **1 ordinal feature**, and **4 nominal features**:

* Quantitative: `duration_ms`, `danceability`, `energy`, `loudness`, `speechiness`, `acousticness`, `instrumentalness`, `liveness`, `valence`
* Ordinal: `time_signature`
* Nominal: `explicit`, `mode`, `track_genre`, `key`

Quantitative and ordinal features were standardized using `StandardScaler`. The nominal features `track_genre` and `key` were transformed using one-hot encoding. Although `explicit` and `mode` are nominal variables, they were already represented numerically and were passed through unchanged.

The baseline model achieved a **Mean Absolute Error (MAE) of 12.59** and an **R² of 0.528** on the test set.

I do not consider this model fully satisfactory, but I consider it a "good" reasonable baseline. On average, predictions differ from actual popularity by about 13 popularity points, and the model explains approximately 53% of the variation in popularity. This suggests that audio characteristics and music category contain meaningful information about popularity, but that the relationship is not entirely linear and additional feature engineering or more flexible models may improve performance.

## Final Model

To improve upon the baseline model, I introduced several engineered interaction features and replaced linear regression with a random forest regressor.

The new engineered features included interactions between audio characteristics: `danceability × energy`, `energy × valence`, and `speechiness × loudness`. These features were added because musical characteristics may combine to influence popularity rather than independently. For example, energetic tracks may be rewarded differently depending on whether they are also highly danceable.

I also added interaction features between genre and energy as well as genre and danceability. These features were motivated directly by the project question about how different music categories reward different musical characteristics, the impact on popularity.

The final model used a **Random Forest Regressor**. Unlike linear regression, random forests can model nonlinear relationships and capture interactions between features without assuming a single linear relationship across all tracks.

To select the final model, I performed **GridSearchCV with 5-fold cross validation** while tuning three hyperparameters:

* `n_estimators`: number of trees in the forest
* `max_depth`: maximum complexity of each tree
* `min_samples_leaf`: minimum observations required in terminal leaves

The best-performing hyperparameters were:

* `n_estimators = 200`
* `max_depth = None`
* `min_samples_leaf = 1`

The final model achieved an **MAE of 9.08** and an **R² of 0.651**, improving over the baseline model’s **MAE of 12.59** and **R² of 0.528**. The lower MAE means predictions were closer to actual popularity values, while the increase in R² suggests the final model explained substantially more variation in popularity. This improvement supports the idea that relationships between audio characteristics, genre, and popularity are nonlinear and depend on interactions between musical traits.


