---
title: Skicka push-meddelanden till iOS med Azure Notification Hubs och iOS SDK
description: I den här självstudien får du lära dig hur du använder Azure Notification Hubs och Apple Push Notification Service för att skicka push-meddelanden till iOS-enheter.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c18814230eda6624e8ab1040797c66108f630e30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581748"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Självstudie: skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs

Den här självstudien visar hur du konfigurerar Azure Notification Hubs och konfigurerar autentiseringsuppgifter för att skicka meddelanden till en iOS-enhet via [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

Att köra den här självstudien är en förutsättning för de efterföljande kurserna för mål C och SWIFT iOS och omfattar följande steg:

- Generera filen för certifikat signerings förfrågan.
- Begär din app för push-meddelanden.
- Skapa en etablerings profil för appen.
- Skapa en meddelandehubb.
- Konfigurera Notification Hub med APN-information.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/).

Du behöver också följande:

- Ett aktivt [Apple Developer](https://developer.apple.com/) -konto.
- En Mac som kör [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), tillsammans med ett giltigt certifikat för utvecklare som installerats i din nyckel Ring.
- En iPhone eller iPad som kör iOS version 10 eller senare.
- Din fysiska enhet som är registrerad i [Apples Portal](https://developer.apple.com/) och kopplad till ditt certifikat.

Se till att läsa [Azure Notification Hubs-översikten](notification-hubs-push-notification-overview.md) om du inte är bekant med tjänsten.

> [!NOTE]
> Notification Hub konfigureras för att endast använda begränsat läge för autentisering. Du bör inte använda detta autentiseringsläge för produktions arbets belastningar.

## <a name="generate-the-certificate-signing-request-file"></a>Generera filen för certifikat signerings förfrågan

APNS (Apple Push Notification Service) använder sig av certifikat för att autentisera dina push-meddelanden. Följ de här instruktionerna för att skapa nödvändiga push-certifikat för att skicka och ta emot meddelanden. Mer information om de här koncepten finns i den officiella [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)-dokumentationen.

Generera CSR-filen (certifikat signerings förfrågan) som Apple använder för att generera ett signerat Push-certifikat:

1. Kör Nyckelhanteraren på din Mac. Den kan öppnas från mappen **verktyg** eller den **andra** mappen i Start fönstret.

2. Välj **nyckel rings åtkomst**, expandera **certifikat assistenten** och välj sedan **begär ett certifikat från en certifikat utfärdare**.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Skärm bild som visar meny alternativet begär ett certifikat från en certifikat utfärdare.":::

   > [!NOTE]
   > Som standard väljer åtkomst till nyckel ringar det första objektet i listan. Detta kan vara ett problem om du befinner dig i kategorin **certifikat** och **certifikat utfärdaren Apple Worldwide Developer relation** är inte det första objektet i listan. Kontrol lera att du har ett objekt som inte är ett nyckel objekt, eller att nyckeln för **certifikat utfärdare för Apple Worldwide Developer-relation** är vald, innan du genererar CSR (certifikat signerings förfrågan).

3. Välj din **användar-e-postadress**, ange ditt **eget namn** -värde, kontrol lera att du har angett **Spara på disk** och välj sedan **Fortsätt**. Lämna **ca-e-postadress** tomt eftersom det inte behövs.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Nödvändig certifikatsinformation":::

4. Ange ett namn på CSR-filen i **Spara som**, Välj den plats **där** och välj sedan **Spara**.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Välj fil namn":::

   Den här åtgärden sparar CSR-filen på den valda platsen. Standard platsen är **Skriv bord**. Kom ihåg den plats du valde för filen.

Registrera sedan din app med Apple, aktivera push-meddelanden och ladda upp den exporterade CSR-filen för att skapa ett push-certifikat.

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

Om du vill skicka push-meddelanden till en iOS-App registrerar du ditt program med Apple och registrerar dig även för push-meddelanden.

1. Om du inte redan har registrerat din app kan du gå till [iOS-etablerings portalen](https://go.microsoft.com/fwlink/p/?LinkId=272456) i Apple Developer Center. Logga in på portalen med ditt Apple-ID och välj **identifierare**. Välj sedan **+** att registrera en ny app.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Sidan app-ID":::

2. På sidan **Registrera en ny identifierare** väljer du alternativ knappen **app-ID** . Välj sedan **Fortsätt**.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Sidan Registrera nytt ID":::

3. Uppdatera följande tre värden för din nya app och välj sedan **Fortsätt**:

   - **Beskrivning**: Ange ett beskrivande namn för din app.
   - **Paket-ID**: Ange ett paket-ID för formatet **organisations-ID. produkt namn** som nämns i [program distributions guiden](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). **Organisations-ID** och **produkt namn** måste matcha organisations-ID och produkt namn som du använder när du skapar ditt Xcode-projekt. I följande skärm bild används **NotificationHubs** -värdet som ett organisations-ID och värdet **GetStarted** används som produkt namn. Se till att värdet för **paket identifieraren** matchar värdet i Xcode-projektet, så att Xcode använder rätt publicerings profil.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Registrera app-ID":::

   - **Push-meddelanden**: Markera alternativet **push-meddelanden** i avsnittet **funktioner** .

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Registrera nytt app-ID":::

      Den här åtgärden genererar ditt app-ID och begär att du bekräftar informationen. Välj **Fortsätt** och välj **Registrera** för att bekräfta det nya app-ID: t.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Bekräfta nytt app-ID":::

      När du har valt **Registrera** visas det nya app-ID: t som ett rad objekt på sidan **certifikat, identifierare & profiler** .

4. På sidan **certifikat, identifierare & profiler** , under **identifierare**, letar du upp det ID-rads objekt för app-ID som du nyss skapade och väljer dess rad för att visa skärmen **Redigera appens ID-konfiguration** .

## <a name="create-a-certificate-for-notification-hubs"></a>Skapa ett certifikat för Notification Hubs

> [!NOTE]
> Med lanseringen av iOS 13 kan du bara ta emot tysta meddelanden med hjälp av tokenbaserad autentisering. Om du använder certifikatbaserad autentisering för dina APN-autentiseringsuppgifter måste du växla till med hjälp av tokenbaserad autentisering.

Ett certifikat krävs för att meddelande hubben ska fungera med **APN**. Detta kan göras på ett av två sätt:

- Skapa en **. p12** -fil som kan överföras direkt till Notification Hubs.

- Skapa en **. P8** -fil som kan användas för [tokenbaserad autentisering](notification-hubs-push-notification-http2-token-authentication.md) (den nyare metoden).

Det andra alternativet har ett antal fördelar jämfört med att använda certifikat, enligt beskrivningen i [token-baserad (http/2)-autentisering för APN](notification-hubs-push-notification-http2-token-authentication.md). Men det finns anvisningar för båda metoderna.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Alternativ 1: skapa ett. p12 Push-certifikat som kan överföras direkt till Notification Hubs

1. Rulla ned till alternativet kontrollerade **push-meddelanden** och välj sedan **Konfigurera** för att skapa certifikatet.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="App-ID":::

2. Fönstret **SSL-certifikat för Apple Push Notification Service** visas. Välj knappen **Skapa certifikat** i avsnittet **utvecklings-SSL-certifikat** .

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Skapa certifikat":::

   Skärmen **skapa ett nytt certifikat** visas.

   > [!NOTE]
   > Den här guiden använder ett utvecklarcertifikat. Du använder samma process när du registrerar ett driftscertifikat. Kontrol lera att du använder samma certifikat typ när du skickar meddelanden.

3. Välj **Välj fil**, bläddra till den plats där du sparade CSR-filen från den första aktiviteten och dubbelklicka sedan på certifikat namnet för att läsa in det. Välj sedan **Fortsätt**.

4. När portalen har skapat certifikatet väljer du knappen **Ladda ned** . Spara certifikatet och kom ihåg platsen där det sparades.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Ladda ned certifikat":::

   Certifikatet laddas ned och sparas i mappen **hämtade filer** .

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Hitta certifikat fil":::

   Som standard heter det hämtade utvecklings certifikatet **aps_development. cer**.

5. Dubbelklicka på det nedladdade push-certifikatet **APS \_ Development. cer**. Den här åtgärden installerar det nya certifikatet i nyckelringen enligt följande bild:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Nyckel rings åtkomst":::

   Även om namnet i ditt certifikat kan vara olika, kommer namnet att föregås av **Apple Development iOS push-tjänster**.

6. I nyckelhanteraren högerklickar du på det nya push-certifikatet som du skapade i **Certifikat**-kategorin. Välj **Exportera**, namnge filen, Välj **. p12** -formatet och välj sedan **Spara**.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Exportera certifikatet":::

   Du kan välja att skydda certifikatet med ett lösen ord, men det är valfritt. Klicka på **OK** om du vill kringgå lösen ords skapande. Anteckna filnamnet och platsen dit .p12-certifikatet exporterats. De används för att aktivera autentisering med APN.

   > [!NOTE]
   > Namnet på och platsen för. p12-filen kan skilja sig från vad som visas i den här självstudien.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Alternativ 2: skapa ett. P8-certifikat som kan användas för tokenbaserad autentisering

1. Anteckna följande information:

   - **App-ID-prefix** (detta är ett **Team-ID**)
   - **Samlings-ID**

2. Tillbaka i **certifikat, identifierare & profiler**, klickar du på **nycklar**. Om du redan har en nyckel som har kon figurer ATS för **APN** kan du använda det. P8-certifikat som du laddade ned direkt efter att det har skapats. I så fall kan du ignorera steg 3 till 5.

3. Klicka på **+** knappen (eller knappen **skapa en nyckel** ) för att skapa en ny nyckel.

4. Ange ett lämpligt **nyckel namns** värde, kontrol lera **APNs-alternativet (Apple Push Notifications service)** och klicka sedan på **Fortsätt**, följt av **Registrera** på nästa skärm.

5. Klicka på **Hämta** och flytta **. P8** -filen (med prefixet till `AuthKey_` ) till en säker lokal katalog och klicka sedan på **Slutför**.

   > [!IMPORTANT]
   > Se till att behålla din. P8-fil på en säker plats (och spara en säkerhets kopia). När du har laddat ned nyckeln går det inte att ladda ned den igen. Server kopian tas bort.

6. Klicka på nyckeln som du nyss skapade (eller en befintlig nyckel om du har valt att använda den i stället) på **nycklar**.

7. Anteckna värdet för **nyckel-ID** .

8. Öppna ditt. P8-certifikat i ett lämpligt program, till exempel [Visual Studio Code](https://code.visualstudio.com/), och anteckna nyckel värdet. Detta är värdet mellan **-----att starta den privata nyckeln-----** och **-----avsluta den privata nyckeln-----** .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Detta är det token-värde som kommer att användas senare för att konfigurera Notification Hubs.

I slutet av de här stegen bör du ha följande information för att kunna använda senare i [Konfigurera Notification Hub med APN-information](#configure-the-notification-hub-with-apns-information):

- **Team-ID** (se steg 1)
- **Paket-ID** (se steg 1)
- **Nyckel-ID** (se steg 7)
- **Token-värde** (. P8-nyckel värde, se steg 8)

## <a name="create-a-provisioning-profile"></a>Skapa en etablerings profil

1. Gå tillbaka till [iOS-etablerings portalen](https://go.microsoft.com/fwlink/p/?LinkId=272456), Välj **certifikat, identifierare & profiler**, Välj **profiler** på den vänstra menyn och välj sedan **+** för att skapa en ny profil. Skärmen **Registrera en ny etablerings profil** visas.

2. Välj **utveckling av iOS-appar** under **utveckling** som etablerings profil typ och välj sedan **Fortsätt**.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Lista med etableringsprofiler":::

3. Välj sedan det app-ID som du skapade i list rutan **app-ID** och välj sedan **Fortsätt**.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Välj app-ID":::

4. I fönstret **Välj certifikat** väljer du det utvecklings certifikat som du använder för kod signering och väljer **Fortsätt**. Det här certifikatet är inte det Push-certifikat som du har skapat. Om det inte finns någon sådan, måste du skapa den. Om det finns ett certifikat går du vidare till nästa steg. Så här skapar du ett utvecklings certifikat om det inte finns något:

   1. Om du **inte ser några certifikat tillgängliga** väljer du **Skapa certifikat**.
   2. Välj **Apple-utveckling** i avsnittet **program vara** . Välj sedan **Fortsätt**.
   3. På skärmen **skapa ett nytt certifikat** väljer du **Välj fil**.
   4. Bläddra till certifikatet **signerings förfrågan** som du skapade tidigare, markera det och välj sedan **Öppna**.
   5. Välj **Fortsätt**.
   6. Hämta utvecklings certifikatet och kom ihåg platsen där det sparades.

5. Gå tillbaka till sidan **certifikat, identifierare & profiler** , Välj **profiler** på den vänstra menyn och välj sedan **+** för att skapa en ny profil. Skärmen **Registrera en ny etablerings profil** visas.

6. I fönstret **Välj certifikat** väljer du det utvecklings certifikat som du nyss skapade. Välj sedan **Fortsätt**.

7. Välj sedan de enheter som ska användas för testning och välj **Fortsätt**.

8. Slutligen väljer du ett namn för profilen i **etablerings profil namn** och väljer sedan **skapa**.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Välj namn på etablerings profil":::

9. När den nya etablerings profilen har skapats väljer du **Hämta**. Kom ihåg platsen där den sparades.

10. Bläddra till platsen för etablerings profilen och dubbelklicka sedan på den för att installera den på Xcode-utvecklings datorn.

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

I det här avsnittet skapar du en Notification Hub och konfigurerar autentisering med APNS genom att använda antingen. p12 Push-certifikat eller tokenbaserad autentisering. Om du vill använda ett meddelande nav som du redan har skapat kan du gå vidare till steg 5.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **alla tjänster** på den vänstra menyn och välj sedan **Notification Hubs** i avsnittet **mobil** . Välj stjärn ikonen bredvid tjänst namnet för att lägga till tjänsten i **Favoriter** -avsnittet på den vänstra menyn. När du har lagt till **Notification Hubs** i **Favoriter** väljer du den.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure-portalen":::

3. På sidan **Notification Hubs** väljer du **Lägg till** i verktygsfältet.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Lägga till knapp i verktygsfältet":::

4. Utför följande steg på sidan **Notification Hubs** :

   1. Ange ett namn i **Notification Hub**.
   2. Ange ett namn i **skapa ett nytt namn område**. En namnrymd innehåller ett eller flera Notification Hub.
   3. Välj ett värde i list rutan **plats** . Det här värdet anger den plats där du vill skapa Notification Hub.
   4. Välj en befintlig resurs grupp i **resurs gruppen** eller skapa en ny resurs grupp.
   5. Välj **Skapa**.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Ange egenskaper":::

5. Välj **meddelanden** (klock ikonen) och välj sedan **gå till resurs**. Du kan också uppdatera listan på sidan **Notification Hubs** och välja hubben.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Portal meddelanden":::

6. Välj **Åtkomstprinciper** i listan. Observera att de två anslutnings strängarna är tillgängliga för dig. Du behöver dem senare för att hantera push-meddelanden.

   > [!IMPORTANT]
   > Använd inte **DefaultFullSharedAccessSignature** -principen i ditt program. Detta är endast avsett att användas i Server delen.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Anslutningssträngar":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Konfigurera Notification Hub med APN-information

Under **Notification Services** väljer du **Apple (APNs)** och följer sedan lämpliga steg baserat på den metod som du valde tidigare i avsnittet [skapa ett certifikat för Notification Hubs](#create-a-certificate-for-notification-hubs) .

> [!NOTE]
> Använd endast **produktion** för **program läge** om du vill skicka push-meddelanden till användare som har köpt din app från Store.

### <a name="option-1-use-a-p12-push-certificate"></a>Alternativ 1: Använd ett. p12-Push-certifikat

1. Välj **Certifikat**.

2. Välj filikonen.

3. Välj den. P12-fil som du exporterade tidigare och välj sedan **Öppna**.

4. Ange rätt lösen ord om det behövs.

5. Välj **Sandbox**-läge.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="I":::

6. Välj **Spara**.

### <a name="option-2-use-token-based-authentication"></a>Alternativ 2: Använd tokenbaserad autentisering

1. Välj **token**.

2. Ange följande värden som du har köpt tidigare:

   - **Nyckel-ID**
   - **Samlings-ID**
   - **Team-ID**
   - **Token**

3. Välj **sandbox**

4. Välj **Spara**.

Du har nu konfigurerat din Notification Hub med APN. Du har också de anslutnings strängar som krävs för att registrera din app och skicka push-meddelanden.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat och konfigurerat en Notification Hub i Azure och konfigurerat det så att meddelanden kan skickas till ditt program via Apple Push Notification Service (APN). Härnäst ska vi skapa ett exempel på iOS-program och integrera Azure Notifications Hub SDK så att det kan ta emot push-meddelanden som skickas via Azure Portal. Gå vidare till följande självstudie baserat på ditt val av språk:

- [Självstudie: skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-current.md)
