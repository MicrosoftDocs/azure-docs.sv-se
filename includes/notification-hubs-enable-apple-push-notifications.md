---
title: ta med fil
description: ta med fil
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 7b5034f2163e8478d7ddb7b9271402b094a809d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95560673"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generera filen för certifikat signerings förfrågan

Apple Push Notification Service (APN) använder certifikat för att autentisera dina push-meddelanden. Följ de här instruktionerna för att skapa nödvändiga push-certifikat för att skicka och ta emot meddelanden. Mer information om de här koncepten finns i den officiella [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)-dokumentationen.

Generera CSR-filen (certifikat signerings förfrågan) som Apple använder för att generera ett signerat Push-certifikat.

1. Kör Nyckelhanteraren på din Mac. Den kan öppnas från mappen **verktyg** eller den **andra** mappen i Start fönstret.

1. Välj **nyckel rings åtkomst**, expandera **certifikat assistenten** och välj sedan **begär ett certifikat från en certifikat utfärdare**.

    ![Använd nyckelhanterare för att begära ett nytt certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Som standard väljer åtkomst till nyckel ringar det första objektet i listan. Detta kan vara ett problem om du befinner dig i kategorin **certifikat** och **certifikat utfärdaren Apple Worldwide Developer relation** är inte det första objektet i listan. Kontrol lera att du har ett objekt som inte är ett nyckel objekt, eller att nyckeln för **certifikat utfärdare för Apple Worldwide Developer-relation** är vald, innan du genererar CSR (certifikat signerings förfrågan).

1. Välj din **användar-e-postadress**, ange ditt **eget namn** -värde, kontrol lera att du har angett **Spara på disk** och välj sedan **Fortsätt**. Lämna **ca-e-postadress** tomt eftersom det inte behövs.

    ![Nödvändig certifikatsinformation](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Ange ett namn på CSR-filen i **Spara som**, Välj den plats **där** och välj sedan **Spara**.

    ![Välj ett fil namn för certifikatet](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Den här åtgärden sparar CSR-filen på den valda platsen. Standard platsen är **Skriv bord**. Kom ihåg den plats du valde för filen.

Registrera sedan din app med Apple, aktivera push-meddelanden och ladda upp den exporterade CSR-filen för att skapa ett push-certifikat.

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

Om du vill skicka push-meddelanden till en iOS-App registrerar du ditt program med Apple och registrerar dig även för push-meddelanden.  

1. Om du inte redan har registrerat din app kan du gå till [iOS-etablerings portalen](https://go.microsoft.com/fwlink/p/?LinkId=272456) i Apple Developer Center. Logga in på portalen med ditt Apple-ID och välj **identifierare**. Välj sedan **+** att registrera en ny app.

    ![Sidan för App-ID:n i etableringsportalen för iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. På sidan **Registrera en ny identifierare** väljer du alternativ knappen **app-ID** . Välj sedan **Fortsätt**.

    ![iOS-etablerings Portal Registrera ny ID-sida](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Uppdatera följande tre värden för din nya app och välj sedan **Fortsätt**:

   * **Beskrivning**: Ange ett beskrivande namn för din app.

   * **Paket-ID**: Ange ett paket-ID för formatet **organisations-ID. produkt namn** som nämns i [program distributions guiden](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). *Organisations-ID* och *produkt namn* måste matcha organisations-ID och produkt namn som du använder när du skapar ditt Xcode-projekt. I följande skärm bild används **NotificationHubs** -värdet som ett organisations-ID och värdet **GetStarted** används som produkt namn. Se till att värdet för **paket identifieraren** matchar värdet i Xcode-projektet, så att Xcode använder rätt publicerings profil.

      ![iOS-etablerings Portal registrera app-ID-sida](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Push-meddelanden**: Markera alternativet **push-meddelanden** i avsnittet **funktioner** .

      ![Formulär för att registrera ett nytt App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Den här åtgärden genererar ditt app-ID och begär att du bekräftar informationen. Välj **Fortsätt** och välj **Registrera** för att bekräfta det nya app-ID: t.

      ![Bekräfta nytt app-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      När du har valt **Registrera** visas det nya app-ID: t som ett rad objekt på sidan **certifikat, identifierare & profiler** .

4. På sidan **certifikat, identifierare & profiler** , under **identifierare**, letar du upp det ID-rads objekt för app-ID som du nyss skapade och väljer dess rad för att visa skärmen **Redigera appens ID-konfiguration** .

## <a name="creating-a-certificate-for-notification-hubs"></a>Skapa ett certifikat för Notification Hubs
Ett certifikat krävs för att meddelande hubben ska fungera med **APN**. Detta kan göras på ett av två sätt:

1. Skapa en **. p12** som kan överföras direkt till Notification Hub.  
2. Skapa en **. P8** som kan användas för [tokenbaserad autentisering](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md) (*den nyare metoden*).

Den nyare metoden har ett antal fördelar (jämfört med att använda certifikat) enligt beskrivningen i [tokenbaserad (http/2) autentisering för APN](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md). Men du har fått anvisningar för båda metoderna. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>ALTERNATIV 1: skapa ett. p12-Push-certifikat som kan överföras direkt till Notification Hub

1. Rulla ned till alternativet kontrollerade **push-meddelanden** och välj sedan **Konfigurera** för att skapa certifikatet.

    ![Redigera sidan för App-ID:n](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Fönstret **SSL-certifikat för Apple Push Notification Service** visas. Välj knappen **Skapa certifikat** under avsnittet **utvecklings-SSL-certifikat** .

    ![Skapa certifikat för knappen för App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Skärmen **skapa ett nytt certifikat** visas.

    > [!NOTE]
    > I den här självstudien används ett utvecklings certifikat, som appen använder för att generera en unik enhets-token. Du använder samma process när du registrerar ett driftscertifikat. Se bara till att du använder samma certifikattyp när du skickar meddelanden.

3. Välj **Välj fil**, bläddra till den plats där du sparade CSR-filen från den första aktiviteten och dubbelklicka sedan på certifikat namnet för att läsa in det. Välj sedan **Fortsätt**.

4. När portalen har skapat certifikatet väljer du knappen **Ladda ned** . Spara certifikatet och kom ihåg platsen där det sparades.

    ![Nedladdningssidan för genererade certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikatet laddas ned och sparas på din dator i mappen **hämtade filer** .

    ![Hitta certifikatfilen i mappen Hämtade filer](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Som standard heter det hämtade utvecklings certifikatet **aps_development. cer**.

5. Dubbelklicka på det hämtade push-certifikatet **aps_development.cer**. Den här åtgärden installerar det nya certifikatet i nyckelringen enligt följande bild:

    ![Nya certifikat visas i listan med certifikat för nyckelhanteraren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Även om namnet i ditt certifikat kan vara olika, kommer namnet att föregås av **Apple Development iOS push-tjänster**.

6. I nyckelhanteraren högerklickar du på det nya push-certifikatet som du skapade i **Certifikat**-kategorin. Välj **Exportera**, namnge filen, Välj **. p12** -formatet och välj sedan **Spara**.

    ![Exportera certifikatet i p12-format](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Du kan välja att skydda certifikatet med ett lösen ord, men det är valfritt. Klicka på **OK** om du vill kringgå lösen ords skapande. Anteckna filnamnet och platsen dit .p12-certifikatet exporterats. De används för att aktivera autentisering med APN.

    > [!NOTE]
    > Namnet på och platsen för. p12-filen kan skilja sig från vad som visas i den här självstudien.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>ALTERNATIV 2: skapa ett. P8-certifikat som kan användas för tokenbaserad autentisering

1. Anteckna följande information:

    - **App-ID-prefix** (detta är ett **Team-ID**)
    - **Samlings-ID**
    
2. Tillbaka i **certifikat, identifierare & profiler**, klickar du på **nycklar**.

   > [!NOTE]
   > Om du redan har en nyckel som har kon figurer ATS för **APN** kan du använda det. P8-certifikat som du laddade ned direkt efter att det har skapats. I så fall kan du ignorera steg **3** till **5**.

3. Klicka på **+** knappen (eller knappen **skapa en nyckel** ) för att skapa en ny nyckel.
4. Ange ett lämpligt **nyckel namn** värde, kontrol lera sedan alternativet för **Apple Push Notifications-tjänsten (APNs)** och klicka sedan på **Fortsätt**, följt av **Registrera** på nästa skärm.
5. Klicka på **Hämta** och flytta **. P8** -filen (med prefixet *AuthKey_*) till en säker lokal katalog och klicka sedan på **Slutför**.

   > [!NOTE] 
   > Se till att behålla din. P8-fil på en säker plats (och spara en säkerhets kopia). När du har laddat ned nyckeln kan du inte ladda ned den igen eftersom Server kopian tas bort.
  
6. Klicka på nyckeln som du nyss skapade (eller en befintlig nyckel om du har valt att använda den i stället) på **nycklar**.
7. Anteckna värdet för **nyckel-ID** .
8. Öppna ditt. P8-certifikat i ett lämpligt program, till exempel [**Visual Studio Code**](https://code.visualstudio.com) , och anteckna värdet för nyckeln. Detta är värdet mellan **-----att starta den privata nyckeln-----** och **-----avsluta den privata nyckeln-----** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Detta är det **token-värde** som kommer att användas senare för att konfigurera **Notification Hub**. 

I slutet av de här stegen bör du ha följande information för att kunna använda senare i [Konfigurera Notification Hub med APN-information](#configure-your-notification-hub-with-apns-information):

- **Team-ID** (se steg 1)
- **Paket-ID** (se steg 1)
- **Nyckel-ID** (se steg 7)
- **Token-värde** , dvs. P8-nyckel värde (se steg 8)

## <a name="create-a-provisioning-profile-for-the-app"></a>Skapa en etableringsprofil för appen

1. Gå tillbaka till [iOS-etablerings portalen](https://go.microsoft.com/fwlink/p/?LinkId=272456), Välj **certifikat, identifierare & profiler**, Välj **profiler** på den vänstra menyn och välj sedan **+** för att skapa en ny profil. Skärmen **Registrera en ny etablerings profil** visas.

1. Välj **utveckling av iOS-appar** under **utveckling** som etablerings profil typ och välj sedan **Fortsätt**.

    ![Lista med etableringsprofiler](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Välj sedan det app-ID som du skapade i list rutan **app-ID** och välj **Fortsätt**.

    ![Välj App-ID:et](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. I fönstret **Välj certifikat** väljer du det utvecklings certifikat som du använder för kod signering och väljer **Fortsätt**. Det här certifikatet är inte det Push-certifikat som du har skapat. Om det inte finns någon sådan, måste du skapa den. Om det finns ett certifikat går du vidare till nästa steg. Så här skapar du ett utvecklings certifikat om det inte finns något:

    1. Om du **inte ser några certifikat tillgängliga** väljer du **Skapa certifikat**.
    2. Välj **Apple-utveckling** i avsnittet **program vara** . Välj sedan **Fortsätt**.
    3. På skärmen **skapa ett nytt certifikat** väljer du **Välj fil**.
    4. Bläddra till certifikatet **signerings förfrågan** som du skapade tidigare, markera det och välj sedan **Öppna**.
    5. Välj **Fortsätt**.
    6. Hämta utvecklings certifikatet och kom ihåg platsen där det sparades.

1. Gå tillbaka till sidan **certifikat, identifierare & profiler** , Välj **profiler** på den vänstra menyn och välj sedan **+** för att skapa en ny profil. Skärmen **Registrera en ny etablerings profil** visas.

1. I fönstret **Välj certifikat** väljer du det utvecklings certifikat som du nyss skapade. Välj sedan **Fortsätt**.

1. Välj sedan de enheter som ska användas för testning och välj **Fortsätt**.

1. Slutligen väljer du ett namn för profilen i **etablerings profil namn** och väljer **generera**.

    ![Välj namn på etableringsprofilen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. När den nya etablerings profilen har skapats väljer du **Hämta**. Kom ihåg platsen där den sparades.

1. Bläddra till platsen för etablerings profilen och dubbelklicka sedan på den för att installera den på Xcode-utvecklings datorn.

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

I det här avsnittet skapar du en Notification Hub och konfigurerar autentisering med APNs genom att använda antingen. p12 Push-certifikat eller tokenbaserad autentisering. Om du vill använda ett meddelande nav som du redan har skapat kan du gå vidare till steg 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurera Notification Hub med APN-information

Under **Notification Services** väljer du **Apple (APNs)** och följer sedan lämpliga steg baserat på den metod som du valde tidigare i avsnittet [skapa ett certifikat för Notification Hubs](#creating-a-certificate-for-notification-hubs) .  

> [!NOTE]
> Om du skapar appen med App Store eller ad hoc-postdistributionsgrupp använder du **produktion** för **program läge**. Detta gör att enheten kan skicka push-meddelanden till användare som har köpt din app från Store.

### <a name="option-1-using-a-p12-push-certificate"></a>ALTERNATIV 1: använda ett. p12-Push-certifikat

1. Välj **Certifikat**.

1. Välj filikonen.

1. Välj den. P12-fil som du exporterade tidigare och välj sedan **Öppna**.

1. Ange rätt lösen ord om det behövs.

1. Välj **Sandbox**-läge.

    ![Konfigurera APNs-certifikat i Azure-portalen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Välj **Spara**.

### <a name="option-2-using-token-based-authentication"></a>ALTERNATIV 2: använda tokenbaserad autentisering

1. Välj **token**.
1. Ange följande värden som du har köpt tidigare:

    - **Nyckel-ID**
    - **Samlings-ID**
    - **Team-ID**
    - **Token** 

1. Välj **sandbox**
1. Välj **Spara**. 

Du har nu konfigurerat din Notification Hub med APN. Du har också anslutnings strängarna för att registrera din app och skicka push-meddelanden.