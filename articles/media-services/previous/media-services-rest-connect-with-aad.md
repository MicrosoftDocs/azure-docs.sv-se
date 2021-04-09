---
title: Använd Azure AD-autentisering för att få åtkomst till Azure Media Services API med REST | Microsoft Docs
description: Lär dig hur du kommer åt Azure Media Services API med Azure Active Directory autentisering med hjälp av REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: a2b4e7bf03ebb1fbc197b78287cb50b3f421d713
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017315"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Använda Azure AD-autentisering för att ansluta till API:et för Media Services med REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

När du använder Azure AD-autentisering med Azure Media Services kan du autentisera på ett av två sätt:

- **Användarautentisering** autentiserar en person som använder appen för att interagera med Azure Media Services resurser. Det interaktiva programmet bör först uppmana användaren att ange autentiseringsuppgifter. Ett exempel är en hanterings konsol app som används av behöriga användare för att övervaka kodnings jobb eller direktsänd strömning. 
- **Autentisering av tjänstens huvud namn** autentiserar en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, tjänster på mellan nivå eller schemalagda jobb, till exempel webbappar, Function-appar, Logic Apps, API: er eller mikrotjänster.

    Den här självstudien visar hur du använder autentisering med Azure AD **-tjänstens huvud namn** för att få åtkomst till AMS API med rest. 

    > [!NOTE]
    > **Tjänstens huvud namn** rekommenderas för de flesta program som ansluter till Azure Media Services. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Hämta autentiseringsinformation från Azure Portal
> * Hämta åtkomsttoken med Postman
> * Testa **till gångs** -API: et med hjälp av åtkomsttoken


> [!IMPORTANT]
> För närvarande har Media Services stöd för Azure Access Control-tjänsternas autentiserings modell. Access Control autentisering kommer dock att föråldras den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- [Skapa ett Azure Media Services konto med hjälp av Azure Portal](media-services-portal-create-account.md).
- Läs artikeln om att [komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md) .
- Installera [Postman](https://www.getpostman.com/) rest-klienten för att köra REST-API: erna som visas i den här artikeln. 

    I den här självstudien använder vi **Postman** , men resten av verktyget är lämpligt. Andra alternativ är: **Visual Studio Code** med rest-plugin-programmet eller **Telerik-Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Hämta autentiseringsinformation från Azure Portal

### <a name="overview"></a>Översikt

Du måste samla in följande data punkter för att få åtkomst till Media Services API.

|Inställning|Exempel|Beskrivning|
|---|-------|-----|
|Azure Active Directory klient domän|microsoft.onmicrosoft.com|Azure AD som en STS-slutpunkt (Secure token service) skapas med följande format: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token> . Azure AD utfärdar en JWT för att få åtkomst till resurser (en åtkomsttoken).|
|Slutpunkt för REST-API:t|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Detta är den slut punkt mot vilken alla Media Services REST API samtal i ditt program görs.|
|Klient-ID (program-ID)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|ID för Azure AD-program (klient). Klient-ID krävs för att hämta åtkomsttoken. |
|Client Secret (Klienthemlighet)|+ mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq + Dbim0 =|Azure AD-programnycklar (klient hemlighet). Klient hemligheten krävs för att få åtkomst-token.|

### <a name="get-azure-active-directory-auth-info-from-the-azure-portal"></a>Hämta Azure Active Directory auth-information från Azure Portal

Följ dessa steg för att hämta informationen:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Navigera till din AMS-instans.
3. Välj **API-åtkomst**.
4. Klicka på **Anslut för att Azure Media Services API med tjänstens huvud namn**.

    ![Skärm bild som visar "A P I Access" vald från "Media Services"-menyn och "Anslut till Azure Media Services ett P I med tjänstens huvud namn" valt i den högra rutan.](./media/connect-with-rest/connect-with-rest01.png)

5. Välj ett befintligt **Azure AD-program** eller skapa ett nytt (visas nedan).

    > [!NOTE]
    > För att Azure Media REST-begäran ska lyckas måste den anropande användaren ha en **deltagar** -eller **ägar** roll för det Media Services konto som den försöker komma åt. Om du får ett undantag som säger "Fjärrservern returnerade ett fel: (401) obehörig," se [åtkomst kontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Följ dessa steg om du behöver skapa en ny AD-App:
    
   1. Tryck på **Skapa nytt**.
   2. Ange ett namn.
   3. Tryck på **Skapa nytt** igen.
   4. Tryck på **Spara**.

      ![Skärm bild som visar dialog rutan "Skapa ny" med text rutan "skapa app" markerad och knappen "Spara" markerad.](./media/connect-with-rest/new-app.png)

      Den nya appen visas på sidan.

6. Hämta **klient-ID** (program-ID).
    
   1. Välj programmet.
   2. Hämta **klient-ID: t** från fönstret till höger. 

      ![Skärm bild som visar "Azure A D-appen" och "hantera program" markerat och "klient I D" i den högra rutan.](./media/connect-with-rest/existing-client-id.png)

7. Hämta programmets **nyckel** (klient hemlighet). 

   1. Klicka på knappen **hantera program** (Observera att klient-ID-informationen är under **program-ID**). 
   2. Tryck på **nycklar**.
    
       ![Skärm bild som visar knappen "hantera program" markerad, "program I D" i mitten av fönstret markerat och "Keys" markerat i den högra rutan.](./media/connect-with-rest/manage-app.png)
   3. Generera appens nyckel (klient hemlighet) genom att fylla i **Beskrivning** och **upphör att gälla** och trycka på **Spara**.
    
       När knappen **Spara** har tryckts ned visas värdet nyckel. Kopiera nyckelvärdet innan du lämnar bladet.

   ![API-åtkomst](./media/connect-with-rest/connect-with-rest03.png)

Du kan lägga till värden för AD-anslutnings parametrar till din web.config eller app.config fil, för senare användning i koden.

> [!IMPORTANT]
> **Klient nyckeln** är en viktig hemlighet och bör skyddas korrekt i ett nyckel valv eller krypteras i produktionen.

## <a name="get-the-access-token-using-postman"></a>Hämta åtkomsttoken med Postman

Det här avsnittet visar hur du använder **Postman** för att köra en REST API som returnerar en JWT Bearer-token (Access-token). Om du vill anropa Media Services REST API måste du lägga till rubriken "Authorization" till anropen och lägga till värdet "Bearer- *your_access_token*" i varje anrop (se nästa avsnitt i den här självstudien). 

1. Öppna **Postman**.
2. Välj **POST**.
3. Ange den URL som innehåller klient namnet i följande format: klient namnet måste sluta med **. onmicrosoft.com** och URL: en ska sluta med **OAuth2/token**: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Välj fliken **sidhuvud** .
5. Ange **rubrik** informationen med hjälp av data rutnätet "nyckel/värde". 

    ![Skärm bild som visar fliken "sidhuvud" och åtgärden "massredigera" vald.](./media/connect-with-rest/headers-data-grid.png)

    Du kan också klicka på **massredigera** länkar till höger om fönstret Postman och klistra in följande kod.

    ```javascript
    Content-Type:application/x-www-form-urlencoded
    Keep-Alive:true
    ```

6. Tryck på fliken **brödtext** .
7. Ange information om brödtext med hjälp av data rutnätet "nyckel/värde" (Ersätt klient-ID och hemliga värden). 

    ![Data rutnät](./media/connect-with-rest/data-grid.png)

    Du kan också klicka på **Mass redigering** till höger om fönstret Postman och klistra in följande text (Ersätt klient-ID och hemliga värden):

    ```javascript
    grant_type:client_credentials
    client_id:{Your Client ID that you got from your Azure AD Application}
    client_secret:{Your client secret that you got from your Azure AD Application's Keys}
    resource:https://rest.media.azure.net
    ```

8. Tryck på **Skicka**.

    ![Skärm bild som visar flikarna "post", "sidhuvud" och "Body" och "access_token" markerat och "Skicka"-knappen har identifierats.](./media/connect-with-rest/connect-with-rest04.png)

Det returnerade svaret innehåller den **åtkomsttoken** som du behöver använda för att få åtkomst till alla AMS-API: er.

## <a name="test-the-assets-api-using-the-access-token"></a>Testa **till gångs** -API: et med hjälp av åtkomsttoken

Det här avsnittet visar hur du kommer åt **till gångs** -API: et med **Postman**.

1. Öppna **Postman**.
2. Välj **GET**.
3. Klistra in REST API slut punkten (till exempel https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Välj fliken **auktorisering** . 
5. Välj **Bearer-token**.
6. Klistra in token som skapades i föregående avsnitt.

    ![Hämta token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX kan skilja sig mellan en Mac och en dator. Om Mac-versionen inte har alternativet "Bearer token" i list rutan för **autentiserings** avsnitt, ska du lägga till **Authorization** -huvudet manuellt på Mac-klienten.

   ![Auth-huvud](./media/connect-with-rest/auth-header.png)

7. Välj **sidhuvud**.
5. Klicka på **Mass redigerings** länk till höger i fönstret Postman.
6. Klistra in följande rubriker:

    ```javascript
    x-ms-version:2.19
    Accept:application/json
    Content-Type:application/json
    DataServiceVersion:3.0
    MaxDataServiceVersion:3.0
    ```

7. Tryck på **Skicka**.

Det returnerade svaret innehåller de till gångar som finns i ditt konto.

## <a name="next-steps"></a>Nästa steg

* Testa den här exempel koden i [Azure AD-autentisering för Azure Media Services åtkomst: både via REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Ladda upp filer med .NET](media-services-dotnet-upload-files.md)
