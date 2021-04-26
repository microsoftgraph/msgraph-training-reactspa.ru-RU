<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="588a6-101">В этом разделе вы создайте новое приложение React.</span><span class="sxs-lookup"><span data-stu-id="588a6-101">In this section you'll create a new React app.</span></span>

1. <span data-ttu-id="588a6-102">Откройте интерфейс командной строки (CLI), перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующие команды для создания React приложения.</span><span class="sxs-lookup"><span data-stu-id="588a6-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following commands to create a new React app.</span></span>

    ```Shell
    yarn create react-app graph-tutorial --template typescript
    ```

1. <span data-ttu-id="588a6-103">После завершения команды измените каталог в CLI и запустите следующую команду, `graph-tutorial` чтобы запустить локальный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="588a6-103">Once the command finishes, change to the `graph-tutorial` directory in your CLI and run the following command to start a local web server.</span></span>

    ```Shell
    yarn start
    ```

    > [!NOTE]
    > <span data-ttu-id="588a6-104">Если у вас нет [установленной пряжи,](https://yarnpkg.com/) вы можете использовать `npm start` вместо этого.</span><span class="sxs-lookup"><span data-stu-id="588a6-104">If you do not have [Yarn](https://yarnpkg.com/) installed, you can use `npm start` instead.</span></span>

<span data-ttu-id="588a6-105">Браузер по умолчанию открывается [https://localhost:3000/](https://localhost:3000) на странице React по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="588a6-105">Your default browser opens to [https://localhost:3000/](https://localhost:3000) with a default React page.</span></span> <span data-ttu-id="588a6-106">Если браузер не открыт, откройте его и просмотрите, чтобы убедиться, [https://localhost:3000/](https://localhost:3000) что новое приложение работает.</span><span class="sxs-lookup"><span data-stu-id="588a6-106">If your browser doesn't open, open it and browse to [https://localhost:3000/](https://localhost:3000) to verify that the new app works.</span></span>

## <a name="add-node-packages"></a><span data-ttu-id="588a6-107">Добавление пакетов node</span><span class="sxs-lookup"><span data-stu-id="588a6-107">Add Node packages</span></span>

<span data-ttu-id="588a6-108">Прежде чем двигаться дальше, установите дополнительные пакеты, которые вы будете использовать позже:</span><span class="sxs-lookup"><span data-stu-id="588a6-108">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="588a6-109">[react-router-dom](https://github.com/ReactTraining/react-router) для декларативной маршрутизирования внутри React приложения.</span><span class="sxs-lookup"><span data-stu-id="588a6-109">[react-router-dom](https://github.com/ReactTraining/react-router) for declarative routing inside the React app.</span></span>
- <span data-ttu-id="588a6-110">[bootstrap](https://github.com/twbs/bootstrap) для укладки и общих компонентов.</span><span class="sxs-lookup"><span data-stu-id="588a6-110">[bootstrap](https://github.com/twbs/bootstrap) for styling and common components.</span></span>
- <span data-ttu-id="588a6-111">[reactstrap](https://github.com/reactstrap/reactstrap) для React компонентов на основе Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="588a6-111">[reactstrap](https://github.com/reactstrap/reactstrap) for React components based on Bootstrap.</span></span>
- <span data-ttu-id="588a6-112">[fontawesome-free](https://github.com/FortAwesome/Font-Awesome) для значков.</span><span class="sxs-lookup"><span data-stu-id="588a6-112">[fontawesome-free](https://github.com/FortAwesome/Font-Awesome) for icons.</span></span>
- <span data-ttu-id="588a6-113">[время](https://github.com/moment/moment) форматирования дат и времени.</span><span class="sxs-lookup"><span data-stu-id="588a6-113">[moment](https://github.com/moment/moment) for formatting dates and times.</span></span>
- <span data-ttu-id="588a6-114">[windows-iana](https://github.com/rubenillodo/windows-iana) для перевода Windows часовых поясов в формат IANA.</span><span class="sxs-lookup"><span data-stu-id="588a6-114">[windows-iana](https://github.com/rubenillodo/windows-iana) for translating Windows time zones to IANA format.</span></span>
- <span data-ttu-id="588a6-115">[msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) для проверки подлинности Azure Active Directory и получения маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="588a6-115">[msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) for authenticating to Azure Active Directory and retrieving access tokens.</span></span>
- <span data-ttu-id="588a6-116">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) для звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="588a6-116">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="588a6-117">Запустите следующую команду в CLI.</span><span class="sxs-lookup"><span data-stu-id="588a6-117">Run the following command in your CLI.</span></span>

```Shell
yarn add react-router-dom@5.2.0 bootstrap@4.6.0 reactstrap@8.9.0 @fortawesome/fontawesome-free@5.15.3
yarn add moment-timezone@0.5.33 windows-iana@5.0.2 @azure/msal-browser@2.14.1 @microsoft/microsoft-graph-client@2.2.1
yarn add -D @types/react-router-dom@5.1.7 @types/microsoft-graph@1.36.0
```

## <a name="design-the-app"></a><span data-ttu-id="588a6-118">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="588a6-118">Design the app</span></span>

<span data-ttu-id="588a6-119">Начните с создания navbar для приложения.</span><span class="sxs-lookup"><span data-stu-id="588a6-119">Start by creating a navbar for the app.</span></span>

1. <span data-ttu-id="588a6-120">Создайте новый файл в `./src` каталоге с именем `NavBar.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="588a6-120">Create a new file in the `./src` directory named `NavBar.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. <span data-ttu-id="588a6-121">Создание домашней страницы для приложения.</span><span class="sxs-lookup"><span data-stu-id="588a6-121">Create a home page for the app.</span></span> <span data-ttu-id="588a6-122">Создайте новый файл в `./src` каталоге с именем `Welcome.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="588a6-122">Create a new file in the `./src` directory named `Welcome.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. <span data-ttu-id="588a6-123">Создайте дисплей сообщения об ошибке для отображения сообщений пользователю.</span><span class="sxs-lookup"><span data-stu-id="588a6-123">Create an error message display to display messages to the user.</span></span> <span data-ttu-id="588a6-124">Создайте новый файл в `./src` каталоге с именем `ErrorMessage.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="588a6-124">Create a new file in the `./src` directory named `ErrorMessage.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. <span data-ttu-id="588a6-125">Откройте файл `./src/index.css` и замените его содержимое приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="588a6-125">Open the `./src/index.css` file and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. <span data-ttu-id="588a6-126">Откройте `./src/App.tsx` и замените все содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="588a6-126">Open `./src/App.tsx` and replace its entire contents with the following.</span></span>

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

1. <span data-ttu-id="588a6-127">Сохраните все изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="588a6-127">Save all of your changes and restart the app.</span></span> <span data-ttu-id="588a6-128">Теперь приложение должно выглядеть совсем по-другому.</span><span class="sxs-lookup"><span data-stu-id="588a6-128">Now, the app should look very different.</span></span>

    ![Снимок экрана: обновленная домашняя страница](images/create-app-01.png)
