---
tag: []
title: Getting started with Expo apps - draft
redirect_from: []
summary: ''
published: false

---
You can generate React Native projects [with the React Native CLI or with the Expo CLI](https://facebook.github.io/react-native/docs/getting-started.html). [Expo](https://docs.expo.io/versions/v32.0.0/) is a toolchain built on React Native. It allows you to quickly get a React Native app up and running without having to fiddle with native code in Xcode or Android Studio.

In this guide we discuss how to set up, test, code sign and deploy your React Native project built with the Expo CLI.

Depending on the nature of your React Native project, you can expect two types of workflows once the project scanner has finished scanning your project. If you've been using the [ExpoKit](https://docs.expo.io/versions/v32.0.0/expokit/eject/) to include custom native modules to your pure JavaScript project, then our project scanner will add the necessary native dependency installer Steps before the build Steps. If you have NOT ejected your project to ExpoKit, your workflow will get the JavaScript and native dependency installer Steps along with the **\[BETA\] Expo Eject** Step.

Whether you've been using ExpoKit or not with your project, Bitrise project scanner detects the necessary configuration and adds the **\[BETA\] Expo Eject** Step to your deploy workflow. If you've been using ExpoKit with your React Native app, Bitrise project scanner adds the necessary platform-specific dependency manager Steps to your workflow so that ExpoKit's native iOS and Android dependencies are included in your app.

## Adding an Expo app to bitrise.io

First, let's see how to add a React Native Expo app to [bitrise.io](https://www.bitrise.io/).

{% include message_box.html type="info" title="Do you have a Bitrise account?" content=" Make sure you have signed up to [bitrise.io](https://www.bitrise.io/) and can access your Bitrise account. Here are [4 ways](https://devcenter.bitrise.io/getting-started/index#signing-up-to-bitrise) on how to connect your Bitrise account to your account found on a Git service provider. "%}

 1. Log into [bitrise.io](https://www.bitrise.io/).
 2. Click **Add a new app**.
 3. Select the privacy setting of your app: **private** and [**public**](https://mpxzvqn7ysfysw.preview.forestry.io/getting-started/adding-a-new-app/public-apps/).
 4. Select the Git hosting service that hosts your repository, then find and select your own repository that hosts the project. Read more about [connecting your repository](/getting-started/adding-a-new-app/connecting-a-repository/).
 5. When prompted to set up repository access, click **No, auto-add SSH key**. Read more about [SSH keys](/getting-started/adding-a-new-app/setting-up-ssh-keys/).
 6. Type the name of the branch that includes your project’s configuration - master, for example, - then click **Next**.
 7. At **Validating repository**, Bitrise runs an automatic repository scanner to set up the best configuration for your project.
 8. At **Project build configuration**, the React Native project type gets automatically selected. If the scanner fails and the project type is not selected automatically, you can [configure your project manually](https://devcenter.bitrise.io/getting-started/adding-a-new-app/setting-up-configuration#manual-project-configuration). Bitrise also detects the **Module** and the **Variant** based on your project.

    Now let's have a look at the fields you manually have to fill out:
    * If you wish to generate an iOS app from your React Native project, enter your iOS Development team ID at the **Specify iOS Development team** field.
    * In **Select ipa export method**, select the export method of your .ipa file: ad-hoc, app-store, development or enterprise method.
    * In **Specify Expo username** enter your username and hit **Next**.
    * In **Specify Expo password**, enter your password and hit **Next**. You only need to provide your Expo credentials if you've been using [ExpoKit](https://docs.expo.io/versions/v32.0.0/expokit/overview/) with your project.
    * Confirm your project build configuration.
 9. [Upload an app icon](/getting-started/adding-a-new-app/setting-up-configuration/#adding-an-app-icon-with-the-project-scanner).
10. At **Webhook setup**, [register a Webhook](/webhooks/index/) so that Bitrise can automatically start a build every time you push code into your repository.

You have successfully set up your React Native project on [bitrise.io](https://www.bitrise.io/)! Your first build gets kicked off automatically using the primary workflow. You can check the generated reports of the first build on the **APPS & ARTIFACTS** tab of your Build’s page.

## Installing dependencies

### Javascript dependencies

If Bitrise scanner has successfully scanned your app, depending on your project configuration, **Run npm command** or **Run yarn command** Step will be included in your workflow.

The default value of the **Run npm command** Step is `install` in the **npm command with arguments to run** input field. This way the Step can add JavaScript dependencies to your project.

### Ejecting your app

React Native apps built with Expo do not come with native modules. Since our build Steps are platform-specific, Bitrise has to eject your app, add and configure the necessary native templates. Then our native dependency installer Steps take care of installing any missing native dependencies so that your project is ready for building and shipping.

Bitrise project scanner automatically inserts the **\[BETA\] Expo Eject** Step right after the **Run npm command** or **Run yarn command** Steps in your deploy workflow.

![](/img/eject-expo-input-fields.png)

Let's see which fields you have to fill out when clicking **\[BETA\] Expo Eject** Step!

* **Working directory input field:** Provide the path of your project directory.
* **Expo CLI version:** Provide the Expo CLI version you used for your project.
* **Username for Expo** and **Password for your Expo account:** Provide your Expo credentials (username and password). If your project uses an Expo SDK, you must provide the username and password for your Expo account. Without the account, the [Expo CLI](https://docs.expo.io/versions/latest/introduction/installation#local-development-tool-expo-cli) will choose the plain `--eject-method` and the Expo SDK imports will stop working.

  If your project does not use an Expo SDK then you don’t need to do anything.  
  Just add the step after the `git-clone` step and you are done.

### Native dependencies

The **Install missing Android SDK components** Step installs the missing native dependencies for your Android project. This Step is by default included in your deploy workflow.

If you've been using the ExpoKit to develop your app, the **Run CocoaPods install** Step automatically is added to your deploy workflow to take care of any missing iOS dependencies.

## Testing your app

You can use React Native’s built in testing method, called jest, to perform unit tests.

1. Add another Run nmp command step to your workflow right after the first **Run npm command** Step.
2. Type test in the npm command with arguments to run input field.

   ![](https://mpxzvqn7ysfysw.preview.forestry.io/img/test-npm.png)
3. Start a build.

You can view the test artifacts on the **APPS & ARTIFACTS** tab of your Build's page.

## Code signing

A React Native app consists of two projects; an Android and an iOS - both must be properly code signed. If you click on the Code Signing tab of your project’s Workflow Editor, all iOS and Android code signing fields are displayed in one page for you.

Let’s see how to fill them out!

### Signing your Android app

1. Select the deploy workflow at the WORKFLOW dropdown menu in the top left corner of your apps’ Workflow Editor.
2. Go to the Code Signing tab.
3. Drag-and-drop your keystore file to the ANDROID KEYSTORE FILE field.
4. Fill out the Keystore password, Keystore alias, and Private key password fields and click Save metadata.

   You should have these already at hand as these are included in your keystore file which is generated in Android Studio prior to uploading your app to Bitrise. For more information on keystore file, click [here](https://developer.android.com/studio/publish/app-signing). With this information added to your Code Signing tab, our Sign APK step (by default included in your Android deploy workflow) will take care of signing your APK so that it’s ready for distribution!

{% include message_box.html type="info" title="More information on Android code signing" content=" Head over to our [Android code signing guide](https://devcenter.bitrise.io/code-signing/android-code-signing/android-code-signing-procedures/) to learn more about your code signing options! "%}

![](https://mpxzvqn7ysfysw.preview.forestry.io/img/keystore.png)

The Android chunk of code signing is done. Let's continue with iOS!

### Signing and exporting your iOS app for deployment

To deploy to Testflight and to the App Store, you will need more code signing files:

* an iOS **Distribution** Certificate
* an **App Store** type Provisioning Profile

If you wish to distribute your app to external testers without uploading the app to Testflight, select `ad-hoc`method and make sure you have the `Deploy to Bitrise.io` step in your workflow.

1. Open the **Workflow** tab of your project on [bitrise.io](https://www.bitrise.io).
2. Click on **Code Signing** tab.
3. Click or drag and drop the App Store type provisioning profile in the **PROVISIONING PROFILE** field and the iOS Distribution certificate in the **CODE SIGNING IDENTITY** field.
4. Click on the **Workflows** tab and select your deploy workflow.
5. Select **Xcode Archive & Export for iOS** Step and scroll down to the **Force Build Settings** input group.
6. Fill out the following fields based on your uploaded code signing files:
   * **Force code signing with Development Team**: Add the team ID.
   * **Force code signing with Code Signing Identity:** Add the Code Signing Identity as full ID (for example iPhone Developer: Bitrise Bot (VV2J4SV8V4)) or as a code signing group ( iPhone Developer or iPhone Distribution ).
   * **Force code signing with Provisioning Profile**: Add the provisioning profile's UDID.

     ![](/img/force-code-signing-1.jpg)
7. If the codesigning files are manually generated on the Apple Developer Portal, you have to specify to use manual codesigning settings since ejected React Native projects have Xcode managed codesigning turned on.

   Click the **Debug** input group and add `CODE_SIGN_STYLE="Manual"` to the **Additional options for xcodebuild call input** field.

## Deploying to Bitrise

The `Deploy to bitrise.io` step uploads all the **artifacts related to your build into the** [**APPS & ARTIFACTS**](/builds/build-artifacts-online/) **tab on your Build’s page. ??**

You can share the generated APK/.ipa file with your team members using the build’s URL. You can also notify user groups or individual users that your APK/.ipa file has been built.

1. Go to the Deploy to bitrise.io step.
2. In the Notify: User Roles, add the role so that only those get notified who have been granted with this role. Or fill out the `Notify: Emails` field with email addresses of the users you want to notify. Make sure you set those email addresses as [secret env vars](/builds/env-vars-secret-env-vars/)! These details can be also modified under Notifications if you click the eye icon next to your generated APK/.ipa file in the APPS & ARTIFACTS tab.

code signing- force.

## Deploying to an app store

If you wish to deploy your iOS app, follow the steps in [Code sign your iOS project for deployment](/getting-started/getting-started-with-react-native-apps/#sign-and-export-your-ios-project-for-deployment).

### Deploying your iOS app to Testflight and iTunes Connect

Have you exported an `app-store` .ipa file yet

Make sure that you have exported an `app-store` .ipa file before starting the deployment procedure to a native marketplace!

1. modify xcode archive step's input fields to the force options.and upload the app store profile and dist certificate **manually**.
2. Add the Deploy to iTunes Connect - Application Loader Step to your workflow, after the Xcode Archive & Export for iOS Step but preferably before the Deploy to Bitrise.io Step.
3. Provide your Apple credentials in the Deploy to iTunes Connect - Application Loader Step.

   The Step will need your:
   * Apple ID
   * password or, if you use two-factor authentication on iTunes Connect, your application password.

   Don’t worry, the password will not be visible in the logs or exposed - [that’s why it is marked SENSITIVE](https://devcenter.bitrise.io/builds/env-vars-secret-env-vars#about-secrets).
4. [Start a build](https://mpxzvqn7ysfysw.preview.forestry.io/builds/Starting-builds-manually/).

   If everything went well, you should see your app on Testflight. From there, you can distribute it to external testers or release it to the App Store.

### Deploying your Android app to Google Play Store

Make sure that you have uploaded the keystore file to the ANDROID KEYSTORE FILE field!

1. Make sure you are in sync with Google Play Store! Learn how to
   * [register to Google Play Store and set up your project](https://devcenter.bitrise.io/tutorials/deploy/android-deployment/#register-to-google-play-store-and-set-up-your-first-project)
   * set up [Google Play API access](https://devcenter.bitrise.io/tutorials/deploy/android-deployment/#set-up-google-play-api-access)
2. In your Bitrise Dashboard, go to Code Signing tab and upload the service account JSON key into the GENERIC FILE STORAGE.
3. Copy the env key which stores your uploaded file’s url.

   For example: `BITRISEIO_SERVICE_ACCOUNT_JSON_KEY_URL`
4. Add the Google Play Deploy step after the Sign APK step in your deploy workflow.
5. Fill out the required input fields as follows:
   * Service Account JSON key file path: This field can accept a remote URL so you have to provide the environment variable which contains your uploaded service account JSON key. For example: `$BITRISEIO_SERVICE_ACCOUNT_JSON_KEY_URL`
   * Package name: the package name of your Android app
   * Track: the track where you want to deploy your APK (for example, alpha/beta/rollout/production or any custom track you set)

And that’s it! Start a build and release your Android app to the app store of your choice.