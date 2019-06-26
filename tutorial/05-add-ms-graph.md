<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы добавите Microsoft Graph в приложение. В этом приложении для совершения вызовов в Microsoft Graph будет использоваться клиентская библиотека [Microsoft](https://github.com/microsoftgraph/msgraph-sdk-javascript) Graph.

## <a name="get-calendar-events-from-outlook"></a>Получение событий календаря из Outlook

Для начала добавьте в `./src/GraphService.js` файл новый метод, чтобы получить события из календаря. Добавьте следующую функцию:

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

Рассмотрите, что делает этот код.

- URL-адрес, который будет вызываться — это `/me/events`.
- `select` Метод ограничит поля, возвращаемые для каждого события, только теми, которые будут реально использоваться в представлении.
- `orderby` Метод сортирует результаты по дате и времени создания, начиная с самого последнего элемента.

Теперь создайте реагирующий компонент, чтобы отобразить результаты вызова. Создайте новый файл в `./src` каталоге `Calendar.js` и добавьте указанный ниже код.

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
      var accessToken = await window.msal.acquireTokenSilent({
        scopes: config.scopes
      });
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

Теперь это просто отрисовывает массив событий в JSON на странице. Добавьте этот новый компонент в приложение. Откройте `./src/App.js` и добавьте приведенный `import` ниже оператор в начало файла.

```js
import Calendar from './Calendar';
```

Затем добавьте следующий компонент сразу после существующего `<Route>`.

```JSX
<Route exact path="/calendar"
  render={(props) =>
    <Calendar {...props}
      showError={this.setErrorMessage.bind(this)} />
  } />
```

Сохраните изменения и перезапустите приложение. Войдите и щелкните ссылку **Календарь** на панели навигации. Если все работает, вы должны увидеть дамп событий JSON в календаре пользователя.

## <a name="display-the-results"></a>Отображение результатов

Теперь вы можете обновить `Calendar` компонент, чтобы отображать события более удобным для пользователя способом. Замените существующую `render` функцию на `./src/Calendar.js` приведенную ниже функцию.

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

В результате получается цикл по коллекции событий и добавляется строка таблицы для каждой из них. Сохраните изменения и перезапустите приложение. Щелкните ссылку **Календарь** , после чего приложение должно отобразить таблицу событий.

![Снимок экрана с таблицей событий](./images/add-msgraph-01.png)
