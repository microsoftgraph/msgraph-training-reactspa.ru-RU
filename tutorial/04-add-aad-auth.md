<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="20620-101">В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="20620-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="20620-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="20620-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="20620-103">На этом этапе вы интегрируете библиотеку microsoft [Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) в приложение.</span><span class="sxs-lookup"><span data-stu-id="20620-103">In this step you will integrate the [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) library into the application.</span></span>

1. <span data-ttu-id="20620-104">Создайте новый файл в `./src` каталоге с именем `Config.ts` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="20620-104">Create a new file in the `./src` directory named `Config.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Config.example.ts":::

    <span data-ttu-id="20620-105">Замените `YOUR_APP_ID_HERE` его на ИД приложения на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="20620-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="20620-106">Если вы используете управление исходным кодом, например git, пришло бы время исключить файл из системы управления исходным кодом, чтобы не допустить случайной утечки вашего `Config.ts` ИД приложения.</span><span class="sxs-lookup"><span data-stu-id="20620-106">If you're using source control such as git, now would be a good time to exclude the `Config.ts` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="20620-107">Реализация входов</span><span class="sxs-lookup"><span data-stu-id="20620-107">Implement sign-in</span></span>

<span data-ttu-id="20620-108">В этом разделе вы создадим поставщика проверки подлинности и внедрим вход и выход.</span><span class="sxs-lookup"><span data-stu-id="20620-108">In this section you'll create an authentication provider and implement sign-in and sign-out.</span></span>

1. <span data-ttu-id="20620-109">Создайте новый файл в `./src` каталоге с именем `AuthProvider.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="20620-109">Create a new file in the `./src` directory named `AuthProvider.tsx` and add the following code.</span></span>

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
      (WrappedComponent: new (props: AuthComponentProps, context?: any) => T): React.ComponentClass {
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
            error={ this.state.error }
            isAuthenticated={ this.state.isAuthenticated }
            user={ this.state.user }
            login={ () => this.login() }
            logout={ () => this.logout() }
            getAccessToken={ (scopes: string[]) => this.getAccessToken(scopes) }
            setError={ (message: string, debug: string) => this.setErrorMessage(message, debug) }
            { ...this.props } />;
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
          catch (err) {
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
            error: { message: message, debug: debug }
          });
        }

        normalizeError(error: string | Error): any {
          var normalizedError = {};
          if (typeof (error) === 'string') {
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

1. <span data-ttu-id="20620-110">Откройте `./src/App.tsx` и добавьте следующий `import` выписку в верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="20620-110">Open `./src/App.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';
    ```

1. <span data-ttu-id="20620-111">Замените `class App extends Component<any> {` строку на следующую.</span><span class="sxs-lookup"><span data-stu-id="20620-111">Replace the line `class App extends Component<any> {` with the following.</span></span>

    ```typescript
    class App extends Component<AuthComponentProps> {
    ```

1. <span data-ttu-id="20620-112">Замените `export default App;` строку на следующую.</span><span class="sxs-lookup"><span data-stu-id="20620-112">Replace the line `export default App;` with the following.</span></span>

    ```typescript
    export default withAuthProvider(App);
    ```

1. <span data-ttu-id="20620-113">Сохраните изменения и обновите браузер.</span><span class="sxs-lookup"><span data-stu-id="20620-113">Save your changes and refresh the browser.</span></span> <span data-ttu-id="20620-114">Нажмите кнопку "Вход", и вы увидите всплывающее окно, которое `https://login.microsoftonline.com` загружается.</span><span class="sxs-lookup"><span data-stu-id="20620-114">Click the sign-in button and you should see a pop-up window that loads `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="20620-115">Войдите в систему с помощью учетной записи Майкрософт и согласиться на запрашиваемую разрешения.</span><span class="sxs-lookup"><span data-stu-id="20620-115">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="20620-116">Страница приложения должна обновиться с отображением маркера.</span><span class="sxs-lookup"><span data-stu-id="20620-116">The app page should refresh, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="20620-117">Получить сведения о пользователе</span><span class="sxs-lookup"><span data-stu-id="20620-117">Get user details</span></span>

<span data-ttu-id="20620-118">В этом разделе вы получите сведения о пользователе из Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="20620-118">In this section you will get the user's details from Microsoft Graph.</span></span>

1. <span data-ttu-id="20620-119">Создайте новый файл в `./src` каталоге, который `GraphService.ts` называется, и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="20620-119">Create a new file in the `./src` directory called `GraphService.ts` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="graphServiceSnippet1":::

    <span data-ttu-id="20620-120">При этом реализуется функция, которая использует microsoft Graph SDK для вызова конечной точки `getUserDetails` `/me` и возврата результата.</span><span class="sxs-lookup"><span data-stu-id="20620-120">This implements the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="20620-121">Откройте `./src/AuthProvider.tsx` и добавьте следующий `import` выписку в верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="20620-121">Open `./src/AuthProvider.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import { getUserDetails } from './GraphService';
    ```

1. <span data-ttu-id="20620-122">Замените имеющуюся функцию `getUserProfile` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="20620-122">Replace the existing `getUserProfile` function with the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/AuthProvider.tsx" id="getUserProfileSnippet" highlight="6-18":::

1. <span data-ttu-id="20620-123">Сохраните изменения и запустите приложение. После регистрации вы должны вернуться на home-страницу, но пользовательский интерфейс должен измениться, чтобы указать, что вы зарегистрировались.</span><span class="sxs-lookup"><span data-stu-id="20620-123">Save your changes and start the app, after sign-in you should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Снимок экрана с домашней страницей после входов](./images/add-aad-auth-01.png)

1. <span data-ttu-id="20620-125">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **"Выйти".**</span><span class="sxs-lookup"><span data-stu-id="20620-125">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="20620-126">При **нажатии** кнопки "Выйти" сеанс сбрасывается и возвращается на домашней странице.</span><span class="sxs-lookup"><span data-stu-id="20620-126">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Снимок экрана с выпадающим меню со ссылкой "Выйти"](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="20620-128">Хранение и обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="20620-128">Storing and refreshing tokens</span></span>

<span data-ttu-id="20620-129">На этом этапе приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="20620-129">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="20620-130">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="20620-130">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="20620-131">Однако этот маркер является кратковременной.</span><span class="sxs-lookup"><span data-stu-id="20620-131">However, this token is short-lived.</span></span> <span data-ttu-id="20620-132">Срок действия маркера истекает через час после его выпуска.</span><span class="sxs-lookup"><span data-stu-id="20620-132">The token expires an hour after it is issued.</span></span> <span data-ttu-id="20620-133">В этом случае маркер обновления становится полезным.</span><span class="sxs-lookup"><span data-stu-id="20620-133">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="20620-134">Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входить.</span><span class="sxs-lookup"><span data-stu-id="20620-134">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="20620-135">Так как приложение использует библиотеку MSAL, вам не нужно реализовывать логику хранения маркеров или обновления.</span><span class="sxs-lookup"><span data-stu-id="20620-135">Because the app is using the MSAL library, you do not have to implement any token storage or refresh logic.</span></span> <span data-ttu-id="20620-136">`PublicClientApplication`Кэшет маркера в сеансе браузера.</span><span class="sxs-lookup"><span data-stu-id="20620-136">The `PublicClientApplication` caches the token in the browser session.</span></span> <span data-ttu-id="20620-137">Метод сначала проверяет кэшный маркер и, если срок его действия еще не истек, возвращает `acquireTokenSilent` его.</span><span class="sxs-lookup"><span data-stu-id="20620-137">The `acquireTokenSilent` method first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="20620-138">Если срок действия истек, для получения нового маркера используется кэшный маркер обновления.</span><span class="sxs-lookup"><span data-stu-id="20620-138">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="20620-139">Этот метод будет больше применяться в следующем модуле.</span><span class="sxs-lookup"><span data-stu-id="20620-139">You'll use this method more in the following module.</span></span>
