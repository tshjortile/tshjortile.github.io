---
layout: post
title: Webscraping with wget
---

Webscraping is a useful tool to get loads of data at once from the internet. One can do that either with a link, or a number of links in a *.txt-document.
What's important: the data has to be open access, otherwise things get complicated (but can be done, anyway).

In class, our assignment was to download the issues from The Daily Times Dispatch, a newspaper that was published in Richmond, Va., in 19th century. It can be found following this [link](http://www.perseus.tufts.edu/hopper/collection?collection=Perseus%3Acollection%3ARichTimes_Nov_1860 "link").
On the webpage, when one clicks on the digital versions of the newspaper, one gets the separated articles. Luckily, there is a xml-version of each.

In order to webscrape the Dispatch, we need to create a list of links. This can be done in two steps:

Step 1: right-click in order to view the site's source code and extract the links with the following regEx:


´´´

href="text(.*)"


´´´

With step 1, we get partial links. These have to be saved to a *.txt-document.
To these e have to add a bit in order to be able to download the xml-files.
The links look like this:

´´´

href="text?doc=Perseus%3atext%3a2006.05.0009"


´´´

The url to the xml-files look almost the same, we just have to replace the first part (href="text?) with the beginning of the xml-file-url:


´´´
www.perseus.tufts.edu/hopper/dltext?

´´´

After removing the quotation marks at the end of the links, the list is ready for the webscraping.
For this, we need wget. I typed in the following commands to my powershell:

´´´
wget -i links.txt -P -nc ./subfolder/

´´´

-P is a folder parameter; it instructs wget where to save the files to.
-nc is a parameter that instructs wget to skip files that are already there;

There are quite a few other parameters one can use, but I didn't need to explore any other options in order to make this work.