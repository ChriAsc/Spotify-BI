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


## 3. Tableau
### 3.1 Geographical analysis by key and mode

The main purpose of this analysis was to identify the key-mode pairs
most popular for each region in our dataset. To do this, the
aggregation operator we used is fashion; unfortunately, Tableau does not provide
implementation of this operator, so we had to resort to the use of calculated fields.
resort to the use of calculated fields that emulate its operation.

The form of the calculated field 'ModaKEYMODE' is as follows:

The objective was to generate a support column that would return Boolean values of the
type:

- _True_ : The key-mode pair in question represents the fashion for the city we are
    being displayed.
- _False_ : The key-mode pair does not represent the fashion for the city.

Having obtained this column for each city, we simply filter out the false values to obtain the
fashions for each of them.

Once we had obtained these values for each city, we simply inserted the Country-
Region-City hierarchy between the dimensions to obtain the views below, where by going
roll-up and drill-down we can observe the various fashions by country [Figure 11],
State/Region [Figure 12], and City [Figure 13].

One problem encountered with this approach is that in Tableau the fields
calculated fields fail to recognise member aliases, and as a result, at the lowest level of
lowest level of aggregation (City), aliases (Do,Do#, ect.) assigned to pairs of key-
modes were not recognised. Conversely, by adding aggregation levels
(Region, Country), the aliases were read correctly. This forced us to take a
step back to the transformation phase, where we changed the
integers of key and mode into string fields.

### 3.2 Energy correlation stories

The second analysis we decided to carry out on Tableau is an analysis aimed at
observe the correlations linking the "energy" measure with other significant measures in our
our dataset. In particular, three measures were chosen on which it is easy to
imagine what kind of relationship may exist with the "energy" value of a track;
these three measures are 'loudness', 'instrumentalness' and 'acousticness'.

To this end, we decided to exploit Tableau stories, which allow us to expose
results in the form of a sequence of visualisations; these sheets that
make up the story are in fact images accompanied by an explanatory text.

There are four sheets that we have decided to include in the stories.

The first provides a representation of the trend of the three correlations based on
of the time dimension at year level [Figure 14]. As can be observed, the three
correlations are all of different types. In particular, Energy-Loudness is very close
to one since an energetic song is almost always also a loud song, Energy-
Instrumentalness does not show an imbalance as there is no link between
the energy of a song and its being instrumental, and finally we have Energy-Acousticess
which shows values very close to -1, due to the fact that most of the
acoustic tracks are much more relaxed.

After this temporal correlation analysis, we thought of proceeding with
an analysis on a geographic basis, and thus we placed the measurements of the three
correlations in a graph together with the spatial hierarchy "country"-"region"-"city". In
this way, it was also possible to reiterate our expectations of the three measures on a
geographical basis; here, positive and negative correlations are represented
respectively by colours close to blue [Figure 15] and red [Figure 16], while the
lack of a relationship can be observed through the presence of different colours [Figure 17].

### 3.3 Trend analysis

Another type of analysis that Tableau allows is that relating to the
trends of certain values. In our case, having many descriptive measures
descriptive measures, what we have done is to observe the predicted trends of these
2020 (the last year represented in our dataset), looking at a time window of 25 years.
considering a time window of 25 years. This choice was guided by the absence
of some years in the dataset and the fact that using too large a time window would have led to less reliable results.
would have led to less reliable results.

In the graphs below, we can see three forecasts made on an annual basis, representing the trend of the average values of three measurements [Figure 18]. These were chosen to show three different trends: stable, increasing and decreasing. The measures in question are 'energy' (increasing), 'liveness' (mostly stable) and 'acousticness' (decreasing).

Forecasts, as such, indicate the possibility that a measure will take on a certain value in the future and are calculated on the basis of historical trends. In the
graphs shown here, the blurred area below indicates the range of values within which the the average of the measurement is expected to fall and, for this reason, measurements that have remained more stable over time have remained more stable over time show less variability (the prediction of "acousticness' prediction than that of 'energy' is safer as the latter has presented several peaks over time).
presented several peaks).

## 4. Power BI
### 4.1 Analysis of track duration

The first analysis tackled on Power BI concerned the average duration (in minutes) of the
tracks grouped according to their position in the album. The rationale behind this
The rationale behind this analysis was that, in most cases, the tracks with the longest duration
longer, often the most structured, are placed by the artists among the first ones on the album.

Therefore, a bar chart representing the average duration of the tracks according to their position in the album was reported.
of tracks according to their position in the album [Figure 19]. At first glance, it is easy
observe that the first 6 songs of the albums have an average duration of more than
5 minutes; after which, going down in position, the durations tend to decrease initially
and then stabilise at a value close to 2 minutes and 30 seconds.

In support of this, through the use of a boxplot, it is possible to observe the distribution
of the durations of the tracks according to their position in the album; in particular, it can be noted
how after the sixth track there is a gap which separates it from the seventh and that the
tracks following the first tend to cluster around the average (about 3 minutes and 15
seconds).

Since the average of the durations is taken into account, in order to have a more representative view of the
data, four gauges were included, showing the minimum value, the maximum value, the median and the
maximum, median and standard deviation of the durations [Figure 20]. In addition, it is
In addition, it is possible to manually explore (by clicking on the bar graph) the characteristics referring
individual track position in the album, thus having a clearer and more granular perspective.
clearer and more granular perspective.

To conclude the study concerning the duration of tracks, these were
analysed on an annual basis using a scatter plot [Figure 21]. The
The resulting distribution shows a very clear trend: as the years pass, there is a constant
years, there is a constant homogenisation of the duration of the tracks, which tends to
assume a value of around 4 minutes and 30 seconds.
For example, selecting the average duration of the songs at the first position
within the album, it can be seen that the corresponding median value (around 5 minutes)
is lower than the mean (7 minutes) and that the standard deviation is rather high
[Figure 22]. With regard to the temporal analysis, from 1950 until 1 99 0 the duration is
fairly heterogeneous, whereas in the 2000s it settles at around 6 minutes. This
This is reflected in the fact that, in general, the first song of the album (usually the intro) is one of the most elaborate, long and
of the most elaborate, long and finished songs.

### 4.2 Analysis of characteristics according to musical tonality

The last analysis carried out on music tracks focuses on the emotions concerning
a song or an album. Until the beginning of the 20th century, it was common to associate musical keys
musical keys with specific emotional or qualitative characteristics, both on the part of the authors and
by the audience. Therefore, since the dataset in question is composed for a
conspicuous part by classical music, the presence of such associations was checked, making
use of 5 available features: valence (how positively a song is perceived)
the energy, the presence of acoustic instruments, the tempo (indicating the trend) and the
loudness. To detect a match, a legend was also added showing the
musical tonalities and their emotional associations [Figure 23].

First, through a graph it was possible to see the average trend of both the
valence and tempo according to artist, album or track [Figure 24]. Indeed,
the analysis is more effective when considering albums rather than individual artists, since
in most cases an artist may write tracks with different valences. From the
graph, a positive correlation between valence and time is apparent at first glance, however
only these two measures are not able to provide broader information;
for example, a sad song might be associated with a slow tempo and -
vice versa - a happy song might be characterised by a faster tempo,
but in more complex cases (such as the concept of imaginative or restless), the valence and the
tempo are not sufficient.

Therefore, four meters and a table were included: the former indicate the average energy
average, the presence of acoustic instruments on average, average time and average noise,
while the table presents on the rows the various musical keys and on the columns its
possible modes (major and minor) and the average valence measure [Figure 25].

Thanks to the other measures, it is also possible to interpret other types of songs, e.g.
e.g. when the valence may not be accurate, mainly due to the fact that the
average valence of the keys does not exceed 0.30.


For example, selecting Mozart's 'Agnus Dei - Classical Music for
Reflection and Meditation', it turns out that the famous 'Lacrimosa', written in D minor
(tonality associated with the serious and the devout), is indeed a mournful and sad track
[Figure 26], since - apart from the valence near 0 - the energy is very low, the use of
acoustic instruments is very high, the tempo is far below average and the noisiness
is not excessive.

If one selects 'Piano Sonata No. 3 in C Major, Op. 2 No. 3: III. Scherzo. Allegro' by
Beethoven which is written in C major (happy), one can see that the valence is well
above average and that - in line with a feeling of happiness - the tempo is high but with
a discrete loudness [Figure 27].

Instead, considering a different genre, one can find that AC/DC's 'T.N.T.',
written in A major, actually expresses satisfaction and optimism [Figure 28];
although the valence does not seem so high (0.44), at the same time it can be noted that
energy, time and - above all - noise are high. From this example, we
realises that the valence measurement alone, especially when the track is not of classical music
classical music, should not be considered in an absolute sense, but in conjunction with the
other measures.

Thus, from this analysis it was possible to trace a largely
qualitative (such as the emotions that are perceived by the listener and composed) from
from quantitative measures. Of course, the result is by no means optimal, but
this is due to the fact that music changes and evolves over time.

## 5. Conclusions

From the study performed, the analyses are essentially descriptive and - as a
consequently - refer to information inherent in the dataset, whereby they merely
bring to light correlations and relationships present in the data.

### 5.1 Artists

Initial analyses immediately revealed that most of the artists in the dataset
come from the United States and the United Kingdom; this is representative of the importance
of the music industry in these countries, which together have approximately 51 % of the artists
of the artists in the dataset (212 out of 417). Note also the total absence of artists from
from Africa and South Central Asia.

### 5.2 Tracks

The majority of the analyses carried out focus on the nature of the songs present in the
dataset. By studying the main characteristics associated with them, we were able to
confirm certain hypotheses concerning the music that surrounds us.

In particular, it was possible to observe that:

- A loud song is almost always a song that conveys a lot of energy;
- Acoustic songs in most cases are associated with a more
    sad or relaxed mood.

The analysis regarding musical notes and modes (major/minor) made it possible to
observe which tonalities are most commonly used in different countries. As might be expected, in many
Western countries, the key of C major is the most widely used; however, it is
possible to notice a certain variance in the different countries.

It is also important to mention the fact that in our case this type of analysis has
more valid at the country level (Country), as the limited size of the dataset means that
that going down to the lower levels of the hierarchy, the data available for each
the lower levels of the hierarchy, the data available for each region/city are so few that the results lose their meaning and are no longer indicative of the real situation.
are no longer indicative of the actual situation.


From the study performed, the analyses are essentially descriptive and - as a
consequently - refer to information inherent in the dataset, whereby they merely
bring to light correlations and relationships present in the data.

### 5.1 Artists

Initial analyses immediately revealed that most of the artists in the dataset
come from the United States and the United Kingdom; this is representative of the importance
of the music industry in these countries, which together have approximately 51 % of the artists
of the artists in the dataset (212 out of 417). Note also the total absence of artists from
from Africa and South Central Asia.

### 5.2 Tracks

The majority of the analyses carried out focus on the nature of the songs present in the
dataset. By studying the main characteristics associated with them, we were able to
confirm certain hypotheses concerning the music that surrounds us.

In particular, it was possible to observe that:

- A loud song is almost always a song that conveys a lot of energy;
- Acoustic songs in most cases are associated with a more
    sad or relaxed mood.

The analysis regarding musical notes and modes (major/minor) made it possible to
observe which tonalities are most commonly used in different countries. As might be expected, in many
Western countries, the key of C major is the most widely used; however, it is
possible to notice a certain variance in the different countries.

It is also important to mention the fact that in our case this type of analysis has
more valid at the country level (Country), as the limited size of the dataset means that
that going down to the lower levels of the hierarchy, the data available for each
the lower levels of the hierarchy, the data available for each region/city are so few that the results lose their meaning and are no longer indicative of the real situation.
are no longer indicative of the actual situation.

#### Disclaimer: images in the text refer to 'Relazione_Qlik_Tableau_PowerBI.pdf' file
