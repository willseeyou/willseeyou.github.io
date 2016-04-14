---
layout: post
title: "gitk not work on Mac"
date: 2016-03-24 15:13:00 +0800
categories: git
---
### When I try to start gitk on Mac, below error occurs:

```
Error in startup script: unknown color name "lime"
    (processing "-fore" option)
    invoked from within
"$ctext tag conf m2 -fore [lindex $mergecolors 2]"
    (procedure "makewindow" line 347)
    invoked from within
"makewindow"
    (file "/usr/local/bin/gitk" line 12434)
```

### Solution
* brew cask install tcl

### Reference
* [gitk-not-work-on-mac](http://stackoverflow.com/questions/34637896/gitk-will-not-start-on-mac-unknown-color-name-lime)
