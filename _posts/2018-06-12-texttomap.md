---
layout: post
title: Text to Map (1 + 2)
---

Or: How I learned not to despair in front of programs that don't work.

The next assignment was broken down into two parts, because it took a bit longer to accomplish and included several tasks.
1) Extract geographical data from the Richmond 'Dispatch'.
2) Match the regularized forms of placenames to latitude and longitude from another file (open access downloaded data), so it can be imported to QGIS.
3) Import the data to QGIS.


# *1) Extract geographical data from the Richmond 'Dispatch'*

As we already got a solution for this assignment from our teacher, I still want to share my take on the first part, because a) I still have a hard time completely using dictionaries without having problems, and b) because I spent quite a lot of time writing the code.
It's impractical and there are better solutions, but still: one version in order to solve the problem.

```python

import re, os

source = ".//files"
target = ".//res"

source1 = ".//US.txt"

#create a list to save the extracted information
placeList = []

lof = os.listdir(source)
lof.sort()

for file in lof:
	if file.startswith("dltext"): # fileName test
		with open(source + "/" + file, "r", encoding="utf8") as geoFile:
			text = geoFile.read()

			#set time parameter --> later on!
			# date = re.search(r'<date value="([\d-]+)"', text).group(1)
			
			split = re.split('<div3', text)
			for s in split[1:]:
				s = "<div3" + s # a step to restore the integrity of items
				#input(s)
				if 'type="article"' in s:
					places = re.findall(r'<placeName[^/]*', s, flags=0) # find complete placename-markups
					for place in places:
						placeID = re.findall(r'key="tgn,([0-9]*)"', place, flags=0) #find tgn ID
						placename = re.findall(r'reg="([^\"]+)"', place, flags=0) #filter placename for regularized form
						# print(places) #testing				
						if len(placeID) == 1 and len(placename) == 1:
							for x in range(len(placeID)):
								var = "\t".join([placename[x], placeID[x]])
								placeList.append(var) #append results to placeList

							# print(placeList)
						
					dicFreq = {} #frequency dictionary
			
					for i in placeList:
						if i in dicFreq:
							dicFreq[i] += 1
						else:
							dicFreq[i]  = 1
						
					resultsTSV = []
					
					for key, value in dicFreq.items():
						if value > 1: # this will exclude items with frequency 1
							newVal = "%09d\t%s" % (value, key)
							resultsTSV.append(newVal)

					resultsTSV = sorted(resultsTSV, reverse=True)
					#print(len(resultsTSV)) # will print out the number of items in the list
					resultsToSave = "\n".join(resultsTSV)

					# print(resultsToSave) #testing
						
					with open(target + "/" + "result" + ".tsv", "w", encoding="utf8") as tsvFile:
						for line in resultsToSave:
							tsvFile.write(line)

```

Now, the solution our teacher gave us of course looks more elegant, but caused me a few problems I will address in the commented segment of code below. I made a few alterations to it, mainly because I had to split the document out of which I extracted the geographical data (US.tsv). So instead of one file, I work with a list of files there.
Otherwise, my computer gave me a MemoryError, because it couldn't compile this much data at once. With the list of files, on the other hand, I encountered a different problem: a UnicodeError. I asked 'my personal IT guy' for advice and he told me I would have to work with everything in binary and would have to convert if back to utf-8 encoding.
This, though, is too much for my limited knowledge of programming in python, which is why I couldn't complete this exercise.

```python
import re, os

source = ".//files"
target = ".//res"

placeDic = {} # create place dictionary

def updateDic(dic, key): #define function to update dictionary
	if key in dic:
		dic[key] += 1
	else:
		dic[key] = 1

#step 1: collect toponyms from the 'Dispatch
def collectTaggedToponyms(xmlText, dic): # function to collect tagged toponyms
	xmlText = re.sub("\s+", " ", xmlText) # replaces white space with with space (security measure)
	date = re.search(r'<date value="([\d-]+)"', xmlText).group(1) # searches for date
	# count1, count2 = 0,0 # counters as control measurements, not necessary in the final code
	for t in re.findall(r"<placeName[^<]+</placeName>", xmlText): # information about locations filtered with for loop
		t = t.lower() # since the letters are case sensitive, they are all made lowercase with this --> avoiding mistakes
		if 'tgn,' in t: # tgn is included in every mention of a place that has a regularized form
			if re.search(r'reg="([^"]+)"', t): # condition: if the if statement is true, the regularized toponym is written into the table at the end
				reg = re.search(r'reg="([^"]+)"', t).group(1)
			else:
				# print(t) # test
				reg = 0

			if re.search(r'key="([^"]+)"', t): # filters tgn-key
				key = re.search(r'key="([^"]+)"', t).group(1)
			else:
				# print(t) # test
				key = 0
			
			if key != 0 and reg != 0:
				var = reg + "\t" + key
				updateDic(placeDic, var) # updates dictionary

def collectRawToponyms(source): # function that sets the parameters
	lof = os.listdir(source)
	lof = sorted(lof, reverse=False)

	for file in lof:
		if file.startswith("dltext"):
			with open(source+"/"+file, "r", encoding="utf8") as f1: # open file as new file
				text = f1.read()
				collectTaggedToponyms(text, placeDic)

	freqList = [] # list so save frequencies of toponyms to
	thres = 1 # anything under 1 won't be added to the list - I usually test my code with only 10 files of the dispatch, which is why there's two of them
	# thres = 100
	for k,v in placeDic.items(): # adresses item level of the place-Dictionary
		if v >= thres:
			freqList.append("%09d\t%s" % (v,k))
	print("Number of unique items with freq at least %d: %d" % (thres, len(freqList))) # testing

	freqList = "\n".join(sorted(freqList, reverse = True)) # join extracted information to frequency list, with a new line at the end of each line
	with open(target+"/"+"freqList.tsv", "w", encoding="utf8") as f9: 
		f9.write(freqList) # save list as file

collectRawToponyms(source) #executes function defined above

# step 2: collect geographical data (latitude, longitude) into a second file
# I encountered a bit of a problem with the size of the US.tsv-File we downloaded for the geographical data and had to split it in order to finish this task.
def loadGeoData(geosource): 
	dic = {} #create empty dictionary to save extracted information to
	lof = os.listdir(geosource)
	data = ""
	for file in lof:
		with open(".//US//geodata//" + file, "r", encoding="utf8") as f1: 
			data = data + f1.read() #reads file after file

	data = data.split("\n")
		
	for d in data:
		d1 = d.split("\t") # splits the line at each tab
		if len(d1) == 19: # all entries in the US.tsv-file have 19 columns - so this can be seen as a security measure
			val = "\t".join([[d1]+", "+d1[10], d1[4], d1[5]]) #appends geonameID, countrycode, latitude, and longitude in this order
			test = d1[1].lower() # sets geonameID lowercase

			if test in dic: #if the placename is mentioned in the dictionary, the extracted information from the geodatafile will be appended to that entry.
				dic[test].append(val)
			else:
				dic[test] = [val]
	return(dic)

#geoDataFile = "./US/US.tsv" --> using the US.tsv-file as a whole caused my computer to crash, so I had to split the file
geosource = ".//US//geodata" #set variable for geodata
geoData = loadGeoData(geosource) # executes function defined above


def processResults(file): 
	with open(file, "r", encoding="utf8") as f1: #opens designated file in utf8-encoding
		data = f1.read().split("\n") # splits the data at the end of every line
		data = data.lower() # data that's read from the file is set to lowercase

		newData = [] #empty list

		noResults = "\t".join(["NA", "NA", "NA"]) # variable for the case that there's no match

		for d in data:
			d1 = d.split("\t") #splits line by each tab
			if "," in d[1]: 
				test = d1[1].split(",")[0] #security measure, splits data if there's a comma, like "Richmond, VA", so only the first part of the regularized toponym is used (i.e. "Richmond"
			else:
				test = d1[1]

			if test in geoData: #condition: if there's a match
				for i in geoData[test]: #for item in geoData
					newData.append(d+"\t"+i) # appends matches to the empty list
			else:
				newData.append(d+"\t" + noResults)
				with open(target + "/" + "matchedResults.tsv", "w", encoding="utf8") as f9:
					f9.write("\n".join(newData))

processResults("res//freqList.tsv") # executes function defined above, defining where the results are saved
```

This is a picture of the last error message I got:
![texttomaperror](/assets/img/texttomaperror.png)

# *2) Import data to QGIS*
*.csv or *.tsv-Files can be imported into QGIS simply by installing the QuickMapServices (QMS) plugin.
The steps necessary for this step in QGIS, which I couldn't complete because the code gave me an error I couldn't fix anymore in time:

- select "Layer --> import layer --> import text layer"
- select the resultfile (matchedResults.tsv) and select either "CSV-file" or "Benutzerdefiniert"
- then import the file, and ta-daah: The geographical data appears on the map. (If I had any, that is.)

![importlayer](/assets/img/importlayer.png)


# *Things I learned doing this assignment (besides the obvious):*
--> it's way more practical to compartmentalize: Break the task down to smaller bits instead of looking at it as one big mess. (functions are great for that)
--> a little bit of human-machine-interaction is always necessary and needed in order to complete the task at hand (a.k.a.: Always look at the outcome file)
--> testing the program every step of the way is very, very helpful (later on these tests can be commented out anyway)
--> sometimes one has to split files in order to make the amount of data manageable for limited working memories on netbooks like mine.