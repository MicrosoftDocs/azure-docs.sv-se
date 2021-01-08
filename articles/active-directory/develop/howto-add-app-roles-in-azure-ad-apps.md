---
title: Lägg till app-roller och hämta dem från en token | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du lägger till app-roller till ett program som är registrerat i Azure Active Directory, tilldelar användare och grupper till dessa roller och tar emot dem i "roles"-anspråk i token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: b2ac90334ade52d68c775d9db5a84545774f3844
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013710"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Gör så här: Lägg till app-roller i programmet och ta emot dem i token

Rollbaserad åtkomst kontroll (RBAC) är en populär mekanism för att genomdriva auktorisering i program. När du använder RBAC beviljar en administratör behörigheter till roller och inte till enskilda användare eller grupper. Administratören kan sedan tilldela roller till olika användare och grupper för att kontrol lera vem som har åtkomst till det innehåll och de funktioner som finns.

Med hjälp av RBAC med program roller och roll anspråk kan utvecklare på ett säkert sätt framtvinga auktorisering i sina appar med mindre ansträngning.

En annan metod är att använda Azure AD-grupper och grupp anspråk som visas i kod exemplet [Active-Directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) på GitHub. Azure AD-grupper och program roller är inte ömsesidigt uteslutande. de kan användas i tandem för att ge ännu bättre åtkomst kontroll.

## <a name="declare-roles-for-an-application"></a>Deklarera roller för ett program

Du definierar app-roller med hjälp av [Azure Portal](https://portal.azure.com). App-roller definieras vanligt vis på en program registrering som representerar en tjänst, app eller API. När en användare loggar in i programmet, genererar Azure AD ett `roles` anspråk för varje roll som användaren eller tjänstens huvud namn har beviljats individuellt för användaren och från deras grupp medlemskap. Detta kan användas för att implementera anspråksbaserad auktorisering. App-roller kan tilldelas [till en användare eller en grupp av användare](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app). App-roller kan också tilldelas tjänstens huvud namn för ett annat program, eller [till tjänstens huvud namn för en hanterad identitet](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md).

> [!IMPORTANT]
> För närvarande om du lägger till ett huvud namn för tjänsten i en grupp och sedan tilldelar en app-roll till gruppen, lägger inte Azure AD till `roles` anspråket för att få problem med token.

Det finns två sätt att deklarera app-roller med hjälp av Azure Portal:

* [Användar gränssnitt för app-roller](#app-roles-ui--preview) | Förhandsgranskningsvyn
* [App manifest redigeraren](#app-manifest-editor)

Antalet roller som du lägger till räknas mot program manifest gränser som tillämpas av Azure Active Directory. Mer information om dessa begränsningar finns i avsnittet  [begränsningar i manifestet](./reference-app-manifest.md#manifest-limits) för [Azure Active Directory app manifest referens](reference-app-manifest.md).

### <a name="app-roles-ui--preview"></a>Användar gränssnitt för app-roller | Förhandsgranskningsvyn

> [!IMPORTANT]
> Funktionen app roles Portal UI [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Så här skapar du en app-roll med hjälp av Azure Portal användar gränssnittet:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Välj filtret **katalog + prenumeration** i den översta menyn och välj sedan den Azure Active Directory klient som innehåller den app-registrering som du vill lägga till en app-roll för.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar** och väljer sedan det program som du vill definiera app-roller i.
1. Välj **app-roller | Förhandsgranska** och välj sedan **skapa app-roll**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="En app-registreringens fönster för app-roller i Azure Portal":::
1. I fönstret **skapa app-roll** anger du inställningarna för rollen. I tabellen som följer bilden beskrivs varje inställning och deras parametrar.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="En app-registrerings app-roller skapa kontext fönster i Azure Portal":::

    | Fält | Beskrivning | Exempel |
    |-------|-------------|---------|
    | **Visningsnamn** | Visnings namn för den app-roll som visas i upplevelserna för administratörs medgivande och app-tilldelning. Det här värdet kan innehålla blank steg. | `Survey Writer` |
    | **Tillåtna medlems typer** | Anger om den här program rollen kan tilldelas till användare, program eller både och.<br/><br/>När det är tillgängligt för `applications` visas app-roller som program behörigheter i en app-registrerings **hanterings** avsnitt > **API-behörigheter > lägg till en behörighet > mina API: er > Välj en API > program behörigheter**. | `Users/Groups` |
    | **Värde** | Anger värdet för det roll anspråk som programmet ska förvänta sig i token. Värdet ska exakt matcha den sträng som refereras i programmets kod. Värdet får inte innehålla blank steg. | `Survey.Create` |
    | **Beskrivning** | En mer detaljerad beskrivning av den app-roll som visas under admin-appens tilldelning och medgivande upplevelser. | `Writers can create surveys.` |
    | **Vill du aktivera den här program rollen?** | Anger om app-rollen är aktive rad. Avmarkera den här kryss rutan om du vill ta bort en app-roll och tillämpa ändringen innan du försöker ta bort åtgärden. | *Markerad* |

1. Tryck på **Apply** (Verkställ) för att spara ändringarna.

### <a name="app-manifest-editor"></a>App manifest redigeraren

För att lägga till roller genom att redigera manifestet direkt:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Välj filtret **katalog + prenumeration** i den översta menyn och välj sedan den Azure Active Directory klient som innehåller den app-registrering som du vill lägga till en app-roll för.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar** och väljer sedan det program som du vill definiera app-roller i.
1. Välj **manifest** igen under **Hantera**.
1. Redigera appens manifest genom att hitta `appRoles` inställningen och lägga till dina program roller. Du kan definiera app-roller som mål `users` , `applications` eller båda. Följande JSON-kodfragment visar exempel på båda.
1. Spara manifestet.

Varje definition av program roll i manifestet måste ha ett unikt GUID för `id` värdet.

`value`Egenskapen för varje app Role-definition ska exakt matcha de strängar som används i koden i programmet. `value`Egenskapen får inte innehålla blank steg. Om det gör det får du ett fel meddelande när du sparar manifestet.

#### <a name="example-user-app-role"></a>Exempel: rollen användar program

I det här exemplet definieras en app-roll med namnet `Writer` som du kan tilldela till en `User` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Exempel: rollen program app

När det är tillgängligt för `applications` visas app-roller som program behörigheter i en app-registrerings **hanterings** avsnitt > **API-behörigheter > lägg till en behörighet > mina API: er > Välj en API > program behörigheter**.

Det här exemplet visar en app-roll som är riktad mot en `Application` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Tilldela roller till användare och grupper

När du har lagt till app-roller i ditt program kan du tilldela roller till användare och grupper. Tilldelning av användare och grupper till roller kan göras via portalens användar gränssnitt eller program mässigt med hjälp av [Microsoft Graph](/graph/api/user-post-approleassignments). När de användare som är tilldelade till de olika app-rollerna loggar in i programmet kommer deras token att ha sina tilldelade roller i `roles` anspråket.

Så här tilldelar du användare och grupper till roller med hjälp av Azure Portal:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. I **Azure Active Directory** väljer du **företags program** i den vänstra navigerings menyn.
1. Välj **alla program** om du vill visa en lista över alla dina program. Om ditt program inte visas i listan använder du filtren högst upp i listan **alla program** för att begränsa listan eller bläddra nedåt i listan för att hitta programmet.
1. Välj det program som du vill tilldela användare eller säkerhets grupp till roller.
1. Under **Hantera** väljer du **Användare och grupper**.
1. Öppna fönstret **Lägg till tilldelning** genom att välja **Lägg till användare** .
1. Välj Välj **användare och grupper** i fönstret **Lägg till tilldelning** . En lista över användare och säkerhets grupper visas. Du kan söka efter en viss användare eller grupp och välja flera användare och grupper som visas i listan.
1. När du har valt användare och grupper väljer du knappen **Välj** för att gå vidare.
1. Välj **Välj en roll** i fönstret **Lägg till tilldelning** . Alla roller som du har definierat för programmet visas.
1. Välj en roll och välj knappen **Välj** .
1. Välj knappen **tilldela** för att slutföra tilldelningen av användare och grupper till appen.

Bekräfta att de användare och grupper som du lade till visas i listan **användare och grupper** .

## <a name="assign-app-roles-to-applications"></a>Tilldela app-roller till program

När du har lagt till app-roller i ditt program kan du tilldela en app-roll till en klient-app med hjälp av Azure Portal eller program mässigt med hjälp av [Microsoft Graph](/graph/api/user-post-approleassignments).

När du tilldelar app-roller till ett program skapar du *program behörigheter*. Program behörigheter används vanligt vis av daemon-appar eller backend-tjänster som behöver autentisera och göra auktoriserade API-anrop i själva fallet utan interaktion av en användare.

Så här tilldelar du app-roller till ett program med hjälp av Azure Portal:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. I **Azure Active Directory** väljer du **Appregistreringar** i den vänstra navigerings menyn.
1. Välj **alla program** om du vill visa en lista över alla dina program. Om ditt program inte visas i listan använder du filtren högst upp i listan **alla program** för att begränsa listan eller bläddra nedåt i listan för att hitta programmet.
1. Välj det program som du vill tilldela en app-roll till.
1. Välj **API-behörigheter** > **Lägg till en behörighet**.
1. Välj fliken **Mina API: er** och välj sedan den app som du definierade app-roller för.
1. Välj **Programbehörigheter**.
1. Välj den eller de roller som du vill tilldela.
1. Välj knappen **Lägg till behörigheter för att lägga till** en fullständig addition av rollerna.

De nyligen tillagda rollerna bör visas i din app Registration-fönstret **API-behörigheter** .

#### <a name="grant-admin-consent"></a>Bevilja administratörs medgivande

Eftersom dessa är *program behörigheter*, inte delegerade behörigheter, måste en administratör bevilja medgivande för att använda de app-roller som tilldelats programmet.

1. I fönstret **API-behörigheter** för app-registrering väljer du **bevilja administrativt medgivande \<tenant name\> för**.
1. Välj **Ja** när du uppmanas att bevilja medgivande för de begärda behörigheterna.

Kolumnen **status** ska visa att medgivande har **beviljats för \<tenant name\>**.

## <a name="use-app-roles-in-your-web-api"></a>Använda app-roller i ditt webb-API

När du har definierat app-roller och tilldelat dem till en användare, grupp eller ett program är nästa steg att lägga till kod till ditt webb-API som söker efter dessa roller när API: et anropas. Det vill säga när ett klient program begär en API-åtgärd som du har bestämt kräver auktorisering måste API: ns kod kontrol lera att omfattningarna finns i den åtkomsttoken som visas i klient appens anrop.

Information om hur du lägger till auktorisering i ditt webb-API finns i [Protected Web API: kontrol lera omfattningar och app-roller](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>App-roller jämfört med grupper

Även om du kan använda app-roller eller grupper för auktorisering kan viktiga skillnader mellan dem påverka vilka du väljer att använda för ditt scenario.

| App-roller                                                                          | Grupper                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| De är specifika för ett program och definieras i appens registrering. De flyttas med programmet. | De är inte särskilda för en app, utan till en Azure AD-klient. |
| App-roller tas bort när deras registrerings program tas bort.                      | Grupper förblir intakta även om appen tas bort.            |
| Anges i `roles` anspråket.                                                     | Anges i `groups` anspråk.                                 |

Utvecklare kan använda app-roller för att kontrol lera om en användare kan logga in på en app eller om en app kan hämta en åtkomsttoken för ett webb-API. För att utöka den här säkerhets kontrollen till grupper kan utvecklare och administratörer också tilldela säkerhets grupper till app-roller.

App-roller föredras av utvecklare när de vill beskriva och kontrol lera behörighets parametrarna i själva appen. En app som använder grupper för auktorisering kommer till exempel att brytas i nästa klient som både grupp-ID och namn kan vara olika. En app som använder app-roller förblir säker. Att tilldela grupper till app-roller är i själva verket populär med SaaS-appar av samma skäl.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om app-roller med följande resurser.

* Kodexempel på GitHub
  * [Lägg till auktorisering med grupper och grupp anspråk till en ASP.NET Core-webbapp](https://aka.ms/groupssample)
  * [En Enkels Ides applikation (SPA) som anropar ett .NET Core webb-API och använder app-roller och säkerhets grupper](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Referens dokumentation
  * [Manifest för Azure AD-App](./reference-app-manifest.md)
  * [Azure AD-åtkomsttoken](access-tokens.md)
  * [Azure AD-ID-token](id-tokens.md)
  * [Tillhandahålla valfria anspråk till din app](active-directory-optional-claims.md)
* Video: [implementera auktorisering i dina program med Microsoft Identity Platform](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
