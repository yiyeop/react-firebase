# react-firebase

#### 리액트에서 firebase 사용하기 위해 정리해놓은 겁니다.

#### sdk 설치
```
yarn add firebase
```

#### src 경로에 .env에 config 정보 카피 해놓자
```js
// firebase.js
import app from 'firebase/app';
import 'firebase/firestore';
import 'firebase/auth';
import 'firebase/storage';

const config = {
  apiKey: process.env.REACT_APP_API_KEY,
  authDomain: process.env.REACT_APP_AUTH_DOMAIN,
  databaseURL: process.env.REACT_APP_DATABASE_URL,
  projectId: process.env.REACT_APP_PROJECT_ID,
  storageBucket: process.env.REACT_APP_STORAGE_BUCKET,
  messagingSenderId: process.env.REACT_APP_MESSAGING_SENDER_ID,
};

class Firebase {
  constructor() {
    app.initializeApp(config);

    this.storage = app.storage();
    this.auth = app.auth();
    this.db = app.firestore();
    this.db.settings({ timestampsInSnapshots: true });
  }
}

export default Firebase;
```

#### Context 제공하는 파일 하나 만든다.
```js
// context.js
import React from 'react';

const FirebaseContext = React.createContext(null);

export const withFirebase = Component => props => (
  <FirebaseContext.Consumer>
    {firebase => <Component {...props} firebase={firebase} />}
  </FirebaseContext.Consumer>
);

export default FirebaseContext;
```

#### 합쳐서 export용
```js
// index.js
import FirebaseContext, { withFirebase } from './context';
import Firebase from './firebase';

export default Firebase;

export { FirebaseContext, withFirebase };

```

#### 자이제 index.js 파일에 넣자
#### 파이어베이스 인스턴스는 하나만 잇어야돼서 저럼..
```js
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import Firebase, { FirebaseContext } from "./firebase";

ReactDOM.render(
  <FirebaseContext.Provider value={new Firebase()}>
    <App />
  </FirebaseContext.Provider>,
  document.getElementById("root")
);
```
#### 적용은 끝났

#### Usage
```js
// HOC ~
export default withFirebase(StoreContainer);

// React Hooks 로 사용 시
// 함수형 컴포넌트 안에 useContext로 
const firebase = useContext(FirebaseContext);

```

#### example
```js
// 파이어스토어에서 데이터 가져오는 샘플
const loadData = () => {
    let data = [];
    this.props.firebase.db
      .collection("users")
      .get()
      .then(snapshots => {
        snapshots.forEach(doc => {
          data.push({ ...doc.data(), id: doc.id });
        });
        this.setState({
          data
        });
      })
      .catch(err => console.log(err));
  };
```
