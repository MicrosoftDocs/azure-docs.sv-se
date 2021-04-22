---
title: Använda Azure Import/Export för att exportera data från Azure Blobs | Microsoft Docs
description: Lär dig hur du skapar exportjobb i Azure Portal för att överföra data från Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 2d4885f23e775f84a412d176568d992ebe01166b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875709"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Använda Azure Import/Export-tjänsten till att exportera data från Azure Blob Storage

Den här artikeln innehåller stegvisa instruktioner om hur du använder Azure Import/Export-tjänsten för att exportera stora mängder data från Azure Blob Storage på ett säkert sätt. Tjänsten kräver att du skickar tomma enheter till Azure-datacentret. Tjänsten exporterar data från ditt lagringskonto till enheterna och skickar sedan tillbaka enheterna.

## <a name="prerequisites"></a>Förutsättningar

Innan du skapar ett exportjobb för att överföra data från Azure Blob Storage bör du noggrant granska och slutföra följande lista över förhandskrav för den här tjänsten.
Du måste:

- Ha en aktiv Azure-prenumeration som kan användas för Import/Export-tjänsten.
- Ha minst ett Azure Storage konto. Se listan över [lagringskonton och lagringstyper som stöds för Import/Export-tjänsten.](storage-import-export-requirements.md) Information om hur du skapar ett nytt lagringskonto finns i [Så här skapar du ett lagringskonto.](../storage/common/storage-account-create.md)
- Ha tillräckligt många diskar av de typer [som stöds.](storage-import-export-requirements.md#supported-disks)
- Ha ett FedEx/DHL-konto. Om du vill använda ett annat transportföretag än FedEx/DHL kontaktar du Azure Data Box på `adbops@microsoft.com` .
  - Kontot måste vara giltigt, ha ett saldo och måste ha leveransfunktioner för returer.
  - Generera ett spårningsnummer för exportjobbet.
  - Varje jobb ska ha ett separat spårningsnummer. Det finns inte stöd för flera jobb med samma spårningsnummer.
  - Om du inte har något transportföretagskonto går du till:
    - [Skapa ett FedEx-konto,](https://www.fedex.com/en-us/create-account.html)eller
    - [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Steg 1: Skapa ett exportjobb

### <a name="portal"></a>[Portal](#tab/azure-portal)

Utför följande steg för att skapa ett exportjobb i Azure Portal.

1. Logga in på <https://portal.azure.com/> .
2. Sök efter **import-/exportjobb.**

    ![Sök efter import-/exportjobb](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Välj **+ Ny**.

    ![Välj + Ny för att skapa en ny ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Gör så här i **Grundläggande**:

   1. Välj en prenumeration.
   1. Välj en resursgrupp eller välj **Skapa ny** och skapa en ny.
   1. Ange ett beskrivande namn för importjobbet. Använd namnet för att spåra förloppet för dina jobb.
       * Namnet får bara innehålla gemener, siffror och bindestreck.
       * Namnet måste börja med en bokstav och får inte innehålla blanksteg.

   1. Välj **Exportera från Azure**.

    ![Grundläggande alternativ för en exportorder](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Välj **Nästa: Jobbinformation >** fortsätta.

5. I **Jobbinformation:**

   1. Välj den Azure-region där dina data finns för närvarande.
   1. Välj det lagringskonto som du vill exportera data från. Använd ett lagringskonto nära din plats.

      Avlämningsplats fylls i automatiskt baserat på regionen för det valda lagringskontot.

   1. Ange vilka blobdata som ska exporteras från ditt lagringskonto till din tomma enhet eller dina enheter. Välj någon av följande tre metoder.

      - Välj att **Exportera alla** blobdata i lagringskontot.

        ![Exportera alla](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Välj **Valda containrar och blobar** och ange containrar och blobar som ska exporteras. Du kan använda fler än en av urvalsmetoderna. Om du **väljer alternativet** Lägg till öppnas en panel till höger där du kan lägga till dina valsträngar.

        |Alternativ|Beskrivning|
        |------|-----------|      
        |**Lägga till containrar**|Exportera alla blobar i en container.<br>Välj **Lägg till containrar** och ange varje containernamn.|
        |**Lägga till blobar**|Ange enskilda blobar som ska exporteras.<br>Välj **Lägg till blobar.** Ange sedan den relativa sökvägen till bloben, som börjar med containernamnet. Använd *$root för* att ange rotcontainern.<br>Du måste ange blobsökvägarna i giltigt format för att undvika fel under bearbetningen, som du ser i den här skärmbilden. Mer information finns i Exempel [på giltiga blobsökvägar.](#examples-of-valid-blob-paths)|
        |**Lägga till prefix**|Använd ett prefix för att välja en uppsättning containrar med liknande namn eller blobar med liknande namn i en container. Prefixet kan vara prefixet för containernamnet, det fullständiga containernamnet eller ett fullständigt containernamn följt av prefixet för blobnamnet. |

        ![Exportera valda containrar och blobar](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Välj **Exportera från bloblistfil (XML-format)** och välj en XML-fil som innehåller en lista över sökvägar och prefix för blobarna som ska exporteras från lagringskontot. Du måste konstruera XML-filen och lagra den i en container för lagringskontot. Filen får inte vara tom.

      > [!IMPORTANT]
      > Om du använder en XML-fil för att välja vilka blobar som ska exporteras kontrollerar du att XML-filen innehåller giltiga sökvägar och/eller prefix. Om filen är ogiltig eller om inga data matchar de angivna sökvägarna avslutas ordern med partiella data eller inga data exporteras.

       Information om hur du lägger till en XML-fil i en container finns i [Exportera order med XML-filen](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file).

      ![Exportera från bloblistefil](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Om en blob som ska exporteras används vid datakopiering tar Azure Import/Export-tjänsten en ögonblicksbild av bloben och kopierar ögonblicksbilden.

   Välj **Nästa: Leverans >att** fortsätta.

6. Under **leverans:**

    - Välj transportföretaget i listrutan. Om du vill använda ett annat transportföretag än FedEx/DHL väljer du ett befintligt alternativ i listrutan. Kontakta Azure Data Box på med `adbops@microsoft.com`  information om det transportföretag som du planerar att använda.
    - Ange ett giltigt transportföretagskontonummer som du har skapat med transportföretaget. Microsoft använder det här kontot för att skicka enheterna tillbaka till dig när exportjobbet har slutförts.
    - Ange ett fullständigt och giltigt kontaktnamn, telefon, e-postadress, gatuadress, ort, postnummer, delstat/provins och land/region.

        > [!TIP]
        > I stället för att ange en e-postadress för en enskild användare anger du en grupp-e-postadress. Detta säkerställer att du får meddelanden även om en administratör lämnar.

    Välj **Granska + skapa för** att fortsätta.

7. I **Granska + skapa:**

   1. Granska informationen om jobbet.
   1. Anteckna jobbnamnet och ange leveransadressen för Azure-datacentret för leverans av diskar till Azure.

      > [!NOTE]
      > Skicka alltid diskarna till datacentret som anges i Azure Portal. Om diskarna levereras till fel datacenter bearbetas inte jobbet.

   1. Granska villkoren **för** din beställning för att få sekretess och borttagning av källdata. Om du godkänner villkoren markerar du kryssrutan under villkoren. Valideringen av ordern börjar.

   ![Granska och skapa din exportbeställning](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. När valideringen har passerat väljer du **Skapa**.

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande steg för att skapa ett exportjobb i Azure Portal.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Skapa ett jobb

1. Använd kommandot [az extension add](/cli/azure/extension#az_extension_add) för att lägga till tillägget az [import-export:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Om du vill hämta en lista över de platser som du kan ta emot diskar från använder du [kommandot az import-export location list:](/cli/azure/import-export/location#az_import_export_location_list)

    ```azurecli
    az import-export location list
    ```

1. Kör följande az [import-export create-kommando](/cli/azure/import-export#az_import_export_create) för att skapa ett exportjobb som använder ditt befintliga lagringskonto:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > I stället för att ange en e-postadress för en enskild användare anger du en grupp-e-postadress. Detta säkerställer att du får meddelanden även om en administratör lämnar.

   Det här jobbet exporterar alla blobar i ditt lagringskonto. Du kan ange en blob för export genom att ersätta det här värdet för **--export**:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Det här parametervärdet exporterar bloben *medlogo.bmp* i rotcontainern.

   Du kan också välja alla blobar i en container med hjälp av ett prefix. Ersätt det här värdet för **--export**:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Mer information finns i Exempel [på giltiga blobsökvägar.](#examples-of-valid-blob-paths)

   > [!NOTE]
   > Om bloben som ska exporteras används vid datakopiering tar Azure Import/Export-tjänsten en ögonblicksbild av bloben och kopierar ögonblicksbilden.

1. Använd kommandot [az import-export list](/cli/azure/import-export#az_import_export_list) för att se alla jobb för resursgruppen myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Om du vill uppdatera jobbet eller avbryta jobbet kör du [kommandot az import-export update:](/cli/azure/import-export#az_import_export_update)

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Använd följande steg för att skapa ett exportjobb i Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> När **PowerShell-modulen Az.ImportExport** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Skapa ett jobb

1. Om du vill hämta en lista över de platser som du kan ta emot diskar från använder du cmdleten [Get-AzImportExportLocation:](/powershell/module/az.importexport/get-azimportexportlocation)

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Kör följande [New-AzImportExport-exempel](/powershell/module/az.importexport/new-azimportexport) för att skapa ett exportjobb som använder ditt befintliga lagringskonto:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > I stället för att ange en e-postadress för en enskild användare anger du en grupp-e-postadress. Detta säkerställer att du får meddelanden även om en administratör lämnar.

   Det här jobbet exporterar alla blobar i ditt lagringskonto. Du kan ange en blob för export genom att ersätta det här värdet för **-ExportBlobListblobPath:**

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Det här parametervärdet exporterar bloben *medlogo.bmp* i rotcontainern.

   Du kan också välja alla blobar i en container med hjälp av ett prefix. Ersätt det här värdet för **-ExportBlobListblobPath:**

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Mer information finns i Exempel [på giltiga blobsökvägar.](#examples-of-valid-blob-paths)

   > [!NOTE]
   > Om bloben som ska exporteras används vid datakopiering tar Azure Import/Export-tjänsten en ögonblicksbild av bloben och kopierar ögonblicksbilden.

1. Använd [cmdleten Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) för att se alla jobb för resursgruppen myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Om du vill uppdatera jobbet eller avbryta jobbet kör du [cmdleten Update-AzImportExport:](/powershell/module/az.importexport/update-azimportexport)

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Steg 2: Skicka enheterna

Om du inte vet hur många enheter du behöver går du till [Kontrollera antalet enheter.](#check-the-number-of-drives) Om du känner till antalet enheter fortsätter du med att skicka enheterna.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Steg 3: Uppdatera jobbet med spårningsinformation

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Steg 4: Ta emot diskarna

När instrumentpanelen rapporterar att jobbet är klart levereras diskarna till dig och spårningsnumret för leveransen är tillgängligt på portalen.

1. När du har fått enheterna med exporterade data måste du hämta BitLocker-nycklarna för att låsa upp enheterna. Gå till exportjobbet i Azure Portal. Klicka **på fliken Importera/exportera.**
2. Välj och klicka på exportjobbet i listan. Gå till **Kryptering** och kopiera nycklarna.

   ![Visa BitLocker-nycklar för exportjobb](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Använd BitLocker-nycklarna för att låsa upp diskarna.

Exporten är klar.

## <a name="step-5-unlock-the-disks"></a>Steg 5: Lås upp diskarna

Använd följande kommando för att låsa upp enheten:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Här är ett exempel på exempelindata.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

För stunden kan du ta bort jobbet eller lämna det. Jobb tas bort automatiskt efter 90 dagar.

## <a name="check-the-number-of-drives"></a>Kontrollera antalet enheter

Det *här* valfria steget hjälper dig att fastställa antalet enheter som krävs för exportjobbet. Utför det här steget på ett Windows-system som kör en [operativsystemversion som stöds.](storage-import-export-requirements.md#supported-operating-systems)

1. [Ladda ned WAImportExport version 1](https://www.microsoft.com/download/details.aspx?id=42659) på Windows-systemet.
2. Packa upp till standardmappen `waimportexportv1` . Till exempel `C:\WaImportExportV1`.
3. Öppna ett PowerShell- eller kommandoradsfönster med administratörsbehörighet. Om du vill ändra katalogen till den uppackade mappen kör du följande kommando:

   `cd C:\WaImportExportV1`

4. Kontrollera antalet diskar som krävs för de valda blobarna genom att köra följande kommando:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Parametrarna beskrivs i följande tabell:

    |Kommandoradsparameter|Description|
    |--------------------------|-----------------|
    |**/logdir:**|Valfritt. Loggkatalogen. Utförliga loggfiler skrivs till den här katalogen. Om inget anges används den aktuella katalogen som loggkatalog.|
    |**/sn:**|Krävs. Namnet på lagringskontot för exportjobbet.|
    |**/sk:**|Krävs endast om en CONTAINER-SAS inte har angetts. Kontonyckeln för lagringskontot för exportjobbet.|
    |**/csas:**|Krävs endast om en lagringskontonyckel inte har angetts. Container-SAS för att visa de blobar som ska exporteras i exportjobbet.|
    |**/ExportBlobListFile:**|Krävs. Sökvägen till XML-filen som innehåller en lista över blobsökvägar eller blobsökvägsprefix för de blobar som ska exporteras. Filformatet som används i `BlobListBlobPath` elementet i åtgärden Placera [jobb](/rest/api/storageimportexport/jobs) i import-/exporttjänstens REST API.|
    |**/DriveSize:**|Krävs. Storleken på enheter som ska användas för ett exportjobb, *till* exempel , 500 GB, 1,5 TB.|

    Se ett [exempel på previewExport-kommandot](#example-of-previewexport-command).

5. Kontrollera att du kan läsa/skriva till de enheter som ska levereras för exportjobbet.

### <a name="example-of-previewexport-command"></a>Exempel på PreviewExport-kommandon

Följande exempel visar `PreviewExport` kommandot:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Filen med listan över exportblobar kan innehålla blobnamn och blobprefix, som du ser här:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Azure Import/Export-verktyget visar en lista över alla blobar som ska exporteras och beräknar hur de ska packas in på enheter av den angivna storleken, med hänsyn till eventuella nödvändiga omkostnader, och beräknar sedan antalet enheter som behövs för att lagra blobar och information om enhetsanvändning.

Här är ett exempel på utdata, där informationsloggar utelämnas:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Exempel på giltiga blobsökvägar

I följande tabell visas exempel på giltiga blobsökvägar:

   | Väljare | Blobsökväg | Description |
   | --- | --- | --- |
   | Börjar med |/ |Exporterar alla blobar i lagringskontot |
   | Börjar med |/$root/ |Exporterar alla blobar i rotcontainern |
   | Börjar med |/book |Exporterar alla blobar i alla containrar som börjar med **prefixboken** |
   | Börjar med |/music/ |Exporterar alla blobar i container **musik** |
   | Börjar med |/music/love |Exporterar alla blobar i container **musik som** börjar med **prefixet love** |
   | Lika med |$root/logo.bmp |Exporterar **bloblogo.bmp** i rotcontainern |
   | Lika med |videor/story.mp4 |Exporterar blob **story.mp4i** **containervideor** |

## <a name="next-steps"></a>Nästa steg

- [Visa jobb- och enhetsstatus](storage-import-export-view-drive-status.md)
- [Granska import-/exportkrav](storage-import-export-requirements.md)
