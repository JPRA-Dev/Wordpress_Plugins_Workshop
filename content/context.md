# Context

Last time we dealt with theme development and so we approached multiple notions like fields, template hierarchy and a bit of the WordPress files & folder structure.

This time we are going to deepen our understanding on how WordPress can extend its core features with the help of hooks or shortcodes in order for users to make infinite amount of features. 

# What are hooks?

The easiest way to put this is to say that hooks are events for example the loading of the admin panel or of a single page. A hook helps us to call a function we defined previously at a precise moment. 

They are two types of hooks: 
- Action: call a function but returns null
- Filter: catch a value at a specific moment in order to modify it

Using APIs developers can either fires hooks or create custom ones. 

This process is called “hooking” and in order to really get this fundamental notion we are going to use it in the context of a [plugin building!](./hooks.md)

![Hook](https://media1.giphy.com/media/6eLVFfvYnirNm/giphy.gif)
