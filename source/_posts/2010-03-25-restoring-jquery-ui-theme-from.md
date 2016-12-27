---
title: Restoring jQuery UI Theme from ThemeRoller
tags:
  - apex
  - jquery
date: 2010-03-25 09:00:00
alias:
---

[![](http://4.bp.blogspot.com/_33EF80fk9sM/S6mfnc13b-I/AAAAAAAADwY/jKWUIeU-UEg/s320/theme-roller.jpg)](http://4.bp.blogspot.com/_33EF80fk9sM/S6mfnc13b-I/AAAAAAAADwY/jKWUIeU-UEg/s1600/theme-roller.jpg)
If you ever use [jQuery's Theme Roller](http://jqueryui.com/themeroller/), you may find it frustrating to alter a custom theme. For example, if you created a theme, downloaded it, then decided to change your colors you may not know how to restore your changes.

Believe it or not there's a pretty easy way to restore a Theme Roller theme. Before you download a theme copy, or bookmark, the URL at the top of the browser. All the changes you have made are stored in the URL! If you forget to copy the URL before downloading, the URL is stored in ..\css\custom-theme\jquery-ui-1.8.custom.css (where 1.8 is the version number).

Here's an example of a theme modification where I changed the header background to red: [Red Header](http://jqueryui.com/themeroller/#ffDefault=Verdana%2CArial%2Csans-serif&fwDefault=normal&fsDefault=1.1em&cornerRadius=4px&bgColorHeader=e10505&bgTextureHeader=03_highlight_soft.png&bgImgOpacityHeader=75&borderColorHeader=aaaaaa&fcHeader=222222&iconColorHeader=222222&bgColorContent=ffffff&bgTextureContent=01_flat.png&bgImgOpacityContent=75&borderColorContent=aaaaaa&fcContent=222222&iconColorContent=222222&bgColorDefault=e6e6e6&bgTextureDefault=02_glass.png&bgImgOpacityDefault=75&borderColorDefault=d3d3d3&fcDefault=555555&iconColorDefault=888888&bgColorHover=dadada&bgTextureHover=02_glass.png&bgImgOpacityHover=75&borderColorHover=999999&fcHover=212121&iconColorHover=454545&bgColorActive=ffffff&bgTextureActive=02_glass.png&bgImgOpacityActive=65&borderColorActive=aaaaaa&fcActive=212121&iconColorActive=454545&bgColorHighlight=fbf9ee&bgTextureHighlight=02_glass.png&bgImgOpacityHighlight=55&borderColorHighlight=fcefa1&fcHighlight=363636&iconColorHighlight=2e83ff&bgColorError=fef1ec&bgTextureError=05_inset_soft.png&bgImgOpacityError=95&borderColorError=cd0a0a&fcError=cd0a0a&iconColorError=cd0a0a&bgColorOverlay=aaaaaa&bgTextureOverlay=01_flat.png&bgImgOpacityOverlay=0&opacityOverlay=30&bgColorShadow=aaaaaa&bgTextureShadow=01_flat.png&bgImgOpacityShadow=0&opacityShadow=30&thicknessShadow=8px&offsetTopShadow=-8px&offsetLeftShadow=-8px&cornerRadiusShadow=8px).

APEX 4.0 will be using jQuery and some jQuery UI components, so I hope this helps when customizing the look and feel of your applications.
