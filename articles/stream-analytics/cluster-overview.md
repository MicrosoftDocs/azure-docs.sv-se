---
title: Översikt över Azure Stream Analytics kluster
description: Lär dig mer om ett dedikerat erbjudande för en klient Stream Analytics klustret.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e5157a02f97370b20db85bf5e3e8aae98a2d8668
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709299"
---
# <a name="overview-of-azure-stream-analytics-cluster"></a>Översikt över Azure Stream Analytics kluster

Azure Stream Analytics kluster erbjuder en distribution med en enda klient för komplexa och krävande strömnings scenarier. I full skala kan Stream Analytics kluster bearbeta mer än 200 MB/sekund i real tid. Stream Analytics jobb som körs på dedikerade kluster kan utnyttja alla funktioner i standard erbjudandet och har stöd för anslutningar med privat anslutning till dina indata och utdata.

Stream Analytics kluster debiteras per enhet för strömning (SUs) som representerar mängden processor-och minnes resurser som allokerats till klustret. En enhet för strömning är densamma för standard-och dedikerade erbjudanden. Du kan köpa 36, 72, 108, 144, 180 eller 216 SUs för varje kluster. Ett Stream Analytics-kluster kan fungera som den strömmande plattformen för din organisation och kan delas av olika team som arbetar i olika användnings fall.

## <a name="what-are-stream-analytics-clusters"></a>Vad är Stream Analytics kluster

Stream Analytics-kluster drivs av samma motor som Stream Analytics-jobb som körs i en miljö med flera klientorganisationer. Det dedikerade klustret med en enda klientorganisation har följande funktioner:

* En enda värdklient utan brus från andra klienter. Resurserna är verkligen ”isolerade” och fungerar bättre när det förekommer trafiktoppar.

* Skala klustret mellan 36 och 216 SUs eftersom din strömnings användning ökar med tiden.

* VNet-stöd som gör att dina Stream Analytics jobb kan ansluta till andra resurser på ett säkert sätt med privata slut punkter.

* Möjlighet att redigera C#-användardefinierade funktioner och anpassade deserialiserare i valfri region.

* Noll underhålls kostnader så att du kan fokusera på din ansträngning för att skapa lösningar för real tids analys.

## <a name="how-to-get-started"></a>Så här kommer du igång

Du kan [skapa ett Stream Analytics kluster](create-cluster.md) via [Azure Portal](https://aka.ms/asaclustercreateportal). Om du har några frågor eller behöver hjälp med onboarding kan du kontakta Stream Analytics- [teamet](mailto:askasa@microsoft.com).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Hur gör jag för att välja mellan ett Stream Analytics kluster och ett Stream Analytics jobb?

Det enklaste sättet att komma igång är att skapa och utveckla ett Stream Analytics jobb för att bekanta dig med tjänsten och se hur det kan uppfylla dina analys krav.

Endast Stream Analytics-jobb stöder inte virtuella nätverk. Om dina indata eller utdata skyddas bakom en brand vägg eller en Azure-Virtual Network har du följande två alternativ:

* Om den lokala datorn har åtkomst till de in-och utdata-resurser som skyddas av ett VNet (till exempel Azure Event Hubs eller Azure SQL Database) kan du [installera Azure Stream Analytics verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md) på den lokala datorn. Du kan utveckla och [testa Stream Analytics jobb lokalt](stream-analytics-live-data-local-testing.md) på din enhet utan att det kostar någon kostnad. När du är redo att använda Stream Analytics i din arkitektur kan du skapa ett Stream Analytics-kluster, konfigurera privata slut punkter och köra jobben i stor skala.

* Du kan skapa ett Stream Analytics kluster, konfigurera klustret med de privata slut punkter som behövs för din pipeline och köra Stream Analytics jobb i klustret.

### <a name="what-performance-can-i-expect"></a>Vilken prestanda kan jag förväntar mig?

En SU är detsamma i standard-och dedikerade erbjudanden. Ett enda jobb som använder ett fullständigt 36 SU-kluster kan uppnå cirka 36 MB/sekund data flöde med en fördröjning på millisekunder. Det exakta antalet beror på händelse formatet och analys typen. Eftersom det är ett dedikerat kluster erbjuder Stream Analytics kluster mer pålitliga prestanda garantier. Alla jobb som körs i klustret tillhör bara dig.

### <a name="can-i-scale-my-cluster"></a>Kan jag skala mitt kluster?

Ja. Du kan enkelt konfigurera kapaciteten för klustret så att du kan [skala upp eller ned](scale-cluster.md) efter behov för att möta dina föränderliga behov.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>Kan jag köra mina befintliga jobb på de här nya kluster som jag har skapat?

Ja. Du kan länka dina befintliga jobb till ditt nyskapade Stream Analytics-kluster och köra det som vanligt. Du behöver inte återskapa dina befintliga Stream Analytics-jobb från grunden.

### <a name="how-much-will-these-clusters-cost-me"></a>Hur mycket kostar dessa kluster mig?

Dina Stream Analytics-kluster debiteras baserat på den valda SU-kapaciteten. Kluster faktureras per timme och det finns inga ytterligare avgifter per jobb som körs i dessa kluster. Se [pris sidan för privata länkar](https://azure.microsoft.com/pricing/details/private-link/) för fakturerings uppdateringar för privata slut punkter.

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Vilka indata och utdata kan jag ansluta privat till från Stream Analytics-klustret?

Stream Analytics stöder olika typer av indata och utdata. Du kan [skapa privata slut punkter](private-endpoints.md) i klustret som gör det möjligt för jobb att komma åt indata-och utmatnings resurser. För närvarande Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, Azure Event Hub och Azure Service Bus tjänster som stöds och andra typer kommer snart att läggas till. 

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över Azure Stream Analytics klustret. Sedan kan du skapa klustret och köra ditt Stream Analytics jobb: 

* [Skapa ett Stream Analytics kluster](create-cluster.md)
* [Hantera privata slut punkter i ett Azure Stream Analytics kluster](private-endpoints.md)
* [Hantera Stream Analytics jobb i ett Stream Analytics-kluster](manage-jobs-cluster.md)
* [Skapa ett Stream Analytics jobb](stream-analytics-quick-create-portal.md)
