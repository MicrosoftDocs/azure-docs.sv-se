---
title: Översikt över Azure Arc
description: Lär dig mer om vad Azure-bågen är och hur den hjälper kunderna att aktivera hantering och styrning av sina hybrid resurser med andra Azure-tjänster och-funktioner.
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 8f39dff16037b5f52a050b7d07193b0ad7cab93d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672694"
---
# <a name="azure-arc-overview"></a>Översikt över Azure Arc

Idag är företag kämpar för att styra och styra en miljö som blir mer komplex. Dessa miljöer sträcker sig över data Center, flera moln och Edge. Varje miljö och moln har en egen uppsättning med verktyg för separat hantering som du behöver lära dig och använda.

I parallellt är nya DevOps-och ITOps-operativa modeller svåra att implementera, eftersom befintliga verktyg inte kan ge stöd för nya moln inbyggda mönster.

Azure-bågen fören klar styrning och hantering genom att leverera en konsekvent hanterings plattform för flera moln och lokalt. Med Azure ARC kan du hantera hela miljön, med en enda ruta av glas, genom att projicera dina befintliga resurser i Azure Resource Manager. Nu kan du hantera virtuella datorer, Kubernetes-kluster och databaser som om de körs i Azure. Oavsett var de bor kan du använda välkända Azure-tjänster och hanterings funktioner. Med Azure ARC kan du fortsätta använda traditionella ITOps, och vi presenterar DevOps-metoder för att stödja nya molnets inbyggda mönster i din miljö.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Kontroll Plans diagram för Azure båg hantering" border="false":::

I dag kan du med Azure Arc hantera följande resurs typer som finns utanför Azure:

* Servrar – både fysiska och virtuella datorer som kör Windows eller Linux.
* Kubernetes-kluster – stöd för flera Kubernetes-distributioner.
* Azure Data Services – Azure SQL Database-och PostgreSQL för storskaliga tjänster.

## <a name="what-does-azure-arc-deliver"></a>Vad levererar Azure Arc?

Viktiga funktioner i Azure Arc är:

* Implementera konsekvent inventering, hantering, styrning och säkerhet för dina servrar i din miljö.

* Konfigurera [Azure VM-tillägg](./servers/manage-vm-extensions.md) för att använda Azures hanterings tjänster för att övervaka, säkra och uppdatera dina servrar.

* Hantera och styr Kubernetes-kluster i stor skala.

* Använd GitOps konfiguration som kod hantering för att distribuera program och konfiguration i ett eller flera kluster direkt från käll kontroll, till exempel GitHub.

* Ingen touch-kompatibilitet och konfiguration för dina Kubernetes-kluster med hjälp av Azure Policy.

* Kör Azure Data Services i valfri Kubernetes-miljö, särskilt Azure SQL-hanterad instans och Azure Database for PostgreSQL storskalig, med fördelar som uppgraderingar/uppdateringar, säkerhet och övervakning som om de körs i Azure. Använd elastisk skalning, tillämpa uppdateringar utan avbrott i programmet, även om det inte har en kontinuerlig anslutning till Azure.

* En enhetlig upplevelse som visar dina Azure Arc-aktiverade resurser oavsett om du använder Azure Portal, Azure CLI, Azure PowerShell eller Azure REST API.

## <a name="how-much-does-azure-arc-cost"></a>Hur mycket kostar Azure Arc?

Följande är pris information för de funktioner som är tillgängliga idag med Azure Arc.

### <a name="arc-enabled-servers"></a>Arc-aktiverade servrar

Azure båg Control plan-funktioner erbjuds utan extra kostnad. Du måste bland annat:

* Resurs organisation via hanterings grupper och-taggar i Azure.

* Sökning och indexering via Azure Resource Graph.

* Åtkomst och säkerhet via Azure RBAC och prenumerationer.

* Miljöer och automatisering via mallar och tillägg.

* Hantering av uppdateringar

Alla Azure-tjänster som används på Arc-aktiverade servrar, till exempel Azure Security Center eller Azure Monitor debiteras enligt prissättningen för den tjänsten. Mer information finns på [sidan med priser för Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-aktiverade Kubernetes

I den aktuella förhands gransknings fasen erbjuds Azure Arc-Kubernetes utan extra kostnad.

### <a name="azure-arc-enabled-data-services"></a>Azure Arc-aktiverade datatjänster

I den aktuella förhands gransknings fasen erbjuds Azure Arc-aktiverade data tjänster utan extra kostnad.

## <a name="next-steps"></a>Nästa steg

* Mer information om ARC-aktiverade servrar finns i följande [Översikt](./servers/overview.md)

* Mer information om ARC-aktiverade Kubernetes finns i följande [Översikt](./kubernetes/overview.md)

* Mer information om ARC-aktiverade data tjänster finns i följande [Översikt](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)

* Upplev Arc-aktiverade tjänster från [koncept beviset rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/)
