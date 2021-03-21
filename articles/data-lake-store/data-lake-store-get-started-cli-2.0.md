---
title: Hantera Azure Data Lake Storage Gen1 konto – Azure CLI
description: Använd Azure CLI för att skapa ett Data Lake Storage Gen1 konto och utföra grundläggande åtgärder.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: de98e25cf5703a43282e551a0eda20d7767c6ce8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103636"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>Kom igång med Azure Data Lake Storage Gen1 med Azure CLI

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Lär dig hur du använder Azure CLI för att skapa ett Azure Data Lake Storage Gen1-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake Storage Gen1 finns i [Översikt över data Lake Storage gen1](data-lake-store-overview.md).

Azure CLI är Azures kommandoradsmiljö för att hantera Azure-resurser. Den kan användas i Mac OS, Linux och Windows. Mer information finns i [Översikt över Azure CLI](/cli/azure). Du kan också titta på [Azure Data Lake Storage GEN1 CLI-referensen](/cli/azure/dls) för en fullständig lista över kommandon och syntax.


## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** – mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli) .

## <a name="authentication"></a>Autentisering

Den här artikeln använder en enklare metod för autentisering med Data Lake Storage Gen1 där du loggar in som slutanvändarens användare. Åtkomst nivån till Data Lake Storage Gen1 kontot och fil systemet styrs sedan av åtkomst nivån för den inloggade användaren. Det finns dock andra metoder att autentisera med Data Lake Storage Gen1, som är **autentisering** med slutanvändare eller **tjänst-till-tjänst-autentisering**. Instruktioner och mer information om hur du autentiserar finns i [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md) eller [Tjänst-till-tjänst-autentisering](./data-lake-store-service-to-service-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

1. Logga in till din Azure-prenumeration.

    ```azurecli
    az login
    ```

    Du får en kod som du ska använda i nästa steg. Använd en webbläsare för att öppna sidan https://aka.ms/devicelogin och ange koden för att autentisera dig. Du uppmanas att logga in med dina autentiseringsuppgifter.

2. När du loggar in visar fönstret alla Azure-prenumerationer som är kopplade till ditt konto. Välj en specifik prenumeration med hjälp av följande kommando.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Skapa ett Azure Data Lake Storage Gen1 konto

1. Skapa en ny resursgrupp. I följande kommando, anger du de parametervärden som du vill använda. Om platsnamnet innehåller blanksteg måste du placera det inom citattecken. Till exempel "USA, östra 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Skapa Data Lake Storage Gen1-kontot.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Skapa mappar i ett Data Lake Storage Gen1 konto

Du kan skapa mappar under ditt Azure Data Lake Storage Gen1 konto för att hantera och lagra data. Använd följande kommando för att skapa en mapp med namnet **mynewfolder** i roten för det data Lake Storage gen1 kontot.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Parametern `--folder` gör att kommandot skapar en mapp. Om den här parametern inte finns skapar kommandot en tom fil med namnet mynewfolder i roten för det Data Lake Storage Gen1 kontot.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Ladda upp data till ett Data Lake Storage Gen1 konto

Du kan ladda upp data till Data Lake Storage Gen1 direkt på rotnivå eller till en mapp som du har skapat i kontot. Fragmenten nedan visar hur du laddar upp exempeldata till mappen (**mynewfolder**) som du skapade i föregående avsnitt.

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hämta filen och lagra den i en lokal katalog på datorn, till exempel C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> För målet måste du ange den fullständiga sökvägen, inklusive filnamnet.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Lista filer i ett Data Lake Storage Gen1 konto

Använd följande kommando för att lista filerna i ett Data Lake Storage Gen1 konto.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

Resultatet av detta ska se ut ungefär så här:

```output
[
    {
        "accessTime": 1491323529542,
        "aclBit": false,
        "blockSize": 268435456,
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "length": 1589881,
        "modificationTime": 1491323531638,
        "msExpirationTime": 0,
        "name": "mynewfolder/vehicle1_09142014.csv",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "pathSuffix": "vehicle1_09142014.csv",
        "permission": "770",
        "replication": 1,
        "type": "FILE"
    }
]
```

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Byt namn på, ladda ned och ta bort data från ett Data Lake Storage Gen1 konto 

* **Om du vill byta namn på en fil** använder du följande kommando:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* Använd följande kommando **för att hämta en fil**. Kontrollera att den målsökväg som du anger redan finns.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Kommandot skapar målmappen om den inte finns.
    > 
    >

* Använd följande kommando **för att ta bort en fil**:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Om du vill ta bort mappen **mynewfolder** och filen **vehicle1_09142014_copy.csv** i samma kommando använder du parametern --recurse

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Arbeta med behörigheter och åtkomst kontrol listor för ett Data Lake Storage Gen1 konto

I det här avsnittet får du lära dig hur du hanterar ACL: er och behörigheter med hjälp av Azure CLI. En detaljerad diskussion om hur ACL: er implementeras i Azure Data Lake Storage Gen1 finns i [åtkomst kontroll i Azure Data Lake Storage gen1](data-lake-store-access-control.md).

* **Om du vill uppdatera ägaren till en fil/mapp** använder du följande kommando:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Om du vill uppdatera behörigheterna för en fil/mapp** använder du följande kommando:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Om du vill hämta åtkomstkontrollistorna för en viss sökväg** använder du följande kommando:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    Resultatet bör likna följande:

    ```output
    {
        "entries": [
        "user::rwx",
        "group::rwx",
        "other::---"
        ],
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "permission": "770",
        "stickyBit": false
    }
    ```

* **Om du vill ange en post för en åtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Om du vill ta bort en post för en åtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Om du vill ta bort en hel standardåtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Om du vill ta bort en hel åtkomstkontrollista som inte är en standardåtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Ta bort ett Data Lake Storage Gen1 konto
Använd följande kommando för att ta bort ett Data Lake Storage Gen1-konto.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

När du uppmanas, anger du **Y** för att ta bort kontot.

## <a name="next-steps"></a>Nästa steg
* [Använd Azure Data Lake Storage Gen1 för Big data-krav](data-lake-store-data-scenarios.md) 
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)