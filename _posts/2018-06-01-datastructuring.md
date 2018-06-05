---
layout: post
title: Data Structuring
---

The next assignment: reformat the 'dispatch' and generate one tsv-file with the entire content of it;
1) each entry is a single record
2) each record should include: date, type of entry, header, text of the entry;

As we got our teacher's solution from last week and thought the assignment through in class (pseudocode), I already had an idea how to tackle the assignment. Thus, it felt a bit easier than before.

The more I learn about python and coding in general, the more it becomes clear that there is a gazillion of ways to do something.
As was explained to us in class, modifying the code we got last time in order to find a solution for this week's assignments is one of the easier ways. That makes sense: we don't have to write everything from scratch.
So, I took the code our teacher gave us as a solution to our last assignment (my code almost worked, by the way!) and took a more thorough look at it.


```


	import re, os	

	source = ".//files"
	target = ".//res"

	# create a list to save the extracted information
	theList = []


```


The first thing I found in my notes was that we should work either with a dictionary or a list if we want to have a list as an output. I decided a list was easier for me to handle as I still feel quite unsure about what I'm doing.
As for most of the code, I could more or less let it be. The functions to search, split and replace work; they do what they are supposed to do and don't have to be messed with.
Our teacher even added something that filters the unitType as well as the header out of the text, which is great. Also, these bits of code didn't have to be changed at all.
```

	lof = os.listdir(source)

	for file in lof:
		if file.startswith("dltext"): # fileName test		
			with open(source + "/" + file, "r", encoding="utf8") as newFile:
				text = newFile.read()

				# try to find the date
				date = re.search(r'<date value="([\d-]+)"', text).group(1)

				# splitting the issue into articles/items
				split = re.split("<div3 ", text)

				c = 0 # item counter
				#loop through the split documents
				for s in split[1:]:
					c += 1
					s = "<div3 " + s # a step to restore the integrity of items
					#input(s)

					# try to find a unitType
					try:
						unitType = re.search(r'type="([^\"]+)"', s).group(1)
					except:
						unitType = "noType"
						print(s)

					# try to find a header
					try:
						header = re.search(r'<head>(.*)</head>', s).group(1)
						header = re.sub("<[^<]+>", "", header)
					except:
						header = "NO HEADER"


```

While our teacher let the program print "no header found" if there was no header, I found that very annoying after running the code a few times. It also slowed down the whole process, so I commented it out to see what happened if it wasn't there. After I saw that the code was faster and I didn't take out anything essential with it, I deleted this line of code.
Another thing I deleted out is the line of code that generated a new file for each entry. As we're collecting everything in a list, this line is not needed.
I only made small modifications to the replace-function: While our teacher inserted ";;;" instead of the new lines, I decided to delete this (after testing it of course), because we're using tabs to separate the differents elements within one entry. 
As our teacher already generated an ID, I just had to delete the "#itemID"-tag he had inserted before. I also decided that it made not much sense to use tags to mark different parts of one entry anymore. These parts (date, unitType, header, text) are already defined in the code and don't need further tagging.


```

					
					#replace xml markup with blank spaces
					text = re.sub("<[^<]+>", "", s)
					text = re.sub(" +\n|\n +", "\n", text)
					text = re.sub("\n+", "", text)

					#generate itemID
					itemID = date+"_"+unitType+"_"+str(c)
		
					# creating a text variable
					var = "\t".join([itemID,date,unitType,header,text])+"\n"

					#input(var)
				
					#append var to list
					theList.append(var)

	# saving each line of the list to the new file (as it doesn't accept lists, but only strings)
	with open(target+"/"+"result"+".tsv", "w", encoding="utf8") as tsvFile:
		for line in theList:
			tsvFile.write(line)
		
```

My one problem I encountered was to find a solution to saving my list (theList) in a file (tsvfile). I didn't come up with this solution by myself, but got a hint by an IT-guy-friend who laughed at me for not figuring it out myself. As the open-function doesn't accept lists, I had to find a way to write every line separately into the document.
As it turns out, the write-function accepts the single lines of the list.

What I learned from this assignment:
- it's often enough to modify existing code instead of writing everything from scratch again (which is the easier way than to think everything up from scratch.)
- for loops are the best thing every
- the .write function doesn't accept lists, so you have to find a workaround
- usually everything I need is already there, I just need to open my eyes and look (which apparently I don't do enough)
- if I want my code to work for everything, it needs to be on the outmost scope (I don't know if this makes sense to anyone except myself)
- "\t" stands for tabs, while "\n" stands for new lines. Yes, I have to repeat this to me, like, every second day.
