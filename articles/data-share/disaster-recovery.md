---
title: Haveri beredskap för Azure Data Share
description: Haveri beredskap för Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218705"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Haveri beredskap för Azure Data Share

Den här artikeln beskriver hur du konfigurerar en haveri beredskaps miljö för Azure Data Share. Avbrott i Azure Data Center är sällsynt, men kan gå från några minuter till timmar. Avbrott i data centret kan orsaka avbrott i miljöer som förlitar sig på att data delas av data leverantören. Genom att följa stegen i den här artikeln kan data leverantörer fortsätta att dela data med sina data konsumenter i händelse av ett avbrott i data centret för den primära regionen som är värd för din data resurs. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Uppnå affärs kontinuitet för Azure Data Share

För att förbereda för ett avbrott i data centret kan dataprovidern ha en data resurs miljö etablerad i en sekundär region. Mått kan vidtas för att säkerställa en smidig redundansväxling i händelse av att ett avbrott i data centret inträffar. 

Data leverantörer kan etablera sekundära Azure-dataresurser i en annan region. Dessa data resurs resurser kan konfigureras för att inkludera resurser och data uppsättningar som finns i den primära Azure Data resurs resursen. De kan bjuda in data konsumenter till sekundära resurser när du konfigurerar DR-miljön eller vid ett senare tillfälle (dvs. som en del av stegen för manuell redundans).

Om data konsumenter har aktiva resurs prenumerationer i en sekundär miljö som har skapats för DR, kan de aktivera ögonblicks bild schema som en del av redundansväxlingen. Om data konsumenter inte vill prenumerera på en sekundär region i DR-syfte, kan de inbjudas till den sekundära resursen vid ett senare tillfälle. 

Data konsumenter kan antingen ha en aktiv resurs prenumeration som är inaktiv i DR-syfte, eller så kan data leverantörer bjuda in dem vid ett senare tillfälle som en del av manuella rutiner för växling vid fel. 

## <a name="related-information"></a>Relaterad information

- [Verksamhets kontinuitet och haveri beredskap](../best-practices-availability-paired-regions.md)
- [Bygg in hög tillgänglighet i din BCDR-strategi](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .