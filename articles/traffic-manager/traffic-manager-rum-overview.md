---
title: Faktisk slutanvändarmätning i Azure Traffic Manager
description: I den här introduktionen får du lära dig hur Azure Traffic Manager Faktisk slutanvändarmätning fungerar.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 618f8fff532da0f6ae315ad9e4cda35a289949d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183718"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Översikt över Faktisk slutanvändarmätning i Traffic Manager

När du konfigurerar en Traffic Manager-profil för att använda metoden för att hitta prestanda, tittar tjänsten på var DNS-förfrågningarna kommer från och fattar beslut om routning för att dirigera dessa förfrågningar till den Azure-region som ger dem den lägsta svars tiden. Detta åstadkommer du genom att använda den nätverks fördröjnings information som Traffic Manager underhåll för olika slut användar nätverk.

Med Faktisk slutanvändarmätning kan du mäta nätverks fördröjnings mätningar i Azure-regioner, från de klient program som slutanvändarna använder, och som har Traffic Manager beakta den informationen även när du fattar beslut om routning. Genom att välja att använda Faktisk slutanvändarmätning kan du öka noggrannheten för routningen för begär Anden som kommer från de nätverk där slutanvändarna finns. 

## <a name="how-real-user-measurements-work"></a>Så här fungerar Faktisk slutanvändarmätning

Faktisk slutanvändarmätning fungerar genom att låta klient programmen mäta svars tiden till Azure-regioner som det visas från de slut användar nätverk där de används. Om du till exempel har en webb sida som används av användare på olika platser (t. ex. i Nord amerikanska regioner) kan du använda Faktisk slutanvändarmätning med metoden för att dirigera prestanda för att få dem till den bästa Azure-region där serverprogrammet finns.

Den startar genom att bädda in ett Java Script (med en unik nyckel) på dina webb sidor. När en användare besöker den här webb sidan så ser JavaScript-frågorna Traffic Manager för att få information om de Azure-regioner som den ska mäta. Tjänsten returnerar en uppsättning slut punkter till skriptet som sedan mäter dessa regioner i följd genom att ladda ned en enda bild punkts bild som finns i Azure-regionerna och som anger svars tiden mellan den tid då begäran skickades och den tidpunkt då den första byten mottogs. Dessa mätningar rapporteras sedan tillbaka till Traffic Managers tjänsten.

Med tiden sker detta ofta många gånger och flera nätverk leder till att Traffic Manager få mer korrekt information om svars tiderna för de nätverk där slutanvändarna finns. Den här informationen börjar tas med i cirkulations besluten som fattas av Traffic Manager. Detta leder till ökad noggrannhet i dessa beslut baserat på Faktisk slutanvändarmätning som skickats.

När du använder Faktisk slutanvändarmätning faktureras du baserat på antalet mått som skickas till Traffic Manager. Mer information om prissättningen finns på [sidan Traffic Manager priser](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Vanliga frågor och svar

* [Vilka är fördelarna med att använda Faktisk slutanvändarmätning?](./traffic-manager-faqs.md#what-are-the-benefits-of-using-real-user-measurements)

* [Kan jag använda Faktisk slutanvändarmätning med icke-Azure-regioner?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-non-azure-regions)

* [Vilken routningsmetod fördelar från Faktisk slutanvändarmätning?](./traffic-manager-faqs.md#which-routing-method-benefits-from-real-user-measurements)

* [Måste jag aktivera Faktisk slutanvändarmätning varje profil separat?](./traffic-manager-faqs.md#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Hur gör jag för att inaktivera Faktisk slutanvändarmätning för min prenumeration?](./traffic-manager-faqs.md#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Kan jag använda Faktisk slutanvändarmätning med andra klient program än webb sidor?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Hur många mätningar görs varje gången mitt Faktisk slutanvändarmätning-aktiverade webb sida renderas?](./traffic-manager-faqs.md#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Är det en fördröjning innan Faktisk slutanvändarmätning skript körs på min webb sida?](./traffic-manager-faqs.md#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Kan jag bara använda Faktisk slutanvändarmätning med de Azure-regioner jag vill mäta?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Kan jag begränsa antalet mätningar som gjorts till ett speciellt tal?](./traffic-manager-faqs.md#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Kan jag se de mått som utförs av klient programmet som en del av Faktisk slutanvändarmätning?](./traffic-manager-faqs.md#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Kan jag ändra mått skriptet som tillhandahålls av Traffic Manager?](./traffic-manager-faqs.md#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Kommer det att vara möjligt för andra att se vilken nyckel jag använder med Faktisk slutanvändarmätning?](./traffic-manager-faqs.md#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Kan andra missbruka min RUM nyckel?](./traffic-manager-faqs.md#can-others-abuse-my-rum-key)

* [Måste jag ange måttet Java Script på alla mina webb sidor?](./traffic-manager-faqs.md#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Kan information om slutanvändarna identifieras av Traffic Manager om jag använder Faktisk slutanvändarmätning?](./traffic-manager-faqs.md#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Behöver webb sidans mätnings Faktisk slutanvändarmätning använda Traffic Manager för routning?](./traffic-manager-faqs.md#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Måste jag vara värd för alla tjänster i Azure-regioner som ska användas med Faktisk slutanvändarmätning?](./traffic-manager-faqs.md#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Ökar Azures bandbredds användning när jag använder Faktisk slutanvändarmätning?](./traffic-manager-faqs.md#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du använder [faktisk slutanvändarmätning med webb sidor](traffic-manager-create-rum-web-pages.md)
- Lär dig [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Lär dig mer om [Mobile Center](/mobile-center/)
- Läs mer om [metoderna för trafik-routning](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Lär dig hur du [skapar en Traffic Manager-profil](./quickstart-create-traffic-manager-profile.md)