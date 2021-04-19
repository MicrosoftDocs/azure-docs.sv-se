---
title: Vad är Azure Arc-aktiverade datatjänster
description: Introducerar Azure Arc-aktiverade datatjänster
ms.custom: references_regions
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 2d866dcb5b2a0be9e6468b3d40258e37ac93834e
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716105"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Vad är Azure Arc-aktiverade datatjänster (förhandsversion)?

Azure Arc gör det möjligt att köra Azure-datatjänster lokalt, vid gränsen och i offentliga moln med kubernetes och valfri infrastruktur.

För närvarande är följande Azure Arc-aktiverade datatjänster tillgängliga i förhandsversionen:

- SQL-hanterad instans
- PostgreSQL Hyperskala

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Alltid aktuell

Azure Arc-aktiverade datatjänster som Azure Arc SQL-hanterad instans och Azure Arc-aktiverad PostgreSQL i hyperskala får uppdateringar regelbundet, inklusive servicekorrigeringar och nya funktioner som liknar upplevelsen i Azure. Uppdateringar från Microsoft Container Registry tillhandahålls till dig och distributionen anges av dig i enlighet med dina principer. På så sätt kan lokala databaser hålla sig uppdaterade samtidigt som du bibehåller kontrollen. Eftersom Azure Arc-aktiverade datatjänster är en prenumerationstjänst kommer du inte längre att få support för dina databaser.

## <a name="elastic-scale"></a>Elastisk skalning

Med molnbaserad elasticitet lokalt kan du skala upp eller ned dina databaser dynamiskt på ungefär samma sätt som i Azure, baserat på infrastrukturens tillgängliga kapacitet. Den här funktionen kan uppfylla burst-scenarier som har instabila behov, inklusive scenarier som kräver inmatning och frågor mot data i realtid, i valfri skala, med svarstid på mindre än en sekund. Dessutom kan du även skala ut databasinstanser med hjälp av det unika distributionsalternativet i hyperskala för Azure Database for PostgreSQL Hyperskala. Den här funktionen ger dataarbetsbelastningar en extra boost för kapacitetsoptimering med hjälp av unika *utskalningsläsningar* och skrivningar.

## <a name="self-service-provisioning"></a>Etablering av självbetjäning

Azure Arc även andra molnfördelar, till exempel snabb distribution och automatisering i stor skala. Tack vare Kubernetes-baserad orkestrering kan du distribuera en databas på några sekunder med antingen GUI- eller CLI-verktyg.

## <a name="unified-management"></a>Enhetlig hantering

Med välbekanta verktyg som Azure Portal, Azure Data Studio och kan du nu få en enhetlig vy över alla dina datatillgångar som distribueras [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] med Azure Arc. Du kan inte bara visa och hantera en mängd olika relationsdatabaser i din miljö och Azure, utan även hämta loggar och telemetri från Kubernetes-API:er för att analysera den underliggande infrastrukturens kapacitet och hälsa. Förutom att ha lokaliserad logganalys och prestandaövervakning kan du nu använda Azure Monitor för omfattande driftsinsikter i hela din egendom.

## <a name="disconnected-scenario-support"></a>Stöd för frånkopplade scenarier

Många av tjänsterna, till exempel självbetjäningsetablering, automatiserade säkerhetskopieringar/återställningar och övervakning kan köras lokalt i infrastrukturen med eller utan direkt anslutning till Azure. När du ansluter direkt till Azure öppnas ytterligare alternativ för integrering med andra Azure-tjänster som Azure Monitor och möjligheten att använda API:erna Azure Portal och Azure Resource Manager från var som helst i världen för att hantera Azure Arc-aktiverade datatjänster.

## <a name="supported-regions"></a>Regioner som stöds

I följande tabell beskrivs de scenarier som för närvarande stöds för Arc-aktiverade datatjänster.

|Azure-regioner  |Direktanslutet läge  |Indirekt anslutet läge  |
|---------|---------|---------|
|East US|Tillgängligt|Tillgängligt
|Europa, västra |Tillgängligt |Tillgängligt
|Europa, norra|Tillgängligt|Tillgängligt

## <a name="next-steps"></a>Nästa steg

> **Vill du bara prova något?**  
> Kom igång snabbt [med Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) on Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) eller på en virtuell Azure-dator.

[Installera klientverktygen](install-client-tools.md)

[Skapa Azure Arc datakontrollant](create-data-controller.md) (kräver att klientverktygen installeras först)

[Skapa en Azure SQL hanterad instans Azure Arc](create-sql-managed-instance.md) (kräver att en Azure Arc datakontrollant skapas först)

[Skapa en Azure Database for PostgreSQL hyperskala-servergrupp i Azure Arc](create-postgresql-hyperscale-server-group.md) (kräver att en datakontrollant Azure Arc skapas först)
