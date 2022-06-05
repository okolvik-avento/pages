# Quick and dirty guide to IIS crash dump creation.
#### By Ole Tøsse Kolvik

## Creating IIS userdumps

Userdumps can be useful for developers to dig down into the callstack of exceptions

Download and install [DebugDiag](https://www.microsoft.com/en-us/download/details.aspx?id=58210), the Microsoft Debug Diagnostic tool

Launch DebugDiag

### Add a new rule
![addrule.png]({{site.baseurl}}/addrule.png)

Add a new rule.


### Select rule
![selectrule.png]({{site.baseurl}}/selectrule.png)

We want to collect crash dumps, so select "Crash", then click next.

### Select target
![selecttarget.png]({{site.baseurl}}/selecttarget.png)

You can debug all IIS instances or select an application pool.
I recommend only selecting the application pool you need to debug.


### Select the application pool you want to debug
![selectapppool.png]({{site.baseurl}}/selectapppool.png)

Select your application pool in the list, then click next.


### Configuration
![advancedconfig.png]({{site.baseurl}}/advancedconfig.png)

Set the action type for unconfigured first chance exceptions to "Log stack trace". This can make it easier to set up exception rules later. You can then check all exceptions in the log file.

Set action limit to how many errors you want to catch. Setting it to 0 will always log exceptions, any number above 0 will create logs until the specified number of crashes is reached. After that logging will stop.

Set maximum number of userdumps. Full userdumps can be several hundred MB, it's smart to limit the number of dumps so you don't fill your entire disk. If you are closely monitoring the progress you can use a relatively high number.


#### Exceptions
Click "Exceptions" in advanced configuration, then click "Add exception" in the window that pops up.

![addexception1.png]({{site.baseurl}}/addexception1.png)

Select the exception you want to log in the list. Depending on the type of exception there will be a few more options.
For CLR exceptions you can filter by exception message or faulting module.
Nullpointer exceptions are usually exception code C0000005 - Access Violation.

Set the action type to "Full Userdump" and the action limit to how many userdumps you wish to capture.
Setting this value to 1 will capture a userdump the first time the exception happens, but not any after that.

![addexception2.png]({{site.baseurl}}/addexception2.png)

After you are done, click "Save & Close"


## Finishing

![advancedconfig2.png]({{site.baseurl}}/advancedconfig2.png)

Click next

If you set the action limit to 0, you will get a warning when you click "Next". 
![warning.png]({{site.baseurl}}/warning.png)

If you are absolutely sure that you will be able to monitor the output at all times during debugging click "Yes". If you aren't click no and change the action limit to a sane number.

![selectlocation.png]({{site.baseurl}}/selectlocation.png)

Write a name (or used the auto-generated one) and select the location where you want to store logs and dumps.

![activate.png]({{site.baseurl}}/activate.png)

Select "Activate rule now" and click "Finish".
The rule will start monitoring immediately. Open the folder where you selected to store logs.

## DebugDiag Analysis
You can use DebugDiag Analysis (installed with DebugDiag) to get information about the dumps

![analysis.png]({{site.baseurl}}/analysis.png)

Select "CrashHangAnalysis" and click "Add Data Files" (the userdumps). Then start analysis.\
This will give you the exception and stack trace for all the dumps. Use it to filter out what files to keep.

