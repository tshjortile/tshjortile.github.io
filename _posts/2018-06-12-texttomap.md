---
layout: post
title: Text to Map (1 + 2)
---

The next assignment was broken down into two parts, because it took a bit longer to accomplish and included several tasks.
1) Extract geographical data from the Richmond 'Dispatch'.
2) Match the regularized forms of placenames to latitude and longitude from another file (open access downloaded data), so it can be imported to QGIS.
3) Import the data to QGIS.


# *1) Extract geographical data from the Richmond 'Dispatch'*

As we already got a solution for this assignment from our teacher, I still want to share my take on the first part, because a) I still have a hard time completely using dictionaries without having problems, and b) because I spent quite a lot of time writing the code.
It's impractical and there are better solutions, but still: one version in order to solve the problem.

```

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

Now, the solution our teacher gave us of course looks more elegant:



# *2) It's a match?*


# *3) Import data to QGIS*



# *Things I learned doing this assignment (besides the obvious):*
--> it's way more practical to compartmentalize: Break the task down to smaller bits instead of looking at it as one big mess. (functions are great for that)
--> a little bit of human-machine-interaction is always necessary and needed in order to complete the task at hand (a.k.a.: Always look at the outcome file)
--> testing the program every step of the way is very, very helpful (later on these tests can be commented out anyway)