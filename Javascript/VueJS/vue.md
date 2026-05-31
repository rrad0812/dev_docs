# Vue tutorial

[Nazad](../README.md)

## Vue Introduction

Vue is a JavaScript framework. It can be added to an HTML page with a `<script>` tag.

Vue extends HTML attributes with `Directives`, and binds data to HTML with `Expressions`.

### Vue - JavaScript Framework

Vue is a front-end JavaScript framework written in JavaScript.

Similar frameworks to Vue are React and Angular, but Vue is more lightweight and easier to start with.

Vue is distributed as a JavaScript file, and can be added to a web page with a script tag:

```html
<script 
  src="https://unpkg.com/vue@3/dist/vue.global.js"> 
</script>
```

### Why Learn Vue?

- It is simple and easy to use.
- It is able to handle both simple and complex projects.
- Its growing popularity and open-source community support.
- In normal JavaScript we need to write HOW HTML and JavaScript is connected, but in Vue we simply need to make sure that there IS a connection and let Vue take care of the rest.
- It allows for a more efficient development process with a template-based syntax, two-way data binding, and a centralized state management.

If some of these points are hard to understand, don't worry, you will understand at the end of the tutorial.

### The Options API

There are two different ways to write code in Vue:

- The Options API and
- The Composition API.

The underlying concepts are the same for both the Options API and Composition API, so after learning one, you can easily switch to the other.

The Options API is what is written in this tutorial because it is considered to be more beginner-friendly, with a more recognizable structure.

### My first page

We will now learn how we can create our very first Vue web page, in 5 basic steps:

1. Start with a basic HTML file.
2. Add a `<div>` tag with `id="app"` for Vue to connect with.
3. Tell the browser how to handle Vue code by adding a `<script>` tag with a link to Vue.
4. Add a `<script>` tag with the Vue instance inside.
5. Connect the Vue instance to the `<div id="app">` tag.

These steps are described in detail below, with the full code in a 'Try It Yourself' example in the end.

- Step 1: HTML page

  Start with a simple HTML page:
  
  ```html
  <!DOCTYPE html> 
  <html lang="en"> 
  <head> 
    <title>My first Vue page</title> 
  </head> 
  <body> 
  
  </body> 
  </html>
  ```

- Step 2: Add a `<div>`

  Vue needs an HTML element on your page to connect to.
  
  Put a `<div>` tag inside the `<body>` tag and give it an id:
  
  ```html
  <body> 
    <div id="app"></div> 
  </body>
  ```

- Step 3: Add a link to Vue

  To help our browser to interpret our Vue code, add this `<script>` tag:
  
  ```html
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
  ```

- Step 4: Add the Vue instance

  Now we need to add our Vue code.
  
  This is called the Vue instance and can contain data and methods and other things, but now it just contains a message.
  
  On the last line in this `<script>` tag our Vue instance is connected to the `<div id="app">` tag:
  
  ```html
  <div id="app"></div> 
  
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script> 
  
  <script> 
  
    const app = Vue.createApp({ 
      data() { 
        return { 
          message: "Hello World!" 
        } 
      } 
    }) 
  
   app.mount('#app') 
  
  </script> 
  ```

- Step 5: Display 'message' with Text Interpolation

  Finally, we can use text interpolation, a Vue syntax with double curly braces {{ }} as a placeholder for data.
  
  ```html
  <div id="app"> {{ message }} </div> 
  ```
  
  The browser will exchange {{ message }} with the text stored in the 'message' property inside the Vue instance.
  
**Example: My first Vue page!**
  
Test this code with the 'Try it Yourself' button below.

```html
<!DOCTYPE html> 
<html lang="en"> 
<head> 
  <title>My first Vue page</title> 
</head> 
<body> 

  <div id="app"> 
    {{ message }} 
  </div> 

  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script> 

  <script> 
    const app = Vue.createApp({ 
      data() { 
        return { 
          message: "Hello World!" 
        } 
      } 
    }) 

   app.mount('#app') 

  </script> 
</body> 
</html>
```

### Text Interpolation

Text interpolation is when text is taken from the Vue instance to show on the web page.
The browser receives the page with this code inside:

```html
<div id="app"> {{ message }} </div> 
```

Then the browser finds the text inside the 'message' property of the Vue instance and translates the Vue code into this:

```html
<div id="app">Hello World!</div> 
```

### JavaScript in Text Interpolation

Simple JavaScript expressions can also be written inside the double curly braces `{{ }}`.

**Example:**  
Use JavaScript syntax to add a random number to the message inside the div element:

```html
<div id="app"> 
  {{ message }} <br> 
  {{'Random number: ' + Math.ceil(Math.random()*6) }} 
</div> 

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script> 

<script> 

  const app = Vue.createApp({ 
    data() { 
      return { 
        message: "Hello World!" 
      } 
    } 
  }) 

 app.mount('#app') 

</script>
```

### Get Started

This tutorial will teach you the basics of Vue.

You need basic prior experience with HTML, CSS and JavaScript to follow this tutorial.

**Exercise:**  
Use text interpolation to display the 'message' data property value.

```html
<div id="app"> ___ </div>
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        message: "Hello World!"
      }
    }
  })
  app.mount('#app')
</script>
```

## Vue Directives

Vue directives are special HTML attributes with the prefix `v-...` that give the HTML tag extra functionality.

Vue directives connect to the Vue instance to create dynamic and reactive user interfaces.

With Vue, creating responsive pages is much easier and requires less code compared to traditional JavaScript methods.

### Different Vue Directives

The different Vue directives we use in this tutorial are listed below.

| Directive | Details |
| --------- | ------- |
| v-bind | Connects an attribute in an HTML tag to a data variable inside the Vue instance. |
| v-if | Creates HTML tags depending on a condition. Directives v-else-if and v-else are used together with the v-if directive. |
| v-show | Specifies if an HTML element should be visible or not depending on a condition. |
| v-for | Creates a list of tags based on an array in the Vue instance using a for-loop. |
| v-on | Connects an event on an HTML tag to a JavaScript expression or a Vue instance method. We can also define more specifically how our page should react to a certain event by using event-modifiers. |
| v-model | Used in HTML forms with tags like `<form>`, `<input>` and `<button>`. Creates a two way binding between an input element and a Vue instance data property. |

**Example: The v-bind Directive:**

The browser finds what class to connect the `<div>` element to from the Vue instance.

```html
<!DOCTYPE html> 
<html lang="en"> 
<head> 
  <style>
    .pinkBG {
      background-color: lightpink; 
    } 
  </style> 
</head> 
<body> 

  <div id="app"> 
    <div v-bind:class="vueClass"></div> 
  </div> 

  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script> 
  <script> 
    const app = Vue.createApp({ 
      data() { 
        return { 
          vueClass: "pinkBG" 
        } 
      } 
    }) 
    app.mount('#app') 
  </script> 
</body> 
</html>
```

**Note:**  
The example above could be written much simpler without the Vue code, but be patient. The real benefits of Vue can be seen in later examples when we make pages that are responsive.

**Exercise:**  
Fill in the missing part to connect the class attribute to the "myClass" data property.

```html
<p ___:class="myClass"></p>
```

## Vue v-bind Directive

You have already seen that a basic Vue setup consists of a Vue instance and that we can access it from the `<div id="app">` tag with `{{ }}` or the `v-bind` directive.

On this page we will explain the `v-bind` directive in more detail.

### The v-bind Directive

The `v-bind` directive lets us bind an HTML attribute to data in the Vue instance. This makes it easy to change the attribute value dynamically.

**Syntax:**

```html
<div v-bind:[attribute]="[Vue data]"></div> 
```

**Example:**  
The src attribute value of an `<img>` tag is taken from the Vue instance data property 'url':

```html
<img v-bind:src="url"> 
```

### CSS Binding

We can use the `v-bind` directive to do in-line styling and modify classes dynamically. We will show you briefly how to do that in this section, and later in this tutorial, on the CSS Binding page, we will explain this in more detail.

#### Bind style

In-line styling with Vue is done by binding the style attribute to Vue with `v-bind`.
As a value to the `v-bind` directive, we can write a JavaScript object with the CSS property and value:

**Example:**  
The font size depends on the Vue data property 'size'.

```html
<div v-bind:style="{ fontSize: size }"> 
  Text example 
</div> 
```

We can also separate the font size number value from the font size unit if we want to, like this:

**Example:**  
The font size number value is stored the Vue data property 'size'.

```html
<div v-bind:style="{ fontSize: size + 'px' }"> 
  Text example 
</div> 
```

We could also write the CSS property name with CSS syntax (kebab-case) in hyphens, but it is not recommended:

**Example:**  
The CSS property fontSize is referred to as 'font-size'.

```html
<div v-bind:style="{ 'font-size': size + 'px' }"> 
  Text example 
</div> 
```

**Example:**  
The background color depends on the 'bgVal' data property value inside the Vue instance.

```html
<div v-bind:style="{ backgroundColor: 'hsl('+bgVal+',80%,80%)' }"> 
  Notice the background color on this div tag. 
</div> 
```

**Example:**  
The background color is set with a JavaScript conditional (ternary) expression depending on whether the 'isImportant' data property value is 'true' or 'false'.

```html
<div v-bind:style="{ backgroundColor: isImportant ? 'lightcoral' : 'lightgray' }"> 
  Conditional background color 
</div> 
```

#### Bind class

We can use `v-bind` to change the class attribute.

The value of `v-bind:class` can be a variable.

**Example:**  
The class name is taken from the 'className' Vue data property:

```html
<div v-bind:class="className"> 
  The class is set with Vue
</div>
```

The value of `v-bind:class` can also be an object, where the class name will only take effect if it is set to 'true':

**Example:**
The class attribute is assigned or not depending on if the class 'myClass' is set to 'true' or 'false':

```html
<div v-bind:class="{ myClass: true }"> 
  The class is set conditionally to change the background color 
</div> 
```

When the value of `v-bind:class` is an object, the class can be assigned depending on a Vue property:

**Example:**  
The class attribute is assigned depending on the 'isImportant' property, if it is 'true' or 'false':

```html
<div v-bind:class="{ myClass: isImportant }"> 
  The class is set conditionally to change the background color 
</div> 
```

#### Shorthand for v-bind

The shorthand for `v-bind`: is simply `:`.

**Example:**  
Here we just write `:` instead of `v-bind:`:

```html
<div :class="{ impClass: isImportant }"> 
  The class is set conditionally to change the background color 
</div> 
```

We will continue to use v-bind: syntax in this tutorial to avoid confusion.

**Exercise:**  
Provide the missing code so that the class is set equal to the 'className' data property, using a Vue directive shorthand.

```html
<div ___="className">
  The class is set with Vue
</div>
```

## Vue v-if Directive

It is a lot easier to create an HTML element depending on a condition in Vue with the v-if directive than with plain JavaScript.

With Vue you just write the if-statement directly in the HTML element you want to create conditionally. It's that simple.

### Conditional Rendering in Vue

Conditional rendering in Vue is done by using the v-if, v-else-if and v-else directives.

Conditional rendering is when an HTML element is created only if a condition is true, i.e. create the text "In stock" if a variable is 'true', or 'Not in stock' if that variable is 'false'.

**Example:**  
Write different messages depending on whether there are any typewriters in stock or not:

```html
<p v-if="typewritersInStock"> 
  in stock 
</p> 

<p v-else> 
  not in stock 
</p> 
```

### Conditions in Vue

A condition, or "if-statement", is something that is either true or false.
A condition is often a comparison check between two values like in the example above to see if one value is greater than the other.

- We use comparison operators like <, >= or !== to do such checks.
- Comparison checks can also be combined with logical operators such as && or ||.
- Go to our JavaScript tutorial page to find out more about JavaScript comparisons.

We can use the number of typewriters in storage with a comparison check to decide if they are in stock or not:

**Example:**
Use a comparison check to decide whether to write "In stock" or "Not in stock" depending on the number of typewriters in storage.

```html
<p v-if="typewriterCount > 0"> 
  in stock 
</p> 

<p v-else> 
  not in stock 
</p> 
```

### Directives for Conditional Rendering

This overview describes how the different Vue directives used for conditional rendering are used together.

#### Directive Details

- `v-if`: Can be used alone, or with `v-else-if` and/or `v-else`. If the condition inside `v-if` is 'true',  `v-else-if` or `v-else` are not considered.
- `v-else-if`: Must be used after `v-if` or another `v-else-if`. If the condition inside `v-else-if` is 'true', `v-else-if` or `v-else` that comes after are not considered.
- `v-else`: This part will happen if the first part of the if-statement is `false`. Must be placed at the very end of the if-statement, after `v-if` and `v-else-if`.

To see an example with all three directives shown above, we can expand the previous example with `v-else-if` so that the user sees 'In stock', 'Very few left!' or 'Out of stock':

**Example:**  
Use a comparison check to decide whether to write "In stock", "Very few left!" or "Not in stock" depending on the number of typewriters in storage.

```html
<p v-if="typewriterCount>3"> 
  In stock 
</p> 

<p v-else-if="typewriterCount>0"> 
  Very few left! 
</p> 

<p v-else> 
  Not in stock 
</p> 
```

### Use The Return Value from a Function

Instead of using a comparison check with the v-if directive, we can use the 'true' or 'false' return value from a function:

**Example:**  
If a certain text contains the word 'pizza', create a `<p>` tag with an appropriate message. The `includes()` method is a native JavaScript method that checks if a text contain certain words.

```html
<div id="app"> 
  <p v-if="text.includes('pizza')">The text includes the word 'pizza'</p> 
  <p v-else>The word 'pizza' is not found in the text</p> 
</div> 
data() { 
  return { 
    text: 'I like taco, pizza, Thai beef salad, pho soup and tagine.' 
  } 
}
```

The example above can be expanded to show that `v-if` also can create other tags like `<div>` and `<img>` tags:

**Example:**  
If a certain text contains the word 'pizza', create a `<div>` tag with a pizza image and a `<p>` tag with a message. The `includes()` method is a native JavaScript method that check if a text contain certain words.

```html
<div id="app"> 
  <div v-if="text.includes('pizza')"> 
    <p>The text includes the word 'pizza'</p> 
    <img src="img_pizza.svg"> 
  </div> 
  <p v-else>The word 'pizza' is not found in the text</p> 
</div> 

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script> 
<script> 
  const app = Vue.createApp({ 
    data() { 
      return { 
        text: 'I like taco, pizza, Thai beef salad, pho soup and tagine.' 
      } 
    } 
  }) 
  app.mount('#app') 
</script> 
```

Below the example is expanded even more.

**Example:**  
If a certain text contains the word 'pizza' or 'burrito' or none of these words, different images and texts will be created.

```html
<div id="app"> 
  <div v-if="text.includes('pizza')"> 
    <p>The text includes the word 'pizza'</p> 
    <img src="img_pizza.svg"> 
  </div> 
  <div v-else-if="text.includes('burrito')"> 
    <p>The text includes the word 'burrito', but not 'pizza'</p> 
    <img src="img_burrito.svg"> 
  </div> 
  <p v-else>The words 'pizza' or 'burrito' are not found in the text</p> 
</div> 

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script> 
<script> 
  const app = Vue.createApp({ 
    data() { 
      return { 
        text: 'I like taco, pizza, Thai beef salad, pho soup and tagine.' 
      } 
    } 
  }) 
  app.mount('#app') 
</script> 
```

With Vue we can now write code that create elements under certain conditions in a much easier way than with traditional JavaScript.

**Exercise:**  
Fill in the missing part so that Vue toggles the visibility of the `<div>` tag below for us, depending on the 'typewritersInStock' boolean data property.

```html
<div id="app">
  <p ___="typewritersInStock">
    in stock
  </p>
  <p ___>
    not in stock
  </p>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
const app = Vue.createApp({
  data() {
    return {
      typewritersInStock: true
    }
  }
})
app.mount('#app')
</script>
```

## Vue v-show Directive

Learn how to make an element visible or not with `v-show`.

`v-show` is easy to use because the condition is written right in the HTML tag attribute.

### Conditional Visibility

The `v-show` directive hides an element when the condition is 'false' by setting the CSS 'display' property value to 'none'.

After writing `v-show` as an HTML attribute we must give a conditon to decide to have the tag visible or not.

**Syntax:**

```html
<div v-show="showDiv">This div tag can be hidden</div>
```

In the code above, 'showDiv' represents a boolean Vue data property with either 'true' or 'false' as property value. If 'showDiv' is 'true' the div tag is shown, and if it is 'false' the tag is not shown.

**Example:**
Display the `<div>` element only if the showDiv property is set to 'true'.

```html
<div id="app">
  <div v-show="showDiv">This div tag can be hidden</div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        showDiv: true
      }
    }
  })
  app.mount('#app')
</script>
```

### v-show vs. v-if

The difference between `v-show` and `v-if` is that `v-if` creates (renders) the element depending on the condition, but with `v-show` the element is already created, `v-show` only changes its visibility.

Therefore, it is better to use `v-show` when switching visibility of an object, because it is easier for the browser to do, and it can lead to a faster response and better user experience.

A reason for using `v-if` over `v-show` is that `v-if` can be used with `v-else-if` and `v-else`.

In the example below `v-show` and `v-if` are used separately on two different `<div>` elements, but based on the same Vue property. You can open the example and inspect the code to see that `v-show` keeps the `<div>` element, and only sets the CSS display property to 'none', but `v-if` actually destroys the `<div>` element.

**Example:**
Display the two `<div>` elements only if the showDiv property is set to 'true'. If the showDiv property is set to 'false' and we inspect the example page with the browser we can see that the `<div>` element with `v-if` is destroyed, but the `<div>` element with `v-show` has just CSS display property set to 'none'.

```html
<div id="app">
  <div v-show="showDiv">Div tag with v-show</div>
  <div v-if="showDiv">Div tag with v-if</div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        showDiv: true
      }
    }
  })
  app.mount('#app')
</script>
```

**Exercise:**
Fill in the missing part so that Vue toggles the visibility of the `<div>` tag below for us, depending on the 'lightOn' boolean data property.

```html
<div id="app">
  <div id="lightDiv">
    <div ="lightOn"></div>
    <img src="img_lightBulb.svg">
  </div>
  <button v-on:click=" lightOn =! lightOn ">Switch light</button>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        lightOn: false
      }
    }
  })
  app.mount('#app')
</script>
```

## Vue v-for Directive

With normal JavaScript you might want to create HTML elements based on an array. You use a `for-loop`, and inside you need to create the elements, adjust them, and then add each element to your page. And these elements will not react to a change in the array.

With Vue you start with the HTML element you want to create into a list, with `v-for` as an attribute, refer to the array inside the Vue instance, and let Vue take care of the rest. And the elements created with `v-for` will automatically update when the array changes.

### List Rendering

List rendering in Vue is done by using the `v-for` directive, so that several HTML elements are created with a for-loop.

Below are three slightly different examples where `v-for` is used.

**Example:**  
Display a list based on the items of an array.

```html
<ol>
  <li v-for="x in manyFoods">{{ x }}</li>
</ol>
```

### Loop Through an Array

Loop through an array to display different images:

**Example:**  
Show images of food, based on an array in the Vue instance.

```html
<div>
  <img v-for="x in manyFoods" v-bind:src="x">
</div>
```

### Loop Through Array of Objects

Loop through an array of objects and display the object properties:

**Example:**  
Show both images and names of different types of food, based on an array in the Vue instance.

```html
<div>
  <figure v-for="x in manyFoods">
    <img v-bind:src="x.url">
    <figcaption>{{ x.name }}</figcaption>
  </figure>
</div>
```

### Get the index

The index number of an array element can be really useful in JavaScript for-loops. Luckily we can get the index number when using v-for in Vue as well.

To get the index number with v-for we need to give two comma separated words in parentheses: the first word will be the array element, and the second word will be the index of that array element.
Example

Show index number and food name of elements in the 'manyFoods' array in the Vue instance.

```html
<p v-for="(x, index) in manyFoods">
  {{ index }}: "{{ x }}" <br>
</p>
```

We can also display both array element index and information from the array element itself, if the array elements are objects:

**Example:**  
Show both the array element index number, and text from the objects in the 'manyFoods' array.

```html
<p v-for="(x, index) in manyFoods">
  {{ index }}: "{{ x.name }}", url: "{{ x.url }}" <br>
</p>
```

**Exercise:**  
List rendering in Vue can be done so that several HTML elements are created based on an array.

What Vue directive makes this possible?

```html
v-___
```

## Vue Events

Event handling in Vue is done with the `v-on` directive, so that we can make something happen when for example a button is clicked.

Event handling is when HTML elements are set up to run a certain code when a certain event happens.

Events in Vue are easy to use and will make our page truly responsive.

Vue methods are code that can be set up to run when an event happens.

With `v-on` modifiers you can describe in more detail how to react to an event.

### Get started with events

Lets start with an example to show how we can click a button to count moose in a forest.

We need:

- A button
- v-on on the `<button>` tag to listen to the 'click' event
- Code to increase the number of moose
- A property (variable) in the Vue instance to hold the number of moose
- Double curly braces {{}} to show the increased number of moose

**Example:**  
Click the button to count one more moose in the forest. The count property increases each time the button is clicked.

```html
<div id="app">
  <img src="img_moose.jpg">
  <p>{{ "Moose count: " + count }}</p>
  <button v-on:click="count++">Count moose</button>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        count: 0
      }
    }
  })
 app.mount('#app')
</script>
```

**Note:**  
A benefit that comes with Vue is that the number of moose in the `<p>` tag is updated automatically.With plain JavaScript we would need to update the number the user sees with an additional line of code.

### Events

There are lots of events we can use as triggers for running code, among the most common ones are: 'click', 'mouseover', 'mouseout', 'keydown' and 'input'.

For a complete list of events to use you can visit our HTML DOM Events page.

#### 'v-on'

The `v-on` directive allows us to create pages that respond to what the user does.

The Vue `v-on` works by telling the browser what event to listen to, and what to do when that event occurs.

#### Methods

If we want to run more complex code when an event occurs we can put the code in a Vue method and refer to this method from the HTML attribute, like this:

```html
<p v-on:click="changeColor">Click me</p>
```

#### Event Modifiers

In addition to `v-on` and Vue methods we can use something called event modifiers to modify an event so that it for example only happens once after a page is loaded, or modify an event like 'submit' to prevent a form from being submitted.

As we can see, there are three techniques we need to learn about to use events in Vue:

- The Vue `v-on` directive
- Vue methods
- Vue `v-on` modifiers

Click 'Next' to continue this tutorial and learn more about these techniques for event handling.

**Exercise:**  
Fill in the missing field.

In Vue, events are handled with the  directive.

## Vue v-on Directive

Like event handling in plain JavaScript, the `v-on` directive in Vue tells the browser:

- which event to listen to ('click', 'keydown', 'mouseover', etc)
- what to do when that event occurs

Let's take a look at some examples to see how `v-on` can be used with different events and different code to run when these events occur.

**Note:**  
To run more advanced code when an event occurs we need to introduce Vue methods. Learn about Vue methods on the next page in this tutorial.

### onclick Event

The `v-on` directive allows us to perform actions based on specified events.

Use `v-on:click` to perform action when the element is clicked.

**Example:**  
The `v-on` directive is used on the `<button>` tag to listen to the `click` event. When the `click` event occurs the 'lightOn' data property is toggled between 'true' and 'false', making the yellow `<div>` behind the lightbulb visible/hidden.

```html
<div id="app">
  <div id="lightDiv">
    <div v-show="lightOn"></div>
    <img src="img_lightBulb.svg">
  </div>
  <button v-on:click="lightOn = !lightOn">Switch light</button>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        lightOn: false
      }
    }
  })
  app.mount('#app')
</script>
```

### oninput Event

Use `v-on:input` to perform action when the element gets an input, like a keystroke inside a text field.

**Example:**  
Count the number of keystroke for a input text field:

```html
<div id="app">
  <input v-on:input="inpCount++">
  <p>{{ 'Input events occured: ' + inpCount }}</p>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        inpCount: 0
      }
    }
  })
  app.mount('#app')
</script>
```

### mousemove Event

Use `v-on:mousemove` to perform action when the mouse pointer moves over an element.

**Example:**  
Change the background color of a `<div>` element whenever the mouse pointer moves over it:

```html
<div id="app">
  <p>Move the mouse pointer over the box below</p>
  <div v-on:mousemove="colorVal=Math.floor(Math.random()*360)"
       v-bind:style="{backgroundColor:'hsl('+colorVal+',80%,80%)'}">
  </div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        colorVal: 50
      }
    }
  })
  app.mount('#app')
</script>
```

### Use `v-on` in a `v-for` Loop

You can also use the `v-on` directive inside a `v-for` loop.

The items of the array are available for each iteration inside the `v-on` value.

**Example:**  
Display a list based on the food array and add an click event for each item that will use a value from the array item to change the source of an image.

```html
<div id="app">
  <img v-bind:src="imgUrl">
  <ol>
    <li v-for="food in manyFoods" v-on:click="imgUrl=food.url">
      {{ food.name }}
    </li>
  </ol>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        imgUrl: 'img_salad.svg',
        manyFoods: [
          {name: 'Burrito', url: 'img_burrito.svg'},
          {name: 'Salad', url: 'img_salad.svg'},
          {name: 'Cake', url: 'img_cake.svg'},
          {name: 'Soup', url: 'img_soup.svg'}
        ]
      }
    }
  })
  app.mount('#app')
</script>
```

### Shorthand for v-on

The shorthand for 'v-on' is simply '@'.

**Example:**  
Here we just write '@' instead of 'v-on':

```html
<button @:click="lightOn = !lightOn">Switch light</button>
```

We will start using @ syntax a little later in this tutorial.

**Exercise:**  
Complete the code so that the image toggles when the button is clicked.

```html
<template>
  <button ="showImg = !showImg">
    Toggle image
  </button>
  <img src="flower.jpg" alt="flower" v-show="showImg">
</template>

<script>
  export default {
    data() {
      return {
        : true
      }
    }
  }
</script>
```

## Vue Methods

Vue methods are functions that belong to the Vue instance under the `methods` property.

Vue methods are great to use with event handling (v-on) to do more complex things.

Vue methods can also be used to do other things than event handling.

### The Vue `methods` Property

We have already used one Vue property in this tutorial, the 'data' property, where we can store values.

There is another Vue property called 'methods' where we can store functions that belong to the Vue instance. A method can be stored in the Vue instance like this:

```js
const app = Vue.createApp({
  data() {
    return {
      text: ''
    }
  },
  methods: {
    writeText() {
      this.text = 'Hello World!'
    }
  }
})
```

**Tip:**  
We need to write this. as prefix to refer to a data property from inside a method.

To call the 'writeText' method when we click the `<div>` element we can write the code below:

```html
<div v-on:click="writeText"></div>
```

**Example:**  
The `v-on` directive is used on the `<div>` element to listen to the `click` event. When the `click` event occurs the 'writeText' method is called and the text is changed.

```html
<div id="app">
  <p>Click on the box below:</p>
  <div v-on:click="writeText">
    {{ text }}
  </div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        text: ''
      }
    },
    methods: {
      writeText() {
        this.text = 'Hello World!'
      }
    }
  })
  app.mount('#app')
</script>
```

### Call a Method with the Event Object

When an event occurs so that a method is called, the event object is passed with the method by default. This is very convenient because the event object contains a lot of useful data, like for example the target object, the event type, or the mouse position when the `click` or `mousemove` event occurred.

**Example:**  
The `v-on` directive is used on the `<div>` element to listen to the `mousemove` event. When the `mousemove` event occurs the 'mousePos' method is called and the event object is sent with the method by default so we can get the mouse pointer position.

We must use the this. prefix to refer to "xPos" inside the Vue instance data property from the method.

```html
<div id="app">
  <p>Move the mouse pointer over the box below:</p>
  <div v-on:mousemove="mousePos"></div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        xPos: 0,
        yPos: 0
      }
    },
    methods: {
      mousePos(event) {
        this.xPos = event.offsetX
        this.yPos = event.offsetY
      }
    }
  })
  app.mount('#app')
</script>
```

If we expand the example above by just one line, we can also make the background color change based on the mouse pointer position in the x-direction. The only thing we need to add is `v-bind` to change the background-color in the style attribute:

**Example:**  
The difference here from the example above is that the background color is bound to 'xPos' with `v-bind` so that hsl 'hue' value is set equal to 'xPos'.

```html
<div
  v-on:mousemove="mousePos"
  v-bind:style="{backgroundColor:'hsl('+xPos+',80%,80%)'}">
</div>
```

In the example below the event object carries a text from the `<textarea>` tag to make it look like we are writing inside a notebook.

**Example:**  
The `v-on` directive is used on the `<textarea>` tag to listen to the `input` event which occurs whenever there is a change in the text inside the `<textarea>` element.

When the `input` event occurs the 'writeText' method is called and the event object is sent with the method by default so we can get the text from the `<textarea>` tag. The `text` property in the Vue instance is updated by the 'writeText' method. A span element is set up to show the `text` value with the double curly braces syntax, and this is updated automatically by Vue.

```html
<div id="app">
  <textarea v-on:input="writeText" placeholder="Start writing.."></textarea>
  <span>{{ text }}</span>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        text: ''
      }
    },
    methods: {
      writeText(event) {
        this.text = event.target.value
      }
    }
  })
  app.mount('#app')
</script>
```

### Passing Arguments

Sometimes we want to pass an argument with the method when an event occurs.

Lets say you work as a forest ranger, and you want to keep count of moose sightings. Sometimes one or two moose are seen, other times over 10 moose might be seen during a day. We add buttons to count sightings '+1' and '+5', and a '-1' button in case we have counted too many.

In this case we can use the same method for all three buttons, and just call the method with a different number as an argument from the different buttons. This is how we can call a method with an argument:

```html
<button v-on:click="addMoose(5)">+5</button>
```

And this is how the 'addMoose' method looks like:

```js
methods: {
  addMoose(number) {
    this.count = this.count + number
  }
}
```

Lets see how passing an argument with a method works in a full example.

**Example:**  

```html
<div id="app">
  <img src="img_moose.jpg">
  <p>{{ "Moose count: " + count }}</p>
  <button v-on:click="addMoose(+1)">+1</button>
  <button v-on:click="addMoose(+5)">+5</button>
  <button v-on:click="addMoose(-1)">-1</button>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        count: 0
      }
    },
    methods: {
      addMoose(number) {
        this.count+=number
      }
    }
  })
 app.mount('#app')
</script>
```

### Passing both an Argument and The Event Object

If we want to pass both the event object and another argument, there is a reserved name '$event' we can use where the method is called, like this:

```html
<button v-on:click="addAnimal($event, 5)">+5</button>
```

And this is how the method in the Vue instance looks like:

```js
methods: {
  addAnimal(e, number) {
    if(e.target.parentElement.id==="tigers"){
      this.tigers = this.tigers + number
    }
  }
}
```

Now let us look at an example to see how to pass both the event object and another argument with a method.

**Example:**
In this example our method receives both the event object and a text.

```html
<div id="app">
  <img
    src="img_tiger.jpg"
    id="tiger"
    v-on:click="myMethod($event,'Hello')">
  <p>"{{ msgAndId }}"</p>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        msgAndId: ''
      }
    },
    methods: {
      myMethod(e,msg) {
        this.msgAndId = msg + ', '
        this.msgAndId += e.target.id
      }
    }
  })
 app.mount('#app')
</script>
```

### Larger Example

In this example we see that it is possible to use only one method to count three different animals with three different increments for each animal. We achieve this by passing both the event object and the increment number:

**Example:**  
Both the increment size and the event object are passed as arguments with the method when a button is clicked. The reserved word `$event` is used to pass the event object with the method to tell what animal to count.

```html
<div id="app">
  <div id="tigers">
    <img src="img_tiger.jpg">
    <button v-on:click="addAnimal($event,1)">+1</button>
    <button v-on:click="addAnimal($event,5)">+5</button>
    <button v-on:click="addAnimal($event,-1)">-1</button>
  </div>
  <div id="moose">
    <img src="img_moose.jpg">
    <button v-on:click="addAnimal($event,1)">+1</button>
    <button v-on:click="addAnimal($event,5)">+5</button>
    <button v-on:click="addAnimal($event,-1)">-1</button>
  </div>
  <div id="kangaroos">
    <img src="img_kangaroo.jpg">
    <button v-on:click="addAnimal($event,1)">+1</button>
    <button v-on:click="addAnimal($event,5)">+5</button>
    <button v-on:click="addAnimal($event,-1)">-1</button>
  </div>
  <ul>
    <li>Tigers: {{ tigers }} </li>
    <li>Moose: {{ moose }} </li>
    <li>Kangaroos: {{ kangaroos }} </li>
  </ul>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        tigers: 0,
        moose: 0,
        kangaroos: 0
      }
    },
    methods: {
      addAnimal(e,number) {
        if(e.target.parentElement.id==="tigers") {
          this.tigers+=number
        }
        else if(e.target.parentElement.id==="moose") {
          this.moose+=number
        }
        else {
          this.kangaroos+=number
        }
      }
    }
  })
 app.mount('#app')
</script>
```

**Exercise:**

Write the missing code so that the 'writeText' method is called when the `<div>` tag is clicked.

```html
<div id="app">
  <p>Click on the box below:</p>
  <div =>
    {{ text }}
  </div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        text: ''
      }
    },
    : {
      writeText() {
        this. = 'Hello World!'
      }
    }
  })
  app.mount('#app')
</script>
```

## Vue Event Modifiers

Event modifiers in Vue modify how events trigger the running of methods and help us handle events in a more efficient and straightforward way.

Event modifiers are used together with the Vue `v-on` directive, to for example:

- Prevent the default submit behavior of HTML forms (`v-on:submit.prevent`)
- Make sure that an event can only run once after the page is loaded (`v-on:click.once`)
- Specify what keyboard key to use as an event to run a method (`v-on:keyup.enter`)

### How To Modify The v-on Directive

Event modifiers are used to define how to react on an event in more detail.

We use event modifiers by first connecting a tag to an event like we have seen before:

```html
<button v-on:click="createAlert">Create alert</button>
```

Now, to define more specifically that the button click event should only fire one time after the page loads, we can add the .once modifier, like this:

```html
<button v-on:click.once="createAlert">Create alert</button>
```

Here is an example with the `.once` modifier:

**Example:**
The `.once` modifier is used on the `<button>` tag to only run the method the first time the `click` event happens.

```html
<div id="app">
  <p>Click the button to create an alert:</p>
  <button v-on:click.once="createAlert">Create Alert</button>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    methods: {
      createAlert() {
        alert("Alert created from button click")
      }
    }
  })
  app.mount('#app')
</script>
```

**Note:**  
It is also possible to handle an event inside the method instead of using event modifiers, but event modifiers make it a lot easier.

### Different v-on Modifiers

Event modifiers are used in different situations. We can use event modifiers when we listen to keyboard events, mouse click events, and we can even use event modifiers in combination with each other.

The event modifier `.once` can be used after both keyboard and mouse click events.

#### Keyboard Key Event Modifiers

We have three different keyboard event types `keydown`, `keypress`, and `keyup`.

With each key event type, we can specify exactly what key to listen to after a key event occurs. We have `.space`, `.enter`, `.w` and `.up` to name a few.

You can write the key event to the web page, or to the console with console.log(event.key), to find the value of a certain key yourself:

**Example:**  
The keydown keyboard event triggers the 'getKey' method, and the value 'key' from the event object is written to the console and to the web page.

```html
<input v-on:keydown="getKey">
<p> {{ keyValue }} </p>
data() {
  return {
    keyValue = ''
  }
},
methods: {
  getKey(evt) {
    this.keyValue = evt.key
    console.log(evt.key)
  }
}
```

We can also choose to limit the event to happen only when a mouse click or a key press happens in combination with system modifier keys .alt, .ctrl, .shift or .meta. The system modifier key .meta represents the Windows key on Windows computers, or command key on Apple computers.

| Key Modifier |
| ------------ |
| [Vue key alias] |
| .enter |
| .tab |
| .delete |
| .esc |
| .space |
| .up |
| .down |
| .left |
| .right |

- .[letter] - Specify the letter that comes when you press the key. As an example: use the `.s` key modifier to listen to the `S` key.
- .[system modifier key] - `.alt`, `.ctrl`, `.shift` or `.meta`. These keys can be used in combination with other keys, or in combination with mouse clicks.

**Example:**  
Use the `.s` modifier to create an alert when the user writes an `s` inside the `<textarea>` tag.

```html
<div id="app">
  <p>Try pressing the 's' key:</p>
  <textarea v-on:keyup.s="createAlert"></textarea>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    methods: {
      createAlert() {
        alert("You pressed the 'S' key!")
      }
    }
  })
  app.mount('#app')
</script>
```

### Combine Keyboard Event Modifiers

Event modifiers can also be used in combination with each other so that more than one thing must happen simultaneous for the method to be called.

**Example:**  
Use the `.s` and `.ctrl` modifiers in combination to create an alert when `s` and `ctrl` are pressed simultaneously inside the `<textarea>` tag.

```html
<div id="app">
  <p>Try pressing the 's' key:</p>
  <textarea v-on:keydown.ctrl.s="createAlert"></textarea>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    methods: {
      createAlert() {
        alert("You pressed the 'S' and 'Ctrl' keys, in combination!")
      }
    }
  })
  app.mount('#app')
</script>
```

### Mouse Button Modifiers

To react on a mouse click, we can write `v-on:click`, but to specify which mouse button that was clicked, we can use `.left`, `.center` or `.right` modifiers.

Trackpad users: You might need to click with two fingers, or in the lower right hand side of the trackpad on your computer to create a right click.

**Example:**  
Change the background color when a user right-clicks in the `<div>` element:

```html
<div id="app">
  <div v-on:click.right="changeColor"
       v-bind:style="{backgroundColor:'hsl('+bgColor+',80%,80%)'}">
    <p>Press right mouse button here.</p>
  </div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        bgColor: 0
      }
    },
    methods: {
      changeColor() {
        this.bgColor+=50
      }
    }
  })
  app.mount('#app')
</script>
```

Mouse button events can also work in combination with a system modifier key.

**Example:**
Change the background color when a user right-clicks in the `<div>` element in combination with the 'ctrl' key:

```html
<div id="app">
  <div v-on:click.right.ctrl="changeColor"
       v-bind:style="{backgroundColor:'hsl('+bgColor+',80%,80%)'}">
    <p>Press right mouse button here.</p>
  </div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        bgColor: 0
      }
    },
    methods: {
      changeColor() {
        this.bgColor+=50
      }
    }
  })
  app.mount('#app')
</script>
```

The event modifier `.prevent` can be used in addition to the `.right` modifier to prevent the default drop-down menu to appear when we right click.

**Example:**
The drop-down menu is prevented from appearing when you right click to change the background color of the `<div>` element:

```html
<div id="app">
  <div v-on:click.right.prevent="changeColor"
       v-bind:style="{backgroundColor:'hsl('+bgColor+',80%,80%)'}">
    <p>Press right mouse button here.</p>
  </div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        bgColor: 0
      }
    },
    methods: {
      changeColor() {
        this.bgColor+=50
      }
    }
  })
  app.mount('#app')
</script>
```

It would be possible to prevent the drop-down menu from appearing after right click by using event.`preventDefault()` inside the method, but with the Vue `.prevent` modifier the code becomes more readable and easier to maintain.

You can also react on left button mouse clicks in combination with other modifiers, like `click.left.shift`:

**Example:**
Hold the 'shift' keyboard key and press left mouse button on the `<img>` tag to change image.

```html
<div id="app">
  <p>Hold 'Shift' key and press left mouse button:</p>
  <img v-on:click.left.shift="changeImg" v-bind:src="imgUrl">
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        imgUrlIndex: 0,
        imgUrl: 'img_tiger_square.jpeg',
        imgages: [
          'img_tiger_square.jpeg',
          'img_moose_square.jpeg',
          'img_kangaroo_square.jpeg'
        ]
      }
    },
    methods: {
      changeImg() {
        this.imgUrlIndex++
        if(this.imgUrlIndex>=3){
          this.imgUrlIndex=0
        }
        this.imgUrl = this.images[this.imgUrlIndex]
      }
    }
  })
  app.mount('#app')
</script>
```

**Exercise:**
Provide the correct code so that the `<div>` element changes color when right clicked.

Also, add code so that the default drop down menu that appears when you right click a web page, is not shown.

```html
<div id="app">
  <div v-on:click.="changeColor"
      v-bind:style="{backgroundColor:'hsl('+bgColor+',80%,80%)'}">
    <p>Press right mouse button here.</p>
  </div>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        bgColor: 0
      }
    },
    methods: {
      changeColor() {
        this.bgColor+=50
      }
    }
  })
  app.mount('#app')
</script>
```

## Vue Forms

Vue gives us an easy way to improve the user experience with forms by adding extra functionality like responsiveness and form validation.

Vue uses the `v-model` directive when handling forms.

### Our First Form with Vue

Lets start with a simple shopping list example to see how Vue can be used when creating a form.

For more information about forms in HTML, with related tags and attributes, see our HTML Forms tutorial.

1. Add standard HTML form elements:

   ```html
   <form>
     <p>Add item</p>
     <p>Item name: <input type="text" required></p>
     <p>How many: <input type="number"></p>
     <button type="submit">Add item</button>
   </form>
   ```

2. Create the Vue instance with the current item name, number and the shopping list, and use `v-model` to connect our inputs to it.

   ```html
   <div id="app">
     <form>
       <p>Add item</p>
       <p>Item name: <input type="text" required v-model="itemName"></p>
       <p>How many: <input type="number" v-model="itemNumber"></p>
       <button type="submit">Add item</button>
     </form>
   </div>
   
   <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
   <script>
     const app = Vue.createApp({
       data() {
         return {
           itemName: null,
           itemNumber: null,
           shoppingList: [
             { name: 'Tomatoes', number: 5 }
           ]
         }
       }
     })
     app.mount('#app')
   </script>
   ```

3. Call the method to add an item to the shopping list, and prevent the default browser refresh on submit.

   ```html
   <form v-on:submit.prevent="addItem">
   ```

4. Create the method that adds the item to the shopping list, and clears the form:

   ```js
   methods: {
     addItem() {
       let item = {
         name: this.itemName,
         number: this.itemNumber
         }
       this.shoppingList.push(item);
       this.itemName = null
       this.itemNumber = null
     }
   }
   ```

5. Use `v-for` to show an automatically updated shopping list below the form:

   ```html
   <p>Shopping list:</p>
   <ul>
     <li v-for="item in shoppingList">{{item.name}}, {{item.number}}</li>
   </ul>
   ```

Below is the final code for our first Vue form.

**Example:**
In this example we can add new items to a shopping list.

```html
<div id="app">
  <form v-on:submit.prevent="addItem">
    <p>Add item</p>
    <p>Item name: <input type="text" required v-model="itemName"></p>
    <p>How many: <input type="number" v-model="itemNumber"></p>
    <button type="submit">Add item</button>
  </form>

  <p>Shopping list:</p>
  <ul>
    <li v-for="item in shoppingList">{{item.name}}, {{item.number}}</li>
  </ul>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        itemName: null,
        itemNumber: null,
        shoppingList: [
          { name: 'Tomatoes', number: 5 }
        ]
      }
    },
    methods: {
      addItem() {
        let item = {
          name: this.itemName,
          number: this.itemNumber
        }
        this.shoppingList.push(item)
        this.itemName = null
        this.itemNumber = null
      }
    }
  })
  app.mount('#app')
</script>
```

Notice the two-way binding `v-model` provides in the example above:

- `v-model` updates the Vue instance data when the HTML input change
- `v-model` also updates the HTML input when the Vue instance data changes

To learn more about `v-model` and see more form examples, click 'Next'.

## Vue v-model Directive

Compared to normal JavaScript, it is easier to work with forms in Vue because the `v-model` directive connects with all types of input elements in the same way.

`v-model` creates a link between the input element value attribute and a data value in the Vue instance. When you change the input, the data updates and when the data changes, the input updates as well (two-way binding).

### Two-way Binding

As we have already seen in the shopping list example on the previous page, `v-model` provides us with a two-way binding, meaning that the form input elements update the Vue data instance, and a change in the Vue instance data updates the inputs.

The example below also demonstrates the two-way binding with `v-model`.

**Example:**
Two-way binding: Try to write inside the input field to see that the Vue data property value gets updated. Try also to write directly in the code to change the Vue data property value, run the code, and see how the input field is updated.

```html
<div id="app">
  <input type="text" v-model="inpText">
  <p> {{ inpText }} </p>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        inpText: 'Initial text'
      }
    }
  })
  app.mount('#app')
</script>
```

**Note:** The `v-model` two-way binding functionality could actually be achieved with a combination of `v-bind:value` and `v-on:input`:

- `v-bind:value` to update the input element from the Vue instance data, and
- `v-on:input` to update the Vue instance data from the input.

But `v-model` is much easier to use so that is what we will do.

### A Dynamic Checkbox

We add a checkbox to our shopping list on the previous page to mark if an item is important or not.

Next to the checkbox we add a text that always reflects the current 'important' status, changing dynamically between 'true' or 'false'.

We use `v-model` to add this dynamic checkbox and text to improve user interaction.

We need:

- a boolean value in the Vue instance data property called 'important'
- a checkbox where the user can check if the item is important
- a dynamic feedback text so that the user can see if the item is important

Below is how the 'important' feature looks, isolated from the shopping list.

**Example:**
The checkbox text is made dynamic so that the text reflects the current checkbox input value.

```html
<div id="app">
  <form>
    <p>
      Important item?
      <label>
        <input type="checkbox" v-model="important">
        {{ important }}
      </label>
    </p>
  </form>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
       important: false
      }
    }
  })
  app.mount('#app')
</script>
```

Let's include this dynamic feature in our shopping list example.
Example

```html
<div id="app">
  <form v-on:submit.prevent="addItem">
    <p>Add item</p>
    <p>Item name: <input type="text" required v-model="itemName"></p>
    <p>How many: <input type="number" v-model="itemNumber"></p>
    <p>
      Important?
      <label>
        <input type="checkbox" v-model="itemImportant">
        {{ important }}
      </label>
    </p>
    <button type="submit">Add item</button>
  </form>
  <hr>
  <p>Shopping list:</p>
  <ul>
    <li v-for="item in shoppingList">{{item.name}}, {{item.number}}</li>
  </ul>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        itemName: null,
        itemNumber: null,
        important: false,
        shoppingList: [
          { name: 'Tomatoes', number: 5, important: false }
        ]
      }
    },
    methods: {
      addItem() {
        let item = {
          name: this.itemName,
          number: this.itemNumber
          important: this.itemImportant
        }
        this.shoppingList.push(item)
        this.itemName = null
        this.itemNumber = null
        this.itemImportant = false
      }
    }
  })
  app.mount('#app')
</script>
```

### Mark Found Items in The Shopping List

Let's add functionality so that items added to the shopping list can be marked as found.

We need:

- the list items to react on click
- to change the status of the clicked item to 'found', and use this to visually move the item away and strike it through with CSS

We create one list with all items we need to find, and one list below with items found striked through. We can actually put all the items in the first list, and all the items in the second list, and just use `v-show` with the Vue data property 'found' to define whether to show the item in the first or second list.

**Example:**
After adding items to the shopping list we can pretend to go shopping, clicking the items away after finding them. If we click an item by mistake we can take it back to the 'not found' list by clicking the item once more.

```html
<div id="app">
  <form v-on:submit.prevent="addItem">
    <p>Add item</p>
    <p>Item name: <input type="text" required v-model="itemName"></p>
    <p>How many: <input type="number" v-model="itemNumber"></p>
    <p>
      Important?
      <label>
        <input type="checkbox" v-model="itemImportant">
        {{ important }}
      </label>
    </p>
    <button type="submit">Add item</button>
  </form>

  <p><strong>Shopping list:</strong></p>
  <ul id="ulToFind">
    <li v-for="item in shoppingList"
        v-bind:class="{ impClass: item.important }"
        v-on:click="item.found=!item.found"
        v-show="!item.found">
          {{ item.name }}, {{ item.number}}
    </li>
  </ul>
  <ul id="ulFound">
    <li v-for="item in shoppingList"
        v-bind:class="{ impClass: item.important }"
        v-on:click="item.found=!item.found"
        v-show="item.found">
          {{ item.name }}, {{ item.number}}
    </li>
  </ul>

</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        itemName: null,
        itemNumber: null,
        important: false,
        shoppingList: [
          { name: 'Tomatoes', number: 5, important: false, found: false }
        ]
      }
    },
    methods: {
      addItem() {
        let item = {
          name: this.itemName,
          number: this.itemNumber,
          important: this.itemImportant,
          found: false
        }
        this.shoppingList.push(item)
        this.itemName = null
        this.itemNumber = null
        this.itemImportant = false
      }
    }
  })
  app.mount('#app')
</script>
```

### Use `v-model` to make The Form Itself Dynamic

We can make a form where the customer orders from a menu. To make it easy for the customer, we only present the drinks to choose from after the customer chooses to order drinks. This is can be argued to be better than presenting the customer with all items from the menu at once. In this example we use `v-model` and `v-show` to make the form itself dynamic.

We need:

- A form, with relevant input tags and 'Order' button.
- Radio-buttons to select 'Dinner', 'Drink' or 'Dessert'.
- After category is chosen, a dropdown menu appears with all the items in that category.
- When an item is chosen you see an image of it, you can choose how many and add it to the order.

The form is reset when the item is added to the order.

**Example:**
This form is dynamic. It changes based on user choices. The user must first choose category, then product and how many, before the order button becomes visible and the user can order it.

**Exercise:**
Provide the correct code so that the default browser refresh on submit is prevented.

Also, provide code so that the input field values get a two-way binding to the Vue data instance properties 'itemName' and 'itemNumber'.

```html
<form v-on:="addItem">
  <p>Add item</p>
  <p>
    Item name: 
    <input type="text" required ="itemName">
  </p>
  <p>
    How many: 
    <input type="number" ="itemNumber">
  </p>
  <button type="submit">Add item</button>
</form>
```

## Vue CSS Binding

Learn more about how to use `v-bind` to modify CSS with the style and class attributes.

While the concept to change the style and class attributes with `v-bind` is fairly straight forward, the syntax might need some getting used to.

### Dynamic CSS in Vue

You have already seen how we can use Vue to modify CSS by using `v-bind` on the `style` and `class` attributes. It has been explained briefly in this tutorial under `v-bind` and several examples with Vue changing CSS has also been given.

Here we will explain in more detail how CSS can be changed dynamically with Vue. But first lets look at two examples with techniques we have already seen in this tutorial: in-line styling with `v-bind:style` and assigning a class with `v-bind:class`.

### Inline Styling

We use `v-bind:style` to do in-line styling in Vue.

**Example:**
An `<input type="range">` element is used to change the opacity of a `<div>` element with the use of in-line styling.

```html
<input type="range" v-model="opacityVal">
<div v-bind:style="{ backgroundColor: 'rgba(155,20,20,'+opacityVal+')' }">
  Drag the range input above to change opacity here.
</div>
```

### Assign a Class

We use v-bind:class to assign a class to an HTML tag in Vue.

**Example:**
Select images of food. Selected food is highlighted with the use of v-bind:class to show what you have selected.

```html
<div v-for="(img, index) in images">
  <img v-bind:src="img.url"
       v-on:click="select(index)"
       v-bind:class="{ selClass: img.sel }">
</div>
```

### Other Ways to Assign Classes and Style

Here are different aspects regarding the use of v-bind:class and v-bind:style that we have not seen before in this tutorial:

- When CSS classes are assigned to an HTML tag with both class="" and v-bind:class="" Vue merges the classes.
- An object containing one or more classes is assigned with v-bind:class="{}". Inside the object one or more classes might be toggled on or off.
- With in-line styling (v-bind:style) camelCase is preferred when defining a CSS property, but 'kebab-case' can also be used if it is written inside quotes.
- CSS classes can be assigned with arrays / with array notation / syntax

These points are explained in more detail below.

1. Vue Merges 'class' And 'v-bind:class'

   In cases when an HTML tag belongs to a class assigned with class="", and is also assigned to a class with v-bind:class="", Vue merges the classes for us.
   Example

   A `<div>` element belongs to two classes: 'impClass' and 'yelClass'. The 'important' class is set the normal way with the class attribute, and 'yellow' class is set with `v-bind:class`.

   ```html
   <div class="impClass" v-bind:class="{yelClass: isYellow}">
     This div belongs to both 'impClass' and 'yelClass'.
   </div>
   ```

2. Assign More Than One Class With 'v-bind:class'

   When assigning an HTML element to a class with v-bind:class="{}", we can simply use comma to separate and assign multiple classes.

   **Example:**
   A `<div>` element can belong to both 'impClass' and 'yelClass' classes, depending on the boolean Vue data properties 'isYellow' and 'isImportant'.

   ```html
   <div v-bind:class="{yelClass: isYellow, impClass: isImportant}">
     This tag can belong to both the 'impClass' and 'yelClass' classes.
   </div>
   ```

3. Camel case vs kebab case notation with `v-bind:style`
   When modifying CSS in Vue with in-line styling (`v-bind:style`), it is recommended to use camel Case notation for the CSS property, but 'kebab-case' can also be used if the CSS property is inside quotes.

   **Example:**
   Here, we set CSS properties background-color and font-weight for a `<div>` element in two different ways: the recommended way with camel Case backgroundColor, and the not recommended way with 'kebab-case' in quotes 'font-weight'. Both alternatives work.

   ```html
   <div v-bind:style="{ backgroundColor: 'lightpink', 'font-weight': 'bolder' }">
     This div tag has pink background and bold text.
   </div>
   ```

   'Camel case' and 'kebab case' notation are ways of writing a series of words without space or punctuation.

   - Camel case notation is when the first word starts with a small letter, and every word after starts with a capital letter, like 'backgroundColor' or 'camelCaseNotation'. It is called camel case because we can imagine every capital letter resembling a hump on a camels back.
   - Kebab case notation is when the words are separated with a dash -, like 'background-color' or 'kebab-case-notation'. It is called kebab case because we can imagine the dashes resembling the skewer in a 'shish kebab'.

4. Array Syntax with 'v-bind:class'

   We can use array syntax with v-bind:class to add multiple classes. With array syntax we can use both classes that depend on a Vue property and classes that do not depend on a Vue property.

   **Example:**
   Here, we set CSS classes 'impClass' and 'yelClass' with array syntax. The class 'impClass' depends on a Vue property isImportant and the class 'yelClass' is always attached to the `<div>` element.

   ```html
   <div v-bind:class="[{ impClass: isImportant }, 'yelClass' ]">
     This div tag belongs to one or two classes depending on the isImportant property.
   </div> 
   ```

## Vue Computed Properties

Computed properties are like data properties, except they depend on other properties.

Computed properties are written like methods, but they do not accept any input arguments.

Computed properties are updated automatically when a dependency changes, while methods are called on when something happens, like with event handling for example.

Computed properties are used when outputting something that depends on something else.

### Computed Properties are Dynamic

The big advantage with a computed property is that it is dynamic, meaning it changes depending on for example the value of one or more data properties.

Computed properties is the third configuration option in the Vue instance that we will learn. The first two configuration options we have already looked at are 'data' and 'methods'.

As with 'data' and 'methods' computed properties also has a reserved name in the Vue instance: 'computed'.

**Syntax:**

```js
const app = Vue.createApp({
  data() {
    ...
  },
  computed: {
    ...
  },
  methods: {
    ...
  }
})
```

### Computed Property 'yes' or 'no'

Let's say we want a form to create items in a shopping list, and we want to mark if a new item is important or not. We could add a 'true' or 'false' feedback when the checkbox gets checked, like we have done in an example before:

**Example:**
An input element is made dynamic so that the text reflects the status.

```html
<input type="checkbox" v-model="chbxVal"> {{ chbxVal }}
data() {
  return {
    chbxVal: false
  }
}
```

However, if you you ask someone if something is important, they will most likely answer 'yes' or 'no' instead of 'true' or 'false'. So to make our form more fitting with normal language (more intuitive) we should have 'yes' or 'no' as feedback on the checkbox instead of 'true' or 'false'.

And guess what, a computed property is a perfect tool to help us with that.

**Example:**
With a computed property 'isImportant' we can now customize the text feedback to the user when the checkbox is toggled on and off.

```html
<input type="checkbox" v-model="chbxVal"> {{ isImportant }}
data() {
  return {
    chbxVal: false
  }
},
computed: {
  isImportant() {
    if(this.chbxVal){
      return 'yes'
    }
    else {
      return 'no'
  }
}
```

### Computed Properties vs. Methods

Computed properties and methods are both written as functions, but they are different:

- Methods runs when called from HTML, but computed properties updates automatically when a dependency change.
- Computed properties are used the same way we use data properties, but they are dynamic.

Vue Exercises
Test Yourself With Exercises
Exercise:

Provide the correct code so that the 'isImportant' computed property is shown on the screen.

```html
<div id="app">
  <form>
    <p>
      Important item?
      <label>
         <input type="checkbox" v-model="chbxVal"> 
         
      </label>
    </p>
  </form>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    data() {
      return {
        chbxVal: false
      }
    },
    : {
      isImportant() {
        if(this.chbxVal){
          return 'yes'
        }
        else {
          return 'no'
        }
      }
    }
  })
 app.mount('#app')
</script>
```

## Vue Watchers

A watcher is a method that watches a data property with the same name.

A watcher runs every time the data property value changes.

Use a watcher if a certain data property value requires an action.

### The Watcher Concept

Watchers is the fourth configuration option in the Vue instance that we will learn. The first three configuration options we have already looked at are 'data', 'methods' and 'computed'.

As with 'data', 'methods' and 'computed' watchers also has a reserved name in the Vue instance: 'watch'.

**Syntax:**

```js
const app = Vue.createApp({
  data() {
    ...
  },
  watch: {
    ...
  },
  computed: {
    ...
  },
  methods: {
    ...
  }
})
```

As mentioned in the green area at the top, a watcher monitors a data property with the same name.

We never call a watcher method. It is only called automatically when the property value changes.

The new property value is always available as an input argument to the watcher method, and so is the old value.

**Example:**
An `<input type="range">` element is used to change a value 'rangeVal'. A watcher is used to prevent the user from choosing values between 20 and 60 that are considered illegal.

```html
<input type="range" v-model="rangeVal">
<p>{{ rangeVal }}</p>
const app = Vue.createApp({
  data() {
    rangeVal: 70
  },
  watch: {
    rangeVal(val){
      if( val>20 && val<60) {
        if(val<40){
          this.rangeVal = 20;
        }
        else {
          this.rangeVal = 60;
        }
      }
    }
  }
})
```

### A Watcher with New and Old Values

In addition to the new property value, the previous property value is also automatically available as an input argument to watcher methods.
Example

We set up click event on a `<div>` element to record mouse pointer x-position 'xPos' with a method 'updatePos'. A watcher calculates the difference in pixels between the new x-position and the previous with the use of old and new input arguments to the watcher method.

```html
<div v-on:click="updatePos"></div>
<p>{{ xDiff }}</p>
const app = Vue.createApp({
  data() {
    xPos: 0,
    xDiff: 0
  },
  watch: {
    xPos(newVal,oldVal){
      this.xDiff = newVal-oldVal
    }
  },
  methods: {
    updatePos(evt) {
      this.xPos = evt.offsetX
    }
  }
})
```

We can also use new and old values to give feedback to the user the exact moment the input goes from being invalid to valid:
Example

The value from an `<input>` element is connected to a watcher. If the value includes a '@' it is considered a valid e-mail address. The user gets a feedback text to inform if the input is valid, invalid, or if it just got valid with the last keystroke.

```html
<input v-type="email" v-model="inpAddress">
<p v-bind:class="myClass">{{ feedbackText }}</p>
const app = Vue.createApp({
  data() {
    inpAddress: '',
    feedbackText: '',
    myClass: 'invalid'
  },
  watch: {
    inpAddress(newVal,oldVal) {
      if( !newVal.includes('@') ) {
        this.feedbackText = 'The e-mail address is NOT valid';
        this.myClass = 'invalid';
      }
      else if( !oldVal.includes('@') && newVal.includes('@') ) {
        this.feedbackText = 'Perfect! You fixed it!';
        this.myClass = 'valid';
      }
      else {
        this.feedbackText = 'The e-mail address is valid :)';
      }
    }
  }
})
```

### Watchers vs. Methods

Watchers and methods are both written as functions, but there are many differences:

- Methods are called from HTML.
- Methods are often called when an event happens.
- Methods automatically receives the event object as an input.
- We can also send other values we choose as an input to a method.
- Watchers are only called when the watched data property value changes, and this happens automatically.
- Watchers automatically receives the new and old value from the watched property.
- We cannot choose to send any other values with a watcher as an input.

### Watchers vs. Computed Properties

Watchers and computed properties are both written as functions.

Watchers and computed properties are both called automatically when a dependency change, and never called from HTML.

Here are some differences between computed properties and watchers:

- Watchers only depend on one property, the property they are set up to watch.
- Computed properties can depend on many properties.
- Computed properties are used like data properties, except they are dynamic.
- Watchers are not referred to from HTML.

**Exercise:**
The watcher in this exercise is supposed to increment the 'count' data property by one every time 'rangeVal' data property changes.

What must the watcher be called?

```js
<script>
  const app = Vue.createApp({
    data() {
      return {
        rangeVal: 70,
        count: 0
      }
    },
    watch: {
      () {
        this.count++
      }
    }
  })
 app.mount('#app')
</script>
```

## Vue Templates

A template in Vue is what we call the HTML part of our Vue application.

The `<template>` tag will later be used in *.vue files to structure our code in a better way.

It is possible to use template as a configuration option in the Vue instance, and put the HTML code inside.

### The Vue Template

Let's look at an example where we use 'template' as a configuration option. This is a simple example where we have just moved the HTML part into the configuration option 'template':

**Example:**
The HTML content from inside the `<div id="app">` is moved to the configuration option `template`, encapsulated in backtick quotes `...`. We can write many lines of HTML inside a back tick quote.

```html
<div id="app"></div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  const app = Vue.createApp({
    template:
      `<h1>{{ message }}</h1>
      <p>This is a second line of HTML code, inside back tick quotes</p>`,
    data() {
      return {
        message: "Hello World!"
      }
    }
  })
app.mount('#app')
</script>
```

## Single File Components (SFCs)

As you can see in the code example above, also the HTML part of our Vue application can be gathered inside the Vue instance, but this does not make it easier to get an overview in the HTML file.

To get a better overview, to make it easier to handle larger projects, and to get a better development environment, we will now switch to write our Vue code in SFCs, or *.vue files.

All *.vue files only consist of three parts:

- `<template>` where the HTML content is.
- `<script>` for our Vue code.
- `<style>` where we write the CSS styling.

But before we can use *.vue files in our project we need to set up our computer in a different way. The next pages in this tutorial will explain this.

[Nazad](../README.md)
