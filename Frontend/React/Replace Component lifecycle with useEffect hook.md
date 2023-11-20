 ## componentDidMount
```react
// With ccomponentDidMount() componentDidMount() { 
	console.log('Hello World'); 
} 

// with useEffect() 
useEffect(() => { 
	console.log('Hello World'); 
}, [])
```
## componentDidUpdate
```react
// With ccomponentDidUpdate() componentDidUpdate(prevProps) { 
    console.log(`Hello World ${prevProps}`); 
} 

// with useEffect() 
useEffect(() => { 
	console.log('Hello World'); }, [prevProps]
)
```
## componentWillUnmount
```react
// With ccomponentDidUnmount() componentWillUnmount() { 
	console.log('Hello World'); 
} 

// with useEffect() 
useEffect(() => { 
	console.log('Hello World'); 
	return () => { 
		console.log('Do some cleanup'); 
	} 


}, [])
```

tag: #React

### useEffect
- The `useEffect` hook takes two arguments
- The first is a ==callback function== which is going to be ==the code== or ==the effect== that we ==want to happen inside of our functional component==
- The second is an ==array of dependencies==. And by dependencies, these are most likely going to be either ==state values== or ==prop values==. What this array says is that whenever any of the values inside of this dependencies array ==change== is when I'm going to ==run the first argument which is this callback function==
- If you don't put some functions in `useEffect`, they will ==execute every time== when the ==component updates==. This could lead to a lot of inefficiencies