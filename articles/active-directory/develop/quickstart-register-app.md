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
ms.openlocfilehash: 4e9e6c9179c7fbf405cf8fc661a209da3c9d3b61
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031274"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snabb start: registrera ett program med Microsoft Identity Platform

I den här snabb starten registrerar du en app i Azure Portal så att Microsoft Identity Platform kan tillhandahålla autentiserings-och auktoriserings tjänster för ditt program och dess användare.

Varje program som du vill att Microsoft Identity Platform ska utföra för att utföra identitets-och åtkomst hantering (IAM) måste registreras. Oavsett om det är ett klient program, t. ex. en webb-eller mobilapp, eller om det är ett webb-API som säkerhetskopierar en klient app, upprättar en förtroende relation mellan ditt program och identitets leverantören, Microsoft Identity Platform.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration – [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Slut för ande av [snabb start: Konfigurera en klient](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Registrera ett program

När du registrerar programmet upprättas en förtroende relation mellan appen och Microsoft Identity Platform. Förtroendet är enkelriktat: appen litar på Microsoft Identity Platform och inte på det andra sättet.

Följ de här stegen för att skapa appens registrering:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett **namn** för ditt program. Användare av appen kan se det här namnet och du kan ändra det senare.
1. Ange vem som kan använda programmet, ibland kallat *inloggnings mål gruppen*.

    | Kontotyper som stöds | Description |
    |-------------------------|-------------|
    | **Endast konton i den här organisationskatalogen** | Välj det här alternativet om du vill skapa ett program för användning endast av användare (eller gäster) i *din* klient organisation.<br><br>Detta är ett program med en **enda klient organisation** i Microsoft Identity Platform, som ofta kallas LOB-program ( *line-of-Business* ). |
    | **Konton i valfri organisationskatalog** | Välj det här alternativet om du vill att användare i *en* Azure AD-klient ska kunna använda ditt program. Det här alternativet är lämpligt om du till exempel skapar ett SaaS-program (program vara som en tjänst) som du vill ge till flera organisationer.<br><br>Detta kallas för ett program **med flera klienter** i Microsoft Identity Platform. |
    | **Konton i en valfri organisationskatalog och personliga Microsoft-konton** | Välj det här alternativet om målgruppen är bredast möjliga uppsättning av kunder.<br><br>Genom att välja det här alternativet registrerar du ett program för **flera klienter** som också har stöd för användare med personliga **Microsoft-konton** (MSA). |
    | **Personliga Microsoft-konton** | Välj det här alternativet om du vill skapa ett program för användning endast av användare med personliga Microsoft-konton. Personliga Microsoft-konton är Skype-, Xbox-, Live-och Hotmail-konton. |

1. Ange inget för **omdirigerings-URI (valfritt)** konfigurera en i nästa avsnitt.
1. Välj **Registrera** för att slutföra den inledande registreringen av appen.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Skärm bild av Azure Portal i en webbläsare som visar fönstret registrera ett program.":::

När registreringen är klar visar Azure Portal **översikts** fönstret för appens registrering, som innehåller dess **program-ID (klient)**. Kallas även för bara *klient-ID: t* identifierar det här värdet ditt program i Microsoft Identity Platform.

Programmets kod, eller mer vanligt vis ett autentiseringspaket som används i ditt program, använder också klient-ID: t som en aspekt för att verifiera säkerhetstoken som tas emot från identitets plattformen.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Skärm bild av Azure Portal i en webbläsare som visar översikts fönstret i en app-registrering.":::

## <a name="add-a-redirect-uri"></a>Lägg till en omdirigerings-URI

En omdirigerings-URI är den plats där Microsoft Identity Platform omdirigerar en användares klient och skickar säkerhetstoken efter autentisering.

I en produktions webb applikation är exempelvis omdirigerings-URI ofta en offentlig slut punkt där appen körs, t. ex `https://contoso.com/auth-response` .. Under utvecklingen är det vanligt att även lägga till slut punkten där du kör appen lokalt, till exempel `https://127.0.0.1/auth-response` eller `http://localhost/auth-response` .

Du lägger till och ändrar omdirigerings-URI: er för dina registrerade program genom att konfigurera deras [plattforms inställningar](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Konfigurera plattforms inställningar

Inställningar för varje program typ, inklusive omdirigerings-URI: er, konfigureras i **plattforms konfigurationerna** i Azure Portal. Vissa plattformar, t. ex. **webb** -och **Enkels Ides program**, kräver att du manuellt anger en omdirigerings-URI. För andra plattformar som mobil och stationär kan du välja att omdirigerings-URI: er som genereras åt dig när du konfigurerar de andra inställningarna.

Konfigurera program inställningar baserat på den plattform eller enhet som du riktar in dig på:

1. Välj ditt program i **Appregistreringar** i Azure Portal.
1. Under **Hantera** väljer du **autentisering**.
1. Under **plattforms konfiguration** väljer du **Lägg till en plattform**.
1. I **Konfigurera plattformar** väljer du panelen för din program typ (plattform) för att konfigurera dess inställningar.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Skärm bild av fönstret plattforms konfiguration i Azure Portal" border="false":::

    | Plattform | Konfigurationsinställningar |
    | -------- | ---------------------- |
    | **Webb** | Ange en **omdirigerings-URI** för appen, platsen där Microsoft Identity Platform omdirigerar en användares klient och skickar säkerhetstoken efter autentisering.<br/><br/>Välj den här plattformen för standard webb program som körs på en server. |
    | **Enkelsidig app** | Ange en **omdirigerings-URI** för appen, platsen där Microsoft Identity Platform omdirigerar en användares klient och skickar säkerhetstoken efter autentisering.<br/><br/>Välj den här plattformen om du skapar en webbapp på klient sidan i Java Script eller med ett ramverk som vinkel, Vue.js, React.js eller blixt webb sammansättning. |
    | **iOS/macOS** | Ange **programpaket-ID: t** som finns i Xcode i *info. plist* eller build Settings.<br/><br/>En omdirigerings-URI genereras åt dig när du anger ett paket-ID. |
    | **Android** | Ange **namnet** på Appaketet, som du hittar i *AndroidManifest.xml* -filen och generera och ange **signaturens hash**.<br/><br/>En omdirigerings-URI genereras åt dig när du anger dessa inställningar. |
    | **Mobil-och skriv bords program** | Välj en av de **föreslagna omdirigerings-URI: erna** eller ange en **anpassad omdirigerings-URI**.<br/>För Skriv bords program rekommenderar vi följande:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Välj den här plattformen för mobila program som inte använder det senaste MSAL (Microsoft Authentication Library) eller som inte använder en Broker. Välj även den här plattformen för Skriv bords program. |
1. Välj **Konfigurera** för att slutföra plattforms konfigurationen.

### <a name="redirect-uri-restrictions"></a>Omdirigera URI-begränsningar

Det finns vissa begränsningar i formatet för omdirigerings-URI: er som du lägger till i en app-registrering. Mer information om dessa begränsningar finns i [omdirigerings-URI (svars-URL) begränsningar och begränsningar](reply-url.md).

## <a name="add-credentials"></a>Lägg till autentiseringsuppgifter

Autentiseringsuppgifter används av konfidentiella klient program som har åtkomst till ett webb-API. Exempel på konfidentiella klienter är Web Apps, andra webb-API: er eller program för tjänst-och bakgrunds program. Autentiseringsuppgifterna gör att ditt program kan autentisera sig självt, vilket kräver ingen interaktion från en användare vid körning.

Du kan lägga till både certifikat och klient hemligheter (en sträng) som autentiseringsuppgifter för den konfidentiella registreringen av klient programmet.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Skärm bild av Azure Portal som visar fönstret certifikat och hemligheter i en app-registrering":::

### <a name="add-a-certificate"></a>Lägg till ett certifikat

Ibland kallas certifikat för en *offentlig nyckel* som den rekommenderade typen av autentiseringsuppgifter eftersom de ger en högre säkerhets nivå än en klient hemlighet.

1. Välj ditt program i **Appregistreringar** i Azure Portal.
1. Välj **certifikat & hemligheter**  >  **Ladda upp certifikat**.
1. Välj den fil som du vill ladda upp. Den måste vara någon av följande filtyper: .cer, .pem eller .crt.
1. Välj **Lägg till**.

### <a name="add-a-client-secret"></a>Lägg till en klient hemlighet

Klient hemligheten, även kallat ett *program lösen ord*, är ett sträng värde som din app kan använda i stället för ett certifikat för att identifiera sig själv. Det är enklare att använda de två typerna av autentiseringsuppgifter som används och används ofta under utveckling, men anses vara mindre säkra än ett certifikat. Du bör använda certifikat i dina program som körs i produktion.

1. Välj ditt program i **Appregistreringar** i Azure Portal.
1. Välj **certifikat & hemligheter**  >   **ny klient hemlighet**.
1. Lägg till en beskrivning för din klienthemlighet.
1. Välj en varaktighet.
1. Välj **Lägg till**.
1. **Registrera hemlighetens värde** för användning i klient program koden – det *visas aldrig igen* när du lämnar den här sidan.

## <a name="next-steps"></a>Nästa steg

Klient program behöver vanligt vis åtkomst till resurser i ett webb-API. Förutom att skydda ditt klient program med Microsoft Identity Platform kan du använda plattformen för auktorisering av begränsad åtkomst till ditt webb-API.

Gå vidare till nästa snabb start i serien för att skapa en annan app-registrering för ditt webb-API och exponera dess omfång.

> [!div class="nextstepaction"]
> [Konfigurera ett program för att exponera ett webb-API](quickstart-configure-app-expose-web-apis.md)
