---
layout: post
title: "Mountain Lion and iStat Pro"
date: 2012-08-08 19:58
comments: true
categories: [OS X, Tips]
excerpt: Make iStat Pro display top cpu processes at Mountain Lion 
---
I'm using OS X at my MBP as a main operation system and iStat Pro widget
for Dashboard to monitor temps, processes, etc.
After upgrading to OS X 10.8 Mountain Lion the processes section was not working.

Here is a small instruction to make processes be displayed again.

1. Open iStat Pro widget directory, it is located in `~/Library/Widgets/iStat\ Pro.wdgt/`
or `/Library/Widgets/iStat\ Pro.wdgt/`;

2. If you are using horizontal (wide) widget edit the `Wide.js` file and if 
vertical (tall) - `Tall.js`;

3. Find function `WideSkinController.prototype.updateProcesses = function(){` and 
replace all `PID|$1` with `PID| $1` (add space after pipe) in this section. So, you should
get something like this:

```js
WideSkinController.prototype.updateProcesses = function(){
	var _self = this;
	var exclude = "";
	if(p.v("processes_excludewidgets") == 'on')
		exclude = " grep -v DashboardClient | ";
	
	if(p.v("processes_sort_mode") == 'cpu')
		widget.system('ps -arcwwwxo "pid %cpu command" | egrep "PID| $1" | grep -v grep | ' + exclude + ' head -7 | tail -6 | awk \'{print "<pid>"$1"</pid><cpu>"$2"</cpu><name>"$3,$4,$5"</name></item>"}\'', function(data){ _self.updateProcessesOut(data);});
	else
		widget.system('ps -amcwwwxo "pid rss command"  | egrep "PID| $1" | grep -v grep | ' + exclude + ' head -7 | tail -6 | awk \'{print "<pid>"$1"</pid><cpu>"$2"</cpu><name>"$3,$4,$5"</name></item>"}\'', function(data){ _self.updateProcessesOut(data);});
}
```

4. Restart the iStat Pro widget and the processes list will be working again! 
