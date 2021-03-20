---
title: Snabb start – skapa en behållar instans – Azure Resource Manager mall
description: I den här snabb starten använder du en Azure Resource Manager-mall för att snabbt distribuera en container som körs i en isolerad Azure Container instance.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-js
ms.date: 04/30/2020
ms.openlocfilehash: 621e3e1cef39e34656c094a39d218d5d5866fa26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91309089"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-an-arm-template"></a>Snabb start: Distribuera en behållar instans i Azure med en ARM-mall

Använd Azure Container Instances för att köra Server lös Docker-behållare i Azure med enkelhet och hastighet. Distribuera ett program till en behållar instans på begäran när du inte behöver en fullständig plattform för behållar dirigering som Azure Kubernetes-tjänsten. I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att distribuera en isolerad Docker-behållare och göra dess webb program tillgänglig med en offentlig IP-adress.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-aci-linuxcontainer-public-ip/).

:::code language="json" source="~/quickstart-templates/101-aci-linuxcontainer-public-ip/azuredeploy.json":::

Följande resurs definieras i mallen:

* **[Microsoft. ContainerInstance/containerGroups](/azure/templates/microsoft.containerinstance/containergroups)**: skapa en Azure Container Group. Den här mallen definierar en grupp som består av en enda behållar instans.

Du hittar fler Azure Container Instances mal sampel i [galleriet snabb starts mal len](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

 1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett register och en replik på en annan plats.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Välj eller ange följande värden.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och välj sedan **OK**.
    * **Plats**: Välj en plats för resursgruppen. Exempel: **centrala USA**.
    * **Namn**: acceptera det genererade namnet för instansen eller ange ett namn.
    * **Bild**: Godkänn standard avbildnings namnet. Det här exemplet på Linux-avbildningar är en liten webbapp som skrivits i Node.js som hanterar en statisk HTML-sida. 

    Acceptera standardvärden för de återstående egenskaperna.

    Granska de allmänna villkoren. Om du samtycker väljer du **Jag accepterar villkoren som anges ovan**.

    ![Mallegenskaper](media/container-instances-quickstart-template/template-properties.png)

 3. När instansen har skapats visas ett meddelande:

    ![Portal meddelande](media/container-instances-quickstart-template/deployment-notification.png)

 Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Använd Azure Portal eller ett verktyg som [Azure CLI](container-instances-quickstart.md) för att granska egenskaperna för behållar instansen.

1. I portalen söker du efter Container Instances och väljer den behållar instans som du skapade.

1. På sidan **Översikt** noterar du **status** för instansen och dess **IP-adress**.

    ![Instansöversikt](media/container-instances-quickstart-template/aci-overview.png)

2. När statusen är *igång* navigerar du till IP-adressen i webbläsaren. 

    ![App som distribuerats via Azure Container Instances visas i webbläsare](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Visa containerloggar

Att visa loggar för en containerinstans är användbart när du felsöker problem med din container eller det program som den kör.

Om du vill visa behållarens loggar går du till **Inställningar** och väljer **behållare**  >  **loggar**. Du bör se HTTP GET-begäran som genereras när du har granskat programmet i webbläsaren.

![Containerloggar i Azure-portalen](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren väljer du **ta bort** på sidan **Översikt** för behållar instansen. Bekräfta borttagningen när du uppmanas att göra det.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Azure Container instance från en offentlig Microsoft-avbildning. Om du vill skapa en container på egen hand och distribuera den från ett privat Azure-containerregister går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Självstudie: skapa en behållar avbildning för distribution till Azure Container Instances](./container-instances-tutorial-prepare-app.md)

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)