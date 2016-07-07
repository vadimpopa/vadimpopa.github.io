+++
date = "2013-04-29T17:50:16+03:00"
title = "Sencha ViewModel Tips"
description = ""
tags = [
    "mobile",
    "android",
    "hardware acceleration",
    "sencha touch 2",
    "phonegap"
]
categories = [
    "Mobile",
    "Sencha Touch 2"
]
+++

As you can see on Sencha <a title="http://www.sencha.com/products/touch/" href="http://www.sencha.com/products/touch/" target="_blank">site</a>,  Sencha Touch (ST) was developed to take advantage of **hardware acceleration**. In case you don&#8217;t know what hardware acceleration means and if you are starting to work on your first ST application then you should be informed about it as it might affect your app performance and responsiveness. Particularly when using PhoneGap 2.6(2.x) to deploy natively for Android 4.x, because Android default browser still has performance problems with 3D CSS transforms.
<!--more-->
To find more about the meaning of hardware acceleration in mobile technology you can visit these blogs:

*   About hardware acceleration: <a title="http://www.sencha.com/blog/understanding-hardware-acceleration-on-mobile-browsers/" href="http://www.sencha.com/blog/understanding-hardware-acceleration-on-mobile-browsers/" target="_blank">http://www.sencha.com/blog/understanding-hardware-acceleration-on-mobile-browsers/</a>
*   And what means to have it in your app: <a title="http://www.tricedesigns.com/2013/03/11/performance-ux-considerations-for-successful-phonegap-apps/" href="http://www.tricedesigns.com/2013/03/11/performance-ux-considerations-for-successful-phonegap-apps/" target="_blank">http://www.tricedesigns.com/2013/03/11/performance-ux-considerations-for-successful-phonegap-apps</a>

Also I recommend a very good [post][1] about GPU accelerated compositing in Chrome.

Where hardware acceleration is used in ST? You can see it almost everywhere, in some layouts, scroller, list and in animations. As example, the Card layout, basically it works on translate3d. You must be careful with your DOM size, where and how you use the elements which trigger hardware acceleration. And if you deployed your hybrid Android app and you feel low performance and responsiveness then you might **check if you disabled hardware acceleration in PhoneGap&#8217;s Android Manifest**. By disabling, paradoxically it will boost your application to an acceptable level. Note that once is disabled then you will have to stick with flat effects because the 3D transforms, more exactly the Z axe won&#8217;t work &#8211; a needed CSS for such component like the <a title="Coverflow" href="http://twomonkeys.com.ar/lab/cover/" target="_blank">Coverflow</a>.

 [1]: http://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome "http://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome"