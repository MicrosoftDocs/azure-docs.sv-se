---
title: Ansluta Stream Analytics jobb till resurser i ett Azure-Virtual Network (VNET)
description: I den här artikeln beskrivs hur du ansluter ett Azure Stream Analytics jobb med resurser som finns i ett VNET.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: a2d26689a3036f05d7c8e7f417fbbb447402aedc
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762868"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Ansluta Stream Analytics jobb till resurser i ett Azure-Virtual Network (VNet)

Dina Stream Analytics jobb gör utgående anslutningar till dina indata och utdata till Azure-resurser för att bearbeta data i real tid och skapa resultat. De här indata-och utmatnings resurserna (till exempel Azure Event Hubs och Azure SQL Database) kan ligga bakom en Azure-brandvägg eller i ett Azure-Virtual Network (VNet). Stream Analytics-tjänsten körs från nätverk som inte kan inkluderas direkt i dina nätverks regler.

Det finns dock två sätt att på ett säkert sätt ansluta dina Stream Analytics-jobb till dina indata-och utmatnings resurser i sådana scenarier.
* Använda privata slut punkter i Stream Analytics kluster.
* Använda Managed Identity Authentication mode tillsammans med inställningen Tillåt betrodda tjänster i nätverket.

Din Stream Analytics jobb accepterar inte inkommande anslutningar.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Privata slut punkter i Stream Analytics kluster.
[Stream Analytics kluster](https://docs.microsoft.com/azure/stream-analytics/cluster-overview) är ett enda dedikerat beräknings kluster där du kan köra dina Stream Analytics-jobb. Du kan skapa hanterade privata slut punkter i Stream Analytics klustret, vilket gör att alla jobb som körs i klustret kan upprätta en säker utgående anslutning till dina indata-och utmatnings resurser.

Att skapa privata slut punkter i Stream Analytics klustret är en åtgärd i [två steg](https://docs.microsoft.com/azure/stream-analytics/private-endpoints). Det här alternativet passar bäst för medel stora och stora strömnings arbets belastningar eftersom minimi storleken på ett Stream Analytics kluster är 36 SUs (även om 36-SUs kan delas av olika jobb i olika prenumerationer eller miljöer som utveckling, testning och produktion).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>Hanterad identitetsautentisering med konfigurationen Tillåt betrodda tjänster
Vissa Azure-tjänster har inställningen **Tillåt betrodda Microsoft Services** -nätverk, som när det är aktiverat, tillåter att Stream Analytics jobb på ett säkert sätt ansluter till din resurs med stark autentisering. Med det här alternativet kan du ansluta dina jobb till dina indata-och utmatnings resurser utan att ett Stream Analytics kluster och privata slut punkter krävs. Att konfigurera jobbet för att använda den här metoden är en 2-steg-åtgärd:
* Använd Managed Identity Authentication mode när du konfigurerar indata eller utdata i ditt Stream Analytics jobb.
* Ge dina specifika Stream Analytics-jobb explicit åtkomst till dina mål resurser genom att tilldela en Azure-roll till jobbets tilldelade hanterade identitet. 

Aktivering av **Tillåt betrodda Microsoft-tjänster** beviljar inte ram åtkomst till något jobb. Detta ger dig fullständig kontroll över vilka enskilda Stream Analyticss jobb som kan komma åt dina resurser på ett säkert sätt. 

Dina jobb kan ansluta till följande Azure-tjänster med hjälp av den här metoden:
1. [Blob Storage eller Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity) – kan vara jobbets lagrings konto, strömmande indata eller utdata.
2. [Azure Event Hubs](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity) – kan vara jobbets strömmande indata eller utdata.

Om dina jobb behöver ansluta till andra indata-eller utdatatyper, är det enda alternativet att använda privata slut punkter i Stream Analytics kluster.

## <a name="next-steps"></a>Nästa steg

* [Skapa och ta bort privata slut punkter i Stream Analytics kluster](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)
* [Ansluta till Event Hubs i ett VNet med hjälp av hanterad identitets autentisering](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity)
* [Ansluta till Blob Storage och ADLS Gen2 i ett VNet med hanterad identitets autentisering](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)
