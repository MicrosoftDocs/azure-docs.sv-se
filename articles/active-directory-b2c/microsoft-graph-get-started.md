---
title: Registrera ett Microsoft Graph-program
titleSuffix: Azure AD B2C
description: Förbered för att hantera Azure AD B2C resurser med Microsoft Graph genom att registrera ett program som har beviljats nödvändiga Graph API behörigheter.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0dcb959184e12ffa22ae25443087684123598e47
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382477"
---
# <a name="register-a-microsoft-graph-application"></a>Registrera ett Microsoft Graph-program

Med [Microsoft Graph][ms-graph] kan du hantera många av resurserna i Azure AD B2C-klienten, inklusive kund användar konton och anpassade principer. Genom att skriva skript eller program som anropar [Microsoft Graph API][ms-graph-api], kan du automatisera klient hanterings uppgifter som:

* Migrera ett befintligt användar arkiv till en Azure AD B2C-klient
* Distribuera anpassade principer med en Azure-pipeline i Azure DevOps och hantera anpassade princip nycklar
* Registrera värd användare på din sida och skapa användar konton i din Azure AD B2C katalog bakom kulisserna
* Automatisera program registrering
* Hämta gransknings loggar

Följande avsnitt hjälper dig att förbereda för att använda Microsoft Graph API för att automatisera hanteringen av resurser i din Azure AD B2Cs katalog.

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API-interaktions lägen

Det finns två kommunikations lägen som du kan använda när du arbetar med Microsoft Graph-API: et för att hantera resurser i Azure AD B2C klient organisationen:

* **Interaktiv** – lämpligt för körnings aktiviteter genom att använda ett administratörs konto i B2C-klienten för att utföra hanterings uppgifter. För det här läget krävs en administratör för att logga in med sina autentiseringsuppgifter innan du anropar Microsoft Graph-API: et.

* **Automatiserad** – för schemalagda eller kontinuerliga körnings aktiviteter använder den här metoden ett tjänst konto som du konfigurerar med de behörigheter som krävs för att utföra hanterings uppgifter. Du skapar "tjänst konto" i Azure AD B2C genom att registrera ett program som dina program och skript använder för att autentisera med hjälp av dess *program-ID* och **OAuth 2,0** -klientautentiseringsuppgifter. I det här fallet fungerar programmet som de ska för att anropa Microsoft Graph-API: t, inte administratörs användaren som i den tidigare beskrivna interaktiva metoden.

Du aktiverar det **automatiserade** interaktions scenariot genom att skapa en program registrering som visas i följande avsnitt.

Även om OAuth 2,0-klientens autentiseringsuppgifter för tilldelning av klient inte stöds direkt av Azure AD B2C-Autentiseringstjänsten, kan du konfigurera flödet för autentiseringsuppgifter för klienten med hjälp av Azure AD och Microsoft Identity Platform/token-slutpunkten för ett program i din Azure AD B2C-klient. En Azure AD B2C-klient delar vissa funktioner med Azure AD Enterprise-klienter.

## <a name="register-management-application"></a>Registrera hanterings program

Innan dina skript och program kan interagera med [Microsoft Graph-API][ms-graph-api] för att hantera Azure AD B2C-resurser måste du skapa en program registrering i din Azure AD B2C klient som ger nödvändiga API-behörigheter.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **Appregistreringar** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *managementapp1*.
1. Välj **konton endast i den här organisations katalogen**.
1. Under **behörigheter** avmarkerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörigheter* .
1. Välj **Register** (Registrera).
1. Registrera **program-ID: t (Client)** som visas på program översikts sidan. Du använder det här värdet i ett senare steg.

## <a name="grant-api-access"></a>Bevilja API-åtkomst

För att ditt program ska kunna komma åt data i Microsoft Graph ger du det registrerade programmet relevanta [program behörigheter](https://docs.microsoft.com/graph/permissions-reference). Programmets gällande behörigheter är den fullständiga behörighets nivån som anges av behörigheten. Om du till exempel vill *skapa*, *läsa*, *Uppdatera* och *ta bort* alla användare i Azure AD B2C-klienten, lägger du till **användaren. readwrite. all** behörighet. 

> [!NOTE]
> **Användaren. readwrite. all** behörighet inkluderar inte möjligheten att uppdatera användar kontots lösen ord. Om ditt program behöver uppdatera användar kontots lösen ord, [tilldelar du rollen som användar administratör](#optional-grant-user-administrator-role). När du beviljar rollen som [användar administratör](../active-directory/roles/permissions-reference.md#user-administrator) krävs inte **User. readwrite. all** . Rollen användar administratör innehåller allt som behövs för att hantera användare.

Du kan ge ditt program flera program behörigheter. Om ditt program till exempel även behöver hantera grupper i Azure AD B2C-klienten, lägger du till **gruppen. readwrite. all** behörighet också. 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>Valfritt Tilldela rollen som användar administratör

Om programmet eller skriptet behöver uppdatera användarnas lösen ord måste du tilldela rollen som *användar administratör* till ditt program. Rollen [användar administratör](../active-directory/roles/permissions-reference.md#user-administrator) har en fast uppsättning behörigheter som du beviljar ditt program. 

Följ dessa steg om du vill lägga till rollen som *användar administratör* :

1. Logga in på [Azure Portal](https://portal.azure.com) och använd filtret för **katalog + prenumeration** för att växla till Azure AD B2C klienten.
1. Sök efter och välj **Azure AD B2C**.
1. Under **Hantera** väljer du **roller och administratörer**.
1. Välj rollen **användar administratör** . 
1. Välj **Lägg till tilldelningar**.
1. I rutan **Välj** text anger du namnet eller ID: t för programmet som du registrerade tidigare, till exempel *managementapp1*. När den visas i Sök resultaten väljer du ditt program.
1. Välj **Lägg till**. Det kan ta några minuter innan behörigheterna är fullständigt spridda.

## <a name="create-client-secret"></a>Skapa klient hemlighet

Ditt program behöver en klient hemlighet för att bevisa sin identitet när en token begärs. Följ dessa steg om du vill lägga till klient hemligheten:

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>Nästa steg

Nu när du har registrerat ditt hanterings program och har beviljat den nödvändiga behörigheten kan dina program och tjänster (till exempel Azure-pipeliner) använda sina autentiseringsuppgifter och behörigheter för att interagera med Microsoft Graph-API: et. 

* [Hämta en åtkomsttoken från Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Använd åtkomsttoken för att anropa Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [B2C-åtgärder som stöds av Microsoft Graph](microsoft-graph-operations.md)
* [Hantera Azure AD B2C användar konton med Microsoft Graph](microsoft-graph-operations.md)
* [Hämta gransknings loggar med Azure AD repor ting API](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
