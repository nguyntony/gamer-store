# eBoy
Educational repo that uses a game API to showcase new, popular and upcoming games. Playing with React/Redux and more!

## Setup
After creating your react app, you will need to install the following packages:
#### Redux
- `redux`
- `react-redux`
- `redux-thunk`
#### Other
- `react-router-dom`
- `styled-components`
- `framer-motion`
- `axios`
- [API](https://api.rawg.io/docs/)

## All about redux
The following guide is primarily used for the initial setup of redux and the necessary technologies to make it function properly within your app. After you have set this up you will basically have a skeleton that will help improve your workflow!

Go to the root index.js file
1. `import {createStore} from 'redux'`
2. `const store = createStore()` this createStore() will take a reducer (ie. you may have multiple reducers and will need to combine them into one)

#### Setup Reducer
You should create a new directory in the src dir for reducers. For this particular app, you will need a game reducer that will hold the popular, new and upcoming games (big idea is that it will hold multiple data points)

After you have created your reducers dir, you will want to create a file for each kind of reducer that you need, in this case, we need a ‘gamesReducer’
1. `const gamesReducer = (state, action) => {}` this reducer will accept a state and an action. It is good to set up a default state 
2. 
```
const initialState = {
	popular: [],
	upcoming: [],
	newGames: []
}
```
This initialState will be an object with keys of anticipated data that we will have, we are expecting the value each key to be an array of objects. Initially, all of this will be empty and we are basically setting up a blueprint or empty shelf to store data in this state. 

3. You will now set the state parameter equal to this initialState
```
const gamesReducer = (state = initialState, action) => {}
```
4. For the code that this reducer needs is a switch case that will have different scenarios based on the ‘action.type’ (so the action will most likely be an object)
```
const gamesReducer = (state = initialState, action) => {
	switch(action.type){
		case "FETCH_GAMES":
			return {...state}	
		default: 
			return {...state}
	}
}
```
5. `export default gamesReducer` make sure you export your reducer!

6. You will follow the same format as above when you would like to create another reducer, be sure to connect it with the master reducer. 

#### TLDR
You will create a reducers dir that will have all of your reducers, one reducer (one file) may contain a reducer that will have more than one state (gamesReducer will have a state that is an object with popular, new and upcoming games).

You will create a reducer function that will accept two parameters, the state and action. Inside of the function there will be a switch case that will perform a specific action depending on the action.type 

#### Reducer directory setup
You have created the reducers dir which should have one file right now (the one we created above). Next, you may want to set up a directory for the reducers (this will be very similar to what we did in node/express). This is good for organization

You will create an index.js file in the reducers dir. 
1. `import {combineReducers} from 'redux'` this will allow you to combine multiple reducers that you may have into one ‘master/almighty’ reducer
2. `import gamesReducer from './gamesReducer'` import reducers
3. Create the ‘master reducer’, if you wish to add more reducers, you will do like so in this object.
```
const rootReducer = combineReducer({
	games: gamesReducer,
})
```
4. `export default rootReducer` export your master reducer!

#### Store setup
You will go back to the root index.js 

1. `import rootReducer from './reducers'` import that master reducer!
2. `const store = createStore(rootReducer)` from the store that we declared earlier we will need to pass the rootReducer to it. 
3. `import {Provider} from 'react-redux'` this will allow you to connect your reducers to your app. “How to connect my react app to my redux”
4. You will need to wrap the Provider around the App.
```
<Provider>
	<App />
</Provider>
```
5. `<Provider store={store}>` You will need to pass the store that we declared to the opening Provider tag. 
6. The createStore will only take two arguments, the first one that we set up so far is the combined reducers which will have all of the reducers that we wish to use, the second argument will be some sort of code that will connect this to the chrome google extension to better visualize our states. 

## Short explanation of the redux process
#### Action
What is it? It is an object that will describe what will happen. It will have a type property. `{type: "FETCH_GAMES"}`

You may see something called an action creator and what that is, is a function that returns an action.
`const fetchGames = () => {type: "FETCH_GAMES,"}` You will invoke this function and it literally returns the action object. When using the action creators, you can actually attach additional data to the reducer. So besides the type property you will also have a payload property. 
```
const fetchUser = (userData) => {
	return {
		type: "FETCH_USER",
		payload: userData	
	}
}
```
It is important to note that if you do this, you will need to ensure that your reducer will access that data by simply adding the action.payload into the reducer. 

#### Dispatch
Dispatch is basically you sending the action to the reducer. `dispatch({type: "FETCH_GAMES"})` what it accepts is essentially an action. 

#### Reducer
The dispatch sends the action.type and it will check the type and it will do something based on the type.

## Thunk
This will allow us to dispatch asynchronous actions. In the process that was explained above, there is no real way for us to do asynchronous actions. This means that in your action creator you will not be able to perform an axios call to an api. 

#### Setup
Go to the root index.js
1. `import thunk from 'redux-thunk'` 
2. `import {applyMiddleware, compose} from 'redux'` you will need to include this in the import statement that we should already have when importing createStore. 

- applyMiddleware, every action that is being dispatch it will allow us to add something between that process. In this case, we want thunk to be ran.

- compose will combine the chrome or window dev tool and the thunk because createStore only ACCEPTS 2 ARGUMENTS

3. `const composeEnhancer = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose` declare a compose enhancer which will be the redux dev tool. 
4. `const store = createStore(rootReducer, composeEnhancer(applyMiddleware(thunk)))` You will need to update the store that we declared earlier to this.

It is important to note that later you will be able to add other middleware such as logger 

## Action 
In the previous section I did talk about what is the purpose of the action and here I will elaborate on it.

Go ahead and create an actions dir inside of the src dir. And go ahead and create a file for it. 

Inside this file you will do the fetching. So you will need to import axios as well as anything code that is related to this fetching (this project has an api file that has the URLS to grab data from the API)
1. Create an action creator that will dispatch the action object.
```
export const loadGames = () => async (dispatch) => {
	// code to do your fetch
	 const popularData = await axios.get(popularGamesURL());
}
```

It is important to note that you need two arrows since we are using thunk and the syntax will be like so.

2. 
```
export const loadGames = () => async (dispatch) => {
	// code to do your fetch
	 const popularData = await axios.get(popularGamesURL());
	const upcomingData = await axios.get(upcomingGamesURL());

	dispatch({
		type: "FETCH_GAMES",
		payload: {
			popular: popularData,
			upcoming: upcomingData,
		}
	})
}
```

Here we are dispatching the action object with the keys type and payload. It is important to note that we want our payload to hold two types of data, upcoming and popular games. You should be able to do that by having the payload key equal to an object with the keys popular and upcoming and set those equal to its corresponding data.

Now if you do that, you will need to go to your reducer and inside of that return statement for this particular case, you will need to do: 
(Inside of our corresponding reducer)
```
const gamesReducer = (state = initialState, action) => {
	switch(action.type) {
		case "FETCH_GAMES":
			return {...state, popular: action.payload.popular}
	}
}
```

3. It is important to note that in this file we creating an action creator so what is dispatch, we didn’t import it in this file? That’s right you will need to go to the component that you wish to use this action creator on and import the following:
- `import {useDispatch, useSelector} from 'react-redux'`
- `import {loadGames} from '../actions/gamesAction'`

4. From there you need to declare the dispatch `const dispatch = useDispatch()` and inside of an onload useEffect you can do this `dispatch(loadGames())`












