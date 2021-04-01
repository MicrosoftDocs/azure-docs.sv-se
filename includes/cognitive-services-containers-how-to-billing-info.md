---
author: aahill
ms.author: aahi
ms.date: 03/02/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d61813e723992f4381c5ea82121da8bbb70016dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102032943"
---
Frågor till behållaren debiteras enligt pris nivån för den Azure-resurs som används för `ApiKey` .

Azure Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till avläsnings-/fakturerings slut punkten. Du måste göra det möjligt för behållarna att kommunicera fakturerings information med fakturerings slut punkten hela tiden. Cognitive Services behållare skickar inte kund information, till exempel den bild eller text som analyseras, till Microsoft.

### <a name="connect-to-azure"></a>Anslut till Azure

Containern behöver de fakturerings argument värden som ska köras. Dessa värden tillåter att behållaren ansluter till fakturerings slut punkten. Behållar rapporteringen visar var 10 till 15: e minut. Om behållaren inte ansluter till Azure inom den tillåtna tids perioden fortsätter behållaren att köras men hanterar inte frågor förrän fakturerings slut punkten återställs. Anslutnings försöket görs 10 gånger med samma tidsintervall 10 till 15 minuter. Om den inte kan ansluta till fakturerings slut punkten inom 10 försök slutar behållaren att betjäna begär Anden. Se [vanliga frågor och svar om Cognitive Services container](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work) för att få ett exempel på den information som skickas till Microsoft för fakturering.

### <a name="billing-arguments"></a>Fakturerings argument

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` Kommandot startar behållaren när alla tre av följande alternativ finns med giltiga värden <span class="docon docon-navigate-external x-hidden-focus"></span> </a> :

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckeln för den Cognitive Services resurs som används för att spåra fakturerings information.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade resurs som anges i `Billing` . |
| `Billing` | Slut punkten för den Cognitive Services resursen som används för att spåra fakturerings information.<br/>Värdet för det här alternativet måste anges till slut punkts-URI för en etablerad Azure-resurs.|
| `Eula` | Anger att du har accepterat licensen för behållaren.<br/>Värdet för det här alternativet måste vara inställt på **acceptera**. |
