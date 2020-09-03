<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="871eb-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="871eb-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="871eb-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="871eb-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="871eb-103">На этом этапе библиотека [библиотеки проверки подлинности (Майкрософт](https://github.com/AzureAD/microsoft-authentication-library-for-js) ) будет интегрирована в приложение.</span><span class="sxs-lookup"><span data-stu-id="871eb-103">In this step you will integrate the [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) library into the application.</span></span>

1. <span data-ttu-id="871eb-104">Создайте новый файл в `./src` каталоге `Config.ts` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="871eb-104">Create a new file in the `./src` directory named `Config.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Config.example.ts":::

    <span data-ttu-id="871eb-105">Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="871eb-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="871eb-106">Если вы используете систему управления версиями (например, Git), то теперь мы бы не могли исключить `Config.ts` файл из системы управления версиями, чтобы избежать случайной утечки идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="871eb-106">If you're using source control such as git, now would be a good time to exclude the `Config.ts` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="871eb-107">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="871eb-107">Implement sign-in</span></span>

<span data-ttu-id="871eb-108">В этом разделе вы создадите поставщика проверки подлинности и выполните вход и выход.</span><span class="sxs-lookup"><span data-stu-id="871eb-108">In this section you'll create an authentication provider and implement sign-in and sign-out.</span></span>

1. <span data-ttu-id="871eb-109">Создайте новый файл в `./src` каталоге `AuthProvider.tsx` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="871eb-109">Create a new file in the `./src` directory named `AuthProvider.tsx` and add the following code.</span></span>

    ```typescript
    import React from 'react';
    import { PublicClientApplication } from '@azure/msal-browser';

    import { config } from './Config';

    export interface AuthComponentProps {
      error: any;
      isAuthenticated: boolean;
      user: any;
      login: Function;
      logout: Function;
      getAccessToken: Function;
      setError: Function;
    }

    interface AuthProviderState {
      error: any;
      isAuthenticated: boolean;
      user: any;
    }

    export default function withAuthProvider<T extends React.Component<AuthComponentProps>>
      (WrappedComponent: new(props: AuthComponentProps, context?: any) => T): React.ComponentClass {
      return class extends React.Component<any, AuthProviderState> {
        private publicClientApplication: PublicClientApplication;

        constructor(props: any) {
          super(props);
          this.state = {
            error: null,
            isAuthenticated: false,
            user: {}
          };

          // Initialize the MSAL application object
          this.publicClientApplication = new PublicClientApplication({
            auth: {
                clientId: config.appId,
                redirectUri: config.redirectUri
            },
            cache: {
                cacheLocation: "sessionStorage",
                storeAuthStateInCookie: true
            }
          });
        }

        componentDidMount() {
          // If MSAL already has an account, the user
          // is already logged in
          const accounts = this.publicClientApplication.getAllAccounts();

          if (accounts && accounts.length > 0) {
            // Enhance user object with data from Graph
            this.getUserProfile();
          }
        }

        render() {
          return <WrappedComponent
            error = { this.state.error }
            isAuthenticated = { this.state.isAuthenticated }
            user = { this.state.user }
            login = { () => this.login() }
            logout = { () => this.logout() }
            getAccessToken = { (scopes: string[]) => this.getAccessToken(scopes)}
            setError = { (message: string, debug: string) => this.setErrorMessage(message, debug)}
            {...this.props} {...this.state} />;
        }

        async login() {
          try {
            // Login via popup
            await this.publicClientApplication.loginPopup(
                {
                  scopes: config.scopes,
                  prompt: "select_account"
              });

            // After login, get the user's profile
            await this.getUserProfile();
          }
          catch(err) {
            this.setState({
              isAuthenticated: false,
              user: {},
              error: this.normalizeError(err)
            });
          }
        }

        logout() {
          this.publicClientApplication.logout();
        }

        async getAccessToken(scopes: string[]): Promise<string> {
          try {
            const accounts = this.publicClientApplication
              .getAllAccounts();

            if (accounts.length <= 0) throw new Error('login_required');
            // Get the access token silently
            // If the cache contains a non-expired token, this function
            // will just return the cached token. Otherwise, it will
            // make a request to the Azure OAuth endpoint to get a token
            var silentResult = await this.publicClientApplication
                .acquireTokenSilent({
                  scopes: scopes,
                  account: accounts[0]
                });

            return silentResult.accessToken;
          } catch (err) {
            // If a silent request fails, it may be because the user needs
            // to login or grant consent to one or more of the requested scopes
            if (this.isInteractionRequired(err)) {
              var interactiveResult = await this.publicClientApplication
                  .acquireTokenPopup({
                    scopes: scopes
                  });

              return interactiveResult.accessToken;
            } else {
              throw err;
            }
          }
        }

        async getUserProfile() {
          try {
            var accessToken = await this.getAccessToken(config.scopes);

            if (accessToken) {
              // TEMPORARY: Display the token in the error flash
              this.setState({
                isAuthenticated: true,
                error: { message: "Access token:", debug: accessToken }
              });
            }
          }
          catch(err) {
            this.setState({
              isAuthenticated: false,
              user: {},
              error: this.normalizeError(err)
            });
          }
        }

        setErrorMessage(message: string, debug: string) {
          this.setState({
            error: {message: message, debug: debug}
          });
        }

        normalizeError(error: string | Error): any {
          var normalizedError = {};
          if (typeof(error) === 'string') {
            var errParts = error.split('|');
            normalizedError = errParts.length > 1 ?
              { message: errParts[1], debug: errParts[0] } :
              { message: error };
          } else {
            normalizedError = {
              message: error.message,
              debug: JSON.stringify(error)
            };
          }
          return normalizedError;
        }

        isInteractionRequired(error: Error): boolean {
          if (!error.message || error.message.length <= 0) {
            return false;
          }

          return (
            error.message.indexOf('consent_required') > -1 ||
            error.message.indexOf('interaction_required') > -1 ||
            error.message.indexOf('login_required') > -1 ||
            error.message.indexOf('no_account_in_silent_request') > -1
          );
        }
      }
    }
    ```

1. <span data-ttu-id="871eb-110">Откройте `./src/App.tsx` и добавьте приведенный ниже `import` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="871eb-110">Open `./src/App.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';
    ```

1. <span data-ttu-id="871eb-111">Замените строку `class App extends Component<any> {` на приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="871eb-111">Replace the line `class App extends Component<any> {` with the following.</span></span>

    ```typescript
    class App extends Component<AuthComponentProps> {
    ```

1. <span data-ttu-id="871eb-112">Замените строку `export default App;` на приведенную ниже строку.</span><span class="sxs-lookup"><span data-stu-id="871eb-112">Replace the line `export default App;` with the following.</span></span>

    ```typescript
    export default withAuthProvider(App);
    ```

1. <span data-ttu-id="871eb-113">Сохраните изменения и обновите браузер.</span><span class="sxs-lookup"><span data-stu-id="871eb-113">Save your changes and refresh the browser.</span></span> <span data-ttu-id="871eb-114">Нажмите кнопку входа, и вы увидите всплывающее окно, которое загружается `https://login.microsoftonline.com` .</span><span class="sxs-lookup"><span data-stu-id="871eb-114">Click the sign-in button and you should see a pop-up window that loads `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="871eb-115">Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями.</span><span class="sxs-lookup"><span data-stu-id="871eb-115">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="871eb-116">Страница приложения должна обновляться, отображая маркер.</span><span class="sxs-lookup"><span data-stu-id="871eb-116">The app page should refresh, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="871eb-117">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="871eb-117">Get user details</span></span>

<span data-ttu-id="871eb-118">В этом разделе вы узнаете сведения о пользователе из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="871eb-118">In this section you will get the user's details from Microsoft Graph.</span></span>

1. <span data-ttu-id="871eb-119">Создайте новый файл в `./src` каталоге `GraphService.ts` и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="871eb-119">Create a new file in the `./src` directory called `GraphService.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="graphServiceSnippet1":::

    <span data-ttu-id="871eb-120">При этом реализуется `getUserDetails` функция, которая использует пакет SDK Microsoft Graph для вызова `/me` конечной точки и возврата результата.</span><span class="sxs-lookup"><span data-stu-id="871eb-120">This implements the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="871eb-121">Откройте `./src/AuthProvider.tsx` и добавьте приведенный ниже `import` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="871eb-121">Open `./src/AuthProvider.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import { getUserDetails } from './GraphService';
    ```

1. <span data-ttu-id="871eb-122">Замените имеющуюся функцию `getUserProfile` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="871eb-122">Replace the existing `getUserProfile` function with the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/AuthProvider.tsx" id="getUserProfileSnippet" highlight="6-18":::

1. <span data-ttu-id="871eb-123">Сохраните изменения и запустите приложение, после чего необходимо вернуться на домашнюю страницу, но пользовательский интерфейс должен измениться, чтобы показать, что вы выполнили вход.</span><span class="sxs-lookup"><span data-stu-id="871eb-123">Save your changes and start the app, after sign-in you should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

1. <span data-ttu-id="871eb-125">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** .</span><span class="sxs-lookup"><span data-stu-id="871eb-125">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="871eb-126">При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="871eb-126">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="871eb-128">Хранение и обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="871eb-128">Storing and refreshing tokens</span></span>

<span data-ttu-id="871eb-129">На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="871eb-129">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="871eb-130">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="871eb-130">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="871eb-131">Однако этот маркер кратковременно используется.</span><span class="sxs-lookup"><span data-stu-id="871eb-131">However, this token is short-lived.</span></span> <span data-ttu-id="871eb-132">Срок действия маркера истечет через час после его выдачи.</span><span class="sxs-lookup"><span data-stu-id="871eb-132">The token expires an hour after it is issued.</span></span> <span data-ttu-id="871eb-133">В этом случае маркер обновления становится полезен.</span><span class="sxs-lookup"><span data-stu-id="871eb-133">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="871eb-134">Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.</span><span class="sxs-lookup"><span data-stu-id="871eb-134">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="871eb-135">Так как приложение использует библиотеку MSAL, нет необходимости внедрять логику хранения или обновления маркеров.</span><span class="sxs-lookup"><span data-stu-id="871eb-135">Because the app is using the MSAL library, you do not have to implement any token storage or refresh logic.</span></span> <span data-ttu-id="871eb-136">`PublicClientApplication`Кэширует маркер в сеансе браузера.</span><span class="sxs-lookup"><span data-stu-id="871eb-136">The `PublicClientApplication` caches the token in the browser session.</span></span> <span data-ttu-id="871eb-137">`acquireTokenSilent`Метод сначала проверяет кэшированный маркер, и если срок его действия не истек, он возвращается.</span><span class="sxs-lookup"><span data-stu-id="871eb-137">The `acquireTokenSilent` method first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="871eb-138">Если срок действия истек, он использует кэшированный маркер обновления, чтобы получить новый.</span><span class="sxs-lookup"><span data-stu-id="871eb-138">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="871eb-139">Этот метод более не используется в следующем модуле.</span><span class="sxs-lookup"><span data-stu-id="871eb-139">You'll use this method more in the following module.</span></span>
