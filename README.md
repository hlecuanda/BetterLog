# BetterLog
With one line of code, BetterLog extends the native apps script Logger and 
gives you automatic additional features like logging to a spreadsheet and more.

## About this fork
Note: the **[upstream BetterLog Library](https://github.com/peterherrmann/BetterLog)**, from which
I forked; is available with the key  `iMYB7yzedMbnJaMKECt6Sm7FLDhaBgl_dE` 

If you wish to use my ~improvements~ changes, use this key instead:    
`MFCkjfQb5nvX8xW17t1y2q4K9BPgm4Dtj`

Note however, that this is **not** production grade, should be considered 
*work in progress* (sporadically) and that  unexpected things may break as my 
dev branch is not always in a consistent state.  Also, **your printer may catch fire**. 
Use at you own risk. 

If you need something actually reliable,  please refer to the upstream repo.  
If you **do** use it, please report any bugs on this repo's 
[issue tracker](https://github.com/hlecuanda/BetterLog/issues).

The features i'm working on are
- [ ] Multicolumn output
    - [x] Standard log output in 4 columns for easy filtering using filter views
    - [ ] Automatic custom column detection when using a separatpr char on the logging output
- [ ] Refactor Apps-script features being deprecated (`ScriptProperties`)
- [ ] Make friendlier documentation, specially for the newcomer to GAS.


## Setup
This library is already published as an Apps Script, making it easy to include
in your project. To add it to your script, do the following in the Apps Script
code editor:

1. Click on the menu item "Resources > Libraries..."
2. In the "Find a Library" text box, enter the project key
   `MFCkjfQb5nvX8xW17t1y2q4K9BPgm4Dtj` and click the "Select" button.
3. Choose a version in the dropdown box (usually best to pick the latest version).
4. Click the "Save" button.


Alternatively, you can copy and paste the Code.gs file directly into your script project.

## Getting Started

````js
// Add one line to use BetterLog
Logger = BetterLog.useSpreadsheet('your-spreadsheet-key-goes-here'); 

//Now you can log and it will also log to the spreadsheet
Logger.log("That's all you need to do");
````

Any logs you generate are automatically copied and rolled over once they reach 50,000 lines so you'll always have a full history of log events. 

![](http://i.imgur.com/6U3z7dN.png)

With some good error handling you can catch and log stack traces:

````js
function myFunction() {
  try {
    // Add one line to use BetterLog and log to a spreadsheet
    Logger = BetterLog.useSpreadsheet('your-spreadsheet-key-goes-here'); 

    //Now you can log and it will also log to the spreadsheet
    Logger.log("That's all you need to do");  

    //Do more logging
    for (var i = 0; i < 5; i++) {
      var processingMessage = 'Processing ' + i;
      Logger.finest('This is inside my loop. i is %s', i );
    }
    //We're done
    Logger.log('The loop is done and i is now %s', i );
    
  } catch (e) { //with stack tracing if your exceptions bubble up to here
    e = (typeof e === 'string') ? new Error(e) : e;
    Logger.severe('%s: %s (line %s, file "%s"). Stack: "%s" . While processing %s.',e.name||'', 
               e.message||'', e.lineNumber||'', e.fileName||'', e.stack||'', processingMessage||'');
    throw e;
  }
}
````
Errors can be displayed with stacktraces:

![](http://i.imgur.com/WewgZCD.png)

##Tips & Tricks

You can change the message layout:

````js
Logger.DATE_TIME_LAYOUT = "''";  //ref http://docs.oracle.com/javase/6/docs/api/java/text/SimpleDateFormat.html
Logger.log('Look no timestamp!');
````
![](http://i.imgur.com/t7NiigO.png)

## Troubleshooting

##### `Error: Not enough arguments (line 134, file "Code", project "BetterLog")`

This can happen if you call the log method without enough arguments. For example, this will cause the error:
`Logger.log("Test 1 2 %s")`
whereas this won't cause the error:
`Logger.log("Test 1 2 %s", "three")`

##### `Execution failed: You do not have permission to perform that action. (line 384, file "Code", project "BetterLog")`

You may need to run a function (any or an empty dummy function) in [the IDE](https://script.google.com/). This
will provoke the script authorization.

## Known Issues

* BetterLog currently can't be used to log from [custom functions](https://developers.google.com/apps-script/guides/sheets/functions) in Google Sheets because [custom functions are run in the context of an anononymous user](https://developers.google.com/apps-script/guides/services/authorization) which means that BetterLog will not be able to edit the spreadsheet to create or append to a log sheet. I'm not aware of a workaround but you could write a web app whose URL could be called anonymously with a query parameter that is written to your log. 

[1] https://github.com/hlecuanda/BetterLog/issues "Issue Tracker for this fork"
