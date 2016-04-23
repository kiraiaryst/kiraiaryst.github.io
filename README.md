# Website Optimization

To view the website live, open http://kiraiaryst.github.io/.

To see the optimized version of the code, open Website-Optimization-Project/dist/views/.

## Optimizing PageSpeed Score for index.html

Originally the index page had a PageSpeed insights score of 35/100 for mobile and 47/100 for the desktop. To optimize it, the following modifications were made:

1. Eliminated render-blocking CSS:
Added the `media="print"` HTML attribute for the external style sheet for print styles.
Inlined the CSS by including it into the HTML document.

2. Eliminated render-blocking JavaScript:
Added the HTML async attribute to GA script.

3. Resized images which were too large.

4. Added media="none" attribute for fonts.

After making changes the score increased to 94/100 for mobile and 95/100 for desktop.

## Optimizing the Frame Rate

### Frame-rate at 60fps when Scrolling

#### *Reduced the number of pizza elemenets to 31 and changed a for loop to a reverse one.*

```js
//Generates the sliding pizzas when the page loads.
document.addEventListener('DOMContentLoaded', function() {
  var cols = 8;
  var s = 256;
  //decreased the number of pizzas to 31 istead of 200
  for (var i = 31; i--;)
    //testing the for loop in reverse for faster code
  {
    var elem = document.createElement('img');
    elem.className = 'mover';
    elem.src = "images/pizzanew.png";
    elem.basicLeft = (i % cols) * s;
    elem.style.top = (Math.floor(i / cols) * s) + 'px';
    document.querySelector("#movingPizzas1").appendChild(elem);
  }
  updatePositions();
});
```

#### *Reduced Browser Paint Events*
Removed the `elem.style.height` and `elem.style.width` from `document.addEventListener`, resized and used the new pizza image 100px x 100px to prevent the browser from resizing it. 
Made necessary changes in the CSS file (`.mover` property changed to width: 100px;).


#### *Optimized the updatePositions function loops.*

 * Moved the calculation of `document.body.scrollTop / 1250` outside of the loop.
 * Changed `document.querySelectorAll()` to `getElementsByClassName` for faster access of the elements.
 * Used the `items.length` approach to avoid hardcoding the number of iterations.

```js
// Moves the sliding background pizzas based on scroll position
function updatePositions() {
    frame++;
    window.performance.mark("mark_start_frame");
    //changed .querySelectorAll to getElementsByClassName
    var items = document.getElementsByClassName('mover');
    //Declared var phase out of the loop, took "document.body.scrollTop / 1250" to a separate variable.
    var phase;
    var count = 5;
    var scrollT = document.body.scrollTop / 1250;
    for (var i = items.length; i--;)
    //Used the .length approach to avoid hardcoding the number of iterations.
    //testing the for loop in reverse for faster code 
    {
        var phase = Math.sin(scrollT + (i % count));
        items[i].style.left = items[i].basicLeft + 100 * phase + 'px';

    }
```

#### CSS
Added the `-webkit-backface-visibility: hidden; /* Chrome, Safari, Opera */
backface-visibility: hidden;` property to css to define the visibility of the element.

#### *Optimized Animation*
Added `requestAnimationFrame` for `updatePositions` for single reflow and repaint.
Telling the browser that I wish to perform an animation and request that the browser call a specified function to update an animation before the next repaint.

```js
// runs updatePositions on scroll
//Added requestAnimationFrame for updatePositions
window.addEventListener('scroll', function() {
    window.requestAnimationFrame(updatePositions);
});
```

### Time to Resize Pizzas

* Moved `var dx` out of the for loop and selected only the first `.randomPizzaContainer` element in the document (by using `querySelector` instead of `querySelectorAll`).
* Moved var newwidth out of the loop and selected only the first `.randomPizzaContainer` element in the document (by using `querySelector` instead of `querySelectorAll`).
* Created `var changingPizzas` to store all the `.randomPizzaContainer` elements in the document.
* Cached the DOM call into a new variable `var domAccess`.
* Applied the reverse for loop.

```js
function changePizzaSizes(size) {
        var domAccess = document.querySelector(".randomPizzaContainer");
        //Cached the DOM call into a variable.
        var dx = determineDx(domAccess, size);
        //moved the dx out of the for loop
        var newwidth = (domAccess.offsetWidth + dx) + 'px';
        var changingPizzas = document.getElementsByClassName("randomPizzaContainer");
        //declared document.getElementsByClassName("randomPizzaContainer") in a separate variable
        for (var i = changingPizzas.length; i--;) {
            changingPizzas[i].style.width = newwidth;
        }
    }
```
