---
title: Översikt över åter givning
description: Introduktion till att använda Azure för åter givning och en översikt över Azure Batch åter givnings funktioner
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98232122"
---
# <a name="rendering-using-azure"></a>Rendering med hjälp av Azure

Rendering är processen att ta 3D-modeller och konvertera dem till 2D-bilder. 3D-scenens filer skapas i program som Autodesk 3ds Max, Autodesk Maya och Blender.  Att rendera program som Autodesk Maya, Autodesk Arnold, kaos Group V-Ray och blends ger 2D-bilder.  Ibland skapas enkla avbildningar från scen filen. Det är dock vanligt att modellera och återge flera avbildningar och kombinera dem sedan i en animering.

Arbets belastningen för rendering används kraftigt för specialeffekter (VFX) i medie-och underhållnings branschen. Rendering används också inom flera andra branscher som marknadsföring, detaljhandel, olja och gas samt tillverkning.

Processen för åter givning är i beräknings intensiv. Det kan finnas många ramar/bilder att producera och varje bild kan ta flera timmar att rendera.  Åter givning är därför en perfekt grupp bearbetnings arbets belastning som kan utnyttja Azure för att köra många åter givningar parallellt och använda en mängd maskin vara, inklusive GPU: er.

## <a name="why-use-azure-for-rendering"></a>Varför ska jag använda Azure för rendering?

Av många skäl är åter givning en arbets belastning som passar perfekt för Azure:

* Åter givnings jobb kan delas upp i många delar som kan köras parallellt med flera virtuella datorer:
  * Animeringar består av många ramar och varje ram kan återges parallellt.  Ju fler virtuella datorer som är tillgängliga för bearbetning av varje ram, desto snabbare kan alla ramar och animeringen skapas.
  * Med vissa åter givnings program kan enskilda ramar delas upp i flera delar, till exempel paneler eller segment.  Varje del kan återges separat och sedan kombineras till den sista bilden när alla delar har slutförts.  Ju fler virtuella datorer som är tillgängliga, desto snabbare kan en ram återges.
* Åter givnings projekt kan kräva enorma skalning:
  * Enskilda ramar kan vara komplexa och kräver många timmar för att kunna återges även på avancerad maskin vara. animeringar kan bestå av hundratals tusentals ramar.  En enorm mängd data krävs för att återge animeringar med hög kvalitet under en rimlig tids period.  I vissa fall har över 100 000 kärnor använts för att återge tusentals ramar parallellt.
* Åter givnings projekt är projektbaserade och kräver varierande beräknings mängder:
  * Tilldela beräknings-och lagrings kapacitet vid behov kan du skala upp eller ned enligt belastningen under ett projekt och ta bort det när projektet är klart.
  * Betala för kapacitet vid tilldelning, men betala inte för det när det inte finns någon belastning, till exempel mellan projekt.
  * Anpassa till burst-överföring på grund av oväntade ändringar. skala högre om det finns oväntade ändringar sent i ett projekt och dessa ändringar måste bearbetas enligt ett tätt schema.
* Välj bland ett brett utbud av maskin vara enligt program, arbets belastning och tidsram:
  * Det finns ett brett utbud av maskin vara som är tillgänglig i Azure och som kan allokeras och hanteras med batch.
  * Beroende på projektet kan kravet vara för bästa pris/prestanda eller bästa övergripande prestanda.  Olika scener och/eller åter givnings program har olika minnes krav.  Vissa åter givnings program kan utnyttja GPU: er för bästa prestanda eller vissa funktioner. 
* Låg prioritets-eller [dekor datorer](https://azure.microsoft.com/pricing/spot/) minskar kostnaderna:
  * Virtuella datorer med låg prioritet och virtuella datorer är tillgängliga för en stor rabatt jämfört med virtuella standard datorer och lämpar sig för vissa jobb typer.
  
## <a name="existing-on-premises-rendering-environment"></a>Befintlig lokal åter givnings miljö

Det vanligaste fallet är att det finns en befintlig lokal åter givning-grupp som hanteras av ett program för rendering Management, till exempel PipelineFX Qube, den senaste frågan, tids gräns eller ett anpassat program.  Kravet är att utöka den lokala åter givnings Server gruppens kapacitet med hjälp av virtuella Azure-datorer.

Azure-infrastruktur och-tjänster används för att skapa en hybrid miljö där Azure används för att komplettera den lokala kapaciteten. Exempel:

* Använd en [Virtual Network](../virtual-network/virtual-networks-overview.md) för att placera Azure-resurserna i samma nätverk som den lokala åter givnings Server gruppen.
* Använd [AVERT vFXT för Azure](../avere-vfxt/avere-vfxt-overview.md) eller [Azure HPC cache](../hpc-cache/hpc-cache-overview.md) för att cachelagra källfiler i Azure för att minska bandbredds användningen och fördröjningen, vilket maximerar prestandan.
* Se till att den befintliga licens servern finns på det virtuella nätverket och köp de ytterligare licenser som krävs för att tillgodose den extra Azure-baserade kapaciteten.

## <a name="no-existing-render-farm"></a>Ingen befintlig åter givnings grupp

Klient datorerna kan utföra åter givning, men åter givnings belastningen ökar och det tar för lång tid att endast använda arbets Stations kapacitet.

Det finns två tillgängliga huvud alternativ:

* Distribuera en lokal åter givnings hanterare, t. ex. på den lokala åter givningen, och konfigurera en hybrid miljö för att använda Azure när ytterligare kapacitet eller prestanda krävs. En åter givnings hanterare skräddarsys särskilt för att återge arbets belastningar och inkluderar plugin-program för populära klient program, vilket möjliggör enkel åter givning av åter givnings jobb.

* En anpassad lösning som använder Azure Batch för att allokera och hantera beräknings kapaciteten samt för att tillhandahålla jobb schemaläggning för att köra åter givnings jobben.

## <a name="next-steps"></a>Nästa steg

 Lär dig hur du [använder Azure-infrastruktur och-tjänster för att utöka en befintlig lokal åter givnings Server grupp](https://azure.microsoft.com/solutions/high-performance-computing/rendering/).

Läs mer om hur du [Azure Batch åter givnings funktioner](batch-rendering-functionality.md).
