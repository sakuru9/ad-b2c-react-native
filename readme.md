React Native Azure AD B2C solution using Pure JS. If you are using expo you dont need to eject. Following explains the usage. Feel free to contribute or sponsor. Thanks

Thanks to https://github.com/sonyarouje/react-native-ad-b2c and https://github.com/wkh237/react-native-azure-ad packages for the inspiration.

### Login screen
```
import React from 'react';
import { Alert } from 'react-native';
import { LoginView } from 'ad-b2c-react-native';
import * as SecureStore from 'expo-secure-store';

export default class Login extends React.PureComponent {
  static navigationOptions = { header: null };

  constructor(props) {
    super(props);
    this.onLogin = this.onLogin.bind(this);
    this.onFail = this.onFail.bind(this);
    this.spinner = this.spinner.bind(this);
  }

  onLogin() {
    const { navigation } = this.props;
    navigation.navigate('App');
  }

  onFail(reason) {
    Alert.alert(reason);
  }

  spinner() {
      return (
        <CView>
            <Spinner />
        </CView>
    );  
  }

  render() {
      //apart from these props you can use any webview props
      //for secureStore, you can pass expo's secure store or create your own wrapper, 
      //which implements deleteItemAsync(key), getItemAsync(key), setItemAsync(key, data)
    
    return (
      <LoginView
        appId="myAppId"
        redirectURI="myRedirectURI"
        tenant="myTenant"
        loginPolicy="B2C_1_SignUpSignIn"
        passwordResetPolicy="B2C_1_PasswordReset"
        profileEditPolicy="B2C_1_ProfileEdit"
        onSuccess={this.onLogin}
        onFail={this.onFail}
        secureStore={MySecureStore}
        renderLoading={spinner}
      />
    );
  }
}

```

### Logout screen
```
import React from 'react';
import { Alert } from 'react-native';
import {LogoutView} from 'ad-b2c-react-native';

export default class Logout extends React.PureComponent {
  static navigationOptions = { header: null };

  constructor(props) {
    super(props);
    this.onSuccess = this.onSuccess.bind(this);
    this.onFail = this.onFail.bind(this);
    this.spinner = this.spinner.bind(this);
  }

  onSuccess() {
    const { navigation } = this.props;
    navigation.navigate('Auth');
  }

  onFail(reason) {
    Alert.alert(reason);
  }

  spinner() {
      return (
        <CView>
            <Spinner />
        </CView>
    );  
  }

  render() {
    return <LogoutView 
             onSuccess={this.onSuccess}
             onFail={this.onFail}
             renderLoading={spinner}
            />;
  }
}
```

### Routes
```
const AuthStack = createStackNavigator({ Login });
const AppStack = createStackNavigator(
  {
    Home,
    Logout,
  }
);

const navigator = createSwitchNavigator(
  {
    App: AppStack,
    Auth: AuthStack,
  },
  {
    initialRouteName: 'Auth',
  },
);
const routes = createAppContainer(navigator);
```

### Get Access Token

Call  adService.getAccessTokenAsync() anywhere in your solution to get latest access token. The token is cached and if expired will use refresh token to get new one seamlessly. Make sure to await or use promise api to use the method.

```
import { adService } from 'ad-b2c-react-native';

await adService.getAccessTokenAsync();
```

### Get ID Token

The idToken is retrievable only after a successful login. Decode it using the library of choice to get user info

```
import { adService } from 'ad-b2c-react-native';

await adService.getIdToken();
```