---
layout: post
title: "Cobalt theme for Matlab"
date: 2013-01-23 16:20
comments: true
categories: 
---


This is a quick post showing how to apply a cobalt-like theme to Matlab's workspace and editor. Matlab has rather limited font and colour customization options compared to XCode ([see my other post about the Cobalt theme](/blog/2013/01/09/cobalt/)) but it is still possible to change the default theme to something that I personally find a little easier on the eyes.

The default Matlab theme (when viewed on a Mac) is shown below . . . 

![raw](http://i50.tinypic.com/10rq15s.png)

The end result is this . . .

![cobalt](http://i50.tinypic.com/2rqd07m.png)

If you like the look of this, make a file called matlab.prf containing the text at the end of this post. Navigate to the folder containing the original matlab.prf, make a backup of the original file and replace it with the new version. In *nix systems, this file is located in . . .

```
$HOME/.matlab/<version>/matlab.prf. 	
```

Below is the config text  . . .

```
#MATLAB Preferences
#Wed Jan 23 15:55:28 GMT 2013
Color_CmdWinWarnings=C-39936
Color_CmdWinErrors=C-1703936
Colors_M_UnterminatedStrings=C-5111808
Colors_M_SystemCommands=C-16022329
ColorsBackground=C-16701878
Colors_M_Warnings=C-27648
ColorsText=C-1
Colors_M_Errors=C-65536
Colors_M_Keywords=C-20124
Colors_HTML_HTMLLinks=C-16711681
Colors_M_Strings=C-16711936
ColorsUseMLintAutoFixBackground=Btrue
Colors_M_Comments=C-16711681
ColorsUseSystem=Bfalse
Desktop.Font.Code=F0 12 Monaco
```
