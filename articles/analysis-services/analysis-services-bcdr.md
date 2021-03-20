---
title: Azure Analysis Services hög tillgänglighet | Microsoft Docs
description: I den här artikeln beskrivs hur Azure Analysis Services ger hög tillgänglighet under avbrott i tjänsten.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: abfcc38601887cd14509ac436e0344b681e3577e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86506762"
---
# <a name="analysis-services-high-availability"></a>Hög tillgänglighet för Analysis Services

I den här artikeln beskrivs att säkerställa hög tillgänglighet för Azure Analysis Services-servrar. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Säkerställa hög tillgänglighet under ett tjänst avbrott

I sällsynta fallet kan ett Azure-datacenter ha ett avbrott. När ett avbrott inträffar kan det orsaka ett verksamhets avbrott som kan vara ett par minuter eller efter timmar. Hög tillgänglighet uppnås oftast med Server-redundans. Med Azure Analysis Services kan du uppnå redundans genom att skapa ytterligare, sekundära servrar i en eller flera regioner. När du skapar redundanta servrar, för att säkerställa att data och metadata på dessa servrar är synkroniserade med servern i en region som har varit offline, kan du:

* Distribuera modeller till redundanta servrar i andra regioner. Den här metoden kräver bearbetning av data på både den primära servern och redundanta servrar parallellt, så att alla servrar är synkroniserade.

* [Säkerhetskopiera](analysis-services-backup.md) databaser från den primära servern och Återställ på redundanta servrar. Du kan till exempel automatisera säkerhets kopieringar på natten till Azure Storage och återställa till andra redundanta servrar i andra regioner. 

I båda fallen måste du ändra anslutnings strängarna i rapporterings klienter för att ansluta till servern i ett annat regionalt Data Center, om den primära servern upplever ett avbrott. Den här ändringen bör betraktas som en sista utväg och endast om ett oåterkalleligt Data Center avbrott inträffar. Det är mer troligt att ett Data Center som är värd för den primära servern kommer att bli online igen innan du kan uppdatera anslutningar på alla klienter. 

För att undvika att behöva ändra anslutnings strängar för rapporterings klienter kan du skapa ett Server [Ali Aset](analysis-services-server-alias.md) för den primära servern. Om den primära servern slutar fungera kan du ändra aliaset så att det pekar på en redundant server i en annan region. Du kan automatisera alias till Server namn genom att koda en slut punkts hälso kontroll på den primära servern. Om hälso kontrollen Miss lyckas kan samma slut punkt dirigeras till en redundant server i en annan region. 

## <a name="related-information"></a>Relaterad information

[Säkerhets kopiering och återställning](analysis-services-backup.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md)   
[Ali Aset Server namn](analysis-services-server-alias.md) 

