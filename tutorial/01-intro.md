<!-- markdownlint-disable MD002 MD041 -->

В этом руководстве рассказывается, как создать React-страницу приложения, которое использует API microsoft Graph для получения сведений о календаре для пользователя.

> [!TIP]
> Если вы предпочитаете просто скачать завершенный учебник, вы можете скачать или клонировать [GitHub репозиторий](https://github.com/microsoftgraph/msgraph-training-reactspa).

## <a name="prerequisites"></a>Необходимые условия

Перед началом этого руководства [](https://nodejs.org) необходимо установитьNode.js[и yarn](https://classic.yarnpkg.com/) на компьютере разработки. Если у вас нет Node.js или пряжи, посетите предыдущие ссылки для скачать варианты.

Вы также должны иметь личную учетную запись Майкрософт с почтовым ящиком на Outlook.com или учетную запись Microsoft work или school. Если у вас нет учетной записи Майкрософт, существует несколько вариантов получения бесплатной учетной записи:

- Вы можете [зарегистрироваться для новой личной учетной записи Майкрософт.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Вы можете [зарегистрироваться в программе Office 365 разработчика,](https://developer.microsoft.com/office/dev-program) чтобы получить бесплатную Office 365 подписку.

> [!NOTE]
> Этот учебник был написан с версией Node 14.15.0 и Yarn версии 1.22.10. Действия в этом руководстве могут работать с другими версиями, но они не были проверены.

## <a name="feedback"></a>Отзывы

В репозитории [](https://github.com/microsoftgraph/msgraph-training-reactspa)GitHub.
