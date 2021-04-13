---
title: Autentisering och auktorisering för Azures statiska Web Apps
description: Lär dig att använda olika Authorization providers för att skydda din statiska app.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 29821733b73717634aa8f0ab72270f058ffd3ddc
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309398"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Autentisering och auktorisering för förhandsversionen av Azure Static Web Apps

Azures statiska Web Apps effektiviserar autentiseringen genom att hantera autentisering med följande providers:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

Providerspecifika [inbjudningar](#invitations) associera användare med roller och behöriga användare beviljas åtkomst till [vägar](routes.md) enligt regler som definierats i _staticwebapp.config.jsi_ filen.

Alla autentiseringsproviders är aktiverade som standard. Begränsa en autentiseringsprovider genom att [blockera åtkomst](#block-an-authorization-provider) med en anpassad väg regel.

Avsnitten om autentisering och auktorisering är avsevärt överlappande av begrepp för routning. Se till att läsa [routnings guiden](routes.md) tillsammans med den här artikeln.

## <a name="roles"></a>Roller

Varje användare som har åtkomst till en statisk webbapp tillhör en eller flera roller. Det finns två inbyggda roller som användarna kan tillhöra:

- **anonymt**: alla användare tillhöra den _anonyma_ rollen automatiskt.
- **autentiserad**: alla användare som är inloggade tillhör den _autentiserade_ rollen.

Utöver de inbyggda rollerna kan du skapa nya roller, tilldela dem till användare via inbjudningar och referera till dem i _staticwebapp.config.jsi_ filen.

## <a name="role-management"></a>Roll hantering

### <a name="add-a-user-to-a-role"></a>Lägga till en användare till en roll

Om du vill lägga till användare till webbplatsen genererar du inbjudningar som gör att du kan koppla användare till vissa roller. Roller definieras och underhålls i _staticwebapp.config.js_ i filen.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Skapa en inbjudan

Inbjudningar är specifika för enskilda Authorization-providers, så du bör tänka igenom behoven i din app när du väljer vilka leverantörer som ska stödjas. Vissa leverantörer visar en användares e-postadress, medan andra endast anger platsens användar namn.

<a name="provider-user-details" id="provider-user-details"></a>

| Authorization Provider | Exponerar en användares |
| ---------------------- | ---------------- |
| Azure Active Directory | e-postadress    |
| Facebook               | e-postadress    |
| GitHub                 | användarnamn         |
| Google<sup>1</sup>     | e-postadress    |
| Twitter                | användarnamn         |

1. Navigera till en statisk Web Apps resurs i [Azure Portal](https://portal.azure.com).
1. Klicka på **roll hantering** under _Inställningar_.
1. Klicka på knappen **Bjud in** .
1. Välj en _Authorization-Provider_ från listan med alternativ.
1. Lägg till antingen användar namnet eller e-postadressen för mottagaren i rutan _Bjud in information_ .
   - För GitHub och Twitter anger du användar namnet. Ange mottagarens e-postadress för alla andra.
1. Välj domänen för den statiska platsen i list rutan _domän_ .
   - Den domän du väljer är den domän som visas i inbjudan. Om du har en anpassad domän som är kopplad till din webbplats vill du förmodligen välja den anpassade domänen.
1. Lägg till en kommaavgränsad lista över roll namn i rutan _roll_ .
1. Ange det maximala antalet timmar som du vill att din inbjudan ska vara giltig.
   - Den största möjliga gränsen är 168 timmar, vilket är 7 dagar.
1. Klicka på knappen **Generate** (Generera).
1. Kopiera länken från rutan _Inbjudnings länk_ .
1. E-posta inbjudan till den person som du beviljar åtkomst till din app.

När användaren klickar på länken i inbjudan uppmanas de att logga in med sitt motsvarande konto. När du har loggat in associeras användaren med de valda rollerna.

> [!CAUTION]
> Se till att dina rutt regler inte hamnar i konflikt med dina valda autentiseringsproviders. Om du blockerar en Provider med en väg regel hindras användarna från att acceptera inbjudningar.

### <a name="update-role-assignments"></a>Uppdatera roll tilldelningar

1. Navigera till en statisk Web Apps resurs i [Azure Portal](https://portal.azure.com).
1. Klicka på **roll hantering** under _Inställningar_.
1. Klicka på användaren i listan.
1. Redigera listan över roller i rutan _roll_ .
1. Klicka på knappen **Uppdatera** .

### <a name="remove-user"></a>Ta bort användare

1. Navigera till en statisk Web Apps resurs i [Azure Portal](https://portal.azure.com).
1. Klicka på **roll hantering** under _Inställningar_.
1. Leta upp användaren i listan.
1. Markera kryss rutan på användarens rad.
1. Klicka på knappen **Ta bort**.

Tänk på följande när du tar bort en användare:

1. Att ta bort en användare upphäver deras behörigheter.
1. Worldwide-spridningen kan ta några minuter.
1. Om användaren läggs tillbaka till appen [ `userId` ändras ändringarna](user-information.md).

## <a name="remove-personal-identifying-information"></a>Ta bort personlig identifierings information

När du ger ett medgivande till ett program som slutanvändare har programmet åtkomst till din e-postadress eller ditt användar namn beroende på identitets leverantören. När den här informationen har angetts bestämmer ägaren till programmet hur du ska hantera information som identifierar dig personligen.

Slutanvändare behöver kontakta administratörer av enskilda webbappar för att återkalla den här informationen från sina system.

Om du vill ta bort personligt identifierad information från Azures statiska Web Apps-plattform, och förhindra att plattformen tillhandahåller den här informationen i framtida begär Anden, skickar du en begäran med hjälp av URL: en:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

För att förhindra att plattformen tillhandahåller den här informationen om framtida begär anden till enskilda appar skickar du en begäran till följande URL:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Systemmapp

Azures statiska Web Apps använder `/.auth` System-mappen för att ge åtkomst till API: er som är relaterade till autentisering. I stället för att exponera någon av vägarna under `/.auth` mappen direkt till slutanvändare, kan du överväga att skapa [regler för routning](routes.md) för att skapa egna URL: er.

## <a name="login"></a>Inloggning

Använd följande tabell för att hitta det providerspecifika inloggnings flödet.

| Authorization Provider | Inloggnings väg             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Om du till exempel vill logga in med GitHub kan du lägga till en inloggnings länk som följande kodfragment:

```html
<a href="/.auth/login/github">Login</a>
```

Om du väljer att stödja fler än en provider måste du Visa en leverantörsspecifik länk för var och en på din webbplats.

Du kan använda en [väg regel](./configuration.md#routes) för att mappa en standardprovider till en egen väg som _/login_.

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Skicka inloggnings omdirigering

Om du vill att en användare ska gå tillbaka till en viss sida efter inloggningen anger du en fullständig URL i frågesträngparametern `post_login_redirect_uri` .

Exempel:

```html
<a href="/.auth/login/github?post_login_redirect_uri=https://zealous-water.azurestaticapps.net/success">Login</a>
```

## <a name="logout"></a>Utloggning

`/.auth/logout`Vägen loggar användare ut från webbplatsen. Du kan lägga till en länk till din webbplats navigering så att användaren kan logga ut så som visas i följande exempel.

```html
<a href="/.auth/logout">Log out</a>
```

Du kan använda en [väg regel](./configuration.md#routes) för att mappa en läsvänlig väg som _/logout_.

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Omdirigera omutloggning

Om du vill att en användare ska återgå till en viss sida efter utloggning anger du en URL i frågesträngparametern `post_logout_redirect_uri` .

## <a name="block-an-authorization-provider"></a>Blockera en Authorization Provider

Du kanske vill begränsa appen från att använda en Authorization-Provider. Till exempel kanske din app bara vill standardisera för [providers som exponerar e-postadresser](#provider-user-details).

Om du vill blockera en provider kan du skapa [väg regler](routes.md) för att returnera en 404 för förfrågningar till den blockerade providerspecifika vägen. Om du till exempel vill begränsa Twitter som Provider lägger du till följande väg regel.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Begränsningar

Se [artikeln om kvoter](quotas.md) för allmänna begränsningar och begränsningar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Åtkomst till användarautentisering och auktoriseringsdata](user-information.md)

<sup>1</sup> väntar på extern certifiering.
