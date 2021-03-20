---
title: Protected Web API – översikt
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en skyddad webb-API (översikt).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 434c255f0b9634e0d17bad1627ddd92af9498bfe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582442"
---
# <a name="scenario-protected-web-api"></a>Scenario: skyddat webb-API

I det här scenariot får du lära dig hur du exponerar ett webb-API. Du lär dig också hur du skyddar webb-API: et så att endast autentiserade användare kan komma åt det.

Om du vill använda ditt webb-API aktiverar du antingen autentiserade användare med både arbets-och skol konton eller aktiverar Microsoft personliga konton.

## <a name="specifics"></a>Information

Här är en detaljerad information som du behöver känna till för att skydda webb-API: er:

- Din app-registrering måste exponera minst en *omfattning* eller en *program roll*.
  - Omfattningar exponeras av webb-API: er som anropas för en användares räkning.
  - Program roller exponeras av webb-API: er som anropas av daemon-program (som anropar ditt webb-API i sitt eget ställe).
- Om du skapar en ny webb-API app-registrering väljer du den [åtkomsttoken](reference-app-manifest.md#accesstokenacceptedversion-attribute) som har godkänts av ditt webb-API till `2` . För äldre webb-API: er kan den godkända token-versionen vara `null` , men det här värdet begränsar inloggnings mål gruppen till endast organisationer, och personliga Microsoft-konton (MSA) stöds inte.
- Kod konfigurationen för webb-API: n måste validera den token som används när webb-API: t anropas.
- Koden i styrenhets åtgärderna måste verifiera rollerna eller omfattningarna i token.

## <a name="recommended-reading"></a>Rekommenderad läsning

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Registrera appen](scenario-protected-web-api-app-registration.md).
