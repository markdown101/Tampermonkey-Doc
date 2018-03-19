本节介绍如何使用 Tampermonkey API 以及于 Geasemonkey 的不同之处。

## 目录

### 用户标题头

@name

@namespace

@version

@author

@description

@homepage, @homepageURL, @website and @source

@icon, @iconURL and @defaulticon

@icon64 and @icon64URL

@updateURL

@downloadURL

@supportURL

@include

@match

@exclude

@require

@resource

@connect

@run-at

@grant

@noframes

@unwrap

@nocompat

### 应用程序接口

unsafeWindow

Subresource Integrity

GM_addStyle(css)

GM_deleteValue(name)

GM_listValues()

GM_addValueChangeListener(name, function(name, old_value, new_value, remote) {})

GM_removeValueChangeListener(listener_id)

GM_setValue(name, value)

GM_getValue(name, defaultValue)

GM_log(message)

GM_getResourceText(name)

GM_getResourceURL(name)

GM_registerMenuCommand(name, fn, accessKey)

GM_unregisterMenuCommand(menuCmdId)

GM_openInTab(url, options), GM_openInTab(url, loadInBackground)

GM_xmlhttpRequest(details)

GM_download(details), GM_download(url, name)

GM_getTab(cb)

GM_saveTab(tab)

GM_getTabs(cb)

GM_notification(details, ondone), GM_notification(text, title, image, onclick)

GM_setClipboard(data, info)

GM_info

<><![CDATA[your_text_here]]></>

## 用户标题头

### @name
脚本的名称。

### @namespace
脚本的命名空间。

### @version
脚本版本。这用于更新检查，以防脚本未从userscript.org安装，或者TM有检索脚本元数据的问题。

### @author
脚本作者。

### @description
一个简短而有意义的描述。

### @homepage, @homepageURL, @website and @source
作者主页用于选项页面从脚本名称链接到给定页面。请注意，如果 **@namespace** 标记以'http://'开头，则其内容也将用于此目的。

### @icon, @iconURL and @defaulticon
低分辨率的脚本图标。

### @icon64 and @icon64URL
此脚本图标为64x64像素。如果这个标签，但 @icon 是给定的,@icon 图像将在选项页面的一些地方缩放。

### @updateURL
用户的更新URL。
注意：需要@version标签才能进行更新检查。

### @downloadURL
定义检测到更新时从脚本中下载脚本的URL。如果使用值none，则不会执行更新检查。

### @supportURL
定义用户可以报告问题并获得个人支持的URL。

### @include
脚本应该运行的页面。允许多个标签实例。
请注意@include不支持URL哈希参数。请访问此论坛主题了解更多信息：[click](http://forum.tampermonkey.net/viewtopic.php?p=3094#p3094).
```
// @include http://tampermonkey.net/*
// @include http://*
// @include https://*
// @include *
```


### @match
或多或少与 **@include** 标签相同。 你可以[在这里](http://code.google.com/chrome/extensions/match_patterns.html)获得更多信息。
注意：'<all_urls>'语句尚不支持，方案部分也接受'http\*://'。
允许多个标签实例。
### @exclude
即使它包含在 **@include** 或 **@match**中，也可以排除网址 。
允许多个标签实例。

### @require
指向脚本自身开始运行之前加载并执行的JavaScript文件。
注意：通过 **@require** 加载的脚本及其“严格使用”语句可能会影响userscript的严格模式！
```
// @require https://code.jquery.com/jquery-2.1.4.min.js
// @require https://code.jquery.com/jquery-2.1.3.min.js#sha256=23456...
// @require https://code.jquery.com/jquery-2.1.2.min.js#md5=34567...,sha256=6789...
```
请查看[子资源完整性](http://tampermonkey.net/documentation.php?ext=dhdg&locale=en#Subresource_Integrity)部分以获取更多信息，以确保完整性。允许多个标签实例。

### @resource
通过脚本预先载入可通过GM_getResourceURL和GM_getResourceText访问的资源。
```
// @resource icon1 http://tampermonkey.net/favicon.ico
// @resource icon2 /images/icon.png
// @resource html http://tampermonkey.net/index.html
// @resource xml http://tampermonkey.net/crx/tampermonkey.xml
// @resource SRIsecured1 http://tampermonkey.net/favicon.ico#md5=123434...
// @resource SRIsecured2 http://tampermonkey.net/favicon.ico#md5=123434...;sha256=234234...
```
请查看[子资源完整性](http://tampermonkey.net/documentation.php?ext=dhdg&locale=en#Subresource_Integrity)部分以获取更多信息，以确保完整性。允许多个标签实例。

### @connect
这个标签定义了域名（没有顶级域名），包括允许被 GM_xmlhttpRequest 检索的子域名。
```
// @connect <value>
```
<value> 可以具有以下值：
- 域名，如 tampermonkey.net（这也将允许所有子域名）
- 子域名，即safari.tampermonkey.net
- 自我白名单脚本当前正在运行的域
- 本地主机访问本地主机
- 1.2.3.4 连接到一个IP地址
- \*
如果无法声明所有域可以连接到一个userscript，那么这是一个很好的做法，可以执行以下操作：
**声明所有已知或至少所有**可能由脚本连接的**公共域**。这样，大多数用户可以避免确认对话框。

另外在脚本中添加“@connect \*”。通过这样做，Tampermonkey仍然会询问用户是否允许未提及的域的下一个连接，但也提供了“始终允许所有域”按钮。如果用户点击此按钮，则所有未来的请求将被自动允许。

用户还可以通过在脚本设置选项卡中将“\*”添加到用户域白名单来将所有请求列入白名单。

注意：
最初和最终的网址都会检查！
为了向后兼容Scriptish @domain 标签也被解释。
允许多个标记实例。

##@run-at
定义脚本注入的时刻。与其他脚本处理程序相反， **@run-at** 定义了脚本想要运行的第一个可能时刻。这意味着可能会发生这样的情况：在文档已经加载之后，使用 **@require** 标签的脚本可能会被执行，导致获取所需的脚本需要很长时间。无论如何，在给定注入时刻之后发生的所有DOMNodeInserted和DOMContentLoaded事件都将被缓存并在注入时传递给脚本。
```
// @run-at document-start
```
脚本将尽快注入。
```
// @run-at document-body
```
如果 body 元素存在，脚本将被注入。
```
// @run-at document-end
```
该脚本将在 DOMContentLoaded 事件分派后或之后注入。
```
// @run-at document-idle
```
该脚本将在DOMContentLoaded事件分派后注入。
```
// @run-at context-menu
```
如果在浏览器上下文菜单中单击该脚本（仅限基于Chrome的浏览器），该脚本将被注入。
注意：如果使用这个值，所有的 **@include** 和 **@exclude** 语句将被忽略，但是这可能会在将来改变。

### @grant
@grant用于将GM_ \*函数，unsafeWindow 对象和一些强大的窗口函数列入白名单。如果没有给出 @grant 标签，则TM会猜测脚本需要。
```
// @grant GM_setValue
// @grant GM_getValue
// @grant GM_setClipboard
// @grant unsafeWindow
// @grant window.close
// @grant window.focus
```
由于关闭和聚焦选项卡是一个强大的功能，因此需要将其添加到 @grant 语句中。

如果@grant后面跟着 'none' ，沙箱将被禁用，脚本将直接在页面上下文中运行。在这种模式下，没有 GM_ \* 功能，但 GM_info 属性将可用
```
// @grant none
```

### @noframes
该标记使脚本在主页面上运行，但不在iframe上运行。

### @unwrap
此标记被忽略，因为在Google Chrome / Chromium中不需要此标记。

### @nocompat
目前，TM试图通过查找 **@match** 标签来检测脚本是否以 Google Chrome / Chromium 的知识编写，但并非每个脚本都使用它。这就是为什么TM支持该标签来禁用所有可能需要运行为 Firefox / Greasemonkey 编写的脚本的优化。为了保持这个标签的可扩展性，您可以添加脚本可以处理的浏览器名称。
```
// @nocompat Chrome
```

##应用程序接口

### unsafeWindow
unsafeWindow对象提供对页面javascript函数和变量的完全访问。

### Subresource Integrity
**@resource** 和 **@require** 标签 的URL的哈希组件可用于此目的。
```
// @resource SRIsecured1 http://tampermonkey.net/favicon1.ico#md5=ad34bb...
// @resource SRIsecured2 http://tampermonkey.net/favicon2.ico#md5=ac3434...,sha256=23fd34...
// @require https://code.jquery.com/jquery-2.1.1.min.js#md5=45eef...
// @require https://code.jquery.com/jquery-2.1.2.min.js#md5=ac56d...,sha256=6e789...
```
TM本身支持MD5散列作为回退，所有其他（SHA-1，SHA-256，SHA-384和SHA-512）都依赖于[window.crypto](https://developer.mozilla.org/en-US/docs/Web/API/Crypto)。如果给出多个哈希（以逗号或分号分隔），则TM使用当前支持的最后一个哈希。如果外部资源的内容与所选散列不匹配，则资源不会传递给userscript。

### GM_addStyle(css)
将给定样式添加到文档并返回注入的样式元素。

### GM_deleteValue(name)
从存储中删除“名称”。

### GM_listValues()
列出存储的所有名称。

### GM_addValueChangeListener(name, function(name, old_value, new_value, remote) {})
将更改侦听器添加到存储并返回侦听器ID。
'name' 是被观察变量的名称。
回调函数的 'remote' 参数显示此值是从另一个选项卡实例（true）还是在此脚本实例（false）中修改的。
因此，不同浏览器选项卡的脚本可以使用此功能彼此进行通信。

### GM_removeValueChangeListener(listener_id)
通过其ID删除更改侦听器。

### GM_setValue(name, value)
将 'name' 的值设置为存储。

### GM_getValue(name, defaultValue)
从存储中获取 'name' 的值。

### GM_log(message)
将消息记录到控制台。

### GM_getResourceText(name)
在脚本标题处获取预定义的 @resource 标记的内容。

### GM_getResourceURL(name)
在脚本头获取预定义@resource标记的base64编码URI。

### GM_registerMenuCommand(name, fn, accessKey)
注册一个菜单，使其在运行此脚本的页面上的Tampermonkey菜单中显示，并返回菜单命令ID。

### GM_unregisterMenuCommand(menuCmdId)
使用给定的菜单命令ID取消注册之前由GM_registerMenuCommand注册的菜单命令。

### GM_openInTab(url, options), GM_openInTab(url, loadInBackground)
用这个URL打开一个新标签。选项对象可以具有以下属性：
- 积极决定是否应该关注新标签，
- 插入插入新标签之后的当前和
- setParent使浏览器关闭当前选项卡。
否则，新选项卡会被添加。**loadInBackground**具有与**active**相反的含义，并被添加以实现Greasemonkey 3.x兼容性。如果既没有激活也没有**loadInBackground**，那么该选项卡将不会被聚焦。这个函数返回一个**关闭**函数的对象，一个**关闭**的监听器和一个**关闭**的标志。

### GM_xmlhttpRequest(details)
创建一个xmlHttpRequest。
详情属性：
- 方法 GET，HEAD，POST之一
- 链接目标网址
- 标题即用户代理，引用程序，... （Safari和Android浏览器不支持某些特殊的头文件）
- 数据通过POST请求发送一些字符串
- 二进制发送二进制模式的数据串
- 超时以毫秒超时
- 上下文将被添加到响应对象的属性
- responseType arraybuffer，blob，json之一
- overrideMime为请求提供MIME类型
- 匿名不要将cookies发送给请求（请参阅提取说明）
- fetch（beta）使用fetch而不是xhr请求
（在Chrome中，这会导致xhr.abort，details.timeout和xhr.onprogress不起作用，并使xhr- .onreadystatechange仅接收readyState 4事件）
- 用户名用于验证的用户名
- 密码密码
- 如果请求被中止，将执行onabort回调
- 如果请求以错误结束，则执行onerror回调
- 如果请求已加载，则将执行onload回调
- 如果请求开始加载，onloadstart回调将被执行
- onprogress如果请求取得了一些进展，以执行回调
- 如果请求的就绪状态改变，onreadystatechange回调将被执行
- 如果请求由于超时而失败，则执行ontimeout回调

返回具有以下属性的对象：
- **中止** - 被调用以取消该请求的功能
**注意**：不支持详细信息中的同步标志
**重要提示**：如果您想使用此方法，请检查有关 @connect 的文档。

### GM_download(details), GM_download(url, name)
将给定的URL下载到本地磁盘。
细节可以具有以下属性：
- url - 数据应该从哪里下载的URL
- 名称 - 文件名 - 出于安全原因，文件扩展名需要在Tampermonkey选项页面列入白名单
- 头文件 - 请参阅GM_xmlhttpRequest以获取更多详细信息
- saveAs - 布尔值，显示一个saveAs对话框
- 如果下载最终出错，则执行onerror回调
- 如果下载完成，将执行onload回调
- onprogress如果下载取得了一些进展，以执行回调
- 如果由于超时导致下载失败，将执行ontimeout回调


onerror回调 的下载参数可以具有以下属性： 
- 错误 - 错误原因
	- not_enabled - 下载功能未被用户启用
	- not_whitelisted - 所请求的文件扩展名未列入白名单
	- not_permitted - 用户启用了下载功能，但未授予下载权限
	- not_supported - 浏览器/版本不支持下载功能
	- not_succeeded - 下载未启动或失败，details属性可能提供更多信息
- 细节 - 关于那个错误的细节

返回具有以下属性的对象：
- 中止 - 将被调用以取消此下载的功能

根据下载模式，GM_info提供了一个名为downloadMode的属性，它被设置为以下值之一：**native**，**disabled** 或 **browser**。

### GM_getTab(cb)
只要此选项卡处于打开状态，即可获取持久性对象。

### GM_saveTab(tab)
保存选项卡对象以在页面卸载后重新打开它。

### GM_getTabs(cb)
将所有选项卡对象作为散列与其他脚本实例进行通信。

### GM_notification(details, ondone), GM_notification(text, title, image, onclick)
显示HTML5桌面通知和/或突出显示当前标签。
细节可以具有以下属性：
- 文本 - 通知的文本（如果突出显示已设置，则为可选）
- 标题 - 通知标题（可选）
- 图像 - 图像（可选）
- 突出显示 - 布尔标志是否突出显示发送通知的选项卡（可选）
- 超时 - 通知将被隐藏的时间（可选，0 =禁用）
- ondone - 在通知关闭时调用（无论是由超时还是点击触发）或突出显示（可选）
- onclick - 在用户点击通知的情况下调用（可选）
所有参数都与其对应的详细资料属性吊坠完全相同。

### GM_setClipboard(data, info)
将数据复制到剪贴板。参数'info'可以是一个对象，例如“{type：'text'，mimetype：'text / plain'}”或者只是一个表示类型的字符串（“text”或“html”）。

### GM_info
获取关于脚本和TM的一些信息。该对象可能如下所示：
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
Tampermonkey支持这种存储元数据的方式。TM会尝试自动检测脚本是否需要启用此兼容性选项。
