---
title: 'Snabb start: registrera en app i Microsoft Identity Platform | Azure'
description: I den här snabb starten får du lära dig hur du registrerar ett program med Microsoft Identity Platform.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 7b7adde8a6e60042bec7695e4553d6470e548316
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055696"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snabb start: registrera ett program med Microsoft Identity Platform

I den här snabb starten registrerar du en app i Azure Portal så att Microsoft Identity Platform kan tillhandahålla autentiserings-och auktoriserings tjänster för ditt program och dess användare.

Microsoft Identity Platform utför enbart identitets-och åtkomst hantering (IAM) för registrerade program. Oavsett om det är ett klient program, t. ex. en webb-eller mobilapp, eller om det är ett webb-API som säkerhetskopierar en klient app, upprättar en förtroende relation mellan ditt program och identitets leverantören, Microsoft Identity Platform.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto som har en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konfiguration av snabb start för [klient organisationen](quickstart-create-new-tenant.md) slutförs.

## <a name="register-an-application"></a>Registrera ett program

När du registrerar programmet upprättas en förtroende relation mellan appen och Microsoft Identity Platform. Förtroendet är enkelriktat: appen litar på Microsoft Identity Platform och inte på det andra sättet.

Följ de här stegen för att skapa appens registrering:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Om du har åtkomst till flera klienter går du till den översta menyn och använder filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett visnings **namn** för ditt program. Användare av programmet kan se visnings namnet när de använder appen, till exempel vid inloggning.
    Du kan när som helst ändra visnings namnet och flera registrerings program kan dela samma namn. App-registreringens automatiskt genererade program-ID (klient), inte dess visnings namn, identifierar din app unikt i identitets plattformen.
1. Ange vem som kan använda programmet, ibland kallat *inloggnings mål gruppen*.

    | Kontotyper som stöds | Beskrivning |
    |-------------------------|-------------|
    | **Endast konton i den här organisationskatalogen** | Välj det här alternativet om du vill skapa ett program för användning endast av användare (eller gäster) i *din* klient organisation.<br><br>Som ofta kallas LOB-program ( *line-of-Business* ) är den här appen ett program med en *enda klient organisation* i Microsoft Identity Platform. |
    | **Konton i valfri organisationskatalog** | Välj det här alternativet om du vill att användare i *någon* Azure Active Directory (Azure AD)-klient ska kunna använda ditt program. Det här alternativet är lämpligt om du till exempel skapar ett SaaS-program (program vara som en tjänst) som du vill ge till flera organisationer.<br><br>Den här typen av app kallas för ett program med flera *klienter* i Microsoft Identity Platform. |
    | **Konton i en valfri organisationskatalog och personliga Microsoft-konton** | Välj det här alternativet om målgruppen är bredast möjliga uppsättning av kunder.<br><br>Genom att välja det här alternativet registrerar du ett program för flera *innehavare* som också stöder användare som har personliga *Microsoft-konton*. |
    | **Personliga Microsoft-konton** | Välj det här alternativet om du bara skapar ett program för användare som har personliga Microsoft-konton. Personliga Microsoft-konton är Skype-, Xbox-, Live-och Hotmail-konton. |

1. Ange inget för **omdirigerings-URI (valfritt)**. Du konfigurerar en omdirigerings-URI i nästa avsnitt.
1. Välj **Registrera** för att slutföra den inledande registreringen av appen.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Skärm bild av Azure Portal i en webbläsare, som visar fönstret registrera ett program.":::

När registreringen är klar visar Azure Portal fönstret **Översikt** av appens registrering. Du ser **program-ID: t (klient)**. Även kallat *klient-ID*, identifierar det här värdet ditt program i Microsoft Identity Platform. 

> [!IMPORTANT]
> Nya registrerings program är dolda för användare som standard. När du är redo för användarna att se appen på [sidan Mina appar](../user-help/my-apps-portal-end-user-access.md) kan du aktivera den. Om du vill aktivera appen går du till Azure Portal navigera till **Azure Active Directory**  >  **företags program** och väljer appen. Växla visningen  av sidan Egenskaper **till användare?** till Ja.

Programmets kod, eller mer vanligt vis ett autentiseringspaket som används i ditt program, använder också klient-ID: t. ID används som en del av verifieringen av säkerhetstoken som tas emot från identitets plattformen.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Skärm bild av Azure Portal i en webbläsare, som visar översikts fönstret i en app-registrering.":::

## <a name="add-a-redirect-uri"></a>Lägg till en omdirigerings-URI

En *omdirigerings-URI* är den plats där Microsoft Identity Platform omdirigerar en användares klient och skickar säkerhetstoken efter autentisering.

I en produktions webb applikation är exempelvis omdirigerings-URI ofta en offentlig slut punkt där appen körs, t. ex `https://contoso.com/auth-response` .. Under utvecklingen är det vanligt att även lägga till slut punkten där du kör appen lokalt, till exempel `https://127.0.0.1/auth-response` eller `http://localhost/auth-response` .

Du lägger till och ändrar omdirigerings-URI: er för dina registrerade program genom att konfigurera deras [plattforms inställningar](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Konfigurera plattforms inställningar

Inställningar för varje program typ, inklusive omdirigerings-URI: er, konfigureras i **plattforms konfigurationerna** i Azure Portal. Vissa plattformar, t. ex. **webb** -och **Enkels Ides program**, kräver att du manuellt anger en omdirigerings-URI. För andra plattformar, som mobil och stationär, kan du välja mellan omdirigerings-URI: er som genereras åt dig när du konfigurerar de andra inställningarna.

Konfigurera program inställningar baserat på den plattform eller enhet som du riktar in dig på:

1. I Azure Portal väljer du ditt program i **Appregistreringar**.
1. Under **Hantera** väljer du **autentisering**.
1. Under **plattforms konfiguration** väljer du **Lägg till en plattform**.
1. Under **Konfigurera plattformar** väljer du panelen för din program typ (plattform) för att konfigurera dess inställningar.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Skärm bild av fönstret plattforms konfiguration i Azure Portal." border="false":::

    | Plattform | Konfigurationsinställningar |
    | -------- | ---------------------- |
    | **Webb** | Ange en **omdirigerings-URI** för appen. Denna URI är den plats där Microsoft Identity Platform omdirigerar en användares klient och skickar säkerhetstoken efter autentisering.<br/><br/>Välj den här plattformen för standard webb program som körs på en server. |
    | **Enkelsidig app** | Ange en **omdirigerings-URI** för appen. Denna URI är den plats där Microsoft Identity Platform omdirigerar en användares klient och skickar säkerhetstoken efter autentisering.<br/><br/>Välj den här plattformen om du skapar en webbapp på klient sidan med hjälp av Java Script eller ett ramverk som vinkel, Vue.js, React.js eller blixt webb sammansättning. |
    | **iOS/macOS** | Ange appens **paket-ID**. Hitta den i **Bygg inställningar** eller i Xcode i *info. plist*.<br/><br/>En omdirigerings-URI genereras åt dig när du anger ett **paket-ID**. |
    | **Android** | Ange namnet på appens **paket**. Hitta den i *AndroidManifest.xml* -filen. Generera och ange även **signaturens hash-värde**.<br/><br/>En omdirigerings-URI genereras åt dig när du anger dessa inställningar. |
    | **Mobil-och skriv bords program** | Välj en av de **föreslagna omdirigerings-URI: erna**. Eller ange en **anpassad omdirigerings-URI**.<br/><br/>För Skriv bords program med inbäddad webbläsare rekommenderar vi<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>För Skriv bords program med hjälp av system webbläsare rekommenderar vi<br/>`http://localhost`<br/><br/>Välj den här plattformen för mobila program som inte använder det senaste MSAL (Microsoft Authentication Library) eller som inte använder en Broker. Välj även den här plattformen för Skriv bords program. |
1. Välj **Konfigurera** för att slutföra plattforms konfigurationen.

### <a name="redirect-uri-restrictions"></a>Omdirigera URI-begränsningar

Det finns vissa begränsningar i formatet för omdirigerings-URI: er som du lägger till i en app-registrering. Mer information om dessa begränsningar finns i [omdirigerings-URI (svars-URL) begränsningar och begränsningar](reply-url.md).

## <a name="add-credentials"></a>Lägg till autentiseringsuppgifter

Autentiseringsuppgifter används av [konfidentiella klient program](msal-client-applications.md) som har åtkomst till ett webb-API. Exempel på konfidentiella klienter är [webbappar](scenario-web-app-call-api-overview.md), andra [webb-API: er](scenario-protected-web-api-overview.md)eller program med typ av program vara [och daemon-typ](scenario-daemon-overview.md). Autentiseringsuppgifterna gör att ditt program kan autentisera sig självt, vilket kräver ingen interaktion från en användare vid körning. 

Du kan lägga till både certifikat och klient hemligheter (en sträng) som autentiseringsuppgifter för den konfidentiella registreringen av klient programmet.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Skärm bild av Azure Portal, som visar fönstret certifikat och hemligheter i en app-registrering.":::

### <a name="add-a-certificate"></a>Lägg till ett certifikat

Ibland kallas en *offentlig nyckel*, ett certifikat är den rekommenderade autentiseringstypen. Den ger mer garantier än en klient hemlighet. Mer information om hur du använder ett certifikat som autentiseringsmetod i programmet finns i autentiseringsuppgifter för [Microsoft Identity Platform Application Authentication](active-directory-certificate-credentials.md).

1. I Azure Portal väljer du ditt program i **Appregistreringar**.
1. Välj **certifikat & hemligheter**  >  **Ladda upp certifikat**.
1. Välj den fil som du vill ladda upp. Det måste vara en av följande filtyper: *. cer*, *. pem*, *. CRT*.
1. Välj **Lägg till**.

### <a name="add-a-client-secret"></a>Lägg till en klient hemlighet

Klient hemligheten kallas även för ett *program lösen ord*. Det är ett sträng värde som din app kan använda i stället för ett certifikat för att identifiera sig själv. Klient hemligheten är det enklare att använda de två typerna av autentiseringsuppgifter. Den används ofta under utvecklingen, men den anses vara mindre säker än ett certifikat. Använd certifikat i dina program som körs i produktion. 

Mer information om program säkerhets rekommendationer finns i [metod tips och rekommendationer för Microsoft Identity Platform](identity-platform-integration-checklist.md#security).

1. I Azure Portal väljer du ditt program i **Appregistreringar**.
1. Välj **certifikat & hemligheter**  >   **ny klient hemlighet**.
1. Lägg till en beskrivning för din klienthemlighet.
1. Välj en varaktighet.
1. Välj **Lägg till**.
1. *Registrera hemlighetens värde* för användning i klient program koden. Detta hemliga värde *visas aldrig igen* när du lämnar den här sidan.


## <a name="next-steps"></a>Nästa steg

Klient program behöver vanligt vis åtkomst till resurser i ett webb-API. Du kan skydda ditt klient program med hjälp av Microsoft Identity Platform. Du kan också använda plattformen för att auktorisera begränsad åtkomst till ditt webb-API.

Gå till nästa snabb start i serien för att skapa en annan app-registrering för ditt webb-API och exponera dess omfång.

> [!div class="nextstepaction"]
> [Konfigurera ett program för att exponera ett webb-API](quickstart-configure-app-expose-web-apis.md)
