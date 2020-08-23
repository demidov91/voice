# Analyzing Voice2020 data.

There are 2 sourcs datasets to analyze


## geo-categorized.csv

Polling station to area mapping. `area` value is one of:
* village
* town_below100
* town_over100 (one of 9 towns with population over 100.000)
* city (one of 5 region centers)
* capital (Minsk)
* embassy
* suburb (20 km around Minsk, 10 km around other region centers)

There are also `latitude`, `longitude` and `town` columns for convinience.

## voice.csv

Data from the Voice patform. Polling station to the platform-registered and official results mapping.
Rows have the following key fields:
* `id` - polling station unique identifier
* `tihanovskaja_registered` - number of users who was going to vote for **tihanovskaja**
* `tihanovskaja_photoVoices` - number of ballots for **tihanovskaja** approved by photo
* `tihanovkaja_officialVotes` - number of ballots for **tihanovskaja** according to an official report
* and 18 more similar columns prefixed by:
  * `dmitriyev`
  * `kanopatskaja`
  * `lukashenko`
  * `cherechen` 
  * `against` (against evryone)
  * `corrupted` (ballot corrupted be a voter)
  

