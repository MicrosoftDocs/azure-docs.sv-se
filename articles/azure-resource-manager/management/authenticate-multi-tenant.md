---
title: Autentisera mellan klienter
description: Beskriver hur Azure Resource Manager hanterar autentiseringsbegäranden över klienter.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75478830"
---
# <a name="authenticate-requests-across-tenants"></a>Autentisera begär anden över klienter

När du skapar ett program med flera klienter kan du behöva hantera autentiseringsbegäranden för resurser som finns i olika klienter. Ett vanligt scenario är när en virtuell dator i en klient måste anslutas till ett virtuellt nätverk i en annan klient. Azure Resource Manager innehåller ett huvud värde för lagring av hjälp-token för att autentisera begär anden till olika klienter.

## <a name="header-values-for-authentication"></a>Huvud värden för autentisering

Begäran har följande värden för Authentication-huvud:

| Huvudnamn | Beskrivning | Exempelvärde |
| ----------- | ----------- | ------------ |
| Auktorisering | Primär token | &lt;Primär token för Bearer&gt; |
| x-MS-Authorization-extra | Extra token | Bearer &lt; -token1 &gt; , EncryptedBearer &lt; -tillägg – token2 &gt; , Bearer &lt; -token3&gt; |

Tilläggs huvudet kan innehålla upp till tre hjälp-token. 

I koden för din app för flera klienter hämtar du autentiseringstoken för andra klienter och lagrar dem i hjälp rubrikerna. Alla tokens måste vara från samma användare eller program. Användaren eller programmet måste ha bjudits in som gäst till andra klienter.

## <a name="processing-the-request"></a>Bearbetar begäran

När din app skickar en begäran till Resource Manager, körs begäran under identiteten från den primära-token. Den primära token måste vara giltig och ha upphört att gälla. Denna token måste vara från en klient som kan hantera prenumerationen.

När begäran refererar till en resurs från en annan klient kontrollerar Resource Manager de extra token för att avgöra om begäran kan bearbetas. Alla extra token i huvudet måste vara giltiga och har inte gått ut. Om någon token har upphört att gälla returnerar Resource Manager en 401-svarskod. Svaret innehåller klient-ID och klient-ID från den token som inte är giltig. Om tilläggs huvudet innehåller en giltig token för klienten, bearbetas begäran om flera innehavare.

## <a name="next-steps"></a>Nästa steg

* Mer information om autentiseringsbegäranden finns i [autentiserings flöden och program scenarier](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Mer information om tokens finns [Azure Active Directory åtkomsttoken](../../active-directory/develop/access-tokens.md).
