---
layout: page
permalink: /quotes
---

<html><head>
  <title>Excuses For Lazy Coders</title>
  <style type="text/css">* {margin: 0;} html, body {height: 100%;} .wrapper {min-height: 100%; height: auto !important; height: 100%; margin: 0 auto -8em;} .footer, .push {height: 8em;}</style>
</head>
<body>
{% assign random = site.time | date: "%s" | modulo: 100 %}
<div class="wrapper">
  <center style="color: #333; padding-top: 200px; font-family: Courier; font-size: 24px; font-weight: bold;">
  {{ site.data.quotes.quotes[random].text }}
  </center>
</div>
</body></html>
