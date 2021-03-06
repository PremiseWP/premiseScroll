# premiseScroll

THIS PLUGIN has been moved to [Premise WP](https://github.com/PremiseWP/Premise-WP/blob/master/js/source/premise-scroll.js)

premiseScroll is a jQuery plugin that allows you to easily bind animations to elements in the DOM on scroll. It is extremely flexible and lightweight.

Check out the [demo](http://scroll.premisewp.com/).

Call it the way you are used to, ` $('selector').premiseScroll( { ..options.. } ); `. The `options` argument is an object containing
the criteria that the element that you want to animate should meet before the animation triggers. Current options available are listed below.

```js
$.fn.premiseScroll.defaults = {

    inView: true,  // whether to trigger the event when element comes into view
    offset: 0,     // number of pixels to delay the trigger
    offsetIn: '',  // number of pixels to delay the trigger when the element comes into view. Defaults to offset value
    offsetOut: '', // number to pixels to delay stopping the animation when the element comes out of view. Defaults to offset value

    /**
     * onScroll is called when the node meets the criteria specified in the options
     *
     * @param  {object}  node the element in context.
     * @return {boolean}      true
     */
    onScroll: function( node ) { return true; },
};
```

**Tip:** If you pass `-1` as the value for `offsetOut` the animation will not stop when the element comes out of view.

## Public Methods

To give you all the control you need to create or stop animations, check out these handy public methods and some examples on how to use them.

**directionScrolled:** Returns the direction in which the user scrolled. `down` or `up` or empty string if user has not scrolled yet.
```js
el = $('selector').premiseScroll({...});
el.directionScrolled();
```

**pixelsScrolled:** Returns the amount of pixels scrolled per event.
```js
el = $('selector').premiseScroll({...});
el.pixelsScrolled();
```

**scrolled:** Returns the amount document has scrolled in pixels.
```js
el = $('selector').premiseScroll({...});
el.scrolled();
```

**totalScrolled:** Returns the total amount scrolled (in pixels) since the event first triggered.
```js
el = $('selector').premiseScroll({...});
el.totalScrolled();
```

**scrollStopped:** Returns Boolean `true` or `false`. true if the animation has been stopped using `stopScroll()`.
```js
el = $('selector').premiseScroll({...});
el.scrollStopped();
```

**stopScroll:** prevents the animation from happening again. Sets `scrollStopped()` return value to true.
```js
el = $('selector').premiseScroll({...});
el.stopScroll();
```

**startScroll:** Allows the animation to trigger. Sets `scrollStopped()` return value back to false (its original value).
```js
el = $('selector').premiseScroll({...});
el.startScroll();
```

**browserMobile:** Quickly check if the browser is a mobile browser or not. Returns `true` if browser is mobile, `false` otherwise.
```js
el = $('selector').premiseScroll({...});
el.browserMobile();
```

## Examples

Here are a few examples to get your creativity going on how to use the public methods above.

**Example 1:** Trigger animation for 800 pixels when element comes into view, but delay it by 350 pixels.

```js
var el = $('selector').premiseScroll({
	offset: 350,
	onScroll: function() {
		if ( 800 >= el.totalScrolled() ) {
			// by calling totalScrolled() I get the amount of pixels scrolled
			// starting from when the animation triggered. in this case,
			// when the element came into view minus 350px.
		}
	}
});
```

**Example 2:** Change the background of an element to randomly pick from an array of background images, but do it every 5 pixels scrolled.

```js
var images = ['image1.png', 'image2.png', 'image3.png',...];

var el = $('#selector').premiseScroll({
	onScroll: function() {
		var randInt = getRandomInt( 0, images.length );

		// el.scrolled() returns the amount the document has scrolled. i.e. the equivalent to $('body').scroolTop()
		// animate every 5 pixels
		if ( el.scrolled() % 5 === 0 ) {
			el.css('background-image', 'url('+ images[randInt] +')');
		}
	}
});

// here is the randomInt function ;)
function getRandomInt(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
}
```

**Example 3:** Make elements rotate at different speeds regardless of whether the are in view or not.

```js
var el = $('.selector').premiseScroll({
	inView: false, // setting inView to false will trigger this animation ALWAYS
	onScroll: function() {
		var _a = $(this),
		a      = _a.find('selector_for_a'),
		b      = _a.find('selector_for_b'),
		c      = _a.find('selector_for_c'),
		d      = _a.find('selector_for_d');

		a.css('transform', 'rotate(-'+ ( el.totalScrolled() * 0.25   ) +'deg)');
		b.css('transform', 'rotate(-'+ ( el.totalScrolled() * 0.558  ) +'deg)');
		c.css('transform', 'rotate('+  ( el.totalScrolled() * 0.7399 ) +'deg)');
		d.css('transform', 'rotate('+  ( el.totalScrolled() * 0.373  ) +'deg)');
	}
});
```

**Example 4:** Animate `element-1` when `element-2` comes into view. But once the animation triggers, if the user scrolls up kill it!

```js
var el = $('element-2').premiseScroll({
	onScroll: function(){
		$('element-1').animate({
			// do what you do
		});

		// Kill the animation if user scrolls up
		// directionScrolled() returns a string down or up
		if ( 'up' == el.directionScrolled() ) {
			// this stops the animation from happening again until the page is refreshed
			el.stopScroll();
		}
	}
});
```

**Example 5:** Add infinte scroll to your blog. Load more posts when the user scrolls to the bottom of the page.

```js
// Bind to the footer
var el = $('footer').premiseScroll({
	onScroll: function(){
		loadMorePosts();
		el.stopScroll(); // prevents AJAX request from firing more than once
	}
});

// Make AJAX request
function loadMorePosts() {
	$.ajax({
		url: 'http://go_get_them_posts.com',
		success: function(resp) {
			// display the posts on your page

			// bind the AJAX request again so that when the user scrolls through the new content
			// and gets to the bottom of the page we call for more posts again.
			el.startScroll();
		},
		error: function(resp) {
			// let the user know that therre are no more posts to show
		}
	});

	return false;
}
```

## Changelog

* **1.1.2:**
	* Fixed issue with element posisiont not re-setting properly if the element moves - i.e. content gets loaded via AJAX.

* **1.1.1:**
	* new public method `browserMobile`. Easily start or stop animations on mobile devices. returns `true` or `false`


* **1.1.0:**
	* new `offsetIn` and `offsetOut` options. Allows you to offset the trigger when the element comes into view and when it goes out of view.
	passing `-1` as the value for `offsetOut` will keep the animation going even when the element goes out of view.

* **1.0.1:**
	* new `startScroll` public method.
