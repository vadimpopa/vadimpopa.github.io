+++
date = "2013-04-02 22:59:32+03:00"
title = "Inside Aymap, an ExtJS4 web application, part 2"
tags = [
    "javascript",
    "extjs4"
]
categories = [
    "ExtJS 4"
]
+++

In this post will continue Aymap views exploration and share with you some extension tips and tricks that I&#8217;ve used and I think might be useful for you too in writing your ExtJS4 web application.

***AyToolbox***  
If to look in previous part at `AyToolbox` you can see that contains 5 items of `toolboxpanel` `xtype`. This widget does the job for 5 views and has the following define:

<!-- more -->

``` javascript
Ext.define('AyMap.view.toolbox.Panel',{
    extend : 'Ext.grid.Panel',

   // More configs
    initComponent: function(){
        var me = this,
            store;

        Ext.apply(me,{
            // More configs
            columns: [{
                width: 100,
                sortable: true,
                dataIndex: 'name'
            }],
                listeners: {
                    afterrender: function(view){
                        view.getPlugin('gridviewdragdrop').dragZone.getDragText = function() {
                            return this.dragData.records[0].get('name')
                        }

                        Ext.create('Ext.tip.ToolTip', {
                            target: view.el,
                            delegate: view.itemSelector,
                            trackMouse: true,
                            renderTo: Ext.getBody(),
                            listeners: {
                                beforeshow: function(tip) {
                                    tip.update(this.getRecord(tip.triggerElement).get('name'));
                                },
                                scope: view
                            }
                        });
                    }
                }
            }
        });
        me.callParent(arguments);
        store = me.getStore();
        store.guaranteeRange(0, store.pageSize-1);
    }
});
```

Note the best approach on how to add tooltips to grid columns trough delegation, which is much performant than using renderers:

``` javascript
renderers:  function (value, metadata){
        metadata.tdAttr = 'data-qtip=" ' + value + '"';
        return value;
    }
```

While working on this class I&#8217;ve learned that if to use ExtJS Models at maximum, you can simplify views configurations. If you have a few similar views, like I had in *AyToolbox*, but your columns *dataIndexes* are not the same tough have the same goal: to show a description, **then use Model&#8217;s mapping feature**. Below you can see how mapping is done for a few models which are bound to the stores that provide records to those 5 grids within `AyToolbox`:

``` javascript
Ext.define('AyMap.model.Output', {
    extend: 'Ext.data.Model',
    fields: [
        {name: 'name', mapping: 'output_name',  type: 'string'}

    ]
});

Ext.define('AyMap.model.Camera', {
    extend: 'Ext.data.Model',
    fields: [
    	{name: 'name', mapping: 'resource_name',  type: 'string'}
    ]
});
```

***Aymap objects***  
{% img right ../images/posts/map.png 260 180 %}

If you look at an Aylook map(see left image), you can see some white or transparent fancy boxes. Those are aylook objects, each one having a few common styling properties like: colors, transparency, icons, borders; and a few Aylook specific properties: data related to the surveillance system and its devices. **Under those objects actually stands the powerful ExtJS `Ext.Component` and the magic of `Ext.XTemplate`**. Using these nice classes, was easy enough just implement basics object layout/template, define an AbstractObject which covers objects common properties and functionalities and then extended it to get all types of objects Aylook would have &#8211; mission accomplished.

``` javascript
Ext.define('AyMap.ux.AbstractObject', {
    extend: 'Ext.Component',
    //More configs
    createObjTemplate: function(){
      return  new Ext.XTemplate(
        //Objects template
      );
    }
});

Ext.define('AyMap.ux.object.Camera', {
    extend: 'AyMap.ux.AbstractObject',
    //More specific configs
});
```

Another trick I&#8217;ve used here is a factory class with a method which actually simplifies objects creation and decouples objects related code from UI code.

``` javascript
Ext.define('AyMap.util.ObjectFactory', {
    requires: [
        'AyMap.ux.object.Camera',
        'AyMap.ux.object.Zone'
         //More requires
    ],

    singleton: true,

    create: function (type, config, extraData) {
        var object;
        switch (type) {
            case 'camera': {
               // Process configs here, make some changes...then create the object
               object = Ext.create('AyMap.ux.object.Camera', config);
            }
            //More objects
        return object;
    }
});
```

***Properties windows***  
One of the main functionality of the Aymap is to change those objects properties according to user&#8217;s preferences. This is done by a few properties windows which also have some common functionality. When building ExtJS applications you often might have common functionalities, which means that you have to use inheritance &#8211; one of the base core feature provided by ExtJS.The same principle was used here. As you can see in images, all windows have in common the colours fieldset and the positions fields, which means that their code can be wrapped in methods that belong to the parent class:

``` javascript
Ext.define('AyMap.view.Properties', {
    extend: 'Ext.window.Window'

    //Add window configs, example:
    modal: true,
    resizable: false,
    layout: 'fit',
    width: 300,

    initComponent: function () {
        var me = this;

        Ext.apply(me, {
            title : 'Objects properties'
            // Add common configs
        });
        me.callParent();

        //Add common events, for all windows
        me.on('beforeclose', me.onBeforeClose,this);
        me.down('form').on('dirtychange',me.onFormDirtyChange);
    },
    getColoursFildsetCfg: function(){
        return {
            xtype: 'fieldset'
            // more configs
        }
    },
    getPositionsFieldsCfg: function(){
        return {
            xtype: 'container',
            layout: 'column',
            defaultType: 'numberfield',
            defaults: {
                labelWidth: 15,
                minValue: 0,
                columnWidth: 0.50
            },
            items: [{
                fieldLabel: 'x',
                name: 'x'
            },
            {
                fieldLabel: 'y',
                name: 'y'
            }]
        };
    },
    getFormCfg: function(cfg){
        return Ext.apply(cfg,{
            xtype: 'form',
            frame: false,
            trackResetOnLoad: true,
            bodyPadding: 2,
            buttons: []
        });
    }
    // Add other methods and event handlers that are in common for all windows
});
```

Then, in the final class, you can customise form&#8217;s items and add other specific code and configs:

``` javascript
Ext.define('AyMap.view.properties.Camera', {
    extend: 'AyMap.view.Properties',

    initComponent: function () {
        var me = this;

        Ext.apply(me, {
            //Add other specific configs
            items: [me.getFormCfg({
                items: [
                    me.getImgOnFieldCfg(),
                    me.getPositionsFieldsCfg(),
                    me.getCheckboxesCfg(),
                    me.getColorFieldsCfg()
                ]
            })]
        });

        me.callParent();
    }
});
```
{% img ../images/posts/camera.png 260 180 %}
{% img ../images/posts/link.png 260 180 %}
{% img ../images/posts/panel.png 260 180 %}

**Conclusions**  
All tips I&#8217;ve shown you and all other you might come up with can make your code smaller and cleaner. Also by applying them we touched some of the applications quality attributes: performance, flexibility and maintainability. Personally, while writing these posts I&#8217;ve learned a important rule that there&#8217;s always place for optimisations. So, think at least twice to the code, before writing and after, if this is the best solution you come up with.