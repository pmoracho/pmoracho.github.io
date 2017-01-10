---
layout: page
title: Buscar
description: "Buscar"
permalink: /search
category: base
sitemap: false
noindex: true
nofollow: true
category: base
---

<!-- Search box form -->
<form onsubmit="return executeQuery();" id="cse-search-box-form-id">
  <!-- This is the input searc box -->
  <input type="text" id="cse-search-input-box-id" size="25" autocomplete="off"/>
  <!-- This is the search button -->
  <input type="submit" value="Search"/>
</form>
<!-- End of search box form -->

<script type="text/javascript"
        src="//www.google.com/cse/brand?form=cse-search-box-form-id&inputbox=cse-search-input-box-id">
</script>
<!-- End of Google branding watermark -->

<!-- Element code snippet -->
<script type="text/javascript">
  function executeQuery() {
    var input = document.getElementById('cse-search-input-box-id');
    var element = google.search.cse.element.getElement('searchresults-only0');
    if (input.value == '') {
      element.clearAllResults();
    } else {
      element.execute(input.value);
    }
    return false;
  }
</script>
