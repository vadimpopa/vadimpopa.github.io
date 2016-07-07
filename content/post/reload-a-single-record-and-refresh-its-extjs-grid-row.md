+++
date = "2013-03-09T17:50:16+03:00"
title = "Reload a single record and refresh its ExtJs grid row"
description = ""
tags = [
    "grid",
    "model",
    "extjs4"
]
categories = [
    "ExtJS 4",
    "Javascript"
]
+++
Just gave the answer to this question on <a title="extjs forum" href="http://www.sencha.com/forum/showthread.php?258228-Reloading-a-single-record-droma-grid" target="_blank">extjs4 forum</a> and decided to post it here too by adding a code example. The answer is very simple: just load the record with the new data from the server by using the static method `Ext.data.Model.load(id,config)` then in the successful callback update that record, do a commit if you need, then refresh record&#8217;s node. Below the explanation in code form:
<!--more-->

``` javascript
Ext.define('Writer.Person', {
    extend: 'Ext.data.Model',
    fields: [
        {name: 'id', type: 'int'},
        {name: 'name', type: 'string'}
    ],
    proxy {
      // Config for your proxy, it's a must for model to have a proxy.
    }
});

Writer.Person.load(3, {
    scope: grid,
    failure: function(record, operation) {
        //do something if the load failed
    },
    success: function(record, operation) {
        var store = grid.getStore(),
            recToUpdate = store.getById(3);

         recToUpdate.set(record.getData());

     // Do commit if you need: if the data from
     // the server differs from last commit data
         recordToUpdate.commit();

         grid.getView().refreshNode(store.indexOfId(3));
    },
    callback: function(record, operation) {
        //do something whether the load succeeded or failed
    }
});
```