---
title: Hantera Azure Data Lake Analytics med Azure CLI
description: Den här artikeln beskriver hur du använder Azure CLI för att hantera Data Lake Analytics jobb, data källor & användare.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 01/29/2018
ms.openlocfilehash: 19b471d85a52fe38b72ad55847d022fb56b3c4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220932"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Hantera Azure Data Lake Analytics med hjälp av Azures kommando rads gränssnitt (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Lär dig hur du hanterar Azure Data Lake Analytics-konton, data källor, användare och jobb med hjälp av Azure CLI. Klicka på fliken ovan om du vill se hanterings ämnen med andra verktyg.

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien måste du ha följande resurser:

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Azure CLI. Se [Installera och konfigurera Azure CLI](/cli/azure/install-azure-cli).

  - Hämta och installera **förhandsversionen** [Azure CLI-verktyg](https://github.com/MicrosoftBigData/AzureDataLake/releases) för att kunna slutföra den här demon.

- Autentisera med hjälp av `az login` kommandot och välj den prenumeration som du vill använda. Mer information om autentisering med ett arbets- eller skolkonto finns i [Anslut till en Azure-prenumeration från Azure CLI](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Nu kan du komma åt Data Lake Analytics-och Data Lake Store kommandon. Kör följande kommando för att Visa Data Lake Store och Data Lake Analytics kommandon:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Hantera konton

Innan du kör Data Lake Analytics jobb måste du ha ett Data Lake Analytics-konto. Till skillnad från Azure HDInsight betalar du inte för ett analys konto när det inte kör ett jobb. Du betalar bara för den tid då jobbet körs.  Mer information finns i [Azure Data Lake Analytics översikt](data-lake-analytics-overview.md).

### <a name="create-accounts"></a>Skapa konton

Kör följande kommando för att skapa ett Data Lake-konto,

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Uppdatera konton

Följande kommando uppdaterar egenskaperna för ett befintligt Data Lake Analytics-konto

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Lista över konton

Lista Data Lake Analytics konton inom en angiven resurs grupp

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Hämta information om ett konto

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Ta bort ett konto

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Hantera datakällor

Data Lake Analytics stöder för närvarande följande två data Källor:

- [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
- [Azure Storage](../storage/common/storage-introduction.md)

När du skapar ett analys konto måste du ange ett Azure Data Lake Storage-konto som standard lagrings konto. Standard Data Lake lagrings kontot används för att lagra jobb-metadata och jobb gransknings loggar. När du har skapat ett analys konto kan du lägga till ytterligare Data Lake Storage konton och/eller Azure Storage konto.

### <a name="find-the-default-data-lake-store-account"></a>Hitta standard Data Lake Stores kontot

Du kan visa standard Data Lake Store kontot som används genom att köra `az dla account show` kommandot. Standard konto namnet visas under egenskapen defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Lägg till ytterligare Blob Storage-konton

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Endast korta Blob Storage-namn stöds. Använd inte FQDN, till exempel "myblob.blob.core.windows.net".
>

### <a name="add-additional-data-lake-store-accounts"></a>Lägg till ytterligare Data Lake Store konton

Följande kommando uppdaterar det angivna Data Lake Analytics-kontot med ett ytterligare Data Lake Store-konto:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Uppdatera befintlig data Källa

Så här uppdaterar du en befintlig Blob Storage-konto nyckel:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Visa lista över data källor

Så här visar du Data Lake Store-konton:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Så här visar du Blob Storage-kontot:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Skärm bild som visar Azure C L I med informationen "dataLakeStoreAccounts:" markerad.](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Ta bort data källor

Ta bort ett Data Lake Store-konto:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Ta bort ett Blob Storage-konto:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Hantera jobb

Du måste ha ett Data Lake Analytics-konto innan du kan skapa ett jobb.  Mer information finns i [hantera data Lake Analytics-konton](#manage-accounts).

### <a name="list-jobs"></a>Lista jobb

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data källa för Data Lake Analytics lista](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Hämta jobb information

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Skicka jobb

> [!NOTE]
> Standard prioriteten för ett jobb är 1000 och standard graden av parallellitet för ett jobb är 1.
>
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Avbryt jobb

Använd list kommandot för att hitta jobb-ID och Använd sedan Avbryt för att avbryta jobbet.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pipelines och upprepningar

### <a name="get-information-about-pipelines-and-recurrences"></a>Hämta information om pipelines och upprepningar

Använd `az dla job pipeline`-kommandon för att visa information om pipeline för jobb som skickats tidigare.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Använd `az dla job recurrence`-kommandon för att visa information om upprepningar för jobb som skickats tidigare.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Kom igång med Data Lake Analytics med hjälp av Azure-portalen](data-lake-analytics-get-started-portal.md)
- [Hantera Azure Data Lake Analytics med Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
