# Fortnight iOS Template

Fortnight iOS Template is a reference app which contains most commonly used features in mobile apps such as signup/login, social Login, Theme management etc. It is built to save development time. UI is also implemented.
 
## Architecture
 - MVVM using `Combine` framework.
 - UI built programatically using `UIKit` and `Anchorage`
## List of Features
 - Theme Management
 - Network Management
 - Signup / Login
 - Google login
 - Facebook Login
 - Apple Login
 - Firebase Authentication
 
 
### Theme Management
- Dark and light modes are handled. Color schemes can be changed with minimal effort by just changing colors in Assets. 
- Steps to integragte theme management :
    1. Copy ThemeManager.swift file from 'Managers' directory. 
    2. Go to assets in Fortnight iOS Template, drag and drop Colors folder to your app assets
    3. Use predefined colors such as screenBackgroundColor, labelTextColor, primaryButtonTextColor etc from ThemeManager whenever a color is needed anywhere in the app. Change color values in assets when needed.
    4. Add new colors to ThemeManager and assets if it is not already present there.
    5. Colors can be accessed like this "ThemeManager.shared.currentTheme.colorName"
    6. To change theme, assign new value to "ThemeManager.shared.currentTheme" and call ViewController.update()
### NetworkManagement
 - The network layer is managed in the Networking directory. 
    1. Copy Networking directory to your app
    2. Change 'baseURL' in MainAPIClient.swift file
    3. Add requests in requests folder, organised in subdirectories of relevant category (e.g Networking -> Requests -> Authentication contains all requests related to user authentication). See already existing request as reference
    4. Usage example
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
 - Signup, sign in and forgot password functionalities are implemented including UI. To use it:
    1. copy the Controllers -> Authentication directory to your project and call SignUpViewController. 
    2. Make sure you have added Networking directory, in order for sign up related APIs work properly
    3. Add AuthManager from Managers directory

### Google login
 1. Add Google Signin capability to your app by following instructions at this link https://developers.google.com/identity/sign-in/ios/start-integrating
 2. Add SocialAuthManager.swift from Managers directory. (Remove code for other logins if not needed)
 3. In viewDidLoad call SocialAuthManager.shared.setPresentingViewController
 4. Call SocialAuthManager.shared.performSignin() for signin and SocialAuthManager.shared.logout() to signout
 
### Facebook Signin
  1. Add Facebook Signin capability to your app by following instructions at this link https://developers.facebook.com/docs/facebook-login/ios/
  2. Create an app on Facebook developers console by following instructions at this link https://developers.facebook.com/docs/development/create-an-app/
  3.  Add SocialAuthManager.swift from Managers directory to your project. (Remove code for other logins if not needed)
  4. Go to app dashboard at developer console, copy the App ID and paste it setAppID() method in SocialAuthManager
  5. In viewDidLoad call SocialAuthManager.shared.setPresentingViewController
  6. Call SocialAuthManager.shared.performSignin() for signin and SocialAuthManager.shared.logout() to signout
  
### Apple Signin
  1. Enable your app's Signin with Apple capability in App developer account
  2. In your project, add signin with apple capability in Targets -> Signing & Capabilities
  3. Click + button, search for Signin with Apple and double click to add capability
  4. Add SocialAuthManager.swift from Managers directory to your project
  5. In viewDidLoad call SocialAuthManager.shared.setPresentingViewController
  6. Call SocialAuthManager.shared.performSignin() for signin and SocialAuthManager.shared.logout() to signout

### Firabase Authentication
1. Create a firebase project at https://console.firebase.google.com/u/0/
2. Add Firebase Authentication to your app using Cocoapods `pod 'FirebaseAuth'`
3. Enable Providers that you want to integrate 
    1. Go to firebase console
    2. select project
    3. select Authentication from the left pane
    4. select signin Methods tab
    5. click add new provider and select desired providers (Facebook, Google, Apple)
3. Import FirebaseCore in AppDelegate and call FirebaseApp.configure() `application:didFinishLaunchingWithOptions:` to initialize FirebaseApp object
4. Add SocialAuthManager.swift from Managers directory to your project
  5. In viewDidLoad call SocialAuthManager.shared.setPresentingViewController
  6. Call SocialAuthManager.shared.performSignin() for signin and SocialAuthManager.shared.logout() to signout
