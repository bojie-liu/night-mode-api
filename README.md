# night-mode-api
Proposal for a Night Mode API

## Abstract
Given the fact that mobile has became the most popular platform in accessing the Internet, browsing under poor light condition is the common theme in our daily life. However, CSS of most websites were designed for well lit environment, like dark text with white background. The contrast leads to uncomfortable experience for user to surf the Internet under such circumstance. This document is to propose a solution which provides a way in which web page can be notified of light conditions from browser and change its CSS accordingly, called Night Mode API.
##Use cases
+ Web page takes control of changes of style when user triggers the day/night mode in browser menu.
+ Web page itself switches the day/night mode and performs changes of style.

##Current Solutions And Problems
This section overviews some top browsers' solutions for night mode browsing.

+ Chrome does not have a built-in implementation. It is supported through extensions. So Chrome for mobile has no night mode supported. Most extensions apply their own CSS to web pages. It is possible that the extensions' CSS would be in conflict with the native ones.
+ Opera mini 10 has night mode feature like night shift on IOS, which only changes screen brightness and blue channel of displaying color.
+ Foxfire implements night mode in confined browsing scenarios, i.e. reader view which modifies color of limited element like text, background, etc..
+ Dolphin implements a general night mode which is enable to override the color of every element in every page, and covers all browsing scenarios. However, it applies the same overriding rules to all of the same kind  elements while ignoring the contents inside the elements, which causes mis-overriding in some cases. The following two screenshots demonstrate it. As we can see, the icons were displayed partly.
![CSS icon in day mode](http://res.imtt.qq.com/qqbrowser_x5/joshliu/night_mode_api/dolphin-day.png) ![CSS icon in night mode](http://res.imtt.qq.com/qqbrowser_x5/joshliu/night_mode_api/dolphin-night.png)
+ The inevitable problem for these browser-side solutions is that browsers can not adapt styles of every web page, thus override all web pages with an universal set of styles in night mode. Then the appearance of web pages becomes unpredictable and out of control from the web developer's point of view. As a consequence, users experience could be bad when browsing in night mode.

##API
Here is to propose a solution which builds connection between web page and browser side. Browser notifies page of "colormodechanged" event when user switches day/night mode in the UI of browser. Then page side calls the registered event listener to perform changes of style by its own CSS and JS.

* The ColorModeChangedEvent IDL:
Noted that the attribute "colortheme" is defined either "normal" or "night" by browser side.
```javascript
[Constructor(DOMString theme)]
interface ColorModeChangedEvent : Event {
    readonly attribute DOMString colortheme;
};
```

* Due to some browsers have implemented browser-side night mode as mentioned above. Web page has to define a special &lt;meta&gt; to inform browser whether it is using the Night Mode API or not:
```javascript
<meta name=”x5-colormode” content=”x5-nightmode”>
```

* (optional) If web page wants to query current "colortheme":
```javascript
window.colortheme
```

* (optional) In case browser takes time in recalculating style, repainting, etc. after the listener was run. A new pseudo-class could be added on some elements and browser would first select the styles in pseudo-class in night mode and performs a quick refreshing. However, the pseudo-class needs to be supported by browser engine.
```javascript
:-x5-colormode-night
```

##Usage
The following is a simple example using Night Mode API. It works in QQBrowser for Android.
```HTML
<html>
<head>
	<meta name="x5-colormode" content="x5-nightmode"/>
	<script>
	window.addEventListener('colormodechanged', function (evt) {
		if (evt.colortheme === 'night') {
			document.documentElement.className += "G-night"
		} else if (evt.colortheme === 'normal') {
			document.documentElement.className -= "G-night"
		}
	});
	</script>
	<style>
		.G-night {background-color:yellow;font:black;}
		.G-night:-x5-colormode-night {background-color:white;font:red;}
	</style>
</head>
<body class="G-night">
	Hello World!
</body>
</html>
```

##Online demo
A popular website([demo url](https://v.html5.qq.com/?ch=001201#p=index&g=1&ch=001201&_t=1480313859053)) also demostrates the Night Mode API. It works only in QQBrowser for Android. The grayer one is in the browser-side night mode. The colorful one is in the page night mode.
![Image of browser-night-mode](http://res.imtt.qq.com/qqbrowser_x5/joshliu/night_mode_api/browser-night.png)
![Image of web-night-mode](http://res.imtt.qq.com/qqbrowser_x5/joshliu/night_mode_api/web-night.png)
