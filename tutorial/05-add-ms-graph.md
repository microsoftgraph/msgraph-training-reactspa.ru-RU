<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="730be-101">В этом упражнении вы включаете Microsoft Graph в приложение.</span><span class="sxs-lookup"><span data-stu-id="730be-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="730be-102">Для этого приложения вы будете использовать клиентскую библиотеку [Microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-javascript) для вызова Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="730be-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="730be-103">Получить события календаря из Outlook</span><span class="sxs-lookup"><span data-stu-id="730be-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="730be-104">Откройте `./src/GraphService.ts` и добавьте следующую функцию.</span><span class="sxs-lookup"><span data-stu-id="730be-104">Open `./src/GraphService.ts` and add the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    <span data-ttu-id="730be-105">Подумайте, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="730be-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="730be-106">Будет вызван URL-адрес `/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="730be-106">The URL that will be called is `/me/calendarview`.</span></span>
    - <span data-ttu-id="730be-107">Метод добавляет загон в запрос, в результате чего время в ответе должно быть в предпочитаемом `header` `Prefer: outlook.timezone=""` пользователем часовом поясе.</span><span class="sxs-lookup"><span data-stu-id="730be-107">The `header` method adds the `Prefer: outlook.timezone=""` header to the request, causing the times in the response to be in the user's preferred time zone.</span></span>
    - <span data-ttu-id="730be-108">Метод `query` добавляет `startDateTime` параметры `endDateTime` и параметры, определяющие окно времени для представления календаря.</span><span class="sxs-lookup"><span data-stu-id="730be-108">The `query` method adds the `startDateTime` and `endDateTime` parameters, defining the window of time for the calendar view.</span></span>
    - <span data-ttu-id="730be-109">Этот `select` метод ограничивает поля, возвращаемые для каждого события, только теми полями, которые будут фактически использовать представление.</span><span class="sxs-lookup"><span data-stu-id="730be-109">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="730be-110">Метод сортировать результаты по дате и времени их создания, при этом самый последний элемент `orderby` является первым.</span><span class="sxs-lookup"><span data-stu-id="730be-110">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>
    - <span data-ttu-id="730be-111">Этот `top` метод ограничивает результаты в одной странице 25 событиями.</span><span class="sxs-lookup"><span data-stu-id="730be-111">The `top` method limits the results in a single page to 25 events.</span></span>
    - <span data-ttu-id="730be-112">Если ответ содержит значение, указывающее на то, что доступно больше результатов, объект используется для страницы коллекции, чтобы `@odata.nextLink` `PageIterator` получить все результаты. [](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript)</span><span class="sxs-lookup"><span data-stu-id="730be-112">If the response contains an `@odata.nextLink` value, indicating there are more results available, a `PageIterator` object is used to [page through the collection](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript) to get all of the results.</span></span>

1. <span data-ttu-id="730be-113">Создайте компонент React для отображения результатов вызова.</span><span class="sxs-lookup"><span data-stu-id="730be-113">Create a React component to display the results of the call.</span></span> <span data-ttu-id="730be-114">Создайте новый файл в `./src` каталоге с именем `Calendar.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="730be-114">Create a new file in the `./src` directory named `Calendar.tsx` and add the following code.</span></span>

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
        if (this.props.user && !this.state.eventsLoaded)
        {
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
          catch (err) {
            this.props.setError('ERROR', JSON.stringify(err));
          }
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

    <span data-ttu-id="730be-115">На данный момент это просто отрисовка массива событий в JSON на странице.</span><span class="sxs-lookup"><span data-stu-id="730be-115">For now this just renders the array of events in JSON on the page.</span></span>

1. <span data-ttu-id="730be-116">Добавьте этот новый компонент в приложение.</span><span class="sxs-lookup"><span data-stu-id="730be-116">Add this new component to the app.</span></span> <span data-ttu-id="730be-117">Откройте `./src/App.tsx` и добавьте следующий отчет в `import` верхнюю часть файла.</span><span class="sxs-lookup"><span data-stu-id="730be-117">Open `./src/App.tsx` and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import Calendar from './Calendar';
    ```

1. <span data-ttu-id="730be-118">Добавьте следующий компонент сразу после `<Route>` существующего.</span><span class="sxs-lookup"><span data-stu-id="730be-118">Add the following component just after the existing `<Route>`.</span></span>

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. <span data-ttu-id="730be-119">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="730be-119">Save your changes and restart the app.</span></span> <span data-ttu-id="730be-120">Войдите и щелкните **ссылку "Календарь"** на панели nav.</span><span class="sxs-lookup"><span data-stu-id="730be-120">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="730be-121">Если все работает, в календаре пользователя должен быть дамп событий JSON.</span><span class="sxs-lookup"><span data-stu-id="730be-121">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="730be-122">Отображение результатов</span><span class="sxs-lookup"><span data-stu-id="730be-122">Display the results</span></span>

<span data-ttu-id="730be-123">Теперь вы можете обновить `Calendar` компонент, чтобы отобразить события более удобным способом.</span><span class="sxs-lookup"><span data-stu-id="730be-123">Now you can update the `Calendar` component to display the events in a more user-friendly manner.</span></span>

1. <span data-ttu-id="730be-124">Создайте новый файл в `./src` каталоге с именем `Calendar.css` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="730be-124">Create a new file in the `./src` directory named `Calendar.css` and add the following code.</span></span>

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. <span data-ttu-id="730be-125">Создайте компонент React для отрисовки событий в один день в качестве строк таблицы.</span><span class="sxs-lookup"><span data-stu-id="730be-125">Create a React component to render events in a single day as table rows.</span></span> <span data-ttu-id="730be-126">Создайте новый файл в `./src` каталоге с именем `CalendarDayRow.tsx` и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="730be-126">Create a new file in the `./src` directory named `CalendarDayRow.tsx` and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. <span data-ttu-id="730be-127">Добавьте следующие `import` утверждения в верхнюю часть **Calendar.tsx.**</span><span class="sxs-lookup"><span data-stu-id="730be-127">Add the following `import` statements to the top of **Calendar.tsx**.</span></span>

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. <span data-ttu-id="730be-128">Замените `render` существующую `./src/Calendar.tsx` функцию на следующую функцию.</span><span class="sxs-lookup"><span data-stu-id="730be-128">Replace the existing `render` function in `./src/Calendar.tsx` with the following function.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    <span data-ttu-id="730be-129">Это разделяет события на соответствующие дни и отрисовка раздела таблицы для каждого дня.</span><span class="sxs-lookup"><span data-stu-id="730be-129">This splits the events into their respective days and renders a table section for each day.</span></span>

1. <span data-ttu-id="730be-130">Сохраните изменения и перезапустите приложение.</span><span class="sxs-lookup"><span data-stu-id="730be-130">Save the changes and restart the app.</span></span> <span data-ttu-id="730be-131">Щелкните **ссылку "Календарь",** и приложение должно отрисовки таблицы событий.</span><span class="sxs-lookup"><span data-stu-id="730be-131">Click on the **Calendar** link and the app should now render a table of events.</span></span>

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
