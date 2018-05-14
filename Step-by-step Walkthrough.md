# Step-by-step Walkthrough to Analyze a Collection Described in MARC

## Harvest the appropriate MARC data

### Catalog records to txt file
(best recollections)
The most useful field I found to pull up movies without getting too many additional records or missing many records was to
search call number for "DVD ". "M-DVD" and "BRV" would need to be included for a complete list, but most M-DVDs won't have a traditional director, and the BRV list is very small. I then used an export feature to harvest those MARC files, pulled them into MARCEdit, and harvested
only the most important fields into a tab-delimited text file.

### Txt file to useable data for Python
Used OpenRefine and GREL to pull out the first name in the 700 fields. It is possible that this work could be made unnecessary
if a skilled MARCEdit user can pull only the 700 fields with dir, director, (Director), or another indication of the director.
This would also improve the accuracy, because currently a small minority of the names we pulled are not the director of the film.

### Use data to identify which webpages need to be scraped
This is the current step. It will likely be an interative process of trying a particular website, and exploring which makes it
easiest to pull only gender data about only these people. We may have to pull a set of pronouns from each webpage and assume
gender based on the dominant pronouns.

We've decided to start with 3 sources that could be harvested with copy and paste: 
1. [Wikipedia's female directors list](https://en.wikipedia.org/wiki/List_of_female_film_and_television_directors)(harvested 4/6/2018)
2. Annenberg Inclusion Initiative's [Inclusion in the Director's Chair](http://assets.uscannenberg.org/docs/inclusion-in-the-directors-chair-2007-2017.pdf), pg. 6 (harvested 5/14/2018)
3. Collider.com's [The Most Exciting Female Directors Working Today](http://collider.com/best-female-directors/) (havested 5/14/2018)

The following sites should be harvested and unique entries added to our list:
1. [Women Make Movies](wmm.com)
2. [Alliance of Women Directors](https://www.allianceofwomendirectors.org/find-a-director/) 
* can be harvested on <h2 data-label="Firstname" class="wppb-name">!


### Pull the data from the webpage scraping back into our dataset
This step may become the same as the step before. It may require a complicated string analysis of pronouns and concatenation of
the prominent pronoun into the data. We'll see!

For wikipedia, we just copied the names into an excel file, opposite a column with names of the directors pulled from a text facet in OpenRefine. 

### Import the revise txt into an Analytics tool
Do analysis to assess what percentage of this subset of DVDs have female directors.

With the initial sample compared to the list of directors on wikipedia, we inferred that 2/200 or 1% of our sample films were directed by women.

We may get better results using the [Microsoft Fuzzy Look-up tool](http://www.k2e.com/tech-update/tips/431-tip-fuzzy-lookups-in-excel) during this part of the process.

### Run again on the entire data set

### Edit and run again on a slightly different data set
* if 100 e is composer or 700 4 is cmp or e is composer, return data
* if composer is of African Descent
* 655 of poetry and gender of the 100 field if present?
* 651 of LaColl and places associated with the 1xx?
* birth date of authors in LAL?
