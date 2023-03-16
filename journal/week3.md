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

      const [cognitoErrors, setCognitoErrors] = React.useState('');

      const onsubmit = async (event) => {
        setCognitoErrors('')
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
          setCognitoErrors(error.message)
        }
        return false
      }

      let errors;
      if (cognitoErrors){
        errors = <div className='errors'>{cognitoErrors}</div>;
      }

      // just before submit component
      {errors}


```

I got an erro while trying to login. Error-  "Incorrect username or password ... "

<img width="1325" alt="Screen Shot 2023-03-16 at 3 27 20 PM" src="https://user-images.githubusercontent.com/63635704/225631727-82f9c65f-ee35-4986-8771-8911c7e6ce11.png">

















