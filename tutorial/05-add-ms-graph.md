<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e9625-101">В этом упражнении вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="e9625-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="e9625-102">В этом приложении для совершения вызовов в Microsoft Graph будет использоваться [Клиентская библиотека Microsoft](https://github.com/microsoftgraph/msgraph-sdk-javascript) Graph.</span><span class="sxs-lookup"><span data-stu-id="e9625-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="e9625-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="e9625-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="e9625-104">Откройте `./src/GraphService.ts` и добавьте указанную ниже функцию.</span><span class="sxs-lookup"><span data-stu-id="e9625-104">Open `./src/GraphService.ts` and add the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getEventsSnippet":::

    <span data-ttu-id="e9625-105">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="e9625-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="e9625-106">URL-адрес, который будет вызываться — это `/me/events`.</span><span class="sxs-lookup"><span data-stu-id="e9625-106">The URL that will be called is `/me/events`.</span></span>
    - <span data-ttu-id="e9625-107">`select` Метод ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.</span><span class="sxs-lookup"><span data-stu-id="e9625-107">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="e9625-108">`orderby` Метод сортирует результаты по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="e9625-108">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="e9625-109">Создайте компонент реагирования для отображения результатов вызова.</span><span class="sxs-lookup"><span data-stu-id="e9625-109">Create a React component to display the results of the call.</span></span> <span data-ttu-id="e9625-110">Создайте новый файл в `./src` каталоге `Calendar.tsx` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="e9625-110">Create a new file in the `./src` directory named `Calendar.tsx` and add the following code.</span></span>

    ```typescript
    import React from 'react';
    import { Table } from 'reactstrap';
    import moment from 'moment';
    import { Event } from 'microsoft-graph';
    import { config } from './Config';
    import { getEvents } from './GraphService';
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';

    interface CalendarState {
      events: Event[];
    }

    // Helper function to format Graph date/time
    function formatDateTime(dateTime: string | undefined) {
      if (dateTime !== undefined) {
        return moment.utc(dateTime).local().format('M/D/YY h:mm A');
      }
    }

    class Calendar extends React.Component<AuthComponentProps, CalendarState> {
      constructor(props: any) {
        super(props);

        this.state = {
          events: []
        };
      }

      async componentDidMount() {
        try {
          // Get the user's access token
          var accessToken = await this.props.getAccessToken(config.scopes);
          // Get the user's events
          var events = await getEvents(accessToken);
          // Update the array of events in state
          this.setState({events: events.value});
        }
        catch(err) {
          this.props.setError('ERROR', JSON.stringify(err));
        }
      }

      render() {
        return (
          <pre><code>{JSON.stringify(this.state.events, null, 2)}</code></pre>
        );
      }
    }

    export default withAuthProvider(Calendar);
    ```

    <span data-ttu-id="e9625-111">Теперь это просто отрисовывает массив событий в JSON на странице.</span><span class="sxs-lookup"><span data-stu-id="e9625-111">For now this just renders the array of events in JSON on the page.</span></span>

1. <span data-ttu-id="e9625-112">Добавьте этот новый компонент в приложение.</span><span class="sxs-lookup"><span data-stu-id="e9625-112">Add this new component to the app.</span></span> <span data-ttu-id="e9625-113">Откройте `./src/App.tsx` и добавьте приведенный `import` ниже оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="e9625-113">Open `./src/App.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import Calendar from './Calendar';
    ```

1. <span data-ttu-id="e9625-114">Добавьте следующий компонент сразу после существующего `<Route>`.</span><span class="sxs-lookup"><span data-stu-id="e9625-114">Add the following component just after the existing `<Route>`.</span></span>

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. <span data-ttu-id="e9625-115">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="e9625-115">Save your changes and restart the app.</span></span> <span data-ttu-id="e9625-116">Войдите и щелкните ссылку **Календарь** на панели навигации.</span><span class="sxs-lookup"><span data-stu-id="e9625-116">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="e9625-117">Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="e9625-117">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="e9625-118">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="e9625-118">Display the results</span></span>

<span data-ttu-id="e9625-119">Теперь вы можете обновить `Calendar` компонент, чтобы отображать события более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="e9625-119">Now you can update the `Calendar` component to display the events in a more user-friendly manner.</span></span>

1. <span data-ttu-id="e9625-120">Замените существующую `render` функцию на `./src/Calendar.js` приведенную ниже функцию.</span><span class="sxs-lookup"><span data-stu-id="e9625-120">Replace the existing `render` function in `./src/Calendar.js` with the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    <span data-ttu-id="e9625-121">В результате получается цикл по коллекции событий и добавляется строка таблицы для каждой из них.</span><span class="sxs-lookup"><span data-stu-id="e9625-121">This loops through the collection of events and adds a table row for each one.</span></span>

1. <span data-ttu-id="e9625-122">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="e9625-122">Save the changes and restart the app.</span></span> <span data-ttu-id="e9625-123">Щелкните ссылку **Календарь** , после чего приложение должно отобразить таблицу событий.</span><span class="sxs-lookup"><span data-stu-id="e9625-123">Click on the **Calendar** link and the app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
