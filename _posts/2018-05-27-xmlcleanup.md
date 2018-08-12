---
layout: post
title: Splitting, Cleaning, Polishing
---

Our homework assignment for May 29th was:
1) Split the files we webscraped (around 1700 issues of a newspaper)
2) Save the articles as separate files.
3) Clear them from xml markup

As we got pieces of python code for the assignment, the most complicated part for me was to bring everything together that I have learned so far from the python tutorials.

My thought process, roughly:

1)
I have to use regular expressions, I should import them.

2)
Also, I need to set the path where I want to take the files from ("files") and where I want them to go once I'm finished with them (directory "res" for results).
In order to specify a path, I needed the import os function so I could set it.

3)
In order to get the files, I needed the os.listdir function. I printed the files there after, to test the function, but deleted it to declutter my code.


```python

import re
import os

#define relative path
input_path = os.path.relpath(".\\files")
output_path = os.path.relpath(".\\res")

input_filelist = os.listdir(".\\files")
output_filelist = os.listdir(".\\res")

```

4)
Then I looked at the xml-Files and looked for the xml-tag of the articles. The pattern is <div3 type="article" n="4" org="uniform" sample="complete"> at the start of every article. The end of an article is marked with </dir3>.
My regular expression for it looks like this:

```python

	div3[^<]+article[^<]+>

```

I put it in my code as note to self, since it was one of the first things I tried to figure out here. (I usually try to do something I know I can do. This was it here.)*

5)
We got a function to open and save documents. I implemented it and had to figure out where to put the variables.
Since I work with two different subfolders, I had to add the relative path to each to tell the programme where it should put my new file.

#6)
First, I looked at how to change the file's naming. Luckily, we got a code for this as well!

```python
for fileName in input_filelist:
	with open(".\\files\\" + fileName, 'r', encoding="utf8") as file_in:
		data = file_in.read()

	#save each article with numerus currens
	n = 0
	for dataNew in results:
		newfile = fileName + "_" + str(n) + ".xml"
		#numerus currens
		n = n + 1

	with open(".\\res\\" + newfile, 'w', encoding="utf8") as file_out:
		file_out.write(text)
```

My thought: Since I have to name quite a lot of articles, the proposition for the naming of the new file ("_modified.xml"), would not be enough.
I decided that a numerus currens was better. (It took me a while to figure out that I had to set the "n" as "str(n)" so it was put out as a string. (I got a bit of help here!)

#7)
I still had to split the files, though. We got another chunk of code for that, thanks to our teacher.


```python
	for fileName in input_filelist:
		with open(".\\files\\" + fileName, 'r', encoding="utf8") as file_in:
			data = file_in.read()

		#split files in articles
		results = re.split("<div3[^<]+article[^<]+>", data)

		#save each article with numerus currens
		n = 0
		for dataNew in results:
			newfile = fileName + "_" + str(n) + ".xml"
			#numerus currens
			n = n + 1
	
	with open(".\\res\\" + newfile, 'w', encoding="utf8") as file_out:
		file_out.write(text)

```

#8)
I ran the code again, checked the "res" folder, and there they were: All the articles, neatly split.
But I still had to clean them from the xml markup. I did this by inserting the piece of code we got from our teacher into the loop.

```python
	for fileName in input_filelist:
		with open(".\\files\\" + fileName, 'r', encoding="utf8") as file_in:
			data = file_in.read()
	
		#split files in articles
		results = re.split("<div3[^<]+article[^<]+>", data)
	
		#save each article with numerus currens
		n = 0
		for dataNew in results:
			newfile = fileName + "_" + str(n) + ".xml"
			#numerus currens
			n = n + 1
			#replace xml markup
			text = re.sub("<[^<]+>", "", dataNew)
		
		with open(".\\res\\" + newfile, 'w', encoding="utf8") as file_out:
			file_out.write(text)

```

And then, it worked!

Things I learned from doing this assignment:
--> test your code at every step of the way
--> visualize what you want to do: draw the tree structure so you don't get lost in different levels of code
--> help is great! I asked an IT guy for his opinion once I was done with my first attemt at coding this; he gave me a lot of useful advice
--> the internet is your friend, once you know how to phrase your questions: I had to try a few attempts until I got where I wanted to get.

And this is how the whole code looks:


```python
import re
import os

#define relative path
input_path = os.path.relpath(".\\files")
output_path = os.path.relpath(".\\res")

input_filelist = os.listdir(".\\files")
output_filelist = os.listdir(".\\res")

#note to self: identify pattern(s) for articles
#Pattern: <div3 type="article" n="4" org="uniform" sample="complete"> at the start of every article
#regEx: <div3[^<]+article[^<]+>

for fileName in input_filelist:
	with open(".\\files\\" + fileName, 'r', encoding="utf8") as file_in:
		data = file_in.read()

	#split files in articles
	results = re.split("<div3[^<]+article[^<]+>", data)

	#save each article with numerus currens
	n = 0
	for dataNew in results:
		newfile = fileName + "_" + str(n) + ".xml"
		#numerus currens
		n = n + 1
		#replace xml markup
		text = re.sub("<[^<]+>", "", dataNew)
	
	with open(".\\res\\" + newfile, 'w', encoding="utf8") as file_out:
		file_out.write(text)

```