# <a name="getting-started-with-create-react-app"></a><span data-ttu-id="0a709-101">Начало работы с созданием приложения React</span><span class="sxs-lookup"><span data-stu-id="0a709-101">Getting Started with Create React App</span></span>

<span data-ttu-id="0a709-102">Этот проект был загрузился с помощью [приложения Create React App.](https://github.com/facebook/create-react-app)</span><span class="sxs-lookup"><span data-stu-id="0a709-102">This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).</span></span>

## <a name="available-scripts"></a><span data-ttu-id="0a709-103">Доступные сценарии</span><span class="sxs-lookup"><span data-stu-id="0a709-103">Available Scripts</span></span>

<span data-ttu-id="0a709-104">В каталоге проекта можно выполнить:</span><span class="sxs-lookup"><span data-stu-id="0a709-104">In the project directory, you can run:</span></span>

### `yarn start`

<span data-ttu-id="0a709-105">Запускает приложение в режиме разработки.</span><span class="sxs-lookup"><span data-stu-id="0a709-105">Runs the app in the development mode.</span></span>\
<span data-ttu-id="0a709-106">Откройте, [http://localhost:3000](http://localhost:3000) чтобы просмотреть его в браузере.</span><span class="sxs-lookup"><span data-stu-id="0a709-106">Open [http://localhost:3000](http://localhost:3000) to view it in the browser.</span></span>

<span data-ttu-id="0a709-107">Страница будет перезагружена при редактировании.</span><span class="sxs-lookup"><span data-stu-id="0a709-107">The page will reload if you make edits.</span></span>\
<span data-ttu-id="0a709-108">Кроме того, в консоли отразятся все ошибки lint.</span><span class="sxs-lookup"><span data-stu-id="0a709-108">You will also see any lint errors in the console.</span></span>

### `yarn test`

<span data-ttu-id="0a709-109">Запускает запуск запуска тестового запуска в интерактивном режиме просмотра.</span><span class="sxs-lookup"><span data-stu-id="0a709-109">Launches the test runner in the interactive watch mode.</span></span>\
<span data-ttu-id="0a709-110">Дополнительные сведения см. в разделе о [запуске](https://facebook.github.io/create-react-app/docs/running-tests) тестов.</span><span class="sxs-lookup"><span data-stu-id="0a709-110">See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.</span></span>

### `yarn build`

<span data-ttu-id="0a709-111">Создает приложение для производства в `build` папку.</span><span class="sxs-lookup"><span data-stu-id="0a709-111">Builds the app for production to the `build` folder.</span></span>\
<span data-ttu-id="0a709-112">Он правильно поставляет React в производственный режим и оптимизирует сборку для обеспечения оптимальной производительности.</span><span class="sxs-lookup"><span data-stu-id="0a709-112">It correctly bundles React in production mode and optimizes the build for the best performance.</span></span>

<span data-ttu-id="0a709-113">Сборка минифицирована, а имена файлов содержат хеши.</span><span class="sxs-lookup"><span data-stu-id="0a709-113">The build is minified and the filenames include the hashes.</span></span>\
<span data-ttu-id="0a709-114">Ваше приложение готово к развертыванию!</span><span class="sxs-lookup"><span data-stu-id="0a709-114">Your app is ready to be deployed!</span></span>

<span data-ttu-id="0a709-115">Дополнительные сведения [см. в](https://facebook.github.io/create-react-app/docs/deployment) разделе о развертывании.</span><span class="sxs-lookup"><span data-stu-id="0a709-115">See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.</span></span>

### `yarn eject`

<span data-ttu-id="0a709-116">**Примечание. Это операция в одну сторону. После этого `eject` вы не сможете вернуться!**</span><span class="sxs-lookup"><span data-stu-id="0a709-116">**Note: this is a one-way operation. Once you `eject`, you can’t go back!**</span></span>

<span data-ttu-id="0a709-117">Если вы не удовлетворены выбором средства сборки и конфигурации, вы можете в `eject` любое время.</span><span class="sxs-lookup"><span data-stu-id="0a709-117">If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time.</span></span> <span data-ttu-id="0a709-118">Эта команда удалит одну зависимость сборки из проекта.</span><span class="sxs-lookup"><span data-stu-id="0a709-118">This command will remove the single build dependency from your project.</span></span>

<span data-ttu-id="0a709-119">Вместо этого он скопирует все файлы конфигурации и транзитивные зависимости (webpack, EsLint, ESLint и т. д.) прямо в проект, чтобы получить полный контроль над ними.</span><span class="sxs-lookup"><span data-stu-id="0a709-119">Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them.</span></span> <span data-ttu-id="0a709-120">Все команды, кроме команд, по-прежнему будут работать, но они будут указать на скопированные сценарии, чтобы `eject` их можно было оптимизировать.</span><span class="sxs-lookup"><span data-stu-id="0a709-120">All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them.</span></span> <span data-ttu-id="0a709-121">На этом этапе вы сами по себе.</span><span class="sxs-lookup"><span data-stu-id="0a709-121">At this point you’re on your own.</span></span>

<span data-ttu-id="0a709-122">Вам не нужно никогда использовать `eject` .</span><span class="sxs-lookup"><span data-stu-id="0a709-122">You don’t have to ever use `eject`.</span></span> <span data-ttu-id="0a709-123">Курсированный набор функций подходит для небольших и средних развертывание, и вы не должны быть обязаны использовать эту функцию.</span><span class="sxs-lookup"><span data-stu-id="0a709-123">The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature.</span></span> <span data-ttu-id="0a709-124">Однако мы понимаем, что это средство не будет полезно, если вы не сможете настроить его, когда будете готовы к этому.</span><span class="sxs-lookup"><span data-stu-id="0a709-124">However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.</span></span>

## <a name="learn-more"></a><span data-ttu-id="0a709-125">Подробнее</span><span class="sxs-lookup"><span data-stu-id="0a709-125">Learn More</span></span>

<span data-ttu-id="0a709-126">Дополнительные узнать можно в документации [по созданию приложения React.](https://facebook.github.io/create-react-app/docs/getting-started)</span><span class="sxs-lookup"><span data-stu-id="0a709-126">You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).</span></span>

<span data-ttu-id="0a709-127">Чтобы узнать о React, ознакомьтесь с [документацией по React.](https://reactjs.org/)</span><span class="sxs-lookup"><span data-stu-id="0a709-127">To learn React, check out the [React documentation](https://reactjs.org/).</span></span>
