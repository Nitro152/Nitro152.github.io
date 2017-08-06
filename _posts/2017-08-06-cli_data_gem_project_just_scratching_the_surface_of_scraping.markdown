---
layout: post
title:  "CLI Data Gem Project: Just scratching the surface of scraping."
date:   2017-08-06 23:44:06 +0000
---

### Scraping

For this project I decided to scrape IMDb's top 250 movie list. I thought this would be good because I could scrape a list and then get further details from a URL for each list item. It is similar to the Scraping lab that we did against a static page for the Learn students page.

One of the selectors I had issues with was the full Release Date for the movie. I did not initially know how to get plain text for data that was outside of a tag.

```<h4 class="inline">Release Date:</h4> 14 October 1994 (USA)```

The solution I found was to find the tag before the text and then do a selection on the next sibling for the text.

```movie_details[:release] ||= index.css("div#titleDetails h4")[3].next_sibling.text.strip```


I had issues with movie duration as well. On the page there are two sections where the duration was shown. At first I wasn’t sure why the css selector was returning two values back, one showing duration in hours/minutes and the other just in minutes. I had to look at the page again to finally realize that the duration was shown twice on the page itself. 

```
[#<Nokogiri::XML::Element:0x1006508 name="time" attributes=[#<Nokogiri::XML::Attr:0x100647c name="itemprop" value="duration">, #<Nokogiri::XML::Attr:0x1006468 name="datetime" value="PT142M">] children=[#<Nokogiri::XML::Text:0x1009e38 "\n                        2h 22min\n                    ">]>, 
#<Nokogiri::XML::Element:0x1009a50 name="time" attributes=[#<Nokogiri::XML::Attr:0x10099ec name="itemprop" value="duration">, #<Nokogiri::XML::Attr:0x10099d8 name="datetime" value="PT142M">] children=[#<Nokogiri::XML::Text:0x1009064 "142 min">]>]
```


I think there are some pages that did not have the duration in minutes which was causing errors, so I opted to stick with selecting the duration in hours/minutes.

```
index.css("time[itemprop='duration']")[0].text.strip
```

I actually just refactored some of the code. Instead of scraping the entire list first. I moved the .take(#) to the index css selector. I had to add this because scraping details for 250 movies was causing my app to time out. So I had to limit the number of movies. 

```
movie_list_array = index.css("tbody.lister-list tr").take(25).collect
```

If I was just going to do just a movie list and then details, I could have just scraped for the movie details on demand when a movie was selected. Please see below on why I implemented it differently.

### Class Structure/Objects
I noticed that some movies had multiple genres and directors, so instead of keeping these values as strings, I thought that it would be good to create objects for these values. This way I could keep track of all genres and directors and list movies associated with each genre/director instance. This allowed me to expand the project a little more. In order to do this though, I needed to pull all movie details first.

In the CLI class, I convert the genre/director string arrays to their object values and added the movie objects to each instance. I did not do this in the Movie class because I wanted to keep each class separate. This way if I needed to refactor or change my code it would be easier.
### CLI
I had a hard time with getting the CLI to flow through options normally. Initially I had my if statements in the while loop for the input. This was causing unexpected behavior, where entering input was not triggering the correct action. I am still not 100% sure what was happening but it seemed like I was getting stuck in the while loops, or the loops were nesting as I called other methods. To resolve this I moved my if statements out of the while loop and made sure I was getting valid input first before going through the if statements.

While troubleshooting, I learned that It’s good to know what the methods you use are doing. 
I was using ```gets.strip.to_i``` to get user input. While struggling with the CLI flow, I learned that 'to_i' already strips leading and trailing spaces for you.

I am using 0 to trigger going back to the main menu. When hitting enter without any input, the CLI was going back to the main menu. I learned that 'to_i' will return 0 when it cannot convert a string to a number.
