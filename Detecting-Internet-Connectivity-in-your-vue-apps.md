# Detecting Internet Connectivity in your vue apps

- Sometimes, while building your app, you would want to allow certain actions to happen only when the user is connected to the internet. 

	A relatable example of an app that does this is Figma. Whenever figma detects that you are no longer connected to the internet, it is quick to inform you that whatever changes you make right now won't be saved cause you are disconnected. That nudges you to check your internet connection. Subtly providing a good user experiece.
	
	This tiny super feature can help your app in several ways one of which is providing a good user experiece for the user by alerting them when there might be issues with their connectivity. Saving them a bit of head scracthing down the line when they find out this.
	
	Also, by checking if there is internet connectivity, you can write checks to prevent some functions to be called if the user isn't online. Thereby keeping your app on the good side of the perfomance scale.
	
	In this article, we would be implement a network status checker that detects when our vue app is connected or disconnected from the internet and in turn we will use this information to perform an action.
	
	## Table of Content
	-  What is vue?
	- Project setup  
	- Craft a tiny demo app  
	- Implement Internet Detector functionality  
	- Style our app
	- Perform an miniature test  
	- Conclusion and Confetti

### What is vue?
[Vue.js](http://Vuejs.org) is an incredibe and super useful JavaScript framework for building fast and lightweight user interfaces. It touts itself as a *progressive framework* which just means that you can use bits of vue in parts of your application only scaling up when you need it. So it's not all or nothing. Vue also combines the best parts of some of the most popular frameworks out there like React and angular.
 
### Project Setup  
The first thing to do is to get vue installed on our machine. If you already have vue setup or you know how to wade through that, you can skip this portion of the project. 
  
So go ahead and run  `npm init vue@latest` in your terminal. This will execute `create-vue`, an official scaffolding tool by Vue to create fully-functional Vue projects.  
  
You will be asked to give your project a name in the terminal wizard. Let’s call our app `Fagowee`.  When prompted for further configuration of the project, press enter for all of the options.  
  
Next, run `cd Fagowee` and `npm install` to set all the necessary dependencies for the project.  
  
Finally, run `npm run dev`, which will launch a dev server for your shiny new Vue app. You can access it by opening `http://localhost:3000` in your browser.  
  
Here is a list of all the commands above in order of execution.  
  
1.  `npm init vue@latest`   - prompted for a name, we chose fagowee.  
2. `cd Fagowee`  
3. `npm install`  
4.  `npm run dev`  
  
### Craft a tiny demo App  
 We would require two images while carving this tiny app. You can download it here. Now copy them into `assets` folder inside src. Good.
 
Then navigate to the src folder and locate the [App.vue](http://App.vue) file. Here we would write the entire logic for our tiny app. We will build everything from scratch, so delete everything in the [App.vue](http://App.vue) file. Then, enter the following lines of code.  

```js
<template>
  <div id="wrapper">
    <h1>{{ isConnected ? "Back online" : "No internet Connection" }}</h1>
    <section>
      <img
        v-if="isConnected"
        src="./assets/farago.gif"
        alt="Cool, Chic, Caml"
      />
      <img v-else src="./assets/losty.gif" alt="Cool, Chic, Caml" />
    </section>
  </div>
</template>

```
In Vue, the entirety of the Single-file-components must be wrapped in the `template` tag. Vue will compile the whole thing and render it in a virtual DOM. 

We created an H1 element that would display either `Back online` or `No internet Connection` based on the value of `isConnected`.  We used the ternary version of if statement to trim down the code.

We also included two images whose visibility also depend of the value of `isConnected`. We used the `v-if` directive provided by vue to render the first image if `isConnected` is `true`.  Likewise, the `v-else` directive render the second image if isConnected is false. 

### Implement Internet Detector functionality
Great! You made it here. This portion is where we would build out the part of our app that responds to the user's network status. 

Vue permits us to write javascript code right inside our `.vue` files. All we need to do is write all the logic inside the `<script></script>` tag. Vue uses this code to add interactivity to our shiny tiny app.

There are two ways we can author our logic in vue. One is the Options API. The other is the Composition API. We will use the Composition API method In our app.

Head over to your `App.vue` file and Just beneath our `template` tag, write out the lines of code below.

```js

<script>
import { ref, onMounted } from "vue";

export default {

  name: "Home",
  setup() {
    const isConnected = ref(true);

    const toggleisConnected = () => {
      if (navigator.onLine) {
        isConnected.value = true;
      } else {
        isConnected.value = false;
      }
    };

    onMounted(() => {
      window.addEventListener("online", toggleisConnected);
      window.addEventListener("offline", toggleisConnected);
	  toggleisConnected();
    });

    return {
      isConnected,
    };

  },
};

</script>

```

The first thing we did is import `ref` and `onMounted` from vue. This pattern is how we use the composition API. We only retrieve what ever we need from Vue's bag of awesomeness. In this case, `ref` is used to make a variable reactive and `onMounted` is a lifecycle hook that provides us a way to run a function when our component is loaded up.

Next, we export a bunch of stuffs so our component instance can pick them up. We also gave our component a name. That is written as `name:Home`.

Also, Since we are using the Composition API, we would need the `setup()` hook.
The `setup()` hook serves as the entry point for using the composition api in our component.  Inside the `setup()` hook, we can declare reactive state using reactivity apis e.g `ref`, `reactive` and expose them to the template by returning an object from `setup()`.

Inside our `setup` function, we created a boolean variable `isConnected` and made it reactive by wrapping it's value i.e `true` with `ref`. 

Next, we wrote out a function called `toggleisConnected` which does only one thing. That is, check if the value of `navigator.onLine` (note the way I wrote onLine, this is how you should too) is `true`. If true, set `isConnected.value` to `true`. 
On the other hand, If `navigator.onLine` is `false`, set `isConnected.value` to false.

Why `isConnected.value` you might ask? Well, when using the composition API, inside setup, our reactive values are still wrapped up inside some proxy thing of sorts. Therefore, we need to manually retrieve our value. But, when accessing the returned objects of `setup()` hook inside our template, vue automatically *shallow unwraps* these values and we can use them without the `.value` operation.

Now to the fun part, Using the `onMounted` hook we unpacked from vue, we would tell vue to apply two event listners to the windows object whenever we instantiate this component. 

Event Listeners are functions that invokes a predefined process whenever a specfic event occurs. So, event listeners *listens* for an action, then calls a function that performs a task. For our use case, we need to listen to the `online` and `offline` event. And whenever any of these two events is fired, the listener will call the `toggleIsConnected` function.

Finally, we return an object containing function and/or variables we want to expose to our template. We would be using `isConnected` to conditionally render a piece of text, so let's expose it. 

This is epic! We have completed the logic of our Fagowee app. In the next part, we would add some styling to clean the look up a bit.

###  🎨 Styling our app
Now to make our app look good, Let's add some styling. We can style our app directly inside `App.vue`. What vue only requires of us, is that we wrap the entire style rule inside a `<style>` tag. So go ahead and include these lines of code below the `script` tag we wrote above.

```css
<style>
*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
} 

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100vh;
  margin: 0;
}

#wrapper {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

section {
  width: fit-content;
  margin: 0 auto;
}

img {
  display: block;
  width: 300px;
  height: auto;
}
</style>
```

The first thing we did is to reset some browser default styles like margin and padding. We then tweaked the box-sizing rule for all the elements of our page to `border-box`. This changes how our box-model is computed. 

Next, we instructed the parent element of our entire app to take the entire height of the viewport. That is represented by `height: 100vh` in `#app`.
The default font rendered by the browser is a serif font, so we replaced that with `avenir` using the `font-family` style rule. Centered aligned all the text on the app. Gave the font a color of `#2c3e50`.

To style the `#wrapper` element, we gave the element a `height` of `100%` and a display of `flex` to modify the contents layout. Then, we used a `flex-direction` of `column`  and aligned the content on the vertical axis to the center using `justify-content: center`.

The section element width is now modified to fit the content.Using the shorthand method of declaring margin rules, It's margin-top and margin-bottom set to 0. Then, margin-left and margin-right is set to auto.

For the images, we style the `img` tag. The default `display` rule of images is `inline` but `inline` doesn't allow us to modify width and height so we change it to `block`. Next, we set it's width to `300px` and gave it a `height`  of `auto`.

This completes our styling and our Fagowee app should look way better now. 
At this stage we've essentially completed our app but fingers crossed let's see if it works in the next portion. Let's test.
  
### Let's Test Fagowee
To test our app, ensure you have your dev server running. If not run, `npm run dev` once again and navigate to your browser using the link provided. It would look something like this `https://localhost:8080`. The last 4 digits, called Port, of your url might differ from mine. It's not a bug, just use whatever your server spits out for you.

Now to test if Fagowee responds to the network status of the user, toggle your machines Airplane mode. When you activate the airplane mode, you should see `No internet Connection` and when you deactivate, automatically you should see `Back online`.

--Insert Gif--

Take a look at your shiny new awesome app. Not any ordinary app but one that responds to the network status of your user. You should be proud of your self. Awesome!
  
### Conclusion and Confetti
This is a tiny but really helpful feature we just implemented. We combined this with the power of Vue to build a responsive appliction that responds to the Network status of the user.

However, a fair warning, this isn't a true test of a user connection to the internet. There are other ways to do this that are more reliable in the wild. I would drop a link in the Further readings section if you are intrested in this.

That's it for this article. See you in the next one. Yeehaw!

### Further Readings
- Stackoverflow site for  👨‍🚀
