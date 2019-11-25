---
title: Reset Authorization Cache
tags:
  - apex
  - security
date: 2017-10-29 22:25:48
alias:
---


APEX Authorization Schemes determine if a user is allowed to access APEX components (such as page, item, region, etc). _Note: It's common to confuse them with Authentication Schemes which determine if a user is allowed to login to the the application_.

If an Authorization scheme is applied to many components within an application it has the potential to be run many times. To help minimize the performance impacts an Authorization Scheme may have, they can be cached using the following options:

- Once per session
- Once per page view
- Once per component
- Always (No Caching)

Suppose an Authorization Scheme is configured to `Once per session` and this Authorization Scheme is applied to a region on Page 1. The first time the region is to be displayed, APEX will run the Authorization Scheme to determine if it can run/display the region. The authorization result will then be cached so all other components that reference the same Authorization Scheme will use the cached result.

What happens if something changes with the system that requires the Authorization to be re-evaluated? Thankfully there's an API for that: [`apex_authorization.reset_cache`](https://docs.oracle.com/database/apex-5.1/doc.51/e64915/RESET_CACHE-Procedure.htm#AEAPI29604).

I recently encountered the need to reset the Authorization cache on an application where users had multiple roles but could only "play" one role at a time. Example roles include Admin, Manager, User. Each time a user changed their role I needed to reset all the Authorization schemes as the role they played significantly affected the access they had to the system.
