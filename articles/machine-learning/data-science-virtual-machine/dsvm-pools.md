---
title: Delade pooler
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du skapar & distribuerar en delad pool av data vetenskap Virtual Machines (Dsvm) som en delad resurs för ett team.
keywords: djup inlärning, AI, data vetenskaps verktyg, virtuell data vetenskaps dator, Geospatial analys, team data vetenskaps process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: eb1242189f4c5a38421a7f44e8f5e738c44970b6
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705531"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Skapa en delad pool av data vetenskaps Virtual Machines

I den här artikeln får du lära dig hur du skapar en delad pool med data vetenskaps Virtual Machines (Dsvm) för ett team. Fördelarna med att använda en delad pool är bättre resursutnyttjande, enklare delning och samarbete och effektivare hantering av DSVM-resurser.

Du kan använda många metoder och tekniker för att skapa en pool med Dsvm. Den här artikeln fokuserar på pooler för interaktiva virtuella datorer (VM). En alternativ hanterad beräknings infrastruktur är Azure Machine Learning beräkning. Mer information finns i [skapa beräknings kluster](../how-to-create-attach-compute-cluster.md).

## <a name="interactive-vm-pool"></a>Interaktiv VM-pool

En pool med interaktiva virtuella datorer som delas av hela AI/data vetenskaps gruppen gör att användarna kan logga in på en tillgänglig instans av DSVM i stället för att ha en dedikerad instans för varje uppsättning användare. Den här installationen ger bättre tillgänglighet och effektivare användning av resurser.

Du använder teknik för [skalnings uppsättningar för virtuella Azure-datorer](../../virtual-machine-scale-sets/index.yml) för att skapa en interaktiv VM-pool. Du kan använda skalnings uppsättningar för att skapa och hantera en grupp med identiska, belastningsutjämnade och automatisk skalning av virtuella datorer.

Användaren loggar in på huvud-poolens IP-eller DNS-adress. Skalnings uppsättningen dirigerar automatiskt sessionen till en tillgänglig DSVM i skalnings uppsättningen. Eftersom användarna vill ha en konsekvent och välbekant miljö oavsett vilken virtuell dator de loggar in på, kan alla instanser av den virtuella datorn i skalnings uppsättningen montera en delad nätverks enhet, till exempel en Azure Files resurs eller en NFS-resurs (Network File System). Användarens delade arbets yta behålls vanligt vis på det delade fil lager som är monterat på var och en av instanserna.

Du kan hitta ett exempel på en Azure Resource Manager mall som skapar en skalnings uppsättning med Ubuntu DSVM-instanser på [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Du hittar ett exempel på [parameter filen](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) för Azure Resource Manager-mallen på samma plats.

Du kan skapa skalnings uppsättningen från Azure Resource Manager-mallen genom att ange värden för parameter filen i Azure CLI:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az deployment group create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Föregående kommandon förutsätter att du har:

* En kopia av parameter filen med värdena som anges för din instans av skalnings uppsättningen.
* Antalet virtuella dator instanser.
* Pekare till Azure Files resursen.
* Autentiseringsuppgifter för det lagrings konto som ska monteras på varje virtuell dator.

Parameter filen refereras lokalt i kommandona. Du kan också skicka parametrar infogade eller uppmanas att ange dem i skriptet.  

Med föregående mall kan SSH-och JupyterHub-porten från front-end-Ubuntu ställas in på backend-poolen för Dsvm. Som användare loggar du in på den virtuella datorn på ett säkert gränssnitt (SSH) eller på JupyterHub på vanligt sätt. Eftersom de virtuella dator instanserna kan skalas upp eller ned dynamiskt, måste alla tillstånd sparas i den monterade Azure Files resursen. Du kan använda samma metod för att skapa en pool med Windows-Dsvm.

[Skriptet som monterar Azure Files resursen](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) är också tillgängligt i Azure DataScienceVM-lagringsplatsen i GitHub. Skriptet monterar Azure Files resursen på den angivna monterings punkten i parameter filen. Skriptet skapar också mjuka länkar till den monterade enheten i den första användarens Hem Katalog. En användarspecifik Notebook-katalog i Azure Files resursen är mjuk länkad till `$HOME/notebooks/remote` katalogen så att användarna kan komma åt, köra och spara sina Jupyter-anteckningsböcker. Du kan använda samma konvention när du skapar ytterligare användare på den virtuella datorn för att peka varje användares Jupyter-arbetsyta till Azure Files resursen.

Skalnings uppsättningar för virtuella datorer stöder autoskalning. Du kan ange regler för när du vill skapa ytterligare instanser och när du ska skala ned instanser. Du kan till exempel skala ned till noll instanser för att spara kostnader för maskin varu användning i molnet när de virtuella datorerna inte används alls. Dokumentations sidorna för skalnings uppsättningar för virtuella datorer ger detaljerade anvisningar för automatisk [skalning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en gemensam identitet](dsvm-common-identity.md)
* [Lagra autentiseringsuppgifter på ett säkert sätt för att få åtkomst till moln resurser](dsvm-secure-access-keys.md)