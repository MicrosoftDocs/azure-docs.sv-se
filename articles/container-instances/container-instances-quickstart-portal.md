---
title: Snabbstart – Distribuera Docker-container till containerinstans – portalen
description: I den här snabbstarten använder du Azure Portal för att snabbt distribuera en containerwebbapp som körs i en isolerad Azure-containerinstans
ms.date: 08/24/2020
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-js
- mode-portal
ms.openlocfilehash: c0189bbd04e454205b34a6415ab6109f95b6f51a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536227"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Snabbstart: Distribuera en containerinstans i Azure med hjälp av Azure Portal

Använd Azure Container Instances för att köra serverlösa Docker-containrar i Azure med enkelhet och hastighet. Distribuera ett program till en containerinstans på begäran när du inte behöver en fullständig plattform för containerorkestrering som Azure Kubernetes Service.

I den här snabbstarten använder du Azure Portal för att distribuera en isolerad Docker-container och göra programmet tillgängligt med ett fullständigt kvalificerat domännamn (FQDN). När du har konfigurerat några inställningar och distribuerat containern kan gå du till programmet som körs:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="App som distribuerats via Azure Container Instances visas i webbläsare":::

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto][azure-free-account] innan du börjar.

## <a name="create-a-container-instance"></a>Skapa en containerinstans

Välj skapa **en resurs**  >  **containrar**  >  **Container Instances**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Skapa en ny containerinstans i Azure Portal":::

På sidan **Grundläggande anger** du följande värden i textrutorna **Resursgrupp,** **Containernamn** och **Containeravbildning.** Lämna de övriga standardvärdena oförändrade och välj **OK**.

* Resursgrupp: **Skapa ny** > `myresourcegroup`
* Containernamn: `mycontainer`
* Bildkälla: **Snabbstartsbilder**
* Containeravbildning: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Konfigurera grundläggande inställningar för en ny containerinstans i Azure Portal":::

I den här snabbstarten använder du standardinställningarna för att distribuera den offentliga `aci-helloworld` Microsoft-avbildningen. Det här Linux-exempelavbildningen paketerar en liten webbapp som skrivits Node.js som fungerar som en statisk HTML-sida. Du kan också ta med dina egna containeravbildningar som lagras Azure Container Registry, Docker Hub eller andra register.

På sidan **Nätverk** anger du en **DNS-namnetikett** för containern. Namnet måste vara unikt i den Azure-region där du skapar containerinstansen. Din container kan nås offentligt på `<dns-name-label>.<region>.azurecontainer.io`. Om du får felmeddelandet ”DNS name label not available” (DNS-namnetikett inte tillgänglig) kan du prova en annan DNS-namnetikett.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Konfigurera nätverksinställningar för en ny containerinstans i Azure Portal":::

Lämna standardinställningarna för de andra inställningarna och välj sedan **Granska + skapa.**

När verifieringen är klar visas en sammanfattning av containerinställningarna. Välj **Skapa för** att skicka din containerdistributionsbegäran.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Sammanfattning av inställningar för en ny containerinstans i Azure Portal":::

När distributionen startar visas ett meddelande som anger att distributionen pågår. Ett nytt meddelande visas när containergruppen har distribuerats.

Öppna översikten för containergruppen genom att gå till **Resursgrupper**  >  **myresourcegroup**  >  **mycontainer**. Anteckna den **FQDN** (fullständigt kvalificerade domännamn) på containerinstansen samt dess **Status**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Översikt över gruppcontainer i Azure-portalen":::

När dess **Status***Körs*, navigera till containerns FQDN i webbläsaren.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="App som distribuerats via Azure Container Instances visas i webbläsare":::

Grattis! Du har distribuerat ett offentligt tillgängligt program i Azure Container Instances genom att konfigurera några inställningar.

## <a name="view-container-logs"></a>Visa containerloggar

Att visa loggar för en containerinstans är användbart när du felsöker problem med din container eller det program som den kör.

Om du vill visa containerns loggar går du **till Inställningar**, väljer **Containrar** och loggar **.** Du bör se HTTP GET-begäran som genereras när du har granskat programmet i webbläsaren.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Containerloggar i Azure-portalen":::


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren väljer du **Översikt** för behållarinstansen *mycontainer* och väljer sedan **Ta bort**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Ta bort containerinstansen i Azure Portal]":::

Välj **Ja** i bekräftelsedialogrutan när den visas.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Ta bort bekräftelse av en containerinstans i Azure Portal]":::

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Azure-containerinstans från en offentlig Microsoft-avbildning. Om du vill skapa en container på egen hand och distribuera den från ett privat Azure-containerregister går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Självstudie för Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/
