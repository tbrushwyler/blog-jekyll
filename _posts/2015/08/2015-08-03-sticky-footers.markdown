---
layout: post
title:  "Sticky footer without flexbox (CSS-Only)"
date:   2015-08-03 15:52:00
author: "Taylor"
permalink: "/sticky-footers-without-flexbox"
---

### The problem

I want my variable-height pages to share a footer that will stick to the bottom of the page. Flexbox is the best way to solve this problem, but [it is **not** compatible with all browsers](http://caniuse.com/#search=flexbox "Flexbox Compatibility").

<div class="text-center gutter-bottom">
<img src="/assets/images/20150707_sticky-footers/sticky-footer.png" alt="Sticky footers, yay!" />
</div>

With a little `display: table;` and `display: table-row;` magic, there is a very simple CSS-only solution.

### The solution

{% highlight html linenos %}
<html>
    <head>
        ...
    </head>
    <body>
        <div id="wrapper">
            <div id="content">
                <!-- header and main content block go here -->
            </div>
            <footer>
                <!-- anything you want! -->
            </footer>
        </div>
    </body>
</html>
{% endhighlight %}

{% highlight scss linenos %}
html,
body {
    height: 100%;
    margin: 0;
}

#wrapper {
    border-collapse: collapse;
    display: table;
    height: 100%;
    width: 100%;

    #content {
        display: table-row;
        height: 100%;
    }

    footer {
        display: table-row;
        margin-top: $footer-margin-top;
        border-top: $footer-margin-top solid transparent;
    }
}
{% endhighlight %}

We used this technique for this site's footer - so call me a liar if it isn't sticky! If you're looking for a widely-compatible CSS-only sticky footer, this is the way to go.