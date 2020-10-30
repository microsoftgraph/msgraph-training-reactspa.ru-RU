<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы добавите Microsoft Graph в приложение. В этом приложении для совершения вызовов в Microsoft Graph будет использоваться [Клиентская библиотека Microsoft](https://github.com/microsoftgraph/msgraph-sdk-javascript) Graph.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

1. Откройте `./src/GraphService.ts` и добавьте указанную ниже функцию.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    Рассмотрите, что делает этот код.

    - URL-адрес, который будет вызываться — это `/me/calendarview` .
    - `header`Метод добавляет `Prefer: outlook.timezone=""` заголовок в запрос, что приводит к тому, что время в отклике будет присутствовать в предпочтительном часовом поясе пользователя.
    - `query`Метод добавляет `startDateTime` `endDateTime` Параметры и определяет окно представления календаря.
    - `select`Метод ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.
    - `orderby`Метод сортирует результаты по дате и времени создания, начиная с самого последнего элемента.
    - `top`Метод ограничит результаты первыми событиями 50.
    - Если ответ содержит `@odata.nextLink` значение, указывающее, что доступны дополнительные результаты, `PageIterator` объект используется для получения всех результатов с [помощью объекта pageing в коллекции](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript) .

1. Создайте компонент реагирования для отображения результатов вызова. Создайте новый файл в `./src` каталоге `Calendar.tsx` и добавьте указанный ниже код.

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

    Теперь это просто отрисовывает массив событий в JSON на странице.

1. Добавьте этот новый компонент в приложение. Откройте `./src/App.tsx` и добавьте приведенный ниже `import` оператор в начало файла.

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

1. Сохраните изменения и перезапустите приложение. Войдите и щелкните ссылку **Календарь** на панели навигации. Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете обновить `Calendar` компонент, чтобы отображать события более удобным для пользователя способом.

1. Создайте новый файл в `./src` каталоге `Calendar.css` и добавьте указанный ниже код.

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. Создание компонента отклика для отображения событий в один день в виде строк таблицы. Создайте новый файл в `./src` каталоге `CalendarDayRow.tsx` и добавьте указанный ниже код.

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. Добавьте следующие `import` операторы в верхнюю часть **Calendar. Целевой** .

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. Замените существующую `render` функцию на `./src/Calendar.tsx` приведенную ниже функцию.

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    При этом события разбиваются на соответствующие дни и отрисовывается раздел таблицы для каждого дня.

1. Сохраните изменения и перезапустите приложение. Щелкните ссылку **Календарь** , после чего приложение должно отобразить таблицу событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
