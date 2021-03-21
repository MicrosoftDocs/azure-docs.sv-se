---
title: Ändra åtkomst nycklar för lagrings kontot
titleSuffix: Azure Machine Learning
description: Lär dig hur du ändrar åtkomst nycklarna för det Azure Storage konto som används av din arbets yta. Azure Machine Learning använder ett Azure Storage konto för att lagra data och modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 787f7727e18b7055055ed2b764ddce5be20f5afc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503444"
---
# <a name="regenerate-storage-account-access-keys"></a>Återskapa åtkomst nycklar för lagrings kontot


Lär dig hur du ändrar åtkomst nycklarna för Azure Storage konton som används av Azure Machine Learning. Azure Machine Learning kan använda lagrings konton för att lagra data eller utbildade modeller.

Av säkerhets synpunkt kan du behöva ändra åtkomst nycklarna för ett Azure Storage-konto. När du återskapar åtkomst nyckeln måste Azure Machine Learning uppdateras för att använda den nya nyckeln. Azure Machine Learning kan använda lagrings kontot för både modell lagring och som ett data lager.

> [!IMPORTANT]

> Autentiseringsuppgifterna som registrerats med data lager sparas i Azure Key Vault som är kopplade till arbets ytan. Om du har [mjuk borttagning](../key-vault/general/soft-delete-overview.md) aktiverat för din Key Vault innehåller den här artikeln instruktioner för att uppdatera autentiseringsuppgifter. Om du avregistrerar data lagret och försöker registrera det igen med samma namn, kommer den här åtgärden att Miss Miss läge. Se [Aktivera mjuk borttagning för ett befintligt nyckel valv]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault) för att aktivera mjuk borttagning i det här scenariot.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .

* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).

* [Azure Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Kodfragmenten i det här dokumentet har testats med version 1.0.83 av python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Vad som behöver uppdateras

Lagrings konton kan användas av Azure Machine Learning arbets ytan (lagrings loggar, modeller, ögonblicks bilder osv.) och som ett data lager. Processen för att uppdatera arbets ytan är ett enda Azure CLI-kommando och kan köras efter uppdatering av lagrings nyckeln. Processen för att uppdatera data lager är mer involverad och du måste identifiera vilka data lager som för närvarande använder lagrings kontot och sedan registrera dem på nytt.

> [!IMPORTANT]
> Uppdatera arbets ytan med hjälp av Azure CLI och data lagret med python, på samma tidpunkt. Uppdatering av endast en eller flera är inte tillräckligt och kan orsaka fel tills båda uppdateras.

Använd följande kod för att identifiera de lagrings konton som används av dina data lager:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Den här koden söker efter registrerade data lager som använder Azure Storage och visar följande information:

* Data lager namn: namnet på det data lager som lagrings kontot är registrerat under.
* Lagrings konto namn: namnet på Azure Storage kontot.
* Container: den behållare i lagrings kontot som används vid registreringen.

Det visar också om data lagret är för en Azure-Blob eller en Azure-filresurs eftersom det finns olika metoder för att omregistrera varje typ av data lager.

Om det finns en post för det lagrings konto som du planerar att återskapa åtkomst nycklar för, sparar du data lagrings namnet, lagrings kontots namn och behållar namnet.

## <a name="update-the-access-key"></a>Uppdatera åtkomst nyckeln

Om du vill uppdatera Azure Machine Learning att använda den nya nyckeln använder du följande steg:

> [!IMPORTANT]
> Utför alla steg, uppdatera både arbets ytan med hjälp av CLI och data lager med python. Uppdatering av bara en av dem kan orsaka fel tills båda uppdateras.

1. Återskapa nyckeln. Information om hur du återskapar en åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings kontot](../storage/common/storage-account-keys-manage.md). Spara den nya nyckeln.

1. Den Azure Machine Learning arbets ytan kommer att synkronisera den nya nyckeln automatiskt och börja använda den efter en timme. Använd följande steg för att tvinga arbets ytan att synkronisera med den nya nyckeln omedelbart:

    1. Logga in på Azure-prenumerationen som innehåller arbets ytan med hjälp av följande Azure CLI-kommando:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Använd följande kommando för att uppdatera arbets ytan för att använda den nya nyckeln. Ersätt `myworkspace` med namnet på din Azure Machine Learning arbets yta och Ersätt `myresourcegroup` med namnet på den Azure-resurs grupp som innehåller arbets ytan.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Det här kommandot synkroniserar automatiskt de nya nycklarna för Azure Storage-kontot som används av arbets ytan.

1. Du kan registrera data lager på nytt som använder lagrings kontot via SDK: n eller [Azure Machine Learning Studio](https://ml.azure.com).
    1. **Om du vill registrera data lager på nytt via python SDK** använder du värdena från avsnittet [vad som behöver uppdateras](#whattoupdate) och nyckeln från steg 1 med följande kod. 
    
        Eftersom `overwrite=True` har angetts skriver den här koden över den befintliga registreringen och uppdaterar den för att använda den nya nyckeln.
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **Om du vill registrera data lager på nytt via Studio** väljer du **data lager** i den vänstra rutan i Studio. 
        1. Välj vilket data lager du vill uppdatera.
        1. Välj knappen **uppdatera autentiseringsuppgifter** längst upp till vänster. 
        1. Använd din nya åtkomst nyckel från steg 1 för att fylla i formuläret och klicka på **Spara**.
        
            Om du uppdaterar autentiseringsuppgifterna för ditt **standard data lager** slutför du det här steget och upprepar steg 2b för att synkronisera om den nya nyckeln med standard data lagret för arbets ytan. 

## <a name="next-steps"></a>Nästa steg

Mer information om hur du registrerar data lager finns i [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29) klass referensen.