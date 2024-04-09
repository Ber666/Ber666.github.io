---
layout: post
title: "Notes on JavaScript"
date: 2023-09-22 09:56:00-0400
description: Notes on Javascript
tags: "web", "javascript"
categories: "notes"
giscus_comments: false
related_posts: false
related_publications: 
---
# React
https://nextjs.org/learn/react-foundations
## Background
We can use JS to manipulate the DOM, but React provides a different way:
```html
<script type="text/javascript">
  const app = document.getElementById('app');
  const header = document.createElement('h1');
  const text = 'Develop. Preview. Ship.';
  const headerContent = document.createTextNode(text);
  header.appendChild(headerContent);
  app.appendChild(header);
</script>
```
The code above (Javascript) is a good example of imperative programming. You're writing the steps for how the user interface should be updated. 

It would be helpful if developers were able to declare what they want to show (in this case, an `h1` tag with some text), called declarative programming.

```html
<script src="https://unpkg.com/react@18/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
<!-- Babel Script -->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
<script type="text/jsx">
  const app = document.getElementById('app');
  const root = ReactDOM.createRoot(app);
  root.render(<h1>Develop. Preview. Ship.</h1>);
</script>
```

Note we need to import babel to intepret this `JSX` code (Javascript plus HTML-like syntax).

## Components
A component is a function that returns UI elements. Inside the return statement of the function, you can write JSX:
```jsx
function Header() {
  return <h1>Develop. Preview. Ship.</h1>;
}
 
const root = ReactDOM.createRoot(app);
root.render(<Header />);
```
Rendering a component is like using an html tag.


**Nesting**
```jsx
function Header() {
  return <h1>Develop. Preview. Ship.</h1>;
}
 
function HomePage() {
  return (
    <div>
      {/* Nesting the Header component */}
      <Header />
    </div>
  );
}
```

**Props**
Passing argument to the components (also like an HTML tag): `<Header title="React" />`

```jsx
function Header(props) {
  console.log(props); // { title: "React" }
  return <h1>Develop. Preview. Ship.</h1>;
}
// or
function Header({ title }) {
  return <h1>{`Cool ${title}`}</h1>;
}
```

**Event Handling**
```jsx
function HomePage() {
  // 	...
  function handleClick() {
    console.log('increment like count');
  }
 
  return (
    <div>
      {/* ... */}
      <button onClick={handleClick}>Like</button>
    </div>
  );
}
```

## State and Hook


React has a set of functions called hooks. Hooks allow you to add additional logic such as state to your components. You can think of state as any information in your UI that changes over time, usually triggered by user interaction.

The React hook used to manage state is called: `useState()`.
```jsx
function HomePage() {
  // ...
  const [likes, setLikes] = React.useState(0);
 
  function handleClick() {
    setLikes(likes + 1);
  }
 
  return (
    <div>
      {/* ... */}
      <button onClick={handleClick}>Likes ({likes})</button>
    </div>
  );
}
```

# Next.js

Next.js uses file-system routing.