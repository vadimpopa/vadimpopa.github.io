+++
date = "2013-09-23 21:12:06+03:00"
title = "How to add SVG patterns in your ExtJS 4.2.1 apps"
description = "Split large angularjs controllers using the mixin pattern and the $controller service"
tags = [
    "draw",
    "vg patterns",
    "sprite",
    "extjs4"
]
categories = [
    "Charts",
    "Javascript",
    "ExtJS 4",
    "SVG"
]
+++

<img class="right" src="../../images/svg-patterns-380x300.png" style="display: block;margin: 0 auto; width:380;height:300;">

Though aren&#8217;t widely used <a title="http://www.w3.org/TR/SVG/pservers.html" href="http://www.w3.org/TR/SVG/pservers.html" target="_blank">SVG Patterns</a> are very powerful and light. Being vector graphics, a pattern adjusts very well to any screen resolution making the <a title="http://philbit.com/svgpatterns/" href="http://philbit.com/svgpatterns/" target="_blank">background crisp and nice</a>. The only drawback would be is the lack of support on IE8 and lower and yes the lack of support in ExtJS 4.2.1 (and lower) too.

Hopefully next versions of ExtJS will have within its SVG engine the needed piece which would allow us to add and use patterns in our ExtJS web apps. Until then, you can check a demo on this <a title="https://fiddle.sencha.com/#fiddle/i9" href="https://fiddle.sencha.com/#fiddle/i9" target="_blank">sencha fiddle</a> to see how SVG patterns can be added and used along with [Ext.draw][1] package. The demo shows a white background with [subtle dots][2] in a chart and a circle sprite.

 [1]: http://docs.sencha.com/extjs/4.2.1/#!/guide/drawing "http://docs.sencha.com/extjs/4.2.1/#!/guide/drawing"
 [2]: http://philbit.com/svgpatterns/#subtledots "http://philbit.com/svgpatterns/#subtledots"