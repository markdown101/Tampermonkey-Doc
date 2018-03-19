# Tampermonkey-Doc 
[中文版](https://github.com/gaoshengyu/Tampermonkey-Doc/blob/master/zh-cn.md)

This section describes how the Tampermonkey API can be used and what is different to Geasemonkey.

## CONTENT

### Userscript Header

[@name](#@name)
[@namespace](#@namespace)
[@version](#@version)
[@author](#@author)
[@description](#@description)
[@homepage, @homepageURL, @website and @source](#@homepage, @homepageURL, @website and @source)
[@icon, @iconURL and @defaulticon](#@icon, @iconURL and @defaulticon)
[@icon64 and @icon64URL](#@icon64 and @icon64URL)
[@updateURL](#@updateURL)
[@downloadURL](#@downloadURL)
[@supportURL](#@supportURL)
[@include](#@include)
[@match](#@match)
[@exclude](#@exclude)
[@require](#@require)
[@resource](#@resource)
[@connect](#@connect)
[@run-at](#@run-at)
[@grant](#@grant)
[@noframes](#@noframes)
[@unwrap](#@unwrap)
[@nocompat](#@nocompat)

### Application Programming Interface

[unsafeWindow](#unsafeWindow)
[Subresource Integrity](#Subresource Integrity)
[GM_addStyle(css)](#GM_addStyle(css))
[GM_deleteValue(name)](#GM_deleteValue(name))
[GM_listValues()](#GM_listValues())
[GM_addValueChangeListener(name, function(name, old_value, new_value, remote) {})](#GM_addValueChangeListener(name, function(name, old_value, new_value, remote) {}))
[GM_removeValueChangeListener(listener_id)](#GM_removeValueChangeListener(listener_id))
[GM_setValue(name, value)](#GM_setValue(name, value))
[GM_getValue(name, defaultValue)](#GM_getValue(name, defaultValue))
[GM_log(message)](#GM_log(message))
[GM_getResourceText(name)](#GM_getResourceText(name))
[GM_getResourceURL(name)](#GM_getResourceURL(name))
[GM_registerMenuCommand(name, fn, accessKey)](#GM_registerMenuCommand(name, fn, accessKey))
[GM_unregisterMenuCommand(menuCmdId)](#GM_unregisterMenuCommand(menuCmdId))
[GM_openInTab(url, options), GM_openInTab(url, loadInBackground)](#GM_openInTab(url, options), GM_openInTab(url, loadInBackground))
[GM_xmlhttpRequest(details)](#GM_xmlhttpRequest(details))
[GM_download(details), GM_download(url, name)](#GM_download(details), GM_download(url, name))
[GM_getTab(cb)](#GM_getTab(cb))
[GM_saveTab(tab)](#GM_saveTab(tab))
[GM_getTabs(cb)](#GM_getTabs(cb))
[GM_notification(details, ondone), GM_notification(text, title, image, onclick)](#GM_notification(details, ondone), GM_notification(text, title, image, onclick))
[GM_setClipboard(data, info)](#GM_setClipboard(data, info))
[GM_info](#GM info)
[<><![CDATA[your_text_here]\]\>\<\/\>](#<><![CDATA[your_text_here]\]\>\<\/\)

## USERSCRIPT HEADER

### @name
The name of the script.

### @namespace
The namespace of the script.

### @version
The script version. This is used for the update check, in case the script is not installed from userscript.org or TM has problems to retrieve the scripts meta data.

### @author
The scripts author.

### @description
A short significant description.

### @homepage, @homepageURL, @website and @source
The authors homepage that is used at the options page to link from the scripts name to the given page. Please note that if the **@namespace** tag starts with 'http://' its content will be used for this too.

### @icon, @iconURL and @defaulticon
The script icon in low res.

### @icon64 and @icon64URL
This scripts icon in 64x64 pixels. If this tag, but **@icon** is given the **@icon** image will be scaled at some places at the options page.

### @updateURL
An update URL for the userscript.
Note: a @version tag is required to make update checks work.

### @downloadURL
Defines the URL where the script will be downloaded from when an update was detected. If the value none is used, then no update check will be done.

### @supportURL
Defines the URL where the user can report issues and get personal support.

### @include
The pages on that a script should run. Multiple tag instances are allowed.
Please note that @include doesn't support the URL hash parameter. Please visit this forum thread for more information: [click](http://forum.tampermonkey.net/viewtopic.php?p=3094#p3094).
```
// @include http://tampermonkey.net/*
// @include http://*
// @include https://*
// @include *
```

### @match
More or less equal to the **@include** tag. You can get more information [here](http://code.google.com/chrome/extensions/match_patterns.html) .
Note: the '<all_urls>' statement is not yet supported and the scheme part also accepts 'http\*://'.
Multiple tag instances are allowed.
### @exclude
Exclude URLs even it they are included by **@include** or **@match** .
Multiple tag instances are allowed.

### @require
Points to a JavaScript file that is loaded and executed before the script itself starts running.
Note: the scripts loaded via **@require** and their "use strict" statements might influence the userscript's strict mode!
```
// @require https://code.jquery.com/jquery-2.1.4.min.js
// @require https://code.jquery.com/jquery-2.1.3.min.js#sha256=23456...
// @require https://code.jquery.com/jquery-2.1.2.min.js#md5=34567...,sha256=6789...
```
Please check the [sub-resource integrity](http://tampermonkey.net/documentation.php?ext=dhdg&locale=en#Subresource_Integrity) section for more information how to ensure integrity. Multiple tag instances are allowed.

### @resource
Preloads resources that can by accessed via GM_getResourceURL and GM_getResourceText by the script.
```
// @resource icon1 http://tampermonkey.net/favicon.ico
// @resource icon2 /images/icon.png
// @resource html http://tampermonkey.net/index.html
// @resource xml http://tampermonkey.net/crx/tampermonkey.xml
// @resource SRIsecured1 http://tampermonkey.net/favicon.ico#md5=123434...
// @resource SRIsecured2 http://tampermonkey.net/favicon.ico#md5=123434...;sha256=234234...
```
Please check the [sub-resource integrity](http://tampermonkey.net/documentation.php?ext=dhdg&locale=en#Subresource_Integrity) section for more information how to ensure integrity. Multiple tag instances are allowed.

### @connect
This tag defines the domains (no top-level domains) including subdomains which are allowed to be retrieved by GM_xmlhttpRequest
```
// @connect <value>
```
<value> can have the following values:
- domains like tampermonkey.net (this will also allow all sub-domains)
- sub-domains i.e. safari.tampermonkey.net
- self to whitelist the domain the script is currently running at
- localhost to access the localhost
- 1.2.3.4 to connect to an IP address
- \*
If it's not possible to declare all domains a userscript might connect to then it's a good practice to do the following:
**Declare all known or at least all common domains** that might be connected by the script. This way the confirmation dialog can be avoided for most of the users.

Additionally add "@connect \*" to the script. By doing so Tampermonkey will still ask the user whether the next connection to a not mentioned domain is allowed, but also **offer a "Always allow all domains" button**. If the user clicks at this button then all future requests will be permitted automatically.

Users can also whitelist all requests by adding '\*' to the user domain whitelist at the script settings tab.

Notes:
- both, the initial **and** the final URL will be checked!
- for backward compatibility to Scriptish [@domain](https://github.com/scriptish/scriptish/wiki/Manual%3A-Metadata-Block#user-content-domain-new-in-scriptish) tags are interpreted as well.
Multiple tag instances are allowed.

##@run-at
Defines the moment the script is injected. In opposition to other script handlers, **@run-at** defines the first possible moment a script wants to run. This means it may happen, that a script that uses the **@require** tag may be executed after the document is already loaded, cause fetching the required script took that long. Anyhow, all DOMNodeInserted and DOMContentLoaded events that happended after the given injection moment are cached and delivered to the script when it is injected.
```
// @run-at document-start
```
The script will be injected as fast as possible.
```
// @run-at document-body
```
The script will be injected if the body element exists.
```
// @run-at document-end
```
The script will be injected when or after the DOMContentLoaded event was dispatched.
```
// @run-at document-idle
```
The script will be injected after the DOMContentLoaded event was dispatched.
```
// @run-at context-menu
```
The script will be injected if it is clicked at the browser context menu (desktop Chrome-based browsers only).
Note: all **@include** and **@exclude** statements will be ignored if this value is used, but this may change in the future.

### @grant
@grant is used to whitelist GM_* functions, the unsafeWindow object and some powerful window functions. If no @grant tag is given TM guesses the scripts needs.
```
// @grant GM_setValue
// @grant GM_getValue
// @grant GM_setClipboard
// @grant unsafeWindow
// @grant window.close
// @grant window.focus
```
Since closing and focusing tabs is a powerful feature this needs to be added to the @grant statements as well.

If @grant is followed by 'none' the sandbox is disabled and the script will run directly at the page context. In this mode no GM_* function but the GM_info property will be available.
```
// @grant none
```

### @noframes
This tag makes the script running on the main pages, but not at iframes.

### @unwrap
This tag is ignored because, it is not needed at Google Chrome/Chromium.

### @nocompat
At the moment TM tries to detect whether a script was written in knowledge of Google Chrome/Chromium by looking for the **@match** tag, but not every script uses it. That's why TM supports this tag to disable all optimizations that might be necessary to run scripts written for Firefox/Greasemonkey. To keep this tag extensible you can to add the browser name that can be handled by the script.
```
// @nocompat Chrome
```

##APPLICATION PROGRAMMING INTERFACE

### unsafeWindow
The unsafeWindow object provides full access to the pages javascript functions and variables.

### Subresource Integrity
The hash component of the URL of **@resource** and **@require** tags can be used for this purpose.
```
// @resource SRIsecured1 http://tampermonkey.net/favicon1.ico#md5=ad34bb...
// @resource SRIsecured2 http://tampermonkey.net/favicon2.ico#md5=ac3434...,sha256=23fd34...
// @require https://code.jquery.com/jquery-2.1.1.min.js#md5=45eef...
// @require https://code.jquery.com/jquery-2.1.2.min.js#md5=ac56d...,sha256=6e789...
```
TM supports MD5 hashes as a fallback natively, all other (SHA-1, SHA-256, SHA-384 and SHA-512) depend on [window.crypto](https://developer.mozilla.org/en-US/docs/Web/API/Crypto). In case multiple hashes (separated by comma or semicolon) are given the last currently supported one is used by TM. If the content of the external resource doesn't match the selected hash, then the resource is not delivered to the userscript.

### GM_addStyle(css)
Adds the given style to the document and returns the injected style element.

### GM_deleteValue(name)
Deletes 'name' from storage.

### GM_listValues()
List all names of the storage.

### GM_addValueChangeListener(name, function(name, old_value, new_value, remote) {})
Adds a change listener to the storage and returns the listener ID. 
'name' is the name of the observed variable. 
The 'remote' argument of the callback function shows whether this value was modified from the instance of another tab (true) or within this script instance (false). 
Therefore this functionality can be used by scripts of different browser tabs to communicate with each other.

### GM_removeValueChangeListener(listener_id)
Removes a change listener by its ID.

### GM_setValue(name, value)
Set the value of 'name' to the storage.

### GM_getValue(name, defaultValue)
Get the value of 'name' from storage.

### GM_log(message)
Log a message to the console.

### GM_getResourceText(name)
Get the content of a predefined @resource tag at the script header.

### GM_getResourceURL(name)
Get the base64 encoded URI of a predefined @resource tag at the script header.

### GM_registerMenuCommand(name, fn, accessKey)
Register a menu to be displayed at the Tampermonkey menu at pages where this script runs and returns a menu command ID.

### GM_unregisterMenuCommand(menuCmdId)
Unregister a menu command that was previously registered by GM_registerMenuCommand with the given menu command ID.

### GM_openInTab(url, options), GM_openInTab(url, loadInBackground)
Open a new tab with this url. The options object can have the following properties:
- active decides whether the new tab should be focused,
- insert that inserts the new tab after the current one and
- setParent makes the browser re-focus the current tab on close.
Otherwise the new tab is just appended. **loadInBackground** has the opposite meaning of **active** and was added to achieve Greasemonkey 3.x compatibility. If neither **active** nor **loadInBackground** is given, then the tab will not be focused. This function returns an object with the function **close**, the listener **onclosed** and a flag called **closed**.

### GM_xmlhttpRequest(details)
Make an xmlHttpRequest.
Property of details:
- **method** one of GET, HEAD, POST
- **url** the destination URL
headers ie. user-agent, referer, ... (some special headers are not supported by Safari and Android - **browsers)
- **data** some string to send via a POST request
- **binary** send the data string in binary mode
- **timeout** a timeout in ms
- **context** a property which will be added to the response object
- **responseType** one of arraybuffer, blob, json
- **overrideMimeType** a MIME type for the request
- **anonymous** don't send cookies with the requests (please see the fetch notes)
- **fetch** (beta) use a fetch instead of a xhr request
(at Chrome this causes xhr.abort, details.timeout and xhr.onprogress to not work and makes - **xhr.onreadystatechange receive only readyState 4 events)
- **username** a username for authentication
- **password** a password
- **onabort** callback to be executed if the request was aborted
- **onerror** callback to be executed if the request ended up with an error
- **onload** callback to be executed if the request was loaded
- **onloadstart** callback to be executed if the request started to load
- **onprogress** callback to be executed if the request made some progress
- **onreadystatechange** callback to be executed if the request's ready state changed
- **ontimeout** callback to be executed if the request failed due to a timeout
Returns an object with the following property:
- **abort** - function to be called to cancel this request

**Note**: the **synchronous** flag at the details is not supported
**Important**: if you want to use this method the please also check the documentation about @connect.

### GM_download(details), GM_download(url, name)
Downloads a given URL to the local disk.
details can have the following attributes:
- **url** - the URL from where the data should be downloaded
- **name** - the filename - for security reasons the file extension needs to be whitelisted at the Tampermonkey options page
- **headers** - see GM_xmlhttpRequest for more details
- **saveAs** - boolean value, show a saveAs dialog
- **onerror** callback to be executed if the download ended up with an error
- **onload** callback to be executed if the download finished
- **onprogress** callback to be executed if the download made some progress
- **ontimeout** callback to be executed if the download failed due to a timeout

The download argument of the onerror callback can have the following attributes:
- **error** - error reason
	- not_enabled - the download feature isn't enabled by the user
	- not_whitelisted - the requested file extension is not whitelisted
	- not_permitted - the user enabled the download feature, but did not give the downloads permission
	- not_supported - the download feature isn't supported by the browser/version
	- not_succeeded - the download wasn't started or failed, the details attribute may provide more information
- **details** - detail about that error
- **Returns** an object with the following property:
- **abort** - function to be called to cancel this download
Depending on the download mode GM_info provides a property called downloadMode which is set to one of the following values: **native**, **disabled** or **browser**.

### GM_getTab(cb)
Get a object that is persistent as long as this tab is open.

### GM_saveTab(tab)
Save the tab object to reopen it after a page unload.

### GM_getTabs(cb)
Get all tab objects as a hash to communicate with other script instances.

### GM_notification(details, ondone), GM_notification(text, title, image, onclick)
Shows a HTML5 Desktop notification and/or highlight the current tab.
details can have the following attributes:
- **text** - the text of the notification (optional if highlight is set)
- **title** - the notificaton title (optional)
- **image** - the image (optional)
- **highlight** - a boolean flag whether to highlight the tab that sends the notfication (optional)
- **timeout** - the time after that the notification will be hidden (optional, 0 = disabled)
- **ondone** - called when the notification is closed (no matter if this was triggered by a timeout or a click) or the tab was highlighted (optional)
- **onclick** - called in case the user clicks the notification (optional)
All parameters do exactly the same like their corresponding details property pendant.

### GM_setClipboard(data, info)
Copies data into the clipboard. The parameter 'info' can be an object like "{ type: 'text', mimetype: 'text/plain'}" or just a string expressing the type ("text" or "html").

### GM_info
Get some info about the script and TM. The object might look like this:
```
Object+
---> script: Object+
------> author: ""
------>copyright: "2012+, You"
------>description: "enter something useful"
------>excludes: Array[0]
------>homepage: null
------>icon: null
------>icon64: null
------>includes: Array[2]
------>lastUpdated: 1338465932430
------>matches: Array[2]
------>downloadMode: 'browser'
------>name: "Local File Test"
------>namespace: "http://use.i.E.your.homepage/"
------>options: Object+
--------->awareOfChrome: true
--------->compat_arrayleft: false
--------->compat_foreach: false
--------->compat_forvarin: false
--------->compat_metadata: false
--------->compat_prototypes: false
--------->compat_uW_gmonkey: false
--------->noframes: false
--------->override: Object+
------------>excludes: false
------------>includes: false
------------>orig_excludes: Array[0]
------------>orig_includes: Array[2]
------------>use_excludes: Array[0]
------------>use_includes: Array[0]
--------->run_at: "document-end"
------>position: 1
------>resources: Array[0]
------>run-at: "document-end"
------>system: false
------>unwrap: false
------>version: "0.1"
---> scriptMetaStr: undefined
---> scriptSource: "// ==UserScript==\n// @name       Local File Test\n ...."
---> scriptUpdateURL: undefined
---> scriptWillUpdate: false
---> scriptHandler: "Tampermonkey"
---> isIncognito: false
---> version: "4.0.25"
```

### <><![CDATA[your_text_here]]></>
Tampermonkey supports this way of storing meta data. TM tries to automatically detect whether a script needs this compatibility option to be enabled.
