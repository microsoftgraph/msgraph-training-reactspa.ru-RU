<!-- markdownlint-disable MD002 MD041 -->

В этом разделе мы добавим возможность создания событий в календаре пользователя.

## <a name="add-method-to-graphservice"></a>Добавить метод в класс graphservice

1. Откройте **./СРК/графсервице.ТС** и добавьте указанную ниже функцию для создания нового события.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="createEventSnippet":::

## <a name="create-new-event-form"></a>Форма создания нового события

1. Создайте новый файл в каталоге **./СРК** с именем **невевент. целевого сервера** и добавьте следующий код.

    :::code language="typescript" source="../demo/graph-tutorial/src/NewEvent.tsx" id="NewEventSnippet":::

1. Откройте **./СРК/АПП.тскс** и добавьте приведенный ниже `import` оператор в начало файла.

    ```typescript
    import NewEvent from './NewEvent';
    ```

1. Добавьте новый маршрут в форму создать событие. Добавьте следующий код сразу после других `Route` элементов.

    ```typescript
    <Route exact path="/newevent"
      render={(props) =>
        this.props.isAuthenticated ?
          <NewEvent {...props} /> :
          <Redirect to="/" />
      } />
    ```

    Теперь полный `return` оператор должен выглядеть так, как показано ниже.

    :::code language="typescript" source="../demo/graph-tutorial/src/App.tsx" id="renderSnippet" highlight="23-28":::

1. Обновите приложение и перейдите к представлению календаря. Нажмите кнопку **создать событие** . Заполните поля и нажмите кнопку **создать**.

    ![Снимок экрана с формой создания события](./images/create-event-01.png)
