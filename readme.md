#Discourses of Theft

What are the discourses of theft on Twitter? Broadly, I am interested in the looting of antiquities, and how that plays out across twitter. On the other hand, I did not want to focus my search so narrowly that I only captured what 'professional' players were saying (though that in itself could be an interesting/valuable question to explore).

1. Collect data with Twarc: ```twarc.py --search looting OR looted OR looters OR illicit OR theft OR unexcavated OR nighthawking OR antiquities OR ransack OR stolen -"grand%20-theft" > loot.json```

I had to add the 'no grand-theft' flag, otherwise I would've also captured the entire 'grand theft auto' twittersphere as well. The resulting json file is 885 mb.
