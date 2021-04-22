---
title: Använda Azure Import/Export för att överföra data till Azure Blobs | Microsoft Docs
description: Lär dig hur du skapar import- och exportjobb i Azure Portal för att överföra data till och från Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 39eb6c164751ebdfa293798850a8d663fe988b82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875691"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Använd Azure Import/Export-tjänsten för att importera data till Azure Blob Storage

Den här artikeln innehåller stegvisa instruktioner om hur du använder Azure Import/Export-tjänsten för att på ett säkert sätt importera stora mängder data till Azure Blob Storage. För att importera data till Azure Blobs kräver tjänsten att du skickar krypterade diskenheter som innehåller dina data till ett Azure-datacenter.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett importjobb för att överföra data Azure Blob Storage bör du noga granska och slutföra följande lista över förhandskrav för den här tjänsten.
Du måste:

* Ha en aktiv Azure-prenumeration som kan användas för Import/Export-tjänsten.
* Ha minst ett Azure Storage med en lagringscontainer. Se listan över [lagringskonton och lagringstyper som stöds för Import/Export-tjänsten.](storage-import-export-requirements.md)
  * Information om hur du skapar ett nytt lagringskonto finns i [Så här skapar du ett lagringskonto.](../storage/common/storage-account-create.md)
  * Information om lagringscontainer finns i [Skapa en lagringscontainer.](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
* Ha tillräckligt många diskar av de typer [som stöds.](storage-import-export-requirements.md#supported-disks)
* Ha ett Windows-system som kör en [operativsystemversion som stöds.](storage-import-export-requirements.md#supported-operating-systems)
* Aktivera BitLocker i Windows-systemet. Se [Så här aktiverar du BitLocker.](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)
* [Ladda ned den senaste WAImportExport version 1](https://www.microsoft.com/download/details.aspx?id=42659) på Windows-systemet. Den senaste versionen av verktyget har säkerhetsuppdateringar som tillåter ett externt skydd för BitLocker-nyckeln och den uppdaterade funktionen för upplåsningsläge.

  * Packa upp till standardmappen `waimportexportv1` . Till exempel `C:\WaImportExportV1`.
* Ha ett FedEx/DHL-konto. Om du vill använda ett annat transportföretag än FedEx/DHL kontaktar du Azure Data Box på `adbops@microsoft.com` .
  * Kontot måste vara giltigt, ha ett saldo och måste ha leveransfunktioner för returer.
  * Generera ett spårningsnummer för exportjobbet.
  * Varje jobb ska ha ett separat spårningsnummer. Det finns inte stöd för flera jobb med samma spårningsnummer.
  * Om du inte har något transportföretagskonto går du till:
    * [Skapa ett FedEx-konto](https://www.fedex.com/en-us/create-account.html), eller
    * [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbereda enheterna

Det här steget genererar en journalfil. Journalfilen lagrar grundläggande information som enhetsserienummer, krypteringsnyckel och lagringskontoinformation.

Utför följande steg för att förbereda enheterna.

1. Anslut dina diskenheter till Windows-systemet via SATA-anslutningsappar.
2. Skapa en enskild NTFS-volym på varje enhet. Tilldela volymen en enhetsbeteckning. Använd inte monteringspunkter.
3. Aktivera BitLocker-kryptering på NTFS-volymen. Om du använder ett Windows Server-system följer du anvisningarna i Aktivera [BitLocker på Windows Server 2012 R2.](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)
4. Kopiera data till en krypterad volym. Använd dra och släpp, Robocopy eller något sådant kopieringsverktyg. En journalfil (*.fcn)* skapas i samma mapp där du kör verktyget.

   Om enheten är låst och du behöver låsa upp enheten kan stegen för att låsa upp vara olika beroende på ditt användningsfall.

   * Om du har lagt till data till en förkrypterad enhet (WAImportExport-verktyget användes inte för kryptering) använder du BitLocker-nyckeln (ett numeriskt lösenord som du anger) i popup-rutan för att låsa upp enheten.

   * Om du har lagt till data till en enhet som har krypterats av WAImportExport-verktyget använder du följande kommando för att låsa upp enheten:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Öppna ett PowerShell- eller kommandoradsfönster med administratörsbehörighet. Om du vill ändra katalogen till den uppackade mappen kör du följande kommando:

    `cd C:\WaImportExportV1`
6. Kör följande kommando för att hämta BitLocker-nyckeln för enheten:

    `manage-bde -protectors -get <DriveLetter>:`
7. Förbered disken genom att köra följande kommando. **Beroende på datastorleken kan diskförberedelse ta flera timmar i dagar.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    En journalfil skapas i samma mapp där du körde verktyget. Två andra filer skapas också – en *XML-fil* (mapp där du kör verktyget) och en *drive-manifest.xml* -fil (mapp där data finns).

    De parametrar som används beskrivs i följande tabell:

    |Alternativ  |Beskrivning  |
    |---------|---------|
    |/j:     |Namnet på journalfilen med filnamnstillägget .fcn. En journalfil genereras per enhet. Vi rekommenderar att du använder diskens serienummer som journalfilnamn.         |
    |/id:     |Sessions-ID:t. Använd ett unikt sessionsnummer för varje instans av kommandot.      |
    |/t:     |Diskens enhetsbeteckning som ska levereras. Kör till exempel `D` .         |
    |/bk:     |BitLocker-nyckeln för enheten. Det numeriska lösenordet från utdata från `manage-bde -protectors -get D:`      |
    |/srcdir:     |Enhetsbeteckningen för disken som ska levereras följt av `:\` . Till exempel `D:\`.         |
    |/dstdir:     |Namnet på målcontainern i Azure Storage.         |
    |/blobtype:     |Det här alternativet anger vilken typ av blobar du vill importera data till. För blockblobar är blobtypen `BlockBlob` och för sidblobar är det `PageBlob` .         |
    |/skipwrite:     | Anger att det inte krävs några nya data som ska kopieras och att befintliga data på disken måste förberedas.          |
    |/enablecontentmd5:     |När alternativet är aktiverat ser du till att MD5 beräknas och anges som `Content-md5` egenskap för varje blob. Använd bara det här alternativet om du vill använda `Content-md5` fältet när data har laddats upp till Azure. <br> Det här alternativet påverkar inte dataintegritetskontrollen (som inträffar som standard). Inställningen ökar den tid det tar att ladda upp data till molnet.          |
8. Upprepa föregående steg för varje disk som måste levereras. En journalfil med det angivna namnet skapas för varje körning av kommandoraden.

    > [!IMPORTANT]
    > * Tillsammans med journalfilen skapas `<Journal file name>_DriveInfo_<Drive serial ID>.xml` även en fil i samma mapp där verktyget finns. XML-filen används i stället för journalfilen när du skapar ett jobb om journalfilen är för stor.
   > * Den maximala storleken för journalfilen som portalen tillåter är 2 MB. Om journalfilen överskrider den gränsen returneras ett fel.

## <a name="step-2-create-an-import-job"></a>Steg 2: Skapa ett importjobb

### <a name="portal"></a>[Portal](#tab/azure-portal)

Utför följande steg för att skapa ett importjobb i Azure Portal.

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

   1. Ladda upp journalfilerna som du skapade i föregående [steg 1: Förbered enheterna](#step-1-prepare-the-drives). Om `waimportexport.exe version1` har använts laddar du upp en fil för varje enhet som du har förberett. Om journalfilens storlek överskrider 2 MB kan du använda som `<Journal file name>_DriveInfo_<Drive serial ID>.xml` också har skapats med journalfilen.
   1. Välj Azure-målregion för beställningen.
   1. Välj lagringskontot för importen.
      
      Listroffplatsen fylls i automatiskt baserat på regionen för det valda lagringskontot.
   1. Om du inte vill spara en utförlig logg avmarkerar du alternativet Spara utförlig logg i blobcontainern **"waimportexport".**

   ![Skapa importjobb – steg 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Välj **Nästa: Leverans >att** fortsätta.

6. Vid **leverans:**

   1. Välj transportföretaget i listrutan. Om du vill använda ett annat transportföretag än FedEx/DHL väljer du ett befintligt alternativ i listrutan. Kontakta Azure Data Box på med `adbops@microsoft.com`  information om det transportföretag som du planerar att använda.
   1. Ange ett giltigt transportföretagskontonummer som du har skapat med transportföretaget. Microsoft använder det här kontot för att skicka enheterna tillbaka till dig när importjobbet har slutförts. Om du inte har ett kontonummer skapar du ett [FedEx- eller](https://www.fedex.com/us/oadr/) [DHL-transportföretagskonto.](https://www.dhl.com/)
   1.  Ange ett fullständigt och giltigt kontaktnamn, telefon, e-postadress, gatuadress, ort, postnummer, region och land/region.

       > [!TIP]
       > I stället för att ange en e-postadress för en enskild användare anger du en grupp-e-postadress. Detta säkerställer att du får meddelanden även om en administratör lämnar.

   ![Skapa importjobb – steg 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Välj **Granska + skapa för** att fortsätta.

7. I ordersammanfattningen:

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

1. Du kan använda ett befintligt lagringskonto eller skapa ett. Skapa ett lagringskonto genom att köra [kommandot az storage account](/cli/azure/storage/account#az_storage_account_create) create:

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
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

## <a name="step-3-optional-configure-customer-managed-key"></a>Steg 3 (valfritt): Konfigurera kund hanterad nyckel

Hoppa över det här steget och gå till nästa steg om du vill använda Den Microsoft-hanterade nyckeln för att skydda bitLocker-nycklarna för enheterna. Om du vill konfigurera din egen nyckel för att skydda BitLocker-nyckeln följer du anvisningarna i Konfigurera kund hanterade nycklar med Azure Key Vault för [Azure Import/Export i Azure Portal](storage-import-export-encryption-key-portal.md).

## <a name="step-4-ship-the-drives"></a>Steg 4: Skicka enheterna

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Steg 5: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Steg 6: Verifiera datauppladdning till Azure

Spåra att jobbet har slutförts. När jobbet är klart kontrollerar du att dina data har laddats upp till Azure. Ta bara bort lokala data efter att du har verifierat att uppladdningen lyckades.

## <a name="next-steps"></a>Nästa steg

* [Visa jobb- och enhetsstatus](storage-import-export-view-drive-status.md)
* [Granska import-/exportkrav](storage-import-export-requirements.md)
