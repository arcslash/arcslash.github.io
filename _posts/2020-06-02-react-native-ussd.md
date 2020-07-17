---
title: USSD Handling for React Native
published: true
date: 2020-06-02 17:00
category: development
author: yeti
tags: [react-native, ussd]
summary: react-native-ussd library usage and development
---
I was in this current project recently which required functionality to to dial USSD codes to recharge/check balance as well as query telecom provider
for some data, but react native doesn't have a library by default to access ussd functions.[react-native-ussd](https://www.npmjs.com/package/react-native-ussd) is a library to address react native based ussd calls and listeners

Unfortunately I couldn't find a way to read up USSD responses in IOS so the current solution will only be limited to android at this time.Although IOS USSD dialling functions will be added in the future releases of the react-native-ussd. (If anyone like to contribute please let me know as well :) )

Basic Library use starts with adding user permissions to android manifest, which naturally need permission to make calls in order to continue

```xml
<manifest>
<uses-permission android:name="android.permission.CALL_PHONE"/>
<application>
```
Then the Ussd module and ussdEventEmitter should be imported and event listener should be added either as a hook(in functional components) or in component mount in class based components


```js
//Functional Components
import * as React from 'react';
import Ussd, {ussdEventEmitter} from 'react-native-ussd';

export default function App() {
  useEffect(() => {
    // Update the document title using the browser API
    const eventListener = ussdEventEmitter.addListener('ussdEvent', (event) => {
      console.log(event.ussdReply);
   });
  });
}
```


```js
//Class Based Components
import * as React from 'react';
import Ussd, {ussdEventEmitter} from 'react-native-ussd';

export default class App extends React.Component {
componentDidMount(){    
    this.eventListener = ussdEventEmitter.addListener('ussdEvent', (event) => {
       console.log(balance);
    });
  }
}
```
Next the dialling can be done by dial method of Ussd module,Make sure to request for user permission at the runtime, better if this can be handled at app startup

```js
//Functional Components
import * as React from 'react';
import { PermissionsAndroid } from 'react-native';
import Ussd, {ussdEventEmitter} from 'react-native-ussd';

export default function App() {
const dial = async () =>{
    let granted = await PermissionsAndroid.request(
      PermissionsAndroid.PERMISSIONS.CALL_PHONE,
      {
        'title': 'I need to make some calls',
        'message': 'Give me permission to make calls '
      }
    )
  
    if (granted) {
      console.log( "CAN Make Calls" );
      Ussd.dial('*#456#');//add your dilaing code instead of *#456#
    } 
    else {
      console.log( "CALL MAKING Permission Denied" );
    }
  }
}
```

```js
//Class Based Components
import * as React from 'react';
import { PermissionsAndroid } from 'react-native';
import Ussd, {ussdEventEmitter} from 'react-native-ussd';

export default class App extends React.Component {
async dial(){
    let granted = await PermissionsAndroid.request(
      PermissionsAndroid.PERMISSIONS.CALL_PHONE,
      {
        'title': 'I need to make some calls',
        'message': 'Give me permission to make calls '
      }
    )
  
    if (granted) {
      console.log( "CAN Make Calls" );
      Ussd.dial('*#456#');
      
      console.log(this.state.userBalance);
    } 
    else {
      console.log( "CALL MAKING Permission Denied" );
    }
  }
}
```
And that's done it should get USSD responses printed out to console for the dials made using the Ussd.dial() function.