---
title: Azure Defender för behållar register – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för behållar register.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ab2ad15da9b1676924197d28e734f6baf59a02ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176645"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Introduktion till Azure Defender för containerregister

Azure Container Registry (ACR) är en hanterad privat Docker-registerpost som lagrar och hanterar behållar avbildningar för Azure-distributioner i ett centralt register. Den är baserad på Docker-registret 2,0 med öppen källkod.

Om du vill skydda alla Azure Resource Manager-baserade register i din prenumeration aktiverar du **Azure Defender för behållar register** på prenumerations nivå. Security Center genomsöker sedan bilder som skickas till registret, importeras till registret eller alla avbildningar som hämtas under de senaste 30 dagarna. Den här funktionen debiteras per avbildning.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Vilka är fördelarna med Azure Defender för behållar register?

Security Center identifierar Azure Resource Manager baserade ACR-register i din prenumeration och ger sömlös Azure-inbyggd sårbarhets bedömning och hantering av dina registers avbildningar.

**Azure Defender för behållar register** innehåller en sårbarhets-skanner för att skanna avbildningarna i dina Azure Resource Manager-baserade Azure Container Registry register och ger djupare insyn i dina avbildnings sårbarheter. Den integrerade skannern drivs av Qualys, branschens ledande sårbarhets söknings leverantör.

När problem påträffas – av Qualys eller Security Center – får du ett meddelande i Security Center instrument panel. För varje sårbarhet ger Security Center rekommendationer som kan åtgärdas, tillsammans med en allvarlighets grad och vägledning för hur du kan åtgärda problemet. Mer information om Security Centers rekommendationer för behållare finns i [referens listan för rekommendationer](recommendations-reference.md#recs-compute).

Security Center filtrerar och klassificerar resultat från skannern. När en bild är felfri, Security Center Markera den som sådan. Security Center skapar endast säkerhets rekommendationer för avbildningar som har problem att lösa. Security Center innehåller information om varje rapporterat sårbarhets-och allvarlighets GRADS klassificering. Dessutom ger den vägledning för hur du kan åtgärda de enskilda sårbarheter som finns på varje avbildning.

Genom att meddela om det uppstår problem kan Security Center minska risken för oönskade informations aviseringar.


> [!TIP]
> Mer information om Security Center behållar säkerhetsfunktioner finns i:
>
> - [Azure Security Center-och behållar säkerhet](container-security.md)
> - [Introduktion till Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>När skannas bilder?

Det finns tre utlösare för en avbildnings genomsökning:

- **Vid push** -när en bild skickas till ditt register, Security Center skannar automatiskt avbildningen. Om du vill utlösa en genomsökning av en avbildning kan du skicka den till din lagrings plats.

- **Nyligen hämtade** – eftersom nya sårbarheter upptäcks varje dag, genomsöker även **Azure Defender for container register** alla bilder som har hämtats under de senaste 30 dagarna. Det tillkommer ingen extra avgift för en ny genomsökning. som vi nämnt ovan faktureras du en gång per bild.

- **Vid import** -Azure Container Registry har import verktyg för att hämta avbildningar till registret från Docker Hub, Microsoft container Registry eller något annat Azure Container Registry. **Azure Defender for container registers** söker igenom alla avbildningar som stöds som du importerar. Läs mer i [Importera behållar avbildningar till ett behållar register](../container-registry/container-registry-import-images.md).
 
Genomsökningen slutförs vanligt vis inom 2 minuter, men det kan ta upp till 15 minuter. Resultaten görs tillgängliga som Security Center rekommendationer som den här:

[![Exempel på Azure Security Center rekommendation om sårbarheter som identifierats i en Azure Container Registry (ACR) värd avbildning](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Hur fungerar Security Center med Azure Container Registry

Nedan visas ett diagram med höga nivåer av komponenterna och fördelarna med att skydda dina register med Security Center.

![Azure Security Center och Azure Container Registry (ACR) Översikt på hög nivå](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Vanliga frågor och svar om Azure Container Registry avbildnings genomsökning

### <a name="how-does-security-center-scan-an-image"></a>Hur skannar Security Center en avbildning?
Security Center hämtar avbildningen från registret och kör den i en isolerad sandbox med Qualys-skannern. Skannern extraherar en lista över kända sårbarheter.

Security Center filtrerar och klassificerar resultat från skannern. När en bild är felfri, Security Center Markera den som sådan. Security Center skapar endast säkerhets rekommendationer för avbildningar som har problem att lösa. Genom att bara meddela dig när det uppstår problem kan Security Center minska risken för oönskade informations aviseringar.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan jag få Sök resultatet via REST API?
Ja. Resultaten finns i [REST API för underutvärderingar](/rest/api/securitycenter/subassessments/list/). Du kan också använda Azure Resource Graph (ARG), Kusto API för alla resurser: en fråga kan hämta en speciell sökning.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Vilka register typer genomsöks? Vilka typer faktureras?
En lista över de typer av behållar register som stöds av Azure Defender för behållar register finns i [tillgänglighet](#availability).

Om du ansluter register som inte stöds till din Azure-prenumeration genomsöker inte Azure Defender dem och debiteras inte för dem.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Kan jag anpassa undersöknings resultaten från sårbarhets skannern?
Ja. Om du har ett organisations behov av att ignorera en sökning i, i stället för att åtgärda det, kan du inaktivera det. Inaktiverade resultat påverkar inte din säkra poäng eller genererar oönskad brus.

[Lär dig mer om att skapa regler för att inaktivera resultat från verktyget Integrated sårbarhets bedömning](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Varför är Security Center Avisera mig om en avbildning som inte finns i mitt register?
Security Center ger sårbarhets bedömningar för varje bild som flyttas eller hämtas i ett register. Vissa bilder kan återanvända taggar från en avbildning som redan har genomsökts. Du kan till exempel tilldela om taggen "senaste" varje gång du lägger till en bild i en sammanfattning. I sådana fall finns fortfarande "Old"-avbildningen kvar i registret och kan fortfarande hämtas av sammanfattningen. Om avbildningen har säkerhets resultat och hämtas, kommer den att exponera säkerhets problem.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Sök igenom dina avbildningar efter sårbarheter](defender-for-container-registries-usage.md)