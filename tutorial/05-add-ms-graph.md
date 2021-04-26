<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы будете включать Graph Microsoft в приложение. Для этого приложения вы будете использовать [библиотеку microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) для звонков в Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

1. Откройте `./src/GraphService.ts` и добавьте следующую функцию.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    Давайте посмотрим, что делает этот код.

    - Вызывается URL-адрес `/me/calendarview`.
    - Метод `header` добавляет загон в запрос, в результате чего время отклика будет в предпочтительном часовом поясе `Prefer: outlook.timezone=""` пользователя.
    - Метод добавляет параметры и добавляет `query` окно времени для представления `startDateTime` `endDateTime` календаря.
    - Метод ограничивает поля, возвращенные для каждого события, только теми, которые будут `select` фактически использовать представление.
    - Метод сортировать результаты по дате и времени их создания, причем последний элемент `orderby` является первым.
    - Метод `top` ограничивает результаты на одной странице 25 событиями.
    - Если в ответе содержится значение, указывающее на то, что доступны дополнительные результаты, объект используется для страницы в коллекции, чтобы получить `@odata.nextLink` `PageIterator` все результаты. [](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript)

1. Создайте React для отображения результатов вызова. Создайте новый файл в `./src` каталоге с именем `Calendar.tsx` и добавьте следующий код.

    ```typescript
    import React from 'react';
    import { NavLink as RouterNavLink } from 'react-router-dom';
    import { Table } from 'reactstrap';
    import moment, { Moment } from 'moment-timezone';
    import { findIana } from "windows-iana";
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
            var ianaTimeZones = findIana(this.props.user.timeZone);

            // Get midnight on the start of the current week in the user's timezone,
            // but in UTC. For example, for Pacific Standard Time, the time value would be
            // 07:00:00Z
            var startOfWeek = moment.tz(ianaTimeZones![0].valueOf()).startOf('week').utc();

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

    Пока это только отрисовка массива событий в JSON на странице.

1. Добавьте этот новый компонент в приложение. Откройте `./src/App.tsx` и добавьте следующее утверждение в `import` верхней части файла.

    ```typescript
    import Calendar from './Calendar';
    ```

1. Добавьте следующий компонент сразу после существующего `<Route>` .

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. Сохраните изменения и перезапустите приложение. Войдите и щелкните **ссылку Календарь** в панели nav. Если все работает надлежащим образом, в календаре пользователя должен появиться дамп событий в формате JSON.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете обновить компонент, чтобы отображать события более удобным `Calendar` для пользователя образом.

1. Создайте новый файл в `./src` каталоге с именем `Calendar.css` и добавьте следующий код.

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. Создайте компонент React для отображения событий в один день в качестве строк таблицы. Создайте новый файл в `./src` каталоге с именем `CalendarDayRow.tsx` и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. Добавьте следующие `import` утверждения в верхнюю часть **Calendar.tsx.**

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. Замените `render` существующую `./src/Calendar.tsx` функцию следующей функцией.

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    Это разделяет события на соответствующие дни и отрисовывка раздела таблицы для каждого дня.

1. Сохраните изменения и перезапустите приложение. Щелкните **ссылку Календарь,** и теперь приложение должно отрисовки таблицы событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
