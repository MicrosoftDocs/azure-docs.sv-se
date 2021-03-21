---
title: Skapa en appregistrering
titleSuffix: Azure Digital Twins
description: Se så här skapar du en Azure AD-App-registrering som ett autentiseringsalternativ för klient program.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: acb5457f10c54a741a738dd8a1008e703b0f23b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051029"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Skapa en app-registrering som ska användas med Azure Digitals dubbla

När du arbetar med en digital Azure-instans, är det vanligt att interagera med den instansen via klient program, till exempel en anpassad app för klienter eller ett exempel som [Azure Digitals Internet Explorer](quickstart-adt-explorer.md). Dessa program behöver autentiseras med Azures digitala dubblare för att kunna interagera med det, och vissa av [de autentiseringsmekanismer](how-to-authenticate-client.md) som appar kan använda inbegriper en [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) -app- **registrering**.

Detta krävs inte för alla autentiserings scenarier. Men om du använder en strategi för autentisering eller kod som kräver en registrering av appen, inklusive ett **klient-ID** och klient **-ID**, visar den här artikeln hur du konfigurerar en.

## <a name="using-azure-ad-app-registrations"></a>Använda Azure AD App-registreringar

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) är Microsofts molnbaserade identitets-och åtkomst hanterings tjänst. Att konfigurera en **app-registrering** i Azure AD är ett sätt att ge en klient åtkomst till Azure Digitals.

Den här appens registrering är den plats där du konfigurerar åtkomst behörigheter till [Azure Digitals dubbla API: er](how-to-use-apis-sdks.md). Senare kan klient-appar autentisera mot registrerings appen med hjälp av registreringens **klient-och klient-ID-värden**, och därför beviljas de konfigurerade åtkomst behörigheterna till API: erna.

>[!TIP]
> Du kanske föredrar att ställa in en ny app-registrering varje gång du behöver en, *eller* för att göra detta bara en gång, och upprätta en enda app-registrering som delas mellan alla scenarier som kräver det.

## <a name="create-the-registration"></a>Skapa registreringen

Börja med att navigera till [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) i Azure Portal (du kan använda den här länken eller hitta den med Portal Sök fältet). Välj *Appregistreringar* på menyn tjänst och sedan *+ ny registrering*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Vy av sidan Azure AD-tjänst i Azure Portal, markera meny alternativet &quot;Appregistreringar&quot; och knappen &quot;+ ny registrering&quot;":::

På sidan *Registrera en program* som följer fyller du i de begärda värdena:
* **Namn**: ett visnings namn för Azure AD-program som ska associeras med registreringen
* **Konto typer som stöds**: Välj *konton endast i den här organisations katalogen (endast standard katalog-en klient)*
* **Omdirigerings-URI**: en *Azure AD Application svars-URL* för Azure AD-programmet. Lägg till en *offentlig klient/ursprunglig (mobile & Desktop)* URI för `http://localhost` .

Tryck på knappen *Registrera* när du är färdig.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Vy av sidan Registrera ett program med de beskrivna värdena ifyllda i":::

När registreringen är färdig kommer portalen att omdirigera dig till sidan med information.

## <a name="collect-client-id-and-tenant-id"></a>Samla in klient-ID och klient-ID

Sedan samlar du in några viktiga värden om appens registrering från informations sidan:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Portal visning av viktiga värden för appens registrering":::

Anteckna _**program**_ -ID och _**katalog (klient)-ID: t**_ som **visas på sidan** . Det här är de värden som en klient app måste använda för att autentisera med Azure Digital-dubbla.

## <a name="provide-azure-digital-twins-api-permission"></a>Ge Azure Digitals dubbla API-behörighet

Konfigurera sedan den app-registrering som du har skapat med baseline-behörigheter till Azures digitala dubbla API: er.

Välj *API-behörigheter* på menyn på Portal sidan för din app-registrering. På sidan följande behörigheter trycker du på knappen *+ Lägg till en behörighet* .

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Vy av appens registrering i Azure Portal, markera meny alternativet API-behörigheter och knappen + Lägg till en behörighet":::

På sidan *begär API-behörigheter* som följer växlar du till fliken *API: er som min organisation använder* och söker efter *Azure Digital-dubbla*. Välj _**Azure Digitals flätar**_ från Sök resultaten om du vill fortsätta med att tilldela behörigheter för Azures digitala dubbla API: er.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Visning av sid Sök resultatet &quot;begär API-behörigheter&quot; som visar Azure Digitals, med ett program (klient) ID för 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Om prenumerationen fortfarande har en befintlig Azure Digital-instans från den tidigare offentliga för hands versionen av tjänsten (före den 2020 juli) måste du söka efter och välja _**Azure Smart Spaces-tjänst**_ i stället. Detta är ett äldre namn för samma uppsättning API: er (Observera att *program-ID: t (klient) ID* är detsamma som i skärm bilden ovan) och att din upplevelse inte ändras utanför det här steget.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Vy av Sök resultatet för sidan begär API-behörigheter som visar Azure Smart Spaces-tjänsten":::

Sedan väljer du vilka behörigheter som ska beviljas för dessa API: er. Expandera **Läs-(1)-** behörigheten och markera kryss rutan med texten *Read. Write* för att ge appen registrerings läsare och skrivar behörigheter.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Vy av sidan begär API-behörigheter väljer Läs. Skriv-behörigheter för Azure Digitals dubbla API: er":::

Tryck på *Lägg till behörigheter* när du är färdig.

### <a name="verify-success"></a>Verifieringen lyckades

På sidan *API-behörigheter* kontrollerar du att det nu finns en post för Azure Digitals dubbla som återspeglar Läs-/skriv behörigheter:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Portal visning av API-behörigheterna för Azure AD-appens registrering, med Läs-/skriv åtkomst för Azure digitala dubbla":::

Du kan också kontrol lera anslutningen till Azure Digitals dubbla i program registreringens *manifest.jspå*, som automatiskt uppdaterades med den digitala Azure-informations informationen när du lade till API-behörigheterna.

Om du vill göra det väljer du *manifest* på menyn för att visa appens registrerings manifest kod. Bläddra till slutet av kod fönstret och leta efter dessa fält under `requiredResourceAccess` . Värdena ska matcha dem i skärm bilden nedan:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Portal visning av manifestet för Azure AD-appens registrering. Kapslad under ' requiredResourceAccess ', ett ' resourceAppId '-värde för 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 och ett ' resourceAccess >-ID '-värde för 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Om dessa värden saknas, gör om stegen i [avsnittet för att lägga till API-behörigheten](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Andra möjliga steg för din organisation

Det är möjligt att organisationen kräver ytterligare åtgärder från prenumerations ägare/-administratörer för att kunna konfigurera en app-registrering. Vilka steg som krävs kan variera beroende på organisationens aktuella inställningar.

Här följer några vanliga potentiella aktiviteter som en ägare/administratör för prenumerationen kan behöva utföra. Dessa och andra åtgärder kan utföras från sidan [*Azure AD App registreringar*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i Azure Portal.
* Bevilja administratörsmedgivande för appregistreringen. Din organisation kan ha ett *administrativt medgivande som måste* aktive ras globalt i Azure AD för alla app-registreringar i din prenumeration. I så fall måste ägaren/administratören välja den här knappen för ditt företag på registrerings sidan för appens *API-behörigheter* för att appen ska vara giltig:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Portal visning av knappen bevilja administrativt medgivande under API-behörigheter":::
  - Om medgivande har beviljats, bör posten för Azure Digital-dubbla, Visa ett *status* värde för _beviljat **(ditt företag)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Portal visning av administrativt medgivande som beviljats för företaget under API-behörigheter":::
* Aktivera offentlig klient åtkomst
* Ange vissa svars-URL: er för webb-och skriv bords åtkomst
* Tillåt för implicita OAuth2-autentiserings flöden

Mer information om registrering av appar och dess olika installations alternativ finns i [*Registrera ett program med Microsoft Identity Platform*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapar du en Azure AD-App-registrering som kan användas för att autentisera klient program med Azure Digitals dubbla API: er.

Läs sedan om autentiseringsmekanismer, inklusive en som använder app-registreringar och andra som inte:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)