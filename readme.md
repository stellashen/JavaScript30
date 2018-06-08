
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
### Takeaways
#### lineJoin and lineCap properties
`ctx.lineJoin = "bevel" || "round" || "miter";`

Example: https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/lineJoin

`ctx.lineCap = "butt" || "round" || "square";`
Example: https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/lineCap

Example:

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
