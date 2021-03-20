---
title: Användar flöden i Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Lär dig mer om den utöknings bara princip ramverket för Azure Active Directory B2C och hur du skapar olika användar flöden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06253b571fd71623501c27fd5b0d9d4013727fc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94840207"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Användarflöden i Azure Active Directory B2C

För att hjälpa dig att konfigurera vanliga identitetsuppgifter för dina program finns det fördefinierade konfigurerbara principer, som kallas **användarflöden**, i Azure AD B2C-portalen. Med ett användar flöde kan du bestämma hur användare interagerar med ditt program när de gör saker som att logga in, registrera sig, redigera en profil eller återställa ett lösen ord. Med användar flöden kan du kontrol lera följande funktioner:

- Konto typer som används för inloggning, till exempel sociala konton som ett Facebook-eller lokala konto
- Attribut som ska samlas in från konsumenten, till exempel förnamn, post nummer och sko storlek
- Azure AD-multifaktorautentisering
- Anpassning av användar gränssnittet
- Information som programmet tar emot som anspråk i en token

Du kan skapa många användar flöden av olika typer av klienter och använda dem i dina program efter behov. Användar flöden kan återanvändas i olika program. Den här flexibiliteten gör det möjligt att definiera och ändra identitets upplevelser med minimala eller inga ändringar i koden. Ditt program utlöser ett användar flöde genom att använda en standard-HTTP-autentiseringsbegäran som innehåller en användar flödes parameter. En anpassad [token](tokens-overview.md) tas emot som ett svar.

I följande exempel visas frågesträngparametern "p" som anger det användar flöde som ska användas:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Användarflödesversioner

Azure AD B2C innehåller flera typer av användar flöden:

- **Registrera dig och logga in** – hanterar både registrerings-och inloggnings upplevelser med en enda konfiguration. Användarna utsätts för rätt sökväg beroende på kontexten. Dessutom ingår separata användar flöden för **registrering** eller **inloggning** . Men vi rekommenderar vanligt vis det kombinerade registrerings-och inloggnings användar flödet.
- **Profil redigering** – gör att användare kan redigera profil informationen.
- **Lösen ords återställning** – låter dig konfigurera om och hur användarna kan återställa sina lösen ord.

De flesta typer av användar flöden har både en **Rekommenderad** version och en **standard** version. Mer information finns i [användar flödes versioner](user-flow-versions.md).

> [!IMPORTANT]
> Om du har arbetat med användar flöden i Azure AD B2C tidigare, ser vi att vi har ändrat hur vi refererar till användar flödes versioner. Tidigare erbjöd vi V1-versioner (produktionsklara), V1.1 och V2 (förhandsversion). Nu har vi konsoliderat användar flöden i två versioner:
>
>- **Rekommenderade** användar flöden är de nya för hands versionerna av användar flöden. De har testats noggrant och kombinerar alla funktioner i äldre **v2** -och **v 1.1** -versioner. De nya rekommenderade användar flödena kommer att behållas och uppdateras. När du flyttar till dessa nya rekommenderade användar flöden har du till gång till nya funktioner när de släpps.
>- **Standard** användar flöden, som tidigare kallats **v1**, är allmänt tillgängliga, produktions färdiga användar flöden. Om dina användar flöden är verksamhets kritiska och är beroende av hög stabila versioner, kan du fortsätta att använda standard användar flöden, vilket innebär att dessa versioner inte upprätthålls och uppdateras.
>
>Alla äldre förhands gransknings användar flöden (V 1.1 och v2) finns på en sökväg till utfasningen den **1 augusti 2021**. När så är möjligt rekommenderar vi starkt att du [växlar till de nya **rekommenderade** användar flödena](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) så snart som möjligt, så att du alltid kan dra nytta av de senaste funktionerna och uppdateringarna.

## <a name="linking-user-flows"></a>Länka användar flöden

En **registrerings-eller inloggnings** användare med lokala konton innehåller ett **bortglömt lösen ord?** länk på den första sidan i upplevelsen. När du klickar på den här länken utlöses inget användar flöde för lösen ords återställning automatiskt.

I stället returneras felkoden `AADB2C90118` till ditt program. Programmet måste hantera denna felkod genom att köra ett speciellt användar flöde som återställer lösen ordet. Om du vill se ett exempel kan du titta på ett [enkelt ASP.net-exempel](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) som visar hur användar flöden länkas.

## <a name="email-address-storage"></a>Lagring av e-postadress

En e-postadress kan krävas som en del av ett användar flöde. Om användaren autentiseras med en social identitetsprovider lagras e-postadressen i egenskapen **otherMails** . Om ett lokalt konto är baserat på ett användar namn lagras e-postadressen i detalj egenskapen för stark autentisering. Om ett lokalt konto är baserat på en e-postadress lagras e-postadressen i egenskapen **signInNames** .

E-postadressen är inte garanterat verifierad i någon av dessa fall. En klient administratör kan inaktivera e-postverifiering i de grundläggande principerna för lokala konton. Även om verifiering av e-postadresser är aktive rad, kontrol leras inte adresser om de kommer från en social identitetsprovider och de inte har ändrats.

Endast egenskaperna **otherMails** och **signInNames** visas via Microsoft Graph-API: et. E-postadressen i detalj egenskapen för stark autentisering är inte tillgänglig.

## <a name="next-steps"></a>Nästa steg

Om du vill skapa de rekommenderade användar flödena följer du anvisningarna i [Självstudier: skapa ett användar flöde](tutorial-create-user-flows.md).
