---
layout: post
title: This Week In Learning (Review) 6/18
---

### Brief Overview of Topics I Covered This Week
* More C# fundamentals: structs, links, more about uses of interfaces
* Sass: Sass variables, mixins, built-in functions, CLI tools for Sass compilation
* JavaScript Fetch API and Promises



After finishing most of the C# Player's Guide I decided it would be appropriate
to try some projects and learn a little more about what C# can do. I've also  
been working on a stock-view project that was just meant to be dashboard so
whenever I learn more about ASP.NET Core 2.x I could have some sample HTML and
CSS. However, I found out a little bit more about the Fetch API in JavaScript
which I'll cover.

## ASP.NET: Core 2.x? MVC5?
When I first began to look into what ASP.NET is I found all this lingo about
ASP.NET Core 2.x, ASP.NET Core 1.x, ASP.NET MVC 5, etc... Turns out, Microsoft
is making an initiative to be more inclusive and cross-platform. ASP.NET Core
are versions of ASP.NET that are easy to make cross-platform. I'm
assuming it is easier as while I was watching [ASP.NET Core for Beginners](https://www.youtube.com/watch?v=--lYHxrsLsc)
by Jeffrey T. Fritz, I found that his first two guest were running on macOS
using the 'dotnet' commands and Visual Studio Code to create their web applications.


But, in all honestly, after reading through the MSDN docs, watching the ASP.NET
Core videos on Microsoft Virtual Academy, and 2 hours of the Jeffrey T. Fritz
video I felt I was missing some foundational knowledge. However, I'm not totally
at a loss as I gained some valuable knowledge on MVC. MVC being the design pattern
behind most modern web-applications. This design pattern involves having a
data model (Model), a way to view model data and send user actions (View), and
a way to send model data to the view and interpret user action (Controller). I've
used this design pattern before when attempting to learn other technologies but
I felt like this is the first time the information has stuck. I'll get to why I
think that is.

## The Power of Anki

Anki is a software used for spaced repetition. Before Anki, I would write down
notes and hope I'd remember the stuff when I needed it. I also used to believe
that rote memorization had no place in actual learning. I definitely don't believe that is the case anymore.
Like any tool, rote memorization
is useful in certain contexts.

When I first started reading through C# Player's
Guide, Anki was incredibly useful. What is the CLR? What is the FCL? A good base
knowledge about the language has helped me better understand C# better than when
I went about learning JavaScript and Python. For me personally I love to know
about the technology I'm using instead of just knowing how to use them. When I
delve deeper into JavaScript topics I will be sure to use Anki.

## Front-end Web Development Topics

This week I wanted to build a little project with just HTML/CSS to learn more
about Sass. This project was supposed to just be a dashboard with just test data.
But, I stumbled across the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API).
I have to say, after reading the docs on it and using it to call an API
for stock data, it was incredibly easy to use. Here's a snippet of how I used it:
``` js
fetch(EXAMPLEURL)
  .then( function(response) {
    // returns a promise for chaining
    return response.json();
  })
  .then(function(json) {
    let stockData = json[TIMESERIES];

    let stockName = json["Meta Data"]["2. Symbol"];
    // simple stock object for test data
    // shouldn't be accessible to the outside
    // we just want to populate HTML w/ data
    let MsftStock = {
      stockData: stockData,
      stockName: stockName,
      currentDateString: formatDate(getCurrentWeekday()),
      previousDateString: formatDate(getPreviousWeekday()),
    };

    populateStockData(MsftStock);

    createStockList(MsftStock);

  })
  .catch(function(error) {
    console.log(error);
  })
```

As you can see, fairly simple. A high-level overview is that fetch returns a
Promise object. We then can call a method to run once the Promise is in it resolved
state (.then()). If this Promise doesn't resolve, we catch the error and log it.
The fetch command made getting API data so ridiculously easy that I dread to review
the Twitch API project I did for FreeCodeCamp using jQuery's AJAX.
[Which you can laugh at me about here](https://github.com/crandonriordan/twitch-API-FCC/blob/master/listener.js).

## Next Week's Plan


### C#
I'm going to get Mosh's Intermediate C# Course and move on to his Advanced Course.
After that I will be going through his ASP.NET MVC5 course. There is no good video
course on ASP.NET Core 2.x that I can find. The way I'm understanding it is that
MVC5 is more of a pain than the easy to use Razor pages of ASP.NET Core 2.x, but
the knowledge of MVC5 is transferable too ASP.NET Core 2.x.
### Front-end
I'll most likely work on the Simon Game project from FreeCodeCamp. I've found
that I need to continuously use CSS/Sass or else my mind forget about the little
styling and position nuisances. Maybe I should use Anki for that? :)
