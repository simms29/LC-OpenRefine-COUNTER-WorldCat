# THIS PAGE IS BEING CREATED
Shield: [![CC BY-NC 4.0][cc-by-nc-shield]][cc-by-nc]

This work is licensed under a
[Creative Commons Attribution-NonCommercial 4.0 International License][cc-by-nc].

[![CC BY-NC 4.0][cc-by-nc-image]][cc-by-nc]

[cc-by-nc]: https://creativecommons.org/licenses/by-nc/4.0/
[cc-by-nc-image]: https://licensebuttons.net/l/by-nc/4.0/88x31.png
[cc-by-nc-shield]: https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg

# LC-OpenRefine-COUNTER-WorldCat
Connecting Project COUNTER ebook usage data to Library of Congress Classification and Library of Congress Subject Headings from the WorldCat API using OpenRefine

### Connecting Project COUNTER ebook usage data with Library of Congress Classification Numbers 

The following is a set of instructions (hopefully easy enough to follow) for collection development librarians to create a data set that merges their Project COUNTER ebook useage with [Library of Congress (LoC) Classification](https://www.loc.gov/catdir/cpso/lcc.html) and [Loc Subject Headings](https://www.loc.gov/aba/publications/FreeLCSH/freelcsh.html) as a way to see their ebook subject collections.  The Project COUNTER data only gives us ISBN, Title, and Publisher to work with and I construe that this is not enough data to look meaningfully at how much ebook content my collection is receiving from what I acquire.  I have wanted to tackle this issue for years, so that I may have data to use to assess my collection useage akin to how I assess ejournal use.

This DIY (do it yourself) stey-by-step guide incorporates [OpenRefine](https://openrefine.org/), [WorldCat API](https://www.oclc.org/developer/api/oclc-apis/worldcat-search-api.en.html), and [Microsoft Excel](https://www.microsoft.com/en-us/microsoft-365/excel).

### Step 1
Gather a small set of your ebook Project COUNTER data to test in .csv file format.  If you want a practice set, I have included a [10-line csv file](github_play_data_set_COUNTER.csv)  
Notes:  If you have your COUNTER data and want to remove the ISBN dashes, first change the format of that column to Number with no decimals and then select said column and do a Replace All function for the dash with nothing.  

### Step 2
In this step you will enage OpenRefine.  I learned about [OpenRefine](https://openrefine.org/) in a [Library Carpentry](https://librarycarpentry.org/) workshop and [their publically available documentation](https://librarycarpentry.github.io/lc-open-refine/) may be useful. Basically, you need to [download OpenRefine](https://openrefine.org/download).  Once you have downloaded OpenRefine and created an account, upload your csv COUNTER data file by doing:  
1. Create Project
2. Get Data from (this computer) and Choose Files
3. click Next
4. in the Parse Data section at the bottom half of your screen, choose **COMMAS** (csv)
5. at top, give your project a sensible name; include any tags you want for managing your OpenRefine files
6. click Create Project in the upper right corner
7. once Done, it should open in the main OpenRefine.  If it does not, reload your browser and choose Open Project and choose this project.
8. at this point, your basic csv file should look familiar in OpenRefine.



### Step 3
This step involves having a [WorldCat Search API 2.0 WSKey](https://www.oclc.org/developer/api/oclc-apis/worldcat-search-api.en.html). I was able to obtain my Libraries' WSKey from my technical services/ information technology department colleagues.  If you are unable to obtain this, you may request a temporary one from the aforementioned OCLC website. Here is an excellent [blog post from OCLC by Karen Combs on how to obtain a WSKey](https://www.oclc.org/developer/news/2018/using-open-refine-and-metadata-to-get-current-oclc-numbers.en.html).  
In this step, you will retrieve the xml records from WorldCat, matching on the ISBN in your Project COUNTER data.  We will be asking WorldCat to bring back ALL the records for each ISBN.  In many cases, there are several records per book in WorldCat, based on various formats and other cataloging considerations I do not fully understand (!). However, in doing this, I learned that some XML records for a book do not include much in the way of useful metadata, including something not having Library of Congress Classification or Library of Congress Subject Headings.  Hence, by bringing in ALL the xml records you have a much better chance of extracing LCC and LCSH per book.  
In this step you will be writing GREL expressions [(General Refine Expression Language)](https://openrefine.org/docs/manual/grel) in OpenRefine to query the WorldCat API.  A bit of writing code, if you will.  
- While in your OpenRefine Project:
- Click on the arrow in the ISBN column
- Click on Edit column
- Click on Add column by fetching URLs
- Give the new column a Name; OCLC XML
- In the GREL express box, replace Value with (Making sure to substitue your WSKey where I have _REPLACE WITH YOUR WSKEY_ in the above code.):

````
"http://www.worldcat.org/webservices/catalog/search/sru?servicelevel=full&wskey=REPLACE WITH YOUR WSKEY&query=srw.bn=" + value + "&recordSchema=info%3Asrw%2Fschema%2F1%2Fdc&frbrGrouping=off"
````
  
- click OK.

Now notice that you have a LOT of XML data in your new OCLC XML column.  Each XML record is divided by the tags <record> and <\record>.  Each ISBN may have any number of associated XML records.  

### Step 4
It is now the time to extract the LCC and LCSH from each XML record for each ISBN.
1. In the down arrow in the OCLC XML column header, select Edit and Add Column based on this Column
2. Add a New Column, call it LCCall
3. In the Expression Box, replace Value with 
````
forEach(value.parseXml().select("dc|subject[xsi:type=http://purl.org/dc/terms/LCC]"),x,x.ownText()).join("|")
````
4. notice in the preview window you are extracting LCC with the | divider!
5. click OK.
6. Let's do the same for adding an LCSH column
7. In the down arrow in the OCLC XML column header, select Edit and Add Column based on this Column
8. Add a New Column, call it LCSHall
9. In the Expression Box, replace Value with
````
forEach(value.parseXml().select("dc|subject[xsi:type=http://purl.org/dc/terms/LCSH]"),x,x.ownText()).join("|")
````
10. Again, notice in the preview window that you are extracting LCSH with the | divider!
11. click OK.
14. 


