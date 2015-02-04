# A styleguide for React
This repo is a collection of (non-authoritative) React style conventions that I find to be useful, distilled from various sources on github and the web.

PRs welcomed!


## Table of Contents

* [Method Organization](##method-organization)
* [JSX](##jsx)
  * [Conditional JSX](###syntax)
* [List Iteration](#list-iteration)
* [Forms](#forms)
* [Formatting](#formatting)
  * [Tags](##tags)
  * [Component Attributes](##component-attributes)

## Method Organization

Order component methods based on the order of their life-cycle methods:
```
React.createClass({  
  displayName : '',
  propTypes: {},
  mixins : [],
  getInitialState : function() {},
  componentWillMount : function() {},
  componentWillUnmount : function() {},
  render : function() {}
});
```
___

Prefix custom functions with an underscore to distinguish between custom methods and methods which are part of React's API:  
```
React.createClass({  
  displayName : '',
  propTypes: {},
  mixins : [],
  getInitialState : function() {},
  componentWillMount : function() {},
  componentWillUnmount : function() {},
  _onChange : function() {},
  _onCreate : function() {},
  render : function() {}
});

```

*Note:
Keep the render functions at the bottom of your component declarations. This way, you can always expect to find it in the same place.*

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

Wrap JSX that spans multiple lines in parentheses:
```
var multilineJsx = (  
  <header>
    <Logo />
    <Nav />
  </header>
);
```

Disregard parentheses for JSX spanning a single line:

```
var singleLineJsx = <h1>Simple JSX</h1>;  
```
___

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
___
### Conditional JSX

Ternaries are fine for simple use-cases:
```
{this.state.show && 'This is Shown'}
{this.state.on ? ‘On’ : ‘Off’}

```
___

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

*Note: It's can be useful to keep a consistent suffix on these variables so they are easily identifiable:*

```
buttonElement, formElement, tabElement
```


## List Iteration

For simple use-cases, list iterations can be done in-line inside `render`. This can aid in keeping code clean and terse:

```
return (
    <div>
        {this.props.list.map(function(data, i) {
            return (<Component data={data} key={i} />)
        })}
    </div>
);
```

More complex logic can be kept outside of the return statement.


*Note: ES6 users can make use of the fat arrow to further simplify things:* 

```
return (
    <div>
        { this.props.list.map( (data, i) => <Component data={data} key={i} /> }
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




## Formatting

### Tags

Childless components should close themselves:
```
<childless />
```  

Components with children should have closing tags:
```
<with-children></with-children>
```

### Component Attributes

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

## Credits

* [React Style Guide, by David Chang](https://reactjsnews.com/react-style-guide-patterns-i-like/)  
* [An opinionated guide to React.js best practices and conventions, by John Cobb](http://web-design-weekly.com/2015/01/29/opinionated-guide-react-js-best-practices-conventions/)  
* [Khan React style guide](https://github.com/Khan/style-guides)  
* [react guides](http://facebook.github.io/react/docs/getting-started.html)

