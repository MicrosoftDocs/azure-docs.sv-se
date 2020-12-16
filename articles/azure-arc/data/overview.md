---
title: Vad är Azure Arc-aktiverade datatjänster
description: Introducerar Azure Arc-aktiverade data tjänster
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: ef16a4c0b6ea40fb4934307916f1fe79ccea72f9
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609147"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Vad är Azure Arc-aktiverade datatjänster (förhandsversion)?

Azure Arc gör det möjligt att köra Azure Data Services lokalt, vid gränsen och i offentliga moln med Kubernetes och den infrastruktur som du väljer.

För närvarande finns följande Azure Arc-aktiverade data tjänster tillgängliga i för hands versionen:

- SQL-hanterad instans
- PostgreSQL-storskalig

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Alltid aktuell

Azure Arc-aktiverade data tjänster som Azure Arc Enabled SQL-hanterad instans och Azure Arc-aktiverade PostgreSQL-skalning tar emot uppdateringar regelbundet, inklusive underhålls korrigeringar och nya funktioner som liknar upplevelsen i Azure. Uppdateringar från Microsoft-Container Registry tillhandahålls till dig och distributions-cadences anges av dig i enlighet med dina principer. På så sätt kan lokala databaser hållas uppdaterade samtidigt som du ser till att du behåller kontrollen. Eftersom Azure Arc-aktiverade data tjänster är en prenumerations tjänst kommer du inte längre att ha slut på support situationer för dina databaser.

## <a name="elastic-scale"></a>Elastisk skalning

Med molnet som elastiskt lokalt kan du skala upp eller ned dem dynamiskt på samma sätt som i Azure, baserat på den tillgängliga kapaciteten för din infrastruktur. Den här funktionen kan uppfylla burst-scenarier som har flyktiga behov, inklusive scenarier som kräver inmatning och fråga data i real tid, i valfri skala, med svars tid under andra. Dessutom kan du också skala ut databas instanser med hjälp av det unika distributions alternativet för storskalig distribution i Azure Database for PostgreSQL storskalig. Den här funktionen ger data arbets belastningar ytterligare förstärkning av kapacitets optimeringen med hjälp av unika skalbara *läsningar* och skrivningar.

## <a name="self-service-provisioning"></a>Själv tjänst etablering

Azure-bågen ger också andra fördelar i molnet, till exempel snabb distribution och automatisering i stor skala. Tack vare Kubernetes-baserad dirigering kan du distribuera en databas på några sekunder med hjälp av antingen GUI-eller CLI-verktyg.

## <a name="unified-management"></a>Enhetlig hantering

Med välbekanta verktyg som Azure Portal, Azure Data Studio och kan [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] du nu få en enhetlig vy över alla dina data till gångar som har distribuerats med Azure Arc. Du kan inte bara visa och hantera en rad Relations databaser i din miljö och Azure, men även få loggar och telemetri från Kubernetes-API: er för att analysera den underliggande infrastruktur kapaciteten och hälsan. Förutom att ha lokaliserad logg analys och prestanda övervakning kan du nu utnyttja Azure Monitor för omfattande Operational Insights på hela din egendom.

## <a name="disconnected-scenario-support"></a>Stöd för frånkopplat scenario

Många av tjänsterna som självbetjänings etablering, automatiserade säkerhets kopieringar/återställning och övervakning kan köras lokalt i din infrastruktur med eller utan en direkt anslutning till Azure. Att ansluta direkt till Azure öppnar ytterligare alternativ för integrering med andra Azure-tjänster, till exempel Azure Monitor och möjligheten att använda Azure Portal-och Azure Resource Manager-API: er från var som helst i världen för att hantera dina Azure Arc-aktiverade data tjänster.

## <a name="next-steps"></a>Nästa steg

> **Vill du bara testa saker?**  
> Kom igång snabbt med [Azure Arc-rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på Azure Kubernetes service (AKS), AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator.

[Installera klient verktyg](install-client-tools.md)

[Skapa data styrenheten för Azure-bågen](create-data-controller.md) (kräver att klient verktygen installeras först)

[Skapa en Azure SQL-hanterad instans på Azure-bågen](create-sql-managed-instance.md) (du måste först skapa en Azure båg-datakontrollant)

[Skapa en Azure Database for PostgreSQL skalnings Server grupp på Azure-bågen](create-postgresql-hyperscale-server-group.md) (du måste först skapa en Azure-båge data Controller)
