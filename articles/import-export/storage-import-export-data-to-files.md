---
title: Använda Azure import/export för att överföra data till Azure Files | Microsoft Docs
description: Lär dig hur du skapar import jobb i Azure Portal för att överföra data till Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: b62c3c4be4fdffd9f509b86d248cd028518ae89a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181950"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Importera data till Azure Files med Import/Export-tjänsten i Azure

Den här artikeln innehåller stegvisa instruktioner för hur du använder Azure import/export-tjänsten för att importera stora mängder data på ett säkert sätt till Azure Files. För att importera data kräver tjänsten att du levererar disk enheter som stöds och som innehåller dina data till ett Azure-datacenter.

Import/export-tjänsten stöder endast import av Azure Files till Azure Storage. Det finns inte stöd för att exportera Azure Files.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett import jobb för att överföra data till Azure Files bör du noggrant granska och slutföra följande lista över krav. Du måste:

- Ha en aktiv Azure-prenumeration som ska användas med import/export-tjänsten.
- Ha minst ett Azure Storage konto. Se listan över [lagrings konton och lagrings typer som stöds för import/export-tjänsten](storage-import-export-requirements.md). Information om hur du skapar ett nytt lagrings konto finns i [så här skapar du ett lagrings konto](../storage/common/storage-account-create.md).
- Har tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks).
- Ha ett Windows-system som kör en [operativ system version som stöds](storage-import-export-requirements.md#supported-operating-systems).
- [Ladda ned WAImportExport version 2](https://aka.ms/waiev2) på Windows-systemet. Zippa upp till standardmappen `waimportexport` . Till exempel `C:\WaImportExport`.
- Ha ett FedEx-/DHL-konto. Om du vill använda en annan operatör än FedEx/DHL kontaktar du Azure Data Box drifts team på `adbops@microsoft.com` .
    - Kontot måste vara giltigt, måste ha ett saldo och måste ha funktioner för retur leverans.
    - Generera ett spårnings nummer för export jobbet.
    - Varje jobb ska ha ett separat spårningsnummer. Det finns inte stöd för flera jobb med samma spårningsnummer.
    - Om du inte har ett transport företags konto går du till:
        - [Skapa ett FedEx-konto](https://www.fedex.com/en-us/create-account.html)eller
        - [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).


## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbered enheterna

I det här steget skapas en journal fil. Journal filen lagrar grundläggande information, till exempel enhets serie nummer, krypterings nyckel och lagrings konto information.

Utför följande steg för att förbereda enheterna.

1. Anslut våra disk enheter till Windows-systemet via SATA-anslutningar.
2. Skapa en enda NTFS-volym på varje enhet. Tilldela volymen en enhets beteckning. Använd inte mountpoints.
3. Ändra *dataset.csv* -filen i rotmappen där verktyget finns. Beroende på om du vill importera en fil eller mapp eller båda lägger du till poster i *dataset.csv* -filen som liknar följande exempel.

   - **Så här importerar du en fil**: i följande exempel finns de data som ska kopieras på enheten F:. Filen *MyFile1.txt*  kopieras till roten i *MyAzureFileshare1*. Om *MyAzureFileshare1* inte finns skapas den i Azure Storage-kontot. Mappstrukturen upprätthålls.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Så här importerar du en mapp**: alla filer och mappar under *MyFolder2* kopieras till fileshare rekursivt. Mappstrukturen upprätthålls.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Flera poster kan göras i samma fil som motsvarar mappar eller filer som importeras.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Läs mer om hur [du förbereder data uppsättningens CSV-fil](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).


4. Ändra *driveset.csv* -filen i rotmappen där verktyget finns. Lägg till poster i *driveset.csv* -filen som liknar följande exempel. Driveset-filen innehåller en lista över diskar och motsvarande enhets beteckningar så att verktyget kan välja listan över diskar som ska förberedas korrekt.

    I det här exemplet förutsätts att två diskar är kopplade till och enkla NTFS-volymer G:\ och H:\ skapas. H:\is har inte krypterats medan G: redan är krypterad. Verktyget formaterar och krypterar den disk som är värd för H:\ endast (och inte G: \) .

   - **För en disk som inte är krypterad**: ange *kryptera* för att aktivera BitLocker-kryptering på disken.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **För en disk som redan är krypterad**: ange *AlreadyEncrypted* och ange BitLocker-nyckeln.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Flera poster kan göras i samma fil som motsvarar flera enheter. Lär dig mer om [att förbereda DRIVESET CSV-filen](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).

5. Använd `PrepImport` alternativet för att kopiera och förbereda data till disk enheten. Kör följande kommando för att kopiera kataloger och/eller filer med en ny Copy-session för den första kopierings sessionen:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Ett exempel på en import visas nedan.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. En journal fil med namnet du angav med `/j:` parameter skapas för varje körning av kommando raden. Varje enhet som du förbereder har en journal fil som måste överföras när du skapar import jobbet. Enheter utan Journal-filer bearbetas inte.

    > [!IMPORTANT]
    > - Ändra inte data på disk enheterna eller journal filen när du har slutfört disk förberedelsen.

Fler exempel finns i [exempel på Journal-filer](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Steg 2: skapa ett import jobb

### <a name="portal"></a>[Portal](#tab/azure-portal)

Utför följande steg för att skapa ett import jobb i Azure Portal.
1. Logga in på https://portal.azure.com/ .
2. Sök efter **import/export-jobb**.

    ![Sök på import/export-jobb](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Välj **+ Ny**.

    ![Välj nytt om du vill skapa en ny ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Gör så här i **Grundläggande**:

   1. Välj en prenumeration.
   1. Välj en resurs grupp eller Välj **Skapa ny** och skapa en ny.
   1. Ange ett beskrivande namn för import jobbet. Använd namnet för att följa förloppet för dina jobb.
       * Namnet får bara innehålla gemena bokstäver, siffror och bindestreck.
       * Namnet måste börja med en bokstav och får inte innehålla blank steg.
   1. Välj **Importera till Azure**.

    ![Skapa import jobb – steg 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

   Välj **Nästa: jobb information >** för att gå vidare.

5. I **jobb information**:

   1. Ladda upp de Journal-filer som du skapade under föregående [steg 1: Förbered enheterna](#step-1-prepare-the-drives).
   1. Välj mål Azure-region för ordern.
   1. Välj lagrings kontot för importen.

      DropOff-platsen fylls i automatiskt baserat på den region där det valda lagrings kontot finns.

   1. Om du inte vill spara en utförlig logg rensar du **Spara utförlig logg i alternativet ' waimportexport ' BLOB container** .


   ![Skapa import jobb – steg 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   Välj **Nästa: leverera >** för att gå vidare.

4. I **leverans**:

    1. Välj transport företags leverantör i list rutan. Om du vill använda en annan operatör än FedEx/DHL väljer du ett befintligt alternativ i list rutan. Kontakta Azure Data Box drifts teamet på `adbops@microsoft.com`  med information om den operatör som du planerar att använda.
    1. Ange ett giltigt transportföretags konto nummer som du har skapat med transport företaget. Microsoft använder det här kontot för att skicka tillbaka enheterna till dig när ditt import jobb har slutförts.
    1. Ange ett fullständigt och giltigt kontakt namn, telefon, e-postadress, gatuadress, ort, post, delstat/provins och land/region.

        > [!TIP]
        > Ange en grupp-e-postadress i stället för att ange en e-postadress för en enskild användare. Detta säkerställer att du får meddelanden även om en administratör lämnar.

    ![Skapa import jobb – steg 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Välj **Granska + skapa** för att fortsätta.

5. I order sammanfattningen:

   1. Granska **villkoren** och välj sedan "Jag bekräftar att all information som anges är korrekt och samtycker till de allmänna villkoren". Verifieringen utförs sedan.
   1. Granska jobb informationen som visas i sammanfattningen. Anteckna jobb namnet och leverans adressen till Azure-datacenter för att leverera diskar tillbaka till Azure. Den här informationen används senare på frakt etiketten.
   1. Välj **Skapa**.

        ![Skapa import jobb – steg 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande steg för att skapa ett import jobb i Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Skapa ett jobb

1. Använd kommandot [AZ Extension Add](/cli/azure/extension#az_extension_add) för att lägga till [AZ import-export-](/cli/azure/ext/import-export/import-export) tillägget:

    ```azurecli
    az extension add --name import-export
    ```

1. Du kan använda en befintlig resurs grupp eller skapa en. Du kan skapa en resursgrupp med hjälp av kommandot [az group create](/cli/azure/group#az_group_create):

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Du kan använda ett befintligt lagrings konto eller skapa ett. Skapa ett lagrings konto genom att köra kommandot [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) :

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Om du vill hämta en lista över platser som du kan leverera diskar till använder du kommandot [AZ import-export location location List](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Använd kommandot [AZ import-export location show](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) för att hämta platser för din region:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Kör följande [AZ import-export Create-](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) kommando för att skapa ett import jobb:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Ange en grupp-e-postadress i stället för att ange en e-postadress för en enskild användare. Detta säkerställer att du får meddelanden även om en administratör lämnar.


1. Använd kommandot [AZ import-export List](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) för att visa alla jobb för resurs gruppen myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Om du vill uppdatera jobbet eller avbryta jobbet kör du kommandot [AZ import-export Update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) :

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Använd följande steg för att skapa ett import jobb i Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Även om **AZ. ImportExport** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten. När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Skapa ett jobb

1. Du kan använda en befintlig resurs grupp eller skapa en. Skapa en resurs grupp genom att köra cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Du kan använda ett befintligt lagrings konto eller skapa ett. Skapa ett lagrings konto genom att köra cmdleten [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) :

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Använd cmdleten [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) för att hämta en lista över platser där du kan leverera diskar:

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Använd `Get-AzImportExportLocation` cmdleten med `Name` parametern för att hämta platser för din region:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Kör följande [New-AzImportExport-](/powershell/module/az.importexport/new-azimportexport) exempel för att skapa ett import jobb:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > Ange en grupp-e-postadress i stället för att ange en e-postadress för en enskild användare. Detta säkerställer att du får meddelanden även om en administratör lämnar.

1. Använd cmdleten [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) för att se alla jobb för resurs gruppen myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Om du vill uppdatera jobbet eller avbryta jobbet kör du cmdleten [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) :

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Steg 3: leverera enheterna till Azure-datacentret

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Steg 4: uppdatera jobbet med spårnings information

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Steg 5: kontrol lera att data laddas upp till Azure

Spåra jobbet till slutfört. När jobbet har slutförts kontrollerar du att dina data har laddats upp till Azure. Ta bara bort lokala data när du har kontrollerat att överföringen har slutförts.

## <a name="samples-for-journal-files"></a>Exempel på Journal-filer

Om du vill **lägga till fler enheter** skapar du en ny driveset-fil och kör kommandot enligt nedan.

För efterföljande Copy-sessioner till andra disk enheter än de som anges i filen *InitialDriveset. csv* anger du en ny driveset *. csv* -fil och anger den som ett värde för parametern `AdditionalDriveSet` . Använd **samma Journal fil** namn och ange ett **nytt sessions-ID**. Formatet på AdditionalDriveset CSV-filen är samma som InitialDriveSet-formatet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Ett exempel på en import visas nedan.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Om du vill lägga till ytterligare data till samma driveset använder du kommandot PrepImport för efterföljande kopierings-sessioner för att kopiera ytterligare filer/kataloger.

För efterföljande kopierings sessioner till samma hård disk enheter som anges i *InitialDriveset.csv* -filen anger du **samma Journal fil** namn och anger ett **nytt sessions-ID**. du behöver inte ange lagrings konto nyckeln.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Ett exempel på en import visas nedan.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Nästa steg

* [Visa jobb-och enhets status](storage-import-export-view-drive-status.md)
* [Granska import/export-krav](storage-import-export-requirements.md)