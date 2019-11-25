---
title: Enhanced APEX Session Timeouts
tags:
  - apex
  - security
date: 2009-09-21 09:00:00
alias:
---

APEX has built in logic to set the lifetime of a session. _To configure this option go to Shared Components / Edit Security Attributes / Maximum Session Idle Time in Seconds and set the time in seconds._

This essentially terminates the user's session in the database and the next time they submit the page they'll be redirected to the login screen. **_The user will only know that they are logged out once they submit the page_**. If you have an Interactive Report (IR), or use Partial Page Refresh (PPR) the users won't know they're logged out. Instead it will look as though the report is still trying to load.

[![](http://2.bp.blogspot.com/_33EF80fk9sM/SrZjdKYpklI/AAAAAAAADqs/GPxkwVTc1Ow/s400/apex_session_timeout_ir_ex.PNG)](http://2.bp.blogspot.com/_33EF80fk9sM/SrZjdKYpklI/AAAAAAAADqs/GPxkwVTc1Ow/s1600-h/apex_session_timeout_ir_ex.PNG)
Another situation that may happen is that the user is filling out a long form on your page, their session timesout, then they click "submit". They'll be redirected to the login page and they'll lose all the information that they entered.

What if a user wants to extend their session? i.e. they haven't done anything to the page but would like a warning message before we automatically log them out? Or they are entering a log form and don't want to be logged out? I got this idea from the [Air Canada](http://www.aircanada.com/) web site when I was booking tickets. I really liked the fact that they let me know that they were to end my session, and gave me the option to extend my session.

[![](http://3.bp.blogspot.com/_33EF80fk9sM/SrZjlS8xbPI/AAAAAAAADq0/GlyvVtBhr2Y/s400/ac_session_timeout.PNG)](http://3.bp.blogspot.com/_33EF80fk9sM/SrZjlS8xbPI/AAAAAAAADq0/GlyvVtBhr2Y/s1600-h/ac_session_timeout.PNG)
The following solution will allow you to use APEX's session timeout and resolve the issues listed above. You can view the demo here: [http://apex.oracle.com/pls/otn/f?p=20195:2600](http://apex.oracle.com/pls/otn/f?p=20195:2600)

_Please note that since the demo page is set to public you can refresh after the session is supposed to have timedout and it will still work. If you set the Idle Session in APEX, this will work for pages that require authentication._

[![](http://2.bp.blogspot.com/_33EF80fk9sM/SrZkYgKz7_I/AAAAAAAADrM/U8se-ZIHyIw/s400/apex_session_extend.PNG)](http://2.bp.blogspot.com/_33EF80fk9sM/SrZkYgKz7_I/AAAAAAAADrM/U8se-ZIHyIw/s1600-h/apex_session_extend.PNG)
[![](http://2.bp.blogspot.com/_33EF80fk9sM/SrZj0SLARDI/AAAAAAAADrE/0ywt1wBNUNA/s400/apex_session_ended.PNG)](http://2.bp.blogspot.com/_33EF80fk9sM/SrZj0SLARDI/AAAAAAAADrE/0ywt1wBNUNA/s1600-h/apex_session_ended.PNG)
Here's a high level overview of what this solution does:

*   Start a timer (pingApexSession) that will constantly "ping" (therefore refresh) your APEX session every X seconds.
*   Start a timer (idleTimer) to detect movement on the page.
*   If the idleTimer times out, give the user the option to extend session
*   If the user does not extend their session, terminate their session
_I haven't put this code into a production application yet. As I mention below, I plan to make a jQuery plugin for this, so if you please send me any feedback that would be useful fur the plugin._

This solution uses [jQuery](http://jquery.com) and the following plugins:

*   [Simple Modal](http://www.ericmmartin.com/projects/simplemodal/)
*   [jQuery idleTimer](http://paulirish.com/2009/jquery-idletimer-plugin/)
*   [jApex](http://tylermuth.wordpress.com/2009/08/19/japex-a-jquery-plugin-for-apex/)
*   [jQuery Countdown](http://keith-wood.name/countdown.html)

## Create Application Process: AP_NULL
- Process Point: On Demand
- Name: AP_NULL
- Type: PL/SQL Anonymous Block
- Process Text: NULL;

## Create Application Process: AP_LOGOUT
- Process Point: On Demand
- Name: AP_LOGOUT
- Type: PL/SQL Anonymous Block
- Process Text:

```sql
begin
  apex_custom_auth.logout(
    p_this_app => :app_id,
    p_next_app_page_sess => :app_id || ':1');
end;
```

## Create Region: "Extend Session" on Page 0
- Title: Extend Session
- Type: HTML Text
- Static ID: P0_REG_EXTEND_SESSION
- Region Attributes: style="display:none"
- Region Source: `Your session will timeout in: <span id="timeoutCountdownDisplay" style="font-weight:bold"></span>`
_You can put whatever message you want. Just make sure the span tags exist for the countdown timer._

## Create Button: "Extend Session" on Page 0
- Button Name: EXTEND_SESSION
- Text Label: Extend Session
- Display in Region: Extend Session
- Target is a: URL
- URL Target: javascript:gTimeout.timers.killSession.liveFn();

## Create Region: "Session Timedout" on Page 0
- Title: Session Ended
- Type: HTML Text
- Static ID: `P0_REG_SESSION_ENDED`
- Region Attributes: `style="display:none"`
- Region Source: Your session has ended. Please login.

## Create Button: "Login" on Page 0
- Button Name: LOGIN
- Text Label: Login
- Display in Region: Session Ended
- Target is a: Page in this Application
- Page: 1

## Create Region: "JavaScript - Session Timeout" on Page 0
- Title: JavaScript - Session Timeout
- Type: HTML Text
- Template: No Template
- Region Source:

```html
<script src="#APP_IMAGES#jquery-1.3.2.min.js" type="text/javascript"></script>
<script src="#APP_IMAGES#jquery.simplemodal-1.3.min.js" type="text/javascript"></script>
<script src="#APP_IMAGES#idle-timer-0.7.080609.js" type="text/javascript"></script>
<script src="#APP_IMAGES#jquery.jApex.0.9.2.js" type="text/javascript"></script>
<script src="#APP_IMAGES#jquery.countdown-1.5.3.js" type="text/javascript"></script>

<script type="text/javascript">
  //*** Insert script from below ***/
  //Removed for display purposes
</script>
```

_You'll need to upload the JS files beforehand. Please see the list above to obtain the files._

Here's the script to put into the region above. I separated them for display purposes.

I probably should have created this as a jQuery plugin. I may convert it later on.

```js
var gTimeout = {
  //debug
  debug: false, //Set to True to turn on debugging
  debugFn: function(pMsg) {
    if (gTimeout.debug){
      console.log(pMsg);
    }
  }, //debug
  modalRegions: {
    //Region that contains the "Extend Session" information
    extendSession: {
      id: 'P0_REG_EXTEND_SESSION',
      backgroundColor: '#CCC',
      opacity: 70,
      openFn: function() {
        gTimeout.debugFn('gTimeout.modalRegions.extendSession.openFn');
        // Start display timeout counter
        $('#timeoutCountdownDisplay').countdown('destroy');
        $('#timeoutCountdownDisplay').countdown({
          until: '+' + (gTimeout.timers.killSession.time / 1000),
          compact: true,
          format: 'M:S'
        });
        // Load modal box to give user option to extend session
        $('#' + gTimeout.modalRegions.extendSession.id).modal({
          overlayCss: {backgroundColor: this.backgroundColor},
          opacity: this.opacity
        });
        return;
      }, //openFn
      closeFn: function(){
        gTimeout.debugFn('gTimeout.modalRegions.extendSession.closeFn');
        $.modal.close();
        return;
      }//closeFn
    },
    //Region that will be displayed if the user does not extend thier session
    sessionEnded: {
      id: 'P0_REG_SESSION_ENDED',
      backgroundColor: 'black',
      opacity: 70,
      openFn: function() {
        gTimeout.debugFn('gTimeout.modalRegions.sessionEnded.openFn');
        // Close Extend Sessios modal window
        gTimeout.modalRegions.extendSession.closeFn();
        // Open Logout modal window
        $('#' + gTimeout.modalRegions.sessionEnded.id).modal({
          overlayCss: {backgroundColor: this.backgroundColor},
          opacity: this.opacity
        });
        return;
      }// openFn
    }//sessionEnded
  },//modalRegions
  timers: {
    //Ping APEX Session timer will update the database session timer
    pingApexSession: {
      id: -1,
      time: 5000, //Time to keep database session alive. This should be really close to the APEX idle time
      loadFn: function(){
        gTimeout.debugFn('gTimeout.timers.pingApexSession.loadFn:');
        this.id = setTimeout('gTimeout.timers.pingApexSession.fn();', this.time);
        return;
      },
      unloadFn: function(){
        gTimeout.debugFn('gTimeout.timers.pingApexSession.unloadFn:');
        clearTimeout(this.id);
        this.id = -1;
        return;
      },//unloadFn
      fn: function(){
        gTimeout.debugFn('gTimeout.timers.pingApexSession.fn: Extending APEX Session');
        jQuery.jApex.ajax({
          appProcess: 'AP_NULL',
          success: function(){},
          async: true
        });
        gTimeout.timers.pingApexSession.loadFn();
        return;
      }//fn
    },//pingApexSessions
    //Kill current session. This is called when the user gets the option to extend their session
    killSession: {
      id: -1,
      time: 5000, // Time to kill the APEX session once launched. Should only be run when extend session popup box is loaded
      loadFn: function(){
        gTimeout.debugFn('gTimeout.timers.killSession.loadFn:');
        this.id = setTimeout('gTimeout.timers.killSession.killFn();', this.time);
        return;
      },
      unloadFn: function(){
        gTimeout.debugFn('gTimeout.timers.killSession.unloadFn: ');
        clearTimeout(this.id);
        this.id = -1;
        gTimeout.modalRegions.extendSession.closeFn(); // Close extendSession Modal
        return;
      },
      killFn: function(){
        gTimeout.debugFn('gTimeout.timers.killSession.killFn: Killing APEX Session');
        // Open Logout modal window
        gTimeout.modalRegions.sessionEnded.openFn();
        // Stop ping Apex session
        gTimeout.timers.pingApexSession.unloadFn();
        // Logout APEX session
        jQuery.jApex.ajax({
          appProcess: 'AP_LOGOUT',
          success: function(){},
          async: true
        });
        return;
      },
      // Prevents the session from being killed. We should be in a about to kill state now
      liveFn: function(){
        gTimeout.debugFn('gTimeout.timers.killSession.liveFn: ');
        //Check that we're about to be killed
        if (this.id == -1){
          alert('Session is not marked to be killed');
          return;
        }

        // Stop the kill timer
        this.unloadFn();
        return;
      }//liveFn
    },//killSession
    // Timer for user movement time
    idle:{
      time: 5000, // Time to load the "Extend Session" popup box
      loadFn: function(){
        gTimeout.debugFn('gTimeout.timers.idle.loadFn:');
        $.idleTimer(this.time);
        $(document).bind("idle.idleTimer", function(){gTimeout.timers.idle.idleFn();});
        // Trigger countdown timer
        return;
      },
      idleFn: function(){
        gTimeout.debugFn('gTimeout.timers.idle.idleFn:');
        // Load modal box to give user option to extend session
        gTimeout.modalRegions.extendSession.openFn();
        // Only load if we're not in a kill state
        if (gTimeout.timers.killSession.id == -1){
          gTimeout.timers.killSession.loadFn();
        }
        return;
      } //idle Fn     
    } // idle
  },//timers
  loadFn: function() {
    gTimeout.timers.pingApexSession.loadFn(); // Keep database sessions alive
    gTimeout.timers.idle.loadFn(); // Turn on user idle timer
    return;
  }//loadFn

};//gTimeout

$(document).ready(function(){
  // Set Parameters
  gTimeout.timers.pingApexSession.time = 5 * 1000; // Refresh APEX session every 5 seconds. This should be really close to your apex session timeout values
  gTimeout.timers.idle.time = 10 * 1000; // 10 seconds of inactivity will trigger this window
  gTimeout.timers.killSession.time = 10 * 1000; // Once the warning message pops up, user has 10 seconds to extend their session
  // Configure Modal windows (not required)
  gTimeout.modalRegions.extendSession.backgroundColor = '#CCC';

  gTimeout.loadFn();
});
```
