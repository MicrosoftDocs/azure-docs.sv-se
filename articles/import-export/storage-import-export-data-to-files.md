---
title: Använda Azure Import/Export för att överföra data till Azure Files | Microsoft Docs
description: Lär dig hur du skapar importjobb i Azure Portal för att överföra data till Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 9c13ffc597349cdd2b304889d142ca7c2f89c713
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861543"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Importera data till Azure Files med Import/Export-tjänsten i Azure

Den här artikeln innehåller stegvisa instruktioner om hur du använder Azure Import/Export-tjänsten för att på ett säkert sätt importera stora mängder data till Azure Files. För att importera data kräver tjänsten att du skickar diskenheter som stöds som innehåller dina data till ett Azure-datacenter.

Import/Export-tjänsten stöder endast import av Azure Files till Azure Storage. Export Azure Files stöds inte.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett importjobb för att överföra data Azure Files bör du noggrant granska och slutföra följande lista över förhandskrav. Du måste:

- Ha en aktiv Azure-prenumeration att använda med Import/Export-tjänsten.
- Ha minst ett Azure Storage konto. Se listan över [lagringskonton och lagringstyper som stöds för Import/Export-tjänsten.](storage-import-export-requirements.md) Information om hur du skapar ett nytt lagringskonto finns i [Så här skapar du ett lagringskonto.](../storage/common/storage-account-create.md)
- Ha tillräckligt många diskar av de typer [som stöds.](storage-import-export-requirements.md#supported-disks)
- Ha ett Windows-system som kör en [operativsystemversion som stöds.](storage-import-export-requirements.md#supported-operating-systems)
- [Ladda ned WAImportExport version 2](https://aka.ms/waiev2) på Windows-systemet. Packa upp till standardmappen `waimportexport` . Till exempel `C:\WaImportExport`.
- Ha ett FedEx/DHL-konto. Om du vill använda ett annat transportföretag än FedEx/DHL kontaktar du Azure Data Box på `adbops@microsoft.com` .
    - Kontot måste vara giltigt, ha ett saldo och måste ha leveransfunktioner för returer.
    - Generera ett spårningsnummer för exportjobbet.
    - Varje jobb ska ha ett separat spårningsnummer. Det finns inte stöd för flera jobb med samma spårningsnummer.
    - Om du inte har något transportföretagskonto går du till:
        - [Skapa ett FedEx-konto,](https://www.fedex.com/en-us/create-account.html)eller
        - [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).


## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbereda enheterna

Det här steget genererar en journalfil. Journalfilen lagrar grundläggande information som enhetsserienummer, krypteringsnyckel och lagringskontoinformation.

Förbered enheterna genom att följa stegen nedan.

1. Anslut våra diskenheter till Windows-systemet via SATA-anslutningsappar.
2. Skapa en enskild NTFS-volym på varje enhet. Tilldela volymen en enhetsbeteckning. Använd inte monteringspunkter.
3. Ändra *dataset.csv* i rotmappen där verktyget finns. Beroende på om du vill importera en fil eller mapp eller båda, lägger du till poster *idataset.csv* som liknar följande exempel.

   - **Importera en fil:** I följande exempel finns de data som ska kopieras på enheten F: . Filen *MyFile1.txt* till roten för *MyAzureFileshare1.* Om *MyAzureFileshare1* inte finns skapas den i Azure Storage konto. Mappstrukturen bibehålls.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Importera en mapp:** Alla filer och mappar under *MyFolder2* kopieras rekursivt till filresursen. Mappstrukturen bibehålls.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Flera poster kan skapas i samma fil som motsvarar mappar eller filer som importeras.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Läs mer om hur [du förbereder CSV-filen för datauppsättningen.](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)


4. Ändra *driveset.csv* i rotmappen där verktyget finns. Lägg till poster i *driveset.csv* som liknar följande exempel. Enhetsuppsättningsfilen innehåller en lista över diskar och motsvarande enhetsbeteckningar så att verktyget kan välja listan över diskar som ska förberedas korrekt.

    Det här exemplet förutsätter att två diskar är anslutna och grundläggande NTFS-volymer G:\ och H:\ skapas. H:\krypteras inte medan G: redan är krypterad. Verktyget formaterar och krypterar disken som är värd för H:\ endast (och inte G: \) .

   - **För en disk som inte är krypterad:** Ange *Kryptera för* att aktivera BitLocker-kryptering på disken.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **För en disk som redan är krypterad:** *Ange AlreadyEncrypted* och ange BitLocker-nyckeln.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Flera poster kan göras i samma fil som motsvarar flera enheter. Läs mer om [hur du förbereder CSV-filen för enheter.](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)

5. Använd alternativet `PrepImport` för att kopiera och förbereda data till diskenheten. För den första kopieringssessionen för att kopiera kataloger och/eller filer med en ny kopieringssession kör du följande kommando:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Ett importexempel visas nedan.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. En journalfil med namnet som du angav `/j:` med parametern skapas för varje körning av kommandoraden. Varje enhet som du förbereder har en journalfil som måste laddas upp när du skapar importjobbet. Enheter utan journalfiler bearbetas inte.

    > [!IMPORTANT]
    > - Ändra inte data på diskenheterna eller journalfilen när du har slutfört diskförberedelserna.

Om du vill ha fler exempel går du [till Exempel för journalfiler.](#samples-for-journal-files)

## <a name="step-2-create-an-import-job"></a>Steg 2: Skapa ett importjobb

### <a name="portal"></a>[Portal](#tab/azure-portal)

Gör följande för att skapa ett importjobb i Azure Portal.
1. Logga in på https://portal.azure.com/ .
2. Sök efter **import-/exportjobb.**

    ![Söka efter import-/exportjobb](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Välj **+ Ny**.

    ![Välj Ny för att skapa en ny ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Gör så här i **Grundläggande**:

   1. Välj en prenumeration.
   1. Välj en resursgrupp eller välj **Skapa ny** och skapa en ny.
   1. Ange ett beskrivande namn för importjobbet. Använd namnet för att spåra jobbens förlopp.
       * Namnet får bara innehålla gemener, siffror och bindestreck.
       * Namnet måste börja med en bokstav och får inte innehålla blanksteg.
   1. Välj **Importera till Azure.**

    ![Skapa importjobb – Steg 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

   Välj **Nästa: Jobbinformation >** fortsätta.

5. I **Jobbinformation:**

   1. Ladda upp journalfilerna som du skapade i föregående [steg 1: Förbered enheterna](#step-1-prepare-the-drives).
   1. Välj Azure-målregion för beställningen.
   1. Välj lagringskonto för importen.

      Listroffplatsen fylls i automatiskt baserat på regionen för det valda lagringskontot.

   1. Om du inte vill spara en utförlig logg avmarkerar du alternativet Spara utförlig logg i **blobcontainern "waimportexport".**


   ![Skapa importjobb – Steg 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   Välj **Nästa: Leverans >att** fortsätta.

4. Under **leverans:**

    1. Välj transportföretaget i listrutan. Om du vill använda ett annat transportföretag än FedEx/DHL väljer du ett befintligt alternativ i listrutan. Kontakta Azure Data Box på med `adbops@microsoft.com`  information om det transportföretag som du planerar att använda.
    1. Ange ett giltigt transportföretagskontonummer som du har skapat med det transportföretaget. Microsoft använder det här kontot för att skicka enheterna tillbaka till dig när importjobbet har slutförts.
    1. Ange ett fullständigt och giltigt kontaktnamn, telefon, e-postadress, gatuadress, ort, postnummer, region och land/region.

        > [!TIP]
        > I stället för att ange en e-postadress för en enskild användare anger du en grupp-e-postadress. Detta säkerställer att du får meddelanden även om en administratör lämnar.

    ![Skapa importjobb – steg 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Välj **Granska + skapa för** att fortsätta.

5. I ordersammanfattningen:

   1. Granska villkoren **och** välj sedan "Jag bekräftar att all information som anges är korrekt och godkänner villkoren". Verifieringen utförs sedan.
   1. Granska jobbinformationen i sammanfattningen. Anteckna jobbnamnet och leveransadressen för Azure-datacentret för att skicka tillbaka diskar till Azure. Den här informationen används senare på adressetiketten.
   1. Välj **Skapa**.

        ![Skapa importjobb – steg 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande steg för att skapa ett importjobb i Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Skapa ett jobb

1. Använd kommandot [az extension add](/cli/azure/extension#az_extension_add) för att lägga till tillägget az [import-export:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Du kan använda en befintlig resursgrupp eller skapa en. Du kan skapa en resursgrupp med hjälp av kommandot [az group create](/cli/azure/group#az_group_create):

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Du kan använda ett befintligt lagringskonto eller skapa ett. Skapa ett lagringskonto genom att köra [kommandot az storage account create:](/cli/azure/storage/account#az_storage_account_create)

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Om du vill hämta en lista över de platser som du kan skicka diskar till använder du [kommandot az import-export location list:](/cli/azure/import-export/location#az_import_export_location_list)

    ```azurecli
    az import-export location list
    ```

1. Använd kommandot [az import-export location show](/cli/azure/import-export/location#az_import_export_location_show) för att hämta platser för din region:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Kör följande az [import-export create-kommando](/cli/azure/import-export#az_import_export_create) för att skapa ett importjobb:

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
   > I stället för att ange en e-postadress för en enskild användare anger du en grupp-e-postadress. Detta säkerställer att du får meddelanden även om en administratör lämnar.


1. Använd kommandot [az import-export list](/cli/azure/import-export#az_import_export_list) för att se alla jobb för resursgruppen myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Om du vill uppdatera jobbet eller avbryta jobbet kör du [kommandot az import-export update:](/cli/azure/import-export#az_import_export_update)

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Använd följande steg för att skapa ett importjobb i Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> När **PowerShell-modulen Az.ImportExport** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Skapa ett jobb

1. Du kan använda en befintlig resursgrupp eller skapa en. Skapa en resursgrupp genom att köra [cmdleten New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Du kan använda ett befintligt lagringskonto eller skapa ett. Skapa ett lagringskonto genom att köra [cmdleten New-AzStorageAccount:](/powershell/module/az.storage/new-azstorageaccount)

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Om du vill hämta en lista över de platser som du kan skicka diskar till använder du cmdleten [Get-AzImportExportLocation:](/powershell/module/az.importexport/get-azimportexportlocation)

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Använd `Get-AzImportExportLocation` cmdleten med `Name` parametern för att hämta platser för din region:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Kör följande [New-AzImportExport-exempel](/powershell/module/az.importexport/new-azimportexport) för att skapa ett importjobb:

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
   > I stället för att ange en e-postadress för en enskild användare anger du en grupp-e-postadress. Detta säkerställer att du får meddelanden även om en administratör lämnar.

1. Använd [cmdleten Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) för att se alla jobb för resursgruppen myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Om du vill uppdatera jobbet eller avbryta jobbet kör du [cmdleten Update-AzImportExport:](/powershell/module/az.importexport/update-azimportexport)

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Steg 3: Skicka enheterna till Azure-datacentret

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Steg 4: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Steg 5: Verifiera datauppladdning till Azure

Spåra att jobbet har slutförts. När jobbet är klart kontrollerar du att dina data har laddats upp till Azure. Ta bara bort lokala data när du har verifierat att uppladdningen lyckades.

## <a name="samples-for-journal-files"></a>Exempel på journalfiler

Om **du vill lägga till** fler enheter skapar du en ny driveset-fil och kör kommandot enligt nedan.

För efterföljande kopieringssessioner till andra diskenheter än de som anges i *.csv-filen InitialDriveset* anger du en ny *.csv-fil* för driveset och anger den som ett värde för parametern `AdditionalDriveSet` . Använd samma **journalfilnamn** och ange ett **nytt sessions-ID.** Formatet för CSV-filen AdditionalDriveset är samma som InitialDriveSet-formatet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Ett importexempel visas nedan.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Om du vill lägga till ytterligare data i samma enhet använder du kommandot PrepImport för efterföljande kopieringssessioner för att kopiera ytterligare filer/kataloger.

För efterföljande kopieringssessioner till samma hårddiskar som anges *iInitialDriveset.csv* anger du samma **journalfilnamn** och anger ett **nytt sessions-ID**. du behöver inte ange lagringskontonyckeln.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Ett importexempel visas nedan.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Nästa steg

* [Visa jobb- och enhetsstatus](storage-import-export-view-drive-status.md)
* [Granska import-/exportkrav](storage-import-export-requirements.md)