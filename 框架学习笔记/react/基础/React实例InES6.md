## `index.js`
```
var React = require('react');
var ReactDOM = require('react-dom');
import ComponentHeader from './component/header';
import ComponentFooter from './component/footer';
import BodyIndex from './component/bodyindex';

class Index extends React.Component {
    render() {
        return (
            <div>
                <ComponentHeader />
                <BodyIndex />
                <ComponentFooter />
            </div>
        );
    }
}

ReactDOM.render(
    <Index />,
    document.getElementById('example');
);
```

## `component/header.js`
```
import React from 'react';

export default class ComponentHeader extends React.Component {
    render() {
        return (
            <header>
                <h1>Here is header;</h1>
            </header>
        );
    }
}
```

## `component/footer.js`
```
import React from 'react';

export default class ComponentFooter extends React.Component {
    render () {
        return (
            <footer>
                <p>Here is footer; </p>
            </footer>
        );
    }
}
```

## `component/bodyindex.js`
```
import React from 'react';

export default class BodyIndex extends React.Component {
    constructor () {
        super();
        this.state = {
            welcome: "Hello",
            name: "Geekzwb"
        };// init
    }

    changeUserInfo() {
        this.setState({welcome: "Hello"});
    }

    render() {
        setTimeout(()=> {
            this.setState({
                welcome: "Welcome"
            }); //edit state
        }, 2000);

        return (
            <div>
                <h1>{this.state.welcome}, {this.state.name}</h1>
                <input type="button" value="提交" onClick={this.changeUserInfo.bind(this)} />
            </div>
        );
    }
}
```
