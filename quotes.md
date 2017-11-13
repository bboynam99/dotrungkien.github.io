---
title: Quotes
permalink: /quotes
---
<html><head>
 <title>Excuses For Lazy Coders</title>
 <style type="text/css">* {margin: 0;} html, body {height: 100%;} .wrapper {min-height: 100%; height: auto !important; height: 100%; margin: 0 auto -8em;} .footer, .push {height: 8em;}</style>
</head>
<body>
 <!-- <div class="wrapper">
  <center style="color: #333; padding-top: 200px; font-family: Courier; font-size: 24px; font-weight: bold;"><a href="/" rel="nofollow" style="text-decoration: none; color: #333;">You must be missing some of the dependencies</a></center>
  <div class="push"></div>
 </div> -->
{% assign random = site.time | date: "%s%N" | modulo: 100 %}
{{ site.data.quotes.quotes[random].text }}
{{ site.time  }}
 <!-- <ul>
{% for quote in site.data.quotes.quotes %}
  <li>
      {{ quote.text }}
      <br>
  </li>
{% endfor %}
</ul> -->
</body></html>