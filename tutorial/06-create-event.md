<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="01dfb-101">В этом разделе мы добавим возможность создания событий в календаре пользователя.</span><span class="sxs-lookup"><span data-stu-id="01dfb-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="add-method-to-graphservice"></a><span data-ttu-id="01dfb-102">Добавить метод в класс graphservice</span><span class="sxs-lookup"><span data-stu-id="01dfb-102">Add method to GraphService</span></span>

1. <span data-ttu-id="01dfb-103">Откройте **./СРК/графсервице.ТС** и добавьте указанную ниже функцию для создания нового события.</span><span class="sxs-lookup"><span data-stu-id="01dfb-103">Open **./src/GraphService.ts** and add the following function to create a new event.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="createEventSnippet":::

## <a name="create-new-event-form"></a><span data-ttu-id="01dfb-104">Форма создания нового события</span><span class="sxs-lookup"><span data-stu-id="01dfb-104">Create new event form</span></span>

1. <span data-ttu-id="01dfb-105">Создайте новый файл в каталоге **./СРК** с именем **невевент. целевого сервера** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="01dfb-105">Create a new file in the **./src** directory named **NewEvent.tsx** and add the following code.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/NewEvent.tsx" id="NewEventSnippet":::

1. <span data-ttu-id="01dfb-106">Откройте **./СРК/АПП.тскс** и добавьте приведенный ниже `import` оператор в начало файла.</span><span class="sxs-lookup"><span data-stu-id="01dfb-106">Open **./src/App.tsx** and add the following `import` statement to the top of the file.</span></span>

    ```typescript
    import NewEvent from './NewEvent';
    ```

1. <span data-ttu-id="01dfb-107">Добавьте новый маршрут в форму создать событие.</span><span class="sxs-lookup"><span data-stu-id="01dfb-107">Add a new route to the new event form.</span></span> <span data-ttu-id="01dfb-108">Добавьте следующий код сразу после других `Route` элементов.</span><span class="sxs-lookup"><span data-stu-id="01dfb-108">Add the following code just after the other `Route` elements.</span></span>

    ```typescript
    <Route exact path="/newevent"
      render={(props) =>
        this.props.isAuthenticated ?
          <NewEvent {...props} /> :
          <Redirect to="/" />
      } />
    ```

    <span data-ttu-id="01dfb-109">Теперь полный `return` оператор должен выглядеть так, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="01dfb-109">The full `return` statement should now look like this.</span></span>

    :::code language="typescript" source="../demo/graph-tutorial/src/App.tsx" id="renderSnippet" highlight="23-28":::

1. <span data-ttu-id="01dfb-110">Обновите приложение и перейдите к представлению календаря.</span><span class="sxs-lookup"><span data-stu-id="01dfb-110">Refresh the app and browse to the calendar view.</span></span> <span data-ttu-id="01dfb-111">Нажмите кнопку **создать событие** .</span><span class="sxs-lookup"><span data-stu-id="01dfb-111">Click the **New event** button.</span></span> <span data-ttu-id="01dfb-112">Заполните поля и нажмите кнопку **создать**.</span><span class="sxs-lookup"><span data-stu-id="01dfb-112">Fill in the fields and click **Create**.</span></span>

    ![Снимок экрана с формой создания события](./images/create-event-01.png)
