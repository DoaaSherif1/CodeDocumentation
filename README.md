# Code Documentation
# Chapter 1   
## Installation
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

# Chapter 2
## Redux APIs
The Redux API surface is tiny. Redux defines a set of contracts for you to implement (such as reducers) and provides a few helper functions to tie these contracts together.
This section documents the complete Redux API. Keep in mind that Redux is only concerned with managing the state. In a real app, you'll also want to use UI bindings like react-redux.

## Importing
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

## Functions
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

This function sends to the backend server a GET Request to check the users table and collect all the users' IDs and then gives an order for taking a snapshot of users' data in order to map (organise) them as every id is next to its user data and store them in the variable USERS_STATE_CHANGE.


- fetchUsersData
```
export function fetchUsersData(uid){
    return ((dispatch,getState) =>{
        const found = ( getState().usersState && getState().usersState.users ) ? getState().usersState.users.some(el => el.uid === uid): [];
        console.log(found )
       if(!found){
        firebase.firestore()
        .collection("users")
        .doc(firebase.auth().currentUser.uid)
        .get()
        .then((snapshot) => {
            if (snapshot.exists) {
                let user= snapshot.data();
                user.uid=snapshot.id;
                dispatch({ type: USERS_DATA_STATE_CHANGE,user });
                dispatch(fetchUsersFollowingPosts(uid))
            }
            else{
                console.log('does not exist')
            }
        })

       }
    })
}
```

This function takes a user id as an argument and sends it to the backend server through a GET Request ,it waits for a response then when the response is recieved it checks whether an error has occured or not, if there is an error it logs 'does not exist' onto the console if there is no error the function changes the values of 'user' object with the values recieved from the api .

Parameters:
uid (string): an id of an existing user in the database

required: yes

- fetchUserChats
```
export function fetchUserChats() {
    return ((dispatch) => {
        let listener = firebase.firestore()
            .collection("chats")
            .where("users", "array-contains", firebase.auth().currentUser.uid)
            .orderBy("lastMessageTimestamp", "desc")
            .onSnapshot((snapshot) => {
                let chats = snapshot.docs.map(doc => {
                    const data = doc.data();
                    const id = doc.id;
                    return { id, ...data }
                })

                for (let i = 0; i < chats.length; i++) {
                    let otherUserId;
                    if (chats[i].users[0] == firebase.auth().currentUser.uid) {
                        otherUserId = chats[i].users[1];
                    } else {
                        otherUserId = chats[i].users[0];
                    }
                    dispatch(fetchUsersData(otherUserId, false))
                }

                dispatch({ type: USER_CHATS_STATE_CHANGE, chats })
            })
        unsubscribe.push(listener)
    })
}

```

This function works when the user write any message it takes it and store it in the user chats table by its id and gets the users' messages in descending order attached to their timestamp and takes a snapshot of the data and store it in the variable USER_CHATS_STATE_CHANGE .


- fetchUserPosts
```
export function fetchUserPosts(){

    return ((dispatch)=>{
        firebase.firestore()
        .collection("posts")
        .doc(firebase.auth().currentUser.uid)
        .collection("event")
        .orderBy("creation","asc")
        .get()
        .then ((snapshot)=>{
            let posts =snapshot.docs.map(doc =>{
                const data = doc.data();
                const id=doc.id;
                return {id,...data}
            })
            dispatch({type:USER_POSTS_STATE_CHANGE,posts})
            
        })
    })
}
```


This function takes the current user id as an argument and sends it to the backend server through a GET Request to check the posts table then the events table and sends back the user's events as a response in ascending order for creation time and takes a snapshot of the data and stores it in the variable USER_POSTS_STATE_CHANGE .


- fetchAllPosts
```
export function fetchAllPosts(){

    return ((dispatch)=>{
        firebase.firestore()
        .collection("posts")
        
        .doc("id")
        .collection("event")
        // .orderBy("creation","asc")
        .get()
        .then ((snapshot)=>{
            let Allposts =snapshot.docs.map(doc =>{
                const data = doc.data();
                const id=doc.id;
                return {id,...data}
            })

            console.log(Allposts)
            dispatch({type:All_POSTS_STATE_CHANGE,Allposts})
            
        })
    })
}
```

This function sends to the backend server a GET Request to check the posts table and the events table and collect all posts' IDs then gives an order for taking a snapshot of posts' data in order to map (organise) them as every id is next to its post data and store them in the variable All_POSTS_STATE_CHANGE.


- fetchUserFollowing
```
export function fetchUserFolowing(){

    return ((dispatch)=>{
        let listener = firebase.firestore()
        .collection("following")
        .doc(firebase.auth().currentUser.uid)
        .collection("userFollowing")
        .onSnapshot ((snapshot)=>{
            let following =snapshot.docs.map(doc =>{
                // const data = doc.data();
                const id=doc.id;
                return {id}
            })

            // console.log(posts)
            dispatch({type:USER_FOLLOWING_STATE_CHANGE,following});
            // for(let i=0; i<following.length; i++){
                for (let i = 0; i < following.length; i++) {
                    dispatch(fetchUsersData(following[i], true));
                }
            
        })
        unsubscribe.push(listener)
    })
}
```

This function used when the user wants to know his following list it checks the following table and userFollowing table and gives an order for making the list by their id and taking a snapshot of the data and sotring it in the variable USER_FOLLOWING_STSTE_CHANGE ,the id is used as a foreign primary key .


- queryUsersByUsername
```
export function queryUsersByUsername(username) {
    return ((dispatch, getState) => {
        return new Promise((resolve, reject) => {
            if (username.length == 0) {
                resolve([])
            }
            firebase.firestore()
                .collection('users')
                .where('username', '>=', username)
                .limit(10)
                .get()
                .then((snapshot) => {
                    let users = snapshot.docs.map(doc => {
                        const data = doc.data();
                        const id = doc.id;
                        return { id, ...data }
                    });
                    resolve(users);
                })
        })
    })
}
```

This function takes a username as an argument and sends it to the backend server through a GET Request to check the users table to know if the username exist or not with limited characters if it exists , it gives an order for taking a snapshot of the data and mapping (organising) it by its id next to it.

Parameters:
username (string): an username of an existing user in the database

required: yes


- sendNotification
```
export const sendNotification = (to, title, body, data) => dispatch => {
    if (to == null) {
        return;
    }

    let response = fetch('https://exp.host/--/api/v2/push/send', {
        method: 'POST',
        headers: {
            Accept: 'application/json',
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            to,
            sound: 'default',
            title,
            body,
            data
        })
    })

}
```

This function contains four parts (to , title , body , data ) if 'to' has a null value it stops and starts over and the response attaches to the resource URL by a post method that sends data to the server to create / update the resource and stores the data in the request body of the HTTP request .


- fetchUsersFollowingPosts
```
export function fetchUsersFollowingPosts(uid){

    return ((dispatch,getState)=>{
        firebase.firestore()
        .collection("posts")
        .doc(uid)
        .collection("event")
        .orderBy("creation","asc")
        .get()
        .then ((snapshot)=>{
            const uid = snapshot.docs[0].ref.path.split('/')[1];
            const user = getState().usersState.users.find(el => el.uid === uid);
            let posts =snapshot.docs.map(doc =>{
                const data = doc.data();
                const id=doc.id;
                return {id,...data,user}
            })
            dispatch({type:USERS_POSTS_STATE_CHANGE,posts,uid})
            
        })
    })
}
```

This function takes the current user id as an argument and sends it to the backend server through a GET Request to check the posts table then the events table and sends back the user's events as a response in ascending order for creation time and takes a snapshot of the data and stores it as each id is next to its username and its data in the variable USERS_POSTS_STATE_CHANGE.



# Chapter 3
## Screens

   ### 3.1. Login 

   - This page has styling that distinguish login page.
   - We used some constants to store data in our database.
        - Constant for e-mail.
        - Constant for password.
        - Constant for signup to enable users that have been registered to use their profiles.
   ```
            function Login (props,{navigation}) {
            const [email, setEmail] = useState('');
            const [password, setPassword] = useState('');



          //   const email =useState();
          //   const password =useState();


          const onSignUp = () => {
            firebase.auth().signInWithEmailAndPassword(email, password)
          }
   ```   
   
   
   

   ### 3.2. Home
   
   - We function (home) to coordinate styling and features of image.
   
      
```
                function Home (props,{navigation}) {


                      const {  item, horizontal, full, style, ctaColor, imageStyle } = props;

                    const imageStyles = [
                      full ? styles.fullImage : styles.horizontalImage,
                      imageStyle
                    ];
                    const cardContainer = [styles.card, styles.shadow, style];
                    const imgContainer = [styles.imageContainer,
                      horizontal ? styles.horizontalStyles : styles.verticalStyles

                    ];

                    const {currentUser,posts,payload,allPosts,users,feed} =props
                    console.log (feed)

```

- We used const (mapStateToProps) to store date about posts and users in database.

```
      const mapStateToProps =(store) => ({
          currentUser: store.user.currentUser,
          posts:store.user.posts,
          following:store.user.following,
          allPosts:store.user.allPosts,

          users:store.user.users,
          feed:store.user.feed,

          // usersLoaded:store.usersState.usersLoaded,
          }
```      



### 3.3.  Profile

  - We used some constants to store data in our database.
 
       1. Constant for setting users.
       2. Constant for applying users. 
           
  - We use some conditions to differentiate between current user or not.
 
```
                 const [following,setFollowing ]= useState(true);
               const [apply,setApply]=useState(true);

                const { navigation, item, horizontal, full, style, ctaColor, imageStyle } = props;

                const imageStyles = [
                  full ? styles.fullImage : styles.horizontalImage,
                  imageStyle
                ];
                const cardContainer = [styles.card, styles.shadow, style];
                const imgContainer = [styles.imageContainer,
                  horizontal ? styles.horizontalStyles : styles.verticalStyles,
                  styles.shadow
                ];
                const [user, setUser] = useState(null);
                   const {currentUser,posts,payload,photo} =props
                  console.log (currentUser)
                  console.log (posts)
                  console.log (photo)

                  console.log (props.following)
                  useEffect(() => {
                    // const { currentUser, posts } = props;

                    if (props.route.params ? props.route.params.item.id : currentUser.uid=== firebase.auth().currentUser.uid) {
                        setUser(currentUser)
                        // setUserPosts(posts)
                        // setLoading(false)
                    }
                    else {
                        firebase.firestore()
                            .collection("users")
                            .doc(props.route.params ? props.route.params.item.id : currentUser.uid)
                            .get()
                            .then((snapshot) => {
                                if (snapshot.exists) {
                                    props.navigation.setOptions({
                                        title: snapshot.data().username,
                                    })

                                    setUser({ uid:props.route.params ? props.route.params.item.id : currentUser.uid, ...snapshot.data() });
                                }
                                // setLoading(false)

                            })
                               }


                    if (props.following.map(a=>a.id).indexOf(props.route.params ? props.route.params.item.id : currentUser.uid) > -1) {
                      setFollowing(true);
                  } else {
                      setFollowing(false);
                  }
                  if (props.following.map(a=>a.id).indexOf(props.route.params ? props.route.params.item.id : currentUser.uid) > -1) {
                    setApply(true);
                } else {
                    setApply(false);
                }

                }, [props.route.params ? props.route.params.item.id : currentUser.uid, props.following])
     
```



### 3.4. Create Event

 - We used some constants to store data in our database :
  
      1. Constant for creating Event title 

      2. Constant for creating Brief Summary for Event

      3. Constant for setting start date and end date for the Event

      4. Constant for selecting number of participants in the Event

      5. Constant for uploading Image about Event

      6. Constant for Event Progress

      7. Constant for task completion
      
```

   const [eventTitle, setEventTitle] = useState('');
  const [briefSummary, setBriefSummary] = useState('');
  const [date, setDate] = useState('');
  const [noParticipants, setNoParticipents] = useState(''); 
  const uploadImage =async()=>{
    const storage = firebase.storage();
    const childPath=`post/${firebase.auth().currentUser.uid}/${Math.random().toString(36)}`;
    const uri =props.route.params.image;
    const response =await fetch(uri);
    const blob = await response.blob();
        const task =firebase
        .storage()
        .ref()
        .child(childPath)
        .put(blob)
    const taskProgress = snapshot =>{
    console.log(`transferred: ${snapshot.bytesTransferred}`)
    }
    const taskCompleted = (snapshot)=>{
        task.snapshot.ref.getDownloadURL().then((snapshot)=>{
            savePostData(snapshot);
            savePostHomeData(snapshot)
            console.log(snapshot)
        })
    }
    const taskError =snapshot =>{
        console.log(snapshot)
    }
```


### 3.5. Create Activity


  - We used some constants to store data in our database :
  
    1. Constant for creating Activity name
    
    2. Constant for setting start date and end date for the Activity
    
    3. Constant for setting Activity location
    
    4. Constant for setting Activity Details
    
```
const [activityName, setActivityName] = useState('')
    const [startDate, setStartDate] = useState('');
    const [endDate, setEndDate] = useState('');
    const [location, setLocation] = useState('');
    const [details, setDetails] = useState('');
    const savePostData=()=>{
      firebase.firestore().collection("posts")
      .doc(firebase.auth().currentUser.uid)
      .collection("event")
      .doc(props.route.params.id)
      .collection("Activities")
```


### 3.6. Choose Goals

- We used a constant to select specific goals in checkbox.

```
const checkboxes = [
  { id: 1, text: "Checkbox 1" },
  { id: 2, text: "Checkbox 2" },
  { id: 3, text: "Checkbox 3" }
]
```
 


       
   
