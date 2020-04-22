<!-- markdownlint-disable MD002 MD041 -->

В этом разделе вы создадите новое приложение для реагирования.

1. Откройте интерфейс командной строки (CLI), перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующие команды, чтобы создать приложение для реагирования.

    ```Shell
    npx create-react-app@3.4.0 graph-tutorial --template typescript
    ```

1. После завершения выполнения команды перейдите к `graph-tutorial` каталогу в CLI и выполните следующую команду для запуска локального веб-сервера.

    ```Shell
    npm start
    ```

Браузер, используемый по умолчанию, открывается [https://localhost:3000/](https://localhost:3000) с используемой по умолчанию страницей отклика. Если браузер не открыт, откройте его и перейдите к [https://localhost:3000/](https://localhost:3000) странице, чтобы убедиться, что новое приложение работает.

## <a name="add-node-packages"></a>Добавление пакетов узлов

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
npm install react-router-dom@5.1.2 @types/react-router-dom@5.1.3 bootstrap@4.4.1 reactstrap@8.4.1 @types/reactstrap@8.4.2
npm install @fortawesome/fontawesome-free@5.12.1 moment@2.24.0 msal@1.2.1 @microsoft/microsoft-graph-client@2.0.0 @types/microsoft-graph@1.12.0
```

## <a name="design-the-app"></a>Проектирование приложения

Для начала создайте панель навигации для приложения.

1. Создайте новый файл в `./src` каталоге `NavBar.tsx` и добавьте указанный ниже код.

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. Создайте домашнюю страницу для приложения. Создайте новый файл в `./src` каталоге `Welcome.tsx` и добавьте указанный ниже код.

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. Создайте сообщение об ошибке для отображения сообщений пользователю. Создайте новый файл в `./src` каталоге `ErrorMessage.tsx` и добавьте указанный ниже код.

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. Откройте файл `./src/index.css` и замените его содержимое приведенным ниже кодом.

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. Откройте `./src/App.tsx` и замените все содержимое приведенным ниже.

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
                user={this.props.user}/>
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

1. Сохраните все изменения и обновите страницу. Теперь приложение должно выглядеть по-другому.

    ![Снимок экрана с переработанной домашней страницей](images/create-app-01.png)
