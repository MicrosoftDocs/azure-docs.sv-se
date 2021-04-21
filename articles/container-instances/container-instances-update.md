---
title: Uppdatera containergrupp
description: Lär dig hur du uppdaterar containrar som körs Azure Container Instances dina containergrupper.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cbb2e830490d2591645b8156ee830856da0f9049
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786971"
---
# <a name="update-containers-in-azure-container-instances"></a>Uppdatera containers i Azure Container Instances

Under normal drift av dina containerinstanser kan det vara nödvändigt att uppdatera de containrar som körs i en [containergrupp](./container-instances-container-groups.md). Du kanske till exempel vill uppdatera en egenskap, till exempel en avbildningsversion, ett DNS-namn eller en miljövariabel, eller uppdatera en egenskap i en container vars program har kraschat.

Uppdatera containrarna i en containergrupp som körs genom att omdistribuera en befintlig grupp med minst en ändrad egenskap. När du uppdaterar en containergrupp startas alla containrar som körs i gruppen om på plats, vanligtvis på samma underliggande containervärd.

> [!NOTE]
> Avslutade eller borttagna containergrupper kan inte uppdateras. När en containergrupp har avslutats (antingen har statusen Lyckades eller Misslyckades) eller har tagits bort, måste gruppen distribueras som ny. Se andra [begränsningar](#limitations).

## <a name="update-a-container-group"></a>Uppdatera en containergrupp

Så här uppdaterar du en befintlig containergrupp:

* Utfärda kommandot create (eller använd Azure Portal) och ange namnet på en befintlig grupp 
* Ändra eller lägg till minst en egenskap i gruppen som har stöd för uppdatering när du distribuerar om. Vissa egenskaper [stöder inte uppdateringar.](#properties-that-require-container-delete)
* Ange andra egenskaper med de värden som du angav tidigare. Om du inte anger ett värde för en egenskap återgår det till dess standardvärde.

> [!TIP]
> En [YAML-fil](./container-instances-container-groups.md#deployment) hjälper till att underhålla en containergrupps distributionskonfiguration och ger en startpunkt för att distribuera en uppdaterad grupp. Om du använde en annan metod för att skapa gruppen kan du exportera konfigurationen till YAML med hjälp av [az container export][az-container-export], 

### <a name="example"></a>Exempel

I följande Azure CLI-exempel uppdateras en containergrupp med en ny DNS-namnetikett. Eftersom egenskapen DNS-namnetikett för gruppen kan uppdateras omdistribueras containergruppen och dess containrar startas om.

Inledande distribution med DNS-namnetiketten *myapplication-staging:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Uppdatera containergruppen med en ny DNS-namnetikett, *myapplication,* och ange de återstående egenskaperna med de värden som användes tidigare:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Uppdatera förmåner

Den främsta fördelen med att uppdatera en befintlig containergrupp är snabbare distribution. När du distribuerar om en befintlig containergrupp hämtas dess containeravbildningslager från de som cachelagrades av den tidigare distributionen. I stället för att hämta alla avbildningslager från registret som det görs med nya distributioner hämtas endast ändrade lager (om det finns några).

Program som baseras på större containeravbildningar som Windows Server Core kan se betydande förbättringar i distributionshastighet när du uppdaterar i stället för att ta bort och distribuera nya.

## <a name="limitations"></a>Begränsningar

* Det är inte alla egenskaper för en containergrupp som stöder uppdateringar. Om du vill ändra vissa egenskaper för en containergrupp måste du först ta bort och sedan distribuera om gruppen. Se [Egenskaper som kräver att containern tas bort.](#properties-that-require-container-delete)
* Alla containrar i en containergrupp startas om när du uppdaterar containergruppen. Du kan inte utföra en uppdatering eller omstart på plats av en specifik container i en grupp med flera containrar.
* IP-adressen för en containergrupp bevaras vanligtvis mellan uppdateringarna, men är inte garanterad att vara densamma. Så länge containergruppen distribueras till samma underliggande värd behåller containergruppen sin IP-adress. Även om det är ovanligt finns det vissa Azure-interna händelser som kan orsaka omdistribution till en annan värd. För att åtgärda det här problemet rekommenderar vi att du använder en DNS-namnetikett för dina containerinstanser.
* Avslutade eller borttagna containergrupper kan inte uppdateras. När en containergrupp stoppas (är i *tillståndet Avslutad)* eller tas bort, distribueras gruppen som ny.

## <a name="properties-that-require-container-delete"></a>Egenskaper som kräver borttagning av container

Det går inte att uppdatera alla egenskaper för containergrupper. Om du till exempel vill ändra omstartsprincipen för en container måste du först ta bort containergruppen och sedan skapa den igen.

Ändringar av dessa egenskaper kräver borttagning av containergrupp före omdistribution:

* OS-typ
* PROCESSOR-, minnes- eller GPU-resurser
* Starta om princip
* Nätverksprofil

När du tar bort en containergrupp och återskapar den är den inte "omdistribuerad", utan skapas ny. Alla avbildningslager hämtas från registret, inte från de som cachelagrats av en tidigare distribution. IP-adressen för containern kan också ändras på grund av att den distribueras till en annan underliggande värd.

## <a name="next-steps"></a>Nästa steg

Containergruppen nämns flera gånger i den **här artikeln.** Varje container i Azure Container Instances distribueras i en containergrupp, och containergrupper kan innehålla fler än en container.

[Behållargrupper i Azure Container Instances](./container-instances-container-groups.md)

[Distribuera en grupp med flera containrar](container-instances-multi-container-group.md)

[Stoppa eller starta containrar manuellt i Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export
