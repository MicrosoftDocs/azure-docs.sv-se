---
title: SAP HANA tillgänglighet i Azure-regioner | Microsoft Docs
description: En översikt över tillgänglighets överväganden när du kör SAP HANA på virtuella Azure-datorer i flera Azure-regioner.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f925e72493a94bcdbde64bdfbcef6acf83977f52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669727"
---
# <a name="sap-hana-availability-across-azure-regions"></a>SAP HANA tillgänglighet i Azure-regioner

I den här artikeln beskrivs scenarier som rör SAP HANA tillgänglighet i olika Azure-regioner. På grund av avståndet mellan Azure-regioner innebär det särskilda överväganden att ställa in SAP HANA tillgänglighet i flera Azure-regioner.

## <a name="why-deploy-across-multiple-azure-regions"></a>Varför distribuera i flera Azure-regioner

Azure-regioner separeras ofta med stora avstånd. Beroende på vilken region som används kan avståndet mellan Azure-regioner vara hundratals mil eller till och med flera tusen mil, som i USA. På grund av avståndet är nätverks trafiken mellan till gångar som distribueras i två olika Azure-regioner ett betydande svars tid för nätverks fördröjning. Svars tiden är tillräckligt stor för att utesluta synkront data utbyte mellan två SAP HANA instanser under vanliga SAP-arbetsbelastningar. 

Å andra sidan har organisationer ofta ett avstånds krav mellan platsen för det primära data centret och ett sekundärt Data Center. Ett avstånds krav gör det möjligt att tillhandahålla tillgänglighet om en naturlig katastrof inträffar på en bredare geografisk plats. Exempel på detta är orkaner som träffar i Karibien och Florida i september och oktober 2017. Din organisation kan ha minst ett minsta avstånds krav. För de flesta Azure-kunder kräver en minimal avstånds definition att du kan utforma för tillgänglighet i [Azure-regioner](https://azure.microsoft.com/regions/). Eftersom avståndet mellan två Azure-regioner är för stort för att använda HANA-läget HANA synkron replikering, kan RTO-och återställnings kraven tvinga dig att distribuera tillgänglighets konfigurationerna i en region och sedan komplettera med ytterligare distributioner i en andra region.

En annan aspekt att tänka på i det här scenariot är redundans och omdirigering av klienter. Antagandet är att en redundansväxling mellan SAP HANA instanser i två olika Azure-regioner alltid är en manuell redundansväxling. Eftersom replikeringsläget för SAP HANA systemreplikering är inställt på asynkron, finns det en risk att data som har allokerats i den primära HANA-instansen ännu inte har gjort det till den sekundära HANA-instansen. Därför är automatisk redundans inte ett alternativ för konfigurationer där replikeringen är asynkron. Även med manuellt kontrollerad redundans, som vid en redundansväxling, måste du vidta åtgärder för att se till att alla allokerade data på den primära sidan har gjorts till den sekundära instansen innan du flyttar över till den andra Azure-regionen manuellt.
 
Azure Virtual Network använder ett annat IP-adressintervall. IP-adresserna distribueras i den andra Azure-regionen. Så du måste ändra SAP HANA klient konfigurationen eller helst behöver du skapa steg för att ändra namn matchningen. På så sätt omdirigeras klienterna till den nya sekundära platsens Server-IP-adress. Mer information finns i SAP-artikeln [återställning av klient anslutning efter övertag](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html)Ande.   

## <a name="simple-availability-between-two-azure-regions"></a>Enkel tillgänglighet mellan två Azure-regioner

Du kan välja att inte placera någon tillgänglighets konfiguration på plats inom en region, men fortfarande ha behov av att hantera arbets belastningen om en katastrof inträffar. Vanliga fall för sådana scenarier är inte produktions system. Även om systemet är nere i en halvtimme eller till och med en dag är hållbart, kan du inte tillåta att systemet inte är tillgängligt i 48 timmar eller mer. Om du vill göra installationen billigare kan du köra ett annat system som är ännu mindre viktigt på den virtuella datorn. Det andra systemet fungerar som ett mål. Du kan också ändra storleken på den virtuella datorn i den sekundära regionen så att den blir mindre och välja att inte förinstallera data. Eftersom redundansväxlingen är manuell och har många fler steg för att redundansväxla den fullständiga programstacken, är det ytterligare tid att stänga av den virtuella datorn, ändra storlek på den och sedan starta om den virtuella datorn.

Om du använder scenariot med att dela DR-målet med ett system för frågor och svar i en virtuell dator måste du tänka på följande:

- Det finns två [Åtgärds lägen](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) med delta_datashipping-och logreplay, som är tillgängliga för ett sådant scenario
- Båda åtgärds lägena har olika minnes krav utan att förladda data
- Delta_datashipping kan kräva mycket mindre minne utan Förhandsladda alternativ än logreplay kan kräva. Se kapitel 4,3 i SAP-dokumentet [hur du utför systemreplikering för SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Minnes kravet för logreplay Operations mode utan preload är inte deterministisk och är beroende av de columnstore-strukturer som lästs in. I extrema fall kan du behöva 50% av minnet för den primära instansen. Minnet för logreplay-arbetsläget är oberoende av om du väljer att använda förinstallerade data eller inte.


![Diagram över två virtuella datorer över två regioner](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> I den här konfigurationen kan du inte ange en återställnings punkt = 0 eftersom HANA-systemets replikeringsläget är asynkront. Om du behöver ange en återställnings punkt = 0 är den här konfigurationen inte den konfiguration du väljer.

En liten ändring som du kan göra i konfigurationen kan vara att konfigurera data som för inläsning. Men med hänsyn till den manuella typen av redundans och det faktum att program lager också måste gå över till den andra regionen, kanske det inte går att använda Förhandsladda data. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinera tillgänglighet inom en region och mellan regioner 

En kombination av tillgänglighet inom och mellan regioner kan drivas av följande faktorer:

- Ett krav för återställnings punkt = 0 inom en Azure-region.
- Organisationen är inte villig eller kan ha globala åtgärder som påverkas av en stor naturlig Catastrophe som påverkar en större region. Detta var fallet för vissa orkaner som nådde Karibien under de senaste åren.
- Regler som kräver distans avstånd mellan primära och sekundära platser som är tydligare än vad Azures tillgänglighets zoner kan tillhandahålla.

I dessa fall kan du ställa in vad SAP anropar en [SAP HANA konfiguration av system konfigurationen](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) på flera nivåer med hjälp av Hana-systemreplikering. Arkitekturen skulle se ut så här:

![Diagram över tre virtuella datorer över två regioner](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP introducerade [system replikering med flera mål](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) med Hana 2,0 SPS3. System replikering med flera mål ger vissa fördelar i uppdaterings scenarier. Till exempel påverkas inte DR-webbplatsen (region 2) när den sekundära HA-platsen är nere för underhåll eller uppdateringar. Du kan få mer information om HANA-systemreplikering med flera mål [här](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Möjlig arkitektur med multi-Target-replikering ser ut så här:

![Diagram över tre virtuella datorer över två regioner Milti-Target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Om organisationen har krav för beredskap för hög tillgänglighet i den andra (DR) Azure-regionen skulle arkitekturen se ut så här:

![Diagram som visar en organisation som har krav för beredskap för hög tillgänglighet i den andra (DR) Azure-regionen.](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Med logreplay som åtgärds läge tillhandahåller den här konfigurationen en återställnings punkt = 0, med låg RTO, inom den primära regionen. Konfigurationen tillhandahåller också vettigt-återställnings punkt om en flytt till den andra regionen är involverad. RTO-tiderna i den andra regionen är beroende av om data har förinstallerats. Många kunder använder den virtuella datorn i den sekundära regionen för att köra ett test system. I detta fall kan data inte förinstalleras.

> [!IMPORTANT]
> Drifts lägena mellan olika nivåer måste vara homogena. Du **kan inte** använda logreply som åtgärds läge mellan nivå 1 och nivå 2 och delta_datashipping att tillhandahålla nivå 3. Du kan bara välja ett eller det andra åtgärds läge som måste vara konsekvent för alla nivåer. Eftersom delta_datashipping inte är lämplig för att ge dig en återställnings punkt = 0, är det enda rimliga åtgärds läget för en sådan konfiguration med flera nivåer fortfarande logreplay. Mer information om åtgärds lägen och vissa begränsningar finns i [Åtgärds lägena för SAP-artiklar för SAP HANA systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Nästa steg

Steg för steg-anvisningar om hur du konfigurerar dessa konfigurationer i Azure finns i:

- [Konfigurera SAP HANA system replikering på virtuella Azure-datorer](sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA med hjälp av systemreplikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
