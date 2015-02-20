# Craftsy React Style Guide (WIP)

## Table of Contents

* [Component Setup](#component-setup)
  * [Component Naming](#component-naming)
  * [Method Organization](#method-organization)
  * [Formatting](#formatting)
  * [Component Attributes(Props)](#component-attributesprops)
    * [Passing Properties](#passing-properties)
    * [Assigning Properties](#assigning-properties)
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
  return Component;
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
  _customHelperMethods: function() {},
  _customDerivedPropertiesMethods: function() {},
 
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
<with-children><child/></with-children>
```  


### Component Attributes(Props)

##### Passing Properties

Always pass specifically need/required props and not all props to components/sub-components:

**bad**
```
<SubComponent {...this.props} />
```

**good**
```
<SubComponent theData={this.props.theData} />

<SubComponent {...this.props.theData} />
```

##### Assigning Properties
If there are multiple component attributes, display them on newlines and indent them instead of keeping them in-line: 

**bad**
```
<Component attribute={...} anotherAttribute={...} attributeThree={...} attributeFour={...} attributeFive={...} attributeSix={...} attributeSeven={...}/>
```

**good**
```
<Component attribute={...}
        anotherAttribute={...}
        attributeThree={...}
        attributeFour={...}
        attributeFive={...}
        attributeSix={...}
        attributeSeven={...}
/>
```

___
Set propTypes for validation and self-documentation, required first:
```
propTypes: {
        arrayOfProps: React.PropTypes.arrayOf(React.PropTypes.object).isRequired,
        arrayProp: React.PropTypes.array,
        boolProp: React.PropTypes.bool,
        funcProp: React.PropTypes.func,
        numProp: React.PropTypes.number,
        objProp: React.PropTypes.object,
        stringProp: React.PropTypes.string
    }
```

Avoid using React.PropTypes.object and React.PropTypes.array if you can be more explicit:
```
propTypes: {
        message: React.PropTypes.instanceOf(Message),
        someView: React.PropTypes.shape({
                numProp: React.PropTypes.number.isRequired,
                funcProp: React.PropTypes.func
            }),
        optionalUnion: React.PropTypes.oneOfType([
                React.PropTypes.string,
                React.PropTypes.number
            ]).isRequired,
        arrayOfProps: React.PropTypes.arrayOf(React.PropTypes.shape({
                arrayProp: React.PropTypes.array,
                boolProp: React.PropTypes.bool,
            })),
        customProp: function(props, propName, componentName) {
          if (!/matchme/.test(props[propName])) {
            return new Error('Validation failed!');
          }
        }
    }
```

## JSX

Keep indenting consistent for JSX which contains nested elements across multiple lines, no matter how few elements are returned. Always use enclosing parens aournd JSX blocks. This helps preserve readability:

**bad**
```
return <div><ComponentOne /><ComponentTwo /></div>;
```

**good**
```
return (
    <div>
        <ComponentOne />
        <ComponentTwo />
    </div>
);
```
### Conditional JSX

Ternaries are fine as long as they fit on one line without scrolling:
```
{this.state.on ? ‘On’ : ‘Off’}

```
___

When an element returned by `render` depends on state, props, or other conditions, declare it in a var at the top of the render function: 
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

It can be useful to keep a consistent suffix on these variables so they are easily identifiable:*

```
optionalButtonElement, optionalFormElement, optionalTabElement
```
___

Do NOT use &&. 

```
//BAD
return (
    <div>
        …
        {this.props.condition && (<div> … </div>)}
        …
    </div>
);

```

### List Iteration


Create lists inline with map calling a render method and ALWAYS assign a unique key:

```
_renderListItem: function(item) {
  // Keep the key as the first attribute so it's readily identifiable.
  return (<Component key={item.key} data={item.data} />);
},

render: function() {
  return (
      <div>
          {this.props.list.map(this._renderListItem)}
      </div>
  );
}
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

## State vs. Props

Props are:

* Never modified by the component itself (directly)
* Are set and modified by the parent

State is:

* Set by the component
* Never set by an external source
* Never determined by props (exception: if a prop is explicitly an initial state i.e. ```<Foo initialCount=3/>```)
* Should be kept as high up in the component hierarchy as reasonable
* Never duplicated or kept "in sync" among siblings.

Derived properties should be moved into a component method (and memoized if needed/possible).

Given this:
```
  propTypes: {
      initialList: React.PropTypes.arrayOf(React.PropTypes.string).required
  },
  getInitialState: function() {
      return {
          title: 'WAT',
          filterString: '',
          myList: this.props.initialList.slice(0);
      }
  },
  ...
```
Bad:
```
  render: function() {
      return (
          <h1> { title.substr(0,20) } </h1>
          <ul>
            { this.state.myList.filter(function(item) { return item.matches(this.state.filterString); }).map(function(item) {
              <li>{ item }</li>
            })}
          </ul>
      );
  }
```
Good:
```
  _shortTitle: function() {
      return title.substr(0,20);
  },
  _filteredList: function() {
      return this.state.myList.filter(function(item) { 
             return item.matches(this.state.filterString); 
          });
  },
  render: function() {
      return (
          <h1>{ _shortTitle() }</h1>
          <ul>
            { this._filteredList().map(function(item) {
              <li>{ item }</li>
            })}
          </ul>
      );
  }
```

Note: Memoization is only practical with immutable objects and arrays. Immutability is recommended.


## Credits

*Initially Forked from the [React Style Guide](https://github.com/DeepAnchor/react-style-guide). Thank you for starting DeepAnchor!*

* [React Style Guide, by David Chang](https://reactjsnews.com/react-style-guide-patterns-i-like/)  
* [An opinionated guide to React.js best practices and conventions, by John Cobb](http://web-design-weekly.com/2015/01/29/opinionated-guide-react-js-best-practices-conventions/)  
* [Khan React style guide](https://github.com/Khan/style-guides)  
* [react guides](http://facebook.github.io/react/docs/getting-started.html)
* [Writing good react components](http://blog.whn.se/post/69621609605/writing-good-react-components)
