+++
authors = ["The Unconcerned Ape"]
title = "OpenGuessr Helper: Inception"
description = "part 01 of the series where I yap about how I am creating an extension to cheat on openguessr: an free alternative to geoguessr"
date = "2025-01-08"
draft = false
tags = [
    "firefox",
    "programming",
    "web",
    "javascript"
]
categories = [
    "web-dev",
    "the-entire-journey-in-words",
    "unedited-copy"
]
series = ["openguessr"]
+++

# openguessr guesser? helper?? I haven't decided on the name yet

# disclaimer

I realize that this isn’t ethical and isn’t in the spirit of sportsmanship. I won’t be using it for more than once. I won’t be maintaining it forever. Don’t cheat on multiplayer games. Cheating makes the game worthless for everyone involved and isn’t fun. Moreover, its not worth it. There isn’t any physical gain by cheating your way through a geoguesser match. I am creating this just for learning purposes.

# reason // why (08/01/2025)

i was playing multiplayer with my friend. ii tried to cheat a bit by trying to us google lens. which felt highly inefficient and impractical. i use google translate to translate the road signs to English. which gave me a sense of the country but I still had >1000km of area to work on. which wasn’t efficient enough. 

# idea inspiration

when he left the hosted room, I went on Reddit to find out how people cheat. i found [this Reddit comment](https://www.reddit.com/r/geoguessr/comments/vo2896/comment/ieau9rw/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) which mentioned that the coordinates can literally be found in the page source. I went to the page source and wouldn’t you know that an iframe with the “PanoramaIframe” ID exists, which shows the Street View on the page. It simply loaded the Street View from Google Maps. 

### example element:

> `<iframe id="PanoramaIframe" referrerpolicy="no-referrer-when-downgrade" frameborder="0" src="[https://www.google.com/maps/embed/v1/streetview?location=-38.512017641085315,175.48522144772303&amp;key=AIzaSyAHt3QJRBDISRaWaqblQl2VwjWiHvjpgIs&amp;fov=90](https://www.google.com/maps/embed/v1/streetview?location=-38.512017641085315,175.48522144772303&amp;key=AIzaSyAHt3QJRBDISRaWaqblQl2VwjWiHvjpgIs&amp;fov=90)" class="svelte-3nuhic" style="filter: none;"></iframe>`
> 

As you can see, it clearly holds the coordinates which can easily give me accurate information about the correct marker.

Now I could inspect the code → get to this URL → paste the coordinates in Google → find the place → find it in the game → choose the place → get ~perfect points. but this isn’t as effective either. 

I, being a programmer(beginner but still), would love to automate my entire life. This solution didn’t feel right to me. Also, if the time limit per round was too less, it would have been a race against myself every single round. It wouldn’t be fun OR sinister. Having either one of them is quintessential or there would be no point in playing the game. 

This led me down the rabbit hole of building an extension. I wanted to create a Firefox extension as that’s the browser I primarily use.

# process

I found this incredible [documentation from Mozilla](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions) based on [a StackOverflow page](https://stackoverflow.com/questions/20409349/what-is-the-easiest-way-to-develop-firefox-extension) which went into deep detail on how to get started with building browser extensions. I with along with the first extension in the tutorial created a bright red 5px border around the openguesser website. However, I could not see the border which gave me some headache. It turns out that this site didn't allow for such major CSS changes. I figured this out because 

1. the border appeared on the Google Home page
2. I added a console.log line which worked

Later, I also observed that the border appeared for a fraction of a second before disappearing, every time I reloaded the site.

### manifest.json

```jsx
{
  "manifest_version": 2,
  "name": "guesser",
  "version": "0.1",
  "description": "Checks for the maps url in openguesser and ...(haven't decided yet)",
  "content_scripts": [
    {
      "matches": [
        "*://*.openguessr.com/*"
      ],
      "js": [
        "guesser.js"
      ]
    }
  ]
}
```

Moving on, I wrote a simple program that made the most logical sense:

### guesser.js

```jsx
let source = document.getElementById("PanoramaIframe");
console.log("url: " + source.src);
```

the console was as follows:

> `url: null`
> 

there was a major problem with it. This was mainly because it was my first time creating a browser extension. These lines were executed when I reloaded the extension on the about:debugging page. This resulted in the extension trying to get an element before the document was even loaded. 

I fixed it by adding a delay. *at least I wished I did*

### guesser.js

```jsx
setTimeout(() => {
    let source = document.getElementById("PanoramaIframe");
    console.log("url: " + source.src);
}, 1000);
```

the console was as follows:

> `url: https://openguesr.com/loader`
> 

yep, that's right. the document isn’t loaded all at once. They are dynamically loaded and that's also true because the URL at the address bar wouldn’t change. all the rounds had the same URL.

I figured that this could be solved by making this code run after the Street View is loaded. This was too complicated for me to implement (it still is) so I went with the easier route. I wanted to do what the [browser-ruler](https://github.com/ianwremmel/browser-ruler) or [measure-it](https://github.com/tsl143/measure-it) extensions do. I can make the code run when the user clicks on the extension icon placed in the toolbar. 

This led me to a [Stack Overflow page](https://stackoverflow.com/questions/28202736/how-to-execute-content-script-after-the-page-is-loaded-completely) which sadly didn’t work due to outdated code. This led me to [search for ways to make a menu appear when I click on the extension icon](https://www.google.com/search?q=menu+in+the+toolbar+firefox+extension) where I discovered ([thanks to MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/user_interface)) that my [grayed-out extension icon in the toolbar](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/user_interface/Toolbar_button) can be allowed to have a [popup](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/user_interface/Popups). 

Going through the extension and a slight modification to my directory structure and manifest.json let me have a menu

### manifest.json

```jsx
{
  "manifest_version": 2,
  "name": "guesser",
  "version": "0.1",
  "description": "Checks for the maps url in openguesser and ...(haven't decided yet)",
  "content_scripts": [
    {
      "matches": [
        "*://*.openguessr.com/*"
      ],
      "js": [
        "guesser.js"
      ]
    }
  ],
  "browser_action": {
    "default_title": "geoo",
    "default_popup": "popup/trial.html"
  }
}
```

### popup/trial.html

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Trial</title>
</head>

<body>
    <!-- idk how it (popup) works at this point -->
    <button type="button" style="background-color: red;"> try </button>
</body>

</html>
```

and voila. there's a bright red button with the value “try” when I click on the extension icon. :yayy :3

now after 

1. replacing `trial.html` with `get_info.html` 
2. making the appropriate changes in *manifest.json* like adding the popup.html path and removing the guesser.js (this key attribute will come in clutch later)
3. making the code a function that can be called by and moving *guesser.js* to *popup/* 

I was ready to get the URL. right? right??

### manifest.json

```jsx
{
  "manifest_version": 2,
  "name": "guesser",
  "version": "0.1",
  "description": "Checks for the maps url in openguesser and ...(haven't decided yet)",
  "content_scripts": [
    {
      "matches": [
        "*://*.openguessr.com/*"
      ]
    }
  ],
  "browser_action": {
    "default_title": "geoo",
    "default_popup": "popup/get_info.html"
  }
}
```

### popup/get_info.html

```jsx
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Get The URL (for now)</title>
</head>

<body>
    <script src="./guesser.js" type="text/javascript"></script>
    <input type="button" value="Get Info" id="get_info" onclick="get_cords();">
</body>

</html>
```

### guesser.js

```jsx
function get_cords() {
    console.log("fn called");
    let element = document.getElementById("PanoramaIframe");
    console.log("Element: " + element);
}
```

nope. if only things were as simple. 

a pair of sharp eyes would’ve noticed that there are two problems here:

1. I can’t use scripts this way (as a matter of fact, even inline scripts) because of the [risk of Cross-site scripting (XSS)](https://stackoverflow.com/q/76166219). 
2. the `document` in *line 3* is directed at the popup file and not the required DOM of the webpage. so getting it to run still meant that my code wouldn’t work.

Based on the [content_security_policy docs](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/content_security_policy), adding the following helped me fix `a.` but I had to remove it as it wasn’t fully nonrestrictive. 

### manifest.json

```jsx
"content_security_policy": "default-src 'self'"
```

the issue is that it allowed for scripts to run but it didn’t allow for inline scripts. The inline scripts would be allowed but then again, it would lead to XSS attacks so its not advisable.

Another [(this was self-answered btw!) StackOverflow question](https://stackoverflow.com/a/63311367) led me to the solution. To access the DOM, I have to use the content_scripts (remember when I implemented the popup files, I removed *guesser.js* from *manifest.json*? I have to create another js file which stays in the content_scripts section and allows for DOM access. neat right?)

you 

# conclusion of part 1

I figured I would write this piece and publish it unedited (even un-re-read) before I get onto the next part.

If you read it so far, Thank you!

If you didn’t read it and just came to the last, we are opps. but then again, at least you opened it, so we are neutral. Do give it a try, it was fun ( 5 1/2 hours of work + 3 * 30 minutes Pomodoro clocks of fun).

The code might be wrong/over-updated as I copied the current code and edited it to the state that it supposedly was, at that incident’s occurrence.  I am not fact-checking it. 

If you find any flaws or want to provide your thoughts, you can comment below. If you are reading this on my blog page, you can add comments as a guest without login by selecting the checkbox option.
