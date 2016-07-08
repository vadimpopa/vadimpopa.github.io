+++
date = "2013-03-03 21:12:06+03:00"
title = " Sencha Touch 2 SQL proxy for SQLite"
description = "Sencha Touch 2 SQL proxy for SQLite"
tags = [
    "proxy",
    "controller",
    "sencha touch 2",
    "SQLite"
]
categories = [
    "Sencha Touch 2",
    "Javascript"
]
+++

Surfing Sencha forums day by day I&#8217;ve come to the conclusion that today many Sencha Touch 2 users still don&#8217;t know about the nice and well written SQL proxy (`Ext.data.proxy.Sql`). Instead of it they use other extensions written by ST users which are easy to find on ST forum. But the default ST proxy also can by used for WebSQL or SQLite with Phonegap plugin and comparing with those extensions has a well written code, is more faster and is part of ST2.

Saying this I&#8217;ve decided to write a few words about how tot use it, only a few as here there&#8217;s nothing more to write. Mainly because actually the SQL proxy is used like the other ST proxies except that it needs an override.
<!--more-->
``` javascript
Ext.define('App.model.MyModel', {
    extend: 'Ext.data.Model',

    config: {
        proxy: {
            type: 'sql'
        }
    }
});
```

The only custom configuration is to include the following override of the default method `getDatabaseObject` which has platform detection (only for iOS, but should be easy to add for Android too): if is not simulator and it&#8217;s a web view then open the database trough Phonegap plugin `sqlitePlugin`, else open it trough overridden method, thus the WebSQL `openDatabase`.

``` javascript
Ext.define('Ext.data.proxy.SQLOverride', {
    override: 'Ext.data.proxy.SQL',

/* //Overridden method
    getDatabaseObject: function() {
        return openDatabase(this.getDatabase(), '1.0', 'Sencha Database', 5 * 1024 * 1024);
    }
*/
    getDatabaseObject: function() {
        var isWebView = false;

        if(Ext.os.is.Android){
            isWebView = /AppName/i.test(navigator.userAgent);
        }
        else
        if (Ext.os.is.iOS && !/simulator/i.test(navigator.platform)) {
            isWebView = /(iPhone|iPad).*AppleWebKit(?!.*Safari)/i.test(navigator.userAgent);
        }

        return isWebView ? sqlitePlugin.openDatabase("data.sqlite") : this.callParent();
    }
 });
```

You can find the Phonegap plugin on github: [https://github.com/brodyspark/PhoneGap-sqlitePlugin-iOS][1]

Also you can check this [blog post][2] for a nice cover.

#### **EDIT(25.03.2013):**

Edited this post to add Android part. In the override just replaced the code of the *getDatabaseObject*. And with the default user agent is impossible to indetify if it&#8217;s a WebView or a browser. To overcome this, in case if you are on Phonegap, as I was, just set you user agent like following:

``` javascript
@Override
    public void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        super.appView.getSettings().setUserAgentString(
        	    super.appView.getSettings().getUserAgentString()
        	    + " "
        	    + getString(R.string.app_name));

        super.loadUrl(Config.getStartUrl());
    }
}
```

 [1]: https://github.com/brodyspark/PhoneGap-sqlitePlugin-iOS "https://github.com/brodyspark/PhoneGap-sqlitePlugin-iOS"
 [2]: http://druckit.wordpress.com/2013/04/02/revisiting-the-sencha-touch-2-2-sql-proxy/