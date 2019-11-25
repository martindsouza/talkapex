---
title: One Thing You Need To Do Before Calling apex_web_service.make_rest_request
tags:
  - apex
  - plsql
date: 2018-05-15 22:36:15
alias:
---


Can you spot the bug in the following code? (_Hint: it compiles and no run-time errors_)

```sql
-- ...
l_json := apex_web_service.make_rest_request(
  p_url => 'http://someurl.com/data',
  p_http_method => 'GET'
);
-- ...
```

It's not so obvious until I expose the code that preceded it. So let's try this again (with more information). Can you spot the bug in the following code?

```sql
-- ...
apex_web_service.g_request_headers(1).name := 'Authorization';
apex_web_service.g_request_headers(1).value := 'Bearer token123';
l_json := apex_web_service.make_rest_request(
  p_url => 'http://a-differnt-url.com/data',
  p_http_method => 'GET'
);

l_json := apex_web_service.make_rest_request(
  p_url => 'http://someurl.com/data',
  p_http_method => 'GET'
);
-- ...
```

The issue with the second call to `apex_web_service.make_rest_request` is that it will use the request headers that were set for the first call (to `a-differnt-url.com`). If the second web service (`someurl.com`) doesn't want/need/expect that header it can cause some unintended issues and may result in an invalid RESTful call.

To get around this simply clear the request headers each time before setting them / calling `apex_web_service.make_rest_request`. You can do this by calling `apex_web_service.g_request_headers.delete();` The following code is the correct way to run the previous example:

```sql
-- ...
apex_web_service.g_request_headers.delete();
apex_web_service.g_request_headers(1).name := 'Authorization';
apex_web_service.g_request_headers(1).value := 'Bearer token123';
l_json := apex_web_service.make_rest_request(
  p_url => 'http://a-differnt-url.com/data',
  p_http_method => 'GET'
);

apex_web_service.g_request_headers.delete();
l_json := apex_web_service.make_rest_request(
  p_url => 'http://someurl.com/data',
  p_http_method => 'GET'
);
-- ...
```
