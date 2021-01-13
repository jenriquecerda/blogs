# Declarative Testing in React

Recently, I started on a project which required some testing in React. I went on to React webpage and discovered they were no longer recommending on Enzyme, a React Testing Utility capable of mimicking React's lifecycle as well as its API for DOM manipulation. I had spent some time learning this utility so I felt a little disappointed. I kept reading and found out that **_React Testing Library (RTL)_** was nos being suggested. So what did this mean? I went on and visit [React Testing Library](https://testing-library.com/docs/) and read its first paragraph:

> <b>The problem</b>
>
> You want to write maintainable tests that give you high confidence that your components are working for your users. <b>As a part of this goal, you want your tests to avoid including implementation details</b> so refactors of your components (changes to implementation but not functionality) don't break your tests and slow you and your team down.

Unlike Enzyme, this tool's focus is to avoid implementation details and treat the test as a user would view it on the screen. So instead of doing this:

```javascript
describe("SlideViewer", () => {
  it("increments 'slideIndex' when Next is clicked, () => {
    const slides = [slide1, slide2, slide 3];

    const wrapper = shallow(<SlideViewer slides={slides} />);
    wrapper.setState({ slideIndex: 0 });

    wrapper.find("button").simulate("click");

    expect(wrapper.state("slideIndex")).toBe(1);
  });
});

```

RTL suggests this:

```javascript
describe("SlideViewer", () => {
  test("it shows next slide when button is clicked", () => {
    const slides = [slide1, slide2, slide 3];

    render(<SlideViewer slides={slides} />);

    clickOnNext();

    expect(screen.findByText("index")).toEq(1);
  });
});

```

The big difference here is the style used to write tests, Imperative (Enzyme) vs Declarative (RTL). With Imperative style, you focus on internals and details that would never be important for a user that may not even know they exist, such as state. With Declarative, as I found out, you put yourself on the user's shoes and test what's in front of you, like the UI (screen). On the second snippet, you render a component, like a screen would whenever you visit a site. Afterwards, you'd click on a button, in this case a "Next" button. And you would expect for the screen to show an increment on the slide index.

## Walktrough example

Download [example code](https://github.com/jenriquecerda/react_testing) or follow along.

The first thing I will do is my initial setup where I will configure a simple server for it to be able to display my component visually... although tests should be enough to prove it works.

### Install Webpack & Html plugin

```sh
$ npm install --save-dev webpack webpack-cli html-webpack-plugin webpack-dev-server
```

- webpack (bundles files)
- webpack-dev-server (provides live reloading)
- webpack-cli (needed by webpack-dev-server)
- html-webpack-plugin (displays on a browser)

Webpack packages your React code (written among others, with JSX), with simple JS language that a browser is able to read. Webpack-dev-server is a module bundler and helps during development for a quicker feedback, providing fast and automatic reloading.

_webpack.config.js_

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  mode: "development",
  entry: { example: "./src/index.js" },
  plugins: [
    new HtmlWebpackPlugin({
      title: "Example",
      chunks: ["example"],
    }),
  ],
};
```

I have provided webpack its own configuration file using the **HtmlWebpackPlugin** which will help us on the visual part by displaying our component on a browser.

### Install React

#### Babel

Babel is a kind of translator that converts ECMASCRIPT 2015+ into JavaScript. We will use a Babel preset, `@babel/preset-react`, specially used to convert React's JSX into JavaScript.

```bash
$ npm install --save-dev @babel/core @babel/preset-env @babel/preset-react babel-loader
```

`babel-loader` is a module used by webpack and it is used by webpack to transpile files using Babel.

You'll need to specify this loader in Babel configuration in your root:

_babel.config.json_

```json
{
  "presets": [
    "@babel/preset-react",
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "current"
        }
      }
    ]
  ]
}
```

And add into your webpack configuration:

_webpack.config.js_

```javascript
module: {
  rules: [
    {
      exclude: /node_modules/,
      loader: "babel-loader",
    },
  ],
},
```

These configurations say that everything that ends with ".js" will be loaded by "babel-loader" and we are also specifying the React presets in the Babel config file.

Finally, to test your webpack server and render a React component:

```sh
$ npm install --save-dev react-dom
```

_index.js_

```javascript
import React from "react";
import ReactDOM from "react-dom";

const container = document.createElement("div");
document.body.appendChild(container);
ReactDOM.render(<h1>Hello, world!</h1>, container);
```

Running `npm run start:dev` should render **Hello, World!** on your browser.

## And now for the testing part...

### Install Jest and RTL

```sh
$ npm install --save-dev jest @testing-library/react
```

Jest is the testing framework that will allow us to make assertions.

And begin testing...

_SlideViewer.test.js_

```javascript
import React from "react";
import { render, screen } from "@testing-library/react";

describe("SlideViewer", () => {
  it("renders a SlideViewer", () => {
    render(<SlideViewer />);

    expect(screen.getByText("No Slides")).toBeTruthy();
  });
});
```

I have rendered a SlideViewer, and I expect the screen to show a text saying "Slides". Obviously this test will fail since there is no SlideViewer, but running `npm run test` outputs the following:

```sh
ReferenceError: SlideViewer is not defined

      4 | describe("SlideViewer", () => {
      5 |   it("renders a SlideViewer", () => {
    > 6 |     render(<SlideViewer />);
```

After implementing **SlideViewer**...

_SlideViewer.js_

```javascript
const SlideViewer = () => {
  return "No Slides:";
};

export default SlideViewer;
```

...and running `npm run test`, the output is finally green:

```sh
 PASS  spec/SliderViewer.test.js
  SlideViewer
    ✓ renders a SlideViewer (26 ms)
```

And now, a test to display the first slide passed on:

_SlideViewer.test.js_

```javascript
it("displays passed slides", () => {
  const slides = [
    { name: "cow", filename: "cow.jpg" },
    { name: "elephant", filename: "elephant.jpg" },
    { name: "lion", filename: "lion.jpg" },
    { name: "table", filename: "table.jpg" },
  ];

  render(<SlideViewer slides={slides} />);

  const slideSource = document.querySelector("img").src;
  expect(slideSource.endsWith(slides[0].filename)).toBe(true);
  expect(screen.getByText(slides[0].name)).toBeTruthy();
});
```

And to make both tests pass:

_SlideViewer.js_

```javascript
const SlideViewer = (props) => {
  return (
    <div>
      {!props.slides && "No Slides"}
      {props.slides && (
        <div>
          <div>Slides:</div>
          <figure>
            <img
              src={`../img/${props.slides[0].filename}`}
              width="500"
              height="600"
            />
            <figcaption>{props.slides[0].name}</figcaption>
          </figure>
        </div>
      )}
    </div>
  );
};
```

Display the component on your browser, obviously you still would need to add some styling, but that's not this blog's objective, we'll leave that for another time.

_index.js_

```javascript
const slides = [
  { name: "cow", filename: "cow.jpg" },
  { name: "elephant", filename: "elephant.jpg" },
  { name: "lion", filename: "lion.jpg" },
  { name: "table", filename: "table.jpg" },
];

const container = document.createElement("div");
document.body.appendChild(container);
ReactDOM.render(<SlideViewer slides={slides} />, container);
```

Next we are going to simulate what a user would do to display the next slide. He would have to click on a button, this button would normally be labeled "Next".

_SlideViewer.test.js_

```javascript
it("displays next slide when Next clicked", () => {
  render(<SlideViewer slides={slides} />);

  clickOn(label);

  expect(getImageSource().endsWith(slides[1].filename)).toBe(true);
  expect(screen.getByText(slides[1].name)).toBeTruthy();
});

const clickOn = (label) =>
  userEvent.click(screen.getByRole("button", { name: label }));
```

I have emulated the clickOn action. `userEvent` is part of [`@testing-library/user-event`](https://testing-library.com/docs/ecosystem-user-event) which I had installed. This library has plenty of user events that will help us to emulate user actions.

And finally, to make this test pass:

_SlideViewer.js_

```javascript
const Button = (props) => {
  return (
    <button type="button" onClick={props.onClick}>
      {props.label}
    </button>
  );
};

const SlideViewer = (props) => {
  const [index, setIndex] = useState(0);

  return (
    <div>
      {!props.slides && "No Slides"}
      {props.slides && (
        <div>
          <div>Slides:</div>
          <figure>
            <img
              src={`../img/${props.slides[index].filename}`}
              width="500"
              height="600"
            />
            <figcaption>{props.slides[index].name}</figcaption>
          </figure>
        </div>
      )}
      <Button label="Next" onClick={() => setIndex(index + 1)} />
    </div>
  );
};
```

You are starting to get the idea, Declarative style tries to emulate what the user actually sees and tests on it. It does not care about internals, there is no state testing nor any other internal function. Instead, it tests over the final result that the user sees on the screen as the example clearly shows.

I will continue this exercise and complete the whole functionality. Please take a look at the different tests and how their implementation works.
