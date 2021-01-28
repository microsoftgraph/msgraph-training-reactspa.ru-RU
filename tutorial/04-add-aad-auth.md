<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы расширим приложение из предыдущего упражнения, чтобы поддерживать проверку подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе вы интегрируете библиотеку microsoft [Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) в приложение.

1. Создайте новый файл в `./src` каталоге с именем `Config.ts` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/Config.example.ts":::

    Замените `YOUR_APP_ID_HERE` его на ИД приложения на портале регистрации приложений.

    > [!IMPORTANT]
    > Если вы используете управление исходным кодом, например git, пришло бы время исключить файл из системы управления исходным кодом, чтобы не допустить случайной утечки вашего `Config.ts` ИД приложения.

## <a name="implement-sign-in"></a>Реализация входов

В этом разделе вы создадим поставщика проверки подлинности и внедрим вход и выход.

1. Создайте новый файл в `./src` каталоге с именем `AuthProvider.tsx` и добавьте следующий код.

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

1. Откройте `./src/App.tsx` и добавьте следующий `import` выписку в верхнюю часть файла.

    ```typescript
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';
    ```

1. Замените `class App extends Component<any> {` строку на следующую.

    ```typescript
    class App extends Component<AuthComponentProps> {
    ```

1. Замените `export default App;` строку на следующую.

    ```typescript
    export default withAuthProvider(App);
    ```

1. Сохраните изменения и обновите браузер. Нажмите кнопку "Вход", и вы увидите всплывающее окно, которое `https://login.microsoftonline.com` загружается. Войдите в систему с помощью учетной записи Майкрософт и согласиться на запрашиваемую разрешения. Страница приложения должна обновиться с отображением маркера.

### <a name="get-user-details"></a>Получить сведения о пользователе

В этом разделе вы получите сведения о пользователе из Microsoft Graph.

1. Создайте новый файл в `./src` каталоге, который `GraphService.ts` называется, и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="graphServiceSnippet1":::

    При этом реализуется функция, которая использует microsoft Graph SDK для вызова конечной точки `getUserDetails` `/me` и возврата результата.

1. Откройте `./src/AuthProvider.tsx` и добавьте следующий `import` выписку в верхнюю часть файла.

    ```typescript
    import { getUserDetails } from './GraphService';
    ```

1. Замените имеющуюся функцию `getUserProfile` указанным ниже кодом.

    :::code language="typescript" source="../demo/graph-tutorial/src/AuthProvider.tsx" id="getUserProfileSnippet" highlight="6-18":::

1. Сохраните изменения и запустите приложение. После регистрации вы должны вернуться на home-страницу, но пользовательский интерфейс должен измениться, чтобы указать, что вы зарегистрировались.

    ![Снимок экрана с домашней страницей после входов](./images/add-aad-auth-01.png)

1. Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **"Выйти".** При **нажатии** кнопки "Выйти" сеанс сбрасывается и возвращается на домашней странице.

    ![Снимок экрана с выпадающим меню со ссылкой "Выйти"](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Хранение и обновление маркеров

На этом этапе приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер является кратковременной. Срок действия маркера истекает через час после его выпуска. В этом случае маркер обновления становится полезным. Маркер обновления позволяет приложению запрашивать новый маркер доступа, не требуя от пользователя повторного входить.

Так как приложение использует библиотеку MSAL, вам не нужно реализовывать логику хранения маркеров или обновления. `PublicClientApplication`Кэшет маркера в сеансе браузера. Метод сначала проверяет кэшный маркер и, если срок его действия еще не истек, возвращает `acquireTokenSilent` его. Если срок действия истек, для получения нового маркера используется кэшный маркер обновления. Этот метод будет больше применяться в следующем модуле.
