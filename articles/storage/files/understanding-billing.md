---
title: Förstå Azure Files fakturering | Microsoft Docs
description: Lär dig hur du tolkar fakturerings modellerna etablerade och betala per användning för Azure-filresurser.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bb608492327baae958c32be05d8f2a1bb4dbfbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226649"
---
# <a name="understand-azure-files-billing"></a>Förstå Azure Files fakturering
Azure Files innehåller två olika fakturerings modeller: etablerade och betala per användning. Den etablerade modellen är bara tillgänglig för Premium-filresurser, vilket är fil resurser som distribueras i **FileStorage** -lagrings kontots typ. Modellen för att betala per användning är bara tillgänglig för standard fil resurser, vilket är fil resurser som distribueras i GPv2-lagrings konto typen **(General Purpose version 2)** . I den här artikeln beskrivs hur båda modellerna fungerar för att hjälpa dig att förstå din månatliga Azure Files faktura.

Information om Azure Files pris information finns på [sidan med Azure Files prissättning](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Lagringsenheter    
Azure Files använder bas mått enheter för att representera lagrings kapacitet: KiB, MiB, GiB och TiB. Operativ systemet kan eventuellt inte använda samma mått enhet eller inventerings system.

### <a name="windows"></a>Windows

Både Windows-operativsystemet och Azure Files mäter lagrings kapacitet med hjälp av bas-2-inventerings systemet, men det finns en skillnad när du märker enheter. Azure Files etikettens lagrings kapacitet med bas mått enheter, medan Windows namnger sin lagrings kapacitet i bas-10 mått enheter. När du rapporterar lagrings kapacitet konverterar Windows inte lagrings kapaciteten från bas-2 till bas-10.

|Förkortning  |Definition  |Enhet  |Windows visas som  |
|---------|---------|---------|---------|
|KiB     |1 024 byte         |kibibyte         |KB (KB)         |
|MiB     |1 024 KiB (1 048 576 byte)         |mebibyte         |MB (megabyte)         |
|GiB     |1024 MiB (1 073 741 824 byte)         |gibibyte         |GB (gigabyte)         |
|TiB     |1024 GiB (1 099 511 627 776 byte)         |tebibyte         |TB (terabyte)         |

### <a name="macos"></a>macOS

Se [hur iOS och MacOS rapporterar lagrings kapacitet](https://support.apple.com/HT201402) på Apples webbplats för att avgöra vilket inventerings system som används.

### <a name="linux"></a>Linux

Ett annat inventerings system kan användas av varje operativ system eller enskild program vara. Se deras dokumentation för att avgöra hur de rapporterar lagrings kapacitet.

## <a name="provisioned-model"></a>Etablerad modell
Azure Files använder en etablerad modell för Premium-filresurser. I en etablerad affärs modell anger du proaktivt till den Azure Files tjänst som dina lagrings krav är, i stället för att faktureras baserat på vad du använder. Detta liknar att köpa maskin vara lokalt, i det här fallet när du etablerar en Azure-filresurs med en viss lagrings mängd, betalar du för lagringen oavsett om du använder den eller inte, precis som du inte börjar betala kostnaden för fysiska medier lokalt när du börjar använda utrymmet. Till skillnad från att köpa fysiska media lokalt kan etablerade fil resurser dynamiskt skalas upp eller ned beroende på dina egenskaper för lagring och IO-prestanda.

När du etablerar en Premium fil resurs anger du hur många GiBs din arbets belastning kräver. Varje GiB som du etablerar ger dig ytterligare IOPS och data flöde enligt ett fast förhållande. Förutom de bas linje-IOPS som du garanterar är det möjligt att varje Premium-filresurs stöder burst-skydd på bästa möjliga sätt. Formlerna för IOPS och data flöde är följande:

- Bas linje IOPS = 400 + 1 * etablerad GiB. (Upp till högst 100 000 IOPS).
- Burst-gräns = MAX (4000, 3 * bas linje IOPS).
- Utgående taxa = 60 MiB/s + 0,06 * etablerad GiB.
- Ingress-taxa = 40 MiB/s + 0,04 * etablerad GiB.

Den allokerade storleken på fil resursen kan ökas när som helst, men den kan bara minskas efter 24 timmar sedan den senaste ökningen. Efter att ha väntat 24 timmar utan en kvot ökning kan du minska delnings kvoten så många gånger du vill, tills du ökar den igen. Ändringar av IOPS/data flödes skalning börjar gälla inom några minuter efter att den etablerade storleken har ändrats.

Det går att minska storleken på den allokerade resursen under den använda GiB. Om du gör detta kommer du inte att förlora data, men du kommer fortfarande att faktureras för den storlek som används och ta emot prestanda (bas linje, data flöde och burst-IOPS) för den etablerade resursen, inte den storlek som används.

I följande tabell visas några exempel på dessa formler för de allokerade resurs storlekarna:

|Kapacitet (GiB) | Bas linje IOPS | Burst IOPS | Utgående (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Upp till 4 000     | 66   | 44   |
|500         | 900     | Upp till 4 000  | 90   | 60   |
|1 024       | 1 424   | Upp till 4 000   | 122   | 81   |
|5 120       | 5 520   | Upp till 15 360  | 368   | 245   |
|10 240      | 10 640  | Upp till 30 720  | 675   | 450   |
|33 792      | 34 192  | Upp till 100 000 | 2 088 | 1 392   |
|51 200      | 51 600  | Upp till 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Upp till 100 000 | 6 204 | 4 136   |

Gällande prestanda för fil resurser är beroende av dator nätverks begränsningar, tillgänglig nätverks bandbredd, i/o-storlekar, parallellitet, bland många andra faktorer. Till exempel, baserat på intern testning med 8 KiB i/o-storlek, kan en virtuell Windows-dator utan SMB Multichannel vara aktive rad, *Standard F16s_v2*, ansluten till Premium-filresurs via SMB, få 20 000 Read IOPS och 15 000 Skriv-IOPS. Med 512 MiB Läs-/skriv-i/o-storlekar kan samma virtuella dator uppnå 1,1 GiB/s utgående och 370 MiB/s ingress-genomflöde. Samma klient kan få upp till \~ 3x prestanda om SMB Multichannel är aktiverat på Premium-resurserna. För att uppnå maximal prestanda skala kan du [Aktivera SMB Multichannel](storage-files-enable-smb-multichannel.md) och sprida belastningen över flera virtuella datorer. Se [SMB Multichannel-prestanda](storage-files-smb-multichannel-performance.md) och [fel söknings guide](storage-troubleshooting-files-performance.md) för några vanliga prestanda problem och lösningar.

### <a name="bursting"></a>Bursting "
Om din arbets belastning behöver den extra prestanda som krävs för att uppfylla den högsta efter frågan kan din resurs använda burst-krediter för att gå över resursens IOPS-gräns för att erbjuda resurs prestanda som krävs för att uppfylla behovet. Premium-filresurser kan överföra sina IOPS upp till 4 000 eller upp till en faktor på tre, beroende på vilket som är ett högre värde. Burst-överföring automatiseras och fungerar baserat på ett kredit system. Burst-överföring fungerar på bästa möjliga sätt och burst-gränsen är inte en garanti, fil resurser kan överföras *till* gränsen för en maximal varaktighet på 60 minuter.

Krediterna ackumuleras i en burst-Bucket när trafiken för fil resursen är under bas linje IOPS. Till exempel har en 100 GiB-resurs 500 bas linje IOPS. Om den faktiska trafiken på resursen var 100 IOPS för ett angivet intervall på 1 sekund, krediteras 400 oanvända IOPS till en burst-Bucket. På samma sätt påförs en TiB-resurs för inaktivitet med 1 424 IOPS. Dessa krediter kommer sedan att användas senare när åtgärder skulle överskrida bas linjens IOPS.

När en resurs överskrider bas linjens IOPS och har krediter i en burst-Bucket, kommer den att överföras till den högsta tillåtna högsta överföringshastigheten. Resurser kan fortsätta att överföra så länge krediterna är återstående, upp till max 60 minuter, men detta baseras på antalet överförda burst-krediter. Varje i/o utöver bas linje IOPS förbrukar en kredit och när alla krediter förbrukas kommer resursen att återgå till bas linjens IOPS.

Dela krediter har tre tillstånd:

- Påförs när fil resursen använder mindre än bas linjens IOPS.
- Avböja, när fil resursen använder mer än bas linjens IOPS och i burst-läge.
- Konstant, när fil resursen använder exakt bas linje IOPS, finns det inga krediter som är upplupna eller använda.

Nya fil resurser börjar med det fullständiga antalet krediter i sin burst-Bucket. Burst-krediter kommer inte att periodiseras om resursens IOPS sjunker under bas linje IOPS på grund av begränsning av servern.

## <a name="pay-as-you-go-model"></a>Betala per användning-modell
Azure Files använder en affärs modell enligt principen betala per användning för standard fil resurser. I en affärs modell enligt principen betala per användning bestäms det belopp du betalar efter hur mycket du faktiskt använder, i stället för baserat på en etablerad mängd. På hög nivå betalar du en kostnad för den mängd data som lagras på disk och sedan en ytterligare uppsättning transaktioner baserat på din användning av dessa data. En modell enligt principen betala per användning kan vara kostnads effektiv eftersom du inte behöver överetablera för framtida tillväxt-eller prestanda krav eller avetablera om din arbets belastning varierar över tid. Å andra sidan kan en modell enligt principen betala per användning också vara svår att planera som en del av en budget process, eftersom fakturerings modellen betala per användning drivs av slut användar förbrukning.

### <a name="differences-in-standard-tiers"></a>Skillnader på standard nivåer
När du skapar en standard fil resurs väljer du mellan transaktionerna optimerade, frekventa och låg frekventa nivåer. Alla tre nivåerna lagras på exakt samma maskin vara för standard lagring. Den största skillnaden för dessa tre nivåer är deras data vid olika lagrings priser, som är lägre på kylare-nivåer och transaktions priserna, som är högre på kylare-nivåerna. Det innebär att du måste:

- Transaktionen som är optimerad, som namnet antyder, optimerar priset för arbets belastningar med hög transaktion. Transaktions optimeringen har det högsta priset vid rest-lagring, men de lägsta transaktions priserna.
- Frekvent är för aktiva arbets belastningar som inte omfattar ett stort antal transaktioner, och som har ett något lägre data lager pris, men något högre transaktions priser jämfört med transaktionerna som optimeras. Tänk på det som mellan grunden mellan transaktionens optimerade och låg frekventa nivåer.
- Häftigt optimerar priset för arbets belastningar som inte har mycket aktivitet, vilket ger lägsta möjliga data lagrings pris, men de högsta transaktions priserna.

Om du använder en arbets belastning som inte används ofta i transaktions optimerings nivån, kommer du att betala nästan inget under några få tillfällen under en månad som du gör transaktioner mot din resurs, men du betalar ett högt belopp för kostnaderna för data lagring. Om du skulle flytta samma resurs till den låg frekventa nivån, betalar du fortfarande nästan ingenting för transaktionskostnaderna, bara för att du behöver göra transaktioner för den här arbets belastningen, men den låg frekventa nivån har ett mycket billigare data lagrings pris. Genom att välja lämplig nivå för ditt användnings fall kan du avsevärt minska kostnaderna. Genom att välja lämplig nivå för ditt användnings fall kan du avsevärt minska kostnaderna.

På samma sätt kommer du att betala mycket mer i transaktionskostnader, men om du lägger till en hög tillgänglig arbets belastning på låg frekvent nivå, kan du betala mer om kostnader för data lagring. Detta kan leda till en situation där ökade kostnader från transaktions priserna ökar uppväger besparingarna från det minskade priset för data lagring, vilket innebär att du betalar mer pengar på häftigt läge än vad du skulle ha på transaktionen optimerad. Det är möjligt att vissa användnings nivåer medan den frekventa nivån är den mest kostnads effektiva nivån. den låg frekventa nivån blir dyrare än transaktionen optimerad.

Din arbets belastning och aktivitets nivå avgör den mest kostnads effektiva nivån för din standard fil resurs. I praktiken innebär det bästa sättet att välja den mest kostnads effektiva nivån att titta på den faktiska resurs förbrukningen för resursen (data som lagras, skriv transaktioner osv.).

### <a name="what-are-transactions"></a>Vad är transaktioner?
Transaktioner är åtgärder eller begär Anden mot Azure Files att ladda upp, ladda ned eller på annat sätt ändra innehållet i fil resursen. Varje åtgärd som utförs på en fil resurs översätts till en eller flera transaktioner, och på standard resurser som använder fakturerings modellen betala per användning, som översätts till transaktionskostnader.

Det finns fem grundläggande transaktions kategorier: skriva, lista, läsa, övrigt och ta bort. Alla åtgärder som utförs via REST API eller SMB är Bucket till någon av följande fyra kategorier:

| Åtgärdstyp | Skriv transaktioner | Lista transaktioner | Läs transaktioner | Andra transaktioner | Ta bort transaktioner |
|-|-|-|-|-|-|
| Hanteringsåtgärder | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Dataåtgärder | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4,1 är endast tillgängligt för Premium-filresurser som använder den etablerade fakturerings modellen, men transaktioner påverkar inte faktureringen för Premium-filresurser.

## <a name="see-also"></a>Se även
- [Sidan Azure Files prissättning](https://azure.microsoft.com/pricing/details/storage/files/).
- [Planera en Azure Files distribution](./storage-files-planning.md) och [planera för en Azure File Sync distribution](./storage-sync-files-planning.md).
- [Skapa en fil resurs](./storage-how-to-create-file-share.md) och [Distribuera Azure File Sync](./storage-sync-files-deployment-guide.md).