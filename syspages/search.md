---
layout: default_minimal
title: "Search"
description: "Search"
permalink: /search/
sitemap: false
noindex: true
nofollow: true
category: base
---

{% if site.google_search %}
<div id="home-search" class="home">
     <script>
         (function() {
             var cx = '006172462016279589704:keh0roaynmg';
             var gcse = document.createElement('script');
             gcse.type = 'text/javascript';
             gcse.async = true;
             gcse.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') +
             '//www.google.com/cse/cse.js?cx=' + cx;
             var s = document.getElementsByTagName('script')[0];
             s.parentNode.insertBefore(gcse, s);
         })();
     </script>
     <gcse:search queryParameterName="searchString"></gcse:search>
 </div>
{% else %}
Google Custom Search key is not set in `_config.yml`
{% endif %}
