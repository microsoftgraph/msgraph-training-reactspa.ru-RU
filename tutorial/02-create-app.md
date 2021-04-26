<!-- markdownlint-disable MD002 MD041 -->

В этом разделе вы создайте новое приложение React.

1. Откройте интерфейс командной строки (CLI), перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующие команды для создания React приложения.

    ```Shell
    yarn create react-app graph-tutorial --template typescript
    ```

1. После завершения команды измените каталог в CLI и запустите следующую команду, `graph-tutorial` чтобы запустить локальный веб-сервер.

    ```Shell
    yarn start
    ```

    > [!NOTE]
    > Если у вас нет [установленной пряжи,](https://yarnpkg.com/) вы можете использовать `npm start` вместо этого.

Браузер по умолчанию открывается [https://localhost:3000/](https://localhost:3000) на странице React по умолчанию. Если браузер не открыт, откройте его и просмотрите, чтобы убедиться, [https://localhost:3000/](https://localhost:3000) что новое приложение работает.

## <a name="add-node-packages"></a>Добавление пакетов node

Прежде чем двигаться дальше, установите дополнительные пакеты, которые вы будете использовать позже:

- [react-router-dom](https://github.com/ReactTraining/react-router) для декларативной маршрутизирования внутри React приложения.
- [bootstrap](https://github.com/twbs/bootstrap) для укладки и общих компонентов.
- [reactstrap](https://github.com/reactstrap/reactstrap) для React компонентов на основе Bootstrap.
- [fontawesome-free](https://github.com/FortAwesome/Font-Awesome) для значков.
- [время](https://github.com/moment/moment) форматирования дат и времени.
- [windows-iana](https://github.com/rubenillodo/windows-iana) для перевода Windows часовых поясов в формат IANA.
- [msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) для проверки подлинности Azure Active Directory и получения маркеров доступа.
- [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) для звонков в Microsoft Graph.

Запустите следующую команду в CLI.

```Shell
yarn add react-router-dom@5.2.0 bootstrap@4.6.0 reactstrap@8.9.0 @fortawesome/fontawesome-free@5.15.3
yarn add moment-timezone@0.5.33 windows-iana@5.0.2 @azure/msal-browser@2.14.1 @microsoft/microsoft-graph-client@2.2.1
yarn add -D @types/react-router-dom@5.1.7 @types/microsoft-graph@1.36.0
```

## <a name="design-the-app"></a>Проектирование приложения

Начните с создания navbar для приложения.

1. Создайте новый файл в `./src` каталоге с именем `NavBar.tsx` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. Создание домашней страницы для приложения. Создайте новый файл в `./src` каталоге с именем `Welcome.tsx` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. Создайте дисплей сообщения об ошибке для отображения сообщений пользователю. Создайте новый файл в `./src` каталоге с именем `ErrorMessage.tsx` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. Откройте файл `./src/index.css` и замените его содержимое приведенным ниже кодом.

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. Откройте `./src/App.tsx` и замените все содержимое следующим.

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

1. Сохраните все изменения и перезапустите приложение. Теперь приложение должно выглядеть совсем по-другому.

    ![Снимок экрана: обновленная домашняя страница](images/create-app-01.png)
