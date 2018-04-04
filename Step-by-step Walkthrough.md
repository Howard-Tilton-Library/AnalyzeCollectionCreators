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

We've decided to start with wikipedia's female directors list, and then harvest the more complicated data on wmm.com Women Make Movies, which may require edits to the txt file related to the title fields.

### Pull the data from the webpage scraping back into our dataset
This step may become the same as the step before. It may require a complicated string analysis of pronouns and concatenation of
the prominent pronoun into the data. We'll see!

### Import the revise txt into an Analytics tool
Do analysis to assess what percentage of this subset of DVDs have female directors.

### Run again on the entire data set

### Edit and run again on a slightly different data set
* 655 of poetry and gender of the 100 field if present?
* 651 of LaColl and places associated with the 1xx?
* birth date of authors in LAL?
