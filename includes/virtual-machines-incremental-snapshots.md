---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a662598efdca05769c7da9fbeecdf692dccdacb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026507"
---
## <a name="overview"></a>Översikt
Azure Storage ger möjlighet att ta ögonblicks bilder av blobbar. Ögonblicks bilder fångar BLOB-statusen vid den tidpunkten. I den här artikeln beskriver vi ett scenario där du kan underhålla säkerhets kopior av virtuella dator diskar med hjälp av ögonblicks bilder. Du kan använda den här metoden när du väljer att inte använda Azure Backup-och återställnings tjänst och vill skapa en anpassad säkerhets kopierings strategi för dina virtuella dator diskar. För virtuella datorer som kör affärs-eller verksamhets kritiska arbets belastningar rekommenderar vi att du använder [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) som en del av strategin för säkerhets kopiering.  

Diskar för virtuella Azure-datorer lagras som Page blobbar i Azure Storage. Eftersom vi beskriver en säkerhets kopierings strategi för virtuella dator diskar i den här artikeln, refererar vi till ögonblicks bilder i kontexten för sid-blobar. Mer information om ögonblicks bilder finns i [skapa en ögonblicks bild av en BLOB](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Vad är en ögonblicks bild?
En BLOB-ögonblicksbild är en skrivskyddad version av en blob som samlas in vid en viss tidpunkt. När en ögonblicks bild har skapats kan den läsas, kopieras eller tas bort, men inte ändras. Ögonblicks bilder är ett sätt att säkerhetskopiera en BLOB när den visas vid ett tillfälle. Till REST version 2015-04-05 hade du möjlighet att kopiera fullständiga ögonblicks bilder. Med resten version 2015-07-08 och senare kan du även kopiera stegvisa ögonblicks bilder.

## <a name="full-snapshot-copy"></a>Fullständig ögonblicks bilds kopia
Ögonblicks bilder kan kopieras till ett annat lagrings konto som en BLOB för att bevara säkerhets kopior av bas-bloben. Du kan också kopiera en ögonblicks bild över dess bas-BLOB, som att återställa blobben till en tidigare version. När en ögonblicks bild kopieras från ett lagrings konto till ett annat upptar det samma utrymme som bas sidans blob. Därför går det långsamt att kopiera hela ögonblicks bilder från ett lagrings konto till ett annat och förbrukar mycket utrymme på mål lagrings kontot.

> [!NOTE]
> Om du kopierar bas-bloben till ett annat mål kopieras inte ögonblicks bilderna av bloben tillsammans med den. På samma sätt påverkas inte ögonblicks bilder som är associerade med bas-bloben om du skriver över en bas-BLOB med en kopia och den förblir intakt under bas-BLOB-namnet.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Säkerhetskopiera diskar med ögonblicks bilder
Som en säkerhets kopierings strategi för dina virtuella dator diskar kan du ta regelbundna ögonblicks bilder av disken eller sid-bloben och kopiera dem till ett annat lagrings konto med hjälp av verktyg som [Kopiera BLOB](/rest/api/storageservices/Copy-Blob) -åtgärd eller [AzCopy](../articles/storage/common/storage-use-azcopy-v10.md). Du kan kopiera en ögonblicks bild till en mål sid-BLOB med ett annat namn. Den resulterande destinations sidans BLOB är en skrivbar sid-blob och inte en ögonblicks bild. Senare i den här artikeln beskriver vi steg för att göra säkerhets kopior av virtuella dator diskar med hjälp av ögonblicks bilder.

### <a name="restore-disks-using-snapshots"></a>Återställa diskar med ögonblicks bilder
När det är dags att återställa disken till en stabil version som tidigare har registrerats i en ögonblicks bild av säkerhets kopian, kan du kopiera en ögonblicks bild över bas sidans blob. När ögonblicks bilden har uppgraderats till bas sidans BLOB, är ögonblicks bilden kvar, men källan skrivs över med en kopia som kan både läsas och skrivas. Senare i den här artikeln beskriver vi steg för att återställa en tidigare version av disken från dess ögonblicks bild.

### <a name="implementing-full-snapshot-copy"></a>Implementera fullständig ögonblicks bilds kopia
Du kan implementera en fullständig ögonblicks bilds kopia genom att göra följande:

* Börja med att ta en ögonblicks bild av bas-bloben med hjälp av [ögonblicks bilds-BLOB](/rest/api/storageservices/Snapshot-Blob) .
* Kopiera sedan ögonblicks bilden till ett mål lagrings konto med hjälp av [copy BLOB](/rest/api/storageservices/Copy-Blob).
* Upprepa den här processen om du vill behålla säkerhets kopior av din bas-blob.

## <a name="incremental-snapshot-copy"></a>Stegvis ögonblicks bilds kopia
Den nya funktionen i [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) -API: et är ett mycket bättre sätt att säkerhetskopiera ögonblicks bilderna av dina sid blobbar eller diskar. API: et returnerar listan över ändringar mellan bas-bloben och ögonblicks bilderna, vilket minskar mängden lagrings utrymme som används på säkerhets kopierings kontot. API: et stöder sid-blobar på Premium Storage och standard lagring. Med det här API: et kan du bygga snabbare och mer effektiva säkerhets kopierings lösningar för virtuella Azure-datorer. Detta API är tillgängligt med REST-versionen 2015-07-08 och högre.

Med en stegvis ögonblicks bilds kopia kan du kopiera från ett lagrings konto till ett annat skillnaden mellan,

* Bas-blob och dess ögonblicks bild eller
* Två ögonblicks bilder av bas-bloben

Under förutsättning att följande villkor är uppfyllda,

* Blobben skapades på Jan-1-2016 eller senare.
* Blobben skrevs inte över med [PutPage](/rest/api/storageservices/Put-Page) eller [Kopiera blobben](/rest/api/storageservices/Copy-Blob) mellan två ögonblicks bilder.

>[!NOTE]
>Den här funktionen är tillgänglig för Azure Page-blobar med Premium och standard.

När du har en anpassad säkerhets kopierings strategi med hjälp av ögonblicks bilder kan det vara långsamt att kopiera ögonblicks bilderna från ett lagrings konto till ett annat, och det kan förbruka mycket lagrings utrymme. I stället för att kopiera hela ögonblicks bilden till ett lagrings konto för säkerhets kopiering kan du skriva skillnaden mellan flera ögonblicks bilder till en säkerhets kopierings sidans blob. På så sätt minskar tiden för kopiering och utrymmet för lagring av säkerhets kopior avsevärt.

### <a name="implementing-incremental-snapshot-copy"></a>Implementera stegvis ögonblicks bilds kopia
Du kan implementera en stegvis ögonblicks bilds kopia genom att göra följande.

* Ta en ögonblicks bild av bas-bloben med hjälp av [Snapshot-BLOB](/rest/api/storageservices/Snapshot-Blob).
* Kopiera ögonblicks bilden till mål lagrings kontot för säkerhets kopian i samma eller någon annan Azure-region som använder [copy BLOB](/rest/api/storageservices/Copy-Blob). Detta är BLOB för säkerhets kopierings sidan. Ta en ögonblicks bild av bloben för säkerhets kopierings sidan och lagra den i säkerhets kopierings kontot.
* Ta en ögonblicks bild av bas-bloben med hjälp av Snapshot-blob.
* Hämta skillnaden mellan den första och andra ögonblicks bilden av bas-bloben med hjälp av [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges). Använd den nya parametern **prevsnapshot Frågeparametern** för att ange datetime-värdet för den ögonblicks bild som du vill ha differensen med. När den här parametern finns innehåller REST-svaret bara de sidor som ändrades mellan mål ögonblicks bilden och föregående ögonblicks bild, inklusive rensa sidor.
* Använd [PutPage](/rest/api/storageservices/Put-Page) för att tillämpa dessa ändringar på sidan med säkerhets kopierings sidans blob.
* Ta slutligen en ögonblicks bild av bloben för säkerhets kopierings sidan och lagra den på lagrings kontot för säkerhets kopian.

I nästa avsnitt beskriver vi i detalj hur du kan underhålla säkerhets kopior av diskar med hjälp av en stegvis ögonblicks bilds kopia

## <a name="scenario"></a>Scenario
I det här avsnittet beskriver vi ett scenario som omfattar en anpassad säkerhets kopierings strategi för virtuella dator diskar med hjälp av ögonblicks bilder.

Överväg en virtuell Azure-dator med en virtuell dator med Premium Storage P30-disk. P30-disken som heter *mypremiumdisk* lagras i ett Premium Storage-konto som kallas *mypremiumaccount*. Ett standard lagrings konto med namnet *mybackupstdaccount* används för att lagra säkerhets kopian av *mypremiumdisk*. Vi vill behålla en ögonblicks bild av *mypremiumdisk* var 12: e timme.

Information om hur du skapar ett lagrings konto finns i [skapa ett lagrings konto](../articles/storage/common/storage-account-create.md).

Information om hur du säkerhetskopierar virtuella Azure-datorer finns i [Planera säkerhets kopiering av virtuella Azure](../articles/backup/backup-azure-vms-introduction.md)-datorer.

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Steg för att upprätthålla säkerhets kopieringar av en disk med hjälp av stegvisa ögonblicks bilder
Följande steg beskriver hur du tar ögonblicks bilder av *mypremiumdisk* och upprätthåller säkerhets kopiorna i *mybackupstdaccount*. Säkerhets kopian är en standard sid-blob som kallas *mybackupstdpageblob*. Säkerhets kopierings sidans BLOB visar alltid samma tillstånd som den sista ögonblicks bilden av *mypremiumdisk*.

1. Skapa säkerhets kopierings sidans BLOB för Premium Storage-disken genom att ta en ögonblicks bild av *mypremiumdisk* som kallas *mypremiumdisk_ss1*.
2. Kopiera den här ögonblicks bilden till mybackupstdaccount som en sid-BLOB med namnet *mybackupstdpageblob*.
3. Ta en ögonblicks bild av *mybackupstdpageblob* med namnet *mybackupstdpageblob_ss1*, med hjälp av [ögonblicks bilds-BLOB](/rest/api/storageservices/Snapshot-Blob) och lagra den i *mybackupstdaccount*
4. Under säkerhets kopierings fönstret skapar du en annan ögonblicks bild av *mypremiumdisk*, säger *mypremiumdisk_ss2* och lagrar den i *mypremiumaccount*.
5. Hämta de stegvisa ändringarna mellan de två ögonblicks bilderna, *mypremiumdisk_ss2* och *Mypremiumdisk_ss1*, [med GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) på *mypremiumdisk_ss2* med **prevsnapshot frågeparametern** -parametern inställd på tidsstämpeln för *mypremiumdisk_ss1*. Skriv dessa stegvisa ändringar på säkerhets kopierings sidans BLOB- *mybackupstdpageblob* i *mybackupstdaccount*. Om det finns borttagna intervall i de stegvisa ändringarna måste de tas bort från bloben för säkerhets kopierings sidan. Använd [PutPage](/rest/api/storageservices/Put-Page) för att skriva stegvisa ändringar av BLOB för säkerhets kopierings sidan.
6. Ta en ögonblicks bild av *mybackupstdpageblob* för säkerhets kopierings sidan, som kallas *mybackupstdpageblob_ss2*. Ta bort föregående ögonblicks bild *mypremiumdisk_ss1* från Premium Storage-kontot.
7. Upprepa steg 4-6 alla säkerhets kopierings fönster. På så sätt kan du behålla säkerhets kopior av *mypremiumdisk* på ett standard lagrings konto.

![Säkerhetskopiera disk med stegvisa ögonblicks bilder](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Steg för att återställa en disk från ögonblicks bilder
Följande steg beskriver hur du återställer Premium-disken, *mypremiumdisk* till en tidigare ögonblicks bild från lagrings kontot för säkerhets kopiering *mybackupstdaccount*.

1. Identifiera den tidpunkt som du vill återställa Premium-disken till. Anta att det är en ögonblicks bild *mybackupstdpageblob_ss2*, som lagras i *mybackupstdaccount* för säkerhets kopierings lagrings kontot.
2. I mybackupstdaccount höjer du ögonblicks bilden *mybackupstdpageblob_ss2* som den nya säkerhets kopierings bas sidan BLOB- *mybackupstdpageblobrestored*.
3. Ta en ögonblicks bild av denna återställda BLOB för säkerhets kopierings sidan, som kallas *mybackupstdpageblobrestored_ss1*.
4. Kopiera den återställda Page BLOB- *mybackupstdpageblobrestored* från *mybackupstdaccount* till *mypremiumaccount* som den nya Premium-disken *mypremiumdiskrestored*.
5. Ta en ögonblicks bild av *mypremiumdiskrestored*, som kallas *mypremiumdiskrestored_ss1* för att göra framtida stegvisa säkerhets kopieringar.
6. Peka VM-seriens virtuella dator till den återställda disken *mypremiumdiskrestored* och koppla bort den gamla *mypremiumdisk* från den virtuella datorn.
7. Påbörja säkerhets kopierings processen som beskrivs i föregående avsnitt för den återställda disken *mypremiumdiskrestored*, med hjälp av *mybackupstdpageblobrestored* som säkerhets kopierings sidans blob.

![Återställa disk från ögonblicks bilder](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Nästa steg
Använd följande länkar om du vill veta mer om att skapa ögonblicks bilder av en blob och planera infrastrukturen för säkerhets kopiering av virtuella datorer.

* [Skapa en ögonblicks bild av en BLOB](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planera infrastrukturen för säkerhets kopiering av virtuella datorer](../articles/backup/backup-azure-vms-introduction.md)