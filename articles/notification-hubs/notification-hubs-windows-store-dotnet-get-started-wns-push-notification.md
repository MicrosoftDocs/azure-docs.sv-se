---
title: Skicka meddelanden till Universal Windows Platform-appar med hjälp av Azure Notification Hubs | Microsoft Docs
description: Luta hur du använder Azure Notification Hubs för att skicka meddelanden till ett Windows Universal Platform-program.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: 'mvc, ms.custom: devx-track-csharp'
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 4f55b6eafe230f722979d535111ce45aa35981f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93125045"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Självstudie: skicka meddelanden till Universell Windows-plattform appar med Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

I den här självstudiekursen kommer du att skapa en meddelandehubb för att skicka push-meddelanden till en Windows Universal Platform-app (UWP). Du skapar en tom Windows Store-app som tar emot push-meddelanden med hjälp av Windows Push Notification Service (WNS). Sedan använder du Notification Hub för att sända push-meddelanden till alla enheter som kör din app.

> [!NOTE]
> Du hittar den färdiga koden för den här självstudiekursen på [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Du utför följande steg:

> [!div class="checklist"]
> * Skapa en app i Windows Store
> * Skapa en meddelandehubb
> * Skapa en Windows-exempelapp
> * Skicka testmeddelanden

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Microsoft Visual Studio 2017 eller senare. Exemplet i den här självstudien använder [Visual Studio 2019](https://www.visualstudio.com/products).
- [Installerade verktyg för UWP-apputveckling](/windows/uwp/get-started/get-set-up)
- Ett aktivt Windows Store-konto
- Bekräfta att inställningen **Hämta meddelanden från appar och annan avsändare** är aktive rad. 
    - Starta fönstret **Inställningar** på datorn.
    - Välj **system** panelen.
    - Välj **meddelanden & åtgärder** på den vänstra menyn. 
    - Bekräfta att inställningen **Hämta meddelanden från appar och andra avsändare** är aktive rad. Aktivera den om den inte är aktive rad.

Du måste slutföra den här självstudiekursen innan du börjar någon annan kurs om Notification Hubs för UWP-appar.

## <a name="create-an-app-in-windows-store"></a>Skapa en app i Windows Store

Om du vill skicka push-meddelanden till UWP-appar, associerar du din app med Windows Store. Konfigurera sedan meddelandehubben för att integrera den med WNS.

1. Navigera till [Windows Dev Center](https://partner.microsoft.com/dashboard/windows/first-run-experience), logga in med ditt Microsoft-konto och välj sedan **Skapa en ny app**.

    ![Knappen Ny app](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Ange ett namn på appen och markera sedan **Reservera produktnamn**. På så sätt skapas en ny Windows Store-registrering för din app.

    ![Lagra appnamnet](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Expandera **produkt hantering**, Välj **WNS/MPNS** och välj sedan **Live Services site**. Logga in på ditt Microsoft-konto. Sidan program registrering öppnas på en ny flik. Du kan också gå direkt till sidan [Mina program](https://apps.dev.microsoft.com) och välja ditt program namn för att komma till den här sidan.

    ![WNS MPNS-sida](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Anteckna lösen ordet för **program hemligheter** samt både **säkerhets identifierare för paket (sid)** och **program identitet** under avsnittet Windows Store.

    >[!WARNING]
    >Programhemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Lämna aldrig ut dessa uppgifter till någon och distribuera dem inte tillsammans med din app.

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Konfigurera WNS-inställningar för hubben

1. I kategorin **MEDDELANDEINSTÄLLNINGAR** väljer du **Windows (WNS)**.
2. Ange de värden för **Package SID** och **Security Key** som du antecknade i föregående avsnitt.
3. Klicka på **Spara** i verktygsfältet.

    ![Rutorna Paket-SID och Säkerhetsnyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Din meddelandehubb har nu konfigurerats för att fungera med WNS. Du har anslutningssträngarna för att registrera din app och skicka meddelanden.

## <a name="create-a-sample-windows-app"></a>Skapa en Windows-exempelapp

1. I Visual Studio öppnar du menyn **Arkiv**. Välj **Nytt** och sedan **Projekt**.
2. Utför följande steg i dialog rutan **skapa ett nytt projekt** :

    1. Skriv **Windows Universal** i rutan Sök högst upp.
    2. I Sök resultaten väljer du **Tom app (Universal Windows)** och väljer sedan **Nästa**.

       ![Dialogrutan Nytt projekt](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. I dialog rutan **Konfigurera ditt nya projekt** anger du ett **projekt namn** och en **plats** för projektfilerna.
    4. Välj **Skapa**.

3. Acceptera standardinställningarna för **mål**- och **minimum** plattformsversioner och välj **OK**.
4. I Solution Explorer högerklickar du på projektet för Windows Store-appen, väljer **publicera** och väljer sedan **associera app med Store**. Guiden **Associera din app med Windows Store** visas.
5. I guiden loggar du in med ditt Microsoft-konto.
6. Välj den app som du registrerade i steg 2, väljer **Nästa** och välj sedan **Associera**. På så sätt läggs den registreringsinformation som krävs för Windows Store till i programmanifestet.
7. Högerklicka på lösningen i Vision Studio och välj sedan **Hantera NuGet-paket**. Fönstret **Hantera NuGet-paket** öppnas.
8. I sökrutan anger du **WindowsAzure.Messaging.Managed**, väljer **Installera** och godkänner användningsvillkoren.

    ![Fönstret Hantera NuGet-paket][20]

    Den här åtgärden hämtar, installerar och lägger till en referens i Azure Notification Hubs-biblioteket för Windows med hjälp av [Microsoft.Azure.Notification Hubs-NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Öppna `App.xaml.cs`-projektfilen och lägg till följande instruktioner:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. `App.xaml.cs`Leta upp klassen i projekt filen `App` och Lägg till följande `InitNotificationsAsync` metod definition. Ersätt `<your hub name>` med namnet på den aviserings hubb som du skapade i Azure Portal och Ersätt `<Your DefaultListenSharedAccessSignature connection string>` med `DefaultListenSharedAccessSignature` anslutnings strängen från sidan **åtkomst principer** i Notification Hub:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

    Den här koden hämtar URI-kanalen för appen från WNS och registrerar sedan denna med din meddelandehubb.

    >[!NOTE]
    > Ersätt platshållaren `hub name` med namnet på den meddelandehubb som visas i Azure-portalen. Byt även ut platshållaren för anslutningssträngen mot den `DefaultListenSharedAccessSignature`-anslutningssträng som du fick från sidan **Åtkomstpolicyer** i meddelandehubben i ett tidigare avsnitt.

11. Längst upp i `OnLaunched`-händelsehanteraren i `App.xaml.cs` lägger du till följande anrop till den nya `InitNotificationsAsync`-metoden:

    ```csharp
    InitNotificationsAsync();
    ```

    Den här åtgärden garanterar att kanal-URI registreras i meddelandehubben varje gång programmet startas.

12. Högerklicka på `Package.appxmanifest` och välj Visa kod (**F7**). Leta upp  `<Identity .../>` och Ersätt värdet med **program identiteten** från WNS som du skapade [tidigare](#create-an-app-in-windows-store).

13. Kör appen genom att trycka på **F5** på tangentbordet. En dialogruta som innehåller registreringsnyckeln visas. Stäng dialogrutan genom att klicka på **OK**.

    ![Registreringen lyckades](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Appen är nu redo att ta emot popup-meddelanden.

## <a name="send-test-notifications"></a>Skicka testmeddelanden

Du kan snabbt testa att ta emot meddelanden i appen genom att skicka meddelanden från [Azure-portalen](https://portal.azure.com/).

1. Växla till fliken Översikt i Azure Portal och välj **Testskicka** i verktygsfältet.

    ![Knappen Testskicka](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Utför följande i fönstret **Testskicka**:
    1. Välj **Windows** under **Plattformar**.
    2. Välj **Toast** under **Meddelandetyp**.
    3. Välj **Skicka**.

        ![Fönstret Skicka test](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Se resultatet av Send-åtgärden i listan **Resultat** längst ned i fönstret. Du kan också se ett varningsmeddelande.

    ![Resultatet av Send-åtgärden](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Aviseringsmeddelandet **Testmeddelande** visas på skrivbordet.

    ![Aviseringsmeddelande](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Nästa steg
Du har skickat broadcast-meddelanden till alla dina Windows-enheter med hjälp av portalen eller en konsol app. Information om hur du skickar meddelanden till specifika enheter finns i följande självstudie:

> [!div class="nextstepaction"]
>[Skicka push-meddelanden till specifika enheter](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: /previous-versions/windows/apps/hh761494(v=win.10)
[tile catalog]: /previous-versions/windows/apps/hh761491(v=win.10)
[badge overview]: /previous-versions/windows/apps/hh779719(v=win.10)
