#Exploring Discourses of Theft

_this is an exploration of a dataset for my [HIST3907b class at Carleton University](https://github.com/hist3907b-winter2015/)_

What are the discourses of theft on Twitter? Broadly, I am interested in the looting of antiquities, and how that plays out across twitter. On the other hand, I did not want to focus my search so narrowly that I only captured what 'professional' players were saying (though that in itself could be an interesting/valuable question to explore).

I therefore framed my data as broadly as seemed prudent.
## Getting the data

1.  I first constructed my search terms on the Twitter advanced search page, and took note of how the search terms were framed in the URL. I can never remember ascii codes for spaces and so on, so this sorts it out for me when I pass it to the command line. I then used [Twarc](https://github.com/edsu/twarc): ```twarc.py --search looting OR looted OR looters OR illicit OR theft OR unexcavated OR nighthawking OR antiquities OR ransack OR stolen -"grand%20-theft" > loot.json``` . I had to add the 'no grand-theft' flag, otherwise I would've also captured the entire 'grand theft auto' twittersphere as well. The resulting json file is 885 mb. (What you _actually_ type: ``` twarc.py --search looting%20OR%20looted%20OR%20looters%20OR%20illicit%20OR%20theft%20OR%20unexcavated%20OR%20nighthawking%20OR%20antiquities%20OR%20ransack%20OR%20stolen%20-"grand%20-theft" > loot.json ``` )
2. Converted loot.json to csv using the json2csv.py function in the twarc/utilities folder.
3. Extracted geolocated tweets using the geojson.py function in the twarc/utilities folder. Uploaded these to [gist](https://gist.github.com/shawngraham/e7ce4758a314ebdf0176) to visualize that aspect of the materials.
4. Extracted twitter ids using the ids.py function in the twarc/utilities folder. Since the twitter terms of service don't allow me to share the full tweets, one can use the ```twarc.py --hydrate``` on the list of ids to restore the full dataset. My twitter ids list is [here](/lootids.txt).

I used R to do some exploratory visualizations of patterns of word use in these tweets. I used my [standard topic modeling script in R](http://hist3907b-winter2015.github.io/module4-holes/tm-CND.html), but set to read a modified version of the csv:

```documents <- read.csv("loot-tm.csv", col.names=c("id", "text"),
                      colClasses=rep("character", 2), sep=",", quote="") ```
                
Interestingly, it seems that the twitter search through the API searches in all languages. Applying an english stopword list is not a complete solution then.

## Preliminary Observations
|words	 |term.freq	 |doc.freq | 
|--------|:---------:|--------:|
|stolen  |127562	 |125995   |
|find	 |6594		 |6583     |
|gold	 |6268		 |6130     |
|banks	 |6220		 |5872     |


I added 'stolen' to my stopword list. It simply was overpowering any other signals in this noise. I did some more data munging from a text analysis point of view using a modification of [my standard script](https://github.com/hist3907b-winter2015/module4-holes/blob/master/text-analysis-cnd.R).

Eventually, I end up with a document-term matrix (which RStudio tells me is 47.4 mb in size):

```R
#check size
dim(myDtm)```

[1] 288510 134278 

I remove sparse terms:

```R
dtms <- removeSparseTerms(myDtm, 0.9999)
dim(dtms)
```

[1] 288510   5185

...which is more manageable, and I begin to look for some correlations in the terms:

```R
findAssocs(dtms, "antiqu", corlimit=0.30)

               antiqu
fund             0.46
gather           0.39
collect          0.37
donat            0.36
evid             0.35
museum           0.35
bbc              0.33
isi              0.32
philanthropist   0.30
```

Evidently, when we speak of 'antiques', 'antiquities', etc on Twitter, we are doing so in a conversation about gathering, collecting, donations: a very museum-centred world. But then we have 'isi' which is likely ISIS. 

```R
findAssocs(dtms, "isi", corlimit=0.33)         
              isi
dbfmake       0.44
millions۪      0.44
alarabiyaeng  0.36
artefact      0.36
sell          0.36
```

What about that WSJ article that was making the rounds?

```R
findAssocs(dtms, "wsj", corlimit=0.33)            
               wsj
dbfmonument    0.61
men۪            0.61
critk          0.60
syria۪          0.59
protect        0.48
race           0.46
pdb            0.45
inclus         0.39
meet           0.39
secondlargest  0.39
rspwbfhdb      0.37
xpheidjnen     0.37
rebuild        0.36
```
We could do this hit-and-miss all night. Let's look at the most frequent terms now:

![image](/freq-words)

Something happening with a missing gold medal, in australia (?), thefts of bitcoins, thefts of cars and other vehicls, banks are in bad odour, thefts of .... girls? Perhaps that's a reference to the kidnappings in Africa by Boko Haram, which have largely slipped from the (Western) headlines. One could work through these keywords to see what other associations are present.

## Topic Model
I fitted a topic model with 150 topics to this information. My standard script writes the output to csv files for further manipulation and so on (while I can do some things in R, sometimes it's just quicker all around to go back to a spreadsheet. The 'right' tool in a situation is the one you know how to use, yes?). 

+[topic-lables, word-freqs, topics-docs (large zip)](/archive.zip)


If I had included user_names in the data that I had imported into R, I could probably have generated a graph of individuals whose tweets tend to fall into broadly similar topics. I have yet to do this. Instead, for this initial pass on my data, I was interested to see the interrelationships between the topics themselves. 

![image](/RAW-output.png)

In this image, I converted a clustergram into a json object along the lines of what [Rolf Fredheim suggests](http://quantifyingmemory.blogspot.co.uk/2013/11/d3-without-javascript.html). I used his script, which generated the json object for me, but I could not persuade it to play nice with the d3 hierarchical edge bundling layouts of Mike Bostock. So I used [RAW](http://app.raw.densitydesign.org/) instead, but I had to convert the json back to csv to make that work. The result you see above.

Alternatively, I used this snippet of code:

`install.packages('ggdendro')
`library(ggdendro)
`ggdendrogram(hc, rotate = TRUE, size = 8, theme_dendro = FALSE, color = "tomato")

to make this image:

![image](/topics-sideways.png)

Broadly then, there's a lot of structure. An obvious question is how much does this depend on my original search terms? I'm not sure how to approach that question at the moment, so let's leave it aside with a promise to come back to it. 
