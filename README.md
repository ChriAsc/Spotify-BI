# Analysis of music featured on Spotify using Qlik, Tableau and Power BI business intelligence tools

## 1. Introduction
### 1.1 Spotify 1.2M+ Songs Dataset
As a basis for the analysis we are going to illustrate in the following paper, we decided to
to operate by relying on a dataset called "Spotify 1.2M+ Songs Dataset."
The latter consists of songs by various artists and in various genres, from music
classical to rock. The dataset, as the name suggests, contained in its original state
a great many records and was characterized by 24 fields that contained information
regarding the type of song, the album in which it is featured, and the artist or artists
who contributed to the creation of the track.

The dataset described above can be found at the following link:
https://www.kaggle.com/datasets/rodolfofigueroa/spotify-12m-songs

All fields in the original dataset are:

**_id_**: track id

**_name_**: track title

**_album_**: album name

**_album_id_**: album id

**_artists_**: full list of artists' names that took part of a song

**_artist_ids_**: full list of artists' ids that took part of a song

**_track_number_**: track number in the relative album

**_disc_number_**: album number

**_explict_**: boolean field used to denote whether the song is explicit

**_danceability_**: number used to denote how much the song is danceable

**_energy_**: number used to denote how much the song  is energic

**_key_**: number between 0 and 11 that indicates the key most present in the song (0 = Do , 1 = Do#, ... 11 = Si).

**_loudness_**: number given in dB that indicates the loudness of the track.

**_mode_**: number that denotes if the mode's song is Major (1) or Minor (0).

**_speechiness_**: number between 1 and 0 which expresses how much speech is present in the track.

**_acousticness_**: number between 1 and 0 which measures the presence of instruments acoustics in the song.

**_instrumentalness_**: number between 1 and 0 which expresses probability that the trace does not contains vocal parts.

**_liveness_**:  number between 1 and 0 which measures the presence of audience in the track.

**_valence_**: number between 1 and 0 which indicates the vibe of the track, 1 extremely positive, 0 extremely negative.

**_tempo_**:  number which indicates the general tempo of the track measured in bpm

**_duration_ms_**: number indicating the duration of the track expressed in milliseconds.

**_time_signature_**: number that specifies how many beats are contained in each measure

**_year_**: the year of release of the track.

**_release_date_**: datetime (yyyy-mm-dd) indicating the release of the trask

### 1.2 Pre-Processing
The data-understanding and data-preparation phases are among the most onerous of the entire
knowledge extraction process, since datasets in their original state have
often noise, null elements or even superfluous columns. Therefore, upstream of the
analysis one must proceed with the ETL (Extraction, Transformation, Loading) phase,
that is, a series of procedures aimed at filtering and cleaning up the dataset. At the end of all this
we will have a dataset that is easy to handle and ready to be used in the
best possible way by the various business intelligence software.

Below we will outline the main steps of the ETL process applied to our
dataset:
- **Extraction** : After downloading the complete dataset (334.63 MB) the first
problem was related to the visualisation of the data as the size of the
size of the data did not allow us to operate easily with the most common tools, such as
e.g. Excel. For this reason, after some consideration, we opted
first for MySql (for its simplicity and immediacy) and then for
PySpark (for its remarkable processing speed). These two tools allowed us
the nature of the data and clearly see which areas were affected by noise or uselessness.
were the areas affected by noise or useless for our analysis.
- **Transformation** : Once we had identified the areas to be modified and cleaned up, we
used the same tools mentioned above to act on the dataset. The following will
the main changes made to the starting dataset divided according to the tool used.
the tool used.

    - **MySql** : The most relevant modification made using MySql was that
    concerning the splitting of the arrays in the "artists" and "artist_ids" fields [Figure
    1]. For this purpose, we wrote an SQL procedure which, taken an array
    outputs all elements within it, cleaned of all separator characters
    separator characters (such as brackets, commas and superscripts). Having obtained the unique values
    of the 'artists' array, these values were placed in an artist column and for
    each one the values of the fields in the original record were replicated in
    so as to obtain a record for each artist in the track.
    A further change made using SQL was the deletion of
    some records that had values of the fields "year" and "release_date"
    respectively of 0 and 0000- 00 - 00. As the time hierarchy is one of the
    most important, we decided to delete these fields. During
    observation of the data, we also noted the presence of some null values
    in the descriptive fields (such as 'liveness', 'instrumentalness' and 'danceability').
    Although most of these values could be considered
    incorrect, and therefore noise, others were indeed representative
    of reality; this, in addition to the considerable number of these incorrectnesses, led us
    led us to decide to disregard these inaccuracies. A final modification
    made using SQL was the concatenation of the "key" and "mode" fields into
    one additional column 'KeyMode', which would have been much easier to operate with.
    simpler to operate.
    
    - **PySpark** : Now having a repurposed "csv" file available, it was decided to
    decided to make use of the PySpark library as the amount of data was
    considerable (almost 2 million records). Therefore, as a first filtering, only those artists
    Therefore, as a first filtering, only artists who had participated in at least 200
    songs; in addition, components were excluded that did not add
    information (such as 'Various Artists'). Then, the fields "id",
    "artist_id", "album_id", "explicit" (since all of them were equal to 0, the field was not
    field was not of interest), "disc_number", "year", as the
    information appeared to be superfluous. The "csv" file was saved with the ";"
    as delimiter as there were portability problems (due to
    escape characters with comma and double quotes). In addition to this, from the
    previous one, a "csv" file was created - delimited by ";" - with 4 fields:
    "artist", to identify the artist, "city", to indicate the city they belong to,
    "region", for the relevant state/district, "country", to indicate the country.
    We then filled in these columns manually.
    
- **Loading** : After filtering and cleaning all the fields in our dataset, the latter,
    represented by a csv file, was loaded into the various analysis tools. Having
    produced only two tables, loading into the three tools was
    immediate.

## 2. Qlik
### 2.1 Analysis by artists

The first analysis performed focuses on artists and their albums. The dashboard is
composed of an interactive map, indicating the spatial distribution of artists, a
table (explorable), illustrating the number of albums and duration (in format
[hh]:[mm]:[ss]) in which the artist/album can be selected, and a bar chart
indicating the number of tracks [Figure 2].

At first glance, it can be seen that the artists with the most songs are exponents of
and that the United States is the country with the most artists.
Using the dashboard, it is possible to explore the levels of the hierarchy; for example, by doing
drill-down on the spatial dimension and selecting a country, states and regions are shown [Figure 3 ].
states and regions [Figure 3].

In this case, in addition to the number, it is possible to see and search for artists that refer
reference exclusively to the previously selected country.
By selecting a state, artists referring to that particular region are shown and
specifically, each individual city is highlighted by means of a bubble, whose size
depends on the number of artists; for instance, it is possible to choose one or more artists and see
the corresponding albums [Figure 4].

At the last level of the hierarchy, in addition to the previous functions, it is also possible to
explore the various albums of the chosen artist to find out the number of tracks [Figure 5].

### 2.2 Energy Analysis

The attribute 'energy' indicates how energetic and active a song is, so it can be
exploited in order to find information about artists. Considering energy and
noise, in the second analysis it was possible to find a linear correlation
positive linear correlation (Pearson's correlation index: 0.81), as usually a song
energetic song (e.g. rock) is also noisy, whereas a (classical) symphony is
calmer [Figure 6].

On the other hand, when analysing energy and the presence of acoustic instruments, there is a negative linear correlation
negative linear correlation (Pearson's correlation index: - 0.81), since the use of acoustic guitars is
acoustic guitars is often associated with quieter or sadder songs [Figure 7].

Another quantity that is used in conjunction with energy is valence, which indicates how much
a track is perceived positively or negatively; in this case, a positive correlation can be seen from the temporal analysis.
temporal analysis, a less pronounced positive correlation is noted [Figure 8].

Changing perspective, artists can be analysed according to how much their albums
push them to dance. Using this measure together with the information derived from energy,
it is possible to segment the dataset into 3 clusters using the K-means technique [Figure 9].

in general, one can distinguish classical music performers (cluster 1), from those of
rock music (cluster 2) to those of other genres (cluster 3). Logically, some artists on the
border are not assigned to the most correct cluster, but this is due to the
clustering technique used.

### 2.3 Tonality analysis

The third analysis focuses on the key of a song, which is derived through the key
(C, C#, D, ...) and the mode (Minor, Major).
Using a tree map [Figure 10], it is possible to visualise the distribution of tonalities among the tracks, from which it can be seen that "C
major" is the most common key and that about 2/3 of the songs are in the "major" mode.

In addition, it is possible to filter the keys by artist, and by related album,
so that you know how many tracks were recorded in a particular key.

Considering the 'minor' mode, most songs have the key 'A'.
