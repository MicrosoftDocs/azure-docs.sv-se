---
title: Distributions problem för Microsoft Azure Cloud Services vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om distribution för Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896485"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Distributions problem för Azure Cloud Services (klassisk): vanliga frågor och svar

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).
Den här artikeln innehåller vanliga frågor om distributions problem för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också se storleks information på [sidan Cloud Services virtuell dator storlek](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Varför Miss känner man ibland att distribuera en moln tjänst till mellanlagringsplatsen med ett resurs tilldelnings fel om det redan finns en befintlig distribution i produktions platsen?
Om en moln tjänst har en distribution i någon av platserna fästs hela moln tjänsten i ett särskilt kluster. Det innebär att om en distribution redan finns på produktions platsen kan en ny mellanlagrings distribution bara allokeras i samma kluster som produktions platsen.

Allokeringsfel inträffar när klustret där moln tjänsten finns inte har tillräckligt med fysiska beräknings resurser för att uppfylla din distributions förfrågan.

Information om hur du åtgärdar sådana allokeringsfel finns i [Cloud Service Allocation Failure: Solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Varför leder det till att det uppstår ett allokeringsfel vid skalning av en moln tjänst distribution?
När en moln tjänst distribueras fästs det vanligt vis i ett enskilt kluster. Det innebär att skala upp eller ut en befintlig moln tjänst måste allokera nya instanser i samma kluster. Om klustret närmar sig kapacitet eller om önskad VM-storlek/typ inte är tillgänglig kan begäran Miss lyckas.

Information om hur du åtgärdar sådana allokeringsfel finns i [Cloud Service Allocation Failure: Solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Varför leder det till att distributionen av en moln tjänst till en tillhörighets grupp ibland leder till allokeringsfel?
En ny distribution till en tom moln tjänst kan allokeras av infrastruktur resursen i alla kluster i den regionen, om inte moln tjänsten fästs på en tillhörighets grupp. Distributioner till samma tillhörighets grupp görs i samma kluster. Om klustret närmar sig kapaciteten kan begäran Miss lyckas.

Information om hur du åtgärdar sådana allokeringsfel finns i [Cloud Service Allocation Failure: Solutions](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Varför kan det ibland leda till allokeringsfel om du ändrar storlek på virtuell dator eller lägger till en ny virtuell dator i en befintlig moln tjänst?
Kluster i ett Data Center kan ha olika konfigurationer av dator typer (t. ex. en serie, AV2 serie, D-serien, Dv2-serien, G-serien, H-serien osv.). Men alla kluster har inte nödvändigt vis alla typer av virtuella datorer. Om du till exempel försöker lägga till en virtuell dator i D-serien till en moln tjänst som redan har distribuerats i ett kluster med endast ett serie kluster får du ett allokeringsfel. Detta inträffar även om du försöker ändra VM-SKU-storlekar (till exempel växla från en serie till en D-serien).

Information om hur du åtgärdar sådana allokeringsfel finns i [Cloud Service Allocation Failure: Solutions](cloud-services-allocation-failures.md#solutions).

För att kontrol lera vilka storlekar som är tillgängliga i din region, se [Microsoft Azure: produkter tillgängliga per region](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Varför Miss kan jag distribuera en moln tjänst på grund av gränser/kvoter/begränsningar i min prenumeration eller tjänst?
Distribution av en moln tjänst kan Miss lyckas om resurserna som måste allokeras överskrider den standard kvot eller högsta kvot som tillåts för din tjänst på region/data center nivå. Mer information finns i [Cloud Services gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Du kan också spåra den aktuella användningen/kvoten för din prenumeration på portalen: Azure Portal => Subscriptions => \<appropriate subscription>   => "användning + kvot".

Resursanvändning/förbrukning-relaterad information kan också hämtas via API:er för Azure-fakturering. Se [Översikt över Azure förbruknings-API](../cost-management-billing/manage/consumption-api-overview.md).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Hur kan jag ändra storleken på en distribuerad virtuell dator i moln tjänsten utan att distribuera om den?
Du kan inte ändra den virtuella dator storleken för en distribuerad moln tjänst utan att distribuera om den. Storleken på den virtuella datorn är inbyggd i CSDEF, som bara kan uppdateras med en omdistribution.

Mer information finns i [så här uppdaterar du en moln tjänst](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Varför kan jag inte distribuera Cloud Services via API: er för service hantering eller PowerShell när du använder Azure Resource Manager lagrings konto? 

Eftersom moln tjänsten är en klassisk resurs som inte är direkt kompatibel med Azure Resource Manager modellen kan du inte associera den med Azure Resource Manager lagrings konton. Här är några alternativ: 

- Distribuera via REST API.

    När du distribuerar via Service Management-REST API kan du få runt begränsningen genom att ange en SAS-URL till blob-lagringen, som fungerar med både det klassiska och Azure Resource Manager lagrings kontot. Läs mer om egenskapen "PackageUrl" [här](/previous-versions/azure/reference/ee460813(v=azure.100)).

- Distribuera via [Azure Portal](https://portal.azure.com).

    Detta fungerar från [Azure Portal](https://portal.azure.com) när anropet går via en proxy/shim som tillåter kommunikation mellan Azure Resource Manager och klassiska resurser. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Varför kräver Azure Portal mig att tillhandahålla ett lagrings konto för distribution?

I den klassiska portalen överfördes paketet till hanterings-API-lagret direkt, och API-lagret skulle då tillfälligt flytta paketet till ett internt lagrings konto.  Den här processen orsakar problem med prestanda och skalbarhet eftersom API-skiktet inte har utformats för att vara en fil överförings tjänst.  I Azure Portal (Resource Manager-distributions modell) har vi hoppat över steget för att först överföra till API-skiktet, vilket resulterar i snabbare och mer tillförlitlig distribution.

Vad gäller kostnaden är det mycket litet och du kan återanvända samma lagrings konto för alla distributioner. Du kan använda [Kalkylatorn för lagrings kostnad](https://azure.microsoft.com/pricing/calculator/#storage1) för att fastställa kostnaden för att ladda upp tjänst paketet (CSPKG), Hämta CSPKG och sedan ta bort CSPKG.
