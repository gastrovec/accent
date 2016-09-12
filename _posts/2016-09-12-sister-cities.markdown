---
title: Sister Cities
author: L3viathan
profile_picture: /images/l3viathan.png
about_stub: Recreational linguistics, distributional gastronomics, and applied galettalogy.
author_site: https://github.com/L3viathan
---

This post is on so called "sister cities", also called "twin towns"; where
towns form symbolic partnerships with other towns. I was wondering how
connected towns are through this bond: Is it possible to start at one town, go
to one of its sister cities, then one of _its_ sister cities, and so on, to
eventually reach any other city.

To test this, I used [WikiData](https://www.wikidata.org), which conveniently
has a property type for sister cities
([P190](https://www.wikidata.org/wiki/Property:P190)).  I ignored the API
documentation, but found the [basic entity request URL for my
hometown](https://www.wikidata.org/w/api.php?action=wbgetentities&ids=Q3955&format=json).

In it, I searched for `P190` and found a list of entities, all refering to
partner cities of Weimar. Then I stopped with doing things manually and wrote a
short [Python
script](https://github.com/L3viathan/sister-cities/blob/master/get_graph.py) to
recursively crawl all towns "reachable" from my starting point.

A few hours later, I had a list of about 34k links between towns (in both
directions).  I also saved the names of the cities, so I didn't have to look at
the WikiData IDs. Afterwards, I wrote a [quick&amp;dirty
script](https://github.com/L3viathan/sister-cities/blob/master/interactive.py)
to explore sister cities interactively.

The network seems to be pretty well connected, probably because:

- Horizontally: Big cities are linked to eachother. They also often have a high
  amount of connections, making them link smaller subnetworks.
- Vertically: Links aren't always between exqually sized cities, which ensures
  that also the smallest towns have a connection to the big ones.

Obviously, the graph that I collected is fully connected because of the method
of collection, but since the network is so big I assume it got all linked towns
or at least most of them.

You can look at the code and the data [here](https://github.com/L3viathan/sister-cities/),
I appreciate input or further work.
