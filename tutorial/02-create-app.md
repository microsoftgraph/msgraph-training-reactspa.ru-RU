<!-- markdownlint-disable MD002 MD041 -->

В этом разделе вы создадим новое приложение React.

1. Откройте интерфейс командной строки (CLI), перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующие команды, чтобы создать новое приложение React.

    ```Shell
    npx create-react-app@4.0.1 graph-tutorial --template typescript
    ```

1. После завершения команды перенастройте каталог в CLI и запустите следующую команду, чтобы `graph-tutorial` запустить локальный веб-сервер.

    ```Shell
    yarn start
    ```

    > [!NOTE]
    > Если [yarn](https://yarnpkg.com/) не установлен, вы можете использовать `npm start` его.

Откроется браузер по умолчанию [https://localhost:3000/](https://localhost:3000) со страницей React по умолчанию. Если браузер не открывается, откройте его и найдите, чтобы убедиться, [https://localhost:3000/](https://localhost:3000) что новое приложение работает.

## <a name="add-node-packages"></a>Добавление пакетов node

Прежде чем двигаться дальше, установите некоторые дополнительные пакеты, которые вы будете использовать позже:

- [react-router-dom для](https://github.com/ReactTraining/react-router) декларативной маршрутизирования в приложении React.
- [bootstrap](https://github.com/twbs/bootstrap) для оформления и общих компонентов.
- [reactstrap](https://github.com/reactstrap/reactstrap) для компонентов React на основе Bootstrap.
- [fontawesome-free](https://github.com/FortAwesome/Font-Awesome) для значков.
- [время](https://github.com/moment/moment) форматирования дат и времени.
- [windows-iana для](https://github.com/rubenillodo/windows-iana) перевода часовых поясов Windows в формат IANA.
- [msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) для проверки подлинности в Azure Active Directory и получения маркеров доступа.
- [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) для звонков в Microsoft Graph.

В CLI запустите следующую команду:

```Shell
yarn add react-router-dom@5.2.0 @types/react-router-dom@5.1.7 bootstrap@4.6.0 reactstrap@8.9.0 @types/reactstrap@8.7.2 @fortawesome/fontawesome-free@5.15.2
yarn add moment@2.29.1 moment-timezone@0.5.32 windows-iana@4.2.1 @azure/msal-browser@2.10.0 @microsoft/microsoft-graph-client@2.2.1 @types/microsoft-graph@1.28.0
```

## <a name="design-the-app"></a>Проектирование приложения

Для начала создайте панель на панели для приложения.

1. Создайте новый файл в `./src` каталоге с именем `NavBar.tsx` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. Создайте домашняя страница для приложения. Создайте новый файл в `./src` каталоге с именем `Welcome.tsx` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. Создайте сообщение об ошибке для отображения сообщений пользователю. Создайте новый файл в `./src` каталоге с именем `ErrorMessage.tsx` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. Откройте файл `./src/index.css` и замените его содержимое приведенным ниже кодом.

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. Откройте `./src/App.tsx` и замените все его содержимое на следующее.

    ```typescript
    import React, { Component } from 'react';
    import { BrowserRouter as Router, Route, Redirect } from 'react-router-dom';
    import { Container } from 'reactstrap';
    import NavBar from './NavBar';
    import ErrorMessage from './ErrorMessage';
    import Welcome from './Welcome';
    import 'bootstrap/dist/css/bootstrap.css';

    class App extends Component<any> {
      render() {
        let error = null;
        if (this.props.error) {
          error = <ErrorMessage
            message={this.props.error.message}
            debug={this.props.error.debug} />;
        }

        return (
          <Router>
            <div>
              <NavBar
                isAuthenticated={this.props.isAuthenticated}
                authButtonMethod={this.props.isAuthenticated ? this.props.logout : this.props.login}
                user={this.props.user} />
              <Container>
                {error}
                <Route exact path="/"
                  render={(props) =>
                    <Welcome {...props}
                      isAuthenticated={this.props.isAuthenticated}
                      user={this.props.user}
                      authButtonMethod={this.props.login} />
                  } />
              </Container>
            </div>
          </Router>
        );
      }
    }

    export default App;
    ```

1. Сохраните все изменения и перезапустите приложение. Теперь приложение должно выглядеть совершенно по-другому.

    ![Снимок экрана с измененной домашней страницей](images/create-app-01.png)
