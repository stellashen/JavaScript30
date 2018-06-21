# My study notes
## 01 - Drum Kit
### JS Code
```js
// associate the sound and letter by using "data-key" property on divs
function playSound(e) {
  // select element with the specified field
  const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);
  const key = document.querySelector(`.key[data-key="${e.keyCode}"]`);
  if (!audio) return;
  audio.currentTime = 0; // rewind to the start
  audio.play();
  key.classList.add('playing');
}

function removeTransition(e) {
  if (e.propertyName !== 'transform') return; // skip if it's not a transform
  this.classList.remove('playing');
}

const keys = document.querySelectorAll('.key');
keys.forEach(key => {
  key.addEventListener('transitionend', removeTransition);
})

window.addEventListener('keydown', playSound);
```
### Takeaways
#### Use `console.log` to find out what's going on
```js
window.addEventListener('keydown', function(e) {
  console.log(e);//You can see keyCode is the number defined in 'data-key'
});
```
<img src="https://github.com/stellashen/JavaScript30/blob/master/01%20-%20JavaScript%20Drum%20Kit/console.png" width="300">

What is "this" inside `function removeTransition(e)`? Use `console.log(this)` to find out:
```js
<div data-key="72" class="key">
  <kbd>H</kbd>
  <span class="sound">ride</span>
</div>
```

#### use querySelector with specified field
for `<audio>` tags and `class="key"`
```js
const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);
const key = document.querySelector(`.key[data-key="${e.keyCode}"]`);
```

#### to play the same sound for multiple times
Use `audio.currentTime = 0`

#### transition
I used to use `setTimeOut` but using `transition` in CSS is more convenient!
```css
.key {
  border: .4rem solid black;
  border-radius: .5rem;
  margin: 1rem;
  font-size: 1.5rem;
  padding: 1rem .5rem;
  transition: all .07s ease;
  /* transition to .playing */
  width: 10rem;
  text-align: center;
  color: white;
  background: rgba(0,0,0,0.4);
  text-shadow: 0 0 .5rem black;
}

.playing {
  transform: scale(1.1);
  border-color: #ffc600;
  box-shadow: 0 0 1rem #ffc600;
}
```

use 'transitionend' and 'animationend' event
```js
key.addEventListener('transitionend', removeTransition);
```
Here we run the `removeTransition` function when the transition ends after .07s.

## 02 - Clock
### JS code
```js
const secondHand = document.querySelector('.second-hand');
const minHand = document.querySelector('.min-hand');
const hourHand = document.querySelector('.hour-hand');

function setDate() {
  const now = new Date();
  const seconds = now.getSeconds();
  const secondsDegrees = (seconds / 60) * 360 + 90; // 90 is the default offset to start from 12 o'clock direction
  secondHand.style.transform = `rotate(${secondsDegrees}deg)`;

  const mins = now.getMinutes();
  const minsDegrees = (mins / 60) * 360 + (seconds / 60) * 6 + 90;
  minHand.style.transform = `rotate(${minsDegrees}deg)`;

  const hours = now.getHours();
  const hoursDegrees = (hours / 12) * 360 + (mins / 60) * 30 + (seconds / 60) * 0.5 + 90;
  hourHand.style.transform = `rotate(${hoursDegrees}deg)`;

}

setInterval(setDate, 1000);
```

### Takeaways
Rotate the hands based on the current time.

By default, it rotates around the center of the element. We want to put the pivot point on the right end => Use `transform-origin`.
```css
.hand {
  transform-origin: 100%;
  /* by default it is 50%*/
}
```

Click the curve symbol to manually adjust the curve for `transition-timing-function`:
<img src="https://github.com/stellashen/JavaScript30/blob/master/02%20-%20JS%20and%20CSS%20Clock/curve.png" width="300">

## 03 - CSS Variables
### html code
```html
<h2>Update CSS Variables with <span class='hl'>JS</span></h2>

<div class="controls">
  <label for="spacing">Spacing:</label>
  <input id="spacing" type="range" name="spacing" min="10" max="200" value="10" data-sizing="px">

  <label for="blur">Blur:</label>
  <input id="blur" type="range" name="blur" min="0" max="25" value="10" data-sizing="px">

  <label for="base">Base Color</label>
  <input id="base" type="color" name="base" value="#ffc600">
</div>
```
### CSS code
```css
:root {
  --base: #ffc600;
  --spacing: 10px;
  --blur: 10px;
}

img {
  padding: var(--spacing);
  background: var(--base);
  filter: blur(var(--blur));
}

.hl {
  color: var(--base);
}
```
### JS code
```js
const inputs = document.querySelectorAll('.controls input');

function handleUpdate() {
  const suffix = this.dataset.sizing || '';
  document.documentElement.style.setProperty(`--${this.name}`, this.value + suffix);
}

inputs.forEach(input => input.addEventListener('change', handleUpdate));
inputs.forEach(input => input.addEventListener('mousemove', handleUpdate));
```

### Takeaways
#### CSS variable
`padding: var(--spacing);` is using the `--spacing` variable.

#### `forEach` method of `NodeList`
Now we can use the `forEach` method of `NodeList` to loop over the HTML elements.
So we don't need to convert the NodeList to an array.
<img src="https://github.com/stellashen/JavaScript30/blob/master/03%20-%20CSS%20Variables/nodelist.png
" width="300">

#### 'change' and 'mousemove' events
```js
inputs.forEach(input => input.addEventListener('change', handleUpdate));
inputs.forEach(input => input.addEventListener('mousemove', handleUpdate));
```
'change' event only triggers the event when you stop moving the bar; 'mousemove' event will keep updating the value while moving.

#### use dataset
```html
<input id="spacing" type="range" name="spacing" min="10" max="200" value="10" data-sizing="px">
```
We can have `data-sizing`, `data-cool`, `data-anyname`... then get access to the value by dataset.anyname
```js
const suffix = this.dataset.sizing || '';
```
For some elements, sizing is undefined, then return ''

## 04 - Array Cardio Day 1
### Takeaways
#### simplify code to one line
example 1:
```js
const fifteen = inventors.filter(inventor => {
  if (inventor.year >= 1500 && inventor.year < 1600) {
    return true; //keep it
  }
});
// simplify code:
const fifteen = inventors.filter(inventor => (inventor.year >= 1500 && inventor.year < 1600));
// () is optional:
const fifteen = inventors.filter(inventor => inventor.year >= 1500 && inventor.year < 1600);
```

example 2:
```js
// 3. Sort the inventors by birthdate, oldest to youngest
const ordered = inventors.sort(function(a, b) {
  if(a.year > b.year) {
    return 1;
  } else {
    return -1;
  }
});
// simplify
const ordered = inventors.sort((a, b) => a.year > b.year ?  1 : -1);
```

#### reduce: intialize undefined parameter
example 1:
```js
// 4. How many years did all the inventors live?
const totalYears = inventors.reduce((total, inventor) => {
  return total + (inventor.passed - inventor.year);
}, 0);
```
If we don't add 0 here, total will be undefined.

example 2:
```js
// 8. Reduce Exercise
// Sum up the instances of each of these
const data = ['car', 'car', 'truck', 'truck', 'bike', 'walk', 'car', 'van', 'bike', 'walk', 'car', 'van', 'car', 'truck' ];
const transportation = data.reduce((obj, item) => {
  if (!obj[item]) {
    obj[item] = 0;
  }
  obj[item]++;
  return obj;
}, {});
```

#### turn NodeList to array if you want to use `map`
```js
// wrong:
const links = category.querySelectorAll('a');
const de = links.map(link => link.textContent);
// correct:
const links = Array.from(category.querySelectorAll('a'));
const de = links.map(link => link.textContent);
```
<img src="https://github.com/stellashen/JavaScript30/blob/master/04%20-%20Array%20Cardio%20Day%201/6.png" width="800">

## 05 - Flex Panel Gallery
### JS code
```js
const panels = document.querySelectorAll('.panel');

function toggleOpen() {
  this.classList.toggle('open');
}

function toggleActive(e) {
  // e.propertyName is "flex-grow" for Chrome/Firefox and "flex" for Safari
  if (e.propertyName.includes('flex')) {
    this.classList.toggle('open-active');
  }
}

panels.forEach(panel => panel.addEventListener('click', toggleOpen));
panels.forEach(panel => panel.addEventListener('transitionend', toggleActive));
```

### Takeaways
#### flex
```css
/* To make children side by side, add to parent: */
.panels {
  display: flex;
}
/* Then there is extra space unused by children. */

/* let children evenly distribute the extra space: */
.panel {
  flex:1
}
```

#### CSS animation using `transform`
```css
/* hide the first child and the last child */
.panel > *:first-child {
  transform: translateY(-100%);
}

.panel > *:last-child {
  transform: translateY(100%);
}

/* show the first child and the last child when class 'open-active' is added */
.panel.open-active > *:first-child {
  transform: translateY(0);
}

.panel.open-active > *:last-child {
  transform: translateY(0);
}
```

#### CSS animation using `transition`
```css
.panel {
  transition:
    font-size 0.7s cubic-bezier(0.61,-0.19, 0.7,-0.11),
    flex 0.7s cubic-bezier(0.61,-0.19, 0.7,-0.11),
    background 0.2s;
  font-size: 20px;
}
.panel.open {
  font-size:40px;
  flex: 5;
}
```

## 06 - Type Ahead
### JS code
```js
const endpoint = 'https://gist.githubusercontent.com/Miserlou/c5cd8364bf9b2420bb29/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json';
const cities = [];
// fetch() returns a promise
// blob.json() returns another promise
// data is an array of 1000 objects: [{...}, {...},...,{...}]
// one of the object is {city: "Wilkes-Barre", state:"Pennsylvania",…}
fetch(endpoint)
  .then(blob => blob.json())
  .then(data => cities.push(...data))

function findMatches(wordToMatch, cities) {
  return cities.filter(place => {
    // here we need to filter places that match the keyword
    const regex = new RegExp(wordToMatch, 'gi');
    // g is global (search scope), i is insensitive (upper or lowercase)
    return place.city.match(regex) || place.state.match(regex)
  });
}

function numberWithCommas(x) {
  return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
}

function displayMatches() {
  const matchArray = findMatches(this.value, cities);
  const html = matchArray.map(place => {
    const regex = new RegExp(this.value, 'gi');
    const cityName = place.city.replace(regex, `<span class="hl">${this.value}</span>`);
    const stateName = place.state.replace(regex, `<span class="hl">${this.value}</span>`);
    return `
      <li>
        <span class="name">${cityName}, ${stateName}</span>
        <span class="population">${numberWithCommas(place.population)}</span>
      </li>
    `;
  }).join('');
  suggestions.innerHTML = html;
}

const searchInput = document.querySelector('.search');
const suggestions = document.querySelector('.suggestions');

searchInput.addEventListener('change', displayMatches);
searchInput.addEventListener('keyup', displayMatches);
```

### Takeaways
#### the spread operator
More examples from https://codeburst.io/javascript-es6-the-spread-syntax-f5c35525f754
```js
var mid = [3, 4];
var arr = [1, 2, ...mid, 5, 6];

console.log(arr); //[1, 2, 3, 4, 5, 6]

var arr = [2, 4, 8, 6, 0];
var max = Math.max(...arr);

console.log(max); //8

// deep copy
var arr = ['a', 'b', 'c'];
var arr2 = [...arr];

arr2.push('d');

console.log(arr);//['a', 'b', 'c']

// string to array
var str = "hello";
var chars = [...str];

console.log(chars); //["h", "e", "l", "l", "o"]
```

## 07 - Array Cardio Day 2
### Takeaways
#### two ways to remove 1 element from index
```js
// remove from original array
comments.splice(index, 1);
// create new array
const newComments = [
  ...comments.slice(0, index),
  ...comments.slice(index + 1)
];
```

## 08 - Fun with HTML5 Canvas
### JS code
```js
const canvas = document.querySelector('#draw');
const ctx = canvas.getContext('2d');
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
ctx.strokeStyle = '#BADA55';
ctx.lineJoin = 'round';
ctx.lineCap = 'round';
ctx.lineWidth = 50;
ctx.globalCompositeOperation = 'multiply';

let isDrawing = false; //flag to tell whether to draw
let lastX = 0;
let lastY = 0;
let hue = 0;
let direction = true;

function draw(e) {
  if (!isDrawing) return; //stop the fn from running when they are not moused down
  console.log(e);
  ctx.strokeStyle = `hsl(${hue}, 100%, 50%)`;
  ctx.beginPath();
  // start from
  ctx.moveTo(lastX, lastY);
  // go to
  ctx.lineTo(e.offsetX, e.offsetY);
  ctx.stroke();
  [lastX, lastY] = [e.offsetX, e.offsetY];
  hue++;
  if (hue >= 360) {
    hue = 0;
  }
  if (ctx.lineWidth >= 100 || ctx.lineWidth <= 1) {
    direction = !direction;
  }
  if (direction) {
    ctx.lineWidth++;
  } else {
    ctx.lineWidth--;
  }
}

canvas.addEventListener('mousemove', draw);
canvas.addEventListener('mousedown', (e) => {
  isDrawing = true;
  [lastX, lastY] = [e.offsetX, e.offsetY];
});
canvas.addEventListener('mouseup', () => isDrawing = false);
canvas.addEventListener('mouseout', () => isDrawing = false);

```

### Takeaways
#### lineJoin and lineCap properties
`ctx.lineJoin = "bevel" || "round" || "miter";`

Example: https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/lineJoin

`ctx.lineCap = "butt" || "round" || "square";`
Example: https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/lineCap

#### hsl rainbow colors
http://mothereffinghsl.com/

```js
let hue = 0;
function draw(e) {
  ctx.strokeStyle = `hsl(${hue}, 100%, 50%)`;
  //...
  hue++;
}
```

#### compositing
`ctx.globalCompositeOperation = 'multiply';` makes a darker color

More options:
https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Compositing

## 09 - Dev Tools Domination
### JS code
```js
const dogs = [{ name: 'Snickers', age: 2 }, { name: 'hugo', age: 8 }];

function makeGreen() {
  const p = document.querySelector('p');
  p.style.color = '#BADA55';
  p.style.fontSize = '50px';
}

// Regular
console.log('hello');

// Interpolated
console.log('Hello I am a %s string!', '💩');

// Styled
console.log('%c I am some great text', 'font-size:50px; background:red; text-shadow: 10px 10px 0 blue')

// warning!
console.warn('Warn!');

// Error :|
console.error('Error!');

// Info
console.info('info');

// Testing
console.assert(1 === 2, 'Wrong!');

// clearing
// console.clear();

// Viewing DOM Elements
const p = document.querySelector('p');
console.log(p);
console.dir(p);

// Grouping together
dogs.forEach(dog => {
  console.group(`${dog.name}`);
  // collapse by default: console.groupCollapsed(`${dog.name}`);
  console.log(`${dog.name} is ${dog.age} years old`);
  console.log(`${dog.name} is ${dog.age * 7} dog years old`);
  console.groupEnd(`${dog.names}`);
});

// counting
console.count('wes');
console.count('wes');
console.count('s');
console.count('wes');
console.count('wes');
console.count('s');
console.count('wes');

// timing
console.time('fetching data');
fetch('https://api.github.com/users/wesbos')
  .then(data => data.json())
  .then(data => {
    console.timeEnd('fetching data');
    console.log(data);
  });

// display in table
console.table(dogs);
```
console:
<img src="https://github.com/stellashen/JavaScript30/blob/master/09%20-%20Dev%20Tools%20Domination/log1.png" width="600">
<img src="https://github.com/stellashen/JavaScript30/blob/master/09%20-%20Dev%20Tools%20Domination/log2.png" width="600">

### Takeaways
#### set break point
<img src="https://github.com/stellashen/JavaScript30/blob/master/09%20-%20Dev%20Tools%20Domination/1.png" width="600">
<img src="https://github.com/stellashen/JavaScript30/blob/master/09%20-%20Dev%20Tools%20Domination/2.png" width="600">

## 10 - Hold Shift and Check Checkboxes
### JS code
```js
const checkboxes = document.querySelectorAll('.inbox input[type="checkbox"]');

let lastChecked;

function handleCheck(e) {
  // check if they had the shift key down
  // AND check that they are checking it
  let inBetween = false;
  if (e.shiftKey && this.checked) {
    checkboxes.forEach(checkbox => {
      if (checkbox === this || checkbox === lastChecked) {
        inBetween = !inBetween;
        // inBetween becomes true at the starting point,
        // and becomes false at the end
      }

      if (inBetween) {
        checkbox.checked = true;
      }
    })
  }
  lastChecked = this;
}

checkboxes.forEach(checkbox => checkbox.addEventListener('click', handleCheck));
```

## 11 - Custom Video Player
### JS code
```js
/* Get Our Elements */
const player = document.querySelector('.player');
const video = player.querySelector('.viewer');
const progress = player.querySelector('.progress');
const progressBar = player.querySelector('.progress__filled');
const toggle = player.querySelector('.toggle');
const skipButtons = player.querySelectorAll('[data-skip]');
const ranges = player.querySelectorAll('.player__slider');

/* Build out functions */
function togglePlay() {
  const method = video.paused ? 'play' : 'pause';
  video[method]();
}

function updateButton() {
  const icon = this.paused ? '►' : '❚ ❚';
  toggle.textContent = icon;
}

function skip() {
 video.currentTime += parseFloat(this.dataset.skip);
}

function handleRangeUpdate() {
  video[this.name] = this.value;
  // name is volumn or playbackRate
}

function handleProgress() {
  const percent = (video.currentTime / video.duration) * 100;
  progressBar.style.flexBasis = `${percent}%`;
}

function scrub(e) {
  const scrubTime = (e.offsetX / progress.offsetWidth) * video.duration;
  video.currentTime = scrubTime;
}

/* Hook up the event listeners */
video.addEventListener('click', togglePlay);
toggle.addEventListener('click', togglePlay);

video.addEventListener('play', updateButton);
video.addEventListener('pause', updateButton);

skipButtons.forEach(button => button.addEventListener('click', skip));

ranges.forEach(range => range.addEventListener('change', handleRangeUpdate));
ranges.forEach(range => range.addEventListener('mousemove', handleRangeUpdate));

video.addEventListener('timeupdate', handleProgress);

let mousedown = false;
progress.addEventListener('click', scrub);
progress.addEventListener('mousemove', (e) => mousedown && scrub(e));
progress.addEventListener('mousedown', () => mousedown = true);
progress.addEventListener('mouseup', () => mousedown = false);
```

### Takeaways
#### conditionally call a method
```js
function togglePlay() {
  const method = video.paused ? 'play' : 'pause';
  video[method]();
}
```
This is the same with:
```js
function togglePlay() {
  if (video.paused) {
    video.play();
  } else {
    video.pause();
  }
}
```

## 12 - Key Sequence Detection
### JS Code
```js
const pressed = [];
const secretCode = 'wesbos';

window.addEventListener('keyup', e => {
  pressed.push(e.key);
  // only keep 6 letters in the pressed array
  //splice(start, deleteCount)
  // here we start from -7 and delete 1, so it will delete the key at index -7
  pressed.splice(-secretCode.length - 1, pressed.length - secretCode.length);
  if (pressed.join('').includes(secretCode)) {
    console.log('DING DING!');
    cornify_add(); //add random stuff to the screen, unicorns, rainbows...
  }
  console.log(pressed);
})
```

## 13 - Slide in on scroll
### JS code
```js
// only run func once every 20ms
  function debounce(func, wait = 20, immediate = true) {
    var timeout;
    return function() {
      var context = this, args = arguments;
      var later = function() {
        timeout = null;
        if (!immediate) func.apply(context, args);
      };
      var callNow = immediate && !timeout;
      clearTimeout(timeout);
      timeout = setTimeout(later, wait);
      if (callNow) func.apply(context, args);
    };
  }

  const sliderImages = document.querySelectorAll('.slide-in');

  function checkSlide() {
    sliderImages.forEach(sliderImage => {
      // window.scrollY: how far down you haved scrolled from the top
      // window.scrollY + window.innerHeight will give us the pixel number of the bottom
      // slide the image in when half of the image is above the window bottom
      const slideInAt = (window.scrollY + window.innerHeight) - sliderImage.height / 2;
      // offsetTop: how far the image top is from the window top
      // imageBottom: bottom of the image
      const imageBottom = sliderImage.offsetTop + sliderImage.height;
      const isHalfShown = slideInAt > sliderImage.offsetTop;
      const isNotScrolledPast = window.scrollY < imageBottom;
      if (isHalfShown && isNotScrolledPast) {
        sliderImage.classList.add('active');
      } else {
        sliderImage.classList.remove('active');
      }
    });
  }

  window.addEventListener('scroll', debounce(checkSlide));

```

### Takeaways
- debounce the scroll function
- use CSS `transform` to create the animation:
```css
.slide-in {
  opacity:0;
  transition:all .5s;
}

.align-left.slide-in {
  transform:translateX(-30%) scale(0.95);
}
.align-right.slide-in {
  transform:translateX(30%) scale(0.95);
}

.slide-in.active {
  opacity:1;
  transform:translateX(0%) scale(1);
}
```

## 14 - JavaScript References VS Copying
This exercise is about object reference and copying.

### Takeaways
#### ways to create a new array (1 level deep copying)
- The new array is a copy of an old array
- The new array has a different reference
```js
const players = ['Wes', 'Sarah', 'Ryan', 'Poppy'];

// one way
const team2 = players.slice();

// or create a new array and concat the old one in
const team3 = [].concat(players);

// or use the new ES6 Spread
const team4 = [...players];

// use Array.from
const team5 = Array.from(players);

// now when we update it, the original one isn't changed
```
#### ways to create a new object (1 level deep copying)
```js
// with Objects
const wes = {
  name: 'Wes',
  age: 100,
  social: {
    twitter: '@wesbos',
    facebook: 'wesbos.developer'
  }
};

const dev = Object.assign({}, wes);

// note it's only 1 level deep copy
// if you change dev.age, wes will not change
// but if you change dev.social.twitter, wes.social.twitter will be changed too

// deep copying
const dev2 = JSON.parse(JSON.stringify(wes));
// if you change dev2.social.twitter, wes.social.twitter will not be changed
// because JSON.stringify(wes) returns a String
```

## 15 - Local Storage
### JS Code
```js
const addItems = document.querySelector('.add-items');
const itemsList = document.querySelector('.plates');
const items = JSON.parse(localStorage.getItem('items')) || [];

function addItem(e) {
  e.preventDefault();
  const text = (this.querySelector('[name=item]')).value;
  const item = {
    text,
    done: false
  };
  // item is the user input
  items.push(item);
  populateList(items, itemsList);
  localStorage.setItem('items', JSON.stringify(items)); // you can only add a String
  this.reset();
  //'this' is a form element, reset() will set the input box to empty again
}

function populateList(plates = [], platesList) {
  platesList.innerHTML = plates.map((plate, i) => {
    return `
      <li>
        <input type="checkbox" data-index=${i} id="item${i}" ${plate.done ? 'checked' : ''} />
        <label for="item${i}">${plate.text}</label>
      </li>
    `;
  }).join('');
}

function toggleDone(e) {
  // e.target can be one of the following two things:
  // <input type="checkbox" data-index=${i} id="item${i}" ${plate.done ? 'checked' : ''} />
  // <label for="item${i}">${plate.text}</label>
  if (!e.target.matches('input')) return; // skip this unless it's an input
  const el = e.target;
  const index = el.dataset.index;
  items[index].done = !items[index].done;
  localStorage.setItem('items', JSON.stringify(items));
  populateList(items, itemsList);
}

addItems.addEventListener('submit', addItem);
itemsList.addEventListener('click', toggleDone);

populateList(items, itemsList);
```

### Takeaways
#### querySelector for `name="item"`
```html
<input type="text" name="item" placeholder="Item Name" required>
```
```js
const text = (this.querySelector('[name=item]')).value;
```
#### conditionally add `checked` for input
```jsx
<input type="checkbox" data-index=${i} id="item${i}" ${plate.done ? 'checked' : ''} />
```
Use CSS to show different content before checked/unchecked item:
```css
.plates input + label:before {
  content: '⬜️';
  margin-right: 10px;
}

.plates input:checked + label:before {
  content: '🌮';
}
```

## 16 - Mouse Move Shadow
### JS code
```js
const hero = document.querySelector('.hero');
const text = hero.querySelector('h1');

function shadow(e) {
  // const width = hero.offsetWidth;
  // const height = hero.offsetHeight;
  const { offsetWidth: width, offsetHeight: height } = hero;
  console.log(width, height);//745, 693
  const walk = 500; // 500px

  let { offsetX: x, offsetY: y } = e;

  // console.log(x, y);

  // this: the element that you listened on - in this case, the hero element
  // e.target: the element that triggers the event, it could be hero or h1
  // when e.target is h1, we need to adjust the values of x and y
  if (this !== e.target) {
    x = x + e.target.offsetLeft;
    y = y + e.target.offsetTop;
  }

  // x: 0 ~ 100
  // xWalk: -50 ~ 50  Purpose of this formula is to get this range.
  const xWalk = Math.round((x / width * walk) - (walk / 2));
  const yWalk = Math.round((y / height * walk) - (walk / 2));

  text.style.textShadow = `
    ${xWalk}px ${yWalk}px 0 rgba(255,0,255,0.7),
    ${xWalk * -1}px ${yWalk}px 0 rgba(0,255,255,0.7),
    ${yWalk}px ${xWalk * -1}px 0 rgba(0,255,0,0.7),
    ${yWalk * -1}px ${xWalk}px 0 rgba(0,0,255,0.7)
  `;
}

hero.addEventListener('mousemove', shadow);
```

### Takeaways
#### destructure an HTMLelement
```js
const hero = document.querySelector('.hero');
const width = hero.offsetWidth;
const height = hero.offsetHeight;
```
same as:
```js
const { offsetWidth: width, offsetHeight: height } = hero;
```
- destructure offsetWidth from hero element, and assign offsetWidth to the new variable width.
- offsetWidth is border-box width: https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetWidth

#### use offsetX and offsetY to get mouse position
`e.offsetX` and `e.offsetY` (`e` is 'mousemove' event)

#### deal with nested elements
When you have nested elements under the element where the event listener is on, event can be triggered by the parent element or the children elements nested inside.

## 17 - Sort Without Articles
### JS code
```js
const bands = ['The Plot in You', 'The Devil Wears Prada', 'Pierce the Veil', 'Norma Jean', 'The Bled', 'Say Anything', 'The Midway State', 'We Came as Romans', 'Counterparts', 'Oh, Sleeper', 'A Skylit Drive', 'Anywhere But Here', 'An Old Dog'];

function strip(bandName) {
  return bandName.replace(/^(a |the |an )/i, '').trim();
  // replace 'a', 'the', 'an' with ''
  // The trim() method removes whitespace from both ends of a string.
}

const sortedBands = bands.sort((a, b) => strip(a) > strip(b) ? 1 : -1);

document.querySelector('#bands').innerHTML =
  sortedBands
    .map(band => `<li>${band}</li>`)
    .join('');

console.log(sortedBands);
```

## 18 - Adding Up Times with Reduce
calculate the total time of videos:
### JS Code
```js
const timeNodes = Array.from(document.querySelectorAll('[data-time]'));
// document.querySelectorAll('[data-time]') is a NodeList
// turn the NodeList into an array of <li>...</li>
const seconds = timeNodes
  .map(node => node.dataset.time)
  .map(timeCode => {
    const [mins, secs] = timeCode.split(':').map(parseFloat);
    return (mins * 60) + secs;
  })
  .reduce((total, videoSeconds) => total + videoSeconds);

// timeCode is a String in the format "5:43"
// timeCode.split(':') will give us ["5", "43"]
// .map(parseFloat) will turn each element in the array to float,
// to get [5, 43]

let secondsLeft = seconds;
const hours = Math.floor(secondsLeft / 3600);
secondsLeft = secondsLeft % 3600;

const mins = Math.floor(secondsLeft / 60);
secondsLeft = secondsLeft % 60;

console.log(hours, mins, secondsLeft);
```

# The original README:

![](https://javascript30.com/images/JS3-social-share.png)

# JavaScript30

Starter Files + Completed solutions for the JavaScript 30 Day Challenge.

Grab the course at [https://JavaScript30.com](https://JavaScript30.com)

## Community #JavaScript30 Content

Feel free to submit a PR adding a link to your own recaps, guides or reviews!

* [Arjun Khode’s blog](http://thesagittariusme.blogspot.com/search/label/JS30) about summaries for each day, including fixed glitches, bugs and extra features
* [Nitish Dayal's Text Guides](https://github.com/nitishdayal/JavaScript30) are great for those who like reading over watching
* [Meredith Underell's](http://meredithunderell.com/tag/javascript30/) Quick Lessons Learned
* [Rowan Weismiller's](http://rowanweismiller.com/blog/javascript-30/) Recaps + Lessons Learned
* [Thorsten Frommen](https://tfrommen.de/tag/javascript-30/) shares how he solved the exercises before viewing the answers
* [Soyaine 写的中文指南](https://github.com/soyaine/JavaScript30)包含了过程记录和难点解释
* [Ayo Isaiah's](https://freshman.tech/archive/#javascript30) Recaps and Lessons Learned
* [Adriana Rios](https://stpcollabr8nlstn.github.io/JavaScript30/) shares her alternative solutions
* [Michael Einsohn](http://30daysofjs.michaeleinsohn.com) publishes each challenge after watching the video once
* [Mike Ekkel](https://medium.com/@mike_ekkel/javascript-30-a-30-day-vanilla-js-challenge-6a733fc9f62c#.9frjtaje9)
* [Akinjide Bankole](https://github.com/akinjide/JS30days) used Node.js with [Jade](http://jadelang.net) to solve the exercises
* [Yusef Habib](https://github.com/yhabib/JavaScript30) lessons and tricks learned, and a [gh-page](https://yhabib.github.io/JavaScript30/) to see working all the mini-projects.
* [Amelie Yeh](https://github.com/amelieyeh/JS30) 30 lessons notes with things I've learned, and those important recaps. and directly view my demos [here](https://amelieyeh.github.io/JS30/) 🇹🇼😄
* [Winar](https://github.com/winar-jin/JavaScript30-Challenge)的JavaScript30天挑战，记录练习过程，重难点和其他的解决方案。🎨
* [Rayhatron](https://rayhatron.github.io/blog/) - walkthroughs, recaps and lessons learned.
* [Andrei Dobra](https://github.com/andreidbr/JS30) Full repo with lessons learned and a [gh-page](https://andreidbr.github.io/JS30/) with all the exercises.
* [从零到壹全栈部落](https://github.com/liyuechun/JavaScript30-liyuechun),春哥发起的从零到壹全栈部落，旨在带领大家一起学习，一起输出，文档化，代码化，中文视频化，全栈部落口号：输出是最好的学习方式。
* [Usmaan Ali's](https://github.com/usyyy/javascript/blob/master/JavaScript30/analysis.md) summary of the technical skills learned from each project. He's also posting them as separate blog posts [here](https://medium.com/@usyyy)
* [Axel](https://github.com/afuh/js30)'s lessons learned and a [showcase](https://afuh.github.io/js30/) with the projects.
* [Chris](https://github.com/dwatow/JavaScript30) 中文實戰，目標描述、過程紀錄。
* [Muhammad D. Ramadhan's](https://miayam.github.io) blog. He shamlesly mixed his personal life with 30 day JavaScript challenge so as to increase his learning retention. He also summarised the challenge on [one single page](https://miayam.github.io/js30). Do not read his blog!
* [Lee Keitel's Blog](https://blog.keitel.xyz/categories/javascript30/) includes summaries of each lesson, what I learned from each one, and my thoughts about the topic taught and using them in the future.
* [Dustin Hsiao](https://github.com/dustinhsiao21/Javascript30-dustin) 包含了各篇介紹、 效果Demo、各篇詳解及記錄過程，附上部分延伸閱讀及[gh-page](https://dustinhsiao21.github.io/Javascript30-dustin/)。
* [GuaHsu](https://github.com/guahsu/JavaScript30) - 紀錄各篇練習過程與心得，並嘗試擴充部分練習，也做了一個包含全部練習的[介紹站](http://guahsu.io/JavaScript30/)🇹🇼
* [Daniela](https://github.com/misslild)'s completed challenges on [GitHub Pages](https://misslild.github.io/WesBos-30day-Coding-challenge/) and [Codepen](https://codepen.io/collection/DapZeP/) :raised_hands: :muscle: :+1:
* [Dmitrii Pashutskii's](https://github.com/guar47) code of all challenges on [GitHub with Pages](https://github.com/guar47/javascript30Summary) and review [blog posts](https://blog.dpashutskii.com/tag/javascript30/)
* [Abid Hasan's](https://github.com/sabidhasan/javascript-30) completion of all challenges. This was awesome!! Learned so much! :+1:
* [Yusong Notes](https://sky172839465.github.io/course/js30) Records Yusong JS 30 days note and demo :star2:

## A note on Pull Requests

These are meant to be 1:1 copies of what is done in the video. If you found a better / different way to do things, great, but I will be keeping them the same as the videos.

The starter files + solutions will be updated if/when the videos are updated.

Thanks!
