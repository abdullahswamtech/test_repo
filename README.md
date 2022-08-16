# Fortnight iOS Template

Fortnight iOS Template is a reference app which contains most commonly used features in mobile apps such as signup/login, social Login, theme management etc. It is built to save development time. UI is also implemented.

## Design Pattern
 - MVVM using `Combine` framework.
 - UI built programatically using `UIKit` and `Anchorage`
 
### Code structure explanation
Each screen has a [view controller](#--view-controller-structure) and a [view model](#--view-model-structure). View controller is responsible to send data to view model and display data received from view model. View model is responsibile to recieve data from view controller, manipulate it and send back to view controller to be displayed. See below code snippet to have an idea how MVVM works in this app.
(Read code comments for clarification)

 #### - View Controller Structure
```
import UIKit
import Anchorage
import Combine

class ExampleViewController: UIViewController {
    
    private let button = UIButton(type: .system)
    private let titleLabel = UILabel()
    
    private let viewModel = ExampleViewModel()
    private var cancelBag = Set<AnyCancellable>()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        configureBindings()
        configureUI()
        updateUI()
    }
    
    private func configureBindings() {
        // listen for the events sent by view model
        viewModel.output.sink { [weak self] output in
            guard let strongSelf = self else { return }
            switch output {
            case .action1Completed:
                strongSelf.updateUI()
            }
        }.store(in: &cancelBag)
    }
    
    private func configureUI() {
        configureTitleLabel()
        configureButton()
    }
    
    private func configureTitleLabel() {
        titleLabel.text = viewModel.labelText
        titleLabel.font = UIFont.systemFont(ofSize: 24, weight: .bold)
        
        // adding titleLabel to view controller and applying constraints
        view.addSubview(titleLabel)
        titleLabel.centerAnchors == view.centerAnchors
    }
    
    private func configureButton() {
        button.setTitle("< Button >", for: .normal)
        button.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
        
        view.addSubview(button)
        button.centerXAnchor == view.centerXAnchor
        button.topAnchor == titleLabel.bottomAnchor
    }
    
    @objc func buttonTapped(sender: UIButton) {
        viewModel.input(.buttonTapped) // tell view model that button has been tapped
    }
    
    private func updateUI() {
//        super.updateUI() // call this if view controller is child of another view controller
        titleLabel.text = viewModel.labelText
    }
}

```
### - View Model Structure
```
import Foundation
import Combine

final class ExampleViewModel {

    // Enum Input contains all the events that view controller can send to view model
    enum Input {
        case buttonTapped
    }
    
    // Enum Output contains all the events that a view model can send to view controller
    enum Output {
        case action1Completed
    }
    
    // _output is used to send events to view controller
    private let _output = PassthroughSubject<Output, Never>()
    
    // output is used by view controller to listen for the events sent by view model
    var output: AnyPublisher<Output, Never> { _output.eraseToAnyPublisher() }
    
    private var action1Success = false
    var labelText: String{ return action1Success ? "Action completed successfully" : "Action failed" }
    
    init(){}
    
    // input() is used by view controller to send events to view model
    func input(_ input : Input) {
        switch input {
        case .buttonTapped:
            performSomeAction()
        }
    }
    
    private func performSomeAction() {
        action1Success = Bool.random()
        self._output.send(.action1Completed) // tell view controller that action has been completed
    }
}

```

## Things to Change 
1. Change bundle id in 'General' tab of targets
2. Change display name in `Targets -> General -> Identity -> Display Name`. If you want to change app name in complete app then follow steps at this [link](https://stackoverflow.com/a/239006/19673742)
3. Change 'baseURL' in MainAPIClient.swift file
4. Change theme colors in assets to match with design
5. Configure relevant developer consoles for social logins

## List of Features
 - [Theme Management](#theme-management)
 - [Network Management](#network-management)
 - [Signup / Login](#signuplogin)
 - [Google Login](#google-login)
 - [Facebook Login](#facebook-login)
 - [Apple Login](#apple-login)
 - [Firebase Authentication](#firebase-authentication)
 
 ## Features Management Explanation
### Theme Management
- Dark and light modes are handled. Color schemes can be changed with minimal effort by just changing colors in Assets.
- Add new colors to ThemeManager and assets if it is not already present there.
- To change theme, assign new value to "ThemeManager.shared.currentTheme" and call ViewController.update()
### Network Management
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
 - Signup, sign in and forgot password functionalities are implemented including UI. Just change 'baseURL' in MainAPIClient.swift file.

### Google Login
 1. Open your project configuration: double-click the project name in the left tree view. Select your app from the TARGETS section, then select the Info tab, and expand the URL Types section.
 2. Change client ID URL scheme to reversed clientID. The reversed client ID is your client ID with the order of the dot-delimited fields reversed.
  Reference: https://developers.google.com/identity/sign-in/ios/start-integrating
 
### Facebook Login
  1. Add Facebook Signin capability to your app by following instructions at [this link](https://developers.facebook.com/docs/facebook-login/ios/). Skip steps 2, 4.2, 5, 6 and 7
  2. Once you create an app at facebook developers console, go to app dashboard, copy the App ID and paste it setAppID() method in SocialAuthManager

### Apple Login
  1. Enable your app's Signin with Apple capability in App developer account
  2. In your project, add signin with apple capability in `Targets -> Signing & Capabilities`
  3. Click + button, search for Signin with Apple and double click to add capability

### Firebase Authentication
1. Create a firebase project at [Firebase Console](https://console.firebase.google.com/u/0/)
2. Enable Providers that you want to integrate 
    1. Go to firebase console
    2. select project
    3. select Authentication from the left pane
    4. select signin Methods tab
    5. click add new provider and select desired providers (Facebook, Google, Apple)
