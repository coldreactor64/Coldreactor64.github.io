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

### Prerequisites:
* A Google Account to use with Firebase
* [React-Native](https://facebook.github.io/react-native/docs/getting-started) cli needs to be installed. This will **not** work with Expo.
* MacOS with XCode [iOS Only]
* CocoaPods needs to be installed on your system [iOS Only]

### Download the Starter Code:

1. Git Clone or download the React Native Firebase Starter by running the command ``git clone https://github.com/invertase/react-native-firebase-starter.git``
   
2. Enter the directory using the command line and run your favourite package manager yarn or npm ``yarn`` or ``npm install``

3. Then enter the ios directory and run ``pod install`` [iOS Only]

4. Move back to the main directory: ``cd ../``
5. Run their rename script and enter the Project Name and Company Name, which in this case is Rocket Watcher and Coldreactor respectively.

### Create a Firebase Project:

1. Go to https://firebase.google.com/ and create a project the name of your app, in this case Rocket Watcher.

2. Add a firebase iOS app using the ``com.companyName.projectName`` that was given above, in my case ``com.coldreactor.rocketwatcher`` by clicking on the button that says iOS [iOS Only]

3. Download the GoogleService-Info.plist and add it to the iOS directory of the starter [iOS Only]


4. Do the same as above but instead of iOS choose android, and instead of the iOS folder place the downloaded file into /android/app/ folder of the project


