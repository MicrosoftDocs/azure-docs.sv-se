---
title: Distribuera mall – IntelliJ idé
description: Lär dig hur du skapar din första Azure Resource Manager mall (ARM-mall) med IntelliJ-idén och hur du distribuerar den.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: f0e007fe8930b30cee1c95159d7e964e3792449f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905969"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Skapa och distribuera ARM-mallar med hjälp av IntelliJ-idén

Lär dig hur du distribuerar en Azure Resource Manager mall (ARM-mall) till Azure med IntelliJ-idén och processen för att redigera och uppdatera mallen direkt från IDE. ARM-mallar är JSON-filer som definierar de resurser som du behöver distribuera för din lösning. Information om de begrepp som är kopplade till att distribuera och hantera dina Azure-lösningar finns i [Översikt över mall-distribution](overview.md).

![Portal diagram för Resource Manager-mall](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

När du har slutfört självstudien kan du distribuera ett Azure Storage-konto. Samma process kan användas till att distribuera andra Azure-resurser.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa stegen i den här artikeln behöver du:

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition eller Community Edition installerat
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) installerat, läs mer i [IntelliJ-guiden om hantering av insticksprogram](https://www.jetbrains.com/help/idea/managing-plugins.html)
* Vara [inloggad](/azure/developer/java/toolkit-for-intellij/sign-in-instructions) på Azure-kontot för Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Distribuera en snabb starts mall

I stället för att skapa en mall från början öppnar du en mall från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/). Azure snabb starts mallar är en lagrings plats för ARM-mallar. Mallen som används i den här artikeln kallas [skapa ett standard lagrings konto](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Den definierar en Azure Storage konto resurs.

1. Högerklicka och spara och på den [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) lokala datorn.

1. Om ditt Azure-verktyg är korrekt installerat och inloggat bör du se Azure Explorer i din IntelliJ-IDÉs marginal List. Högerklicka på **resurs hantering** och välj **skapa distribution**.

    ![Resource Manager-mall Högerklicka för att skapa distribution](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Konfigurera **distributions namn**, **prenumeration**, **resurs grupp** och **region**. Här distribuerar vi mallen till en ny resurs grupp `testRG` . Välj sedan sökväg för **resurs mal len** som `azuredeploy.json` och **resurs parametrar** när `azuredeploy.parameters.json` du laddade ned.

    ![Resource Manager-mall Välj filer för att skapa distribution](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. När du har klickat på OK startas distributionen. Tills distributionen är klar kan du se förloppet från IntelliJ-IDÉts **Statusfält** längst ned.

    ![Distributions status för Resource Manager-mall](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Bläddra i en befintlig distribution

1. När distributionen är färdig kan du se den nya resurs gruppen `testRG` och en ny distribution har skapats. Högerklicka på distributionen så kan du se en lista över möjliga åtgärder. Välj nu **Visa egenskaper**.

    ![Bläddra i Resource Manager-mall](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. En flik öppnas för att visa några användbara egenskaper, t. ex. distributions status och mallens struktur.

    ![Resource Manager-mall Visa distributions egenskaper](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Redigera och uppdatera en befintlig distribution

1. Välj **Redigera distribution** från snabb meny eller Visa egenskapsvyn innan. En annan flik öppnas som visar mall-och parametervärdena för distributionen på Azure. Om du vill spara filerna på en lokal plats kan du klicka på **Exportera mallfil**  eller **Exportera parameter** filen.

    ![Redigera distribution av Resource Manager-mall](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Du kan redigera de två filerna på den här sidan och distribuera ändringarna till Azure. Här ändrar vi värdet för **storageAccountType** i parameter-filer från `Standard_LRS` till `Standard_GRS` . Klicka sedan på **Uppdatera distribution** längst ned och bekräfta uppdateringen.

    ![Skärm bild som visar Resource Manager-mallen med frågan om uppdaterings distribution visas.](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. När uppdaterings distributionen har slutförts kan du kontrol lera på portalen att det skapade lagrings kontot har ändrats till `Standard_GRS` .

## <a name="clean-up-resources"></a>Rensa resurser

1. När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Du kan göra det från Azure Portal eller Azure CLI. I Azure Explorer från IntelliJ idé högerklickar du på din skapade **resurs grupp** och väljer Ta bort.

    ![Ta bort resurs grupp i Azure Explorer från IntelliJ idé](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Observera att om du tar bort en distribution tas inte resurser som skapats i distributionen bort. Ta bort motsvarande resurs grupp eller vissa resurser om du inte längre behöver dem.

## <a name="next-steps"></a>Nästa steg

Huvud fokus i den här artikeln är att använda IntelliJ idé för att distribuera en befintlig mall från Azure snabb starts mallar. Du har också lärt dig hur du visar och uppdaterar en befintlig distribution på Azure. Snabbstartsmallarna i Azure kanske inte innehåller exakt det du behöver. Mer information om hur du utvecklar mallar finns i vår nya nybörjar-serien:

> [!div class="nextstepaction"]
> [Självstudier för nybörjare](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Besök java på Azure dev Center](/azure/java)
