---
title: Custom APEX Notification Messages
tags:
  - apex-5.1
date: 2018-03-29 08:00:19
alias:
---


Ever wonder how APEX generates error messages (shown below)? Want to create your own? Starting in APEX 5.1 you can create your own error and success messages using the [`apex.message`](https://docs.oracle.com/database/apex-5.1/AEAPI/apex-message-namespace.htm#AEAPI-GUID-D15040D1-6B1A-4267-8DF7-B645ED1FDA46) library.

{% asset_img apex-error-message.png %}

The following demos show how to create custom error and success messages in JavaScript.

## Error Messages

```js
apex.message.clearErrors();

apex.message.showErrors([
  {
    type: apex.message.TYPE.ERROR,
    location: ["page", "inline"],
    pageItem: "P1_DEMO",
    message: "This field is required",
    unsafe: false
  },
  {
    type: apex.message.TYPE.ERROR,
    location: ["page"],
    message: "Page level error",
    unsafe: false
  }
]);
```
</br>

{% asset_img apex-custom-error-message.png %}

## Success Message

```js
apex.message.clearErrors();

apex.message.showPageSuccess( "It works!" );
```

</br>

{% asset_img apex-custom-success-message.png %}

## Wrap up
For more examples and full documentation read the [`apex.message`](https://docs.oracle.com/database/apex-5.1/AEAPI/apex-message-namespace.htm#AEAPI-GUID-D15040D1-6B1A-4267-8DF7-B645ED1FDA46) documentation.

The {% post_link how-to-save-page-data-but-show-errors-in-apex next post %} covers how to leverage the `apex_message` API to simulate an error in APEX without an error happening (it will also cover why you'd want to do this).

