# Code Documentation
## Chapter 1   
### Installation
- 	Redux: 
    - Is a predictable state container for JavaScript apps.it helps you write applications that behave consistently, run in different                                           environments(client, server and native    ). On top of that , it provides a great developer experience , such as live code editing                                       combined with a time traveling debugger.  
    
    ```
    npm install  react-redux
    ```
- 	Redux Thunk :
      - thunk middleware for redux it allows writing functions with logic inside that can interact with a redux store’s dispatch and getState methods.
      ```
      npm install redux-thunk
      ```
- 	React native expo
      - Expo CLI :
        Is a command line app that is the main interface between a developer and expo tools .Expo CLI also has a web-based GUI that pops up in your web browser when you         start your project .

      - REQUIREMENTS:
          1.	Node.js LTS release
          2.	Git
       ```
          npm install  -global expo-cli
       ```
       To Open Your Server
       ```
       npm start    
       ```
       OR
       ```
       expo start
       ```
-   React Navigation / Stack
       - Stack Navigator provides a way for your app to transition between screens where each new screen is placed on top of a stack.
       - To use this navigator ,ensure that you have @react-navigation/native  and it dependencies then install react-navigation/stack.
       ```
       npm install @react-navigation/stack
       ```
       You also need to install react-native-gesture-handler as
       ```
       npm install react-native-gesture-handler
       ```
- 	Galio Framework
      - Galio is one of the coolest UI libraries you could ever use , licensed under MIT . Carefully crafted by developers for developers. Ready-made components ,             typography and a base theme that is easily adaptable to each project.
      ```
      npm install   galio-framework
      ```
-  	Fontawesome
       - Is a widely-used icon set that gives you scalable vector images that can be customized with CSS.
       ```
       npm install  -- save react-native-fontawesome
       ```
- 	Gifted Chat
      - The most complete chat UI for React Native.
      - Has many features as:
         - 	Write with TypeScript
         -	Fully customizable components
         -	Composer actions (to attach photos, etc)
         -  Load earlier messages
         -	Copy messages to clipboard
         -	Touchable links using react-native-parsed-text
         -	Avatar as user’s initials
         -	Localized dates
         -	Multi-line TextInput
         -	InputToolbar avoiding keyboard
         -	Redux support
         -	System messages
         -	Quick reply message
         -	Typing indicator react-native-typing-animation
      ```
      npm install  react-native-gifted-chat  --save
      ```
- 	Firebase Config
       - Firebase Remote config is a cloud service that lets you change the behavior and appearance of your app without requiring users to download an app update .              when using Remote config , you create in-app default values that control the behavior and appearance of your app.
       ```
       npm install - - save firebase
       ```
- 	Image  Picker
       - A React Native module that allows you to select a photo/video from the device library or camera.
       ```
        npm install react-native-image-picker
       ```

## Chapter 2
### Redux APIs
The Redux API surface is tiny. Redux defines a set of contracts for you to implement (such as reducers) and provides a few helper functions to tie these contracts together.
This section documents the complete Redux API. Keep in mind that Redux is only concerned with managing the state. In a real app, you'll also want to use UI bindings like react-redux.

### Importing
```
import firebase from 'firebase/compat/app';
```

```
import {USER_STATE_CHANGE, USER_POSTS_STATE_CHANGE, USER_PHOTO_STATE_CHANGE, STORE_USER, USER_FOLLOWING_STATE_CHANGE, USERS_STATE_CHANGE, USER_CHATS_STATE_CHANGE, USERS_DATA_STATE_CHANGE,USERS_POSTS_STATE_CHANGE,All_POSTS_STATE_CHANGE} from '../constants/index'
```

```
import 'firebase/compat/firestore';
```

```
import 'firebase/compat/storage';
```

### Functions
- fetchUser
```
    export function fetchUser() {
    return ((dispatch) => {
       firebase.firestore()
            .collection("users")
            .doc(firebase.auth().currentUser?firebase.auth().currentUser.uid:firebase.auth().currentUser.uid)
            .onSnapshot((snapshot, error) => {
                if (snapshot.exists) {
                    dispatch({ type: USER_STATE_CHANGE, currentUser: { uid: firebase.auth().currentUser.uid, ...snapshot.data() } })
                }
            })
     
    })
    }
```

This function takes a user id as an argument and checks the users table in the database if the id exists it gives an order for taking a snapshot of user's data and store it in the variable USER_STATE_CHANGE and if the id doesn't exist there'll be an error.


- fetchUserPhoto
```
export function fetchUserPhoto() {
    return ((dispatch) => {
       firebase.firestore()
            .collection("users")
            
            .doc(firebase.auth().currentUser?firebase.auth().currentUser.uid:firebase.auth().currentUser.uid)
            .collection('profilePhoto')
         
            .onSnapshot((snapshot, error) => {
                if (snapshot.exists) {
                    dispatch({ type: USER_PHOTO_STATE_CHANGE, photo: { uid: firebase.auth().currentUser.uid, ...snapshot.data() } })
                }
            
            })
     
    })
}
```

This function takes a user id as an argument and checks the users table in the database if the id exists then checks profilePhoto table and gives an order for taking a snapshot of user's profile photo URL and store it in the variable USER_PHOTO_STATE_CHANGEand store every photo next to its user id and the snapshot of data and if the id doesn't exist there'll be an error.


- fetchUsers
```
export function fetchUsers(){

    return ((dispatch)=>{
        firebase.firestore()
        .collection("users")
        .get()
        .then ((snapshot)=>{
            let users =snapshot.docs.map(doc =>{
                const data = doc.data();
                const id=doc.id;
                return {id,...data}
            })
            dispatch({type:USERS_STATE_CHANGE,users})
            
        })
    })
}
```

This function sends to the backend server a GET Request to check the users table and collect 
