---
title: Lagring och dataförflyttning för rendering
description: Lär dig mer om de olika alternativen för lagring och dataförflyttning för återgivning av arbetsbelastningar för tillgångar och utdatafiler.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 0a18ee6961cb601b0fa9db7213eb6115afa20096
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765205"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Alternativ för lagring och dataförflyttning för att återge tillgångs- och utdatafiler

Det finns flera alternativ för att göra scen- och tillgångsfiler tillgängliga för renderingsprogram på pool-VM:arna:

* [Azure Blob Storage:](../storage/blobs/storage-blobs-introduction.md)
  * Scen- och tillgångsfiler laddas upp till bloblagring från ett lokalt filsystem. När programmet körs av en aktivitet kopieras de nödvändiga filerna från bloblagringen till den virtuella datorn så att de kan nås av renderingsprogrammet. Utdatafilerna skrivs av renderingsprogrammet till VM-disken och kopieras sedan till bloblagringen.  Om det behövs kan utdatafilerna laddas ned från bloblagringen till ett lokalt filsystem.
  * Azure Blob Storage är ett enkelt och kostnadseffektivt alternativ för mindre projekt.  Eftersom alla tillgångsfiler krävs på varje pool-VM måste du se till att filöverföringarna är så effektiva som möjligt när antalet och storleken på tillgångsfilerna ökar.  
* Azure Storage som ett filsystem med [blobfuse:](../storage/blobs/storage-how-to-mount-container-linux.md)
  * För virtuella Linux-datorer kan ett lagringskonto exponeras och användas som ett filsystem när den virtuella filsystemdrivrutinen blobfuse används.
  * Det här alternativet har fördelen att det är mycket kostnadseffektivt, eftersom inga virtuella datorer krävs för filsystemet, plus blobfuse-cachelagring på de virtuella datorerna undviker upprepade hämtningar av samma filer för flera jobb och uppgifter.  Dataförflyttning är också enkelt eftersom filerna bara är blobar och standard-API:er och verktyg, till exempel azcopy, kan användas för att kopiera filer mellan ett lokalt filsystem och Azure Storage.
* Filsystem eller filresurs:
  * Beroende på operativsystem för virtuella datorer och krav på prestanda/skalning inkluderar alternativen [Azure Files](../storage/files/storage-files-introduction.md), användning av en virtuell dator med anslutna diskar för NFS, användning av flera virtuella datorer med anslutna diskar för ett distribuerat filsystem som Till exempel LimsterFS, eller användning av ett tredjepartserbjudande.
  * [Avere Systems](https://www.averesystems.com/) är nu en del av Microsoft och kommer att ha lösningar inom en snar framtid som är idealiska för storskalig återgivning med höga prestanda.  Med Avere-lösningen kan du skapa en Azure-baserad NFS- eller SMB-cache som fungerar tillsammans med bloblagring eller med lokala NAS-enheter.
  * Med ett filsystem kan filer läsas eller skrivas direkt till filsystemet eller kopieras mellan filsystemet och de virtuella pool-datorerna.
  * Med ett delat filsystem kan ett stort antal tillgångar som delas mellan projekt och jobb användas, med renderingsuppgifter som endast har åtkomst till det som krävs.

## <a name="using-azure-blob-storage"></a>Använda Azure Blob Storage

Ett bloblagringskonto eller ett v2-lagringskonto för generell användning bör användas.  Dessa två typer av lagringskonto kan konfigureras med betydligt högre gränser jämfört med ett v1-lagringskonto för generell användning, enligt beskrivningen i [det här blogginlägget](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  När den här inställningen konfigureras ger de högre gränserna mycket bättre prestanda och skalbarhet, särskilt när det finns många virtuella pool-datorer som har åtkomst till lagringskontot.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopiera filer mellan klient- och bloblagring

För att kopiera filer till och från Azure Storage kan olika mekanismer användas, inklusive API:et för lagringsblob, [Azure Storage Data Movement Library,](https://github.com/Azure/azure-storage-net-data-movement)kommandoradsverktyget azcopy för [Windows](../storage/common/storage-use-azcopy-v10.md) eller [Linux,](../storage/common/storage-use-azcopy-v10.md) [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) [och Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Med azcopy kan till exempel alla tillgångar i en mapp överföras på följande sätt:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Om du bara vill kopiera ändrade filer kan parametern /XO användas:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgångsfiler från Blob Storage till virtuella datorer i Batch-pool

Det finns ett par olika metoder för att kopiera filer med den bästa metoden som bestäms av storleken på jobbtillgångarna.
Det enklaste sättet är att kopiera alla tillgångsfiler till poolens virtuella datorer för varje jobb:

* När det finns filer som är unika för ett jobb, men [](/rest/api/batchservice/job/add#jobpreparationtask) som krävs för alla aktiviteter i ett jobb, kan en jobbförberedelseaktivitet anges för att kopiera alla filer.  Jobbförberedelseaktiviteten körs en gång när den första jobbaktiviteten körs på en virtuell dator men inte körs igen för efterföljande jobbaktiviteter.
* En [jobbutgåraktivitet](/rest/api/batchservice/job/add#jobreleasetask) ska anges för att ta bort filerna per jobb när jobbet har slutförts. På så sätt undviker du att VM-disken fylls i av alla jobbtillgångsfiler.
* När det finns flera jobb som använder samma tillgångar, med endast inkrementella ändringar av tillgångarna för varje jobb, kopieras fortfarande alla tillgångsfiler, även om bara en delmängd har uppdaterats.  Detta skulle vara ineffektivt när det finns många stora tillgångsfiler.

När tillgångsfiler återanvänds mellan jobb, med endast inkrementella ändringar mellan jobb, är en mer effektiv men något mer involverad metod att lagra tillgångar i den delade mappen på den virtuella datorn och synkronisera ändrade filer.

* Jobbförberedelseaktiviteten utför kopieringen med azcopy med parametern /XO till den delade vm-mapp som anges av AZ_BATCH_NODE_SHARED_DIR miljövariabeln.  Detta kopierar endast ändrade filer till varje virtuell dator.
* Vi måste tänka på storleken på alla tillgångar för att säkerställa att de får plats på poolens virtuella datorers tillfälliga enhet.

Azure Batch har inbyggt stöd för att kopiera filer mellan ett lagringskonto och virtuella datorer i Batch-pool.  [Aktivitetsresursfiler](/rest/api/batchservice/job/add#resourcefile) kopierar filer från lagring till virtuella pool-datorer och kan anges för jobbförberedelseaktiviteten.  Om det finns hundratals filer går det tyvärr att komma upp i en gräns och aktiviteter misslyckas.  Om det finns ett stort antal tillgångar rekommenderar vi att du använder kommandoraden azcopy i jobbförberedelseaktiviteten, som kan använda jokertecken och inte har någon gräns.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopiera utdatafiler till Blob Storage från virtuella datorer i Batch-pool

[Utdatafiler](/rest/api/batchservice/task/add#outputfile) kan användas för att kopiera filer från en pool-VM till lagring.  En eller flera filer kan kopieras från den virtuella datorn till ett angivet lagringskonto när aktiviteten har slutförts.  Renderade utdata ska kopieras, men det kan också vara önskvärt att lagra loggfiler.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Använda ett virtuellt blobfuse-filsystem för virtuella Linux-datorpooler

Blobfuse är en drivrutin för virtuellt filsystem för Azure Blob Storage som gör att du kan komma åt filer som lagras som blobar i ett lagringskonto via Linux-filsystemet.

Poolnoder kan montera filsystemet när det startas eller monteringen kan ske som en del av en jobbförberedelseaktivitet – en aktivitet som bara körs när den första aktiviteten i ett jobb körs på en nod.  Blobfuse kan konfigureras för att utnyttja både en ramdisk och de virtuella datorernas lokala SSD för cachelagring av filer, vilket ökar prestanda avsevärt om flera aktiviteter på en nod har åtkomst till vissa av samma filer.

[Exempelmallar är tillgängliga](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) för att köra fristående V-Ray-renderingar med hjälp av ett blobfuse-filsystem och kan användas som grund för mallar för andra program.

### <a name="accessing-files"></a>Komma åt filer

Jobbaktiviteter anger sökvägar för indatafiler och utdatafiler med hjälp av det monterade filsystemet.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgångsfiler från Blob Storage till virtuella datorer i Batch-pool

Eftersom filer bara är blobar i Azure Storage kan standardblob-API:er, verktyg och ANIS användas för att kopiera filer mellan ett lokalt filsystem och bloblagring. till exempel azcopy, Storage Explorer, Batch Explorer osv.

## <a name="using-azure-files-with-windows-vms"></a>Använda Azure Files virtuella Windows-datorer

[Azure Files](../storage/files/storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet.  Azure Files baseras på Azure Blob Storage. Det är [kostnadseffektivt](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med datareplikering till en annan region så globalt redundant.  [Skalningsmål](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) bör granskas för att avgöra Azure Files bör användas med tanke på prognospoolens storlek och antalet tillgångsfiler.

Det finns [dokumentation om](../storage/files/storage-how-to-use-files-windows.md) hur du monterar en Azure-filresurs.

### <a name="mounting-an-azure-files-share"></a>Montera en Azure Files resurs

Om du vill använda i Batch måste en monteringsåtgärd utföras varje gång en aktivitet körs eftersom det inte går att bevara anslutningen mellan aktiviteter.  Det enklaste sättet att göra detta är att använda cmdkey för att bevara autentiseringsuppgifter med startaktiviteten i poolkonfigurationen och sedan montera resursen före varje uppgift.

Exempel på användning av cmdkey i en poolmall (ryms för användning i JSON-fil) – Observera att när du avgränsar cmdkey-anropet från net use-anropet måste användarkontexten för startaktiviteten vara samma som den som används för att köra aktiviteterna:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Exempel på kommandorad för jobbaktivitet:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Komma åt filer

Jobbaktiviteter anger sökvägar för indatafiler och utdatafiler med hjälp av det monterade filsystemet, antingen med en mappad enhet eller en UNC-sökväg.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indatatillgångsfiler från Blob Storage till virtuella datorer i Batch-pool

Azure Files stöds av alla huvud-API:er och verktyg som har Azure Storage stöd. t.ex. azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer osv.

[Azure File Sync](../storage/file-sync/file-sync-planning.md) är tillgänglig för att automatiskt synkronisera filer mellan ett lokalt filsystem och en Azure-filresurs.

## <a name="next-steps"></a>Nästa steg

Mer information om lagringsalternativen finns i den djupgående dokumentationen:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
