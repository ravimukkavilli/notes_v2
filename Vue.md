# Vue


*  Vue is a open source front end JS library
*  Vue uses moduler components for code reuse.
*  Vue apps are written in JS.
*  easy to added to an existng site.
*  Vue and Angular have sticter structure.
*  Vue and React are libraries and Angulare is Framework
*  Vue is easiest to add to an exisitng site.
*  Parts of an Vue Components:
    *  Template is the HTML structure
    *  Props: are inputs that we can past to components.
    *  Data: the component's state.
    *  Computed: data the requires extra logic.
    *  Events: outputs thay the component can trigger.
    *  Lifecycle hooks: function that are called at specific times.
*  State in Components:
    *  state is any data that component contains that can change over time like counter   *  Where to put the state, when more than one component uses that state.
*  There are many lifecycle hooks
    *      




-  Create a an appication by using a  Vue object we acalled the createApp() which create an Vue Instance, we supplied a obtions object that defines the data that oru applications is going to use. We are
mounting the app to an element of #content where the template is defined. The template define where we want to show our application data.

```html
  <!DOCTYPE html>
  <html>
  
  <head>
      <title>Vue Basics</title>
      <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
      <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css">
  </head>
  
  <body>
      <div id="content" class="container">
          <h1>{{pageTitle}}</h1>
          <h2>{{ content }}</h2>
      </div>
  
      <script>
          Vue.createApp({
              data() {
                  return {
                      pageTitle: 'Hello Vue',
                      content: 'Welcome to the world of Vue.js.'
                  };
              }
          }).mount('#content')
      </script>
  </body>
  
  </html>
```

-  We create as many Vue applications in a single web page.
-  We can use a `Vue derective` inside of the template. Vue directive start with `v-`.
  ```html
    <ul class="navbar-nav">
        <li v-for="link in links" class="nav-item active">
            <a class="nav-link" href="#">{{link}}</a>
        </li>
    </ul>
  ```
-  We can define objects in the options.
-  We can use `v-bind:` or jus the `:` to bind a vue data to an `html attribute`. For example to bind the Vue data to href attribute we can say `v-bind:href="link.url"` or `:href="link.url"`.
  ```html
    <!DOCTYPE html>
    <html>
    
    <head>
        <title>Vue Basics</title>
        <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.0.0/dist/css/bootstrap.min.css">
    </head>
    
    <body>
        <nav class="navbar navbar-expand-lg navbar-light bg-light">
            <a class="navbar-brand" href="#">Navbar</a>
            <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
                <div class="navbar-nav">
                    <ul class="navbar-nav">
                        <li v-for="link in links" class="nav-item active">
                            <a class="nav-link" v-bind:href="link.url">{{link.text}}</a>
                        </li>
                    </ul>
                </div>
        </nav>
        <div id="content" class="container">
            <h1>{{pageTitle}}</h1>
            <h2>{{ content }}</h2>
        </div>
    
        <script>
            Vue.createApp({
                data() {
                    return {
                        links: [
                            { text: 'Home', url: 'home.html' },
                            { text: 'About', url: 'about.html' },
                            { text: 'Contact', url: 'contact.html' }
                        ]
                    };
                }
            }).mount('nav')
            Vue.createApp({
                data() {
                    return {
                        pageTitle: 'Hello Vue',
                        content: 'Welcome to the world of Vue.js.'
                    };
                }
            }).mount('#content')
        </script>
    </body>
    
    </html>
  ```

