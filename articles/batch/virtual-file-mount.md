---
title: Montera ett virtuellt fil system på en pool
description: Lär dig hur du monterar ett virtuellt fil system i en batch-pool.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 03/26/2021
ms.openlocfilehash: dcd56a12d8728b83cdcb7cea4c16c4aedd4251a7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105756"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montera ett virtuellt fil system i en batch-pool

Azure Batch stöder montering av moln lagring eller ett externt fil system på Windows-eller Linux-datornoder i dina batch-pooler. När en Compute-nod ansluter till en pool monteras det virtuella fil systemet och behandlas som en lokal enhet på den noden. Du kan montera fil system som Azure Files, Azure Blob Storage, NFS (Network File System), inklusive ett [AVERT vFXT-cache](../avere-vfxt/avere-vfxt-overview.md)eller CIFS (common Internet File System).

I den här artikeln får du lära dig hur du monterar ett virtuellt fil system på en pool av datornoder med hjälp av [batch Management-biblioteket för .net](/dotnet/api/overview/azure/batch).

> [!NOTE]
> Det går bara att montera ett virtuellt fil system på batch-pooler som skapats den 8 augusti 2019. Batch-pooler som skapats före det datumet kommer inte att ha stöd för den här funktionen.

## <a name="benefits-of-mounting-on-a-pool"></a>Fördelar med att montera i en pool

Genom att montera fil systemet till poolen, i stället för att låta aktiviteter Hämta egna data från en stor data uppsättning, gör det enklare och mer effektivt för aktiviteter att komma åt nödvändiga data.

Överväg ett scenario med flera aktiviteter som kräver åtkomst till en gemensam uppsättning data, t. ex. rendering av en film. Varje uppgift återger en eller flera bild rutor i taget från scenens filer. Genom att montera en enhet som innehåller scen filerna är det enklare att beräkna noder för att komma åt delade data.

Dessutom kan det underliggande fil systemet väljas och skalas oberoende av varandra baserat på prestanda och skala (data flöde och IOPS) som krävs för antalet datornoder som används för att komma åt data samtidigt. Till exempel kan en [aver-vFXT](../avere-vfxt/avere-vfxt-overview.md) distribuerad in-memory cache användas för att stödja stora bildskärms skalor med tusentals samtidiga rendering-noder, komma åt käll data som finns lokalt. För data som redan finns i molnbaserad Blob Storage kan [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) också användas för att montera dessa data som ett lokalt fil system. Blobfuse är bara tillgängligt på Linux-noder, men [Azure Files](../storage/files/storage-files-introduction.md) tillhandahåller ett liknande arbets flöde och är tillgängligt på både Windows och Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montera ett virtuellt fil system på en pool  

Genom att montera ett virtuellt fil system på en pool gör du fil systemet tillgängligt för varje Compute-nod i poolen. Fil systemet konfigureras antingen när en Compute-nod ansluter till en pool, eller när noden startas om eller avbildningas om.

Skapa ett objekt för att montera ett fil system på en pool `MountConfiguration` . Välj det objekt som passar ditt virtuella fil system: `AzureBlobFileSystemConfiguration` , `AzureFileShareConfiguration` , `NfsMountConfiguration` eller `CifsMountConfiguration` .

Alla monterings konfigurations objekt behöver följande bas parametrar. Vissa Mount-konfigurationer har parametrar som är speciella för det fil system som används, som beskrivs mer ingående i kod exemplen.

- **Konto namn eller källa**: om du vill montera en virtuell fil resurs behöver du namnet på lagrings kontot eller dess källa.
- **Relativ monterings Sök väg eller källa**: platsen för det fil system som är monterat på Compute-noden, i förhållande till standard katalogen som är `fsmounts` tillgänglig på noden via `AZ_BATCH_NODE_MOUNTS_DIR` . Den exakta platsen varierar beroende på vilket operativ system som används på noden. Till exempel mappas den fysiska platsen på en Ubuntu-nod till `mnt\batch\tasks\fsmounts` , och på en CentOS-nod mappas den till `mnt\resources\batch\tasks\fsmounts` .
- **Monterings alternativ eller alternativ för blobfuse**: de här alternativen beskriver vissa parametrar för att montera ett fil system.

När `MountConfiguration` objektet har skapats tilldelar du det till `MountConfigurationList` egenskapen när du skapar poolen. Fil systemet monteras antingen när en nod ansluter till en pool eller när noden startas om eller avbildningar.

När fil systemet är monterat skapas en miljö variabel `AZ_BATCH_NODE_MOUNTS_DIR` som pekar på platsen för de monterade fil systemen samt loggfiler, vilket är användbart för fel sökning och fel sökning. Loggfilerna beskrivs mer ingående i avsnittet [diagnosticera monterings fel](#diagnose-mount-errors) .  

> [!IMPORTANT]
> Det maximala antalet monterade fil system i en pool är 10. Mer information och andra begränsningar finns i [batch-tjänstens kvoter och begränsningar](batch-quota-limit.md#other-limits) .

## <a name="examples"></a>Exempel

Följande kod exempel visar hur du monterar en rad olika fil resurser i en pool med datornoder.

### <a name="azure-files-share"></a>Azure Files resurs

Azure Files är standard erbjudandet för Azure Cloud File System. Mer information om hur du hämtar någon av parametrarna i monterings kods exemplet finns i [använda en Azure Files dela-SMB](../storage/files/storage-how-to-use-files-windows.md) eller [använda en Azure Files-resurs med-NFS](../storage/files/storage-files-how-to-create-nfs-shares.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob File System

Ett annat alternativ är att använda Azure Blob Storage via [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Att montera ett BLOB-filsystem kräver ett `AccountKey` eller `SasKey` för ditt lagrings konto. Information om hur du hämtar dessa nycklar finns i [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md) eller [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md). Mer information och tips om hur du använder blobfuse finns i blobfuse.

Kör uppgiften som **administratör** för att få standard åtkomst till den blobfuse monterade katalogen. Blobfuse monterar katalogen i användar utrymmet och när poolen skapas monteras den som rot. I Linux är alla **Administratörs** aktiviteter rot. Alla alternativ för den säkra modulen beskrivs på [referens sidan för säkring](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Läs [vanliga frågor och svar](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) om du vill ha mer information och tips om hur du använder blobfuse. Du kan också granska [GitHub-problem i blobfuse-lagringsplatsen](https://github.com/Azure/azure-storage-fuse/issues) för att kontrol lera aktuella blobfuse-problem och-lösningar.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Network File System

NFS (Network File System) kan monteras till pool-noder, så att traditionella fil system kan nås av Azure Batch. Detta kan vara en enda NFS-server som distribueras i molnet, eller en lokal NFS-server som nås via ett virtuellt nätverk. Du kan också använda den [Avera vFXT](../avere-vfxt/avere-vfxt-overview.md) -distribuerade cache-lösningen i minnet för data intensiva HPC-uppgifter (data behandling med höga prestanda)

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Common Internet File System

Att montera [vanliga CIFS-data (Internet File System)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) till pooler är ett annat sätt att ge åtkomst till traditionella fil system. CIFS är ett fildelnings protokoll som ger en öppen och plattforms oberoende mekanism för att begära filer och tjänster för nätverks servrar. CIFS baseras på den förbättrade versionen av [SMB-protokollet (Server Message Block)](/windows-server/storage/file-server/file-server-smb-overview) för fildelning i Internet och intranät och kan användas för att montera externa fil system på Windows-noder.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnostisera monterings fel

Om en monterings konfiguration Miss lyckas kommer Compute-noden i poolen att Miss lyckas och nodens tillstånd ställs in på `unusable` . Om du vill diagnostisera ett monterings konfigurations fel kan du granska [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) egenskapen för information om felet.

Om du vill hämta loggfilerna för fel sökning använder du [OutputFiles](batch-task-output-files.md) för att ladda upp `*.log` filerna. `*.log`Filerna innehåller information om fil systemets montering på `AZ_BATCH_NODE_MOUNTS_DIR` platsen. Monterings logg filen har formatet: `<type>-<mountDirOrDrive>.log` för varje montering. Till exempel har en `cifs` Mount i en monterings katalog med namnet `test` en monterings logg fil med namnet: `cifs-test.log` .

## <a name="supported-skus"></a>SKU: er som stöds

| Publisher | Erbjudande | SKU | Azure Files resurs | Blobfuse | NFS-montering | CIFS-montering |
|---|---|---|---|---|---|---|
| batch | rendering – centos73 | Render | :heavy_check_mark: <br>Obs: kompatibel med CentOS 7,7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16,04-LTS, 18,04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | röntgen | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Ads | Linux-data-science-VM | linuxdsvm | :heavy_check_mark: <br>Obs: kompatibel med CentOS 7,4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft – Azure-Batch | CentOS-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft – Azure-Batch | CentOS-container-RDMA | 7,4 | :heavy_check_mark: <br>Obs: stöder A_8-eller 9-lagring</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft – Azure-Batch | Ubuntu-Server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-dsvm | Linux-data-science-VM-Ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS – HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | röntgen | röntgen | röntgen | röntgen |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | röntgen | röntgen | röntgen |

## <a name="networking-requirements"></a>Nätverkskrav

När du använder virtuella fil monteringar med [Azure Batch pooler i ett virtuellt nätverk](batch-virtual-network.md)bör du tänka på följande krav och se till att ingen nödvändig trafik är blockerad.

- **Azure Files**:
  - Kräver att TCP-port 445 är öppen för trafik till/från tjänst tag gen "Storage". Mer information finns i [använda en Azure-filresurs med Windows](../storage/files/storage-how-to-use-files-windows.md#prerequisites).
- **Blobfuse**:
  - Kräver att TCP-port 443 är öppen för trafik till/från tjänst tag gen "Storage".
  - De virtuella datorerna måste ha åtkomst till för https://packages.microsoft.com att kunna hämta blobfuse-och gpg-paketen. Beroende på din konfiguration kan du också behöva åtkomst till andra URL: er för att hämta ytterligare paket.
- **NFS (Network File System)**:
  - Kräver åtkomst till port 2049 (som standard. konfigurationen kan ha andra krav).
  - Virtuella datorer måste ha åtkomst till lämplig paket hanterare för att kunna hämta nfs-common-paketet (för Debian eller Ubuntu) eller NFS-utils-paketet (för CentOS). URL: en kan variera beroende på din OS-version. Beroende på din konfiguration kan du också behöva åtkomst till andra URL: er för att hämta ytterligare paket.
- **CIFS (common Internet File System)**:
  - Kräver åtkomst till TCP-port 445.
  - De virtuella datorerna måste ha åtkomst till lämplig paket hanterare för att kunna hämta CIFS-utils-paketet. URL: en kan variera beroende på din OS-version.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du monterar en Azure Files-resurs med [Windows](../storage/files/storage-how-to-use-files-windows.md) eller [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Lär dig mer om att använda och montera [blobfuse](https://github.com/Azure/azure-storage-fuse) -virtuella fil system.
- Se [Översikt över Network File System](/windows-server/storage/nfs/nfs-overview) om du vill lära dig mer om NFS och dess program.
- Se [Översikt över Microsoft SMB-protokoll och CIFS-protokoll](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) för att lära dig mer om CIFS.
