# Yo! In Flutter.

![yo_flutter](https://user-images.githubusercontent.com/11478053/49794374-b1a1ce00-fd37-11e8-9364-9ec0efde9ca6.png)

Basically [Yo!](http://www.justyo.co/) written in Flutter. Done in a three hour workshop with people who never used Flutter before 🍻

## Lesson 1

- Clone the project `git clone git@github.com:flutter-school/yo-flutter.git` and 
- run it `flutter run`

Flutter install instructions: 
- https://flutter.dev/docs/get-started/install
- https://flutter.dev/docs/get-started/editor

If you have trouble setting up flutter, ask for support

You've finished this lessen if you see 

## Lesson 2

Create the list layout of your friends. Use the screenshot above as reference.

1. Create a `FriendsPage` widget. "Screen" are called "Pages" in Flutter.
2. Use the [`ListView.builder`](https://docs.flutter.io/flutter/widgets/ListView-class.html) widget to show the items. 

Here are some helpful snippets:
```dart
final List<Person> friends = [
  Person("aaaa", "Frederik Schweiger", "https://lh3.googleusercontent.com/HJalMgJTCQ_Tf3OJrYLrUEYDuY2hQ6vw16Nw9RexsoQyJtl3TaduDICztFsV3-OeGTQqnlOIZlwk9q0=s360-rw-no"),
  Person("bbbb", "Pascal Welsch", "https://lh3.googleusercontent.com/GtJbCj84PLL8BLTORq_9MDxTR-UUFxwjY7h9dQRZcRrhxQlizifGWE9fZf6hVtlHcx3YSATWhA7qO8M=s360-rw-no"),
  Person("cccc", "Georg Bednorz", "https://lh3.googleusercontent.com/s4jjTjRsseMEaHhGUYLehPvatrs5h-DDUI7TcJh5RZYUCk73Ggh60IGEXYcxhW795IikC-LN3E9y-1o=s360-rw-no"),
  Person("dddd", "Seth Ladd", "https://pbs.twimg.com/profile_images/986316447293952000/oZWVUWDs_400x400.jpg"),
  Person("eeee", "Tim Sneath", "https://pbs.twimg.com/profile_images/653618067084218368/XlQA-oRl_400x400.jpg"),
];

List<Color> _colors = [
Color(0xFFF8B195),
Color(0xFFF67280),
Color(0xFFC06C84),
Color(0xFF6C5B7B),
Color(0xFF355C7D),
Color(0xFF34495D),
];
```

## Lesson 3

<img height="400px" src="https://user-images.githubusercontent.com/1096485/54165147-c2a9ca80-445f-11e9-8e9e-c1956e17c9ed.png" ></img>

Build the login screen with [scoped_model](https://github.com/brianegan/scoped_model/)

Use the existing `SessionModel` and register it globally in your `main` function.
Build a Widget which toggles between the `FriedsPage` and a `LoginPage` depending on the session state.
Use `ScopedModelDescendant` to access the `SessionModel`

```dart
ScopedModelDescendant<SessionModel>(
    builder: (BuildContext context, Widget child, SessionModel model) {
      // TODO use model
      return anyWidget;
    },
),
```

Lear how ScopedModel works under the hood: [InheritedWidgets and App-scope/Page-scope](https://medium.com/@mehmetf_71205/inheriting-widgets-b7ac56dbbeb1)

## Lesson 4 (optional)

We want to send YO!s to each other via push notifications. 
You need a apple developer account to make it work with iOS.
For now, we'll focus on Android to receive the notifications.

The android project is already fully connected to a instance firebase. 
If you want full access you have to create your own firebase project.

- Create the Android app
    - applicationId `school.flutter.yo`
    - SHA-1 `D2:6E:E8:94:62:5E:1D:74:C7:84:26:0A:32:8A:4E:26:2D:DD:FE:E4` (for existing key `flutterschool.jks`)
- (Create the iOS apps with `school.flutter.yo` as bundleId)
- Enable Google Authentication (`Develop -> Authentication -> Sign-in method -> Goolge -> Enable`) 

- Deploy firebase cloud functions, see `fireabse/README.md`

## Lesson 5

Show real users form firebase

1. Refactor your static `FriendsPage` and load your Friends from firebase. 
Don't put the Firebase loading code in the UI, create a page-scoped model (`FriendsModel`).
```dart
final stream = Firestore.instance.collection(Person.REF).orderBy("name").snapshots();
stream.listen((QuerySnapshot snapshot) {
  final friends = snapshot.documents.map((data) => Person.fromJson(data.data));
});
```

## Lesson 6

Send push notifications

Register after successful login
```dart
final fmToken = await FirebaseMessaging().getToken();
Firestore.instance
    .collection("tokens")
    .document(_user.uid)
    .setData({'token': fmToken})
```

Send yo! to another user
```dart
await http.get('https://us-central1-yo-flutter-80f0f.cloudfunctions.net/sendYo?'
    'fromUid=${userModel.uid}&toUid=${person.uid}');
```

Caution: 
- You won't see a notification if the app is in foreground
- Test on a real device, not the emulator/simulator
- It doesn't work on iOS unless you've registered you APNs Authentication Key


## Disclaimer

Please note that this code is not production ready, it should just show how quick you could build a million dollar app for Android and iOS 😉

## License

```
Copyright 2019 flutter.school

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```