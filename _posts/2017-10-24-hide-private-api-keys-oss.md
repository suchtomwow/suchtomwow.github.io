---
layout: post
title: How to hide private API keys in an open source project
published: true
date: 2017-10-24 20:56:00 -0600
permalink: /:title
author: Thomas Carey
---

While preparing to publish my simple little open source app, [Glancify](https://github.com/suchtomwow/glancify), on GitHub, I ran across an interesting problem when I decided to integrate Crashlytics.

The following information is inspired by the guide located at [https://www.herzbube.ch/blog/2016/08/how-hide-fabric-api-key-and-build-secret-open-source-project](https://www.herzbube.ch/blog/2016/08/how-hide-fabric-api-key-and-build-secret-open-source-project), but I wanted to preserve the content here in case that link ever goes down. Also that link gave me a sketchy "unsecure" warning in Chrome when I visited it, ¯\_(ツ)_/¯. Give those folks a high five for the original guide.

So, you want to publish an open source project that contains private keys? Here's how you can safely share your code with the world while maintaining the privacy of your information:

We'll use Fabric/Crashlytics as an example 

_NOTE: If you've already pushed any commit to the internet that contained your private API key, you'll need to first remove that commit._

1. Create 2 files in the root of your project directory called `fabric.apikey` and `fabric.buildsecret` and enter your respective API key and build secret into each appropriate file.
2. Add each of the newly created files to your .gitignore. 
    
    ```
    /fabric.apikey
    /fabric.buildsecret
    ```
3. Add `fabric.apikey` to your Xcode project, making sure that the option to add it to your target _is_ selected since the file is needed at runtime. Additionally, ensure that the file is included as part of the "Copy Bundle Resources" build phase.

    <img src="../resources/Screen Shot 2017-10-24 at 9.16.51 PM.png" alt="Screenshot of adding file to target" class="inline"/>

4. If you want, you can also copy the `fabric.buildsecret` to the project also, but make sure that the option to add to target is _unchecked_. It is not needed at runtime and _**should not**_ be included in the "Copy Bundle Resources" build phase.
5. If you've already created a "Run Script" build phase as per the Crashlytics integration instructions, replace it with the following. Otherwise, add a new "Run Script" build phase and paste the following:

    ``` bash
    FABRIC_APIKEY_FILE="${SRCROOT}/Resources/fabric.apikey"
    FABRIC_BUILDSECRET_FILE="${SRCROOT}/Resources/fabric.buildsecret"
    
    if test ! -f "$FABRIC_APIKEY_FILE" -o ! -f "$FABRIC_BUILDSECRET_FILE"; then
      echo "This build wants to upload dSYM files to Crashlytics."
      echo "Uploading is possible only if a Fabric API key and a Fabric build secret are"
      echo "available. This build is failing because at least one of these pieces of"
      echo "information is missing."
      echo ""
      echo "To fix the problem, create the following files and store the API key and"
      echo "build secret, respectively, within those files:"
      echo ""
      echo "  $FABRIC_APIKEY_FILE"
      echo "  $FABRIC_BUILDSECRET_FILE"
      echo ""
      echo "If you forked the project then you must register with Crashlytics and"
      echo "get your own API key and build secret."
    
      # Let the build fail
      exit 1
    fi
    
    FABRIC_APIKEY=$(cat "$FABRIC_APIKEY_FILE")
    if test $? -ne 0; then
      echo "Cannot read $FABRIC_APIKEY_FILE"
      exit 1
    fi
    
    FABRIC_BUILDSECRET=$(cat "$FABRIC_BUILDSECRET_FILE")
    if test $? -ne 0; then
      echo "Cannot read $FABRIC_BUILDSECRET_FILE"
      exit 1
    fi
    
        
    echo "Uploading dSYM files to Crashlytics"
    "${PODS_ROOT}/Fabric/run" "$FABRIC_APIKEY" "$FABRIC_BUILDSECRET"
    ```
    
6. Replace the line that initializes Crashlytics (`Fabric.with([Crashlytics.self])`, usually located in AppDelegate.swift) with the following:
        
    ``` swift
    do {
      if let url = Bundle.main.url(forResource: "fabric.apikey", withExtension: nil) {
        let key = try String(contentsOf: url, encoding: .utf8).trimmingCharacters(in: .whitespacesAndNewlines)
        Crashlytics.start(withAPIKey: key)
      }
    } catch {
      NSLog("Could not retrieve Crashlytics API key. Check that fabric.apikey exists, contains your Crashlytics API key, and is a member of the target")
    }
    ```

7. Lastly, remove the key from your Info.plist!
8. Celebrate by pushing your code to the internet! 🎉

As mentioned, these steps outline the exact steps you would take for initializing Crashlytics, but the framework should hold for most other services as well. Good luck!