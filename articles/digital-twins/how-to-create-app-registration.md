---
title: Skapa en appregistrering
titleSuffix: Azure Digital Twins
description: Se hur du skapar en Azure AD-appregistrering som ett autentiseringsalternativ för klientappar.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bd45bb264f8e29a2aad870a7daff45fdd44e0d3c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478819"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Skapa en appregistrering som ska användas med Azure Digital Twins

När du arbetar med en Azure Digital Twins-instans är det vanligt att interagera med den instansen via klientprogram, till exempel en anpassad klientapp eller ett [exempel som Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md). Dessa program måste autentisera med Azure Digital Twins för att kunna interagera med [](how-to-authenticate-client.md) den, och vissa av de autentiseringsmekanismer som appar kan använda omfattar [en appregistrering för Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md) 

Detta krävs inte för alla autentiseringsscenarier. Men om du använder en autentiseringsstrategi eller ett kodexempel som kräver en appregistrering, inklusive ett **klient-ID** och **klient-ID**, visar den här artikeln hur du ställer in ett.

## <a name="using-azure-ad-app-registrations"></a>Använda Azure AD-appregistreringar

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) är Microsofts molnbaserade tjänst för identitets- och åtkomsthantering. Att konfigurera en **appregistrering** i Azure AD är ett sätt att ge en klientapp åtkomst till Azure Digital Twins.

I den här appregistreringen konfigurerar du åtkomstbehörigheter till [Azure Digital Twins API:er](how-to-use-apis-sdks.md). Senare kan klientappar autentisera mot appregistreringen med hjälp av registreringens klient- och klient-ID-värden, och därför beviljas de konfigurerade åtkomstbehörigheterna till API:erna.

>[!TIP]
> Du kanske föredrar att konfigurera en ny appregistrering varje gång du behöver *en,* eller bara göra detta en gång, och upprätta en enda appregistrering som ska delas mellan alla scenarier som kräver det.

## <a name="create-the-registration"></a>Skapa registreringen

Börja genom att gå [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) i Azure Portal (du kan använda den här länken eller hitta den i portalens sökfält). Välj *Appregistreringar* på tjänstmenyn och sedan *+ Ny registrering.*

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Vy över Azure AD-tjänstsidan i Azure Portal med menyalternativet &quot;Appregistreringar&quot; och knappen &quot;+ Ny registrering&quot;":::

På sidan *Registrera ett program* som följer fyller du i de begärda värdena:
* **Namn:** Ett visningsnamn för Ett Azure AD-program som ska associeras med registreringen
* **Kontotyper som stöds:** *Välj Endast konton i den här organisationskatalogen (endast standardkatalog – enskild klient)*
* **Omdirigerings-URI:** *En svars-URL för Azure AD-programmet.* Lägg till *en offentlig klient-/intern URI (& desktop)* för `http://localhost` .

När du är klar trycker du på *knappen* Registrera.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Vy över sidan Registrera ett program med de beskrivna värdena ifyllda":::

När registreringen är klar omdirigeras du till informationssidan på portalen.

## <a name="collect-client-id-and-tenant-id"></a>Samla in klient-ID och klient-ID

Samla sedan in några viktiga värden om appregistreringen från dess informationssida:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Portalvy över viktiga värden för appregistreringen":::

Anteckna _**program-ID(klient) och**_ _**katalog-ID (klient)**_ som visas **på din** sida. Det här är de värden som en klientapp måste använda den här registreringen för att autentisera med Azure Digital Twins.

## <a name="provide-azure-digital-twins-api-permission"></a>Ange Azure Digital Twins API-behörighet

Konfigurera sedan appregistreringen som du har skapat med baslinjebehörigheter till Azure Digital Twins API:er.

På portalsidan för din appregistrering väljer du *API-behörigheter* på menyn. På följande behörighetssida trycker du på *knappen + Lägg till en* behörighet.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Vy över appregistreringen i Azure Portal med menyalternativet API-behörigheter och knappen &quot;+ Lägg till en behörighet&quot;":::

På sidan *Begär API-behörigheter* växlar du till de API:er *som min organisation använder* och söker efter Azure Digital *Twins*. Välj _**Azure Digital Twins**_ sökresultaten för att fortsätta med att tilldela behörigheter för Azure Digital Twins API:er.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Vy över sökresultatet &quot;Begär API-behörigheter&quot; som visar Azure Digital Twins, med program-ID (klient) ID 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Om din prenumeration fortfarande har en befintlig Azure Digital Twins-instans från den tidigare offentliga förhandsversionen av tjänsten (före juli 2020) måste du söka efter och välja _**Azure Smart Spaces-tjänsten**_ i stället. Det här är ett äldre namn för samma uppsättning API:er (observera att *program-ID (klient) är* samma som i skärmbilden ovan), och din upplevelse kommer inte att ändras utöver det här steget.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Vy över sökresultatet &quot;Begär API-behörigheter&quot; som visar Azure Smart Spaces-tjänsten":::

Därefter väljer du vilka behörigheter som ska beviljas för dessa API:er. Expandera **behörigheten Läsa (1)** och markera kryssrutan *Read.Write* för att ge den här appen läs- och skrivbehörighet för registrering.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Vy över sidan &quot;Begär API-behörigheter&quot; där behörigheter för &quot;Read.Write&quot; för de Azure Digital Twins API:erna":::

Tryck *på Lägg till behörigheter* när du är klar.

### <a name="verify-success"></a>Verifiera att det lyckades

På sidan *API-behörigheter* kontrollerar du att det nu finns en post för Azure Digital Twins återspeglar läs-/skrivbehörigheter:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Portalvy över API-behörigheter för Azure AD-appregistrering, med läs- och skrivbehörighet för Azure Digital Twins":::

Du kan också verifiera anslutningen till Azure Digital Twins i appregistreringens *manifest.jspå*, som automatiskt uppdaterades med Azure Digital Twins när du lade till API-behörigheterna.

Det gör du genom att *välja Manifest* på menyn för att visa appregistreringens manifestkod. Rulla längst ned i kodfönstret och leta efter dessa fält under `requiredResourceAccess` . Värdena ska matcha värdena i skärmbilden nedan:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Portalvy över manifestet för Azure AD-appregistreringen. Kapslad under &quot;requiredResourceAccess&quot;, det finns ett resourceAppId-värde på 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 och värdet &quot;resourceAccess > id&quot; på 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Om dessa värden saknas försöker du igen med stegen i avsnittet för [att lägga till API-behörigheten](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Andra möjliga steg för din organisation

Det är möjligt att din organisation kräver ytterligare åtgärder från prenumerationens ägare/administratörer för att kunna konfigurera en appregistrering. Vilka steg som krävs kan variera beroende på organisationens aktuella inställningar.

Här följer några vanliga potentiella aktiviteter som en ägare/administratör för prenumerationen kan behöva utföra. Dessa och andra åtgärder kan utföras från [*Azure AD App registreringssidan*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i Azure Portal.
* Bevilja administratörsmedgivande för appregistreringen. Din organisation kan ha *administratörsmedgivande krävs* globalt aktiverat i Azure AD för alla appregistreringar i din prenumeration. I så fall måste ägare/administratör välja den här knappen för ditt företag på *appregistreringens* API-behörighetssida för att appregistreringen ska vara giltig:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Portalvy av knappen Bevilja administratörsmedgivande under API-behörigheter":::
  - Om medgivande har beviljats ska posten för Azure Digital Twins visa *statusvärdet* _Beviljad för **(ditt företag)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Portalvy över det administratörsmedgivande som beviljats för företaget under API-behörigheter":::
* Aktivera offentlig klientåtkomst
* Ange specifika svars-URL:er för webb- och skrivbordsåtkomst
* Tillåt implicita OAuth2-autentiseringsflöden

Mer information om appregistrering och dess olika installationsalternativ finns i [*Registrera ett program med Microsoft Identity Platform.*](/graph/auth-register-app-v2)

## <a name="next-steps"></a>Nästa steg

I den här artikeln ställer du in en Azure AD-appregistrering som kan användas för att autentisera klientprogram med Azure Digital Twins API:er.

Läs sedan om autentiseringsmekanismer, inklusive en som använder appregistreringar och andra som inte gör det:
* [*Så här skriver du kod för appautentisering*](how-to-authenticate-client.md)