---
title: APEX 5.1 Validations and Session State
tags:
  - apex
  - apex-5.1
date: 2017-04-30 04:24:06
alias:
---


Starting in APEX 5.1, pages are submitted via AJAX by default instead of the traditional post and refresh. I recorded a short video for [Insum](www.insum.ca) a while back on this and suggest that you [watch it](https://www.youtube.com/watch?v=yF58mPBLxX0) before continuning.

Last week [Vincent Morneau](https://twitter.com/vincentmorneau) and I had a discussion that lead to the following question: What happens when an `After Submit` computation or process changes a page item's value in session state and a validation fails? The short answer is that the value in session state is modified but not reflected on the page afterwards. I created the following test case to demonstrate this behavior:

On Page 2 (P2) I have an text item called `P2_NAME`. I then have an `After Submit` computation that always sets the value to `Set from Computation` and a validation that always fails as shown below.

<img src="{% asset_path page-processing.png %}">

If I enter `martin` as a value in `P2_NAME` and submit the page I'll get an error message, yet the session state value is now `Set from Computation`. The following animation shows this.

<img src="{% asset_path ajax-page-submission.gif %}">


## What does this mean?

Before analyzing this behavior it's important to understand when commits occur in APEX. After each computation or process an implicit `commit` may occur. [Dan McGhan](https://twitter.com/dmcghan) wrote an excellent [blog post](http://www.danielmcghan.us/2012/08/implicit-commits-in-apex.html) about this several years ago that I highly suggest reading it.

I can't speak for the APEX development team but it appears they had a few options when introducing the new AJAX submission functionality: 

1. Change the behavior of when commits occur to not commit until afer all validations pass
1. Upon a failed validation modify the page with the updated session state values
1. Do neither

The first two options may actually introduce more problems and complexity and also ruin backwards compatibility with older applications. I think the third option is the best one as most applications don't do computations or process after submitting but before validations. If developers pass in current page item values into AJAX Dyanmic Actions or cascading LOVs it will make the new AJAX page submission a non issue.

### Why might this be a problem?

Modifying the session state and not reflecting the change on the page may cause some undesired UI bugs when dealing with AJAX functions (Dynamic Actions, cascading LOVs, etc) that reference page items. I dont' think there are many situations where this will actually cause issues and most of this can be mitigated by passing in the reference page items when calling an AJAX function.

### What to do about it?

This is one thing that I think develoeprs will just need to be aware of and analyze their code to see if there may be any impacts. The following query will identify pages where computations or processes exist before validations.

```sql
select distinct
  pv.application_id,
  pv.page_id,
  pv.page_name
from apex_application_page_val pv
where 1=1
  and pv.application_id = :app_id
  and exists (
    select 1
    from apex_application_page_proc pp
    where 1=1
      and pp.application_id = pv.application_id
      and pp.page_id = pv.page_id
      and pp.process_point_code = 'ON_SUBMIT_BEFORE_COMPUTATION'
    union
    select 1
    from apex_application_page_comp pc
    where 1=1
      and pc.application_id = pv.application_id
      and pc.page_id = pv.page_id
      and pc.computation_point = 'After Submit'   
  )
```



