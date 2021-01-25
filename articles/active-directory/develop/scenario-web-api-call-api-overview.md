---
title: 'Bygg ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar underordnade webb-API: er (översikt).'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a66f0a2de1d8239baffbe53dfb5d6f2dd275d448
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756339"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: ett webb-API som anropar webb-API: er

Lär dig vad du behöver veta för att bygga ett webb-API som anropar webb-API: er.

## <a name="prerequisites"></a>Krav

Det här scenariot, där ett skyddat webb-API anropar andra webb-API: er, bygger på [Scenario: skyddat webb-API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Översikt

- En webb-, skriv bords-, mobil-eller mobil-eller Enkels Ides program klient (visas inte i det medföljande diagrammet) anropar ett skyddat webb-API och tillhandahåller en JSON Web Token (JWT) Bearer-token i HTTP-huvudet "Authorization".
- Det skyddade webb-API: et validerar token och använder Microsoft Authentication Library (MSAL)- `AcquireTokenOnBehalfOf` metoden för att begära en annan token från Azure Active Directory (Azure AD) så att det skyddade webb-API: et kan anropa ett andra webb-API eller underordnat webb-API för användarens räkning.
- Det skyddade webb-API: et kan också anropa `AcquireTokenSilent` senare för att begära token för andra underordnade API: er för samma användares räkning. `AcquireTokenSilent` uppdaterar token vid behov.

![Diagram över ett webb-API som anropar ett webb-API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Information

Appens registrerings del som är relaterad till API-behörigheter är klassisk. App-konfigurationen innebär att du använder OAuth 2,0 på uppdrag av Flow för att byta JWT Bearer-token mot en token för ett underordnat API. Den här token läggs till i token cache, där den är tillgänglig i webb-API: s kontrollanter och kan sedan hämta en token tyst för att anropa underordnade API: er.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Registrera appen](scenario-web-api-call-api-app-registration.md).
