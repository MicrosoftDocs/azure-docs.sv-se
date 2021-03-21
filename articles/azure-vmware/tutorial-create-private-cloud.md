---
title: Självstudie – distribuera ett privat moln i Azure VMware-lösningen
description: Lär dig hur du skapar och distribuerar ett privat moln i Azure VMware-lösningen
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: ed916305cd1a67162f07c24e3bf97766e5389b74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462175"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Självstudie: Distribuera ett privat moln i Azure VMware-lösningen

Med Azure VMware-lösningen kan du distribuera ett vSphere-kluster i Azure. Den minsta första distributionen är tre värdar. Ytterligare värdar kan läggas till en i taget, upp till högst 16 värdar per kluster.

Eftersom Azure VMware-lösningen inte tillåter att du hanterar ditt privata moln med din lokala vCenter vid lanseringen krävs ytterligare konfiguration. Dessa procedurer och relaterade krav beskrivs i den här självstudien.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa ett privat moln för Azure VMware-lösningen
> * Verifiera att det privata molnet har distribuerats

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Lämpliga administrativa rättigheter och behörigheter för att skapa ett privat moln. Du måste vara minst deltagar nivå i prenumerationen.
- Följ den information som du samlade in i [planerings](production-ready-deployment-steps.md) artikeln för att distribuera Azure VMware-lösningen.
- Se till att du har rätt nätverks konfiguration enligt beskrivningen i [Självstudier: check lista för nätverk](tutorial-network-checklist.md).
- Värdarna har etablerats och Microsoft. AVS-resurshanteraren har registrerats enligt beskrivningen i [begär ande värdar och aktiverar Microsoft. AVS-Resurshanterarens resurs leverantör](enable-azure-vmware-solution.md).

## <a name="create-a-private-cloud"></a>Skapa ett privat moln

Du kan skapa ett privat moln i Azure VMware-lösningen med hjälp av [Azure Portal](#azure-portal) eller med hjälp av [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

I stället för Azure Portal skapa ett privat moln i Azure VMware-lösningen kan du använda Azure CLI med hjälp av Azure Cloud Shell.  En lista över kommandon som du kan använda med Azure VMware-lösningen finns i [Azure VMware-kommandon](/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Välj **testa det** övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och tryck på **RETUR** för att köra den.

#### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs grupp med kommandot ["AZ Group Create"](/cli/azure/group) . En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Skapa ett privat moln

Ange ett namn för resurs gruppen och det privata molnet, en plats och klustrets storlek.

| Egenskap  | Beskrivning  |
| --------- | ------------ |
| **-g** (resurs gruppens namn)     | Namnet på resurs gruppen för dina privata moln resurser.        |
| **-n** (namn på privat moln)     | Namnet på ditt privata moln i Azure VMware-lösningen.        |
| **--plats**     | Den plats som används för ditt privata moln.         |
| **--kluster storlek**     | Klustrets storlek. Minimivärdet är 3.         |
| **--Network-block**     | CIDR IP-adressens nätverks block som ska användas för ditt privata moln. Adress blocket får inte överlappa adress block som används i andra virtuella nätverk som finns i din prenumeration och lokala nätverk.        |
| **--SKU** | SKU-värdet: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Azure VMware-kommandon

En lista över kommandon som du kan använda med Azure VMware-lösningen finns i [Azure VMware-kommandon](/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du lärt dig att:

> [!div class="checklist"]
> * Skapa ett privat moln för Azure VMware-lösningen
> * Verifiera att det privata molnet har distribuerats
> * Ta bort ett privat moln i Azure VMware-lösningen

Fortsätt till nästa självstudie och lär dig hur du skapar en hopp ruta. Du använder rutan hopp för att ansluta till din miljö så att du kan hantera ditt privata moln lokalt.


> [!div class="nextstepaction"]
> [Få åtkomst till ett privat moln i Azure VMware-lösningen](tutorial-access-private-cloud.md)