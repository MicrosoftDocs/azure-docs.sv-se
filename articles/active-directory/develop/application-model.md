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
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795658"
---
# <a name="application-model"></a>Programmodell

Program kan logga in användare själva eller delegera inloggning till en identitets leverantör. I den här artikeln beskrivs de steg som krävs för att registrera ett program med Microsoft Identity Platform.

## <a name="register-an-application"></a>Registrera ett program

För en identitets leverantör att veta att en användare har åtkomst till en viss app måste både användaren och programmet vara registrerat hos identitets leverantören. När du registrerar ditt program med Azure Active Directory (Azure AD) ger du en identitets konfiguration för ditt program som gör det möjligt att integrera med Microsoft Identity Platform. Genom att registrera appen kan du också:

* Anpassa program anpassningen i dialog rutan för inloggning. Den här anpassningen är viktig eftersom inloggningen är den första upplevelsen som en användare kommer att ha med din app.
* Bestäm om du bara vill tillåta att användare loggar in om de tillhör din organisation. Den här arkitekturen kallas för ett program med en enda klient. Du kan också låta användarna logga in med ett arbets-eller skol konto, som kallas för ett program med flera innehavare. Du kan också tillåta personliga Microsoft-konton eller ett socialt konto från LinkedIn, Google och så vidare.
* Begär omfångs behörigheter. Du kan till exempel begära kommandot "User. Read", som ger behörighet att läsa profilen för den inloggade användaren.
* Definiera omfattningar som definierar åtkomst till ditt webb-API. När en app vill komma åt ditt API måste du vanligt vis begära behörigheter till de omfattningar som du definierar.
* Dela en hemlighet med Microsoft Identity Platform som visar appens identitet. Att använda en hemlighet är relevant i det fall där appen är ett konfidentiellt klient program. Ett konfidentiellt klient program är ett program som kan lagra autentiseringsuppgifter på ett säkert sätt. En betrodd backend-server krävs för att lagra autentiseringsuppgifterna.

När appen har registrerats får den en unik identifierare som den delar med Microsoft Identity Platform när den begär token. Om appen är ett [konfidentiellt klient program](developer-glossary.md#client-application), kommer den också att dela hemligheten eller den offentliga nyckeln beroende på om certifikat eller hemligheter användes.

Microsoft Identity Platform representerar program med hjälp av en modell som uppfyller två huvud funktioner:

* Identifiera appen av de autentiseringsprotokoll som det stöder.
* Ange alla identifierare, URL: er, hemligheter och relaterad information som behövs för att autentisera.

Microsoft Identity Platform:

* Innehåller alla data som krävs för att stödja autentisering vid körning.
* Innehåller alla data för att bestämma vilka resurser som en app kan behöva komma åt och under vilka omständigheter en specifik begäran ska uppfyllas.
* Innehåller en infrastruktur för att implementera app-etablering i appens utvecklares klient organisation och till en annan Azure AD-klient.
* Hanterar användarens medgivande under Tokenbegäran och underlättar dynamisk etablering av appar över klient organisationer.

*Samtycke* är en resurs ägare som beviljar auktorisering för ett klient program att komma åt skyddade resurser, under specifika behörigheter för resurs ägarens räkning. Med Microsoft Identity Platform kan du:

* Användare och administratörer kan dynamiskt bevilja eller neka tillåtelse för appen att få åtkomst till resurser för deras räkning.
* Administratörer kan slutligen bestämma vilka appar som får göra och vilka användare som kan använda vissa appar och hur katalog resurserna ska användas.

## <a name="multi-tenant-apps"></a>Appar för flera klienter

I Microsoft Identity Platform beskriver ett [program objekt](developer-glossary.md#application-object) ett program. Vid distributions tillfället använder Microsoft Identity Platform programobjektet som en skiss för att skapa ett [huvud namn för tjänsten](developer-glossary.md#service-principal-object)som representerar en konkret instans av ett program i en katalog eller klient organisation. Tjänstens huvud namn definierar vad appen faktiskt kan göra i en speciell mål katalog, som kan använda den, vilka resurser den har åtkomst till och så vidare. Microsoft Identity Platform skapar ett huvud namn för tjänsten från ett program objekt via [medgivande](developer-glossary.md#consent).

Följande diagram visar ett förenklat etablerings flöde för Microsoft Identity Platform som drivs av medgivande. Den visar två klienter: *A* och *B*.

* *Klient organisation A* äger programmet.
* *Klient B* instansierar programmet via ett huvud namn för tjänsten.

![Diagram som visar ett förenklat etablerings flöde som drivs av medgivande.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

I det här etableringsflödet sker följande:

1. En användare från klient B försöker logga in med appen. Slut punkten för auktorisering begär en token för programmet.
1. Autentiseringsuppgifterna för användaren förvärvas och verifieras för autentisering.
1. Användaren uppmanas att ange medgivande för appen för att få åtkomst till klient B.
1. Microsoft Identity Platform använder programobjektet i klient organisationen som en skiss för att skapa ett huvud namn för tjänsten i klient B.
1. Användaren får en begärd token.

Du kan upprepa den här processen för fler klienter. Klient organisation A behåller skissen för appen (program objekt). Användare och administratörer för alla andra innehavare där appen ges tillåtelse att behålla kontrollen över vad programmet tillåts att göra via motsvarande tjänst huvud objekt i varje klient organisation. Mer information finns i [program-och tjänst huvud objekt i Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Nästa steg

Mer information om autentisering och auktorisering i Microsoft Identity Platform finns i följande artiklar:

* Läs mer om grundläggande begrepp för autentisering och auktorisering i autentisering och [auktorisering](authentication-vs-authorization.md).
* Information om hur åtkomsttoken, uppdaterings-token och ID-token används vid autentisering och auktorisering finns i [säkerhetstoken](security-tokens.md).
* Läs mer om inloggnings flödet för webb-, skriv bords-och mobilappar i [appens inloggnings flöde](app-sign-in-flow.md).

Mer information om program modellen finns i följande artiklar:

* Mer information om program objekt och tjänst huvud namn i Microsoft Identity Platform finns i [hur och varför program läggs till i Azure AD](active-directory-how-applications-are-added.md).
* För mer information om appar för en enda klient och appar för flera klient organisationer, se [innehav i Azure Active Directory](single-and-multi-tenant-apps.md).
* Mer information om hur Azure AD tillhandahåller Azure Active Directory B2C så att organisationer kan logga in användare, vanligt vis kunder, genom att använda sociala identiteter som ett Google-konto, se [Azure Active Directory B2C dokumentation](../../active-directory-b2c/index.yml).