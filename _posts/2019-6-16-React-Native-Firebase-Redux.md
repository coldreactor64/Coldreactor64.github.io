---
layout: post
title:  "Rocket Watcher Part 1"
date:   2019-6-16
---

# Rocket Watcher: Part 1 - Setting up React Native Firebase with Redux

## Part 1: Overview

This is a first series in progress for a App called: Rocket Watcher. The app will give the ability to find the next Rocket Launch and be continually updated with the newest countdown time and infomation about the launch.

In this tutorial, we will be exploring how to setup Firebase with React Native and Redux. This tutorial can easily be adapted to work for other applications and to get you started with this application.

We will be setting up [React-Native-Firebase](https://rnfirebase.io/)
along with [React-Redux-Firebase](https://github.com/prescottprue/react-redux-firebase) and [Redux-Firestore](https://github.com/prescottprue/redux-firestore) to form a near native firebase experience with the syntax of React with the flexibility of Redux. This allows us to do many things including using many of the features of things like Analytics, and Crashlytics that you can do with the native Firebase SDK but not the web SDK. Along with those features, we get the regular Web SDK features and Firestore within Redux for easy state management for things like authentication and the Database.


## Part 2: Setting up React Native Firebase

### Prerequisites

* A Google Account to use with Firebase
* [React-Native](https://facebook.github.io/react-native/docs/getting-started) cli needs to be installed. This will **not** work with Expo.
* MacOS with XCode [iOS Only]
* CocoaPods needs to be installed on your system [iOS Only]

### Download the Starter Code

1. Git Clone or download the React Native Firebase Starter by running the command ``git clone https://github.com/invertase/react-native-firebase-starter.git``

2. Enter the directory using the command line and run your favourite package manager yarn or npm ``yarn`` or ``npm install``

3. Then enter the ios directory and run ``pod install`` [iOS Only]

4. Move back to the main directory: ``cd ../``
5. Run their rename script ``yarn rename`` or ``npm run rename`` and enter the Project Name and Company Name, which in this case is Rocket Watcher and Coldreactor respectively.

### Create a Firebase Project

1. Go to https://firebase.google.com/ and create a project the name of your app, in this case Rocket Watcher. 
![CreateProject](/assets/images/2019/6/16/RocketWatcherPart1/createproject.PNG)

2. Click the button to add Firebase to iOS App
   
3. Add a firebase iOS app using the ``com.companyName.projectName`` that was given above, in my case ``com.coldreactor.rocketwatcher`` by clicking on the button that says iOS [iOS Only]
![CreateProject](/assets/images/2019/6/16/RocketWatcherPart1/addiOS.PNG)


4. Download the GoogleService-Info.plist and add it to the iOS directory of the starter app you downloaded and setup [iOS Only] 
![CreateProject](/assets/images/2019/6/16/RocketWatcherPart1/downloadplist.PNG)

5. Repeat the steps above  but instead of iOS choose android, and instead of the iOS folder place the downloaded file into /android/app/ folder of the project

Run the app, using ``yarn run ios`` or ``yarn run android`` and you should see the list of modules that are installed and that you can use.

## Part 3: Setting up Redux, with Firebase, Firestore, and Thunk

### Prerequisites 

In the section you will have to install multiple packages:

* redux - State Container
* react-redux - React bindings for Redux
* redux-thunk - async Redux using thunks
* react-redux-firebase - React and Redux bindings for firebase
* redux-firestore - Redux bindings for Firestore

To install you can use either one of your favourite package managers

````bash
yarn add redux react-redux redux-thunk react-redux-firebase redux-firestore
````

or

````bash
npm install react-redux redux-thunk react-redux-firebase redux-firestore
````

### Setup Redux with Firebase and Firestore:

Create a Redux directory and files accordingly in the main directory

```
|--redux
    |--actions
    |--reducers
        |--index.js
    |--store.js
```

In store.js we will setup the redux store to use with the application.

````javascript
import { compose, createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import RNFirebase from 'react-native-firebase';
import {reactReduxFirebase, getFirebase} from 'react-redux-firebase';
import {reduxFirestore} from 'redux-firestore'
import reducers from './reducers/index';


const reduxFirebaseConfig = {
    userProfile: 'users', // save users profiles to 'users' collection
};

const rfConfig = {} // optional redux-firestore Config Options

export const middleware = [thunk.withExtraArgument(getFirebase)];//define the middleware
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;//Compose enchancer for Redux Dev Tools

export default (initialState = { firebase: {} }) => {
    // initialize firebase
    const firebase = RNFirebase.app();

    const store = createStore(
        reducers,
        initialState,
        compose(
        reactReduxFirebase(firebase, reduxFirebaseConfig),
        reduxFirestore(firebase,rfConfig), // pass initialized react-native-firebase app instance
        composeEnhancers(applyMiddleware(...middleware)) //Apply middleware
        )
    );

return store;
};
````

I this we definde out middleware with:

```export const middleware = [thunk.withExtraArgument(getFirebase)];```

This defines thunk with a extra argumeent that grabs firebase and allows it to be passed to thunk.

This allows you to use it in a thunk action like this [example](https://react-redux-firebase.com/docs/integrations/thunks.html)

````javascript
const sendNotification = (payload) => ({
  type: NOTIFICATION,
  payload
})

export const addTodo = (newTodo) =>
  (dispatch, getState, getFirebase) => {
    const firebase = getFirebase()
    firebase
      .push('todos', newTodo)
      .then(() => {
        dispatch(sendNotification('Todo Added'))
      })
  };
````

Further on I added a compose enchancer which allows you to use Redux Devtools with Firebase. A good standalone app for react native devtools is [here](https://github.com/jhen0409/react-native-debugger)

Within the arrow function which returns our store we define the firebase instance to pass to the react-redux-firebase instance with `const firebase = RNFirebase.app()`

Further on we define a standard store using the createStore function from redux with special arguments for the enchancers which allow us to use Firebase and Firestore within Redux. We also pass in the specific configuration files that were defined above that allow you to define things like where you store the user data within firestore, other configuration options can be found [here](http://react-redux-firebase.com/docs/api/enhancer.html) for react-redux-firebase and [here](https://github.com/prescottprue/redux-firestore) for redux-firestore

````javascript
    const store = createStore(
        reducers,
        initialState,
        compose(
        reactReduxFirebase(firebase, reduxFirebaseConfig),
        reduxFirestore(firebase, rfConfig), // pass initialized react-native-firebase app instance
        composeEnhancers(applyMiddleware(...middleware)) //Apply middleware
        )
    );
````

Next we will setup the reducers for react-redux-firebase and redux-firestore

In the /redux/reducers/index.js file paste

````javascript
import {firebaseReducer} from 'react-redux-firebase'
import {firestoreReducer} from 'redux-firestore'
import {combineReducers} from 'redux'


export default combineReducers({
    firebase: firebaseReducer,
    firestore: firestoreReducer
})
````

This is a very basic combine reducers function from redux, using firebase and firestore.

### Add Redux to the main application

Here in the final step will add redux to the main file so you can use it throughout the application.

In App.js replace the starting code with


````Javascript 
import React from 'react';
import { StyleSheet, Platform, Image, Text, View } from 'react-native';
import {Provider} from 'react-redux'
import store from './redux/store'

export default class App extends React.Component {
  constructor() {
    super();
    this.state = {};
  }

  render() {
    return (
    <Provider store={store()}>
    <View> <View/>
    </Provider>

    );
  }
}

const styles = StyleSheet.create({

});

````

This imports the store and wraps the main application, and any components within it can use the redux store which now includes Firebase and Firestore.

## Conclusion

Adding Firebase and Firstore to React Native and Redux is a fairly simple endeavour, which will help you immensely if you are using firebase or would like to use firebase in your application. In future tutorials I will be using Firebase and React Native to develop my Rocket Watcher app.


## Further Reading/Resources
* https://rnfirebase.io/
* http://react-redux-firebase.com
* https://github.com/prescottprue/redux-firestore
* https://github.com/reduxjs/redux-thunk
* https://redux.js.org/
* https://react-redux.js.org/

<!-- %enddocs -->
