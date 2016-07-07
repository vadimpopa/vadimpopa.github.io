+++
date = "2013-06-22T17:50:16+03:00"
title = "NVD3 and Raphael as alternative charting libs for Sencha Touch"
description = ""
tags = [
    "javascript",
    "Charts",
    "mobile",
    "Sencha Touch 2"
]
categories = [
    "charts",
    "d3js",
    "mobile",
    "nvd3",
    "raphael",
    "sencha touch 2"
]
+++
Below my short journey in taking a look to NVD3 and Raphael as alternative charting libraries for <a title="Sencha Touch 2" href="http://www.sencha.com/products/touch" target="_blank">Sencha Touch</a>(ST). ST has a powerful and nice charting library which is included as a part of Touch (available as GPLv3 or as a part of Sencha Complete and Complete: Team). Yet I&#8217;ve wanted to try other charts libs with a more flexible license which will allow me to integrate them easily in a ST app and develop a simple line chart ST component.
<!--more-->
<img class="right" src="../../images/raphael_chart-280x200.png" style="display: block;margin: 0 auto; width:280px;height:200px;">
First on the list was <a href="http://raphaeljs.com" target="_blank">Raphael</a> which is part of Sencha Labs. Integration went pretty easy, and with a few lines of code, a simple basic line chart component in ST was ready. Many thanks to the author of this [blog][1] from which I took the Raphael example.

<!-- more -->

``` javascript
/*
 * xtype: 'rchartline',
 * chartData: {
 *   x: [1, 2, 3, 4, 5, 6, 7],
 *   y: [12, 32, 23, 15, 17, 27, 22]
 * }
 */

Ext.define('Ux.chart.RLine',{
    extend: 'Ext.Component',

    xtype: 'rchartline',

    config: {
        chartCfg:  {
           nostroke: false,   // lines between points are drawn
           axis: "0 0 1 1",   // draw axes on the left and bottom
           symbol: "circle",    // use a filled circle as the point symbol
           smooth: true,      // curve the lines to smooth turns on the chart
           dash: "-",         // draw the lines dashed
           colors: [
             "#995555",       // the first line is red
             "#555599"        // the second line is blue
           ]
         },
         chartData: {
          x: [],
          y: []
         }
    },
    initialize: function(){
      this.canvas = Raphael(this.innerElement.createChild({tag: "div", cls: 'raphael-chartWrapper'}).dom);

      this.on('painted',function(){
          var data = this.getChartData();

          this.canvas.linechart(10,10,390,180,data.x,data.y,this.getChartCfg());
      });
    }
});
```

<img class="right" src="../../images/nvd3-280x200.png" style="display: block;margin: 0 auto; width:200px;height:160px;">
Next one was <a href="http://nvd3.org" target="_blank">NVD3</a>, a very nice simple library based on the powerful <a href="http://d3js.org" target="_blank">D3</a>. While NVD3 is not documented, D3 seems very well documented, and since the former is sort of facade to the second, it&#8217;s good to check for examples and documentation on both sites. As you can see form first example you can suppose already that making an ST chart component based on NVD3 is also easy, which is true. The difference is that following example has the implementation of real time update while on Raphael it doesn&#8217;t. That&#8217;s because I was constrained by project time and haven&#8217;t had it enough for looking the best way of how to do it (one of the ideas which worked was a call of canvas.clear() and draw the chart from scratch with linechart method).

``` javascript
Ext.define('Ux.chart.Line',{
    extend: 'Ext.Component',

    xtype: 'chartline',

    initialize: function(){

      nv.addGraph(Ext.bind(function(){
          var chart = nv.models.lineChart();

          chart.xAxis.tickFormat(function (d) {
            return d3.time.format('%x')(new Date(d))
          });

          chart.yAxis.tickFormat(d3.format(',.1%'));

          d3.select(this.innerElement.dom).append('svg');

          nv.utils.windowResize(chart.update);

          this.chart = chart;

          return chart;
      },this));

      this.on('painted',function(){
          this.redraw();

          setInterval(Ext.bind(function () {
            var long = this.getData()[0].values,
                next = new Date(long[long.length - 1].x);

            next.setDate(next.getDate() + 1);
            long.shift();
            long.push({x:next.getTime(), y:Math.random() * 100});

            this.redraw();

          },this), 1500);
      },this)

    },

    redraw: function() {
      d3.select(this.innerElement.down('svg').dom)
            .datum(this.getData())
            .transition().duration(500)
            .call(this.chart);
    },
    generateData: function() {
        var arr = [],
            theDate = new Date(2012, 01, 01, 0, 0, 0, 0);

        for (var x = 0; x &lt; 30; x++) {
            arr.push({x: theDate.getTime(), y: Math.random() * 100});
            theDate.setDate(theDate.getDate() + 1);
        }
        return arr;
    },
    getData: function() {
      return [{
        "key": "Long",
        "values": this.generateData()
      }];
    }
});
```

At first impression D3 seems very powerful in charts drawing and richer than Raphael. Since it gave good results I stopped looking for other libraries and started work on an extension based on NVD3 and above example. For full examples source code and a running demo check my <a href="https://github.com/vadimpopa/Ux.chart.Line" target="_blank">github repo</a>.

My journey stops here and I wish you happy charting.

 [1]: http://www.exratione.com/2011/10/a-few-tips-for-graphael-line-charts/