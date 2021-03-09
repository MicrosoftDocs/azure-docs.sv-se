---
title: ta med fil
description: ta med fil
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: fc61c111291f9862f71f9a81828fa0fa828ab2ad
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510654"
---
Azure Compute erbjuder storlekar för virtuella datorer som är isolerade till en viss maskin varu typ och som är dedikerad till en enda kund. De isolerade storlekarna är aktiva och fungerar på en speciell maskin varu generation och kommer att bli föråldrade när maskin varu genereringen dras tillbaka.

Isolerade storlekar på virtuella datorer lämpar sig bäst för arbets belastningar som kräver en hög grad av isolering från andra kunders arbets belastningar av skäl som innehåller krav på efterlevnad och regler.  Användning av en isolerad storlek garanterar att den virtuella datorn är den enda som körs på den aktuella Server instansen. 


Dessutom kan kunderna välja att dela upp resurserna för de virtuella datorerna med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)eftersom de isolerade virtuella datorerna är stora.

De aktuella isolerade virtuella dator erbjudandena är:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5


> [!NOTE]
> Isolerade VM-storlekar har en maskinvaru begränsad livs längd. Mer information finns nedan.

## <a name="deprecation-of-isolated-vm-sizes"></a>Utfasning av isolerade VM-storlekar

Isolerade VM-storlekar har en maskinvaru begränsad livs längd. Azure utfärdar påminnelser 12 månader före det officiella datumet för utfasnings datumet i storlekarna och kommer att tillhandahålla ett uppdaterat, isolerat erbjudande för ditt övervägande.

| Storlek | Datum för isolerings Pension | 
| --- | --- |
| Standard_DS15_v2 | 15 maj, 2021 |
| Standard_D15_v2  | 15 maj, 2021 |
| Standard_G5  | 15 februari 2022 |
| Standard_GS5  | 15 februari 2022 |
| Standard_E64i_v3  | 15 februari 2022 |
| Standard_E64is_v3  | 15 februari 2022 |


## <a name="faq"></a>Vanliga frågor
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>F: är storleken på att bli föråldrad eller bara dess isolerings funktion?
**A**: för närvarande dras bara isolerings funktionen för VM-storlek ned. De föråldrade isolerade storlekarna kommer fortfarande att finnas i icke isolerade tillstånd. Om isolering inte behövs, finns det ingen åtgärd som kan vidtas och den virtuella datorn fortsätter att fungera som förväntat.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>F: finns det en nedtid när min virtuella dator hamnar på en icke isolerad maskin vara?
**A**: om det inte finns något behov av isolering behövs ingen åtgärd och det kommer inte att finnas något avbrott. Vid motsats om isolering krävs kommer vårt meddelande att inkludera den rekommenderade ersättnings storleken. Om du väljer ersättnings storleken måste våra kunder ändra storlek på sina virtuella datorer.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>F: kostar det någon kostnad delta för att flytta till en icke-isolerad virtuell dator?
**A**: Nej

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>F: När är de andra isolerade storlekarna som ska tas ur bruk?
S **: vi** kommer att tillhandahålla påminnelser 12 månader före den officiella utfasningen av den isolerade storleken. Vårt senaste meddelande innefattar isolerings funktion för att dra tillbaka Standard_G5, Standard_GS5 Standard_E64i_v3 och Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>F: Jag är en Azure Service Fabric-kund som förlitar sig på silver-eller Gold-nivåerna. Påverkar den här ändringen mig?
**A**: Nej. De garantier som tillhandahålls av Service Fabrics [hållbarhets nivåer](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) kommer att fortsätta att fungera även efter den här ändringen. Om du behöver en fysisk maskin varu isolering av andra orsaker kan du fortfarande behöva vidta en av de åtgärder som beskrivs ovan. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>F: vilka är mil stolparna för D15_v2 eller DS15_v2 isoleringen? 
**A**: 
 
| Datum | Action |
|---|---| 
| 15 maj, 2020<sup>1</sup> | D/DS15_v2 överdragnings meddelande vid isolering| 
| 15 maj, 2021 | D/DS15_v2 isolerings garanti borttagen| 

<sup>1</sup> befintlig kund som använder dessa storlekar får ett meddelande via e-post med detaljerade anvisningar om nästa steg.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>F: vilka är mil stolparna för G5, Gs5, E64i_v3 och E64is_v3 isolering? 
**A**: 
 
| Datum | Action |
|---|---|
| Feb 15, 2021<sup>1</sup> | Meddelande om borttagning av G5/GS5/E64i_v3/E64is_v3 isolering |
| Feb 15, 2022 | G5/GS5/E64i_v3/E64is_v3 isolerings garanti tas bort |

<sup>1</sup> befintlig kund som använder dessa storlekar får ett meddelande via e-post med detaljerade anvisningar om nästa steg.  

## <a name="next-steps"></a>Nästa steg

Kunder kan också välja att ytterligare dela upp resurserna i de isolerade virtuella datorerna med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
