<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e173d-101">В этом упражнении вы будете расширяем приложение из предыдущего упражнения для поддержки проверки подлинности с помощью Azure AD.</span><span class="sxs-lookup"><span data-stu-id="e173d-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="e173d-102">Это необходимо для получения необходимого маркера доступа OAuth для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e173d-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="e173d-103">На этом этапе библиотека [библиотеки проверки подлинности (Майкрософт](https://github.com/AzureAD/microsoft-authentication-library-for-js) ) будет интегрирована в приложение.</span><span class="sxs-lookup"><span data-stu-id="e173d-103">In this step you will integrate the [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) library into the application.</span></span>

<span data-ttu-id="e173d-104">Создайте новый файл в `./src` каталоге `Config.js` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="e173d-104">Create a new file in the `./src` directory named `Config.js` and add the following code.</span></span>

```js
module.exports = {
  appId: 'YOUR_APP_ID_HERE',
  redirectUri: 'http://localhost:3000',
  scopes: [
    'user.read',
    'calendars.read'
  ]
};
```

<span data-ttu-id="e173d-105">Замените `YOUR_APP_ID_HERE` идентификатором приложения на портале регистрации приложений.</span><span class="sxs-lookup"><span data-stu-id="e173d-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e173d-106">Если вы используете систему управления версиями (например, Git), то теперь мы бы не могли исключить `Config.js` файл из системы управления версиями, чтобы избежать случайной утечки идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="e173d-106">If you're using source control such as git, now would be a good time to exclude the `Config.js` file from source control to avoid inadvertently leaking your app ID.</span></span>

<span data-ttu-id="e173d-107">Откройте `./src/App.js` и добавьте приведенные `import` ниже операторы в начало файла.</span><span class="sxs-lookup"><span data-stu-id="e173d-107">Open `./src/App.js` and add the following `import` statements to the top of the file.</span></span>

```js
import config from './Config';
import { UserAgentApplication } from 'msal';
```

## <a name="implement-sign-in"></a><span data-ttu-id="e173d-108">Реализация входа</span><span class="sxs-lookup"><span data-stu-id="e173d-108">Implement sign-in</span></span>

<span data-ttu-id="e173d-109">Начните `constructor` с обновления `App` класса для создания экземпляра `UserAgentApplication` класса и вызова `getUser` , чтобы убедиться, что пользователь уже выполнил вход в систему.</span><span class="sxs-lookup"><span data-stu-id="e173d-109">Start by updating the `constructor` for the `App` class to create an instance of the `UserAgentApplication` class and call `getUser` to see if there's already a logged-in user.</span></span> <span data-ttu-id="e173d-110">Замените существующий `constructor` ниже.</span><span class="sxs-lookup"><span data-stu-id="e173d-110">Replace the existing `constructor` with the following.</span></span>

```js
constructor(props) {
  super(props);

  this.userAgentApplication = new UserAgentApplication({
        auth: {
            clientId: config.appId,
            redirectUri: config.redirectUri
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    });

  var user = this.userAgentApplication.getAccount();

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

<span data-ttu-id="e173d-111">Этот код инициализирует `UserAgentApplication` класс с идентификатором приложения и проверяет наличие пользователя.</span><span class="sxs-lookup"><span data-stu-id="e173d-111">This code initializes the `UserAgentApplication` class with your application ID and checks for the presence of a user.</span></span> <span data-ttu-id="e173d-112">При наличии пользователя ему присваивается `isAuthenticated` значение true.</span><span class="sxs-lookup"><span data-stu-id="e173d-112">If there is a user, it sets `isAuthenticated` to true.</span></span> <span data-ttu-id="e173d-113">`getUserProfile` Метод еще не реализован.</span><span class="sxs-lookup"><span data-stu-id="e173d-113">The `getUserProfile` method isn't implemented yet.</span></span> <span data-ttu-id="e173d-114">Вы сделаете это чуть позже.</span><span class="sxs-lookup"><span data-stu-id="e173d-114">You will implement this a bit later.</span></span>

<span data-ttu-id="e173d-115">Затем добавьте функцию в `App` класс, чтобы выполнить вход.</span><span class="sxs-lookup"><span data-stu-id="e173d-115">Next, add a function to the `App` class to do the login.</span></span> <span data-ttu-id="e173d-116">Добавьте к классу `App` следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="e173d-116">Add the following function to the `App` class.</span></span>

```js
async login() {
  try {
    await this.userAgentApplication.loginPopup(
        {
          scopes: config.scopes,
          prompt: "select_account"
      });
    await this.getUserProfile();
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

<span data-ttu-id="e173d-117">Этот метод вызывает `loginPopup` функцию для выполнения входа, а затем вызывает `getUserProfile` функцию.</span><span class="sxs-lookup"><span data-stu-id="e173d-117">This method calls the `loginPopup` function to do the login, then calls the `getUserProfile` function.</span></span>

<span data-ttu-id="e173d-118">Теперь добавьте функцию в Logout.</span><span class="sxs-lookup"><span data-stu-id="e173d-118">Now add a function to logout.</span></span> <span data-ttu-id="e173d-119">Добавьте к классу `App` следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="e173d-119">Add the following function to the `App` class.</span></span>

```js
logout() {
  this.userAgentApplication.logout();
}
```

<span data-ttu-id="e173d-120">Теперь, когда `login` методы `logout` и реализованы, обновите `NavBar` элементы `Welcome` и в `render` методе `App` класса.</span><span class="sxs-lookup"><span data-stu-id="e173d-120">Now that the `login` and `logout` methods are implemented, update the `NavBar` and `Welcome` elements in the `render` method of the `App` class.</span></span> <span data-ttu-id="e173d-121">Замените существующий `NavBar` элемент следующим.</span><span class="sxs-lookup"><span data-stu-id="e173d-121">Replace the existing `NavBar` element with the following.</span></span>

```JSX
<NavBar
  isAuthenticated={this.state.isAuthenticated}
  authButtonMethod={this.state.isAuthenticated ? this.logout.bind(this) : this.login.bind(this)}
  user={this.state.user}/>
```

<span data-ttu-id="e173d-122">Замените существующий `Welcome` элемент следующим.</span><span class="sxs-lookup"><span data-stu-id="e173d-122">Replace the existing `Welcome` element with the following.</span></span>

```JSX
<Welcome {...props}
  isAuthenticated={this.state.isAuthenticated}
  user={this.state.user}
  authButtonMethod={this.login.bind(this)} />
```

<span data-ttu-id="e173d-123">Наконец, реализуйте `getUserProfile` функцию.</span><span class="sxs-lookup"><span data-stu-id="e173d-123">Finally, implement the `getUserProfile` function.</span></span> <span data-ttu-id="e173d-124">Добавьте к классу `App` следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="e173d-124">Add the following function to the `App` class.</span></span>

```js
async getUserProfile() {
  try {
    // Get the access token silently
    // If the cache contains a non-expired token, this function
    // will just return the cached token. Otherwise, it will
    // make a request to the Azure OAuth endpoint to get a token

    var accessToken = await this.userAgentApplication.acquireTokenSilent({
        scopes: config.scopes
      });

    if (accessToken) {
      // TEMPORARY: Display the token in the error flash
      this.setState({
        isAuthenticated: true,
        error: { message: "Access token:", debug: accessToken.accessToken }
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

<span data-ttu-id="e173d-125">Этот код вызывает `acquireTokenSilent` получение маркера доступа, а затем просто выводит маркер в виде ошибки.</span><span class="sxs-lookup"><span data-stu-id="e173d-125">This code calls `acquireTokenSilent` to get an access token, then just outputs the token as an error.</span></span>

<span data-ttu-id="e173d-126">Сохраните изменения и обновите браузер.</span><span class="sxs-lookup"><span data-stu-id="e173d-126">Save your changes and refresh the browser.</span></span> <span data-ttu-id="e173d-127">Нажмите кнопку входа, и вы будете перенаправлены на `https://login.microsoftonline.com`.</span><span class="sxs-lookup"><span data-stu-id="e173d-127">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="e173d-128">Войдите с помощью учетной записи Майкрософт и согласия с запрошенными разрешениями.</span><span class="sxs-lookup"><span data-stu-id="e173d-128">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="e173d-129">Страница приложения должна обновляться, отображая маркер.</span><span class="sxs-lookup"><span data-stu-id="e173d-129">The app page should refresh, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="e173d-130">Получение сведений о пользователе</span><span class="sxs-lookup"><span data-stu-id="e173d-130">Get user details</span></span>

<span data-ttu-id="e173d-131">Начните с создания нового файла, в котором будет храниться весь набор вызовов Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e173d-131">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="e173d-132">Создайте новый файл в `./src` каталоге `GraphService.js` и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="e173d-132">Create a new file in the `./src` directory called `GraphService.js` and add the following code.</span></span>

```js
var graph = require('@microsoft/microsoft-graph-client');

function getAuthenticatedClient(accessToken) {
  // Initialize Graph client
  const client = graph.Client.init({
    // Use the provided access token to authenticate
    // requests
    authProvider: (done) => {
      done(null, accessToken.accessToken);
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

<span data-ttu-id="e173d-133">При этом реализуется `getUserDetails` функция, которая использует пакет SDK Microsoft Graph для вызова `/me` конечной точки и возврата результата.</span><span class="sxs-lookup"><span data-stu-id="e173d-133">This implements the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="e173d-134">Обновите `getUserProfile` метод в `./src/App.js` методе, чтобы вызвать эту функцию.</span><span class="sxs-lookup"><span data-stu-id="e173d-134">Update the `getUserProfile` method in `./src/App.js` to call this function.</span></span> <span data-ttu-id="e173d-135">Сначала добавьте следующий `import` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="e173d-135">First, add the following `import` statement to the top of the file.</span></span>

```js
import { getUserDetails } from './GraphService';
```

<span data-ttu-id="e173d-136">Замените имеющуюся функцию `getUserProfile` указанным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="e173d-136">Replace the existing `getUserProfile` function with the following code.</span></span>

```js
async getUserProfile() {
  try {
    // Get the access token silently
    // If the cache contains a non-expired token, this function
    // will just return the cached token. Otherwise, it will
    // make a request to the Azure OAuth endpoint to get a token

    var accessToken = await this.userAgentApplication.acquireTokenSilent({
      scopes: config.scopes
    });

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

<span data-ttu-id="e173d-137">Теперь, если вы сохраните изменения и запустите приложение, после входа на домашнюю страницу необходимо изменить пользовательский интерфейс, чтобы указать, что вы вошли в систему.</span><span class="sxs-lookup"><span data-stu-id="e173d-137">Now if you save your changes and start the app, after sign-in you should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Снимок экрана домашней страницы после входа](./images/add-aad-auth-01.png)

<span data-ttu-id="e173d-139">Щелкните аватар пользователя в правом верхнем углу, чтобы получить доступ к ссылке **выхода** .</span><span class="sxs-lookup"><span data-stu-id="e173d-139">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="e173d-140">При нажатии кнопки **выйти** сбрасывается сеанс и возвращается на домашнюю страницу.</span><span class="sxs-lookup"><span data-stu-id="e173d-140">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Снимок экрана с раскрывающимся меню со ссылкой "выйти"](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="e173d-142">Хранение и обновление маркеров</span><span class="sxs-lookup"><span data-stu-id="e173d-142">Storing and refreshing tokens</span></span>

<span data-ttu-id="e173d-143">На этом шаге приложение имеет маркер доступа, который отправляется в `Authorization` заголовке вызовов API.</span><span class="sxs-lookup"><span data-stu-id="e173d-143">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="e173d-144">Это маркер, который позволяет приложению получать доступ к Microsoft Graph от имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="e173d-144">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="e173d-145">Однако этот маркер кратковременно используется.</span><span class="sxs-lookup"><span data-stu-id="e173d-145">However, this token is short-lived.</span></span> <span data-ttu-id="e173d-146">Срок действия маркера истечет через час после его выдачи.</span><span class="sxs-lookup"><span data-stu-id="e173d-146">The token expires an hour after it is issued.</span></span> <span data-ttu-id="e173d-147">В этом случае маркер обновления становится полезен.</span><span class="sxs-lookup"><span data-stu-id="e173d-147">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="e173d-148">Маркер обновления позволяет приложению запросить новый маркер доступа, не требуя от пользователя повторного входа.</span><span class="sxs-lookup"><span data-stu-id="e173d-148">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="e173d-149">Так как приложение использует библиотеку MSAL, нет необходимости внедрять логику хранения или обновления маркеров.</span><span class="sxs-lookup"><span data-stu-id="e173d-149">Because the app is using the MSAL library, you do not have to implement any token storage or refresh logic.</span></span> <span data-ttu-id="e173d-150">`UserAgentApplication` Метод кэширует маркер в сеансе браузера.</span><span class="sxs-lookup"><span data-stu-id="e173d-150">The `UserAgentApplication` method caches the token in the browser session.</span></span> <span data-ttu-id="e173d-151">`acquireTokenSilent` Метод сначала проверяет кэшированный маркер, и если срок его действия не истек, он возвращается.</span><span class="sxs-lookup"><span data-stu-id="e173d-151">The `acquireTokenSilent` method first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="e173d-152">Если срок действия истек, он использует кэшированный маркер обновления, чтобы получить новый.</span><span class="sxs-lookup"><span data-stu-id="e173d-152">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="e173d-153">Этот метод более не используется в следующем модуле.</span><span class="sxs-lookup"><span data-stu-id="e173d-153">You'll use this method more in the following module.</span></span>
