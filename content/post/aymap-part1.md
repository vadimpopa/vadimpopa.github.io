+++
date = "2013-02-16 22:59:32+03:00"
title = "Inside Aymap, an ExtJS4 web application, part 1"
tags = [
    "javascript",
    "extjs4"
]
categories = [
    "ExtJS 4"
]
+++

**Overview**  
Having more than two years of experience in developing web applications based on Sencha Products, I thought I would start this blog with the first article about an ExtJS4 based application. As on the internet you can find a lot of simple applications and thousands of examples and solutions on how to do ordinary things or how to achieve a behavior, articles about real life applications are a few.

So these posts will cover how to build a real life application, from views to application structure and architecture, ending with application’s specifics. The app itself is called Aymap and takes care of drawing and customizing surveillance maps for a surveillance system called Aylook, which you can find more about on <a title="www.aylook.com" href="http://aylook.com" target="_blank">www.aylook.com</a>
<!--more-->
This articles require some ExtJS knowledge in order to understand the code, as I won&#8217;t add explanations to it except to some parts which may need. So to understand them is necessary to know: ExtJS class system, ExtJS widgets, how to extend a component and the basics of an ExtJS app, thus almost everything what you find in <a title="Sencha" href="http://sencha.com" target="_blank">Sencha</a>&#8216;s guide from the ExtJS4 documentation link.

**Intro**  
When designing an application besides the functional requirements should be considered the quality attributes requirements too as they affect the run-time behavior, system design and the user experience. Performance, flexibility, maintainability, usability and conceptual integrity were the main attributes, for Aymap. ExtJS4 covers all of them in some level, which is one of the reasons why we chose it, but for a full cover, from developer side is needed a good JavaScript knowledge, best ExtJS practices and a solid application architecture.

As is written in sencha guides splitting up the application’s UI into views is the first step of building an app, well of course except the cases when there are some challenging UI parts which you might first want to try to find their solutions, to be sure that the project is feasible, is just as I do usually. The splitting operation should result with a balance between the views, not too granular but at the same time no too generic.

**The Viewport**  
For Aymap I’ve come up with a simple Viewport as you can see in the picture and code:  
{% img center ../images/posts/aymap_viewport.png 580 400 %}

``` javascript app/view/Viewport.js
Ext.define('AyMap.view.Viewport',{

    /* Begin Definitions */

	extend : 'Ext.container.Viewport',

    alias: 'widget.ayviewport',

    requires: [
        'Ext.layout.container.Border',
        'Ext.ux.StatusBar'
    ],

    /* End Definitions */

	layout: 'border',

    /**
     * @property {Boolean} isFullscreen
     * Read-only property indicating whether the viewport is in fullscreen mode or not
     */

	isFullscreen: false,

	initComponent: function(){

        Ext.apply(this,{
        	items: [{
                    xtype: 'aymenu',
                    itemId: 'menu',
                    region: 'north'
                },{
                    xtype: 'aytoolbox',
                    itemId: 'toolbox',
                    region : 'west'
                },{
                    xtype: 'statusbar',
                    itemId: 'statusbar',
                    region: 'south',
                    height:26,
                    defaultText: localizer.get('statusBarDefaultText'),
                    statusAlign: 'right',
                    items: [{
                        xtype: 'component',
                        itemId: 'cmpLeftStatus',
                        cls: 'ay-left-status',
                        tpl: '&lt;span&gt;{text}&lt;/span&gt;'
                    }]
                },{
                    xtype:'mapcontainer',
                    region: 'center'
                }]
        });
		this.callParent(arguments);
	}
});
```

Aymap viewport is consisted from:

*   AyMenu `Ext.toolbar.Toolbar` - menu which covers all Aymap operations on aylook maps
*   AyToolbox `Ext.panel.Panel` &#8211; a container of grids which delivers to user a bunch of aylook objects which are present in that specific aylook system. Objects like: cameras, alarms, diagnostics, inputs, outputs and system buttons and so on.
*   MapContainer `Ext.container.Container` &#8211; as the name says it&#8217;s a container for the current loaded aylook map.
*   StatusBar `Ext.ux.StatusBar` &#8211; it&#8217;s the bottom bar which shows mouse coordinates on map and some status messages.

``` javascript app/view/AyMenu.js

Ext.define('AyMap.view.AyMenu', {

    /* Begin Definitions */
    extend: 'Ext.toolbar.Toolbar',
    alias: 'widget.aymenu',

    requires: [
        'Ext.container.ButtonGroup',
        'Ext.form.field.ComboBox'
    ],
    /* End Definitions */

    collapsible : true,
    height : 69,
    ui : 'aylook',

    initComponent: function () {
        var me = this;

        Ext.apply(me, {
            items: [
                me.getMapsMenuCfg(),
                me.getClipboardMenuCfg(),
                me.getEditMenuCfg(),
                me.getInsertMenuCfg(),
                me.getToolsMenuCfg(),
                me.getSearchFieldCfg()
            ]
        });
        me.callParent(arguments);
    },

    /**
     * Returns config object for Ext.container.ButtonGroup of the Maps menu
     * @private
     */
    getMapsMenuCfg: function(){
        return {
            xtype: 'buttongroup',
            title: localizer.get('titleMap'), //'Map',
            itemId: 'btnGpMaps',
            columns: 3,
            defaults: {
                scale: 'small',
                ui: 'aylook'
            },
            defaultType: 'button',
            items: [{
                text: localizer.get('txtLoad'), //'Load'
                iconCls: 'ay_load_icon',
                itemId: 'mMapsLoad',
                menuHandler: 'onMapLoadClick',
                scale: 'large',
                rowspan: 3,
                disabled: true
            },
                {
                    text: localizer.get('txtNew'), //'New',
                    iconCls: 'ay_new_icon',
                    itemId: 'mMapsNew',
                    menuHandler: 'onMapNewClick'
                },{
                    text: localizer.get('txtProperties'), //'Properties',
                    iconCls: 'ay_prop32_icon',
                    itemId: 'mMapsProperties',
                    menuHandler: 'onMapPropertiesClick',
                    disabled: true,
                    rowspan: 3,
                    scale: 'large'
                },{
                    text: localizer.get('txtDelete'), //'Delete',
                    iconCls: 'ay_delete16_icon',
                    menuHandler: 'onMapDeleteClick',
                    itemId: 'mMapsDelete',
                    disabled: true
                }
            ]
        };
    },
```

If you notice in the `getMapsMenuCfg` more exactly in buttons configurations, there is a custom property called `menuHandler`, it&#8217;s a config which keeps the name of the method to be called and is used usually to simplify and minimise the code. As you can see the menu is very big and has a lot of buttons and to use such approach was very in handy. In controller there is a global listener to buttons clicks of the `AyMenu` which just calls the method stored in `menuHandler` of each button:

``` javascript
// in controller
 me.control({
  'aymenu button':{
      click: function(button){
        this[button.menuHandler].call(this,button);
      }
   }
```

``` javascript app/view/AyToolbox.js

Ext.define('AyMap.view.AyToolbox',{

    /* Begin Definitions */

	extend: 'Ext.panel.Panel',
	alias: 'widget.aytoolbox',

    requires: [
        'Ext.layout.container.Accordion',
        'AyMap.view.toolbox.Panel'
    ],

    /* End Definitions */

    collapsible : true,
    layout :'accordion',
    width : 118,
	collapseMode : 'mini',
	split : true,
    ui : 'aylook',
    title : localizer.get('titleTolbox'),

	initComponent: function(){

    	Ext.apply(this,{
            defaultType : 'toolboxpanel',
			items:[{
                title: localizer.get('titleCameras'),
                itemId: 'gridcamera',
                store: 'Cameras'
            },{
                title: localizer.get('titleInputs'),
                itemId: 'gridinput',
                store: 'Inputs'
            },{
                title: localizer.get('titleOutputs'),
                itemId: 'gridoutput',
                store:  'Outputs'
            },{
                title: localizer.get('titleVariables'),
                itemId: 'gridvariable',
                store: 'Variables'
            },{
                title: localizer.get('titleAlarms'),
                itemId: 'gridburglar_alarms',
                store: 'Alarms'
            }]
    	});

		this.callParent(arguments);
	}
});
```

``` javascript app/view/MapContainer.js

Ext.define('AyMap.view.MapContainer',{

    /* Begin Definitions */

	extend: 'Ext.container.Container',
	alias: 'widget.mapcontainer',

    requires: [
        'AyMap.ux.MapSurface',
        'Ext.ux.layout.Center'
    ],

    /* End Definitions */

	autoScroll: true,
    layout: 'ux.center',
    cls: 'ay-mapcontainer',

    getContextMenu: function(){
        if (!this.ctxMenu) {
            this.ctxMenu = Ext.create('AyMap.view.ContextMenu', {
                id: 'ctxMenu'
            });
        }
        return this.ctxMenu;
    }
});
```

So, that&#8217;s it for the Viewport.  
**Conclusions**  
In this part I have shown how to split application&#8217;s UI in views and how to extend ExtJS components wisely. In the next part I will describe the rest of applications views and add some details about MapSurface component &#8211; application&#8217;s heart.