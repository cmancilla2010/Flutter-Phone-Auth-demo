# Flutter Firebase Phone Authentication Demo

![Flutter Firebase Phone Authentication Demo](flutter_phone_auth.png)

This Demo will show us how to authenticate a user by using phone number.

![Flutter Firebase Phone Authentication Demo](flutter_phone_auth.gif)

## Setup

Use latest versions of below mentioned plugins in `pubspec.yaml`.

| Plugin | Pub | Explanation |
|--------|-----|-------------|
| [connectivity](https://github.com/flutter/plugins/tree/master/packages/connectivity/connectivity) | [![pub package](https://img.shields.io/pub/v/connectivity.svg)](https://pub.dev/packages/connectivity) | Used to check internet connectivity. 
| [firebase_auth](https://github.com/FirebaseExtended/flutterfire/tree/master/packages/firebase_auth/firebase_auth) | [![pub package](https://img.shields.io/pub/v/firebase_auth.svg)](https://pub.dev/packages/firebase_auth) | Used to authenticate phone.
| [firebase_core](https://github.com/FirebaseExtended/flutterfire/tree/master/packages/firebase_core/firebase_core) | [![pub package](https://img.shields.io/pub/v/firebase_core.svg)](https://pub.dev/packages/firebase_core) | Used for core Firebase Api.
| [fluttertoast](https://github.com/PonnamKarthik/FlutterToast) | [![pub package](https://img.shields.io/pub/v/fluttertoast.svg)](https://pub.dev/packages/fluttertoast) | Used to show toast.

And then

    flutter pub get

Visit [Firebase Console](https://console.firebase.google.com/u/0/?pli=1) to add new project. Add `Android` and `iOS` app to that project. Add `google-services.json` and `GoogleService-Info.plist` for `Android` and `iOS` respetively to its predefined place in flutter project.

Now enable `Phone` Sign-in method (second tab) in Authentication. 

Please add some phone numbers for testing 

    To prevent abuse, Firebase enforces a limit on the number of SMS messages that can be sent to a single phone number within a period of time. If you exceed this limit, phone number verification requests might be throttled. If you encounter this issue during development, use a different phone number for testing, or try the request again later.

| Phone number | Verification code |
| ------------ | ----------------- |
| +91 11 1111 1111 | 111111 |
| +91 22 2222 2222 | 222222 |
| +91 33 3333 3333 | 333333 |

You can also get this steps in Firebase docs for [Android](https://firebase.google.com/docs/auth/android/phone-auth) and [iOS](https://firebase.google.com/docs/auth/ios/phone-auth) and [web](https://firebase.google.com/docs/auth/web/phone-auth).

#### For Android

    <uses-permission android:name="android.permission.INTERNET" />

Please mention `internet` permission in `AndroidManifest.xml`. This will not affect in `debug` mode but in `release` mode it will give `socket exception`.

Add SHA-1 in firebase app 

    1. Open app in Android Studio
    2. Open Gradle panel
    3. Goto andoid -> app -> Tasks -> android
    4. Double click on signingReport, it will generate SHA-1

Add below line in android/build.gradle

    buildscript {
        // ...
        dependencies {
            // ...
            classpath 'com.google.gms:google-services:4.3.2'
        }
    }

Add below line in app/build.gradle

    apply plugin: 'com.android.application'

    android {
        // ...
    }

    dependencies {
        // ...
    }

    // ADD THIS AT THE BOTTOM
    apply plugin: 'com.google.gms.google-services'

#### For iOS

Follow the steps in [google_sign_in](https://pub.dev/packages/google_sign_in) library

### Initialise Firebase

    await Firebase.initializeApp();

### Verify Phone

    final PhoneVerificationFailed verificationFailed = (FirebaseAuthException authException) {
        // Handle the case when phone verification failes.
    };

    final PhoneCodeSent codeSent = (String verificationId, [int forceResendingToken]) async {
        // OTP send successfully.
        _verificationId = verificationId;
    };

    final PhoneCodeAutoRetrievalTimeout codeAutoRetrievalTimeout = (String verificationId) {
        // Handle the case when phone verification automatic retrival timeout.
        _verificationId = verificationId;
    };

    PhoneVerificationCompleted verificationCompleted = (PhoneAuthCredential phoneAuthCredential) {
        // Handle the case when we want automatic phone verification.
        // Only called on Android.
    };

    if (kIsWeb) { //for web
        await _auth.signInWithPhoneNumber("+911111111111")
        .then((value) {
            _verificationId = value.verificationId;
        });
    } else { // for native
        await _auth
            .verifyPhoneNumber(
                phoneNumber: "+911111111111",                             // Phone number with country code.
                timeout: const Duration(seconds: 5),                    // Phone code automatic retrival timeout duration.
                verificationCompleted: verificationCompleted,           // Triggers for instant verification of phone.
                verificationFailed: verificationFailed,                 // Triggers when phone verification failed.
                codeSent: codeSent,                                     // Triggers when code is sent successfully.
                codeAutoRetrievalTimeout: codeAutoRetrievalTimeout,     // Triggers when phone code auto retrival timeout.
            ); 
    }

### Sign in

    AuthCredential credential = PhoneAuthProvider.credential(
        verificationId: _verificationId,
        smsCode: otp,
    );
    User user = (await _auth.signInWithCredential(credential)).user;
    User currentUser = _auth.currentUser;

#### For Web

Add Firebase core JS SDK

    <script src="https://www.gstatic.com/firebasejs/8.6.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.6.1/firebase-firestore.js"></script>

Add Firebase Auth and Analytics JS for google sign-in

    <script src="https://www.gstatic.com/firebasejs/8.6.1/firebase-auth.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.6.1/firebase-analytics.js"></script>

Initialize Firebase with configuration

    <script>
    var firebaseConfig = {
      // ... your web apps configuration. This is available in your Firebase project settings.
    };

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    firebase.analytics();
    </script>

Finally for android and ios

    flutter run

for web

    flutter run -d chrome

<!-- ##### Please refer to my [blogs](https://ankitsolanki.netlify.app/blog.html) for more information. -->

Checkout [this demo](https://flutter-web-phone-auth.netlify.app/#/) in [Flutter Web](https://flutter.dev/docs/get-started/web).
