<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e8506-101">В этом руководстве рассказывается, как создать приложение, поддерживающее одностраничное приложение, которое использует API Microsoft Graph для получения сведений о календаре для пользователя.</span><span class="sxs-lookup"><span data-stu-id="e8506-101">This tutorial teaches you how to build a React single-page app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="e8506-102">Если вы предпочитаете просто скачать заполненный учебник, вы можете скачать или клонировать [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-reactspa).</span><span class="sxs-lookup"><span data-stu-id="e8506-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-reactspa).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e8506-103">Необходимые компоненты</span><span class="sxs-lookup"><span data-stu-id="e8506-103">Prerequisites</span></span>

<span data-ttu-id="e8506-104">Прежде чем приступить к работе с этим руководством, на компьютере для разработки должен быть установлен [node. js](https://nodejs.org) .</span><span class="sxs-lookup"><span data-stu-id="e8506-104">Before you start this tutorial, you should have [Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="e8506-105">Если у вас нет Node. js, посетите предыдущую ссылку для получения вариантов загрузки.</span><span class="sxs-lookup"><span data-stu-id="e8506-105">If you do not have Node.js, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="e8506-106">Это руководство было написано с узлом версии 10.15.3.</span><span class="sxs-lookup"><span data-stu-id="e8506-106">This tutorial was written with Node version 10.15.3.</span></span> <span data-ttu-id="e8506-107">Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.</span><span class="sxs-lookup"><span data-stu-id="e8506-107">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="e8506-108">Отзывы</span><span class="sxs-lookup"><span data-stu-id="e8506-108">Feedback</span></span>

<span data-ttu-id="e8506-109">Сообщите о нем в [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-reactspa).</span><span class="sxs-lookup"><span data-stu-id="e8506-109">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-reactspa).</span></span>