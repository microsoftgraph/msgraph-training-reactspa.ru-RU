<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d2bfd-101">В этом упражнении вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="d2bfd-102">В этом приложении для совершения вызовов в Microsoft Graph будет использоваться клиентская библиотека [Microsoft](https://github.com/microsoftgraph/msgraph-sdk-javascript) Graph.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="d2bfd-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="d2bfd-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="d2bfd-104">Для начала добавьте в `./src/GraphService.js` файл новый метод, чтобы получить события из календаря.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-104">Start by adding a new method to the `./src/GraphService.js` file to get the events from the calendar.</span></span> <span data-ttu-id="d2bfd-105">Добавьте следующую функцию:</span><span class="sxs-lookup"><span data-stu-id="d2bfd-105">Add the following function.</span></span>

```js
export async function getEvents(accessToken) {
  const client = getAuthenticatedClient(accessToken);

  const events = await client
    .api('/me/events')
    .select('subject,organizer,start,end')
    .orderby('createdDateTime DESC')
    .get();

  return events;
}
```

<span data-ttu-id="d2bfd-106">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="d2bfd-107">URL-адрес, который будет вызываться — это `/me/events`.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-107">The URL that will be called is `/me/events`.</span></span>
- <span data-ttu-id="d2bfd-108">`select` Метод ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-108">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="d2bfd-109">`orderby` Метод сортирует результаты по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-109">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="d2bfd-110">Теперь создайте реагирующий компонент, чтобы отобразить результаты вызова.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-110">Now create a React component to display the results of the call.</span></span> <span data-ttu-id="d2bfd-111">Создайте новый файл в `./src` каталоге `Calendar.js` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-111">Create a new file in the `./src` directory named `Calendar.js` and add the following code.</span></span>

```JSX
import React from 'react';
import { Table } from 'reactstrap';
import moment from 'moment';
import config from './Config';
import { getEvents } from './GraphService';

// Helper function to format Graph date/time
function formatDateTime(dateTime) {
  return moment.utc(dateTime).local().format('M/D/YY h:mm A');
}

export default class Calendar extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      events: []
    };
  }

  async componentDidMount() {
    try {
      // Get the user's access token
      var accessToken = await window.msal.acquireTokenSilent(config.scopes);
      // Get the user's events
      var events = await getEvents(accessToken);
      // Update the array of events in state
      this.setState({events: events.value});
    }
    catch(err) {
      this.props.showError('ERROR', JSON.stringify(err));
    }
  }

  render() {
    return (
      <pre><code>{JSON.stringify(this.state.events, null, 2)}</code></pre>
    );
  }
}
```

<span data-ttu-id="d2bfd-112">Теперь это просто отрисовывает массив событий в JSON на странице.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-112">For now this just renders the array of events in JSON on the page.</span></span> <span data-ttu-id="d2bfd-113">Добавьте этот новый компонент в приложение.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-113">Add this new component to the app.</span></span> <span data-ttu-id="d2bfd-114">Откройте `./src/App.js` и добавьте приведенный `import` ниже оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-114">Open `./src/App.js` and add the following `import` statement to the top of the file.</span></span>

```js
import Calendar from './Calendar';
```

<span data-ttu-id="d2bfd-115">Затем добавьте следующий компонент сразу после существующего `<Route>`.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-115">Then add the following component just after the existing `<Route>`.</span></span>

```JSX
<Route exact path="/calendar"
  render={(props) =>
    <Calendar {...props}
      showError={this.setErrorMessage.bind(this)} />
  } />
```

<span data-ttu-id="d2bfd-116">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-116">Save your changes and restart the app.</span></span> <span data-ttu-id="d2bfd-117">Войдите и щелкните ссылку **Календарь** на панели навигации.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-117">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="d2bfd-118">Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="d2bfd-119">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="d2bfd-119">Display the results</span></span>

<span data-ttu-id="d2bfd-120">Теперь вы можете обновить `Calendar` компонент, чтобы отображать события более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-120">Now you can update the `Calendar` component to display the events in a more user-friendly manner.</span></span> <span data-ttu-id="d2bfd-121">Замените существующую `render` функцию на `./src/Calendar.js` приведенную ниже функцию.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-121">Replace the existing `render` function in `./src/Calendar.js` with the following function.</span></span>

```JSX
render() {
  return (
    <div>
      <h1>Calendar</h1>
      <Table>
        <thead>
          <tr>
            <th scope="col">Organizer</th>
            <th scope="col">Subject</th>
            <th scope="col">Start</th>
            <th scope="col">End</th>
          </tr>
        </thead>
        <tbody>
          {this.state.events.map(
            function(event){
              return(
                <tr key={event.id}>
                  <td>{event.organizer.emailAddress.name}</td>
                  <td>{event.subject}</td>
                  <td>{formatDateTime(event.start.dateTime)}</td>
                  <td>{formatDateTime(event.end.dateTime)}</td>
                </tr>
              );
            })}
        </tbody>
      </Table>
    </div>
  );
}
```

<span data-ttu-id="d2bfd-122">В результате получается цикл по коллекции событий и добавляется строка таблицы для каждой из них.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-122">This loops through the collection of events and adds a table row for each one.</span></span> <span data-ttu-id="d2bfd-123">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-123">Save the changes and restart the app.</span></span> <span data-ttu-id="d2bfd-124">Щелкните ссылку **Календарь** , после чего приложение должно отобразить таблицу событий.</span><span class="sxs-lookup"><span data-stu-id="d2bfd-124">Click on the **Calendar** link and the app should now render a table of events.</span></span>

![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)