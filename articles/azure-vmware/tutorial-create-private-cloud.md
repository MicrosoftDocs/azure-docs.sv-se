---
title: Självstudie – Distribuera ett Azure VMware Solution privat moln
description: Lär dig hur du skapar och distribuerar Azure VMware Solution privata molnet
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: 89a44ce7e5910609068f72c321971ced2e3646b4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374853"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Självstudie: Distribuera ett Azure VMware Solution privat moln

Azure VMware Solution ger dig möjlighet att distribuera ett vSphere-kluster i Azure. Den minsta inledande distributionen är tre värdar. Ytterligare värdar kan läggas till en i taget, upp till högst 16 värdar per kluster.

Eftersom Azure VMware Solution inte tillåter att du hanterar ditt privata moln med ditt lokala vCenter vid start krävs ytterligare konfiguration. Dessa procedurer och relaterade förutsättningar tas upp i den här självstudien.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure VMware Solution privat moln
> * Verifiera det privata molnet som distribuerats

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Lämpliga administrativa rättigheter och behörighet för att skapa ett privat moln. Du måste vara på lägsta deltagarnivå i prenumerationen.
- Följ informationen som du samlade in i [planeringsartikeln](production-ready-deployment-steps.md) för att distribuera Azure VMware Solution.
- Se till att du har rätt nätverk konfigurerat enligt beskrivningen i [Självstudie: Checklista för nätverk.](tutorial-network-checklist.md)
- Värdar har etablerats och Resursprovidern Microsoft.AVS har registrerats enligt beskrivningen i Begär värdar och aktiverar [resursprovidern Microsoft.AVS.](enable-azure-vmware-solution.md)

## <a name="create-a-private-cloud"></a>Skapa ett privat moln

Du kan skapa ett Azure VMware Solution privat moln med hjälp av [Azure Portal](#azure-portal) eller med hjälp av [Azure CLI.](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

I stället för Azure Portal skapa ett Azure VMware Solution privat moln kan du använda Azure CLI med hjälp av Azure Cloud Shell.  En lista över kommandon som du kan använda med Azure VMware Solution finns i [Azure VMware-kommandon.](/cli/azure/ext/vmware/vmware)

#### <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Välj **Prova från** det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kodblocken, klistra in det i Cloud Shell och tryck på **Retur för** att köra den.

#### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [kommandot "az group create".](/cli/azure/group) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Skapa ett privat moln

Ange ett namn för resursgruppen och det privata molnet, en plats och storleken på klustret.

| Egenskap  | Beskrivning  |
| --------- | ------------ |
| **-g** (resursgruppsnamn)     | Namnet på resursgruppen för dina privata molnresurser.        |
| **-n** (namn på privat moln)     | Namnet på ditt Azure VMware Solution privat moln.        |
| **--location**     | Den plats som används för ditt privata moln.         |
| **--cluster-size**     | Klustrets storlek. Det minsta värdet är 3.         |
| **--network-block**     | CIDR-IP-adressnätverksblocket som ska användas för ditt privata moln. Adressblocket får inte överlappa adressblock som används i andra virtuella nätverk som finns i din prenumeration och lokala nätverk.        |
| **--sku** | SKU-värdet: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Azure VMware-kommandon

En lista över kommandon som du kan använda med Azure VMware Solution finns i [Azure VMware-kommandon.](/cli/azure/ext/vmware/vmware)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure VMware Solution privat moln
> * Verifiera det privata molnet som distribuerats
> * Ta bort ett Azure VMware Solution privat moln

Fortsätt till nästa självstudie för att lära dig hur du skapar en hoppbox. Du använder jumpboxen för att ansluta till din miljö så att du kan hantera ditt privata moln lokalt.


> [!div class="nextstepaction"]
> [Åtkomst till ett Azure VMware Solution privat moln](tutorial-access-private-cloud.md)
