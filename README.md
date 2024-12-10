# LC-OpenRefine-COUNTER-WorldCat
Connecting Project COUNTER ebook usage data to Library of Congress Classification and Library of Congress Subject Headings from the WorldCat API using OpenRefine

### Connecting Project COUNTER ebook usage data with Library of Congress Classification Numbers 

The following is a set of instructions (hopefully easy enough to follow) for collection development librarians to create a data set the merges their Project COUNTER ebook useage with [Library of Congress (LoC) Classification](https://www.loc.gov/catdir/cpso/lcc.html) and [Loc Subject Headings](https://www.loc.gov/aba/publications/FreeLCSH/freelcsh.html) as a way to see their ebook subject collections.  The Project COUNTER data only gives us ISBN, Title, and Publisher to work with and I construe that this is not enough data to look meaningfully at how much ebook content my collection is receiving from what I acquire.  I have wanted to tackle this issue for years, so that I may have data to use to assess my collection useage akin to how I assess ejournal use.

This DIY (do it yourself) stey-by-step guide incorporates [OpenRefine](https://openrefine.org/), [WorldCat API](https://www.oclc.org/developer/api/oclc-apis/worldcat-search-api.en.html), and [Microsoft Excel](https://www.microsoft.com/en-us/microsoft-365/excel).

### Step 1
Gather a small set of your ebook Project COUNTER data to test in .csv file format.  If you want a practice set, I have included a [10-line csv file](github_play_data_set_COUNTER.csv)  
Notes:  If you have your COUNTER data and want to remove the ISBN dashes, first change the format of that column to Number with no decimals and then select said column and do a Replace All function for the dash with nothing.  

### Step 2
In this step you will enage OpenRefine.  I learned about [OpenRefine](https://openrefine.org/) in a [Library Carpentry](https://librarycarpentry.org/) workshop and [their publically available documentation](https://librarycarpentry.github.io/lc-open-refine/) may be useful. Basically, you need to [download OpenRefine](https://openrefine.org/download).


### Step 3
This step involves having a [WorldCat Search API 2.0 WSKey](https://www.oclc.org/developer/api/oclc-apis/worldcat-search-api.en.html). I was able to obtain my Libraries' WSKey from my technical services/ information technology department colleagues.  If you are unable to obtain this, you may request a temporary one from the aforementioned OCLC website.


