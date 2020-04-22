<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы добавите Microsoft Graph в приложение. В этом приложении для совершения вызовов в Microsoft Graph будет использоваться [Клиентская библиотека Microsoft](https://github.com/microsoftgraph/msgraph-sdk-javascript) Graph.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

1. Откройте `./src/GraphService.ts` и добавьте указанную ниже функцию.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getEventsSnippet":::

    Рассмотрите, что делает этот код.

    - URL-адрес, который будет вызываться — это `/me/events`.
    - `select` Метод ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.
    - `orderby` Метод сортирует результаты по дате и времени создания, начиная с самого последнего элемента.

1. Создайте компонент реагирования для отображения результатов вызова. Создайте новый файл в `./src` каталоге `Calendar.tsx` и добавьте указанный ниже код.

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

    Теперь это просто отрисовывает массив событий в JSON на странице.

1. Добавьте этот новый компонент в приложение. Откройте `./src/App.tsx` и добавьте приведенный `import` ниже оператор в начало файла.

    ```typescript
    import Calendar from './Calendar';
    ```

1. Добавьте следующий компонент сразу после существующего `<Route>`.

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

1. Замените существующую `render` функцию на `./src/Calendar.js` приведенную ниже функцию.

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    В результате получается цикл по коллекции событий и добавляется строка таблицы для каждой из них.

1. Сохраните изменения и перезапустите приложение. Щелкните ссылку **Календарь** , после чего приложение должно отобразить таблицу событий.

    ![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
