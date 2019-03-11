<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD. Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph. На этом этапе библиотека [библиотеки проверки](https://github.com/AzureAD/microsoft-authentication-library-for-js) подлинности (Майкрософт) будет интегрирована в приложение.

Создайте новый файл в `./src` каталоге `Config.js` и добавьте указанный ниже код.

```js
module.exports = {
  appId: 'YOUR_APP_ID_HERE',
  scopes: [
    "user.read",
    "calendars.read"
  ]
};
```

Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений.

> [!IMPORTANT]
> Если вы используете систему управления версиями (например, Git), то теперь мы бы не могли исключить `Config.js` файл из системы управления версиями, чтобы избежать случайной утечки идентификатора приложения.

Откройте `./src/App.js` и добавьте приведенные `import` ниже операторы в начало файла.

```js
import config from './Config';
import { UserAgentApplication } from 'msal';
```

## <a name="implement-sign-in"></a>Реализация входа

Начните `constructor` с обновления `App` класса для создания экземпляра `UserAgentApplication` класса и вызова `getUser` , чтобы убедиться, что пользователь уже выполнил вход в систему. Замените существующий `constructor` ниже.

```js
constructor(props) {
  super(props);

  this.userAgentApplication = new UserAgentApplication(config.appId, null, null);

  var user = this.userAgentApplication.getUser();

  this.state = {
    isAuthenticated: (user !== null),
    user: {},
    error: null
  };

  if (user) {
    // Enhance user object with data from Graph
    this.getUserProfile();
  }
}
```

Этот код инициализирует `UserAgentApplication` класс с идентификатором приложения и проверяет наличие пользователя. При наличии пользователя ему присваивается `isAuthenticated` значение true. `getUserProfile` Метод еще не реализован. Вы сделаете это чуть позже.

Затем добавьте функцию в `App` класс, чтобы выполнить вход. Добавьте к классу `App` следующую функцию:

```js
async login() {
  try {
    await this.userAgentApplication.loginPopup(config.scopes);
    await this.getUserProfile();
  }
  catch(err) {
    var errParts = err.split('|');
    this.setState({
      isAuthenticated: false,
      user: {},
      error: { message: errParts[1], debug: errParts[0] }
    });
  }
}
```

Этот метод вызывает `loginPopup` функцию для выполнения входа, а затем вызывает `getUserProfile` функцию.

Теперь добавьте функцию в Logout. Добавьте к классу `App` следующую функцию:

```js
logout() {
  this.userAgentApplication.logout();
}
```

Теперь, когда `login` методы `logout` и реализованы, обновите `NavBar` элементы `Welcome` и в `render` методе `App` класса. Замените существующий `NavBar` элемент следующим.

```JSX
<NavBar
  isAuthenticated={this.state.isAuthenticated}
  authButtonMethod={this.state.isAuthenticated ? this.logout.bind(this) : this.login.bind(this)}
  user={this.state.user}/>
```

Замените существующий `Welcome` элемент следующим.

```JSX
<Welcome {...props}
  isAuthenticated={this.state.isAuthenticated}
  user={this.state.user}
  authButtonMethod={this.login.bind(this)} />
```

Наконец, реализуйте `getUserProfile` функцию. Добавьте к классу `App` следующую функцию:

```js
async getUserProfile() {
  try {
    // Get the access token silently
    // If the cache contains a non-expired token, this function
    // will just return the cached token. Otherwise, it will
    // make a request to the Azure OAuth endpoint to get a token

    var accessToken = await this.userAgentApplication.acquireTokenSilent(config.scopes);

    if (accessToken) {
      // TEMPORARY: Display the token in the error flash
      this.setState({
        isAuthenticated: true,
        error: { message: "Access token:", debug: accessToken }
      });
    }
  }
  catch(err) {
    var errParts = err.split('|');
    this.setState({
      isAuthenticated: false,
      user: {},
      error: { message: errParts[1], debug: errParts[0] }
    });
  }
}
```

Этот код вызывает `acquireTokenSilent` получение маркера доступа, а затем просто выводит маркер в виде ошибки.

Сохраните изменения и обновите браузер. Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`. Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями. Страница приложения должна обновляться, отображая маркер.

### <a name="get-user-details"></a>Получение сведений о пользователе

Начните с создания нового файла, в котором будет храниться весь набор вызовов Microsoft Graph. Создайте новый файл в `./src` каталоге `GraphService.js` и добавьте приведенный ниже код.

```js
var graph = require('@microsoft/microsoft-graph-client');

function getAuthenticatedClient(accessToken) {
  // Initialize Graph client
  const client = graph.Client.init({
    // Use the provided access token to authenticate
    // requests
    authProvider: (done) => {
      done(null, accessToken);
    }
  });

  return client;
}

export async function getUserDetails(accessToken) {
  const client = getAuthenticatedClient(accessToken);

  const user = await client.api('/me').get();
  return user;
}
```

При этом реализуется `getUserDetails` функция, которая использует пакет SDK Microsoft Graph для вызова `/me` конечной точки и возврата результата.

Обновите `getUserProfile` метод в `./src/App.js` методе, чтобы вызвать эту функцию. Сначала добавьте следующий `import` оператор в начало файла.

```js
import { getUserDetails } from './GraphService';
```

Замените имеющуюся функцию `getUserProfile` указанным ниже кодом.

```js
async getUserProfile() {
  try {
    // Get the access token silently
    // If the cache contains a non-expired token, this function
    // will just return the cached token. Otherwise, it will
    // make a request to the Azure OAuth endpoint to get a token

    var accessToken = await this.userAgentApplication.acquireTokenSilent(config.scopes);

    if (accessToken) {
      // Get the user's profile from Graph
      var user = await getUserDetails(accessToken);
      this.setState({
        isAuthenticated: true,
        user: {
          displayName: user.displayName,
          email: user.mail || user.userPrincipalName
        },
        error: null
      });
    }
  }
  catch(err) {
    var error = {};
    if (typeof(err) === 'string') {
      var errParts = err.split('|');
      error = errParts.length > 1 ?
        { message: errParts[1], debug: errParts[0] } :
        { message: err };
    } else {
      error = {
        message: err.message,
        debug: JSON.stringify(err)
      };
    }

    this.setState({
      isAuthenticated: false,
      user: {},
      error: error
    });
  }
}
```

Теперь, если вы сохраните изменения и запустите приложение, после входа на домашнюю страницу необходимо изменить пользовательский интерфейс, чтобы указать, что вы вошли в систему.

![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** . При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.

![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Хранение и обновление маркеров

На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API. Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.

Однако этот маркер кратковременно используется. Срок действия маркера истечет через час после его выдачи. В этом случае маркер обновления становится полезен. Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.

Так как приложение использует библиотеку MSAL, нет необходимости внедрять логику хранения или обновления маркеров. `UserAgentApplication` Метод кэширует маркер в сеансе браузера. `acquireTokenSilent` Метод сначала проверяет кэшированный маркер, и если срок его действия не истек, он возвращается. Если срок действия истек, он использует кэшированный маркер обновления, чтобы получить новый. Этот метод более не используется в следующем модуле.