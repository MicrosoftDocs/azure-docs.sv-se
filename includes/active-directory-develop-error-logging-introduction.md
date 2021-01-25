---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763475"
---
Microsoft Authentication Library (MSAL)-appar genererar logg meddelanden som kan hjälpa till att diagnostisera problem. En app kan konfigurera loggning med några rader kod och har anpassad kontroll över detalj nivån och huruvida personliga och organisatoriska data loggas. Vi rekommenderar att du skapar ett återanrop för MSAL-loggning och ger användarna möjlighet att skicka loggar när de har problem med autentiseringen.

## <a name="logging-levels"></a>Loggnings nivåer

MSAL tillhandahåller flera nivåer av loggnings information:

- Fel: indikerar att något har gått fel och ett fel genererades. Används för fel sökning och identifiering av problem.
- Varning! det har inte nödvändigt vis uppstått något fel eller fel, men är avsedda för diagnostik och att hitta problem.
- Info: MSAL kommer att logga händelser som är avsedda för informations ändamål som inte nödvändigt vis är avsedda för fel sökning.
- Verbose: standard. MSAL loggar fullständig information om biblioteks beteendet.

## <a name="personal-and-organizational-data"></a>Personliga och organisatoriska data

Som standard fångar MSAL-loggaren inte upp mycket känsliga personliga eller organisatoriska data. Biblioteket ger möjlighet att aktivera loggning av personliga och organisatoriska data om du bestämmer dig för detta.

I följande avsnitt finns mer information om MSAL-fel loggning för ditt program.