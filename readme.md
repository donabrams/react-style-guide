# Craftsy React Style Guide (WIP)

## Table of Contents

* [Component Setup](#component-setup)
  * [Component Naming](#component-naming)
  * [Method Organization](#method-organization)
  * [Formatting](#formatting)
  * [Component Attributes(Props)](#component-attributesprops)
* [JSX](#jsx)
  * [Conditional JSX](#conditional-jsx)
  * [List Iteration](#list-iteration)
* [Forms](#forms)

## Component Setup

### Component Naming

Components should be assigned to variables whose name will be the components name used throughout the application.
In JSX:
```
var Component = React.createClass({});
```
Compiles to:
```
var Component = React.createClass({displayName: "Component"});
```
___

This helps with modulatity in your application - Export/Require (Component.jsx example):
```
var Component = React.createClass({});
module.export = Component;
```
```
define([],function(){
  var Component = React.createClass({});
  return Componenet
});
```

### Method Organization

Order component methods based on the order of their life-cycle methods:
```
var Component = React.createClass({
  propTypes: {},
  mixins : [],

  getInitialState: function() {},
  getDefaultProps: function() {},
  componentWillMount: function() {},
  componentWillReceiveProps: function() {},
  componentWillUnmount: function() {},

  // Begin custom methods
  _customProcessMethods: function() {},
 
  _customEventMethods: function() {},
 
  _customRenderMethods: function() {},

  // End with render
  // Locate quickly and understand components internal elements and structure
  render: function() {}

})
```
___

Prefix custom functions with an underscore to distinguish between custom methods and methods which are part of React's API (example Component.jsx):  
```
var Component = React.createClass({
  propTypes: {},
  
  getInitialState: function() {},
  componentWillReceiveProps: function() {},
  
  _computePrice : function() {},
  
  _onChange : function() {},
  
  _renderButtonGroup : function() {},
  
  render : function() {}
});

```

*Note:
Keep the render functions at the bottom of your component declarations. This way, you can always expect to find it in the same place.*


### Formatting

```
// Childless components should close themselves
<childless />

// Components with children should have closing tags
<with-children></with-children>
```  


### Component Attributes(Props)

***Discuss***
Always pass specifically need/required props and not all props to components/sub-components:
**bad**
```
<SubComponent {...this.props} />
```

**good**
```
<SubComponent theData={this.props.theData} />
```

***Discuss***
If there are multiple component attributes, display them on newlines and indent them instead of keeping them in-line: 

**good**
```
<Component
        attribute={...}
        anotherAttribute={...}
        attributeThree={...}
        …
/>
```
**bad**
```
<Component attribute={...} anotherAttribute={...} attributeThree={...} />
```

___
Set propTypes for validation and self-documentation:
```
propTypes: {
        arrayProp: React.PropTypes.array,
        boolProp: React.PropTypes.bool,
        funcProp: React.PropTypes.func,
        numProp: React.PropTypes.number,
        objProp: React.PropTypes.object,
        stringProp: React.PropTypes.string,
    }
```

## JSX

Keep indenting consistent for JSX which contains nested elements across multiple lines, no matter how few elements are returned. This helps preserve readability:

**good**
```
return (
    <div>
        <ComponentOne />
        <ComponentTwo />
    </div>
);
```
**bad**
```
return (<div><ComponentOne /><ComponentTwo /></div>);
```
### Conditional JSX

Ternaries are fine for simple use-cases:
```
{this.state.show && 'This is Shown'}
{this.state.on ? ‘On’ : ‘Off’}

```
___

***Discuss***
When the element returned by `render` depends on state, props, or other conditions, declare it at the top of the render function: 
either the element will be rendered, or it won't be.

```
var optionalElement;

if (this.props.condition) {
    optionalElement = (<div> … </div>);
}

return (
    <div>
        …
        {optionalElement}
        …
    </div>
);

```

*Note: It can be useful to keep a consistent suffix on these variables so they are easily identifiable:*

```
optionalButtonElement, optionalFormElement, optionalTabElement
```

### List Iteration

Create lists outside of the returned render and always assign a unique key:

```
var renderList = this.props.list.map(function(data, i) {
        return (<Component data={data} key={i} />)
 })

return (
    <div>
        {renderList}
    </div>
);
```

## Forms

Form elements can be written with an onChange handler to avoid having to register onChange on every field (within the same form):

```
<form onChange={this.inputHandler}>  
  ...
    <input type="text" name="newDinosaurName" value={this.state.newDinosaurName} />
  ...
</form>  
```
where the `inputHandler` looks like:
```
function(event) {  
  actions.propagateValue({
    field : event.target.name,
    value : event.target.value
  });
}
```

## Misc

* Avoid placing state inside components if at all possible


## Credits

*Initially Forked from the [React Style Guide](https://github.com/DeepAnchor/react-style-guide). Thank you for starting DeepAnchor!*

* [React Style Guide, by David Chang](https://reactjsnews.com/react-style-guide-patterns-i-like/)  
* [An opinionated guide to React.js best practices and conventions, by John Cobb](http://web-design-weekly.com/2015/01/29/opinionated-guide-react-js-best-practices-conventions/)  
* [Khan React style guide](https://github.com/Khan/style-guides)  
* [react guides](http://facebook.github.io/react/docs/getting-started.html)
* [Writing good react components](http://blog.whn.se/post/69621609605/writing-good-react-components)
