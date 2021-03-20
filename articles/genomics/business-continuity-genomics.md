---
title: Översikt över affärs kontinuitet
titleSuffix: Microsoft Genomics
description: Den här översikten beskriver de funktioner som Microsoft Genomics tillhandahåller för affärs kontinuitet och haveri beredskap.
keywords: verksamhets kontinuitet, haveri beredskap
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 3097041c96f70dda384f0b116bbbc9bc9130be4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395758"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Översikt över affärs kontinuitet med Microsoft Genomics
Den här översikten beskriver de funktioner som Microsoft Genomics tillhandahåller för affärs kontinuitet och haveri beredskap. Lär dig mer om alternativ för att återskapa från störande händelser, till exempel ett avbrott i Azure-regionen, som kan orsaka data förlust. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funktioner som stöder affärs kontinuitet 
Även om det är sällsynt kan ett Azure-datacenter ha ett avbrott, vilket kan orsaka ett affärs avbrott som kan uppstå några minuter till några timmar. När ett avbrott uppstår kommer inte alla jobb som körs i data centret att fungera, och tjänsten Microsoft Genomics skickar inte automatiskt om jobb till en sekundär region. 

* Ett alternativ är att vänta tills data centret kommer tillbaka online när avbrott i data centret är över. Om avbrottet är kort identifierar Microsoft Genomics tjänsten automatiskt de misslyckade jobben och arbets flödet startas om automatiskt.

* Ett annat alternativ är att proaktivt Skicka arbets flödet i ett annat data område. Microsoft Genomics distribuerar instanser i flera [Azure-regioner](https://azure.microsoft.com/regions/services/)och varje landsspecifika instans är oberoende. Om någon av de Microsoft Genomics instanserna får ett lokalt regions problem fortsätter andra regioner som kör Microsoft Genomicss instanser att bearbeta jobb. Den här överföringen till en alternativ region kontrol leras av användaren och är tillgänglig när som helst.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Redundansväxla Microsoft Genomics arbets flöden manuellt till en annan region
Om ett regionalt Data Center avbrott inträffar kan du välja att skicka Microsoft Genomics arbets flöden i en sekundär region, baserat på dina individuella krav på data suveränitet och affärs kontinuitet. Om du vill redundansväxla Microsoft Genomics arbets flöden manuellt kan du använda en annan regions information. Genomiks konto och skicka jobbet med lämpliga landsspecifika enkäter och autentiseringsuppgifter för lagrings kontot.

Mer specifikt behöver du:
* Skapa ett genomik-konto i den sekundära regionen med hjälp av Azure Portal. 
* Migrera indata till ett lagrings konto i den sekundära regionen och konfigurera en mapp för utdata i den sekundära regionen.
* Skicka arbets flödet i den sekundära regionen.

När den ursprungliga regionen återställs migrerar Microsoft Genomics tjänsten inte data från den sekundära regionen tillbaka till den ursprungliga regionen. Du kan välja att flytta indata-och utdatafiler från den sekundära regionen tillbaka till den ursprungliga regionen.  Om du väljer att flytta data är detta utanför Genomiks tjänsten och alla kostnader som rör data flytten är ditt ansvar. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Förbereda för ett eventuellt landsspecifika avbrott
Om du är orolig över snabbare återställningar när det gäller ett avbrott i data centret, finns det några steg som du kan vidta för att minska den tid det tar att skicka in Microsoft Genomics arbets flöden manuellt till en sekundär region:

* Identifiera en lämplig sekundär region och pro-aktivt skapa ett genomik-konto i den regionen
* Duplicera dina data i den primära och den sekundära regionen så att dina data omedelbart är tillgängliga i den sekundära regionen. Detta kan göras manuellt eller med hjälp av den [geo-redundanta lagrings](../storage/common/storage-redundancy.md) funktionen som är tillgänglig i Azure Storage. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om dina alternativ för verksamhets kontinuitet och haveri beredskap när du använder tjänsten Microsoft Genomics. Mer information om verksamhets kontinuitet och haveri beredskap i Azure i allmänhet finns i [teknisk vägledning för Azure-återhämtning.](/azure/architecture/resiliency/recovery-loss-azure-region)