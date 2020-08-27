# Analyzing Voice2020 data.


## Source data

There are 2 sourcs datasets to analyze


### geo-categorized.csv

Columns: 
* `id` - poll station unique identifier.
* `latitude`, `longitude` - poll station coordinates.
* `town` - town/city/village where poll station is situated.
* `area` - one of the following:
 * `village`
 * `town_below100` (towns with population less than 100.000 people)
 * `town_over100` (one of 9 towns with population over 100.000)
 * `city` (one of 5 region centers)
 * `capital` (Minsk)
 * `embassy`
 * `suburb` (20 km around Minsk, 10 km around other region centers)

### voice.csv

Data from the Voice patform. Rows have the following key fields:
* `id` - poll station unique identifier
* `tihanovkaja_registered` - number of users who was going to vote for **tihanovkaja**
* `tihanovkaja_photoVoices` - number of ballots for **tihanovkaja** approved by photo
* `tihanovkaja_officialVotes` - number of ballots for **tihanovkaja** according to an official report
* and 18 more similar columns prefixed by:
  * `dmitriyev`
  * `kanopatskaja`
  * `lukashenko`
  * `cherechen` 
  * `against` (against evryone)
  * `corrupted` (ballot is corrupted by a voter)
  
 
  
## Clearing the data.

Poll stations results are filtered:
* Number of **official** voters for each candidate should be **greater or the same** as the number of ballot **photos** in the Voice platform.
* Any **outliers** explained by a Zubr report (rules violations) or poll station location (like hospital) should be removed.

List of `823` poll stations with trusted results can be found in `trusted-for-alternative.csv` 

## Building coefficiets.
After exploring the data following algorith was chosen:
```
Voice photos number -> Tsikhanouskaya estimated voters number -> Other alternative candidates voters number
```

Each step is a linear regression `y=kx` with coefficient `k` calculated separately for each kind of `area`.

Voice-to-Tsikhanouskaya coefficients can be found in `tihanovkaja_photo_coefficients.csv`

|area|coefficient|
|---|---|
|capital|1.9|
|city|2.6|
|suburb|2.2|
|town_over100|3.2|
|town_below100|3.6|
|village|3.3|
|embassy|13.7|


Tsikhanouskaya-to-OtherCandidates coefficients can be found in `alternative_coefficients.csv`. Here are very approximate values for a quick refererence: 


|candidate|coefficient|top areas|
|---|---|---|
|against|0.14|big towns|
|dmitriyev|0.04|villages, big towns|
|cherechen|0.036|big towns|
|kanopatskaja|0.035|villages, capital|
|corrupted|0.03|capital, big towns|


## Calculating results.

There are 58 poll stations with missing Voice data which is 1% of total number. They are just ignored.

### candidates-by-poll-station.csv

* `id` - poll station unique identifier.
* `area` - kind of area (see **geo-categorized.csv**)
* `source` - one of:
 * `official` - numbers from official report
 * `official-corrupted-fix` - numbers from official report with estimated number for the `corrupted` column (it's often missed)
 * `estimated` - estimated number based on coefficients described above.
* `tihanovkaja`, `cherechen`,	`dmitriyev`,	`kanopatskaja`, `against`, `corrupted` - official or estimated number of votes for the candidate

### candidates-total.csv

Absolute values for votes for each alternative candidate:

|candidate|estimated_total|
|---|---|
|tihanovkaja|1427032|
|against|218322|
|dmitriyev|63605|
|cherechen|57309|
|kanopatskaja|54572|
|corrupted|42723|

## Estimated final results.

Turnout should be estimated separately after analyzis of Zubr data. After finding out turnout for each area/region trustworthy relative resuts per each poll station can be obtained. But so far considering 75% turnout through the country (vs 84% officially):

Lukashenko - 64%
Tsikhanouskaya - 27%
Against everyone - 4%
Dmitriyev, Cherechen, Kanopatskaja - 5% in toatal 

