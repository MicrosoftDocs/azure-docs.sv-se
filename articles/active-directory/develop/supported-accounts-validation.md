---
title: Validerings skillnader efter konto typer som stöds | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om validerings skillnader för olika egenskaper för olika typer av konton som stöds när du registrerar din app med Microsoft Identity Platform.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: eea2e587a075d774a25f479ec61575a002b57f75
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937814"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Validerings skillnader per konto typer som stöds (signInAudience)

När du registrerar ett program med Microsoft Identity Platform för utvecklare uppmanas du att välja vilka konto typer som programmet har stöd för. Den här egenskapen är i programobjektet och manifestet `signInAudience` .

Alternativen omfattar följande:

- **AzureADMyOrg**: endast konton i den organisations katalog där appen är registrerad (en enda klient).
- **AzureADMultipleOrgs**: konton i valfri organisations katalog (flera innehavare).
- **AzureADandPersonalMicrosoftAccount**: konton i valfri organisations katalog (flera innehavare) och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com).

För registrerade program kan du hitta värdet för konto typer som stöds i avsnittet **autentisering** i ett program. Du kan också hitta den under `signInAudience` egenskapen i **manifestet**.

Värdet du väljer för den här egenskapen har effekter på andra egenskaper för app-objekt. Det innebär att om du ändrar den här egenskapen kan du först behöva ändra andra egenskaper.

I följande tabell visas validerings skillnader för olika egenskaper för olika typer av konton som stöds.

| Egenskap | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` och `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Program-ID URI ( `identifierURIs` )  | Måste vara unikt i klienten <br><br> urn://-scheman stöds <br><br> Jokertecken stöds inte <br><br> Frågesträngar och fragment stöds <br><br> Maximal längd på 255 tecken <br><br> Ingen gräns * på antalet identifierURIs  | Måste vara globalt unikt <br><br> urn://-scheman stöds <br><br> Jokertecken stöds inte <br><br> Frågesträngar och fragment stöds <br><br> Maximal längd på 255 tecken <br><br> Ingen gräns * på antalet identifierURIs | Måste vara globalt unikt <br><br> urn://-scheman stöds inte <br><br> Jokertecken, fragment och frågesträngar stöds inte <br><br> Maximal längd på 120 tecken <br><br> Högst 50 identifierURIs |
| Certifikat ( `keyCredentials` ) | Symmetrisk signerings nyckel | Symmetrisk signerings nyckel | Kryptering och asymmetrisk signerings nyckel | 
| Klient hemligheter ( `passwordCredentials` ) | Ingen gräns * | Ingen gräns * | Om liveSDK har Aktiver ATS: högst 2 klient hemligheter | 
| Omdirigerings-URI: er ( `replyURLs` ) | Se [OMDIRIGERA URI/svars-URL-begränsningar och begränsningar](reply-url.md) för mer information. | | | 
| API-behörigheter ( `requiredResourceAccess` ) | Ingen gräns * | Ingen gräns * | Högst 50 resurser per program och 30 behörigheter per resurs (t. ex. Microsoft Graph). Total gräns på 200 per program (resurser x-behörigheter). | 
| Omfattningar som definieras av detta API ( `oauth2Permissions` ) | Maximal omfattnings namn längd på 120 tecken <br><br> Ingen gräns * för antalet definierade omfattningar | Maximal omfattnings namn längd på 120 tecken <br><br> Ingen gräns * för antalet definierade omfattningar |  Maximal omfattnings namn längd på 40 tecken <br><br> Högst 100 omfattningar har definierats | 
| Auktoriserade klient program ( `preAuthorizedApplications` ) | Ingen gräns * | Ingen gräns * | Totalt Max 500 <br><br> Högst 100 klient program har definierats <br><br> Högst 30 scope som definierats per klient | 
| appRoles | Stöds <br> Ingen gräns * | Stöds <br> Ingen gräns * | Stöds inte | 
| Utloggnings-URL för frontend-kanal | https://localhost tillåts <br><br> `http` schemat tillåts inte <br><br> Maximal längd på 255 tecken | https://localhost tillåts <br><br> `http` schemat tillåts inte <br><br> Maximal längd på 255 tecken | <br><br> https://localhost tillåts, http://localhost Miss lyckas för MSA <br><br> Maximal längd på 255 tecken <br><br> `http` schemat tillåts inte <br><br> Jokertecken stöds inte | 

* Det finns en global gräns på cirka 1000 objekt över alla samlings egenskaper på App-objektet.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [program registrering](app-objects-and-service-principals.md).
- Läs mer om [applikations manifestet](reference-app-manifest.md).
