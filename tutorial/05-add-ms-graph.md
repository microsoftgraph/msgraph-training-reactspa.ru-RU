<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы включаете Microsoft Graph в приложение. Для этого приложения вы будете использовать клиентскую библиотеку [Microsoft-graph](https://github.com/microsoftgraph/msgraph-sdk-javascript) для вызова Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Получить события календаря из Outlook

1. Откройте `./src/GraphService.ts` и добавьте следующую функцию.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    Подумайте, что делает этот код.

    - Будет вызван URL-адрес `/me/calendarview` .
    - Метод добавляет загон в запрос, в результате чего время в ответе должно быть в предпочитаемом `header` `Prefer: outlook.timezone=""` пользователем часовом поясе.
    - Метод `query` добавляет `startDateTime` параметры `endDateTime` и параметры, определяющие окно времени для представления календаря.
    - Этот `select` метод ограничивает поля, возвращаемые для каждого события, только теми полями, которые будут фактически использовать представление.
    - Метод сортировать результаты по дате и времени их создания, при этом самый последний элемент `orderby` является первым.
    - Этот `top` метод ограничивает результаты в одной странице 25 событиями.
    - Если ответ содержит значение, указывающее на то, что доступно больше результатов, объект используется для страницы коллекции, чтобы `@odata.nextLink` `PageIterator` получить все результаты. [](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript)

1. Создайте компонент React для отображения результатов вызова. Создайте новый файл в `./src` каталоге с именем `Calendar.tsx` и добавьте следующий код.

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

    На данный момент это просто отрисовка массива событий в JSON на странице.

1. Добавьте этот новый компонент в приложение. Откройте `./src/App.tsx` и добавьте следующий отчет в `import` верхнюю часть файла.

    ```typescript
    import Calendar from './Calendar';
    ```

1. Добавьте следующий компонент сразу после `<Route>` существующего.

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. Сохраните изменения и перезапустите приложение. Войдите и щелкните **ссылку "Календарь"** на панели nav. Если все работает, в календаре пользователя должен быть дамп событий JSON.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете обновить `Calendar` компонент, чтобы отобразить события более удобным способом.

1. Создайте новый файл в `./src` каталоге с именем `Calendar.css` и добавьте следующий код.

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. Создайте компонент React для отрисовки событий в один день в качестве строк таблицы. Создайте новый файл в `./src` каталоге с именем `CalendarDayRow.tsx` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. Добавьте следующие `import` утверждения в верхнюю часть **Calendar.tsx.**

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. Замените `render` существующую `./src/Calendar.tsx` функцию на следующую функцию.

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    Это разделяет события на соответствующие дни и отрисовка раздела таблицы для каждого дня.

1. Сохраните изменения и перезапустите приложение. Щелкните **ссылку "Календарь",** и приложение должно отрисовки таблицы событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
