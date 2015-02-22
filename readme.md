#Discourses of Theft

What are the discourses of theft on Twitter? Broadly, I am interested in the looting of antiquities, and how that plays out across twitter. On the other hand, I did not want to focus my search so narrowly that I only captured what 'professional' players were saying (though that in itself could be an interesting/valuable question to explore).

## Getting the data

1. Collect data with [Twarc](https://github.com/edsu/twarc): ```twarc.py --search looting OR looted OR looters OR illicit OR theft OR unexcavated OR nighthawking OR antiquities OR ransack OR stolen -"grand%20-theft" > loot.json``` . I had to add the 'no grand-theft' flag, otherwise I would've also captured the entire 'grand theft auto' twittersphere as well. The resulting json file is 885 mb.
2. Converted loot.json to csv using the json2csv.py function in the twarc/utilities folder.
3. Extracted geolocated tweets using the geojson.py function in the twarc/utilities folder. Uploaded these to [gist](https://gist.github.com/shawngraham/e7ce4758a314ebdf0176) to visualize that aspect of the materials.
4. Extracted twitter ids using the ids.py function in the twarc/utilities folder. Since the twitter terms of service don't allow me to share the full tweets, one can use the ```twarc.py --hydrate``` on the list of ids to restore the full dataset. My twitter ids list is [here](/lootids.txt).

I used R to do some exploratory visualizations of patterns of word use in these tweets. I used my [standard topic modeling script in R](http://hist3907b-winter2015.github.io/module4-holes/tm-CND.html), but set to read a modified version of the csv:

```documents <- read.csv("loot-tm.csv", col.names=c("id", "text"),
                      colClasses=rep("character", 2), sep=",", quote="") ```
                
Interestingly, it seems that the twitter search through the API searches in all languages. Applying an english stopword list is not a complete solution then.

## Preliminary Observations
words	|term.freq	|doc.freq
stolen	|127562		|125995
find	|6594		|6583
gold	|6268		|6130
banks	|6220		|5872

I added 'stolen' to my stopword list. It simply was overpowering any other signals in this noise. I did some more data munging from a text analysis point of view using a modification of [my standard script](https://github.com/hist3907b-winter2015/module4-holes/blob/master/text-analysis-cnd.R).


