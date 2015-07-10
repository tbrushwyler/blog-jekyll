---
layout: post
title:  "Sticky footer without flexbox"
date:   2015-07-07 11:11:00
---



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
        margin-top: @footer-margin-top;
        border-top: @footer-margin-top solid transparent;
    }
}
{% endhighlight %}