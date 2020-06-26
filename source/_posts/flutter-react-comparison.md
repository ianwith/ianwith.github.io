title: Flutter React Comparison
date: 2020-06-26 23:23:07
tags:
  - front end
  - android
  - ios
  - flutter
  - react
  - cross platform
categories: work
---

In the past six months, I spent a lot of time spearheading and experimenting with Flutter, and finally implemented a multi-platform Application for data tracking which is used within LINE company. Then I took this opportunity to give a talk inside the company that introduced and promoted Flutter. Here's the slide of the presentation.

https://slides.com/yanggong/flutter-intro/

<!-- more -->

## Flutter vs React

The most interesting part I want to address is the comparsion between Flutter and React.

As we know, Flutter was very much inspired by React. So when I tried to write the same App(UI) by using Flutter and React, I found that the code was surprisingly similar. So I think it's a good idea for a front-end engineer to learn about Flutter by comparing the similarities and differences in writing layout of React and Flutter.

Here is two demo: [demo_flutter](https://github.com/ianwith/demo_flutter) vs [demo_react](https://github.com/ianwith/demo_react)

Say that we are going to make a screen like this:

<img src="/img/flutter-react-comparison/demo_screen.png" alt="Demo Screen" style="width:460px">

First of all, let's see how we will make it in React.

```jsx
class App extends Component {
  render() {
    return <HomePage />
  }
}

class HomePage extends Component {
  state = {
    counter: 0
  }

  incrementCounter = () => {
    this.setState((state) => ({
      counter: state.counter + 1
    }))
  }

  render() {
    return (
      <div className="container">
        <p className="text">I will code {this.state.counter} lines this month.</p>
        <button className="button" onClick={this.incrementCounter}>Increment</button>
      </div>
    )
  }
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

```css
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}

.text {
  font-size: 28px;
  font-weight: 500;
  margin: 0 0 8px 0;
}

.button {
  width: 90px;
  padding: 10px;
  background-color: #9037AA;
  color: white;
  font-weight: 600;
  border: 0;
  border-radius: 8px;
  outline: none;
}
```

Then comes the most exciting part, how should we implement this with Flutter?

```dart
class App extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return CupertinoApp(
      title: 'Flutter Demo',
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  // This widget is the home page of your application.
  HomePage({Key key}) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return CupertinoPageScaffold(
      child: Container(
        width: MediaQuery.of(context).size.width,
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          crossAxisAlignment: CrossAxisAlignment.center,
          children: <Widget>[
            Text(
              'I will code $_counter lines this month.',
              style: TextStyle(
                fontSize: 28,
              ),
            ),
            FlatButton(
              child: Text(
                'Increment',
                style: TextStyle(
                  color: Colors.white,
                ),
              ),
              onPressed: _incrementCounter,
              color: Colors.purple,
              shape: RoundedRectangleBorder(
                borderRadius: BorderRadius.circular(8.0),
              ),
            )
          ],
        ),
      ),
    );
  }
}

void main() {
  runApp(App());
}
```

As you can see, the code for Flutter and the code for React are almost one-to-one.

For example, we have the entry of Application in Flutter: `runApp(App())`. This can correspond to the `ReactDOM.render(<App/>, root)` in the React.

We have defined two widgets(components) for the screen: `App` and `HomePage`, each have a `build` function(`render` in react). When you take a close look at the `HomePage`, you will find class is organized exactly the same way: we have an initialized state and an Event handle to change the state. Although there is one big difference while writing `StatefulWidget`, the actual `State` is seperated from parent `StatefulWidget`, this is because every widget, stateless or stateful, is designed to be immutable, while a state is mutable. For now we can just ignore the technical details here and keep going to compare the layout part.

So for the layout of `HomePage`. The basic tree structure here is that we have a `container` as a root, and it contains two children: a `text` and a `button`. Although the structure is the same but the code of Flutter is a little bit longer the React's one. This is because we are making styles programmatically, instead of writing CSS in traditional Web development. But you can also find the corresponding CSS in React from the Layout widget of Flutter. For example, the `Column` widget corresponds to `flex-direction: column`, the `MainAxisAlignment.center` parameter corresponds to `justify-content: center`, the `CrossAxisAlignment.center` parameter corresponds to `align-items: center`. And then naturally, we go ahead and specify `TextStyle`, `color`, `shape`, `onPressed` while instantiating widgets class, fairly straightforward.

It's amazing when you see such a high level of consistency. Your knowledge on React can be seamlessly linked to Flutter. More than that, Flutter has other concepts that have been transplanted from React like Redux, Provider, simply because the data flow model in Flutter is also inspired by React. Personally, I am glad to see how React's philosophy is affecting all areas of UI development, and looking forward to seeing the UI-related development unified under the same pattern.

To summarize, I am trying to link Flutter with React and find a way to flatten the learning curve for front-end engineer with React experience. This is just a introductory article about Flutter. Flutter team also provides a series of helpful tutorials for developers coming from different backgrounds. Go and check it out: [From another platform?]([https://flutter.dev/docs/get-started/flutter-for/react-native-devs])

Happy making Flutter Apps.
