---
title: Översikt över resurs ögonblicks bilder för Azure Files | Microsoft Docs
description: En resurs ögonblicks bild är en skrivskyddad version av en Azure Files resurs som tas vid en tidpunkt, som ett sätt att säkerhetskopiera resursen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d415ef165da18312a458d7d14fba18acd1bf44cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84235611"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Översikt över resursögonblicksbilder för Azure Files

Med Azure Files kan du ta ögonblicksbilder av filresurser. Resursögonblicksbilder fångar resursens status vid den tidpunkten. I den här artikeln beskriver vi vilka funktioner som finns för resursögonblicksbilder och hur du kan använda dem i ditt specifika användningsfall.

## <a name="when-to-use-share-snapshots"></a>När du ska använda resursögonblicksbilder

### <a name="protection-against-application-error-and-data-corruption"></a>Skydd mot programfel och skadade data

Program som använder filresurser utför åtgärder som skrivning, läsning, lagring, överföring och bearbetning. Om ett program är felaktigt konfigurerat eller innehåller ett oavsiktligt programmeringsfel kan block skrivas över eller skadas av misstag. För att skydda dig mot detta kan du ta resursögonblicksbilder innan du distribuerar ny programkod. Om ett fel eller ett programfel introduceras med den nya distributionen kan du gå tillbaka till en tidigare version av dina data på filresursen. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Skydd mot oavsiktliga borttagningar eller ändringar

Tänk dig att du arbetar med en textfil i en filresurs. När textfilen stängs förlorar du möjligheten att ångra ändringarna. I så fall måste du återställa till en tidigare version av filen. Du kan använda resursögonblicksbilder för att återställa en fil till en tidigare version om filnamnet har ändrats eller om filen har tagits bort av misstag.

### <a name="general-backup-purposes"></a>För allmän säkerhetskopiering

När du har skapat en filresurs kan du säkerhetskopiera dina data genom att regelbundet skapa en resursögonblicksbild av filresursen. Genom att ta regelbundna resursögonblicksbilder kan du spara flera versioner av dina data som sedan kan användas för framtida granskningskrav eller haveriberedskap. Vi rekommenderar att du använder [säkerhets kopiering av Azure-filresurs](../../backup/azure-file-share-backup-overview.md) som säkerhets kopierings lösning för att ta och hantera ögonblicks bilder. Du kan också ta och hantera ögonblicks bilder själv, antingen med CLI eller PowerShell.

## <a name="capabilities"></a>Funktioner

En ögonblicks bild av en resurs är en skrivskyddad kopia av dina data vid en viss tidpunkt. Du kan skapa, ta bort och hantera ögonblicks bilder med hjälp av REST API. Samma funktioner är också tillgängliga i klient biblioteket, Azure CLI och Azure Portal. 

Du kan visa ögonblicks bilder av en resurs med hjälp av både REST API och SMB. Du kan hämta listan över versioner av katalogen eller filen och du kan montera en speciell version direkt som en enhet (endast tillgänglig i Windows-se [gränser](#limits)). 

När en resurs ögonblicks bild har skapats kan den läsas, kopieras eller tas bort, men inte ändras. Du kan inte kopiera en fullständig resurs ögonblicks bild till ett annat lagrings konto. Du måste göra filen efter fil genom att använda AzCopy eller andra kopierings metoder.

Delning av ögonblicks bilder ges på fil resurs nivå. Hämtning ges på enskild filnivå, så att enskilda filer kan återställas. Du kan återställa en fullständig fil resurs med hjälp av SMB, REST API, portalen, klient biblioteket eller PowerShell/CLI-verktyget.

En resurs ögonblicks bild av en fil resurs är identisk med bas fil resursen. Den enda skillnaden är att ett **datetime** -värde läggs till i resurs-URI: n för att ange den tid då resurs ögonblicks bilden togs. Om en fil resurs-URI t. ex. är http: \/ /storagesample.Core.File.Windows.net/MyShare, liknar resursens ögonblicks bilds-URI följande:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Resurs ögonblicks bilder sparas tills de tas bort explicit. En resurs ögonblicks bild kan inte leva ut från sin bas fil resurs. Du kan räkna upp ögonblicks bilderna som är associerade med bas fil resursen för att spåra dina befintliga ögonblicks bilder. 

När du skapar en resurs ögonblicks bild av en fil resurs kopieras filerna i resursens system egenskaper till resursens ögonblicks bild med samma värden. Bas filerna och fil resursens metadata kopieras också till resursens ögonblicks bild, om du inte anger separata metadata för resurs ögonblicks bilden när du skapar den.

Det går inte att ta bort en resurs som har ögonblicks bilder av resursen om du inte tar bort alla ögonblicks bilder först.

## <a name="space-usage"></a>Utrymmes användning

Resurs ögonblicks bilder är stegvisa. Endast de data som har ändrats efter att du har sparat den senaste ögonblicks bilden av resursen. Detta minimerar den tid som krävs för att skapa ögonblicks bilden av resursen och sparar pengar på lagrings kostnaderna. Alla Skriv åtgärder till åtgärden för objekt-eller egenskaps-eller metadata-uppdatering räknas till "ändrat innehåll" och lagras i resurs ögonblicks bilden. 

För att spara utrymme kan du ta bort resurs ögonblicks bilden för den period då omsättningen var högst.

Även om resurs ögonblicks bilder sparas stegvis, behöver du bara behålla den senaste ögonblicks bilden för att återställa resursen. När du tar bort en resurs ögonblicks bild tas endast de data som är unika för den delade ögonblicks bilden bort. Aktiva ögonblicks bilder innehåller all information som du behöver för att bläddra bland och återställa dina data (från den tidpunkt då resursens ögonblicks bild togs) till den ursprungliga platsen eller en alternativ plats. Du kan återställa på objekt nivå.

Ögonblicks bilder räknas inte mot din gräns för 5 TB-resursen. Det finns ingen gräns för hur mycket utrymmes resurs ögonblicks bilder som upptas totalt. Lagrings konto gränser gäller fortfarande.

## <a name="limits"></a>Gränser

Det maximala antalet resurs ögonblicks bilder som Azure Files tillåter idag är 200. Efter 200 resurs ögonblicks bilder måste du ta bort gamla ögonblicks bilder av resurser för att skapa nya. 

Det finns ingen gräns för samtidiga anrop för att skapa resurs ögonblicks bilder. Det finns ingen gräns för mängden utrymme som delar ögonblicks bilder av en viss fil resurs som kan använda. 

Idag är det inte möjligt att montera ögonblicks bilder av resurser i Linux. Detta beror på att Linux SMB-klienten inte stöder montering av ögonblicks bilder som Windows gör.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiera data tillbaka till en resurs från en resursögonblicksbild

Kopieringsåtgärder som rör ögonblicksbilder av filer och filresurser följer dessa regler:

Du kan kopiera enskilda filer i en ögonblicksbild av en filresurs till dess basresurs eller någon annan plats. Du kan återställa en tidigare version av en fil eller återställa hela filresursen genom att kopiera en fil i taget från ögonblicksbilden. Ögonblicksbilden av filresursen upphöjs inte till basresurs. 

Resursögonblicksbilden förblir intakt efter kopieringen, men basfilresursen skrivs över med en kopia av de data som fanns i resursögonblicksbilden. Alla återställda filer räknas med i ”ändrat innehåll”.

Du kan kopiera en fil i en resursögonblicksbild till ett annat mål med ett annat namn. Den resulterande målfilen blir en skrivbar fil och inte en resursögonblicksbild. I det här fallet förblir basfilresursen intakt.

När en målfil skrivs över med en kopia förblir alla resursögonblicksbilder som är associerade med den ursprungliga målfilen intakta.

## <a name="general-best-practices"></a>Allmän bästa praxis

Vi rekommenderar att du använder [säkerhets kopiering av Azure-filresurs](../../backup/azure-file-share-backup-overview.md) som en säkerhets kopierings lösning för automatisering av ögonblicks bilder, samt hantering av ögonblicks bilder. När du kör en infrastruktur på Azure kan du automatisera säkerhets kopieringar för data återställning närhelst det är möjligt. Automatiserade åtgärder är mer pålitliga än manuella processer, vilket hjälper till att förbättra data skydd och återställning. Du kan använda Azure-filresursens säkerhets kopiering, REST API, klient-SDK eller skript för automatisering.

Innan du distribuerar Schemaläggaren för resurs ögonblicks bilder bör du ta hänsyn till din resurs frekvens för ögonblicks bilder och inställningarna för kvarhållning för att undvika onödiga kostnader.

Resurs ögonblicks bilder tillhandahåller bara skydd på filnivå. Resurs ögonblicks bilder förhindrar inte borttagning av fat-fingrar på en fil resurs eller ett lagrings konto. För att skydda ett lagrings konto från oavsiktlig borttagning kan du antingen [Aktivera mjuk borttagning](storage-files-prevent-file-share-deletion.md)eller låsa lagrings kontot och/eller resurs gruppen.

## <a name="next-steps"></a>Nästa steg
- Arbeta med resurs ögonblicks bilder i:
    - [Säkerhets kopiering av Azure-filresurs](../../backup/azure-file-share-backup-overview.md)
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Vanliga frågor och svar om att dela bilder](storage-files-faq.md#share-snapshots)