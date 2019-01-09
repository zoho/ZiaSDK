
Zia is an AI-driven platform written in swift for iOS, which uses NLP to solve business case problems. Zia SDK provides access to Zia platform with sleek and lightweight APIs. With Zia SDK integrated to your application, you can make your users converse with Zia, either Chat or Call. Zia can help them with available skills.

## Requirements

1. Xcode 8.3+
2. Swift 4.0+
3. iOS 10+

## Installation

### CocoaPods

```
To integrate ZiaSDK into your Xcode project using CocoaPods, specify it in your Podfile:


source 'https://git.csez.zohocorpin.com/zoho/zohopodspecs.git'
platform :ios, ’10.0'
use_frameworks!

target '<Your Target Name>' do
    pod 'ZiaSDK', '~> 0.0.8’ 
end

Then, run the following command:

$ pod install
```
### Manually

If you prefer not to use any dependency managers, you can integrate Zia SDK into your project manually.
Just add the ZiaSDK.framework into your Embedded Binaries.

#### SDK Usage :

#### Info Plist Settings :

```
ZiaSDK requires the following keys need to be add into info.plist

  Speech Recognition

	Privacy - Speech Recognition Usage Description

  Microphone Access

 	Privacy - Microphone Usage Description

  To Add the Event to Calendar

 	Privacy - Calendars Usage Description
```

### Initialisation

*Implement the below method on application launch,*

```
  Zia.intialize(withAuthHandler: ZiaHandler?, debugMode: .AutoDebug)

```

*This function will initialise the Zia with the handler and debugMode.*

| debugMode  | Description  |   
|---         |---           |
| AutoDebug  | Based on macro (DEBUG) debugging will be automatically handled for development and production.            |   
| Debug      |    Development Mode |   
| Release    |    Production Mode |  

*Create a class that inherit “ZiaHandler” and implement the methods as given below*

```
class ZiaAuthAdapter: ZiaHandler {
    
    //This is used to set the connection type for Zia

    override func getConnectionType() -> ConnectionType {
        return .OAuth
    }

    //This is used to return the OAuth Token from SSOKit

    override func getAuthToken(_ tokenCallBackHandler: @escaping (String?, Error?) -> Void) {
        ZSSOKit.getOAuth2Token { (token, error) in
            tokenCallBackHandler(token,error)
        }
    }

    //To hide the Zia call icon in chat window, return true

    override func shouldHideZiaCall() -> Bool {
        return true
    }

   //To set the client session data
    override func getCustomClientSessionData() -> [String : Any]?
    {
        return nil
    }
    
   //1.To handle the custom data from zia use the below method, return type is UIView(Client can handle the custom data and return view object)

    override func handleZiaResponse(_ actionName: String?, data: 
   [String : AnyObject], customViewWidth : CGFloat, processType : 
   ZiaCustomViewProcessType) -> UIView? {

     return nil
   }

//OBSERVERS

//Use the below methods for observing the action such as Zia chat closed ,Zia Call ended, Zia Call Invoked from chat window,Zia Initialisation Failed 

    override func didEndZiaCall()
    {
        
    }
    
    override func didCloseZiaChat()
    {
        
    }

    override func didInvokeZiacallFromChatWindow()
    {
    }

    override func didZiaIntialisedWith(status : ZiaIntializeStatus)
    {
       	if status == .ZiaUnauthenticated{
	}
    }
}
```

*Following are the connection type supported by Zia,*

| ConnectionType  | Description  |   
|---         |---           |
| OAuth  | Authenticated with Oauth Token            |   
| Auth      |    Authenticated with Auth Token  |   
| None    |    Unauthenticated (For public bot) | 

## ZIA API's

### Zia Build Type

*You set the build type for Zia using the below API,*

```
  Zia.setBuild(type: ZiaBuildType)

```

*Following are the build type you can configure,*

| ZiaBuildType  | Description  |   
|---         |---           |
| Live  | Production            |   
| Local      |    Staging  |   

*By default based on the build type ZIA will point to the below endpoint*

| ZiaBuildType  | EndPoint  |   
|---         |---           |
| Live  | https://zia.zoho.com            |   
| Local      |    https://zia.localzoho.com   |   

*If you want to set the custom domain prefix, base domain, use the below API,*

```

  Zia.setBuildType(type: ZiaBuildType,domainPrefix: String?,baseDomain: String?)

Example :

  	1.Zia.setBuildType(type: .Local, domainPrefix: “ziatest”, baseDomain: nil) 
		This will set as -> https://ziatest.localzoho.com

	2.Zia.setBuildType(type: .Live, domainPrefix: “zia”, baseDomain: nil) 
		This will set as -> https://zia.zoho.com

	3.Zia.setBuildType(type: .Live, domainPrefix: nil, baseDomain: “zoho.com") 
		This will set as -> https://zia.zoho.com


```

### Zia Skill Setup

*To set the skill for Zia, use the below API*

```

  Zia.setSkill(name : String?)

 Example  :

 	Zia.setSkill(name : “zohocrm”)

```

### Zia Scopes

*To get the scopes used in Zia SDK, use the below API*

```
 Zia.getScopes()
 
 Return Type - > String

```

### Voice

*To invoke the zia voice use the below API,*

```
  Zia-Voice helps the user to interact with Zia through voice.

  Zia.call()
```

### Chat

*To invoke the zia chat use the below API,*

```
  Zia-Chat helps the user to interact with Zia through chat window.
  
  Zia.chat()
```

### Mute Zia Call

*To stop Zia recognising on call, use the below API,*

```
  Zia.muteZia()
```

### UnMute Zia Call

*To resume Zia recognising on call, use the below API,*

```
   Zia.unmuteZia()
```

### End Zia Call

*To close/end Zia call, use the below API,*

```
  Zia.endZiacall()
```

### Close Zia Chat

*To close Zia chat, use the below API,*

```
  Zia.closeZiachat()

```

### Zia Clear Data

* To clear all the user data stored in Zia SDK memory, shared 		    preferences and database.Use the below API,*

```
  Zia.clearData()
```

### Enable Help Option in ZiaChat

*To enable help option in Zia chat set the below variable to true*

```
  Zia.shouldShowHelpButton = true
```

### Show Help Screen on "Noaction" in Call screen

*To show help screen on “No action” in Zia Call ,set the below variable to true*

```
  Zia.shouldShowHelpMenuForNoAction = true
```

### Change chat window title

*To Change the chat window title, use the below API*

```
  Zia.setChatWindowTitle(title: "Zia")
```

## Handling Custom Data

As mentioned in above Ziahandler method,the below example show how to handle the custom data from Zia,

```
Example:

class ZiaAdapter: ZiaHandler {
    
    override func handleZiaResponse(_ actionName: String?, data: [String : AnyObject], customViewWidth : CGFloat, processType : ZiaCustomViewProcessType) -> UIView?
    {
	 if let action = actionName,action == "callacustomer"
        {
            if llet record = data[“call_to"] as? [String : AnyObject],let number = record["number"] as? String
            {
                Zia.muteZia() // Mute Zia When Handling the custom data
                
                DispatchQueue.main.async {
                    
                    let phoneStr = "telprompt://\(number)"
                    UIApplication.shared.open(URL(string:phoneStr)!, options: [:], completionHandler: { (status) in
                        if status{
                        }else{
                        }
                    })
                }
            }
        }
	return nil
     }
}

```

## ZIA THEME CUSTOMISATION

*To customise the appearance of Zia chat use the below API,*

```
  Zia.setChatTheme(object: ZiaChatTheme)

```
> ZiaChatTheme - This is an object class which holds the instance to customise the Navigation bar,Chat window,Chat bubble.

*Following are the examples, how to change the appearance for each 
 components*

```
Navigation Bar

let ziaChatNavigationBar = ZiaChatNavigationBar()

ziaChatNavigationBar.chatNavigationBarShadowColor = UIColor.black        	

ziaChatNavigationBar.chatNavigationBarShadowOpacity = 0.3 	

ziaChatNavigationBarchatNavigationBarShadowRadius = 1

ziaChatNavigationBarchatNavigationBarShadowOffset = CGSize(width: 0, height: 1)

ziaChatNavigationBar.chatNavigationBarTitleColor = UIColor.black

ziaChatNavigationBar.chatNavigationBarTitleFont = 
UIFont.systemFont(ofSize: 18,weight: .medium)

ziaChatNavigationBar.chatRightBarButtonTextColor = UIColor.outgoingColor

ziaChatNavigationBar.chatLeftBarButtonTextColor = UIColor.outgoingColor

ziaChatNavigationBar.chatLeftBarButtonFont = 
UIFont.systemFont(ofSize: 18,weight: .regular)

Chat Window

let ziaChatWindow = ZiaChatWindow()

ziaChatWindow.chatWindowBackgroundColor = UIColor.white

ziaChatWindow chatWindowInputTextColor = UIColor.black

ziaChatWindow.chatWindowInputFont = MessageWindowProperties.inputFont

ziaChatWindow.chatWindowInputBackgroundColor = UIColor.white

ziaChatWindow.chatWindowInputBorderColor = UIColor.lightGray

ziaChatWindow.chatWindowInputShadowColor = UIColor.black

ziaChatWindow.chatWindowInputShadowOpacity = 0.3

ziaChatWindow.chatWindowInputShadowRadius = 1

ziaChatWindow.chatWindowInputShadowOffset = 
CGSize(width: 0, height: 1) 
       
ziaChatWindow.chatWindowDateTitleColor = UIColor.messageDateTitleColor

ziaChatWindow.chatWindowDateTitleFont=MessageWindowProperties.caption2BoldFont 							 
    
ziaChatWindow.chatWindowDateSeperatorLineColor = 
UIColor(white: 0.95, alpha: 1.0)

ziaChatWindow.chatWindowNewConversationColor = 
UIColor(red: 216/255.0, green:60/255.0, blue: 126/255.0, alpha: 1.0)

ziaChatWindow.chatWindowInputSendButtonDisableColor = UIColor.lightGray

ziaChatWindow.chatWindowInputSendButtonEnableColor = UIColor.outgoingColor

ziaChatWindow.chatWindowPromptSubmitButtonEnableBgColor = UIColor.outgoingColor

ziaChatWindow.chatWindowPromptSubmitButtonDisableBgColor = 
UIColor(red:170.0/255.0, green: 170.0/255.0, blue: 170.0/255.0,alpha:1.0)

ziaChatWindow.chatWindowPromptSubmitButtonTextColor = UIColor.white

ziaChatWindow.chatWindowPromptSubmitButtonFont = 
UIFont.systemFont(ofSize:18.0, weight:UIFont.Weight.medium)

ziaChatWindow.chatWindowPromptDiscardButtonBgColor = 
UIColor(red: 84.0/255.0,green: 87.0/255.0, blue: 102.0/255.0, alpha: 1.0)

ziaChatWindow.chatWindowPromptDiscardButtonTextColor = UIColor.white

ziaChatWindow.chatWindowPromptDiscardButtonFont = 
UIFont.systemFont(ofSize:19.3, weight: UIFont.Weight.medium)

ziaChatWindow.chatWindowHelpButtonButtonDisableColor = UIColor.white

ziaChatWindow.chatWindowScrollToLastMessageTextColor = UIColor.white

ziaChatWindow.chatWindowScrollToLastMessageBackgroundColor = UIColor.white

ziaChatWindow.chatWindowTipViewBackgroundColor = UIColor.white

ziaChatWindow.chatWindowTipLabelTextColor = UIColor.black

ziaChatWindow.chatWindowTipLabelTextFont = UIFont.systemFont(ofSize: 15.0, weight: UIFont.Weight.medium)

Chat Bubble

let ziaChatBubble = ZiaChatBubble()

ziaChatBubble.chatbubbleShadowColor = UIColor.black
	
ziaChatBubble.chatbubbleShadowOpacity = 0.3

ziaChatBubble.chatbubbleShadowRadius = 1 

ziaChatBubble.chatbubbleShadowOffset = CGSize(width: 0, height: 1)

ziaChatBubble.chatbubbleOutgoingBgcolor = UIColor.outgoingColor

ziaChatBubble.chatbubbleOutgoingTextcolor = UIColor.white

ziaChatBubble.chatbubbleIncomingBgcolor = UIColor.incommingColor

ziaChatBubble.chatbubbleIncomingTextcolor = UIColor.black

ziaChatBubble.chatbubbleTextFont=MessageWindowProperties.subtitleFont

ziaChatBubble.chatbubbleIncomingImageColor = UIColor.white

ziaChatBubble.chatbubbleIncomingImageBgColor = UIColor.outgoingColor

Chat Window Onboarding

let chatOnboardingWindow = ZiaChatOnBoardingWindow()

chatOnboardingWindow.titleColor = UIColor.black

chatOnboardingWindow. titleFont= MessageWindowProperties.subtitleFont

chatOnboardingWindow.descriptionTextColor = UIColor.black

chatOnboardingWindow.descriptionTextFont=MessageWindowProperties.subtitleFont

chatOnboardingWindow.closeButtonColor = UIColor.black

chatOnboardingWindow.onboardingBackgroundColor = UIColor.black

Setting Chat Theme

let ziaCustomTheme = ZiaChatTheme()

ziaCustomTheme.chatNavigationBar = ziaChatNavigationBar
ziaCustomTheme.chatWindow = ziaChatWindow
ziaCustomTheme.chatBubble = ziaChatBubble
ziaCustomTheme.chatOnboardingWindow = ziaChatOnboardingWindow

Zia.setChatTheme(object: ziaCustomTheme)

```
