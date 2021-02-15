---
title: 'Snabb start: Konfigurera en app för att få åtkomst till ett webb-API | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten konfigurerar du en app-registrering som representerar ett webb-API i Microsoft Identity Platform för att aktivera begränsad resurs åtkomst (behörigheter) till klient program.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: bd6acc5d3b7cd6e88b56e4a922e777adca031538
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102571"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Snabb start: Konfigurera ett klient program för att få åtkomst till ett webb-API

I den här snabb starten tillhandahåller du en klient app som registrerats med Microsoft Identity Platform med begränsad, behörighets-baserad åtkomst till ditt eget webb-API. Du ger också klient programmet åtkomst till Microsoft Graph.

Genom att ange ett webb-API: s omfång i klient appens registrering, kan klient appen Hämta en åtkomsttoken som innehåller dessa omfattningar från Microsoft Identity Platform. I sin kod kan webb-API: et tillhandahålla behörighets-baserad åtkomst till sina resurser baserat på de omfattningar som finns i åtkomsttoken.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration – [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Slut för ande av [snabb start: registrera ett program](quickstart-register-app.md)
* Slut för ande av [snabb start: Konfigurera ett program för att exponera ett webb-API](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Lägg till behörigheter för åtkomst till ditt webb-API

I det första scenariot beviljar du en klient program åtkomst till ditt eget webb-API, som du bör ha registrerat som en del av kraven. Om du inte har både en klient app och ett webb-API registrerat, slutför du stegen i de två [krav](#prerequisites) artiklarna.

Det här diagrammet visar hur de två app-registreringarna relaterar till varandra. I det här avsnittet lägger du till behörigheter till klient appens registrering.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Linje diagram som visar ett webb-API med exponerade omfattningar till höger och en klient app till vänster med de omfattningar som har valts som behörigheter" border="false":::

När du har registrerat både din klient app och ditt webb-API och har exponerat API genom att skapa omfång, kan du konfigurera klientens behörigheter till API: et genom att följa dessa steg:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: på den översta menyn och väljer den klient som innehåller klient appens registrering.
1. Välj **Azure Active Directory**  >  **Appregistreringar** och välj sedan ditt klient program (*inte* ditt webb-API).
1. Välj **API-behörigheter**  >  **Lägg till en behörighet**  >  **Mina API: er**.
1. Välj det webb-API som du registrerade som en del av förutsättningarna.

    **Delegerade behörigheter** är markerad som standard. Delegerade behörigheter är lämpliga för klient program som har åtkomst till ett webb-API som inloggad användare och vars åtkomst ska begränsas till de behörigheter som du väljer i nästa steg. Lämna **delegerade behörigheter** markerade för det här exemplet.

    **Program behörigheter** är för tjänst-eller daemon-typ av program som behöver åtkomst till ett webb-API som själva, utan användar interaktion för inloggning eller medgivande. Om du inte har definierat program roller för ditt webb-API är det här alternativet inaktiverat.
1. Under **Välj behörigheter** expanderar du den resurs vars omfång du har definierat för ditt webb-API och väljer de behörigheter som klient programmet ska ha för den inloggade användarens räkning.

    Om du har använt exempel omfångs namnen som angavs i föregående snabb start bör du se **anställda. Read. all** och **anställda. Write. all**.
    Välj **anställda. Läs. alla** eller en annan behörighet som du kan ha skapat när du slutfört kraven.
1. Välj **Lägg till behörigheter** för att slutföra processen.

När du har lagt till behörigheter till ditt API bör du se de valda behörigheterna under **konfigurerade behörigheter**. Följande bild visar exempel *anställda. Read. alla* delegerade behörigheter som har lagts till i klient appens registrering.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Fönstret konfigurerade behörigheter i Azure Portal visar den nyligen tillagda behörigheten":::

Du kan också se att *användaren. Läs* -behörighet för Microsoft Graph-API: et. Den här behörigheten läggs till automatiskt när du registrerar en app i Azure Portal.

## <a name="add-permissions-to-access-microsoft-graph"></a>Lägg till behörigheter för åtkomst Microsoft Graph

Förutom att få åtkomst till ditt eget webb-API å den inloggade användarens vägnar kan ditt program också behöva komma åt eller ändra användarens (eller andra) data som lagras i Microsoft Graph. Eller så kanske du har en tjänst eller daemon-app som behöver ha åtkomst Microsoft Graph som de ska utföra åtgärder utan någon användar interaktion.

### <a name="delegated-permission-to-microsoft-graph"></a>Delegerad behörighet till Microsoft Graph

Konfigurera delegerad behörighet till Microsoft Graph så att ditt klient program kan utföra åtgärder för den inloggade användarens räkning, t. ex. läsa e-postmeddelandet eller ändra deras profil. Som standard uppmanas användare av klient programmet när de loggar in att godkänna de delegerade behörigheter som du har konfigurerat för den.

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: på den översta menyn och väljer den klient som innehåller klient appens registrering.
1. Välj **Azure Active Directory**  >  **Appregistreringar** och välj sedan ditt klient program.
1. Välj **API-behörigheter**  >  **Lägg till en behörighets**  >  **Microsoft Graph**
1. Välj **delegerade behörigheter**. Microsoft Graph visar många behörigheter, med de vanligaste som visas överst i listan.
1. Under **Välj behörigheter** väljer du följande behörigheter:

    | Behörighet       | Description                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Visa användarnas e-postadress                           |
    | `offline_access` | Behåll åtkomst till data som du har gett dem åtkomst till |
    | `openid`         | Logga in användare                                       |
    | `profile`        | Visa användarnas grundläggande profil                           |
1. Välj **Lägg till behörigheter** för att slutföra processen.

När du konfigurerar behörigheter uppmanas användare av din app vid inloggning för sitt medgivande att tillåta att appen får åtkomst till resurs-API: et för deras räkning.

Som administratör kan du också bevilja medgivande åt *alla* användare, så att de inte uppmanas att göra det. Administratörs medgivande diskuteras senare i avsnittet [mer om API-behörigheter och administrativt medgivande](#more-on-api-permissions-and-admin-consent) i den här artikeln.

### <a name="application-permission-to-microsoft-graph"></a>Program behörighet för att Microsoft Graph

Konfigurera program behörigheter för ett program som behöver autentisera sig självt utan användar interaktion eller medgivande. Program behörigheter används vanligt vis av bakgrunds tjänster eller daemon-appar som har åtkomst till ett API på ett sätt som inte går att använda och via webb-API: er som har åtkomst till ett annat (underordnat

I följande steg beviljar du behörighet till Microsoft Graph *filer. Read. all* behörighet som exempel.

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: på den översta menyn och väljer den klient som innehåller klient appens registrering.
1. Välj **Azure Active Directory**  >  **Appregistreringar** och välj sedan ditt klient program.
1. Välj **API-behörigheter**  >  **Lägg till en behörighet**  >  **Microsoft Graph**  >  **program behörigheter**.
1. Alla behörigheter som visas av Microsoft Graph visas under **Välj behörigheter**.
1. Välj den behörighet eller de behörigheter som du vill ge ditt program. Som exempel kan du ha en daemon-app som söker igenom filer i din organisation, som aviserar om en speciell filtyp eller ett särskilt namn.

    Under **Välj behörigheter** expanderar du **filer** och väljer sedan *filerna. Read. all* behörighet.
1. Välj **Lägg till behörigheter**.

Vissa behörigheter, t. ex *. Microsoft graphs filer. Read. all* behörighet, Kräv administratörs medgivande. Du beviljar administratörs medgivande genom att välja knappen **bevilja administratörs medgivande** , som beskrivs senare i avsnittet [administrativt medgivande](#admin-consent-button) .

### <a name="configure-client-credentials"></a>Konfigurera klientautentiseringsuppgifter

Appar som använder program behörigheter autentiseras med sina egna autentiseringsuppgifter, utan att det krävs några åtgärder från användaren. Innan ditt program (eller API) kan komma åt Microsoft Graph, ditt eget webb-API eller något annat API genom att använda program behörigheter måste du konfigurera klient appens autentiseringsuppgifter.

Mer information om hur du konfigurerar appens autentiseringsuppgifter finns i avsnittet [Lägg till autentiseringsuppgifter](quickstart-register-app.md#add-credentials) i [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>Mer om API-behörigheter och administrativt medgivande

Rutan **API-behörigheter** för en app-registrering innehåller en [konfigurerad behörighets](#configured-permissions) tabell och kan också innehålla en [annan behörighet som är beviljad](#other-permissions-granted) tabell. Både tabeller och [knappen administrativt medgivande](#admin-consent-button) beskrivs i följande avsnitt.

### <a name="configured-permissions"></a>Konfigurerade behörigheter

Den **konfigurerade behörighets** tabellen i rutan **API-behörigheter** visar en lista över behörigheter som programmet kräver för grundläggande åtgärd – rra-listan ( *nödvändig resurs åtkomst* ). Användare eller deras administratörer måste samtycka till dessa behörigheter innan de kan använda appen. Andra, valfria behörigheter kan begäras senare vid körning (med dynamiskt medgivande).

Detta är den minsta listan med behörigheter som användarna måste godkänna för din app. Det kan finnas mer, men de kommer alltid att krävas. För säkerhet och för att hjälpa användare och administratörer att känna sig bekvämare med din app, behöver du aldrig fråga efter vad du inte behöver.

Du kan lägga till eller ta bort behörigheterna som visas i den här tabellen med hjälp av stegen som beskrivs ovan eller från [andra behörigheter som beviljats](#other-permissions-granted) (beskrivs i nästa avsnitt). Som administratör kan du bevilja administrativt medgivande för den fullständiga uppsättningen med API: er behörigheter som visas i tabellen och återkalla medgivande för enskilda behörigheter.

### <a name="other-permissions-granted"></a>Andra behörigheter som har beviljats

Du kan också se en tabell med **andra behörigheter som har beviljats för {din klient}** i rutan **API-behörigheter** . De **andra behörigheterna för {din klient}** -tabell visar behörigheter som har beviljats för klienten som inte har kon figurer ATS uttryckligen för programobjektet. Dessa behörigheter begärdes dynamiskt och samtyckde till. Det här avsnittet visas bara om det finns minst en behörighet som gäller.

Du kan lägga till en fullständig uppsättning behörigheter för ett API eller enskilda behörigheter som visas i tabellen i den **konfigurerade behörighets** tabellen. Som administratör kan du återkalla administratörs medgivande för API: er eller enskilda behörigheter i det här avsnittet.

### <a name="admin-consent-button"></a>Knappen administratörs medgivande

Med knappen **bevilja administratörs medgivande för {din klient}** kan en administratör bevilja administrativt medgivande till de behörigheter som har kon figurer ATS för programmet. När du väljer knappen visas en dialog ruta där du får en förfrågan om att bekräfta medgivande åtgärden.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Knappen bevilja administratörs medgivande markerat i fönstret konfigurerade behörigheter i Azure Portal":::

När godkännandet har beviljats visas de behörigheter som krävde administratörs medgivande har beviljats:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Konfigurera behörighets tabellen i Azure Portal visar administrativt godkännande beviljat för filerna. Read. all behörighet":::

Knappen **bevilja administratörs medgivande** är *inaktive rad* om du inte är administratör eller om inga behörigheter har kon figurer ATS för programmet. Om du har behörighet som har beviljats men ännu inte har kon figurer ATS, kommer du att be dig att hantera behörigheterna med knappen administratörs medgivande. Du kan lägga till dem i konfigurerade behörigheter eller ta bort dem.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa snabb start i serien och lär dig hur du konfigurerar vilka konto typer som har åtkomst till ditt program. Du kanske till exempel vill begränsa åtkomsten enbart till de användare i din organisation (en enskild klient) eller tillåta användare i andra Azure AD-klienter (flera innehavare) och de med personliga Microsoft-konton (MSA).

> [!div class="nextstepaction"]
> [Ändra konton som stöds av en app](quickstart-modify-supported-accounts.md)