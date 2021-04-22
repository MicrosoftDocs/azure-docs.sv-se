---
title: Snabbstart – Skapa en containerinstans – Azure Resource Manager mall
description: I den här snabbstarten använder du en Azure Resource Manager för att snabbt distribuera en containeriserad webbapp som körs i en isolerad Azure-containerinstans.
services: azure-resource-manager
ms.date: 04/30/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- devx-track-js
- mode-arm
ms.openlocfilehash: 2f57e86421f7522467a3f4adf4a4e9a21456ba4c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878748"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-an-arm-template"></a>Snabbstart: Distribuera en containerinstans i Azure med hjälp av en ARM-mall

Använd Azure Container Instances för att köra serverlösa Docker-containrar i Azure med enkelhet och hastighet. Distribuera ett program till en containerinstans på begäran när du inte behöver en fullständig plattform för containerorkestrering som Azure Kubernetes Service. I den här snabbstarten använder du en Azure Resource Manager-mall (ARM-mall) för att distribuera en isolerad Docker-container och göra dess webbapp tillgänglig med en offentlig IP-adress.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-aci-linuxcontainer-public-ip/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.containerinstance/aci-linuxcontainer-public-ip/azuredeploy.json":::

Följande resurs definieras i mallen:

* **[Microsoft.ContainerInstance/containerGroups:](/azure/templates/microsoft.containerinstance/containergroups)** skapa en Azure-containergrupp. Den här mallen definierar en grupp som består av en enda containerinstans.

Fler Azure Container Instances mallexempel finns i [snabbstartsmallgalleriet](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

 1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett register och en replik på en annan plats.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Välj eller ange följande värden.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resursgrupp:** välj **Skapa ny,** ange ett unikt namn för resursgruppen och välj sedan **OK.**
    * **Plats**: Välj en plats för resursgruppen. Exempel: **USA, centrala.**
    * **Namn:** acceptera det genererade namnet för instansen eller ange ett namn.
    * **Bild:** acceptera standardavbildningens namn. Det här Linux-exempelavbildningen paketerar en liten webbapp som skrivits Node.js som fungerar som en statisk HTML-sida. 

    Acceptera standardvärden för de återstående egenskaperna.

    Granska villkoren. Om du godkänner väljer **du Jag godkänner de villkor som anges ovan.**

    ![Mallegenskaper](media/container-instances-quickstart-template/template-properties.png)

 3. När instansen har skapats får du ett meddelande:

    ![Portalmeddelande](media/container-instances-quickstart-template/deployment-notification.png)

 Azure-portalen används för att distribuera mallen. Förutom den här Azure Portal kan du använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-cli.md)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Använd Azure Portal eller ett verktyg som [Azure CLI](container-instances-quickstart.md) för att granska egenskaperna för containerinstansen.

1. I portalen söker du efter Container Instances och väljer den containerinstans som du skapade.

1. På sidan **Översikt** noterar du **Status för** instansen och dess **IP-adress**.

    ![Instansöversikt](media/container-instances-quickstart-template/aci-overview.png)

2. När statusen är *Körs* går du till IP-adressen i webbläsaren. 

    ![App som distribuerats via Azure Container Instances visas i webbläsare](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Visa containerloggar

Att visa loggar för en containerinstans är användbart när du felsöker problem med din container eller det program som den kör.

Om du vill visa containerns loggar går du till **Inställningar och** väljer   >  **Containerloggar.** Du bör se HTTP GET-begäran som genereras när du har granskat programmet i webbläsaren.

![Containerloggar i Azure-portalen](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med containern går du till **sidan Översikt för** containerinstansen och väljer Ta **bort**. Bekräfta borttagningen när du uppmanas att göra det.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Azure-containerinstans från en offentlig Microsoft-avbildning. Om du vill skapa en container på egen hand och distribuera den från ett privat Azure-containerregister går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Självstudie: Skapa en containeravbildning för distribution till Azure Container Instances](./container-instances-tutorial-prepare-app.md)

En stegvis självstudiekurs som vägleder dig genom processen med att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
