<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c9a72-101">В этом упражнении вы добавите Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="c9a72-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="c9a72-102">В этом приложении для совершения вызовов в Microsoft Graph будет использоваться [Клиентская библиотека Microsoft](https://github.com/microsoftgraph/msgraph-sdk-javascript) Graph.</span><span class="sxs-lookup"><span data-stu-id="c9a72-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="c9a72-103">Получение событий календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="c9a72-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="c9a72-104">Откройте `./src/GraphService.ts` и добавьте указанную ниже функцию.</span><span class="sxs-lookup"><span data-stu-id="c9a72-104">Open `./src/GraphService.ts` and add the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    <span data-ttu-id="c9a72-105">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="c9a72-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="c9a72-106">URL-адрес, который будет вызываться — это `/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="c9a72-106">The URL that will be called is `/me/calendarview`.</span></span>
    - <span data-ttu-id="c9a72-107">`header`Метод добавляет `Prefer: outlook.timezone=""` заголовок в запрос, что приводит к тому, что время в отклике будет присутствовать в предпочтительном часовом поясе пользователя.</span><span class="sxs-lookup"><span data-stu-id="c9a72-107">The `header` method adds the `Prefer: outlook.timezone=""` header to the request, causing the times in the response to be in the user's preferred time zone.</span></span>
    - <span data-ttu-id="c9a72-108">`query`Метод добавляет `startDateTime` `endDateTime` Параметры и определяет окно представления календаря.</span><span class="sxs-lookup"><span data-stu-id="c9a72-108">The `query` method adds the `startDateTime` and `endDateTime` parameters, defining the window of time for the calendar view.</span></span>
    - <span data-ttu-id="c9a72-109">`select`Метод ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.</span><span class="sxs-lookup"><span data-stu-id="c9a72-109">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="c9a72-110">`orderby`Метод сортирует результаты по дате и времени создания, начиная с самого последнего элемента.</span><span class="sxs-lookup"><span data-stu-id="c9a72-110">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>
    - <span data-ttu-id="c9a72-111">`top`Метод ограничит результаты первыми событиями 50.</span><span class="sxs-lookup"><span data-stu-id="c9a72-111">The `top` method limits the results to the first 50 events.</span></span>
    - <span data-ttu-id="c9a72-112">Если ответ содержит `@odata.nextLink` значение, указывающее, что доступны дополнительные результаты, `PageIterator` объект используется для получения всех результатов с [помощью объекта pageing в коллекции](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript) .</span><span class="sxs-lookup"><span data-stu-id="c9a72-112">If the response contains an `@odata.nextLink` value, indicating there are more results available, a `PageIterator` object is used to [page through the collection](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript) to get all of the results.</span></span>

1. <span data-ttu-id="c9a72-113">Создайте компонент реагирования для отображения результатов вызова.</span><span class="sxs-lookup"><span data-stu-id="c9a72-113">Create a React component to display the results of the call.</span></span> <span data-ttu-id="c9a72-114">Создайте новый файл в `./src` каталоге `Calendar.tsx` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="c9a72-114">Create a new file in the `./src` directory named `Calendar.tsx` and add the following code.</span></span>

    ```typescript
    import React from 'react';
    import { NavLink as RouterNavLink } from 'react-router-dom';
    import { Table } from 'reactstrap';
    import moment, { Moment } from 'moment-timezone';
    import { findOneIana } from "windows-iana";
    import { Event } from 'microsoft-graph';
    import { config } from './Config';
    import { getUserWeekCalendar } from './GraphService';
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';

    interface CalendarState {
      eventsLoaded: boolean;
      events: Event[];
      startOfWeek: Moment | undefined;
    }

    class Calendar extends React.Component<AuthComponentProps, CalendarState> {
      constructor(props: any) {
        super(props);

        this.state = {
          eventsLoaded: false,
          events: [],
          startOfWeek: undefined
        };
      }

      async componentDidUpdate() {
        try {
          // Get the user's access token
          var accessToken = await this.props.getAccessToken(config.scopes);
          // Convert user's Windows time zone ("Pacific Standard Time")
          // to IANA format ("America/Los_Angeles")
          // Moment needs IANA format
          var ianaTimeZone = findOneIana(this.props.user.timeZone);

          // Get midnight on the start of the current week in the user's timezone,
          // but in UTC. For example, for Pacific Standard Time, the time value would be
          // 07:00:00Z
          var startOfWeek = moment.tz(ianaTimeZone!.valueOf()).startOf('week').utc();

          // Get the user's events
          var events = await getUserWeekCalendar(accessToken, this.props.user.timeZone, startOfWeek);

          // Update the array of events in state
          this.setState({
            eventsLoaded: true,
            events: events,
            startOfWeek: startOfWeek
          });
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

    <span data-ttu-id="c9a72-115">Теперь это просто отрисовывает массив событий в JSON на странице.</span><span class="sxs-lookup"><span data-stu-id="c9a72-115">For now this just renders the array of events in JSON on the page.</span></span>

1. <span data-ttu-id="c9a72-116">Добавьте этот новый компонент в приложение.</span><span class="sxs-lookup"><span data-stu-id="c9a72-116">Add this new component to the app.</span></span> <span data-ttu-id="c9a72-117">Откройте `./src/App.tsx` и добавьте приведенный ниже `import` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="c9a72-117">Open `./src/App.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import Calendar from './Calendar';
    ```

1. <span data-ttu-id="c9a72-118">Добавьте следующий компонент сразу после существующего `<Route>` .</span><span class="sxs-lookup"><span data-stu-id="c9a72-118">Add the following component just after the existing `<Route>`.</span></span>

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. <span data-ttu-id="c9a72-119">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="c9a72-119">Save your changes and restart the app.</span></span> <span data-ttu-id="c9a72-120">Войдите и щелкните ссылку **Календарь** на панели навигации.</span><span class="sxs-lookup"><span data-stu-id="c9a72-120">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="c9a72-121">Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="c9a72-121">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="c9a72-122">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="c9a72-122">Display the results</span></span>

<span data-ttu-id="c9a72-123">Теперь вы можете обновить `Calendar` компонент, чтобы отображать события более удобным для пользователя способом.</span><span class="sxs-lookup"><span data-stu-id="c9a72-123">Now you can update the `Calendar` component to display the events in a more user-friendly manner.</span></span>

1. <span data-ttu-id="c9a72-124">Создайте новый файл в `./src` каталоге `Calendar.css` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="c9a72-124">Create a new file in the `./src` directory named `Calendar.css` and add the following code.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. <span data-ttu-id="c9a72-125">Создание компонента отклика для отображения событий в один день в виде строк таблицы.</span><span class="sxs-lookup"><span data-stu-id="c9a72-125">Create a React component to render events in a single day as table rows.</span></span> <span data-ttu-id="c9a72-126">Создайте новый файл в `./src` каталоге `CalendarDayRow.tsx` и добавьте указанный ниже код.</span><span class="sxs-lookup"><span data-stu-id="c9a72-126">Create a new file in the `./src` directory named `CalendarDayRow.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. <span data-ttu-id="c9a72-127">Добавьте следующие `import` операторы в верхнюю часть **Calendar. Целевой** .</span><span class="sxs-lookup"><span data-stu-id="c9a72-127">Add the following `import` statements to the top of **Calendar.tsx** .</span></span>

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. <span data-ttu-id="c9a72-128">Замените существующую `render` функцию на `./src/Calendar.tsx` приведенную ниже функцию.</span><span class="sxs-lookup"><span data-stu-id="c9a72-128">Replace the existing `render` function in `./src/Calendar.tsx` with the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    <span data-ttu-id="c9a72-129">При этом события разбиваются на соответствующие дни и отрисовывается раздел таблицы для каждого дня.</span><span class="sxs-lookup"><span data-stu-id="c9a72-129">This splits the events into their respective days and renders a table section for each day.</span></span>

1. <span data-ttu-id="c9a72-130">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="c9a72-130">Save the changes and restart the app.</span></span> <span data-ttu-id="c9a72-131">Щелкните ссылку **Календарь** , после чего приложение должно отобразить таблицу событий.</span><span class="sxs-lookup"><span data-stu-id="c9a72-131">Click on the **Calendar** link and the app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
