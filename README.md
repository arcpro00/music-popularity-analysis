# Introduction

This project uses a Spotify tracks dataset containing song metadata and audio characteristics extracted through Spotify’s analysis pipeline. Each row represents a track and includes both descriptive information (such as genre and release date) and quantitative musical characteristics (such as danceability, energy, loudness, and tempo). The final dataset contains **114000 rows in total.** The analysis selects to focus on five music categories: anime, classical, disney, jazz, and k-pop.

## Research Question

The question of this project is:

**Do different music categories reward different audio characteristics when it comes to popularity?**

In other words, do features such as danceability, energy, loudness, or acousticness relate to popularity differently depending on the type of music?

## Why This Question Matters

This question matters because popularity on streaming platforms is often treated as if there is a universal formula for success, sometimes leading to generic production, but listener preferences may differ substantially across musical categories. Understanding whether relationships between audio characteristics and popularity vary across genres can provide insight into how musical traits influence with audience behavior on the platform.

## Relevant Columns

The columns most relevant to this analysis are the `popularity`, `track_genre`, and audio characteristics columns (`duration_ms`, `danceability`, `energy`, `loudness`, `speechiness`, `acousticness`, `instrumentalness`, `liveness`, `valence`, `tempo`, `time_signature`, `release_date`).

