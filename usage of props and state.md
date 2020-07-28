
# React: Usage of Props and State

codes and quotes are from [react tutorial]([https://reactjs.org/tutorial/tutorial.html](https://reactjs.org/tutorial/tutorial.html))

### What is different?

* props is stand for *properties*. It is a variable that user want to pass data to component and keep it. So, props are unchangeable, and it's passed from outside component.
* state is describing the variables in component itself. Components can set state by `this.state` in their constructors. 

###  Sync between components

> **To collect data from multiple children, or to have two child components communicate with each other, you need to declare the shared state in their parent component instead. The parent component can pass the state back down to the children by using props; this keeps the child components in sync with each other and with the parent component.**

By setting value in parent component's state, and passing down to children components with props, we can keep sync between multiple components.

### Recognize that passed props are updated

```javascript
class Board extends React.Component {
  renderSquare(i) {
    return (
      <Square
        value={this.state.squares[i]}
        onClick={() => this.handleClick(i)}      />
    );
  }
```

```javascript
class Square extends React.Component {  
	render() {    
		return (
	      <button
	        className="square"
	        onClick={() => this.props.onClick()}      >
	        {this.props.value}      
	      </button>
	    );
	}
}
```

> In React terms, the Square components are now **controlled components**. The Board has full control over them.

