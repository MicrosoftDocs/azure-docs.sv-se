---
title: Förstå Azure Files fakturering | Microsoft Docs
description: Lär dig hur du tolkar de etablerade och betala per användning-faktureringsmodellerna för Azure-filresurser.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11d22fd83106bb1802514d0c7d5f67724664464d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788393"
---
# <a name="understand-azure-files-billing"></a>Förstå Azure Files fakturering
Azure Files har två olika faktureringsmodeller: etablerade och betala per förbrukning. Den etablerade modellen är endast tillgänglig för premiumfilresurser, som är filresurser som distribueras i **lagringskontotyp FileStorage.** Modellen med användningsbaserade betalning är endast tillgänglig för standardfilresurser, som är filresurser som distribueras i **GPv2-lagringskontotyp (general purpose version 2).** Den här artikeln förklarar hur båda modellerna fungerar för att hjälpa dig att förstå din månatliga Azure Files faktura.

Mer Azure Files prisinformation finns på [Azure Files sidan med priser.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="storage-units"></a>Lagringsenheter    
Azure Files använder base-2-måttenheter för att representera lagringskapacitet: KiB, MiB, GiB och TiB. Operativsystemet kanske eller kanske inte använder samma mått- eller inventeringssystem.

### <a name="windows"></a>Windows

Både Windows-operativsystemet och Azure Files lagringskapacitet med hjälp av räkningen base-2, men det finns en skillnad när du etiketterar enheter. Azure Files etiketterar lagringskapaciteten med bas 2 måttenheter medan Windows etiketterar lagringskapaciteten i bas 10 måttenheter. När du rapporterar lagringskapacitet konverterar Inte Windows sin lagringskapacitet från base-2 till base-10.

|Akronym  |Definition  |Enhet  |Windows visas som  |
|---------|---------|---------|---------|
|Kib     |1 024 byte         |kibibyte         |KB (kilobyte)         |
|Mib     |1 024 KiB (1 048 576 byte)         |mebibyte         |MB (megabyte)         |
|Gib     |1 024 MiB (1 073 741 824 byte)         |gibibyte         |GB (gigabyte)         |
|Tib     |1 024 GiB (1 099 511 627 776 byte)         |tebibyte         |TB (terabyte)         |

### <a name="macos"></a>macOS

Se [Hur iOS- och macOS rapporterar lagringskapacitet](https://support.apple.com/HT201402) på Apples webbplats för att avgöra vilket beräkningssystem som används.

### <a name="linux"></a>Linux

Ett annat beräkningssystem kan användas av varje operativsystem eller enskild programvara. Se deras dokumentation för att avgöra hur de rapporterar lagringskapacitet.

## <a name="provisioned-model"></a>Etablerad modell
Azure Files använder en etablerad modell för premiumfilresurser. I en etablerat affärsmodell kan du proaktivt ange för Azure Files-tjänsten vilka dina lagringskrav är, i stället för att debiteras baserat på vad du använder. Detta påminner om att köpa maskinvara lokalt. När du etablerar en Azure-filresurs med en viss mängd lagringsutrymme betalar du för lagringen oavsett om du använder den eller inte, precis som du inte börjar betala kostnaderna för fysiska medier lokalt när du börjar använda utrymme. Till skillnad från att köpa fysiska media lokalt kan etablerade filresurser skalas upp eller ned dynamiskt beroende på dina lagrings- och I//S-prestandaegenskaper.

När du etablerar en Premium-filresurs anger du hur många GiBs din arbetsbelastning kräver. Varje GiB som du etablerar berättigar dig till ytterligare IOPS och dataflöde med ett fast förhållande. Förutom baslinje-IOPS som du är garanterad för stöder varje Premium-filresurs bursting efter bästa prestanda. Formlerna för IOPS och dataflöde är följande:

- Baslinje-IOPS = 400 + 1 * etablerat GiB. (Upp till högst 100 000 IOPS).
- Burst Limit = MAX(4000, 3 * Baslinje-IOPS).
- Utgående hastighet = 60 MiB/s + 0,06 * etablerat GiB.
- Ingressfrekvens = 40 MiB/s + 0,04 * etablerat GiB.

Den etablerade storleken på filresursen kan ökas när som helst, men kan bara minskas efter 24 timmar sedan den senaste ökningen. När du har väntat i 24 timmar utan en kvotökning kan du minska resurskvoten så många gånger du vill tills du ökar den igen. Skalningsändringar för IOPS/dataflöde börjar gälla inom några minuter efter att den etablerade storleken har ändrats.

Du kan minska storleken på din etablerade resurs under din använda GiB. Om du gör detta förlorar du inte data, men du debiteras fortfarande för den storlek som används och får prestanda (baslinje-IOPS, dataflöde och burst-IOPS) för den etablerade resursen, inte den storlek som används.

I följande tabell visas några exempel på dessa formler för de etablerade resursstorlekarna:

|Kapacitet (GiB) | Baslinje-IOPS | Burst-IOPS | Utgående (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Upp till 4 000     | 66   | 44   |
|500         | 900     | Upp till 4 000  | 90   | 60   |
|1,024       | 1,424   | Upp till 4 000   | 122   | 81   |
|5,120       | 5,520   | Upp till 15 360  | 368   | 245   |
|10,240      | 10,640  | Upp till 30 720  | 675   | 450   |
|33,792      | 34,192  | Upp till 100 000 | 2,088 | 1,392   |
|51,200      | 51,600  | Upp till 100 000 | 3,132 | 2,088   |
|102,400     | 100 000 | Upp till 100 000 | 6,204 | 4,136   |

Effektiv filresursprestanda omfattas av nätverksbegränsningar för datorn, tillgänglig nätverksbandbredd, I/O-storlekar, parallellitet och många andra faktorer. Baserat på intern testning med 8 KiB-storlekar för läs-/skriv-I/O kan en enskild virtuell Windows-dator utan SMB Multichannel aktiverad, *Standard F16s_v2*, ansluten till premiumfilresurs över SMB uppnå 20 000 läs-IOPS och 15 000 skriv-IOPS. Med 512 MiB-storlekar för läs-/skriv-I/O kan samma virtuella dator uppnå 1,1 GiB/s utgående dataflöde och 370 MiB/s ingress-dataflöde. Samma klient kan uppnå upp till \~ 3 x prestanda om SMB Multichannel är aktiverat på Premium-resurser. För att uppnå maximal [prestandaskala aktiverar du SMB Multichannel](storage-files-enable-smb-multichannel.md) och sprider belastningen över flera virtuella datorer. Se [prestanda- och felsökningsguiden för SMB multichannel](storage-files-smb-multichannel-performance.md) [för](storage-troubleshooting-files-performance.md) några vanliga prestandaproblem och lösningar.

### <a name="bursting"></a>Spricker
Om din arbetsbelastning behöver den extra prestandan för att uppfylla toppar i efterfrågan kan din resurs använda burst-krediter för att gå över IOPS-gränsen för resursbaslinjen för att erbjuda resursprestanda som krävs för att möta efterfrågan. Premium-filresurser kan burst-burst-använda sina IOPS upp till 4 000 eller upp till en faktor på tre, beroende på vilket som är ett högre värde. Bursting är automatiserat och fungerar baserat på ett kreditsystem. Bursting fungerar efter bästa försök och burst-gränsen är inte  en garanti, filresurser kan burst-upp till gränsen för en maximal varaktighet på 60 minuter.

Krediter som ackumuleras i en burst-bucket när trafiken för din filresurs är lägre än baslinje-IOPS. Till exempel har en 100 GiB-resurs 500 baslinje-IOPS. Om den faktiska trafiken på resursen var 100 IOPS under ett specifikt intervall på 1 sekund krediteras de 400 oanvända IOPS:erna till en burst-bucket. På samma sätt ackumuleras en inaktiv 1 TiB-resurs med burst-kredit med 1 424 IOPS. Dessa krediter kommer sedan att användas senare när åtgärderna skulle överskrida baslinje-IOPS.

När en resurs överskrider baslinje-IOPS och har krediter i en burst-bucket, kommer den att burst-överstiga högsta tillåtna toppfrekvens. Resurser kan fortsätta att brista så länge krediterna är kvar, upp till max 60 minuter, men detta baseras på antalet upplupna burst-krediter. Varje I/O utöver baslinje-IOPS förbrukar en kredit och när alla krediter har förbrukats återgår resursen till baslinje-IOPS.

Delningskrediter har tre delstater:

- Gäller när filresursen använder mindre än baslinje-IOPS.
- Degressering när filresursen använder mer än baslinje-IOPS och i bursting-läge.
- Konstant, när filresursen använder exakt baslinje-IOPS, finns det antingen inga upplupna eller använda krediter.

Nya filresurser börjar med det fullständiga antalet krediter i sin burst-bucket. Burst-krediter ackumuleras inte om resursens IOPS faller under baslinje-IOPS på grund av begränsning av servern.

## <a name="pay-as-you-go-model"></a>Betala enligt modellen
Azure Files använder en affärsmodell där du betalar per användning för standardfilresurser. I en affärsmodell där du betalar per användning bestäms det belopp som du betalar av hur mycket du faktiskt använder, i stället för baserat på ett etablerat belopp. På en hög nivå betalar du en kostnad för mängden data som lagras på disk och sedan en ytterligare uppsättning transaktioner baserat på din användning av dessa data. En modell där du betalar per användning kan vara kostnadseffektiv eftersom du inte behöver överetablera för att ta hänsyn till framtida tillväxt- eller prestandakrav eller avetablera om arbetsbelastningen är datafotavtryck varierar över tid. Å andra sidan kan en betala per användning-modell också vara svår att planera som en del av en budgeteringsprocess, eftersom faktureringsmodellen betala per användning drivs av slutanvändarförbrukningen.

### <a name="differences-in-standard-tiers"></a>Skillnader i standardnivåer
När du skapar en standardfilresurs väljer du mellan transaktionsoptimerade, heta och coola nivåer. Alla tre nivåerna lagras på exakt samma standardlagringsmaskinvara. Den största skillnaden för dessa tre nivåer är deras data i vila-lagringspriser, som är lägre på låg kallare nivåer, och transaktionspriserna, som är högre på de kallare nivåerna. Det innebär att du måste:

- Transaktionsoptimerad optimerar, som namnet antyder, priset för höga transaktionsarbetsbelastningar. Transaktionsoptimerad har det högsta lagringspriset för vilodata, men de lägsta transaktionspriserna.
- Hot är för aktiva arbetsbelastningar som inte omfattar ett stort antal transaktioner och som har ett något lägre data i vila-lagringspris, men något högre transaktionspriser jämfört med transaktionsoptimerade. Se det som mittengrunden mellan transaktionsoptimerade och coola nivåer.
- Låg belastning optimerar priset för arbetsbelastningar som inte har mycket aktivitet, vilket ger det lägsta lagringspriset för data i vila, men de högsta transaktionspriserna.

Om du använder en arbetsbelastning som sällan används på den transaktionsoptimerade nivån betalar du nästan ingenting för de få gånger i månaden som du gör transaktioner mot din resurs, men du betalar en hög mängd för datalagringskostnaderna. Om du skulle flytta samma resurs till lågfrekvent nivå skulle du fortfarande betala nästan ingenting för transaktionskostnaderna, bara för att du sällan gör transaktioner för den här arbetsbelastningen, men den lågfrekventa nivån har ett mycket billigare datalagringspris. Genom att välja lämplig nivå för ditt användningsfall kan du avsevärt minska dina kostnader. Genom att välja lämplig nivå för ditt användningsfall kan du avsevärt minska dina kostnader.

På samma sätt betalar du mycket mer i transaktionskostnader om du lägger till en arbetsbelastning med hög åtkomst på låg nivå, men mindre för datalagringskostnader. Detta kan leda till en situation där de ökade kostnaderna från transaktionspriserna uppväger besparingarna från det minskade datalagringspriset, vilket leder till att du betalar mer för den coola kostnaden än du skulle ha på transaktionsoptimerad. Det är möjligt för vissa användningsnivåer att även om den heta nivån är den mest kostnadseffektiva nivån blir den låg nivå dyrare än transaktionsoptimerad.

Din arbetsbelastning och aktivitetsnivå avgör den mest kostnadseffektiva nivån för din standardfilresurs. I praktiken är det bästa sättet att välja den mest kostnadseffektiva nivån att titta på resursens faktiska resursförbrukning (lagrade data, skrivtransaktioner osv.).

### <a name="what-are-transactions"></a>Vad är transaktioner?
Transaktioner är åtgärder eller begäranden mot Azure Files att ladda upp, ladda ned eller på annat sätt ändra innehållet i filresursen. Varje åtgärd som vidtas på en filresurs översätts till en eller flera transaktioner och på standardresurser som använder faktureringsmodellen betala per användning, som innebär transaktionskostnader.

Det finns fem grundläggande transaktionskategorier: skriva, lista, läsa, andra och ta bort. Alla åtgärder som utförs via REST API eller SMB bucketeras i någon av följande fyra kategorier enligt följande:

| Åtgärdstyp | Skriva transaktioner | Visa en lista över transaktioner | Läsa transaktioner | Andra transaktioner | Ta bort transaktioner |
|-|-|-|-|-|-|
| Hanteringsåtgärder | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Dataåtgärder | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1 är endast tillgängligt för premiumfilresurser, som använder den etablerade faktureringsmodellen. Transaktioner påverkar inte faktureringen för premiumfilresurser.

## <a name="see-also"></a>Se även
- [Azure Files prissättningssidan](https://azure.microsoft.com/pricing/details/storage/files/).
- [Planera för en Azure Files och](storage-files-planning.md) planera för en Azure File Sync [distribution.](../file-sync/file-sync-planning.md)
- [Skapa en filresurs](storage-how-to-create-file-share.md) och [distribuera Azure File Sync](../file-sync/file-sync-deployment-guide.md).