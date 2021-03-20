---
title: Flytta Azure-resurser till en annan region
description: Ger en översikt över hur du flyttar Azure-resurser i Azure-regioner.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90007646"
---
# <a name="moving-azure-resources-across-regions"></a>Flytta Azure-resurser mellan regioner

Den här artikeln innehåller information om hur du flyttar Azure-resurser i Azure-regioner.

Azures geografiska områden, regioner och tillgänglighets zoner utgör grunden i den globala Azure-infrastrukturen. Azures [geografiska](https://azure.microsoft.com/global-infrastructure/geographies/) områden innehåller vanligt vis två eller flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/). En region är ett område inom ett geografiskt område som innehåller Tillgänglighetszoner och flera data Center. 

När du har distribuerat resurser i en särskild Azure-region finns det ett antal orsaker till att du kanske vill flytta resurser till en annan region.

- **Justera till en regions lansering**: flytta dina resurser till en nyligen introducerad Azure-region som inte var tillgänglig tidigare.
- **Justera för tjänster/funktioner**: flytta resurser för att dra nytta av tjänster eller funktioner som är tillgängliga i en speciell region.
- **Svara på affärs utveckling**: flytta resurser till en region som svar på affärs ändringar, till exempel fusioner eller förvärv.
- **Justera mot närhet**: flytta resurser till en region som är lokal för din verksamhet.
- **Uppfylla data kraven**: flytta resurser så att de överensstämmer med data placering-krav eller data klassificerings behov. [Läs mer](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Svara på distributions krav**: flytta resurser som har distribuerats i fel eller flytta som svar på kapacitets behoven. 
- **Svara på** inaktive ring: flytta resurser på grund av inaktive ring av regioner.

## <a name="move-resources-with-resource-mover"></a>Flytta resurser med resurs förflyttning

Du kan flytta resurser till en annan region med [Azure Resource-arbetskraften](../../resource-mover/overview.md). Resurs förflyttning ger:

- En enda hubb för att flytta resurser mellan regioner.
- Minskad flytt tid och komplexitet. Allt du behöver finns på en enda plats.
- En enkel och konsekvent upplevelse för att flytta olika typer av Azure-resurser.
- Ett enkelt sätt att identifiera beroenden för resurser som du vill flytta. Detta hjälper dig att flytta relaterade resurser tillsammans, så att allt fungerar som förväntat i mål regionen efter flytten.
- Automatisk rensning av resurser i käll regionen, om du vill ta bort dem efter flytten.
- Prestandatester. Du kan prova en flytt och sedan ta bort den om du inte vill göra en fullständig flytt.

Du kan flytta resurser till en annan region med ett par olika metoder:

- **Börja flytta resurser från en resurs grupp**: med den här metoden tar du bort regionen från en resurs grupp. När du har valt de resurser som du vill flytta fortsätter processen i resurs förflyttnings hubben, för att kontrol lera resurs beroenden och dirigera flyttnings processen. [Läs mer](../../resource-mover/move-region-within-resource-group.md).
- **Börja flytta resurser direkt från resurs förflyttnings hubben**: med den här metoden avslutar du flytt processen för regioner direkt i hubben. [Läs mer](../../resource-mover/tutorial-move-region-virtual-machines.md).


## <a name="support-for-region-move"></a>Stöd för flyttning av region

Du kan för närvarande använda resurs förflyttning för att flytta resurserna till en annan region:

- Virtuella Azure-datorer och tillhör ande diskar
- Nätverkskort
- Tillgänglighetsuppsättningar
- Virtuella Azure-nätverk
- Offentliga IP-adresser
- Nätverkssäkerhetsgrupper (NSG:er)
- Interna och offentliga belastningsutjämnare
- Azure SQL-databaser och elastiska pooler

## <a name="region-move-process"></a>Process för flyttning av region

Den faktiska processen för att flytta resurser mellan regioner beror på vilka resurser du flyttar. Det finns dock några vanliga viktiga steg:

1. **Verifiera förutsättningar**: förutsättningarna är att se till att de resurser du behöver är tillgängliga i mål regionen, kontrol lera att du har tillräckligt med kvot och verifiera att din prenumeration har åtkomst till mål regionen.
2. **Analysera beroenden**: dina resurser kan ha beroenden på andra resurser. Innan du flyttar kan du räkna ut beroenden så att flyttade resurser fortsätter att fungera som förväntat efter flytten.
3. **Förbered för flytt**: det här är de steg du utför i din primära region innan du går vidare. Du kan till exempel behöva exportera en Azure Resource Manager mall eller börja replikera resurser från källa till mål.
4. **Flytta resurserna**: hur du flyttar resurser beror på vad de är. Du kan behöva distribuera en mall i mål regionen eller redundansväxla resurser till målet.
5. **Ta bort mål resurser**: när du har flyttat resurser kanske du vill ta en titt på resurserna nu i mål regionen och bestämma om det finns något som du inte behöver.
6. **Genomför flyttningen**: när du har verifierat resurser i mål regionen kan vissa resurser kräva en slutgiltig inchecknings åtgärd. I en mål region som nu är den primära regionen kan du behöva konfigurera haveri beredskap till en ny sekundär region. 
7. **Rensa källan**: När allt är igång i den nya regionen kan du rensa upp och ta bort resurser som du har skapat för flytten och resurserna i din primära region.



## <a name="next-steps"></a>Nästa steg

[Läs mer](../../resource-mover/about-move-process.md) om flytt processen i resurs förflyttning.
