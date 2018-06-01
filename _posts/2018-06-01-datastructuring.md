---
layout: post
title: Data Structuring
---

The next assignment: reformat the 'dispatch' and generate one tsv-file with the entire content of it;
1) each entry is a single record
2) each record should include: date, type of entry, header, text of the entry;

As we got our teacher's solution from last week and thought the assignment through in class and had already some kind of pseudocode how to tackle the assignment, it felt a bit easier then before.

As far as I've learned so far, there is a gazillion of ways to do something. As it was explained to me, modifying the code we got last time as a solution is one of the easiest ways. So, I took the code our teacher gave us as a solution to our last assignment (my code almost worked, by the way!) and took a more thorough look at it.


'''


	import re, os	

	source = ".//files"
	target = ".//res"

	# create a list to save the extracted information
	theList = []


'''


The first thing I found in my notes was that we should work either with a dictionary or a list in this case. I decided a list was easier for me to handle as I still feel quite unsure about what I'm doing.
As for most of the code, I more or less let it be until I got to the juicy parts: the functions to search, split and replace work and do what they are supposed to. Our teacher even added something that filters the unitType as well as the header out of the text, which is great. 

'''


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


'''


While our teacher let the programme print "no header found", I found that very annoying. It also slowed down the whole process of running my code, so I took it out.
Another thing I took out is the line of code that generated a new file for each entry, as we're collecting everything in a list.
I only made small modifications to the replace-function as well: While our teacher inserted ";;;" instead of the new lines, I decided this was not functional anymore here, since tabs are used to separate the singular elements we should unite 
As our teacher already generated an ID, I just had to delete the "#itemID"-tag he used before. I also decided that it made not much sense to use tags to mark different parts of one entry. These parts (date, unitType, header, text) are already defined in the code and don't need further tagging.


'''

					
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
		
'''

My one problem was to find a solution to saving my list (theList) in a file (tsvfile). I didn't come up with this solution by myself, but got a tip by an IT-guy-friend who laughed at me for not figuring it out myself.

What I learned from this assignment:
- Changes don't have to be major, it's often enough to modify existing code (which is the easier way than to think everything up from scratch.)
- for loops are the best thing every
- the .write function doesn't accept lists, so you have to find a workaround
- usually everything I need is already there, I just need to open my eyes and look (which apparently I don't do enough)
- if I want my code to work for everything, it needs to be on the outmost scope (I don't know if this makes sense to anyone except maybe myself)
- "\t" stands for tabs, while "\n" stands for new lines. Yes, I have to repeat this to me, like, every second day.
