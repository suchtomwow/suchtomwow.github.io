---
layout: post
title: Pinboard Tokens and Keychain Access
published: false
date: 2016-04-18 21:25:48 -0600
permalink: /:title
author: Tom Carey
---

I have come to the point in developing Airpin where I need to securely store a user's Pinboard token in the keychain. I have never had to deal with the keychain before and as such, I am not exactly clear on what this will look like.

Pinboard tokens come in the form `username:hash`. Developers are encouraged (🔫) to use tokens instead of a username/password pair to make authenticated requests. The usage of tokens over a username/password pair ensures that if a hacker gets ahold of the token, they can't do any damage with it, as it will only work on Pinboard's site. Secondly, you can reset the token at anytime if it is compromised. All great things. See [API Authentication Tokens](https://blog.pinboard.in/2012/07/api_authentication_tokens/) on the Pinboard Blog for more on the matter.

I have opted to use Matthew Palmer's clever, protocol-oriented keychain wrapper, [Locksmith](https://github.com/matthewpalmer/Locksmith) since it wraps up Apple's nasty C-based API into a very elegant and easy to read Swift API.

For instance, I can create a PinboardAccount struct like this...

    struct PinboardAccount {
      let username: String
      let password: String
  
      init(token: String) {
        let components = token.componentsSeparatedByString(":")
    
        // We can do better than this, but for the purposes of demonstration...
        username = components[0]
        password = components[1]
      }
    }
    
...and instantiate it like this.

    let account = PinboardAccount(token: "tom_carey:123456abcdef")
    
Now, in order to store that token in the keychain, all I have to do with Locksmith is conform to a couple protocols...

    struct PinboardAccount: GenericPasswordSecureStorable, CreateableSecureStorable {

      let username: String
      let password: String
  
      init(token: String) {
        let components = token.componentsSeparatedByString(":")
    
        username = components[0]
        password = components[1]
      }
    }
    
...and add a few properties...
  
    struct PinboardAccount: GenericPasswordSecureStorable, CreateableSecureStorable {

      let username: String
      let password: String
  
      init(token: String) {
        let components = token.componentsSeparatedByString(":")
    
        username = components[0]
        password = components[1]
      }

      // Required by GenericPasswordSecureStorable
      let service = "Pinboard"

      var account: String {
        return username
      }
  
      // Required by CreateableSecureStorable
      var data: [String: AnyObject] {
        return ["password": password]
      }
    }
    
...and now I can create, read, and delete items from the keychain  right from `account`!

    try account.createInSecureStore()
    
    let result = account.readFromSecureStore() 
    
    try account.deleteFromSecureStore()
