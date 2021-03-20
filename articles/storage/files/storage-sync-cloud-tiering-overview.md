---
title: Förstå Azure File Sync moln nivåer | Microsoft Docs
description: Förstå moln nivåer, en valfri Azure File Sync-funktion. Filer som används ofta cachelagras lokalt på servern. andra nivårar till Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcd58e966da7ca596a14ca1b2839cbeb6399a855
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576466"
---
# <a name="cloud-tiering-overview"></a>Översikt över moln nivåer
Med moln nivåer, en valfri funktion i Azure File Sync, minskar mängden lokal lagring som krävs, samtidigt som prestandan för en lokal fil Server hålls kvar.

När den här funktionen är aktive rad lagras bara ofta använda filer (frekvent) på den lokala servern. Filer som sällan används delas upp i namn området (fil-och mappstruktur) och fil innehåll. Namn området lagras lokalt och fil innehållet som lagras i en Azure-filresurs i molnet. 

När en användare öppnar en skiktad fil, återkallar Azure File Sync sömlöst fil data från fil resursen i Azure.

## <a name="how-cloud-tiering-works"></a>Så här fungerar moln nivåer

### <a name="cloud-tiering-policies"></a>Principer för moln skikt
När du aktiverar moln nivåer finns det två principer som du kan ställa in för att informera Azure File Sync när du ska göra nivå av häftiga filer: **princip för ledigt utrymme på volymen** och **datum principen**. 

#### <a name="volume-free-space-policy"></a>Princip för ledigt utrymme på volym
**Principen för ledigt utrymme på volymen** visar Azure File Sync att nivån är låg frekvent filer i molnet när en viss mängd utrymme tas upp på din lokala disk. 

Om din lokala disk kapacitet till exempel är 200 GB och du vill att minst 40 GB av den lokala disk kapaciteten alltid ska vara kostnads fri, bör du ställa in principen för ledigt utrymme på volymen på 20%. Volymens lediga utrymme gäller volym nivån i stället för på nivån för enskilda kataloger eller Server slut punkter. 

Om du vill veta hur mängden ledigt utrymme på volymen påverkar filer som hämtas från början när en ny server slut punkt läggs till (se avsnittet [principer som påverkar moln nivåer](#sync-policies-that-affect-cloud-tiering)).

#### <a name="date-policy"></a>Datum princip
Med **datum policyn** på nivå av låg frekventa filer till molnet, om de inte har öppnats (dvs. läsning eller skrivning till) för x antal dagar. Om du till exempel har märkt att filer som har varit äldre än 15 dagar utan åtkomst är vanligt vis arkiverade filer, bör du ställa in din datum policy på 15 dagar. 

Fler exempel på hur datum principen och mängden ledigt utrymme på volymen fungerar tillsammans finns i [välj Azure File Sync principer för moln nivå](storage-sync-choose-cloud-tiering-policies.md)hantering.

### <a name="windows-server-data-deduplication"></a>Windows Server-datadeduplicering
Datadeduplicering stöds på volymer som har aktiverat moln skiktning från och med Windows Server 2016. Mer information finns i [Planera för en Azure File Sync distribution](./storage-sync-files-planning.md#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Termisk karta för moln nivåer
Azure File Sync övervakar fil åtkomst (Läs-och skriv åtgärder) över tid och, baserat på hur frekvent och senaste åtkomst är, tilldelar en värme poäng till varje fil. De här poängen används för att bygga en "termisk karta" för ditt namn område på varje server slut punkt. Den här termisk karta är en lista över alla synkroniserade filer på en plats med aktive rad lagrings nivå för molnet. Ofta använda filer som nyligen har öppnats betraktas som frekventa, medan filer som är knappt vidrörta och som inte har använts under en viss tid anses vara häftiga. 

För att fastställa den relativa positionen för en enskild fil i den termisk karta använder systemet det maximala antalet tidsstämplar i följande ordning: MAX (senaste åtkomst tid, senast ändrad tid, skapande tid). 

Normalt spåras senaste åtkomst tid och är tillgänglig. Men när en ny server slut punkt skapas, med aktive rad moln nivå, har inte tillräckligt med tid gått till att observera fil åtkomsten. Om det inte finns någon giltig senaste åtkomst tid används den senast ändrade tiden i stället för att utvärdera den relativa positionen i termisk karta.  

Datum principen fungerar på samma sätt. Utan senaste åtkomst tid kommer datum policyn att vidtas på den senaste ändrings tiden. Om detta inte är tillgängligt kommer det att återgå till skapande tiden för en fil. Med tiden kommer systemet att observera fler och fler fil åtkomst begär Anden och börjar automatiskt att använda den senast spårade senaste åtkomst tiden.

> [!Note]
> Moln nivåer är inte beroende av NTFS-funktionen för att spåra senaste åtkomst tid. Den här NTFS-funktionen är inaktive rad som standard och på grund av prestanda överväganden rekommenderar vi inte att du aktiverar den här funktionen manuellt. Moln nivåer spårar senaste åtkomst tid separat.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Principer för synkronisering som påverkar moln nivåer

Med Azure File Sync agent version 11 finns det två ytterligare Azure File Syncs principer som du kan ange som påverkar moln hantering: **inledande nedladdning** och **proaktiv återanrop**.

#### <a name="initial-download"></a>Inledande nedladdning

När en server ansluter till en Azure-filresurs med filer i den, kan du nu bestämma hur du vill att servern ska hämta fil resurs data från början. När du har aktiverat moln nivå kan du välja mellan två alternativ. 

![En skärm bild av den första hämtningen när moln nivån är aktive rad](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

Det första alternativet är att återkalla namn området först och sedan återkalla fil innehållet efter senaste ändring av tidsstämpel, tills den lokala disk kapaciteten har nåtts. Det här alternativet är idealiskt om det finns tillräckligt med disk utrymme och du vet att filer som senast ändrades ska cachelagras lokalt. Filer som inte kan lagras lokalt på grund av brist på disk utrymme kommer att skiktas.        

Det andra alternativet är att först återkalla namn området och bara återkalla fil innehållet när det används. Det här alternativet är bäst om du vill minimera kapaciteten som används på din lokala disk och vill att användarna ska kunna bestämma vilka filer som ska cachelagras lokalt. Alla filer börjar stängas av som filer i nivå med det här alternativet.

![En skärm bild av den första hämtningen när moln nivån är inaktive rad](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

När moln nivån är inaktive rad har du ett tredje alternativ, vilket innebär att du slipper samla filer på flera nivåer. I den här situationen visas filerna bara på servern när de har laddats ned helt. Om du har program som kräver att fullständiga filer finns och inte kan tolerera filer på nivån i namn området, är detta idealiskt.      

#### <a name="proactive-recalling"></a>Proaktivt återanrop

När en fil skapas eller ändras kan du återkalla en fil på ett proaktivt-servrar som du anger. Detta gör den nya eller ändrade filen lätt att komma åt för förbrukning på varje angiven server. 

Ett globalt distribuerat företag har till exempel avdelnings kontor i USA och i Indien. I morgon (oss tid) informations anställda skapar du en ny mapp och nya filer för ett helt nytt projekt och arbetar hela dagen på den. Azure File Sync synkroniserar mapp och filer med Azure-filresursen (moln slut punkten). Informations anställda i Indien fortsätter att arbeta med projektet i sin tidszon. När de anländer till morgon måste den lokala Azure File Sync-aktiverade servern i Indien ha dessa nya filer tillgängliga lokalt, så att Indien-teamet kan arbeta effektivt från en lokal cache. Att aktivera det här läget förhindrar att den initiala fil åtkomsten går långsammare på grund av återkallande på begäran och gör att servern kan återkalla filerna proaktivt så fort de har ändrats eller skapats i Azure-filresursen.

Om filer som har återkallats till servern inte behövs lokalt, kan det onödiga återställnings jobbet öka den utgående trafiken och kostnaderna. Därför är det bara att aktivera proaktiv återställning när du vet att förifylla en servers cache med de senaste ändringarna från molnet kommer att ha en positiv inverkan på användare eller program som använder filerna på den servern. 

Aktivering av proaktivt återanrop kan också leda till ökad bandbredds användning på servern och kan orsaka att annat relativt nytt innehåll på den lokala servern blir aggressivt på grund av ökningen av filer som återkallas. I sin tur kan detta leda till fler återanrop om filerna som skiktas är aktiva av servrar. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="En bild som visar hämtnings beteendet för Azure File-resurser för en server slut punkt som för närvarande används och en knapp för att öppna en meny som gör det möjligt att ändra den.":::

Mer information om proaktiv återställning finns i [distribuera Azure File Sync](storage-sync-files-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Nivå-jämfört med lokalt cachelagrat fil beteende

Moln skiktning är separationen mellan namn området (filen och mapphierarkin samt fil egenskaper) och fil innehållet. 

#### <a name="tiered-file"></a>Skiktad fil

För nivåbaserade filer är storleken på disken noll eftersom själva fil innehållet inte lagras lokalt. När en fil skiktas, ersätter Azure File Sync fil system filtret (StorageSync.sys) filen lokalt med en pekare (referens punkt). Referens punkten representerar en URL till filen i Azure-filresursen. En fil med flera nivåer har både attributet "offline" och attributet FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS som har angetts i NTFS så att tredjepartsprogram kan identifiera nivåbaserade filer på ett säkert sätt.   

![En skärm bild av en fils egenskaper när den har ett skiktat namn område.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Lokalt cachelagrad fil

Å andra sidan, för en fil som lagras på en lokal fil server, är storleken på disken ungefär lika med filens logiska storlek sedan hela filen (filattribut + fil innehåll) lagras lokalt.     

![En skärm bild av en fils egenskaper när den inte har skiktat-namnrymd + fil innehåll.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Det är också möjligt för en fil att delvis skiktas (eller delvis återkallas). I en delvis skiktad fil finns en del av filen på disk. Detta kan inträffa när filer delvis läses av program som multimedie spelare eller zip-verktyg som har stöd för direkt åtkomst till filer. Azure File Sync är smart och återkallar bara den begärda informationen från den anslutna Azure-filresursen.

> [!NOTE]
> Storlek representerar filens logiska storlek. Storlek på disk representerar den fysiska storleken på fil strömmen som lagras på disken.

## <a name="next-steps"></a>Nästa steg
* [Välj Azure File Sync principer för moln nivåer](storage-sync-choose-cloud-tiering-policies.md)
* [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)