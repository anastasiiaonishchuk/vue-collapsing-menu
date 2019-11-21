# vue-collapsing-menu

this component is collapsing overflow menu, that add items into hidden dropdown block.

#Installation
 
```
npm install vue-collapsing-menu --save-dev
```

```
yarn add -D vue-collapsing-menu
```

# Usage

At first, you need to register the component

```
  import VCollapsingMenu from "vue-collapsing-menu/src/Index";
  
  export default {
    components: {
      VCollapsingMenu
    }
  }
```
and after - add to your template

```
<template>
  <v-collapsing-menu :navbar="routes"/>
</template>
```

#Properties

The required property is `:navbar` - an array of routes for your menu.
It have to contain the next rules: 
* if you use a simple routing, use key "link", to add them to 'a' tag:

```
[
  {
    link: '/first-node',
    name: 'first node'
  },
  {
    link: '/second-node', 
    name: 'second node'
  }
]
``` 
* if you use a vue-router, use key "path", to add them to 'router-link' tag:

```
[
  {
    path: '/first-node',
    name: 'first node'
  },
  {
    path: '/second-node', 
    name: 'second node'
  }
]
``` 
* if you need to use a custom html-element, use key "html":

```
[
  {
    html: '<a href="/first-path"><img src="/first-node"/></a>',
    name: 'first node'
  },
  {
    html: '<a href="/second-path"><img src="/second-node"/></a>', 
    name: 'second node'
  }
]
``` 
* if your categories have a children categories, just add them to the object
with key "children", and also add property `"hasChildren": true` :

```
[
  {
    link: '/first-node',
    name: 'first node',
    hasChildren: true,
    children: [
      {
          link: '/first-children-node',
          name: 'first children node'
       },
       {
          link: '/second-children-node',
          name: 'second children node'
       },
    ]
  },
  {
    link: '/second-node',
    name: 'second node'
  }
]
```
If you have more than one vue-collapsing-menu component on the page, you need
to add the property `:component-key`, for it works correctly. Component key have
to has a unique value  


Also, If you have a special margin or padding properties in your parent block
you can add a properties `:external-padding-max` for window width more than 1024px and
`:external-padding-min` for less. Please, use this properties with v-bind or : directives,
because this value have to be a number.

#Styling

This component already have a styles, using lang SCSS, but you can add your own 
styles in your component. For example:

``` 
.nav-bar {
    max-width: calc(100vw - 20px);
    margin: -10px 0;
    @media (max-width: 1540px) {
      margin: -40px;
    }
    @media (max-width: 1024px) {
      margin: -10px -20px;
      position: absolute !important;
      padding-left: 50px !important;
      max-width: calc(100vw - 40px) !important;
    }
}  
.and-another-class {
  .nav-bar {
    margin: 0;
    min-width: calc(100% - 40px);
    @media (max-width: 1024px) {
      min-width: calc(100% - 90px);
      position: static!important;
      padding-left: 0!important;
    }
    &__hidden-categories {
      background: #000;
      color: #fff;
    }
  }
}  
``` 

All styles classes you can see in `/src/Index.vue`

#Browser Support
 
I tested it for Chrome, Safari and Firefox and it works for all browsers

## Author

&#169; [Anastasiia Onishchuk](https://github.com/anastasiiaonishchuk) 