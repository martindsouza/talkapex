---
title: Listagg for IR Column Break Report
tags:
  - apex
date: 2017-10-27 06:01:52
alias:
---


If you've used Interactive Reports (IR) in APEX for a while you may find a need to display a delimited list of values next to a `sum` row. The following screenshot shows this problem. Suppose you wanted to have a comma delimited list of names for each job such as `FORD,SCOTT` in the aggregation (sum) row.

{% asset_img report-problem.png %}

In SQL this is easy to do using the [`listagg`](https://docs.oracle.com/database/122/SQLRF/LISTAGG.htm#SQLRF30030) function. unfortunately `listagg` isn't available as an IR aggregation option (`Actions > Data > Aggregate`). I recently needed to do this for an IR. The rest of this post will cover setup along with the JavaScript (JS) solution.

## Setup

1. Create an Interactive Report: `select ename, sal from emp`
2. On the IR go to `Actions > Format > Control Break` and Select `Job` as the column (shown below) </br> {% asset_img control-break.png %}</br>
3. On the IR go to `Actions > Data > Aggregate` and select `Function: Sum` and `Column: Sal` as shown below. </br>{% asset_img aggregation-sum.png %}

The resulting report should look like the first screenshot at the begging of this article.

## Solution

The JavaScript below will add in the necessary `listagg` for the `Ename` column for the IR.

```js
//Set to the displayed header name
var headerName = 'Ename';

var headerNum; //ID for TH header object

// Find the header to listagg
$('a.a-IRR-headerLink').each(function(){
  var $this = $(this)
  if($this.text() == headerName){
    headerNum = $this.closest('th.a-IRR-header').attr('id');
  }
});

$('.a-IRR-aggregate-value').each(function(){
  var $aggRowTr = $(this).first().closest('tr');
  var $prevRow = null;
  var listaggHtml = '';

  // Loop over all the rows until next next column break
  while (true){
    $prevRow = !$prevRow ? $aggRowTr.prev('tr') : $prevRow.prev('tr');

    if ($prevRow.children('th').length > 0){
      // If TH is detected it's the end of the IR column break
      break;
    }
    else{
      listaggHtml = $prevRow.children(`td[headers*="${headerNum}"]`).text() + ',' + listaggHtml;
    }
  }//for

  // Regex is to remove trailing comma
  $aggRowTr.children(`td[headers*="${headerNum}"]`).html(listaggHtml.replace(/,$/gi, ''));

}); // ('.a-IRR-aggregate-value').each
```

After running the JS code the report will look like:

{% asset_img report-solution.png %}

A few comments about this solution:
- This code was for a personal report and thus the JS hasn't been optimized or properly tested on all browsers
- Depending on your needs you may want to move it to a Dynamic Action
- If you have time you may want to convert this to an APEX plugin. If so please share on [APEX.world plugins](https://apex.world/ords/f?p=100:700)
