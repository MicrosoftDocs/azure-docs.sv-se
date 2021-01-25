---
title: Program modell | Azure
titleSuffix: Microsoft identity platform
description: Läs om hur du registrerar ditt program så att det kan integreras med Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 2ba41e36d12b58da2e572cf870195716eacaddef
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755676"
---
# <a name="application-model"></a>Programmodell

Program kan logga in användare själva eller delegera inloggning till en identitets leverantör. I det här avsnittet beskrivs de steg som krävs för att registrera ett program med Microsoft Identity Platform.

## <a name="registering-an-application"></a>Registrera ett program

För en identitets leverantör att veta att en användare har åtkomst till en viss app måste både användaren och programmet vara registrerat hos identitets leverantören. När du registrerar ditt program med Azure AD ger du en identitets konfiguration för ditt program som gör det möjligt att integrera med Microsoft Identity Platform. Genom att registrera appen kan du också:

* Anpassa anpassningen av programmet i dialog rutan för inloggning. Detta är viktigt eftersom det är den första upplevelsen som en användare kommer att ha med din app.
* Bestäm om du bara vill låta användarna logga in om de tillhör din organisation. Detta är ett enda klient program. Eller Tillåt användare att logga in med ett arbets-eller skol konto. Detta är ett program med flera innehavare. Du kan också tillåta personliga Microsoft-konton eller ett socialt konto från LinkedIn, Google och så vidare.
* Begär omfångs behörigheter. Du kan till exempel begära kommandot "User. Read", som ger behörighet att läsa profilen för den inloggade användaren.
* Definiera omfattningar som definierar åtkomst till ditt webb-API. När en app vill komma åt ditt API måste du vanligt vis begära behörigheter till de omfattningar som du definierar.
* Dela en hemlighet med Microsoft Identity Platform som visar appens identitet.  Detta är relevant i de fall där appen är ett konfidentiellt klient program. Ett konfidentiellt klient program är ett program som kan lagra autentiseringsuppgifter på ett säkert sätt. De kräver en betrodd backend-server för att lagra autentiseringsuppgifterna.

När programmet har registrerats får du en unik identifierare som appen delar med Microsoft Identity Platform när den begär token. Om appen är ett [konfidentiellt klient program](developer-glossary.md#client-application), kommer den också att dela hemligheten eller den offentliga nyckeln, beroende på om certifikat eller hemligheter användes.

Microsoft Identity Platform representerar program som använder en modell som uppfyller två huvud funktioner:

* Identifiera appen med de autentiseringsprotokoll som stöds
* Ange alla identifierare, URL: er, hemligheter och relaterad information som behövs för att autentisera

Microsoft Identity Platform:

* Innehåller alla data som krävs för att stödja autentisering vid körning
* Innehåller alla data för att bestämma vilka resurser en app kan behöva komma åt och under vilka omständigheter en specifik begäran ska vara uppfylld
* Innehåller en infrastruktur för att implementera app-etablering i appens utvecklares klient organisation och till en annan Azure AD-klient
* Hanterar användarens medgivande under Tokenbegäran och underlättar dynamisk etablering av appar över klient organisationer

**Samtycke** är en resurs ägare som beviljar auktorisering för ett klient program att komma åt skyddade resurser, under specifika behörigheter för resurs ägarens räkning. Microsoft Identity Platform:

* Möjliggör för användare och administratörer att dynamiskt bevilja eller neka medgivande för appen att få åtkomst till resurser för deras räkning.
* Möjliggör för administratörer att i slutänden bestämma vilka appar som tillåts göra vad och vilka användare som kan använda specifika appar samt hur åtkomsten till katalogresurserna går till.

## <a name="multi-tenant-apps"></a>Appar för flera klienter

I Microsoft Identity Platform beskriver ett [program objekt](developer-glossary.md#application-object) ett program. Vid distributions tillfället använder Microsoft Identity Platform programobjektet som en skiss för att skapa ett [huvud namn för tjänsten](developer-glossary.md#service-principal-object)som representerar en konkret instans av ett program i en katalog eller klient organisation. Tjänstens huvud namn definierar vad appen faktiskt kan göra i en speciell mål katalog, som kan använda den, vilka resurser den har åtkomst till och så vidare. Microsoft Identity Platform skapar ett huvud namn för tjänsten från ett program objekt via [medgivande](developer-glossary.md#consent).

Följande diagram visar ett förenklat etablerings flöde för Microsoft Identity Platform som drivs av medgivande. Den visar två klienter: *A* och *B*.

* *Klient organisation A* äger programmet.
* *Klient B* instansierar programmet via ett huvud namn för tjänsten.

![Förenklat etableringsflöde som drivs av medgivande](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

I det här etableringsflödet sker följande:

1. En användare från klient B försöker logga in med appen. slut punkten för auktorisering begär en token för programmet.
1. Autentiseringsuppgifterna för användaren förvärvas och verifieras för autentisering.
1. Användaren uppmanas att ange medgivande för appen för att få åtkomst till klient B.
1. Microsoft Identity Platform använder programobjektet i klient organisationen som en skiss för att skapa ett huvud namn för tjänsten i klient B.
1. Användaren får en begärd token.

Du kan upprepa den här processen för ytterligare klienter. Klient organisation A behåller skissen för appen (program objekt). Användare och administratörer för alla andra innehavare där appen ges tillåtelse att behålla kontrollen över vad programmet tillåts att göra via motsvarande tjänst huvud objekt i varje klient organisation. Mer information finns i [program-och tjänst huvud objekt i Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Nästa steg

För andra ämnen som behandlar grundläggande autentisering och auktorisering:

* Se [autentisering kontra auktorisering](authentication-vs-authorization.md) för att lära dig mer om grundläggande begrepp för autentisering och auktorisering i Microsoft Identity Platform.
* Se [säkerhetstoken](security-tokens.md) för att lära dig hur åtkomsttoken, uppdaterings-token och ID-token används vid autentisering och auktorisering.
* Se [appens inloggnings flöde](app-sign-in-flow.md) för att lära dig om inloggnings flödet för webben, skriv bordet och mobila appar i Microsoft Identity Platform.

Mer information om program modellen:

* Se [hur och varför program läggs till i Azure AD](active-directory-how-applications-are-added.md) för mer information om program objekt och tjänst huvud namn i Microsoft Identity Platform.
* Se [innehav i Azure Active Directory](single-and-multi-tenant-apps.md) för mer information om appar för en enda klient och appar för flera klient organisationer.
* Se [Azure Active Directory B2C-dokumentationen](../../active-directory-b2c/index.yml) för mer information om hur Azure AD också tillhandahåller Azure Active Directory B2C så att organisationer kan logga in användare, vanligt vis kunder, med hjälp av sociala identiteter som ett Google-konto.