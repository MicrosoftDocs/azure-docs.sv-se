---
title: Översikt över Azure Active Directory för utvecklare (v 1.0)
description: Den här artikeln innehåller en översikt över hur du loggar in Microsoft arbets-och skol konton med hjälp av Azure Active Directory v 1.0-slutpunkt och-plattform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: overview
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8cb426239fe9f3dea207df06b6f2bafbdb54058a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88117676"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Översikt över Azure Active Directory för utvecklare (v 1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst som utvecklare kan använda för att skapa appar som på ett säkert sätt kan logga in användare som har ett arbets- eller skolkonto hos Microsoft. Azure AD stöder utvecklare som skapar såväl verksamhetsspecifika appar för en klient som appar för flera klienter. Förutom grundläggande inloggning, kan Azure AD även låta appar anropa både Microsoft API:er som [Microsoft Graph](/graph/overview) och anpassade API:er som bygger på Azure AD-plattformen. Den här dokumentationen innehåller information om hur du lägger till stöd för Azure AD i ditt program med hjälp av protokoll av branschstandardtyp som OAuth2.0 och OpenID Connect.

> [!NOTE]
> Merparten av innehållet på den här sidan fokuserar på slut punkten och plattformen för v 1.0, som endast stöder Microsoft arbets-eller skol konton. Om du vill logga in på konsumenter eller personliga Microsoft-konton kan du läsa informationen på [slut punkten och plattformen för v 2.0](../develop/v2-overview.md). V 2.0-slutpunkten erbjuder en enhetlig utvecklings upplevelse för appar som vill logga in på alla Microsoft-identiteter.

- [Grundläggande autentisering](v1-authentication-scenarios.md) En introduktion till autentisering med Azure AD.
- [Typer av program](app-types.md) En översikt över de autentiserings scenarier som stöds av Azure AD.

## <a name="get-started"></a>Kom igång

V 1.0 snabb starter och självstudier vägleder dig genom att skapa en app på önskad plattform med hjälp av Azure AD Authentication Library (ADAL) SDK. Se **snabb starter** och **v 1.0-självstudierna** för v 1.0 i [Microsoft Identity Platform (Azure Active Directory för utvecklare)](index.yml) för att komma igång.

## <a name="how-to-guides"></a>Anvisningar

Mer information och genom gångar för de vanligaste uppgifterna i Azure AD finns i **v 1.0 instruktions guider** .

## <a name="reference-topics"></a>Referensämnen

Följande artiklar innehåller detaljerad information om API:er, protokollmeddelanden och de termer som används i Azure AD.

- [ADAL (Authentication Libraries)](active-directory-authentication-libraries.md) En översikt över de bibliotek och SDK: er som tillhandahålls av Azure AD.
- [Kod exempel](sample-v1-code.md) En lista över alla kod exempel för Azure AD.
- [Ord lista](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Terminologi och definitioner av ord som används i den här dokumentationen.

## <a name="videos"></a>Video

Se [Azure Active Directory Developer Platform-videor](videos.md) för att hjälpa till att migrera till den nya Microsoft Identity Platform.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]