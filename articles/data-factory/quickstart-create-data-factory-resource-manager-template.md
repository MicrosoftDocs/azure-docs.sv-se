---
title: Skapa en Azure Data Factory med hjälp av en Azure Resource Manager-mall (ARM-mall)
description: Skapa ett exempel Azure Data Factory pipeline med hjälp av en Azure Resource Manager-mall (ARM-mall).
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: maghan, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: f3e76bf16b702adb26183209d36189a53b695c40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373852"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Snabb start: skapa en Azure Data Factory med ARM-mall

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Aktuell version](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en Azure-datafabrik. Den pipeline du skapar i den här datafabriken **kopierar** data från en mapp till en annan mapp i Azure Blob Storage. Om du vill se en självstudie som visar hur du **omvandlar** data med Azure Data Factory går du till [Tutorial: Transform data using Spark](transform-data-using-spark.md) (Självstudie: Omvandla data med Spark).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Den här artikeln ger inte någon detaljerad introduktion till Azure Data Factory-tjänsten. En introduktion till Azure Data Factory-tjänsten finns i [Introduktion till Azure Data Factory](introduction.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

### <a name="azure-subscription"></a>Azure-prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="create-a-file"></a>Skapa en fil

Öppna en text redigerare, till exempel **anteckningar**, och skapa en fil med namnet **emp.txt** med följande innehåll:

```emp.txt
John, Doe
Jane, Doe
```

Spara filen i mappen **C:\ADFv2QuickStartPSH** (Om mappen inte redan finns skapar du den.)

## <a name="review-template"></a>Granska mall

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/).

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

Det finns Azure-resurser definierade i mallen:

- [Microsoft. Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): definierar ett lagrings konto.
- [Microsoft. DataFactory/factors](/azure/templates/microsoft.datafactory/factories): skapa en Azure Data Factory.
- [Microsoft. DataFactory/factors/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): skapa en Azure Data Factory länkad tjänst.
- [Microsoft. DataFactory/factors/data uppsättningar](/azure/templates/microsoft.datafactory/factories/datasets): skapa en Azure Data Factory data uppsättning.
- [Microsoft. DataFactory/factors/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines): skapa en pipeline för Azure Data Factory.

Du hittar fler Azure Data Factory mal sampel i [galleriet snabb starts mal len](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Data Factory konto, ett lagrings konto och en BLOB-behållare.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="Distribuera ADF ARM-mall":::

    Om den inte anges använder du standardvärdena för att skapa Azure Data Factory resurser:

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och välj sedan **OK**.
    - **Region**: Välj en plats.  Till exempel *USA, östra*.
    - **Data Factory namn**: Använd standardvärdet.
    - **Plats**: Använd standardvärde.
    - **Lagrings konto namn**: Använd standardvärdet.
    - **BLOB-behållare**: Använd standardvärde.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Välj **gå till resurs grupp**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Resursgrupp":::

2.  Verifiera att din Azure Data Factory skapas.
    1. Ditt Azure Data Factory namn är i formatet-DataFactory \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Exempel Data Factory":::

2. Verifiera att ditt lagrings konto har skapats.
    1. Lagrings kontots namn är i formatet-lagring \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Lagrings konto":::

3. Välj det lagrings konto som skapats och välj **behållare**.
    1. På sidan **behållare** väljer du den BLOB-behållare som du skapade.
        1. BLOB container-namnet är i formatet-BLOB \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="BLOB-behållare":::

### <a name="upload-a-file"></a>Ladda upp en fil

1. På sidan **behållare** väljer du **överför**.

2. I det högra fönstret markerar du rutan **filer** och bläddrar sedan till och väljer den **emp.txt** -fil som du skapade tidigare.

3. Expandera den **avancerade** rubriken.

4. I rutan **Ladda upp till mapp** anger du *ininformation*.

5. Välj knappen **Ladda upp**. Du bör se filen **emp.txt** och uppladdningens status i listan.

6. Välj ikonen **Stäng** (a **X**) för att stänga sidan **Ladda upp BLOB** .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Ladda upp fil till indata-mapp":::

Låt sidan behållare vara öppen eftersom du kan använda den för att verifiera utdata i slutet av den här snabb starten.

### <a name="start-trigger"></a>Starta utlösare

1. Gå till sidan **data fabriker** och välj den data fabrik du skapade. 

2. Välj panelen **författare & Monitor** . 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="Övervaka & övervakare":::

2. Välj fliken **författare** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false"::: .

3. Välj pipeline created-ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="ARM-mallens pipeline":::

4. Välj **Lägg till Utlös**  >  **utlösare nu**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Utlösare":::

5. I den högra rutan under **pipeline-körning** väljer du **OK**.

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Välj fliken **övervaka** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false"::: .

2. Du ser att aktiviteten som körs är associerad med pipelinekörningen. I den här snabbstarten har pipelinen endast en aktivitet av typen: kopiera. Därför visas en körning för den aktiviteten.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Lyckad körning":::

### <a name="verify-the-output-file"></a>Verifiera utdatafilen

Pipelinen skapar automatiskt en output-mapp i BLOB-behållaren. Filen emp.txt kopieras från indatamappen till utdatamappen. 

1. På sidan **behållare** på sidan Azure Portal väljer du **Uppdatera** för att visa mappen utdata. 

2. Välj **utdata** i mapplistan.

3. Kontrollera att **emp.txt** har kopierats till utdatamappen. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Resultat":::

## <a name="clean-up-resources"></a>Rensa resurser

Du kan rensa de resurser som du skapade i snabbstarten på två sätt. Du kan [ta bort Azure-resurs gruppen](../azure-resource-manager/management/delete-resource-group.md), som innehåller alla resurser i resurs gruppen. Om du vill behålla de andra resurserna intakta ska du bara ta bort den datafabrik du har skapat i den här självstudiekursen.

När du tar bort en resursgrupp tas alla resurser inklusive datafabriker i den bort. Kör följande kommando om du vill ta bort hela resursgruppen: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Om du bara vill ta bort data fabriken och inte hela resurs gruppen kör du följande kommando: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Azure Data Factory med en ARM-mall och validerade distributionen. Om du vill veta mer om Azure Data Factory och Azure Resource Manager fortsätter du till artiklarna nedan.

- [Azure Data Factory dokumentation](index.yml)
- Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Hämta andra [Azure Data Factory arm-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)