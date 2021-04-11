---
title: Om Azure Cloud Services (utökad support)
description: Lär dig mer om underordnade element i nätverks konfigurations filen för tjänst konfigurations filen, som anger Virtual Network och DNS-värden.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cc470d12377f50f9f9ae4b362bcabf3a5ce34e8f
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169291"
---
# <a name="about-azure-cloud-services-extended-support"></a>Om Azure Cloud Services (utökad support)

> [!IMPORTANT]
> Cloud Services (utökad support) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cloud Services (utökad support) är en ny [Azure Resource Manager](../azure-resource-manager/management/overview.md) baserad distributions modell för [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) -produkten och för närvarande finns i en offentlig för hands version. Cloud Services (utökad support) har den främsta fördelen med att tillhandahålla regional återhämtning tillsammans med funktions paritet med Azure Cloud Services som distribueras med Azure-Service Manager. Den erbjuder också vissa ARM-funktioner som rollbaserad åtkomst och kontroll (RBAC), taggar, principer och stöd för mallar för distribution.  

Med den här ändringen kommer den Azure Service Manager-baserade distributions modellen för Cloud Services att byta namn till [Cloud Services (klassisk)](../cloud-services/cloud-services-choose-me.md). Du behåller möjligheten att bygga och snabbt distribuera dina webb-och moln program och-tjänster. Du kommer att kunna skala infrastrukturen för moln tjänster baserat på aktuell efter frågan och se till att programmens prestanda kan fortsätta samtidigt som kostnaderna minskar.  

## <a name="what-does-not-change"></a>Vad ändras inte 
- Du skapar koden, definierar konfigurationerna och distribuerar den till Azure. Azure konfigurerar beräknings miljön, kör koden och övervakar och underhåller den åt dig.
- Cloud Services (utökad support) stöder också två typer av roller, [Web och Worker](../cloud-services/cloud-services-choose-me.md). Det finns inga ändringar i design, arkitektur eller komponenter för webb-och arbets roller. 
- De tre komponenterna i en moln tjänst, tjänst definitionen (. csdef), tjänst konfigurationen (. cscfg) och tjänst paketet (. cspkg) överförs och det sker ingen ändring i deras [format](cloud-services-model-and-package.md). 
- Inga ändringar krävs för körnings kod eftersom data planet är samma och kontroll planet ändras bara. 
- Azure-gäst-versioner och associerade uppdateringar justeras med Cloud Services (klassisk)
- Underliggande uppdaterings process med avseende på uppdaterings domäner, hur uppgradering fortsätter, återställning och tillåten tjänst ändringar under en uppdatering ändras inte

## <a name="changes-in-deployment-model"></a>Ändringar i distributions modellen

Minimala ändringar krävs för tjänst konfigurations-(. cscfg) och service definitions-filer (. csdef) för att distribuera Cloud Services (utökad support). Inga ändringar krävs för körnings kod. Distributions skript måste dock uppdateras för att anropa de nya Azure Resource Manager baserade API: erna. 

:::image type="content" source="media/overview-image-1.png" alt-text="Bild visar klassisk moln tjänst konfiguration med tillägg av mall-avsnittet. ":::

De stora skillnaderna mellan Cloud Services (klassiska) och Cloud Services (utökad support) vad gäller distribution är: 

- Azure Resource Manager distributioner använder [arm-mallar](../azure-resource-manager/templates/overview.md) som är en JavaScript Object Notation-fil (JSON) som definierar infrastrukturen och konfigurationen för ditt projekt. Mallen använder deklarativ syntax, som låter dig ange vad du vill distribuera utan att du behöver skriva sekvensen med programmeringskommandon för att skapa det. Tjänst konfigurationen och tjänst definitions filen måste överensstämma med [arm-mallen](../azure-resource-manager/templates/overview.md) när du distribuerar Cloud Services (utökad support). Detta kan uppnås antingen genom att [skapa arm-mallen manuellt](deploy-template.md) eller med [PowerShell](deploy-powershell.md), [portalen](deploy-portal.md) och [Visual Studio](deploy-visual-studio.md).  

- Kunder måste använda [Azure Key Vault](../key-vault/general/overview.md) för att [hantera certifikat i Cloud Services (utökad support)](certificates-and-key-vault.md). Med Azure Key Vault kan du lagra och hantera programautentiseringsuppgifter på ett säkert sätt, till exempel hemligheter, nycklar och certifikat i en central och säker moln lagrings plats. Dina program kan autentiseras för Key Vault vid körning för att hämta autentiseringsuppgifter. 

- Alla resurser som distribueras via [Azure Resource Manager](../azure-resource-manager/templates/overview.md) måste finnas i ett virtuellt nätverk. Virtuella nätverk och undernät skapas i Azure Resource Manager att använda befintliga Azure Resource Manager-API: er och måste refereras till i avsnittet NetworkConfiguration i. cscfg när du distribuerar Cloud Services (utökad support).   

- Varje moln tjänst (utökad support) är en enda oberoende distribution. Moln tjänster (utökad support) har inte stöd för flera platser i en enda moln tjänst.  
    - Funktioner för VIP-växling kan användas för att växla mellan två moln tjänster (utökad support). Testa och mellanlagra en ny version av en moln tjänst, distribuera en moln tjänst (utökad support) och tagga den som VIP-utbytbar med en annan moln tjänst (utökad support)  

- Domain Name Service (DNS)-etiketten är valfri för en moln tjänst (utökad support). I Azure Resource Manager är DNS-etiketten en egenskap hos den offentliga IP-resurs som är kopplad till moln tjänsten. 

## <a name="migration-to-azure-resource-manager"></a>Migrering till Azure Resource Manager

Cloud Services (utökad support) innehåller två sökvägar som du kan använda för att migrera från [Azure Service Manager](/powershell/azure/servicemanagement/overview) till [Azure Resource Manager](../azure-resource-manager/management/overview.md). 
1) Kunder distribuerar moln tjänster direkt i Azure Resource Manager och tar sedan bort den gamla moln tjänsten i Azure Service Manager. 
2) Migrering på plats har stöd för möjligheten att migrera Cloud Services (klassisk) med minimal avbrotts tid till Cloud Services (utökad support). 

### <a name="additional-migration-options"></a>Ytterligare migrerings alternativ

När du utvärderar migrerings planer från Cloud Services (klassisk) till Cloud Services (utökad support) kanske du vill undersöka ytterligare Azure-tjänster som: [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [azure Kubernetes service](../aks/intro-kubernetes.md)och [Azure Service Fabric](../service-fabric/service-fabric-overview.md). De här tjänsterna fortsätter att ha ytterligare funktioner, medan Cloud Services (utökad support) huvudsakligen underhåller funktions paritet med Cloud Services (klassisk). 

Beroende på programmet kan Cloud Services (utökad support) kräva betydligt mindre ansträngning för att flytta till Azure Resource Manager jämfört med andra alternativ. Om ditt program inte utvecklas är Cloud Services (utökad support) ett användbart alternativ att överväga eftersom det ger en snabb migrering. Om ditt program ständigt utvecklas och behöver en mer modern funktions uppsättning, kan du utforska andra Azure-tjänster för att bättre hantera dina aktuella och framtida krav. 

## <a name="next-steps"></a>Nästa steg
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
