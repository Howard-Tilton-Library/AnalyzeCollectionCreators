# Step-by-step Walkthrough to Analyze a Collection Described in MARC

## Harvest the appropriate MARC data

### Catalog records to txt file
1. Find the appropriate selection of bibnumbers for the particular project. For this original project, the best set was found by doing a query on location hmc for Howard-Tilton Media Collection in Access. I then limited to unsuppress records and records that include at least one holdings record. Finally, I filtered the normalized call numbers by those that contain DVD, and sorted by that field. I then copied and pasted the Bib IDs or bib numbers into a plain text file. Each number is delimited with a hard return, including the final number.

The query I used was:
```
SELECT BIB_MASTER.BIB_ID, BIB_MASTER.SUPPRESS_IN_OPAC, utf8to16([bib_text].[AUTHOR]) AS AUTHOR, utf8to16([bib_text].[TITLE_BRIEF]) AS TITLE_BRIEF, BIB_TEXT.BIB_FORMAT, MFHD_MASTER.MFHD_ID, MFHD_MASTER.SUPPRESS_IN_OPAC, MFHD_MASTER.NORMALIZED_CALL_NO, MFHD_MASTER.DISPLAY_CALL_NO, LOCATION.LOCATION_CODE INTO [Shelflist by Location Code]
FROM (BIB_MASTER INNER JOIN ((BIB_MFHD INNER JOIN MFHD_MASTER ON BIB_MFHD.MFHD_ID = MFHD_MASTER.MFHD_ID) INNER JOIN LOCATION ON MFHD_MASTER.LOCATION_ID = LOCATION.LOCATION_ID) ON BIB_MASTER.BIB_ID = BIB_MFHD.BIB_ID) INNER JOIN BIB_TEXT ON BIB_MASTER.BIB_ID = BIB_TEXT.BIB_ID
WHERE (((LOCATION.LOCATION_CODE)=[Location Code]));
```

2. I used vgerselect to pull all of the MARC record information for every one of those bibnumbers using the ??? process. This created a mrc file and a tab-delimited text with the MARC data.

3. One option at this point is to directly pull open the tab-delimited text file in OpenRefine. However, I recommend using MARCEdit on the mrc file first and pulling out particular MARC fields and subfields. Technically this can be done with vgerselect, but vgerselect requires you to type in the exact fields every time. MARCEdit allows you to create, export, and import a file with details about which fields and subfields you want to include in your tab-delimited text...which is ideal when you want to reuse this process on different sets of data.

### Txt file to useable data for Python

1. We used OpenRefine and GREL/Jython to pull out the first name in the 700 fields. It is possible that this work could be made unnecessary if a skilled MARCEdit user can pull only the 700 fields with dir, director, (Director), or another indication of the director. This would also improve the accuracy, because currently a small minority of the names we pulled are not the director of the film. However, the current scripts we used can be found at [rtilla/JythonScripts](https://github.com/rtilla1/JythonScripts).

### Use data to identify which webpages need to be scraped
This is the most difficult step. It will likely be an interative process of trying a particular website, and exploring which makes it
easiest to pull only gender data about only these people. We may have to pull a set of pronouns from each webpage and assume
gender based on the dominant pronouns.

We've decided to start with 3 sources that could be harvested with copy and paste: 
1. [Wikipedia's female directors list](https://en.wikipedia.org/wiki/List_of_female_film_and_television_directors)(harvested 4/6/2018)
2. Annenberg Inclusion Initiative's [Inclusion in the Director's Chair](http://assets.uscannenberg.org/docs/inclusion-in-the-directors-chair-2007-2017.pdf), pg. 6 (harvested 5/14/2018)
3. Collider.com's [The Most Exciting Female Directors Working Today](http://collider.com/best-female-directors/) (havested 5/14/2018)

* For many of these resources, some clean-up in Excel was necessary. For this process, Flash Fill was very useful. Flash Fill allows you to make the necessary edits to a small number of cells in a column. After doing so, you can select Data > Flash Fill and it will attempt to recreate those edits for the rest of the cells in the column. If it gets it partially correct, you can continue to go down the column and make edits and it will "learn" from what you do to improve the results.

Example:
Copying the source data from the wikipedia page above results in list data that looks like this:

```
<li><a href="/wiki/Jennifer_Abbott" title="Jennifer Abbott">Jennifer Abbott</a></li>
<li><a href="/w/index.php?title=Marcelle_Abela&amp;action=edit&amp;redlink=1" class="new" title="Marcelle Abela (page does not exist)">Marcelle Abela</a> (Malta)</li>
<li><a href="/wiki/Marguerite_Abouet" title="Marguerite Abouet">Marguerite Abouet</a></li>
<li><a href="/wiki/Abiola_Abrams" title="Abiola Abrams">Abiola Abrams</a></li>
<li><a href="/wiki/Jill_Ackles" title="Jill Ackles">Jill Ackles</a> (USA)</li>
<li><a href="/wiki/Sangeeta_(Pakistani_actress)" title="Sangeeta (Pakistani actress)">Sangeeta</a></li>
<li><a href="/wiki/Joey_Lauren_Adams" title="Joey Lauren Adams">Joey Lauren Adams</a> (USA)</li>
```
Creating a new column and copying over just the data between the <a> elements for the first three lines, then selecting Flash Fill, should result in this list:
 
 ```
Jennifer Abbott
Marcelle Abela
Marguerite Abouet
Abiola Abrams
Jill Ackles
Sangeeta
Joey Lauren Adams
```


The following sites should be harvested and unique entries added to our list:
1. [Women Make Movies](wmm.com)
2. [Alliance of Women Directors](https://www.allianceofwomendirectors.org/find-a-director/) 
  * can be harvested on `<h2 data-label="Firstname" class="wppb-name">`
3. https://www.dga.org/Employers/EmployersSearch.aspx
4. https://www.ranker.com/list/best-female-directors/jeff419
5. http://www.denofgeek.com/us/movies/female-directors/252810/26-female-directors-you-should-know-about-and-why-you-might-not
6. http://www.vulture.com/2015/10/100-women-directors-hollywood-should-be-hiring.html
7. https://www.allianceofwomendirectors.org/find-a-director/
8. Is there a page where google hosts all the carousel data at the top of this search: https://www.google.com/search?client=firefox-b-1-ab&ei=GtP5WviODOXCjwTfsanQAw&q=list+of+directors+female&oq=list+of+directors+female&gs_l=psy-ab.3..0i22i30k1l10.4338.5350.0.5982.7.6.0.0.0.0.170.776.0j6.6.0....0...1c.1.64.psy-ab..1.6.772...0.0.KB_gfxzfI_4
9.	http://www.thedirectorlist.com/database/

### Pull the data from the webpage scraping back into our dataset
This step may become the same as the step before. It may require a complicated string analysis of pronouns and concatenation of
the prominent pronoun into the data. We'll see!

For wikipedia, we just copied the names into an excel file, opposite a column with names of the directors pulled from a text facet in OpenRefine. We used Home>Conditional Formatting>Highlight Cells>Duplicate Values to find the names in from OpenRefine that were included in our compiled list of Female Directors. In some processes I used ASAP Utilities to remove all diacritics from the names, but I'm hoping to find better ways to verify the formatting of the names in the list match exactly and/or creating a method for matching that doesn't require the names to be an exact match.

### Import the revise txt into an Analytics tool
Do analysis to assess what percentage of this subset of DVDs have female directors.

With the initial sample compared to the list of directors on wikipedia, we inferred that 2/200 or 1% of our sample films were directed by women. With our second review (including a set of data with many non-movie DVDs), the percentage went up to 4%.

We may get better results using other tools, including wikidata reconciliation to check sex or gender, or if we are able to create a more comprehensive list of women directors to check for duplicates in Excel. We will also likely improve our results when we are pulling an accurate list of DVDs with actual directors (instead of instructional DVDs or performance DVDs which often don't).

### Run again on the entire data set
This is in process. The "Catalog records to Text File" reflects the most recent version of the process. Other steps will be updated as we go.

### Edit and run again on a slightly different data set
* if 100 e is composer or 700 4 is cmp or e is composer, return data
* if composer is of African Descent
* 655 of poetry and gender of the 100 field if present?
* 651 of LaColl and places associated with the 1xx?
* birth date of authors in LAL?
