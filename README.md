# WebCrawler
This project has two different type of web-crawling projects with JavaScript visualization.

**GMANE Data Visualizer using the D3 JavaScript library**

The first step is to spider the gmane repository.  The base URL is hard-coded in the gmane.py and is hard-coded to the Sakai developer list.  You can spider another repository by changing that base url.   Make sure to delete the content.sqlite file if you switch the base url.  The gmane.py file operates as a spider in that it runs slowly and retrieves one mail message per second so as to avoid getting throttled by gmane.org.   It stores all of its data in a database and can be interrupted and re-started as often as needed.   It may take many hours to pull all the data down. So you may need to restart several times.Sometimes gmane.org is missing a message.  Perhaps administrators can delete messages or perhaps they get lost - I don't know.   If your spider stops, and it seems it has hit a missing message, go into the SQLite Manager and add a row with the missing id - leave all the other fields blank - and then restart gmane.py.   This will unstick the spidering process and allow it to continue.  These empty messages will be ignored in the next
phase of the process.

The content.sqlite data is pretty raw, with an innefficient data model, and not compressed.
This is intentional as it allows you to look at content.sqlite to debug the process.
It would be a bad idea to run any queries against this database as they would be 
slow.

The second process is running the program gmodel.py.  gmodel.py reads the rough/raw 
data from content.sqlite and produces a cleaned-up and well-modeled version of the 
data in the file index.sqlite.  The file index.sqlite will be much smaller (often 10X
smaller) than content.sqlite because it also compresses the header and body text.

Each time gmodel.py runs - it completely wipes out and re-builds index.sqlite, allowing
you to adjust its parameters and edit the mapping tables in content.sqlite to tweak the 
data cleaning process.
The gmodel.py program does a number of data cleaing steps

Domain names are truncated to two levels for .com, .org, .edu, and .net 
other domain names are truncated to three levels.  So si.umich.edu becomes
umich.edu and caret.cam.ac.uk becomes cam.ac.uk.   Also mail addresses are
forced to lower case and some of the @gmane.org address like the following

   arwhyte-63aXycvo3TyHXe+LvDLADg@public.gmane.org

are converted to the real address whenever there is a matching real email
address elsewhere in the message corpus.
There is a simple vizualization of the word frequence in the subject lines
in the file gword.py:

Mac: python3 gword.py
Win: gword.py

![gword](https://user-images.githubusercontent.com/65606499/146014620-80cebeea-d9ea-4985-b68e-4a6e74b3353e.png)

A second visualization is in gline.py.  It visualizes email participation by 
organizations over time.

Mac: python3 gline.py 
Win: gline.py 

![gline](https://user-images.githubusercontent.com/65606499/146014800-e5019632-6138-406e-805c-4eb766aabe56.png)

**PageRank Visualization**

Simple Python Search Spider, Page Ranker, and Visualizer

This is a set of programs that emulate some of the functions of a 
search engine.  They store their data in a SQLITE3 database named
'spider.sqlite'.  This file can be removed at any time to restart the
process.   

You should install the SQLite browser to view and modify 
the databases from:

http://sqlitebrowser.org/

This program crawls a web site and pulls a series of pages into the
database, recording the links between pages.

Mac: rm spider.sqlite
Mac: python3 spider.py

Win: del spider.sqlite
Win: spider.py

In this sample run, we told it to crawl a website and retrieve two 
pages.  If you restart the program again and tell it to crawl more
pages, it will not re-crawl any pages already in the database.  Upon 
restart it goes to a random non-crawled page and starts there.  So 
each successive run of spider.py is additive.

Mac: python3 spider.py 
Win: spider.py

You can have multiple starting points in the same database - 
within the program these are called "webs".   The spider
chooses randomly amongst all non-visited links across all
the webs.

If you want to dump the contents of the spider.sqlite file, you can 
run spdump.py as follows:

Mac: python3 spdump.py 
Win: spdump.py

This shows the number of incoming links, the old page rank, the new page
rank, the id of the page, and the url of the page.  The spdump.py program
only shows pages that have at least one incoming link to them.

Once you have a few pages in the database, you can run Page Rank on the
pages using the sprank.py program.  You simply tell it how many Page
Rank iterations to run.

Mac: python3 sprank.py 
Win: sprank.py 

You can dump the database again to see that page rank has been updated:

Mac: python3 spdump.py 
Win: spdump.py 

You can run sprank.py as many times as you like and it will simply refine
the page rank the more times you run it.  You can even run sprank.py a few times
and then go spider a few more pages sith spider.py and then run sprank.py
to converge the page ranks.

If you want to restart the Page Rank calculations without re-spidering the 
web pages, you can use spreset.py

Mac: python3 spreset.py 
Win: spreset.py 

All pages set to a rank of 1.0

Mac: python3 sprank.py 
Win: sprank.py 

For each iteration of the page rank algorithm it prints the average
change per page of the page rank.   The network initially is quite 
unbalanced and so the individual page ranks are changing wildly.
But in a few short iterations, the page rank converges.  You 
should run prank.py long enough that the page ranks converge.

If you want to visualize the current top pages in terms of page rank,
run spjson.py to write the pages out in JSON format to be viewed in a
web browser.

Mac: python3 spjson.py 
Win: spjson.py 

Creating JSON output on spider.js...
How many nodes? 30
Open force.html in a browser to view the visualization

You can view this data by opening the file force.html in your web browser.  
This shows an automatic layout of the nodes and links.  You can click and 
drag any node and you can also double click on a node to find the URL
that is represented by the node.

![force2](https://user-images.githubusercontent.com/65606499/146015764-4a8f5a5c-229b-4f3d-b345-b1a453504280.png)
