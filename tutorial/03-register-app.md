<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="52797-101">В этом упражнении вы создадите регистрацию нового веб-приложения Azure AD с помощью центра администрирования Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="52797-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="52797-102">Откройте браузер и перейдите к [Центру администрирования Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="52797-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="52797-103">Войдите с помощью **личной учетной записи** (т.е. учетной записи Microsoft) или **рабочей (учебной) учетной записи**.</span><span class="sxs-lookup"><span data-stu-id="52797-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="52797-104">Выберите **Azure Active Directory** на панели навигации слева, затем выберите **Регистрация приложений** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="52797-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="52797-105">Снимок экрана с регистрациями приложений</span><span class="sxs-lookup"><span data-stu-id="52797-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

    > [!NOTE]
    > <span data-ttu-id="52797-106">Пользователи Azure AD B2C могут видеть только **регистрации приложений (прежних версий)**.</span><span class="sxs-lookup"><span data-stu-id="52797-106">Azure AD B2C users may only see **App registrations (legacy)**.</span></span> <span data-ttu-id="52797-107">В этом случае перейдите непосредственно к [https://aka.ms/appregistrations](https://aka.ms/appregistrations) .</span><span class="sxs-lookup"><span data-stu-id="52797-107">In this case, please go directly to [https://aka.ms/appregistrations](https://aka.ms/appregistrations).</span></span>

1. <span data-ttu-id="52797-108">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="52797-108">Select **New registration**.</span></span> <span data-ttu-id="52797-109">На странице**Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="52797-109">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="52797-110">Введите **имя** `React Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="52797-110">Set **Name** to `React Graph Tutorial`.</span></span>
    - <span data-ttu-id="52797-111">Введите **поддерживаемые типы учетных записей** для **учетных записей в любом каталоге организаций и личных учетных записей Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="52797-111">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="52797-112">В разделе **URI адрес перенаправления** введите значение в первом раскрывающемся списке `Single-page application (SPA)` и задайте значение `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="52797-112">Under **Redirect URI**, set the first drop-down to `Single-page application (SPA)` and set the value to `http://localhost:3000`.</span></span>

    ![Снимок страницы "регистрация приложения"](./images/aad-register-an-app.png)

1. <span data-ttu-id="52797-114">Нажмите кнопку **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="52797-114">Choose **Register**.</span></span> <span data-ttu-id="52797-115">На странице **учебника "реагирующий график** " СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="52797-115">On the **React Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](./images/aad-application-id.png)
