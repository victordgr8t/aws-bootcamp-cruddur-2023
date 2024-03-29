# Week 3 — Decentralized Authentication

In week 3 of my AWS Cloud Project Bootcamp, I completed several tasks related to Amazon Cognito and authentication. First, I provisioned an Amazon Cognito user pool using ClickOps. Then, I installed and configured the Amplify client-side library for Amazon Cognito.

Next, you implemented API calls to Amazon Cognito for custom login, signup, recovery, and forgot password pages. I also demonstrated how to display conditional elements and data based on whether the user is logged in or logged out.

Finally, I verified JWT tokens server-side to serve authenticated API endpoints in a Flask application. Overall, I made significant progress in setting up a secure and reliable authentication system for your project.


# Tasks completed

### Install AWS Amplify

```sh
   npm i aws-amplify --save
```

## Provision Cognito User Group

We will create a user pool using the aws management console

<img width="1281" alt="Screen Shot 2023-03-16 at 3 12 00 PM" src="https://user-images.githubusercontent.com/63635704/225627681-b5c05376-6a1e-4be7-83f5-f7d7bc27da33.png">

# Configure Amplify

We need to hook up our cognito pool to our code in the ```App.js```

```js
    import { Amplify } from 'aws-amplify';

    Amplify.configure({
      "AWS_PROJECT_REGION": process.env.REACT_APP_AWS_PROJECT_REGION,
      "aws_cognito_identity_pool_id": process.env.REACT_APP_AWS_COGNITO_IDENTITY_POOL_ID,
      "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
      "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
      "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
      "oauth": {},
      Auth: {
        region: process.env.REACT_APP_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
        userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
        userPoolWebClientId: process.env.REACT_APP_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
      }
    });

```
Add this variables in docker-compose.yml (fronten section)

```
   REACT_APP_AWS_PROJECT_REGION: "${AWS_DEFAULT_REGION}"
   REACT_APP_AWS_COGNITO_REGION: "${AWS_DEFAULT_REGION}"
   REACT_APP_AWS_USER_POOLS_ID: "us-east-1_HPXmaEWDj"
   REACT_APP_CLIENT_ID: "2es1tl395fituvl8rlho380479"
```

# Conditionally show components based on logged in or logged out

Inside our ```HomeFeedPage.js```

```js
  import { Auth } from 'aws-amplify';

  // set a state
  const [user, setUser] = React.useState(null);

  // check if we are authenicated
  const checkAuth = async () => {
    Auth.currentAuthenticatedUser({
      // Optional, By default is false. 
      // If set to true, this call will send a 
      // request to Cognito to get the latest user data
      bypassCache: false 
    })
    .then((user) => {
      console.log('user',user);
      return Auth.currentAuthenticatedUser()
    }).then((cognito_user) => {
        setUser({
          display_name: cognito_user.attributes.name,
          handle: cognito_user.attributes.preferred_username
        })
    })
    .catch((err) => console.log(err));
  };

  // check when the page loads if we are authenicated
  React.useEffect(()=>{
    loadData();
    checkAuth();
  }, [])

```
 We'll want to pass user to the following components:
 ```js
     <DesktopNavigation user={user} active={'home'} setPopped={setPopped} />
     <DesktopSidebar user={user} />
 
 ```

We'll rewrite ```DesktopNavigation.js``` so that it it conditionally shows links in the left hand column on whether you are logged in or not.

Notice we are passing the user to ProfileInfo
```js
    import './DesktopNavigation.css';
    import {ReactComponent as Logo} from './svg/logo.svg';
    import DesktopNavigationLink from '../components/DesktopNavigationLink';
    import CrudButton from '../components/CrudButton';
    import ProfileInfo from '../components/ProfileInfo';

    export default function DesktopNavigation(props) {

      let button;
      let profile;
      let notificationsLink;
      let messagesLink;
      let profileLink;
      if (props.user) {
        button = <CrudButton setPopped={props.setPopped} />;
        profile = <ProfileInfo user={props.user} />;
        notificationsLink = <DesktopNavigationLink 
          url="/notifications" 
          name="Notifications" 
          handle="notifications" 
          active={props.active} />;
        messagesLink = <DesktopNavigationLink 
          url="/messages"
          name="Messages"
          handle="messages" 
          active={props.active} />
        profileLink = <DesktopNavigationLink 
          url="/@andrewbrown" 
          name="Profile"
          handle="profile"
          active={props.active} />
      }

      return (
        <nav>
          <Logo className='logo' />
          <DesktopNavigationLink url="/" 
            name="Home"
            handle="home"
            active={props.active} />
          {notificationsLink}
          {messagesLink}
          {profileLink}
          <DesktopNavigationLink url="/#" 
            name="More" 
            handle="more"
            active={props.active} />
          {button}
          {profile}
        </nav>
      );
    }

```

We'll update ```ProfileInfo.js```
 ```js
    import { Auth } from 'aws-amplify';  //replace import Cookies from 'js-cookie'

    const signOut = async () => {
      try {
          await Auth.signOut({ global: true });
          window.location.href = "/"
      } catch (error) {
          console.log('error signing out: ', error);
      }
    }
 
 ```
 
 We'll rewrite ```DesktopSidebar.js``` so that it conditionally shows components in case you are logged in or not.
```js
    import './DesktopSidebar.css';
    import Search from '../components/Search';
    import TrendingSection from '../components/TrendingsSection'
    import SuggestedUsersSection from '../components/SuggestedUsersSection'
    import JoinSection from '../components/JoinSection'

    export default function DesktopSidebar(props) {
      const trendings = [
        {"hashtag": "100DaysOfCloud", "count": 2053 },
        {"hashtag": "CloudProject", "count": 8253 },
        {"hashtag": "AWS", "count": 9053 },
        {"hashtag": "FreeWillyReboot", "count": 7753 }
      ]

      const users = [
        {"display_name": "Andrew Brown", "handle": "andrewbrown"}
      ]

      let trending;
      if (props.user) {
        trending = <TrendingSection trendings={trendings} />
      }

      let suggested;
      if (props.user) {
        suggested = <SuggestedUsersSection users={users} />
      }
      let join;
      if (props.user) {
      } else {
        join = <JoinSection />
      }

      return (
        <section>
          <Search />
          {trending}
          {suggested}
          {join}
          <footer>
            <a href="#">About</a>
            <a href="#">Terms of Service</a>
            <a href="#">Privacy Policy</a>
          </footer>
        </section>
      );
    }

```


# Signin Page

```js
      import { Auth } from 'aws-amplify';

      const [Errors, setErrors] = React.useState('');

      const onsubmit = async (event) => {
        setErrors('')
        event.preventDefault();
        try {
          Auth.signIn(username, password)
            .then(user => {
              localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
              window.location.href = "/"
            })
            .catch(err => { console.log('Error!', err) });
        } catch (error) {
          if (error.code == 'UserNotConfirmedException') {
            window.location.href = "/confirm"
          }
          setErrors(error.message)
        }
        return false
      }

      let errors;
      if (Errors){
        errors = <div className='errors'>{Errors}</div>;
      }

      // just before submit component
      {errors}


```

I got an error while trying to login. Error-  "Incorrect username or password ... "

<img width="1325" alt="Screen Shot 2023-03-16 at 3 27 20 PM" src="https://user-images.githubusercontent.com/63635704/225631727-82f9c65f-ee35-4986-8771-8911c7e6ce11.png">

I force the password change with this command:

``` aws cognito-idp admin-set-user-password --user-pool-id us-east-1_Y6OvSWej5 --username sparkmind --password Obama456! --permanent ```

If done correctly the login credential would appear just like below:

<img width="1125" alt="Screen Shot 2023-03-18 at 11 49 59 AM" src="https://user-images.githubusercontent.com/63635704/226098234-17290de6-f669-41b6-b01f-0036617bae1f.png">


# Signup Page
On ```signup.js``` file we will update our code 
```js
      import { Auth } from 'aws-amplify';

      const [Errors, setErrors] = React.useState('');

      const onsubmit = async (event) => {
        event.preventDefault();
        setCognitoErrors('')
        try {
            const { user } = await Auth.signUp({
              username: email,
              password: password,
              attributes: {
                  name: name,
                  email: email,
                  preferred_username: username,
              },
              autoSignIn: { // optional - enables auto sign in after user is confirmed
                  enabled: true,
              }
            });
            console.log(user);
            window.location.href = `/confirm?email=${email}`
        } catch (error) {
            console.log(error);
            setErrors(error.message)
        }
        return false
      }

      let errors;
      if (Errors){
        errors = <div className='errors'>{Errors}</div>;
      }

      //before submit component
      {errors}

```

# Confirmation Page
In our confirmation page we need to update it with some codes

```js
   import { Auth } from 'aws-amplify';
   
   const resend_code = async (event) => {
  setErrors('')
  try {
    await Auth.resendSignUp(email);
    console.log('code resent successfully');
    setCodeSent(true)
  } catch (err) {
    // does not return a code
    // does cognito always return english
    // for this to be an okay match?
    console.log(err)
    if (err.message == 'Username cannot be empty'){
      setCognitoErrors("You need to provide an email in order to send Resend Activiation Code")   
    } else if (err.message == "Username/client id combination not found."){
      setCognitoErrors("Email is invalid or cannot be found.")   
    }
  }
}

const onsubmit = async (event) => {
  event.preventDefault();
  setErrors('')
  try {
    await Auth.confirmSignUp(email, code);
    window.location.href = "/"
  } catch (error) {
    setErrors(error.message)
  }
  return false
}

```

I delete the user I created manually then updated ```SignupPage.js``` file to have the user sign up automatically which will send a verification code to your email

<img width="1057" alt="Screen Shot 2023-03-18 at 1 26 06 PM" src="https://user-images.githubusercontent.com/63635704/226102404-4e0707ce-f837-4a4d-a383-b975da8ed0a2.png">

I logged into the management console to confirm the user signup

<img width="1141" alt="Screen Shot 2023-03-18 at 1 28 09 PM" src="https://user-images.githubusercontent.com/63635704/226102826-c3508421-6ca2-47eb-87da-5c4d9599b7ad.png">

I tested the new user Login and it's perfect 

<img width="1113" alt="Screen Shot 2023-03-18 at 1 36 39 PM" src="https://user-images.githubusercontent.com/63635704/226103277-68c0ef82-efdd-4ecc-9125-ac17edd2d88b.png">


# Recovery Page

We will have to update our recoverypage file 

```js
   import { Auth } from 'aws-amplify';

   const onsubmit_send_code = async (event) => {
     event.preventDefault();
     setErrors('')
     Auth.forgotPassword(username)
     .then((data) => setFormState('confirm_code') )
     .catch((err) => setErrors(err.message) );
     return false
   }

   const onsubmit_confirm_code = async (event) => {
     event.preventDefault();
     setErrors('')
     if (password == passwordAgain){
       Auth.forgotPasswordSubmit(username, code, password)
       .then((data) => setFormState('success'))
       .catch((err) => setErrors(err.message) );
     } else {
       setErrors('Passwords do not match')
     }
     return false
   }

```

I click on forgot password which took me the recovery password page and sent a verification code to my email. 

<img width="1022" alt="Screen Shot 2023-03-18 at 2 29 30 PM" src="https://user-images.githubusercontent.com/63635704/226106277-5819c982-9a6e-473f-a3ec-85b4f6b5c153.png">

After inputing the verification code and new password, I got a succes message as shown below

<img width="1159" alt="Screen Shot 2023-03-18 at 2 30 08 PM" src="https://user-images.githubusercontent.com/63635704/226106280-5e4e41e7-6cee-4bc5-8d03-e42ffadc7990.png">


# Authenticating Server Side

In ```app.py``` I updated cors wiht the code below, hence i was getting a cors error 

```
   cors = CORS(
     app, 
     resources={r"/api/*": {"origins": origins}},
     headers=['Content-Type', 'Authorization'], 
     expose_headers='Authorization',
     methods="OPTIONS,GET,HEAD,POST"
)

```


