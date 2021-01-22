---
title: Replikering mellan regioner av Azure NetApp Files-volymer | Microsoft Docs
description: Beskriver vad Azure NetApp Files replikering i flera regioner, stödda region par, service nivå mål, data hållbarhet och kostnads modell.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: b-juche
ms.openlocfilehash: e51297e8fe5c3dccf43318a066ac5da4a7d24cb2
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696099"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Replikering mellan regioner av Azure NetApp Files volymer

Den Azure NetApp Files replikeringsinställningarna ger data skydd genom replikering över flera regioner. Du kan replikera data asynkront från en Azure NetApp Files volym (källa) i en region till en annan Azure NetApp Files volym (mål) i en annan region.  Med den här funktionen kan du redundansväxla ditt kritiska program i händelse av ett stort avbrott eller en katastrof.

> [!IMPORTANT]
> Funktionen för replikering över flera regioner finns för närvarande i offentlig för hands version. Du måste skicka in en Waitlist-begäran för att få åtkomst till funktionen via [Azure NetApp Files över flera regioner Waitlist sändnings sida](https://aka.ms/anfcrrpreviewsignup). Vänta tills en officiell bekräftelse skickas från Azure NetApp Files-teamet innan du använder funktionen för replikering över flera regioner.

## <a name="supported-region-pairs"></a>Region par som stöds

Azure NetApp Files Volume Replication är för närvarande tillgänglig i följande fasta regions par:  

* Västra USA och östra USA
* USA, väst 2 och östra USA 
* USA, Central syd och USA, Central 
* Södra centrala USA och östra USA
* Södra centrala USA och östra USA 2 
* Östra USA och östra USA 2  
* USA, östra 2 och USA, Central 
* Östra Australien och Australien, sydöstra
* Kanada, centrala och Kanada, öst
* Centrala Indien och södra Indien
* Tyskland, västra centrala och Tyskland, norra
* Japan, östra och Japan, väst
* Nord Europa och Västeuropa
* Sydostasien och östra Australien
* Storbritannien, södra och Tyskland, västra centrala
* Storbritannien, södra och Storbritannien, västra

## <a name="service-level-objectives"></a>Mål på service nivå

Återställnings punkt mål (återställnings punkt mål) eller högsta tillåtna data förlust definieras som två gånger i replikeringsschema.  Den faktiska återställningen kan variera beroende på faktorer som den totala data uppsättningens storlek tillsammans med ändrings takten, procent andelen av data skrivs över och replikeringens bandbredd som är tillgänglig för överföring.   

* Det maximala antalet återställnings scheman är 20 minuter för replikeringsschemat på 10 minuter.  
* För schemat per timme är det högsta antalet återställningar två timmar.  
* För schemat för daglig replikering är det högsta antalet återställnings bara två dagar.  

Återställnings tids mål (RTO), eller högsta tillåtna stillestånds tid för affärs program, bestäms av faktorer i att öppna programmet och ge åtkomst till data på den andra platsen. Lagrings delen av RTO för att bryta peering-relationen för att aktivera mål volymen och tillhandahålla Läs-och Skriv data åtkomst på den andra platsen förväntas vara slutförd inom en minut.

## <a name="cost-model-for-cross-region-replication"></a>Kostnads modell för replikering över flera regioner  

Med Azure NetApp Files replikering över flera regioner betalar du bara för den mängd data som du replikerar. Det finns inga inställningar för avgifter eller minimi avgifter för användning. Replikeringsintervallet baseras på replikeringsfrekvens och regionen för den *mål* volym som du väljer under den inledande konfigurationen av replikeringen. Mer information finns på sidan med [Azure NetApp Files prissättning](https://azure.microsoft.com/pricing/details/netapp/) .  

Den normala Azure NetApp Files lagrings kapacitets avgiften gäller för replikeringens mål volym (kallas även för *data skydds* volym). 

### <a name="pricing-examples"></a>Prissättningsexempel

Det totala antalet replikeringar mellan regioner som faktureras under en månad baseras på mängden data som replikeras via funktionen för replikering över flera regioner under den månaden. Mängden data som replikeras mäts i GiB. Den representerar summan av data som replikeras mellan två regioner under alla vanliga replikeringar från käll volymerna till mål volymerna och under alla omsynkroniseringar av replikeringar från mål volymerna till käll volymerna.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Exempel 1: månad 1 bas linje replikering och stegvisa replikeringar

Anta följande situationer:

* *Käll* volymen är från Azure NetApp Files *Premium* service nivå. Den har en volym kvot storlek på 1000 GiB och en förbrukad volym på 500 GiB i början av den första dagen i månaden. Volymen finns i regionen *USA, södra centrala* .
* *Mål* volymen är från Azure NetApp Files *standard* service nivå. Den finns i regionen *USA, östra 2* .
* Du har konfigurerat en *timmes* baserade replikering över flera regioner mellan de två volymerna ovan. Därför är priset för replikering $0,12 per GiB.
* För enkelhetens skull kan du anta att din käll volym har en konstant 0,5-GiB data ändring varje timme, men den totala förbrukade storleken på volymen inte växer (förblir på 500 GiB). 

Efter den första installationen sker bas linje replikeringen omedelbart.  

* Data mängd replikerad under bas linje replikering: `500 GiB`
* Avgifter för bas linje replikering: `500 GiB * $0.12 = $60`

Efter bas linje replikeringen replikeras endast ändrade block. Därför kommer endast 0,5 GiB av data att replikeras varje timme i de efterföljande stegvisa replikeringarna.

* Summan av data mängden som replikeras mellan stegvisa replikeringar under en 30-dagars månad: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Avgifter för stegvis replikering: `360 GiB * $0.12 = $43.2`

I slutet av månaden 1 är den totala replikeringen av replikering mellan regioner följande:  

*  Total kostnad för replikering mellan regioner från månad 1: `$60 + $43.2 = $103.2`

Den normala Azure NetApp Files lagrings kapacitets avgiften gäller för mål volymen. Men mål volymen kan använda en annan lagrings nivå än (och billigare än) som käll volym nivå.

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Exempel 2: månad 2, stegvisa replikeringar och omsynkronisering av replikeringar  

Anta att du har en käll volym, en mål volym och en replikeringsrelation mellan de två inställningarna som beskrivs i exempel 1. För 29 dagar i den andra månaden (30 dagar per månad) pågick replikeringen varje timme som förväntat.

* Summan av data mängden som replikeras mellan stegvisa replikeringar i 29 dagar: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Anta att det uppstod ett oplanerat avbrott i käll regionen och du växlade till mål volymen på den sista dagen i månaden. Efter 2 timmar återställdes käll regionen och du utförde en omsynkronisering av replikeringen från mål volymen till käll volymen. Under 2 timmar inträffade 0,8 GiB av data ändringar på mål volymen och måste synkroniseras om till källan.

* Summan av data mängden som replikeras över vanliga replikeringar i 22 timmar den senaste dagen: `0.5 GiB * 22 hours = 11 GiB`
* Data mängd replikerat under en omsynkronisering av replikering: `0.8 GiB`

I slutet av månad 2 är därför den totala replikeringen av replikering mellan regioner följande:  

* Total kostnad för replikering mellan regioner från månad 2: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

Vanlig Azure NetApp Files lagrings kapacitet för månad 2 gäller för mål volymen.

## <a name="next-steps"></a>Nästa steg
* [Krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md)
* [Skapa volymreplikering](cross-region-replication-create-peering.md)
* [Visa hälsostatus för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Hantera haveriberedskap](cross-region-replication-manage-disaster-recovery.md)
* [Mått för volym replikering](azure-netapp-files-metrics.md#replication)
* [Ta bort volymreplikeringar eller volymer](cross-region-replication-delete.md)
* [Felsöka replikering mellan regioner](troubleshoot-cross-region-replication.md)


