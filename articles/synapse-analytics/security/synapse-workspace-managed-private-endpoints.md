---
title: Hanterade privata slutpunkter
description: En artikel som förklarar hanterade privata slut punkter i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569954"
---
# <a name="synapse-managed-private-endpoints"></a>Synapse hanterade privata slut punkter

Den här artikeln förklarar hanterade privata slut punkter i Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Hanterade privata slutpunkter

Hanterade privata slut punkter är privata slut punkter som skapats i en hanterad Virtual Network som är associerade med din Azure Synapse-arbetsyta. Hanterade privata slut punkter upprättar en privat länk till Azure-resurser. Azure Synapse hanterar de här privata slut punkterna för din räkning. Du kan skapa hanterade privata slut punkter från din Azure Synapse-arbetsyta för att få åtkomst till Azure-tjänster (till exempel Azure Storage eller Azure Cosmos DB) och Azure-värdbaserade kund-/partner tjänster.

När du använder hanterade privata slut punkter går trafiken mellan din Azure Synapse-arbetsyta och andra Azure-resurser helt över Microsoft stamnät nätverket. Hanterade privata slut punkter skyddar mot data exfiltrering. En hanterad privat slutpunkt använder en privat IP-adress från din hanterade Virtual Network för att effektivt ta den Azure-tjänst som din Azure Synapse-arbetsyta kommunicerar med i Virtual Network. Hanterade privata slut punkter mappas till en angiven resurs i Azure och inte till hela tjänsten. Kunder kan begränsa anslutningen till en speciell resurs som godkänts av organisationen. 

Lär dig mer om [privata länkar och privata slut punkter](../../private-link/index.yml).

>[!IMPORTANT]
>Hanterade privata slut punkter stöds bara i Azure Synapse-arbetsytor med en hanterad arbets yta Virtual Network.

>[!NOTE]
>När du skapar en Azure dataSynapses-arbetsyta kan du välja att koppla en hanterad Virtual Network till den. Om du väljer att använda en hanterad Virtual Network kopplad till din arbets yta, kan du också välja att begränsa utgående trafik från din arbets yta till enbart godkända mål. Du måste skapa hanterade privata slut punkter till dessa mål. 


En privat slut punkts anslutning skapas i ett väntande tillstånd när du skapar en hanterad privat slut punkt i Azure Synapse. Ett arbets flöde för godkännande har startats. Ägaren till den privata länk resursen ansvarar för att godkänna eller avvisa anslutningen. Om ägaren godkänner anslutningen upprättas den privata länken. Men om ägaren inte godkänner anslutningen upprättas inte den privata länken. I båda fallen kommer den hanterade privata slut punkten att uppdateras med anslutningens status. Endast en hanterad privat slutpunkt i ett godkänt tillstånd kan användas för att skicka trafik till den privata länk resurs som är länkad till den hanterade privata slut punkten.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Hanterade privata slut punkter för dedikerad SQL-pool och Server lös SQL-pool

Dedikerad SQL-pool och Server lös SQL-pool är analys funktioner i din Azure Synapse-arbetsyta. Dessa funktioner använder infrastruktur för flera innehavare som inte distribueras till den [hanterade arbets ytan Virtual Network](./synapse-workspace-managed-vnet.md).

När en arbets yta skapas skapar Azure Synapse två hanterade privata slut punkter i arbets ytan, en för dedikerad SQL-pool och en för Server lös SQL-pool. 

Dessa två hanterade privata slut punkter visas i Synapse Studio. Välj **Hantera** i det vänstra navigerings fältet och välj sedan **hanterade privata slut punkter** för att se dem i Studio.

Den hanterade privata slut punkten som är riktad mot SQL-poolen heter *Synapse- \<workspacename\> WS-SQL-* och den som är riktad mot en server lös SQL-pool kallas *Synapse-WS-sqlOnDemand-- \<workspacename\>*.

![Hanterade privata slut punkter för dedikerad SQL-pool och Server lös SQL-pool](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Dessa två hanterade privata slut punkter skapas automatiskt åt dig när du skapar din Azure dataSynapses-arbetsyta. Du debiteras inte för dessa två hanterade privata slut punkter.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer kan du gå vidare till artikeln [skapa hanterade privata slut punkter till dina data källor](./how-to-create-managed-private-endpoints.md) .