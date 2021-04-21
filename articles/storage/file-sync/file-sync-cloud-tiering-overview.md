---
title: Förstå Azure File Sync för molnnivåindelad | Microsoft Docs
description: Förstå molnnivåindelad, en valfri Azure File Sync funktion. Filer som används ofta cachelagras lokalt på servern. andra är nivåindelade i Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f58aa4e2aa4637dfb9fc8b29c52209975e3e367
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797382"
---
# <a name="cloud-tiering-overview"></a>Översikt över molnnivåindelad
Molnnivåindelning, en valfri funktion i Azure File Sync, minskar mängden lokal lagring som krävs samtidigt som prestandan för en lokal filserver finns kvar.

När den här funktionen är aktiverad lagras endast filer som används ofta (frekvent) på den lokala servern. Filer som används sällan (cool) delas upp i namnrymd (fil- och mappstruktur) och filinnehåll. Namnområdet lagras lokalt och filinnehållet lagras i en Azure-filresurs i molnet. 

När en användare öppnar en nivåindelad fil Azure File Sync återkallar sömlöst fildata från filresursen i Azure.

## <a name="how-cloud-tiering-works"></a>Så här fungerar molnnivåindelad

### <a name="cloud-tiering-policies"></a>Principer för molnnivåindelad lagring
När du aktiverar molnnivåindelad lagring finns det två principer som du kan  ange för att informera Azure File Sync när du ska nivåindelad lågnivåfiler: principen för ledigt volymutrymme och **datumprincipen**. 

#### <a name="volume-free-space-policy"></a>Princip för ledigt utrymme på volym
Principen **för ledigt utrymme** på volymen Azure File Sync att nivåindelad lagring av filer till molnet när en viss mängd utrymme tas upp på den lokala disken. 

Om din lokala diskkapacitet till exempel är 200 GB och du vill att minst 40 GB av din lokala diskkapacitet alltid ska vara kostnadsfri bör du ställa in principen för ledigt utrymme på 20 %. Volymens lediga utrymme gäller på volymnivå i stället för på nivån för enskilda kataloger eller serverslutpunkter. 

Information om hur principen för ledigt utrymme på volymen påverkar filer som ursprungligen hämtades när en ny serverslutpunkt läggs till finns i avsnittet Synkronisera principer som påverkar [molnnivåindelad](#sync-policies-that-affect-cloud-tiering)nivå).

#### <a name="date-policy"></a>Datumprincip
Med **datumprincipen** nivåindelades coola filer i molnet om de inte har använts (det vill säga läsa eller skriva till) under x antal dagar. Om du till exempel har märkt att filer som har gått mer än 15 dagar utan att ha åtkomst är arkiveringsfiler, bör du ange datumprincipen till 15 dagar. 

Fler exempel på hur datumprincipen och principen för ledigt volymutrymme fungerar tillsammans finns i [Välj Azure File Sync för molnnivåindelad lagring.](file-sync-choose-cloud-tiering-policies.md)

### <a name="windows-server-data-deduplication"></a>Windows Server-datadeduplicering
Datadeduplicering stöds på volymer som har molnnivåindelad lagring aktiverat från och med Windows Server 2016. Mer information finns i Planera [för en Azure File Sync distribution.](file-sync-planning.md#data-deduplication)

### <a name="cloud-tiering-heatmap"></a>Heatmap för molnnivåindelad
Azure File Sync övervakar filåtkomst (läs- och skrivåtgärder) över tid och tilldelar, baserat på hur frekvent och senaste åtkomst är, en värmepoäng till varje fil. Den använder dessa poäng för att skapa en "heatmap" av ditt namnområde på varje serverslutpunkt. Den här heatmap är en lista över alla synkroniserade filer på en plats med molnnivåindelad aktiverad, sorterade efter deras värmepoäng. Filer som används ofta och som nyligen öppnats betraktas som frekventa, medan filer som har använts vidrörts och som inte har använts under en viss tid anses vara coola. 

För att fastställa den relativa positionen för en enskild fil i den heatmap använder systemet det maximala antalet dess tidsstämplar, i följande ordning: MAX(Senaste åtkomsttid, Senaste ändringstid, Skapandetid). 

Vanligtvis spåras och är senaste åtkomsttid tillgänglig. Men när en ny serverslutpunkt skapas, med molnnivåindelning aktiverad, har det inte gått tillräckligt med tid för att observera filåtkomsten. Om det inte finns någon giltig senaste åtkomsttid används den senaste ändringstiden i stället för att utvärdera den relativa positionen i heatmap.  

Datumprincipen fungerar på samma sätt. Utan en senaste åtkomsttid kommer datumprincipen att agera vid tidpunkten för senaste ändring. Om den inte är tillgänglig kommer den att gå tillbaka till tidpunkten för att skapa en fil. Med tiden kommer systemet att observera fler och fler begäranden om filåtkomst och automatiskt börja använda den självspårade senaste åtkomsttiden.

> [!Note]
> Molnnivåindelad lagring är inte beroende av NTFS-funktionen för spårning av senaste åtkomsttid. Den här NTFS-funktionen är inaktiverad som standard och på grund av prestandaöverväganden rekommenderar vi inte att du aktiverar den här funktionen manuellt. Molnnivåindelad lagring spårar senaste åtkomsttid separat.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Synkroniseringsprinciper som påverkar molnnivåindelad

Med Azure File Sync agentversion 11 finns det två ytterligare principer Azure File Sync som du kan ange som påverkar molnnivåindelad: **inledande** nedladdning och **proaktivt återkallande.**

#### <a name="initial-download"></a>Första nedladdningen

När en server ansluter till en Azure-filresurs med filer i den kan du nu bestämma hur du vill att servern ursprungligen ska ladda ned filresursdata. När molnnivåindelad lagring är aktiverat har du två alternativ. 

![En skärmbild av den första nedladdningen när molnnivåindelad lagring är aktiverad](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

Det första alternativet är att återkalla namnområdet först och sedan återkalla filinnehållet genom att senast ändra tidsstämpeln tills den lokala diskkapaciteten har nåtts. Om du har tillräckligt med diskutrymme och du vet att filer som senast har ändrats ska cachelagras lokalt är det här alternativet idealiskt. Filer som inte kan lagras lokalt på grund av brist på diskutrymme nivåindelade.        

Det andra alternativet är att först återkalla endast namnområdet och återkalla filinnehållet endast när det används. Det här alternativet är bäst om du vill minimera kapaciteten som används på den lokala disken och vill att användarna ska bestämma vilka filer som ska cachelagras lokalt. Alla filer börjar som nivåindelade filer med det här alternativet.

![En skärmbild av den första nedladdningen när molnnivåindelad är inaktiverad](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

När molnnivåindelad är inaktiverad har du ett tredje alternativ, som är att undvika nivåindelade filer på samma sätt. I så fall visas filerna bara på servern när de har laddats ned helt. Om du har program som kräver att fullständiga filer finns och inte tolererar nivåindelade filer i dess namnområde är detta idealiskt.      

#### <a name="proactive-recalling"></a>Proaktiv återkallning

När en fil skapas eller ändras kan du proaktivt återkalla en fil till servrar som du anger. Detta gör den nya eller ändrade filen lättillgänglig för användning på varje angiven server. 

Ett globalt distribuerat företag har till exempel avdelningskontor i USA och Indien. På morgonen (amerikansk tid) skapar informationsarbetare en ny mapp och nya filer för ett helt nytt projekt och arbetar hela dagen med det. Azure File Sync mappar och filer till Azure-filresursen (molnslutpunkt). Informationsarbetare i Indien fortsätter att arbeta med projektet i sin tidszon. När de anländer på morgonen måste den lokala Azure File Sync-aktiverade servern i Indien ha dessa nya filer tillgängliga lokalt, så att Indien-teamet effektivt kan arbeta bort från en lokal cache. Aktivering av det här läget förhindrar att den första filåtkomsten går långsammare på grund av återkallelse på begäran och gör att servern proaktivt kan återkalla filerna så fort de har ändrats eller skapats i Azure-filresursen.

Om filer som återkallas till servern faktiskt inte behövs lokalt kan den onödiga återkallningen öka utgående trafik och kostnader. Därför bör du endast aktivera proaktiv återkallning när du vet att förifylld en servers cache med de senaste ändringarna från molnet kommer att ha en positiv effekt på användare eller program som använder filerna på servern. 

Aktivering av proaktiv återkallning kan också resultera i ökad bandbreddsanvändning på servern och kan orsaka att annat relativt nytt innehåll på den lokala servern är aggressivt nivåindelat på grund av den ökade antalet filer som återkallas. Detta kan i sin tur leda till fler återkallningar om de filer som nivåindelade anses vara heta av servrar. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="En bild som visar nedladdningsbeteendet för Azure-filresursen för en serverslutpunkt som för närvarande gäller och en knapp för att öppna en meny som tillåter att den ändras.":::

Mer information om proaktiv återkallning finns i [Distribuera Azure File Sync](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Beteende för nivåindelade och lokalt cachelagrade filer

Molnnivåindelning är avgränsningen mellan namnområdet (fil- och mapphierarkin samt filegenskaper) och filinnehållet. 

#### <a name="tiered-file"></a>Nivåindelad fil

För nivåindelade filer är storleken på disken noll eftersom själva filinnehållet inte lagras lokalt. När en fil är nivåindelad ersätter Azure File Sync filsystemfiltret (StorageSync.sys) filen lokalt med en pekare (reparse point). Reparspunkten representerar en URL till filen i Azure-filresursen. En nivåindelad fil har både attributet "offline" och attributet FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS i NTFS så att program från tredje part på ett säkert sätt kan identifiera nivåindelade filer.   

![En skärmbild av en fils egenskaper när den är nivåindelad – endast namnområde.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Lokalt cachelagrad fil

Å andra sidan, för en fil som lagras på en lokal filserver, är storleken på disken ungefär lika med den logiska storleken på filen eftersom hela filen (filattribut + filinnehåll) lagras lokalt.     

![En skärmbild av en fils egenskaper när den inte är nivåindelad – namnområde + filinnehåll.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Det är också möjligt för en fil att vara delvis nivåindelad (eller delvis återkallad). I en delvis nivåindelad fil finns en del av filen på disken. Detta kan inträffa när filer delvis läses av program som multimediaspelare eller zip-verktyg som stöder strömmande åtkomst till filer. Azure File Sync är smart och återkallar endast den begärda informationen från den anslutna Azure-filresursen.

> [!NOTE]
> Storlek representerar filens logiska storlek. Storleken på disken representerar den fysiska storleken på filströmmen som lagras på disken.

## <a name="next-steps"></a>Nästa steg

* [Välj Azure File Sync för molnnivåindelad lagring](file-sync-choose-cloud-tiering-policies.md)
* [Planera för distribution av Azure File Sync](file-sync-planning.md)