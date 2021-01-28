<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e3a60-101">В этом разделе вы создадим новое приложение React.</span><span class="sxs-lookup"><span data-stu-id="e3a60-101">In this section you'll create a new React app.</span></span>

1. <span data-ttu-id="e3a60-102">Откройте интерфейс командной строки (CLI), перейдите в каталог, в котором у вас есть права на создание файлов, и запустите следующие команды, чтобы создать новое приложение React.</span><span class="sxs-lookup"><span data-stu-id="e3a60-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following commands to create a new React app.</span></span>

    ```Shell
    npx create-react-app@4.0.1 graph-tutorial --template typescript
    ```

1. <span data-ttu-id="e3a60-103">После завершения команды перенастройте каталог в CLI и запустите следующую команду, чтобы `graph-tutorial` запустить локальный веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="e3a60-103">Once the command finishes, change to the `graph-tutorial` directory in your CLI and run the following command to start a local web server.</span></span>

    ```Shell
    yarn start
    ```

    > [!NOTE]
    > <span data-ttu-id="e3a60-104">Если [yarn](https://yarnpkg.com/) не установлен, вы можете использовать `npm start` его.</span><span class="sxs-lookup"><span data-stu-id="e3a60-104">If you do not have [Yarn](https://yarnpkg.com/) installed, you can use `npm start` instead.</span></span>

<span data-ttu-id="e3a60-105">Откроется браузер по умолчанию [https://localhost:3000/](https://localhost:3000) со страницей React по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e3a60-105">Your default browser opens to [https://localhost:3000/](https://localhost:3000) with a default React page.</span></span> <span data-ttu-id="e3a60-106">Если браузер не открывается, откройте его и найдите, чтобы убедиться, [https://localhost:3000/](https://localhost:3000) что новое приложение работает.</span><span class="sxs-lookup"><span data-stu-id="e3a60-106">If your browser doesn't open, open it and browse to [https://localhost:3000/](https://localhost:3000) to verify that the new app works.</span></span>

## <a name="add-node-packages"></a><span data-ttu-id="e3a60-107">Добавление пакетов node</span><span class="sxs-lookup"><span data-stu-id="e3a60-107">Add Node packages</span></span>

<span data-ttu-id="e3a60-108">Прежде чем двигаться дальше, установите некоторые дополнительные пакеты, которые вы будете использовать позже:</span><span class="sxs-lookup"><span data-stu-id="e3a60-108">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="e3a60-109">[react-router-dom для](https://github.com/ReactTraining/react-router) декларативной маршрутизирования в приложении React.</span><span class="sxs-lookup"><span data-stu-id="e3a60-109">[react-router-dom](https://github.com/ReactTraining/react-router) for declarative routing inside the React app.</span></span>
- <span data-ttu-id="e3a60-110">[bootstrap](https://github.com/twbs/bootstrap) для оформления и общих компонентов.</span><span class="sxs-lookup"><span data-stu-id="e3a60-110">[bootstrap](https://github.com/twbs/bootstrap) for styling and common components.</span></span>
- <span data-ttu-id="e3a60-111">[reactstrap](https://github.com/reactstrap/reactstrap) для компонентов React на основе Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="e3a60-111">[reactstrap](https://github.com/reactstrap/reactstrap) for React components based on Bootstrap.</span></span>
- <span data-ttu-id="e3a60-112">[fontawesome-free](https://github.com/FortAwesome/Font-Awesome) для значков.</span><span class="sxs-lookup"><span data-stu-id="e3a60-112">[fontawesome-free](https://github.com/FortAwesome/Font-Awesome) for icons.</span></span>
- <span data-ttu-id="e3a60-113">[время](https://github.com/moment/moment) форматирования дат и времени.</span><span class="sxs-lookup"><span data-stu-id="e3a60-113">[moment](https://github.com/moment/moment) for formatting dates and times.</span></span>
- <span data-ttu-id="e3a60-114">[windows-iana для](https://github.com/rubenillodo/windows-iana) перевода часовых поясов Windows в формат IANA.</span><span class="sxs-lookup"><span data-stu-id="e3a60-114">[windows-iana](https://github.com/rubenillodo/windows-iana) for translating Windows time zones to IANA format.</span></span>
- <span data-ttu-id="e3a60-115">[msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) для проверки подлинности в Azure Active Directory и получения маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="e3a60-115">[msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) for authenticating to Azure Active Directory and retrieving access tokens.</span></span>
- <span data-ttu-id="e3a60-116">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) для звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e3a60-116">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="e3a60-117">В CLI запустите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="e3a60-117">Run the following command in your CLI.</span></span>

```Shell
yarn add react-router-dom@5.2.0 @types/react-router-dom@5.1.7 bootstrap@4.6.0 reactstrap@8.9.0 @types/reactstrap@8.7.2 @fortawesome/fontawesome-free@5.15.2
yarn add moment@2.29.1 moment-timezone@0.5.32 windows-iana@4.2.1 @azure/msal-browser@2.10.0 @microsoft/microsoft-graph-client@2.2.1 @types/microsoft-graph@1.28.0
```

## <a name="design-the-app"></a><span data-ttu-id="e3a60-118">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="e3a60-118">Design the app</span></span>

<span data-ttu-id="e3a60-119">Для начала создайте панель на панели для приложения.</span><span class="sxs-lookup"><span data-stu-id="e3a60-119">Start by creating a navbar for the app.</span></span>

1. <span data-ttu-id="e3a60-120">Создайте новый файл в `./src` каталоге с именем `NavBar.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="e3a60-120">Create a new file in the `./src` directory named `NavBar.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. <span data-ttu-id="e3a60-121">Создайте домашняя страница для приложения.</span><span class="sxs-lookup"><span data-stu-id="e3a60-121">Create a home page for the app.</span></span> <span data-ttu-id="e3a60-122">Создайте новый файл в `./src` каталоге с именем `Welcome.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="e3a60-122">Create a new file in the `./src` directory named `Welcome.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. <span data-ttu-id="e3a60-123">Создайте сообщение об ошибке для отображения сообщений пользователю.</span><span class="sxs-lookup"><span data-stu-id="e3a60-123">Create an error message display to display messages to the user.</span></span> <span data-ttu-id="e3a60-124">Создайте новый файл в `./src` каталоге с именем `ErrorMessage.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="e3a60-124">Create a new file in the `./src` directory named `ErrorMessage.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. <span data-ttu-id="e3a60-125">Откройте файл `./src/index.css` и замените его содержимое приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="e3a60-125">Open the `./src/index.css` file and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. <span data-ttu-id="e3a60-126">Откройте `./src/App.tsx` и замените все его содержимое на следующее.</span><span class="sxs-lookup"><span data-stu-id="e3a60-126">Open `./src/App.tsx` and replace its entire contents with the following.</span></span>

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

1. <span data-ttu-id="e3a60-127">Сохраните все изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="e3a60-127">Save all of your changes and restart the app.</span></span> <span data-ttu-id="e3a60-128">Теперь приложение должно выглядеть совершенно по-другому.</span><span class="sxs-lookup"><span data-stu-id="e3a60-128">Now, the app should look very different.</span></span>

    ![Снимок экрана с измененной домашней страницей](images/create-app-01.png)
