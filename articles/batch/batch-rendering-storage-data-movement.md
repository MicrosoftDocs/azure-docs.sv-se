---
title: Lagring och data förflyttning för åter givning
description: Lär dig mer om de olika alternativen för lagring och data förflyttning för att återge arbets belastningar till gångar och utdatafiler.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 55ec04df2a107dabfc72298bc8849c13f3a926e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86147305"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Alternativ för lagring och data förflyttning för åter givning av till gångar och utdatafiler

Det finns flera alternativ för att göra scen-och till gångs filer tillgängliga för åter givnings programmen på de virtuella datorerna i poolen:

* [Azure Blob-lagring](../storage/blobs/storage-blobs-introduction.md):
  * Scen-och till gångs filer laddas upp till Blob Storage från ett lokalt fil system. När programmet körs av en uppgift kopieras de filer som krävs från Blob Storage till den virtuella datorn så att de kan nås av åter givnings programmet. Utdatafilen skrivs av åter givnings programmet till den virtuella dator disken och kopieras sedan till Blob Storage.  Vid behov kan utdatafilerna hämtas från Blob Storage till ett lokalt fil system.
  * Azure Blob Storage är ett enkelt och kostnads effektivt alternativ för mindre projekt.  Allt eftersom alla till gångs filer krävs på varje virtuell dator i poolen, och när antalet och storleken på till gångs filen ökar, måste du vidta åtgärder för att säkerställa att fil överföringen är så effektiv som möjligt.  
* Azure Storage som ett fil system med [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md):
  * För virtuella Linux-datorer kan ett lagrings konto exponeras och användas som ett fil system när blobfuse-drivrutinen för fil systemet används.
  * Det här alternativet har fördelen att det är mycket kostnads effektivt eftersom inga virtuella datorer krävs för fil systemet, plus blobfuse-cachelagring på de virtuella datorerna förhindrar upprepade hämtningar av samma filer för flera jobb och aktiviteter.  Data flytt är också enkelt eftersom filerna bara är blobbar och standard-API: er och verktyg, till exempel AzCopy, kan användas för att kopiera filer mellan ett lokalt fil system och Azure Storage.
* Fil system eller fil resurs:
  * Beroende på den virtuella datorns operativ system och prestanda-/skalnings kraven inkluderar alternativen [Azure Files](../storage/files/storage-files-introduction.md), med hjälp av en virtuell dator med anslutna diskar för NFS, med hjälp av flera virtuella datorer med anslutna diskar för ett distribuerat fil system som GlusterFS, eller med ett erbjudande från tredje part.
  * [AVERT Systems](https://www.averesystems.com/) är nu en del av Microsoft och kommer att ha lösningar inom en snar framtid som är idealiska för storskalig åter givning av höga prestanda.  Med hjälp av aver-lösningen kan en Azure-baserad NFS-eller SMB-cache skapas som fungerar tillsammans med Blob Storage eller med lokala NAS-enheter.
  * Med ett fil system kan filer läsas eller skrivas direkt till fil systemet eller kopieras mellan fil systemet och poolens virtuella datorer.
  * Ett delat fil system gör det möjligt för ett stort antal till gångar som delas mellan projekt och jobb att användas, med åter givnings aktiviteter endast åtkomst till vad som krävs.

## <a name="using-azure-blob-storage"></a>Använda Azure Blob Storage

Ett Blob Storage-konto eller ett allmänt-syfte v2-lagrings konto ska användas.  Dessa två typer av lagrings konton kan konfigureras med betydligt högre gränser jämfört med ett allmänt v1-lagrings konto, enligt beskrivningen i [det här blogg inlägget](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  När de högre gränserna har kon figurer ATS kommer högre gränser att möjliggöra mycket bättre prestanda och skalbarhet, särskilt när det finns många virtuella pooler som har åtkomst till lagrings kontot.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopiera filer mellan klient-och blob-lagring

För att kunna kopiera filer till och från Azure Storage kan olika mekanismer användas inklusive Storage BLOB API, [Azure Storage data flyttnings bibliotek](https://github.com/Azure/azure-storage-net-data-movement), kommando rads verktyget AzCopy för [Windows](../storage/common/storage-use-azcopy-v10.md) eller [Linux](../storage/common/storage-use-azcopy-v10.md), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)och [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Med AZCopy kan till exempel alla till gångar i en mapp överföras på följande sätt:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Om du bara vill kopiera ändrade filer kan parametern/XO användas:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indata till gångs filer från Blob Storage till virtuella datorer i batch-pooler

Det finns ett par olika metoder för att kopiera filer med den bästa metoden som bestäms av storleken på jobb till gångarna.
Det enklaste sättet är att kopiera alla till gångs filen till de virtuella datorerna för varje jobb:

* När det finns filer som är unika för ett jobb, men som krävs för alla uppgifter i ett jobb, kan en [jobb förberedelse uppgift](/rest/api/batchservice/job/add#jobpreparationtask) anges för att kopiera alla filer.  Jobb förberedelse aktiviteten körs en gång när den första jobb aktiviteten körs på en virtuell dator men inte körs igen för efterföljande jobb aktiviteter.
* En [jobb publicerings aktivitet](/rest/api/batchservice/job/add#jobreleasetask) ska anges för att ta bort filerna per jobb när jobbet har slutförts. på så sätt undviker du att den virtuella dator disken fylls med alla jobb till gångs filer.
* När det finns flera jobb som använder samma till gångar, med endast stegvisa ändringar av till gångarna för varje jobb, kopieras alla till gångs filer fortfarande, även om bara en del av dem har uppdaterats.  Detta skulle vara ineffektivt när det finns många stora till gångar-filer.

När till gångs filer återanvänds mellan jobb, med endast stegvisa ändringar mellan jobb, är en mer effektiv men lite mer engagerad metod att lagra till gångar i den delade mappen på den virtuella datorn och synkronisera ändrade filer.

* Uppgiften att förbereda jobbet skulle utföra kopieringen med AZCopy med parametern/XO till den virtuella datorns delade mapp som anges av AZ_BATCH_NODE_SHARED_DIR-miljövariabeln.  Då kopieras endast ändrade filer till varje virtuell dator.
* Tanke måste ges till storleken på alla till gångar för att de ska få plats på den tillfälliga enheten för de virtuella datorerna i poolen.

Azure Batch har inbyggt stöd för att kopiera filer mellan ett lagrings konto och virtuella datorer i batch-poolen.  [Resurs fil](/rest/api/batchservice/job/add#resourcefile) för aktiviteter kopiera filer från lagring till virtuella datorer i pooler och kan anges för jobb förberedelse aktiviteten.  Om det finns hundratals filer går det tyvärr att nå en gräns och aktiviteter för att inte fungera.  När det finns ett stort antal till gångar rekommenderar vi att du använder kommando raden AzCopy i jobb förberedelse aktiviteten, som kan använda jokertecken och har ingen gräns.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopiera utdatafiler till Blob Storage från virtuella batch-pooler

[Utdatafiler](/rest/api/batchservice/task/add#outputfile) kan användas för att kopiera filer från en virtuell pool till lagring.  En eller flera filer kan kopieras från den virtuella datorn till ett angivet lagrings konto när uppgiften har slutförts.  De återgivna utdata bör kopieras, men det kan också vara önskvärt att lagra loggfiler.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Använda ett virtuellt blobfuse-filsystem för virtuella Linux-pooler

Blobfuse är en virtuell fil system driv rutin för Azure Blob Storage, vilket gör att du kan komma åt filer som lagras som blobbar i ett lagrings konto via Linux-filsystemet.

Pool-noder kan montera fil systemet när det startas eller så kan monteringen inträffa som en del av en jobb förberedelse aktivitet – en aktivitet som bara körs när den första aktiviteten i ett jobb körs på en nod.  Blobfuse kan konfigureras för att utnyttja både en RAMDISK och de virtuella datorerna lokalt SSD för cachelagring av filer, vilket ökar prestanda avsevärt om flera aktiviteter på en nod har åtkomst till några av samma filer.

[Exempel på mallar är tillgängliga](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) för att köra fristående V-Ray-åter givning med ett blobfuse-filsystem och kan användas som underlag för mallar för andra program.

### <a name="accessing-files"></a>Komma åt filer

Jobb aktiviteter anger sökvägar för indatafiler och utdatafiler med det monterade fil systemet.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indata till gångs filer från Blob Storage till virtuella datorer i batch-pooler

Eftersom filer bara är blobbar i Azure Storage kan standard-BLOB-API: er, verktyg och UIs användas för att kopiera filer mellan ett lokalt fil system och Blob Storage. till exempel AzCopy, Storage Explorer, Batch Explorer osv.

## <a name="using-azure-files-with-windows-vms"></a>Använda Azure Files med virtuella Windows-datorer

[Azure Files](../storage/files/storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via SMB-protokollet.  Azure Files baseras på Azure Blob Storage; Det är [kostnads effektivt](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med datareplikering till en annan region så globalt redundant.  [Skala mål](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) bör granskas för att avgöra om Azure Files ska användas med prognostiserad pool storlek och antal till gångs filer.

Det finns [dokumentation](../storage/files/storage-how-to-use-files-windows.md) som beskriver hur du monterar en Azure-filresurs.

### <a name="mounting-an-azure-files-share"></a>Montera en Azure Files resurs

För att kunna använda i batch måste en monterings åtgärd utföras varje gång en aktivitet körs eftersom det inte går att bevara anslutningen mellan aktiviteter.  Det enklaste sättet att göra detta är att använda cmdkey för att bevara autentiseringsuppgifter med start aktiviteten i konfigurationen för poolen och sedan montera resursen före varje aktivitet.

Exempel på användning av cmdkey i en mall för pooler (som är undantagen för användning i JSON-filen) – Observera att när cmdkey-anropet skiljs från net use-anropet måste användar kontexten för start aktiviteten vara samma som den som användes för att köra uppgifterna:

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

Exempel på kommando rad för jobb aktivitet:
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

Jobb aktiviteter anger sökvägar för indatafiler och utdatafiler med det monterade fil systemet, antingen med hjälp av en mappad enhet eller en UNC-sökväg.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiera indata till gångs filer från Blob Storage till virtuella datorer i batch-pooler

Azure Files stöds av alla huvud-API: er och verktyg som har Azure Storage stöd för. t. ex. AzCopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer osv.

[Azure File Sync](../storage/files/storage-sync-files-planning.md) är tillgängligt för att automatiskt synkronisera filer mellan ett lokalt fil system och en Azure-filresurs.

## <a name="next-steps"></a>Nästa steg

Mer information om lagrings alternativen finns i den djupgående dokumentationen:

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
