<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ee6cd-101">В этом разделе вы создадите новое приложение для реагирования.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-101">In this section you'll create a new React app.</span></span>

1. <span data-ttu-id="ee6cd-102">Откройте интерфейс командной строки (CLI), перейдите к каталогу, в котором у вас есть права на создание файлов, и выполните следующие команды, чтобы создать приложение для реагирования.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-102">Open your command-line interface (CLI), navigate to a directory where you have rights to create files, and run the following commands to create a new React app.</span></span>

    ```Shell
    npx create-react-app@3.4.1 graph-tutorial --template typescript
    ```

1. <span data-ttu-id="ee6cd-103">После завершения выполнения команды перейдите к `graph-tutorial` каталогу в CLI и выполните следующую команду для запуска локального веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-103">Once the command finishes, change to the `graph-tutorial` directory in your CLI and run the following command to start a local web server.</span></span>

    ```Shell
    npm start
    ```

<span data-ttu-id="ee6cd-104">Браузер, используемый по умолчанию, открывается [https://localhost:3000/](https://localhost:3000) с используемой по умолчанию страницей отклика.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-104">Your default browser opens to [https://localhost:3000/](https://localhost:3000) with a default React page.</span></span> <span data-ttu-id="ee6cd-105">Если браузер не открыт, откройте его и перейдите к странице, чтобы [https://localhost:3000/](https://localhost:3000) убедиться, что новое приложение работает.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-105">If your browser doesn't open, open it and browse to [https://localhost:3000/](https://localhost:3000) to verify that the new app works.</span></span>

## <a name="add-node-packages"></a><span data-ttu-id="ee6cd-106">Добавление пакетов узлов</span><span class="sxs-lookup"><span data-stu-id="ee6cd-106">Add Node packages</span></span>

<span data-ttu-id="ee6cd-107">Прежде чем переходить, установите несколько дополнительных пакетов, которые будут использоваться позже:</span><span class="sxs-lookup"><span data-stu-id="ee6cd-107">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="ee6cd-108">[реагирующий маршрутизатор — модель DOM](https://github.com/ReactTraining/react-router) для декларативной маршрутизации в приложении реагируя.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-108">[react-router-dom](https://github.com/ReactTraining/react-router) for declarative routing inside the React app.</span></span>
- <span data-ttu-id="ee6cd-109">[Начальная](https://github.com/twbs/bootstrap) Загрузка стилей и общих компонентов.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-109">[bootstrap](https://github.com/twbs/bootstrap) for styling and common components.</span></span>
- <span data-ttu-id="ee6cd-110">[реактстрап](https://github.com/reactstrap/reactstrap) для отклика компонентов на основе начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-110">[reactstrap](https://github.com/reactstrap/reactstrap) for React components based on Bootstrap.</span></span>
- <span data-ttu-id="ee6cd-111">[фонтавесоме — без](https://github.com/FortAwesome/Font-Awesome) значков.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-111">[fontawesome-free](https://github.com/FortAwesome/Font-Awesome) for icons.</span></span>
- <span data-ttu-id="ee6cd-112">[время для форматирования](https://github.com/moment/moment) даты и времени.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-112">[moment](https://github.com/moment/moment) for formatting dates and times.</span></span>
- <span data-ttu-id="ee6cd-113">[Windows — IANA](https://github.com/rubenillodo/windows-iana) для перевода часовых поясов Windows в формат IANA.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-113">[windows-iana](https://github.com/rubenillodo/windows-iana) for translating Windows time zones to IANA format.</span></span>
- <span data-ttu-id="ee6cd-114">[msal — браузер](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) для проверки подлинности Azure Active Directory и получения маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-114">[msal-browser](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) for authenticating to Azure Active Directory and retrieving access tokens.</span></span>
- <span data-ttu-id="ee6cd-115">[Microsoft — Graph — клиент](https://github.com/microsoftgraph/msgraph-sdk-javascript) для совершения звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-115">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="ee6cd-116">Выполните следующую команду в командной панели CLI.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-116">Run the following command in your CLI.</span></span>

```Shell
npm install react-router-dom@5.2.0 @types/react-router-dom@5.1.5 bootstrap@4.5.2 reactstrap@8.5.1 @types/reactstrap@8.5.1 @fortawesome/fontawesome-free@5.14.0
npm install moment@2.27.0 moment-timezone@0.5.31 windows-iana@4.2.1 @azure/msal-browser@2.1.0 @microsoft/microsoft-graph-client@2.0.0 @types/microsoft-graph@1.18.0
```

## <a name="design-the-app"></a><span data-ttu-id="ee6cd-117">Проектирование приложения</span><span class="sxs-lookup"><span data-stu-id="ee6cd-117">Design the app</span></span>

<span data-ttu-id="ee6cd-118">Для начала создайте панель навигации для приложения.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-118">Start by creating a navbar for the app.</span></span>

1. <span data-ttu-id="ee6cd-119">Создайте новый файл в `./src` каталоге `NavBar.tsx` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-119">Create a new file in the `./src` directory named `NavBar.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/NavBar.tsx" id="NavBarSnippet":::

1. <span data-ttu-id="ee6cd-120">Создайте домашнюю страницу для приложения.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-120">Create a home page for the app.</span></span> <span data-ttu-id="ee6cd-121">Создайте новый файл в `./src` каталоге `Welcome.tsx` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-121">Create a new file in the `./src` directory named `Welcome.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Welcome.tsx" id="WelcomeSnippet":::

1. <span data-ttu-id="ee6cd-122">Создайте сообщение об ошибке для отображения сообщений пользователю.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-122">Create an error message display to display messages to the user.</span></span> <span data-ttu-id="ee6cd-123">Создайте новый файл в `./src` каталоге `ErrorMessage.tsx` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-123">Create a new file in the `./src` directory named `ErrorMessage.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/ErrorMessage.tsx" id="ErrorMessageSnippet":::

1. <span data-ttu-id="ee6cd-124">Откройте файл `./src/index.css` и замените его содержимое приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-124">Open the `./src/index.css` file and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/index.css":::

1. <span data-ttu-id="ee6cd-125">Откройте `./src/App.tsx` и замените все содержимое приведенным ниже.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-125">Open `./src/App.tsx` and replace its entire contents with the following.</span></span>

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

1. <span data-ttu-id="ee6cd-126">Сохраните все изменения и обновите страницу.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-126">Save all of your changes and refresh the page.</span></span> <span data-ttu-id="ee6cd-127">Теперь приложение должно выглядеть по-другому.</span><span class="sxs-lookup"><span data-stu-id="ee6cd-127">Now, the app should look very different.</span></span>

    ![Снимок экрана с переработанной домашней страницей](images/create-app-01.png)
