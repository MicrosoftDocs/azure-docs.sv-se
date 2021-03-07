---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: edcbeb15b58d59bf080a1acf1d54f1b60e109c8a
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425756"
---
Gå till Azure Portal och <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" skapa en ny formulär igenkännings resurs " target="_blank"> skapa en ny formulär igenkännings resurs </a> . I fönstret **skapa** anger du följande information:

|    |    |
|--|--|
| **Namn** | Ett beskrivande namn för din resurs. Vi rekommenderar att du använder ett beskrivande namn, till exempel *MyNameFormRecognizer*. |
| **Prenumeration** | Välj den Azure-prenumeration som har beviljats åtkomst. |
| **Plats** | Platsen för din kognitiva tjänst instans. Olika platser kan orsaka svars tid, men har ingen inverkan på resursens tillgänglighet för körning. |
| **Prisnivå** | Kostnaden för din resurs beror på vilken pris nivå du väljer och din användning. Mer information finns i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/)för API.
| **Resursgrupp** | Den [Azure-resurs grupp](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) som ska innehålla din resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

> [!NOTE]
> Normalt när du skapar en kognitiv tjänst resurs i Azure Portal har du möjlighet att skapa en prenumerations nyckel för flera tjänster (används i flera kognitiva tjänster) eller en prenumerations nyckel för enskild tjänst (används endast med en viss kognitiv tjänst). Formulär tolken är dock inte inkluderad i multi-service-prenumerationen.

När du har slutfört distributionen av formulärets tolknings resurs söker du efter och väljer den från listan **alla resurser** i portalen. Din nyckel och slut punkt kommer att finnas på resursens nyckel-och slut punkts sida under resurs hantering. Spara båda dessa på en tillfällig plats innan du går vidare.