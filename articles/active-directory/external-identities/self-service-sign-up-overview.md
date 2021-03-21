---
title: Registrering av självbetjäning för externa identiteter – Azure AD
description: Lär dig hur du tillåter externa användare att registrera sig för dina program genom att aktivera självbetjänings registrering. Skapa en personlig registrerings upplevelse genom att anpassa användar flödet för självbetjänings registrering.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13023ef93cabcf46924cc2cc76dc2d868c4a1ddd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653576"
---
# <a name="self-service-sign-up"></a>Registrering av självbetjäning

När du delar ett program med externa användare är det inte säkert att du alltid vet i förväg vilka som behöver åtkomst till programmet. Som ett alternativ till att skicka inbjudningar direkt till enskilda användare kan du tillåta att externa användare registrerar sig för enskilda program genom att aktivera självbetjänings registrering. Du kan skapa en personlig registrerings upplevelse genom att anpassa användar flödet för självbetjänings registrering. Du kan till exempel ange alternativ för att registrera dig för Azure AD eller sociala identitets leverantörer och samla in information om användaren under registrerings processen.

> [!NOTE]
> Du kan koppla användar flöden till appar som skapats av din organisation. Användar flöden kan inte användas för Microsoft-appar, t. ex. SharePoint eller team.

## <a name="user-flow-for-self-service-sign-up"></a>Användar flöde för självbetjänings registrering

Ett självbetjänings registrerings användar flöde skapar en registrerings upplevelse för dina externa användare genom det program som du vill dela. Användar flödet kan associeras med ett eller flera av dina program. Först ska du aktivera självbetjänings registrering för din klient och federera med de identitets leverantörer som du vill att externa användare ska kunna använda för inloggning. Sedan skapar du och anpassar registrerings användar flödet och tilldelar dina program till den.
Du kan konfigurera användar flödes inställningarna för att styra hur användaren registrerar sig för programmet:

- Konto typer som används för inloggning, till exempel sociala konton som Facebook eller Azure AD-konton
- Attribut som ska samlas in från användaren som registrerar sig, till exempel förnamn, post nummer eller land/region för placering

När en användare vill logga in i ditt program, oavsett om det är en webb-, mobil-, skriv bords-eller Enkels Ides applikation (SPA), initierar programmet en auktoriseringsbegäran till slut punkten för användar flödet. Användar flödet definierar och styr användarens upplevelse. När användaren slutför inloggnings användar flödet genererar Azure AD en token och omdirigerar användaren tillbaka till ditt program. När registreringen är klar skapas ett gäst konto för användaren i katalogen. Flera program kan använda samma användar flöde.

## <a name="example-of-self-service-sign-up"></a>Exempel på självbetjänings registrering

I följande exempel visas hur vi samlar in sociala identitets leverantörer till Azure AD med självbetjänings registrerings funktioner för gäst användare.  
En partner av Sparbanken öppnar Sparbanken-appen. De bestämmer sig för att registrera sig för ett leverantörs konto, så att de väljer begär ditt leverantörs konto, som initierar registrerings flödet för självbetjäning.

![Exempel på Start sidan för självbetjänings registrering](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

De använder e-postmeddelandet för att registrera dig.

![Exempel som visar valet av Facebook för inloggning](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD skapar en relation med Sparbanken med partnerns Facebook-konto och skapar ett nytt gäst konto för användaren när de har registrerat sig.

Sparbank vill veta mer om användaren, t. ex. namn, företags namn, kod för företags registrering, telefonnummer.

![Exempel som visar användarens registrerings-attribut](media/self-service-sign-up-overview/example-enter-user-attributes.png)

Användaren anger informationen, fortsätter med registrerings flödet och får åtkomst till de resurser som de behöver.

![Exempel som visar den inloggade användaren](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Nästa steg

 Mer information finns i så här lägger du till självbetjänings [registrering i en app](self-service-sign-up-user-flow.md).