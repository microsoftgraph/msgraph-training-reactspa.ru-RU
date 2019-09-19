<!-- markdownlint-disable MD002 MD041 -->

Откройте интерфейс командной строки (CLI), перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующие команды, чтобы установить средство [создать-реагирующий App](https://www.npmjs.com/package/create-react-app) и создать новое приложение для реагирования.

```Shell
npm install create-react-app@3.1.1 -g
create-react-app graph-tutorial
```

После завершения выполнения команды перейдите к `graph-tutorial` каталогу в CLI и выполните следующую команду для запуска локального веб-сервера.

```Shell
npm start
```

Браузер, используемый по умолчанию, открывается [https://localhost:3000/](https://localhost:3000) с используемой по умолчанию страницей отклика. Если браузер не открыт, откройте его и перейдите к [https://localhost:3000/](https://localhost:3000) странице, чтобы убедиться, что новое приложение работает.

Прежде чем переходить, установите несколько дополнительных пакетов, которые будут использоваться позже:

- [реагирующий маршрутизатор — модель DOM](https://github.com/ReactTraining/react-router) для декларативной маршрутизации в приложении реагируя.
- [Начальная](https://github.com/twbs/bootstrap) Загрузка стилей и общих компонентов.
- [реактстрап](https://github.com/reactstrap/reactstrap) для отклика компонентов на основе начальной загрузки.
- [фонтавесоме — без](https://github.com/FortAwesome/Font-Awesome) значков.
- [время для форматирования](https://github.com/moment/moment) даты и времени.
- [msal](https://github.com/AzureAD/microsoft-authentication-library-for-js) для проверки подлинности в Azure Active Directory и получения маркеров доступа.
- [Microsoft — Graph — клиент](https://github.com/microsoftgraph/msgraph-sdk-javascript) для совершения звонков в Microsoft Graph.

Выполните следующую команду в командной панели CLI.

```Shell
npm install react-router-dom@5.0.1 bootstrap@4.3.1 reactstrap@8.0.1 @fortawesome/fontawesome-free@5.10.2
npm install moment@2.24.0 msal@1.1.3 @microsoft/microsoft-graph-client@1.7.0
```

## <a name="design-the-app"></a>Проектирование приложения

Для начала создайте панель навигации для приложения. Создайте новый файл в `./src` каталоге `Navbar.js` и добавьте указанный ниже код.

```JSX
import React from 'react';
import { NavLink as RouterNavLink } from 'react-router-dom';
import {
  Collapse,
  Container,
  Navbar,
  NavbarToggler,
  NavbarBrand,
  Nav,
  NavItem,
  NavLink,
  UncontrolledDropdown,
  DropdownToggle,
  DropdownMenu,
  DropdownItem } from 'reactstrap';
import '@fortawesome/fontawesome-free/css/all.css';

function UserAvatar(props) {
  // If a user avatar is available, return an img tag with the pic
  if (props.user.avatar) {
    return <img
            src={props.user.avatar} alt="user"
            className="rounded-circle align-self-center mr-2"
            style={{width: '32px'}}></img>;
  }

  // No avatar available, return a default icon
  return <i
          className="far fa-user-circle fa-lg rounded-circle align-self-center mr-2"
          style={{width: '32px'}}></i>;
}

function AuthNavItem(props) {
  // If authenticated, return a dropdown with the user's info and a
  // sign out button
  if (props.isAuthenticated) {
    return (
      <UncontrolledDropdown>
        <DropdownToggle nav caret>
          <UserAvatar user={props.user}/>
        </DropdownToggle>
        <DropdownMenu right>
          <h5 className="dropdown-item-text mb-0">{props.user.displayName}</h5>
          <p className="dropdown-item-text text-muted mb-0">{props.user.email}</p>
          <DropdownItem divider />
          <DropdownItem onClick={props.authButtonMethod}>Sign Out</DropdownItem>
        </DropdownMenu>
      </UncontrolledDropdown>
    );
  }

  // Not authenticated, return a sign in link
  return (
    <NavItem>
      <NavLink onClick={props.authButtonMethod}>Sign In</NavLink>
    </NavItem>
  );
}

export default class NavBar extends React.Component {
  constructor(props) {
    super(props);

    this.toggle = this.toggle.bind(this);
    this.state = {
      isOpen: false
    };
  }

  toggle() {
    this.setState({
      isOpen: !this.state.isOpen
    });
  }

  render() {
    // Only show calendar nav item if logged in
    let calendarLink = null;
    if (this.props.isAuthenticated) {
      calendarLink = (
        <NavItem>
          <RouterNavLink to="/calendar" className="nav-link" exact>Calendar</RouterNavLink>
        </NavItem>
      );
    }

    return (
      <div>
        <Navbar color="dark" dark expand="md" fixed="top">
          <Container>
            <NavbarBrand href="/">React Graph Tutorial</NavbarBrand>
            <NavbarToggler onClick={this.toggle} />
            <Collapse isOpen={this.state.isOpen} navbar>
              <Nav className="mr-auto" navbar>
                <NavItem>
                  <RouterNavLink to="/" className="nav-link" exact>Home</RouterNavLink>
                </NavItem>
                {calendarLink}
              </Nav>
              <Nav className="justify-content-end" navbar>
                <NavItem>
                  <NavLink href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                    <i className="fas fa-external-link-alt mr-1"></i>
                    Docs
                  </NavLink>
                </NavItem>
                <AuthNavItem
                  isAuthenticated={this.props.isAuthenticated}
                  authButtonMethod={this.props.authButtonMethod}
                  user={this.props.user} />
              </Nav>
            </Collapse>
          </Container>
        </Navbar>
      </div>
    );
  }
}
```

Затем создайте домашнюю страницу для приложения. Создайте новый файл в `./src` каталоге `Welcome.js` и добавьте указанный ниже код.

```JSX
import React from 'react';
import {
  Button,
  Jumbotron } from 'reactstrap';

function WelcomeContent(props) {
  // If authenticated, greet the user
  if (props.isAuthenticated) {
    return (
      <div>
        <h4>Welcome {props.user.displayName}!</h4>
        <p>Use the navigation bar at the top of the page to get started.</p>
      </div>
    );
  }

  // Not authenticated, present a sign in button
  return <Button color="primary" onClick={props.authButtonMethod}>Click here to sign in</Button>;
}

export default class Welcome extends React.Component {
  render() {
    return (
      <Jumbotron>
        <h1>React Graph Tutorial</h1>
        <p className="lead">
            This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from React
        </p>
        <WelcomeContent
          isAuthenticated={this.props.isAuthenticated}
          user={this.props.user}
          authButtonMethod={this.props.authButtonMethod} />
      </Jumbotron>
    );
  }
}
```

Теперь создайте сообщение об ошибке, чтобы отобразить сообщения для пользователя. Создайте новый файл в `./src` каталоге `ErrorMessage.js` и добавьте указанный ниже код.

```JSX
import React from 'react';
import { Alert } from 'reactstrap';

export default class ErrorMessage extends React.Component {
  render() {
    let debug = null;
    if (this.props.debug) {
      debug = <pre className="alert-pre border bg-light p-2"><code>{this.props.debug}</code></pre>;
    }
    return (
      <Alert color="danger">
        <p className="mb-3">{this.props.message}</p>
        {debug}
      </Alert>
    );
  }
}
```

Теперь, когда эти базовые компоненты определены, обновите приложение, чтобы использовать их. Сначала откройте `./src/index.css` файл и замените все его содержимое на приведенный ниже код.

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

Затем откройте `./src/App.js` и замените все содержимое приведенным ниже.

```JSX
import React, { Component } from 'react';
import { BrowserRouter as Router, Route } from 'react-router-dom';
import { Container } from 'reactstrap';
import NavBar from './NavBar';
import ErrorMessage from './ErrorMessage';
import Welcome from './Welcome';
import 'bootstrap/dist/css/bootstrap.css';

class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isAuthenticated: false,
      user: {},
      error: null
    };
  }

  render() {
    let error = null;
    if (this.state.error) {
      error = <ErrorMessage message={this.state.error.message} debug={this.state.error.debug} />;
    }

    return (
      <Router>
        <div>
          <NavBar
            isAuthenticated={this.state.isAuthenticated}
            authButtonMethod={null}
            user={this.state.user}/>
          <Container>
            {error}
            <Route exact path="/"
              render={(props) =>
                <Welcome {...props}
                  isAuthenticated={this.state.isAuthenticated}
                  user={this.state.user}
                  authButtonMethod={null} />
              } />
          </Container>
        </div>
      </Router>
    );
  }

  setErrorMessage(message, debug) {
    this.setState({
      error: {message: message, debug: debug}
    });
  }
}

export default App;
```

Сохраните все изменения и обновите страницу. Теперь приложение должно выглядеть по-другому.

![Снимок экрана с переработанной домашней страницей](images/create-app-01.png)
