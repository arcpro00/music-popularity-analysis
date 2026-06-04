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

```html
<iframe
src="assets/music_tracks_clean_head.html"
width="100%"
height="350"
frameborder="0">
</iframe>
```

## Univariate Analysis

<iframe
src="assets/popularity_distribution.html"
width="100%"
height="500"
frameborder="0">
</iframe>

## Bivariate Analysis

<iframe
src="assets/popularity_by_genre.html"
width="100%"
height="500"
frameborder="0">
</iframe>

## Interesting Aggregates

<iframe
src="assets/genre_audio_summary.html"
width="100%"
height="600"
frameborder="0">
</iframe>

