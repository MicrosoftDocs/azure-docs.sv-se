---
title: Snabbstart för Microsoft Azure Data Box Disk| Microsoft Docs
description: Använd den här snabbstarten för att snabbt distribuera Azure Data Box Disk på Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 241b7c0c07d1fbaa6a43c6be4b264424612f538a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869049"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Snabbstart: Distribuera Azure Data Box Disk med hjälp av Azure-portalen

Den här snabbstarten beskriver hur du distribuerar Azure Data Box Disk med hjälp av Azure Portal. Stegen beskriver bland annat hur du snabbt skapar en order, tar emot diskar, packar upp och ansluter diskar samt kopierar data till diskarna så att de laddas upp till Azure.

Detaljerade stegvisa instruktioner för distribution och spårning finns i [Självstudie: Beställa Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true).

::: zone-end

::: zone target="chromeless"

Den här guiden beskriver steg för steg hur du använder Azure Data Box Disk på Azure-portalen. Guiden hjälper dig att besvara följande frågor.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Krav

Innan du börjar:

- Kontrollera att din prenumeration har aktiverats för Azure Data Box-tjänsten. Aktivera din prenumeration för den här tjänsten genom att [registrera dig för tjänsten](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Gå igenom förhandskraven**: Kontrollera antalet diskar och kablar, operativsystem och annan programvara.
> - **Ansluta och låsa upp**: Anslut enheten och lås upp disken för att kopiera data.
> - **Kopiera data till disken och verifiera**: Kopiera data till de förskapade mapparna på diskarna.
> - **Returnera diskarna**: Returnera diskarna till Azure-datacentret där data överförs till ditt lagrings konto.
> - **Verifiera data i Azure**: Kontrollera att dina data har laddats upp till ditt lagringskonto innan du tar bort dem från källdataservern.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Beställa

### <a name="portal"></a>[Portal](#tab/azure-portal)

Det här steget tar ungefär 5 minuter.

1. Skapa en ny Azure Data Box-resurs på Azure Portal. 
2. Välj en prenumeration som har aktiverats för den här tjänsten och välj överföringstypen **Importera**. Ange **ursprungslandet** där dina data finns och **Azure-målregionen** för dataöverföringen.
3. Välj **Data Box Disk**. Den högsta lösningskapaciteten är 35 TB och du kan skapa flera diskorder för större datastorlekar.  
4. Ange order- och leveransinformationen. Om tjänsten är tillgänglig i din region anger du e-postadresser för aviseringsmeddelanden, läser sammanfattningen och skapar sedan ordern.

När ordern har skapats förbereds diskarna för leverans.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd de här Azure CLI-kommandona för att skapa ett Data Box Disk-jobb.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Kör kommandot [az group create](/cli/azure/group#az_group_create) om du vill skapa en resursgrupp eller använda en befintlig resursgrupp:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. Använd kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create) om du vill skapa ett lagringskonto eller använda ett befintligt lagringskonto:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Kör kommandot [az databox job create](/cli/azure/databox/job#az_databox_job_create) om du vill skapa ett Data Box-jobb med SKU:n DataBoxDisk:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Kör [az databox job update](/cli/azure/databox/job#az_databox_job_update) om du vill uppdatera ett jobb, som i det här exemplet, där du ändrar kontaktnamnet och e-postadressen:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Kör kommandot [az databox job show](/cli/azure/databox/job#az_databox_job_show) om du vill hämta information om jobbet:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Använd kommandot [az databox job list]( /cli/azure/databox/job#az_databox_job_list) om du vill visa alla Data Box-jobb för en resursgrupp:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Kör kommandot [az databox job cancel](/cli/azure/databox/job#az_databox_job_cancel) om du vill avbryta ett jobb:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Kör kommandot [az databox job delete](/cli/azure/databox/job#az_databox_job_delete) om du vill ta bort ett jobb:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Använd kommandot [az databox job list-credentials]( /cli/azure/databox/job#az_databox_job_list_credentials) om du vill visa autentiseringsuppgifter för ett Data Box-jobb:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

När ordern har skapats förbereds enheten för leverans.

---

## <a name="unpack"></a>Uppackning

Det här steget tar ungefär 5 minuter.

Data Box-disken skickas i en UPS Express-box. Öppna boxen och kontrollera att den innehåller:

- 1 till 5 USB-diskar inlindade i bubbelplast.
- En anslutningskabel per disk.
- En fraktsedel för returförsändelse.

## <a name="connect-and-unlock"></a>Ansluta och låsa upp

Det här steget tar ungefär 5 minuter.

1. Använd den medföljande kabeln för att ansluta disken till en Windows-/Linux-dator som kör en version som stöds. Mer information om operativsystemversioner som stöds finns i [Systemkrav för Azure Data Box Disk](data-box-disk-system-requirements.md). 
2. Så här låser du upp disken:

    1. På Azure Portal går du till **Allmänt > Enhetsinformation** och hämtar nyckeln.
    2. Ladda ned och extrahera det operativsystemspecifika Data Box Disk-upplåsningsverktyget på den dator som används för att kopiera data till diskarna. 
    3. Kör upplåsningsverktyget för Data Box Disk och ange nyckeln. Om du sätter i nya diskar kör du upplåsningsverktyget igen och anger nyckeln. **Använd inte BitLocker-dialogrutan eller BitLocker-nyckeln för att låsa upp disken.** Mer information om hur du låser upp diskar finns i [Låsa upp diskar på en Windows-klient](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) eller [Låsa upp diskar på en Linux-klient](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. Diskens tilldelade enhetsbeteckning visas i verktyget. Skriv ned diskens enhetsbeteckning. Du behöver den i efterföljande steg.

## <a name="copy-data-and-validate"></a>Kopiera data och verifiera

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. Enheten innehåller mapparna *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* och *DataBoxDiskImport*. Dra och släpp för att kopiera de data som ska importeras som blockblobar till mappen *BlockBlob*. På samma sätt kan du dra och släppa data som VHD/VHDX till *PageBlob*-mappen och lämpliga data till *AzureFile*. Kopiera de virtuella hårddiskar som du vill överföra som hanterade diskar till en mapp under *ManagedDisk*.

    En container skapas i Azure Storage-kontot för varje undermapp under mapparna *BlockBlob* och *PageBlob*. En filresurs skapas för en undermapp under *AzureFile*.

    Alla filer under mapparna *BlockBlob* och *PageBlob* kopieras till standardcontainern `$root` under Azure Storage-kontot. Kopiera filer till en mapp i *AzureFile*. Filer som kopieras direkt till *AzureFile*-mappen misslyckas och laddas upp som blockblobar.

    > [!NOTE]
    > - Alla containrar, blobar och filer måste följa [namngivningskonventionerna för Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Om dessa regler inte uppfylls misslyckas datauppladdningen till Azure.
    > - Kontrollera att filerna inte överskrider ~4,75 TiB för blockblobar, ~8 TiB för sidblobar och ~1 TiB för Azure Files.

2. **(Valfritt men rekommenderas)** När kopieringen är klar rekommenderar vi starkt att du åtminstone kör `DataBoxDiskValidation.cmd` som finns i mappen *DataBoxDiskImport* och väljer alternativ 1 för att validera filerna. Vi rekommenderar också att du använder alternativ 2 för att generera kontrollsummor för verifiering (det kan ta tid beroende på datastorleken). De här stegen minimerar risken för eventuella problem när data laddas upp till Azure.
3. Ta bort enheten på ett säkert sätt.

## <a name="ship-to-azure"></a>Skicka till Azure

Det här steget tar cirka 5–7 minuter att slutföra.

1. Lägg alla diskar tillsammans i det ursprungliga paketet. Använd den medföljande fraktsedeln. Om etiketten är skadad eller saknas kan du ladda ned den från portalen. Gå till **Översikt** och klicka på **Ladda ned adressetikett** i kommandofältet.
2. Lämna in det förseglade paketet på inlämningsstället.  

Data Box Disk-tjänsten skickar ett e-postmeddelande och uppdaterar orderstatusen på Azure Portal.

## <a name="verify-your-data"></a>Verifiera dina data

Hur lång tid det tar att slutföra den här åtgärden beror på datastorleken.

1. När Data Box-disken har anslutits till nätverket på Azures datacenter startar datauppladdningen till Azure automatiskt.
2. Azure Data Box-tjänsten meddelar dig via Azure Portal när datakopieringen är klar.
    
    1. Kontrollera felloggarna för eventuella fel och vidta lämpliga åtgärder.
    2. Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan.

## <a name="clean-up-resources"></a>Rensa resurser

Det här steget tar 2–3 minuter att slutföra.

Om du vill rensa resurser kan du avbryta Data Box-beställningen och ta bort ordern.

- Du kan annullera Data Box-beställningen på Azure Portal innan orderbehandlingen har påbörjats. När orderbehandlingen har påbörjats kan du inte avbryta ordern. Ordern behandlas tills den når slutskedet i processen.

    Om du vill avbryta beställningen går du till **Översikt** och klickar på **Avbryt** i kommandofältet.  

- Du kan ta bort ordern när statusen visas som **slutförd** eller **avbruten** på Azure Portal.

    Om du vill ta bort ordern går du till **Översikt** och klickar på **Ta bort** i kommandofältet.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat Azure Data Box Disk för att importera dina data till Azure. Gå vidare till nästa självstudie om du vill veta mer om Azure Data Box Disk-hantering:

> [!div class="nextstepaction"]
> [Administrera Data Box Disk via Azure Portal](data-box-portal-ui-admin.md)

::: zone-end
