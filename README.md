# premiseScroll  

premiseScroll allows you to easily bind animations to elements in the DOM on scroll. It is extremely flexible and lightweight.

Call it the way you are used to, ` $('selector').premiseScroll( { ..options.. } ); `. The `options` argument is an object containing
the criteria that the element that you want to animate should meet before the animation triggers. The two options, currently, are 
`inView` and `offset`.

```js
$.fn.premiseScroll.defaults = {
    
    inView: true,  // whether to trigger the event when element comes into view
    offset: 0,     // number of pixels to delay the trigger when the user scrolls

    // when the node meets the criteria specified above, this function is called
    onScroll: function() { return true; },
}
```  

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

**Example 2:** Change the background of an element to randomly pick from an array of 20 background images, but do it every 5 pixels scrolled.

```js
var el = $('selector').premiseScroll({
	onScroll: function() {
		var randInt = getRandomInt( 1, 20 );
		
		// el.scrolled() returns the amount the document has scrolled. i.e. the equivalent to $('body').scroolTop()
		// animate every 5 pixels
		if ( el.scrolled() % 5 === 0 ) {
			// assuming imaes are all saved in folder /images/ and are titled bg-1.png, bg-2.png, etc.
			$(this).css('background-image', 'url(/images/bg-'+ randInt +'.png)');
		}
	}
});

// here is the randomInt function ;)
function getRandomInt(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
}
```

**Example 3:** Make elements rotate at different speeds regardless of wheter the are in view or not.

```js
var el = $('selector').premiseScroll({
	inView: false, // setting inView to false will trigger this animation ALWAYS
	onScroll: function() {
		var a = $(this).find('selector_for_a'),
		b     = $(this).find('selector_for_b'),
		c     = $(this).find('selector_for_c'),
		d     = $(this).find('selector_for_d');

		a.css('transform', 'rotate(-'+ ( cogs.totalScrolled() * 0.25   ) +'deg)');
		b.css('transform', 'rotate(-'+ ( cogs.totalScrolled() * 0.558  ) +'deg)');
		c.css('transform', 'rotate('+  ( cogs.totalScrolled() * 0.7399 ) +'deg)');
		d.css('transform', 'rotate('+  ( cogs.totalScrolled() * 0.373  ) +'deg)');
	}
});
```

**Example 4:** Animate one `element-1` when `element-2` comes into view. But once the animation triggers, if the user scrolls up kill it!

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