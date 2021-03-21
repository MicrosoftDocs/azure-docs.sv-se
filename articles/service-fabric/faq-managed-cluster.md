---
title: Vanliga frågor om Service Fabric hanterade kluster
description: Vanliga frågor och svar om Service Fabric hanterade kluster, inklusive funktioner, användnings fall och vanliga scenarier.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633095"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Vanliga frågor och svar om Service Fabric hanterade kluster

Här följer några vanliga frågor och svar om Service Fabric hanterade kluster (för hands version).

## <a name="general"></a>Allmänt

### <a name="what-are-service-fabric-managed-clusters"></a>Vad är Service Fabric hanterade kluster?

Service Fabric hanterade kluster är en utveckling av Service Fabric kluster resurs modell som har utformats för att göra det enklare att distribuera och hantera kluster. Ett Service Fabric hanterat kluster använder Azure Resource Manager inkapslings modell så att en användare bara behöver definiera och distribuera en enda kluster resurs jämfört med de många oberoende resurser som de måste distribuera idag (skalnings uppsättning för virtuella datorer, Load Balancer, IP och mer).

### <a name="what-regions-are-supported-in-the-preview"></a>Vilka regioner stöds i förhands granskningen?

Regioner som stöds för förhands granskningen Service Fabric hanterade kluster inkluderar `centraluseuap` , `eastus2euap` , `eastasia` ,, `northeurope` `westcentralus` och `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Kan jag göra en migrering på plats av mitt befintliga Service Fabric-kluster till en hanterad kluster resurs?

Nej. Vid detta tillfälle måste du skapa en ny Service Fabric kluster resurs för att använda den nya Service Fabric hanterade kluster resurs typen.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Finns det ytterligare kostnader för Service Fabric hanterade kluster?

Nej. Det finns ingen ytterligare kostnad kopplad till ett Service Fabric hanterat kluster utöver kostnaden för underliggande beräknings-, lagrings-och nätverks resurser som krävs för klustret.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Introduceras ett nytt service avtal för den Service Fabric hanterade kluster resursen?

SLA ändras inte från den aktuella Service Fabric resurs modellen.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Vad är skillnaden mellan ett Basic-och standard-SKU-kluster?

Ett Basic SKU-kluster innebär att de flesta konfigurationer tillhandahålls av Service Fabric Resource Provider. Basic SKU-kluster är avsedda att användas för testning och för produktions miljöer. Med ett standard-SKU-kluster kan användare konfigurera klustret så att det uppfyller deras behov. Mer information finns i [Service Fabric hanterade kluster SKU: er](./overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="cluster-deployment-and-management"></a>Kluster distribution och hantering

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Jag kör anpassade skript tillägg på den virtuella datorns skalnings uppsättning, kan jag fortsätta med en hanterad Service Fabric-resurs?

Ja, du kan ange VM-tillägg på hanterade typer av klusternoder. Mer information finns i [Lägg till ett skalnings uppsättnings tillägg till en Service Fabric hanterad cluster node-typ](how-to-managed-cluster-vmss-extension.md).

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Jag vill ha en intern belastningsutjämnare, är det möjligt?

Nej. Det går för närvarande inte att ha en intern belastningsutjämnare. Vi rekommenderar att du låser reglerna för nätverks säkerhets gruppen (NSG) för att blockera all oönskad inkommande/utgående trafik.

### <a name="can-i-autoscale-my-cluster"></a>Kan jag Autoskala mitt kluster?

Autoskalning är för närvarande inte tillgängligt i förhands granskningen.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Kan jag distribuera mitt kluster mellan tillgänglighets zoner?

Kluster för kors tillgänglighets zoner är för närvarande inte tillgängliga i förhands granskningen.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Kan jag välja mellan automatiska och manuella uppgraderingar för min kluster körning?

I för hands versionen slutförs alla uppgraderingar av uppgraderings tiden automatiskt.

## <a name="applications"></a>Program

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Finns det en lokal utvecklings miljö för Service Fabric hanterade kluster?

Den lokala utvecklings miljön är oförändrad från befintliga Service Fabric-kluster. Mer information finns i [Konfigurera din utvecklings miljö](./service-fabric-get-started.md) för mer information om den lokala utvecklings miljön.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Kan jag distribuera mina program som en Azure Resource Manager-resurs?

Ja. Stöd har lagts till för att distribuera program som en Azure Resource Manager resurs (förutom distribution med PowerShell och CLI). Information om hur du kommer igång finns i [distribuera ett Service Fabric hanterat kluster program med arm-mall](how-to-managed-cluster-app-deployment-template.md).
