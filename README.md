# Fortnight iOS Template

Fortnight iOS Template is a reference app which contains most commonly used features in mobile apps such as signup/login, social Login, theme management etc. It is built to save development time. UI is also implemented.
## Architecture
 - MVVM using `Combine` framework.
 - UI built programatically using `UIKit` and `Anchorage`

## How to use template 
1. Change bundle id in 'General' tab of targets
2. Change app name
    1. Go to `Targets -> Build Settings`
    2. Search for Product Name under Packaging. 
    3. Change its value to what you want your new project name to be.
3. Handle features in the next section according to app requirements

## List of Features
 - [Theme Management](#theme-management)
 - [Network Management](#network-anagement)
 - [Signup / Login](#signuplogin)
 - [Google Login](#google-login)
 - [Facebook Login](#facebook-login)
 - [Apple Login](#apple-login)
 - [Firebase Authentication](#firebase-authentication)
 
 
### Theme Management
- Dark and light modes are handled. Color schemes can be changed with minimal effort by just changing colors in Assets.
- Add new colors to ThemeManager and assets if it is not already present there.
- To change theme, assign new value to "ThemeManager.shared.currentTheme" and call ViewController.update()
### NetworkManagement
 - The network layer is managed in the Networking directory. 
    1. Change 'baseURL' in MainAPIClient.swift file
    2. Add requests in requests folder, organised in subdirectories of relevant category (e.g Networking -> Requests -> Authentication contains all requests related to user authentication). See already existing request as reference
    3. Usage example
        ```
        private func signUp() {
            let email = emailViewModel.text.lowercased()
            let password = passwordViewModel.text
            MainAPIClient.shared.execute(.signIn(email: email, password: password)) { [weak self] result in
                guard let strongSelf = self else { return }
                switch result {
                case .failure(let error):
                    print(error.debugDescription)
                    strongSelf._output.send(.signinError(error: error.message))
                case .success(let authResponse):
                    AuthManager.shared.authToken = authResponse.token
                    AuthManager.shared.currentUser = authResponse.user
                    strongSelf._output.send(.signInSuccess)
                    break
                }
            }
        }
        ```

### Signup/Login
 - Signup, sign in and forgot password functionalities are implemented including UI. Just replace 'baseURL' in MainAPIClient.swift file and endpoints for authentication in relevant request files in `Networking -> Requests -> Authentication` directory.

### Google login
 1. Open your project configuration: double-click the project name in the left tree view. Select your app from the TARGETS section, then select the Info tab, and expand the URL Types section.
 2. Click the + button, and add your reversed client ID as a URL scheme.The reversed client ID is your client ID with the order of the dot-delimited fields reversed.
  Reference: https://developers.google.com/identity/sign-in/ios/start-integrating
 
### Facebook Signin
  1. Add Facebook Signin capability to your app by following instructions at [this link](https://developers.facebook.com/docs/facebook-login/ios/). Skip steps 2, 4.2, 5, 6 and 7
  2. Once you create an app at facebook developers console, go to app dashboard, copy the App ID and paste it setAppID() method in SocialAuthManager

### Apple Signin
  1. Enable your app's Signin with Apple capability in App developer account
  2. In your project, add signin with apple capability in `Targets -> Signing & Capabilities`
  3. Click + button, search for Signin with Apple and double click to add capability

### Firabase Authentication
1. Create a firebase project at [Firebase Console](https://console.firebase.google.com/u/0/)
2. Enable Providers that you want to integrate 
    1. Go to firebase console
    2. select project
    3. select Authentication from the left pane
    4. select signin Methods tab
    5. click add new provider and select desired providers (Facebook, Google, Apple)
