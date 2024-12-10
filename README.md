Shield: [![CC BY-NC 4.0][cc-by-nc-shield]][cc-by-nc]

This work is licensed under a
[Creative Commons Attribution-NonCommercial 4.0 International License][cc-by-nc].

[![CC BY-NC 4.0][cc-by-nc-image]][cc-by-nc]

[cc-by-nc]: https://creativecommons.org/licenses/by-nc/4.0/
[cc-by-nc-image]: https://licensebuttons.net/l/by-nc/4.0/88x31.png
[cc-by-nc-shield]: https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg

# LC-OpenRefine-COUNTER-WorldCat
Connecting Project COUNTER ebook usage data to Library of Congress Classification and Library of Congress Subject Headings from the WorldCat API using OpenRefine.

### Connecting Project COUNTER ebook usage data with Library of Congress metadata

The following is a set of instructions (hopefully easy enough to follow) for collection development librarians to create a data set that merges their Project COUNTER ebook useage with [Library of Congress (LoC) Classification](https://www.loc.gov/catdir/cpso/lcc.html) and [LoC Subject Headings](https://www.loc.gov/aba/publications/FreeLCSH/freelcsh.html) as a way to see and assess their ebook subject collections.  The Project COUNTER data only gives us ISBN, Title, and Publisher and I construe that this is not enough data to meaningfully understand how much of the ebook content I acquire in my collection is receiving use.

This DIY (do it yourself) stey-by-step guide incorporates [OpenRefine](https://openrefine.org/), [WorldCat API](https://www.oclc.org/developer/api/oclc-apis/worldcat-search-api.en.html), and [Microsoft Excel](https://www.microsoft.com/en-us/microsoft-365/excel).

### Step 1
Gather a small set of your ebook Project COUNTER data to test in .csv file format (I used Microsoft Excel).  If you want a practice set, I have included a [10-line csv file](github_play_data_set_COUNTER.csv)  

Note:  If you have your COUNTER data, you will want to remove the ISBN dashes. In Excel, --> change the format of that column to **number** with no decimals and then select said column and do a **replace all** function for the dash with nothing.  

### Step 2
In this step you will enage OpenRefine.  I learned about [OpenRefine](https://openrefine.org/) in a [Library Carpentry](https://librarycarpentry.org/) workshop and [their publically available documentation](https://librarycarpentry.github.io/lc-open-refine/) may be useful. 

Basically, you need to [download OpenRefine](https://openrefine.org/download).  Once you have downloaded OpenRefine and created an account, upload your csv COUNTER data file by doing:  
1. Create Project;
2. Get Data from (this computer) and Choose Files;
3. Click Next;
4. In the Parse Data section at the bottom half of your screen, choose **COMMAS** (csv);
5. At top, give your project a sensible name; include any tags you want for managing your OpenRefine files;
6. Click Create Project in the upper right corner.
7. Once done, your data should open in the main OpenRefine environment.  If it does not, reload your browser and choose Open Project and choose this project.
8. At this point, your basic csv file should look familiar in OpenRefine.

### Step 3
This step involves using the WorldCat API having a [WorldCat Search API 2.0 WSKey](https://www.oclc.org/developer/api/oclc-apis/worldcat-search-api.en.html). I was able to obtain my Libraries' **WSKey** from my technical services/information technology department colleagues.  If you are unable to obtain this, you may request a temporary one from the aforementioned OCLC website. Here is an excellent [blog post from OCLC by Karen Combs on how to obtain a WSKey](https://www.oclc.org/developer/news/2018/using-open-refine-and-metadata-to-get-current-oclc-numbers.en.html).  

In this step, you will retrieve the xml records from WorldCat, matching on the ISBN in your Project COUNTER data.  We will be asking WorldCat to bring back ALL the records for each ISBN.  In many cases, there are several records per book in WorldCat, based on various formats and other cataloging considerations I do not fully understand! [(Invoke FRBR here.)](https://www.oclc.org/research/activities/frbr.html)

In fussing over this project, I learned that some XML records for a book do not include much in the way of useful metadata, including some records not having any Library of Congress Classification or Library of Congress Subject Headings.  Hence, by bringing in ALL the xml records you have a much better chance of extracing LCC and LCSH per book. 

In this step you will be writing **GREL expressions** [(General Refine Expression Language)](https://openrefine.org/docs/manual/grel) in OpenRefine to query the WorldCat API.  A bit of writing code, if you will.  
1. While in your OpenRefine Project: Click on the arrow in the ISBN column;
2. Click on Edit column;
3. Click on Add column by fetching URLs;
4. Give the new column a Name; OCLC XML;
5. In the GREL express box, replace Value with (Making sure to substitue your WSKey where I have _REPLACE WITH YOUR WSKEY_ in the above code.):

````
"http://www.worldcat.org/webservices/catalog/search/sru?servicelevel=full&wskey=REPLACE WITH YOUR WSKEY&query=srw.bn=" + value + "&recordSchema=info%3Asrw%2Fschema%2F1%2Fdc&frbrGrouping=off"
````
  
6. Click OK.

Now notice that you have a LOT of XML data in your new OCLC XML column.  Each individual XML record is divided by the tags <record> and <\record>.  Each ISBN may have n number associated XML records.  

### Step 4
It is now time to extract the LCC and LCSH from each XML record for each ISBN

This portion of the work was heavily influenced by the excellent support received from the [OpenRefine Forum](https://forum.openrefine.org/t/parsing-xml-from-worldcat-records/1689) [by Owen Stephens](https://forum.openrefine.org/u/ostephens/summary).

1. In the down arrow in the OCLC XML column header, select Edit and Add Column based on this Column;
2. Add a New Column, call it LCCall;
3. In the Expression Box, replace Value with:
````
forEach(value.parseXml().select("dc|subject[xsi:type=http://purl.org/dc/terms/LCC]"),x,x.ownText()).join("|")
````
4. notice in the preview window you are extracting LCC with the | divider!;
5. click OK.

Let's do the same for adding an LCSH column.
1. In the down arrow in the OCLC XML column header, select Edit and Add Column based on this Column;
2. Add a New Column, call it LCSHall;
3. In the Expression Box, replace Value with:
````
forEach(value.parseXml().select("dc|subject[xsi:type=http://purl.org/dc/terms/LCSH]"),x,x.ownText()).join("|")
````
4. Again, notice in the preview window that you are extracting LCSH with the | divider!;
5. Click OK.


### Step 5
At this point, you have choices as to how you want to work with your merged data.  I am still unable to separate the unique LCC and LCSH retrieved and extracted from the WorldCat XML.  A colleague in my library was able to achieve this with python which I am not going to share because it is not my work and evidently not easy when I wanted to share something an average subject librarian could do themselves.

I did want to separate each LCC and LCSH from the cell in which they are combined.  This is what I did:
1. In the dropdown arrow for the LCCall column, choose Edit Column --> Split into Several Columns
2. UNCHECK the box Remove this Column
3. Change the comma seperator/delimitter to the Pipe |
4. Click OK.
5. Do almost the same for the LCSHall column, but first remove the periods from the delimitted data.
6. In the dropdown arrow for the LCSHall column, choose Edit Cells --> Replace
7. In the Find field, type a period (.).
8. In the Replace field, add nothing.
9. Click OK.
10. Proceed as above: In the dropdown arrow for the LCSHall column, choose Edit Column --> Split into Several Columns
11. UNCHECK the box Remove this Column
12. Change the comma seperator/delimitter to the Pipe |
13. Click OK.
