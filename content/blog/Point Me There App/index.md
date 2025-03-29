+++
date = 2022-08-10
title = "Compass App with Variable Heading"
description = "Drop a pin or select a location to choose your heading."
authors = ["Alyn Musslelman"]
[taxonomies]
tags = ["Java", "Android SDK", "Google Maps API", "Google Places API"]
[extra]
math = true
image = "compass.png"
+++

## Motivation

When I was working for doordash I would always run into this problem where I was too close to the delivery address for the map to help. Despite being able to see details like house numbers on google maps, it can be really dificult to physically reach those destinations when you've lost your bearing in a dense area. This app was designed to solve that problem. I was originally going to add a physical "hand" that a user could place to the dash of their car, link to the app, and have it point in the direction you needed to drive. I've yet to get to that part though.

## Methodology

The first thing I needed to do to start this project was choose my development stack. Since I was using an Android phone, I decided to use Android Studio with Java for oop and the layouts within the app were written in xml. To add the maps feature I used Google Maps API, and to get a search bar that returned coordinates I used the Google Places API. To get the real time precise location of the user I also used the Google Maps API.

The techincal aspects of this project were intents, managing security within the APIs, generating a heading from a pair of coordinates and implementing the rotating graphic that gave the heading to the user.

Intents are a very complicated presentation for one of the most basic mobile programming features. They are simply the feature that allows a user to go from one page within an app to another. For example, if you have a news feed app and a user is scrolling through articles, the feature that takes the user from the list of articles to the one they tapped on is called the intent. In my case, I needed to create an intent that took a user from the maps page to the compass page once a valid pin/address were loaded onto the map. 

To generate the heading, I got to apply some fun math.

To present the changing heading to the user I just forked a piece of code from a tutorial and swapped the image to this one of Mickey Mouse's hand:

## Results

