---
title: Uppdatera behållar grupp
description: Lär dig hur du uppdaterar pågående behållare i Azure Container Instances behållar grupper.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: fb31eeda83532c408a303e879439006bcd7d4e45
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200656"
---
# <a name="update-containers-in-azure-container-instances"></a>Uppdatera containers i Azure Container Instances

Under normal drift av dina behållar instanser kan du se till att du behöver uppdatera de behållare som körs i en [behållar grupp](./container-instances-container-groups.md). Du kanske vill uppdatera en egenskap, till exempel en avbildnings version, ett DNS-namn eller en miljö variabel eller uppdatera en egenskap i en behållare vars program har kraschat.

Uppdatera behållarna i en behållare grupp som körs genom att distribuera om en befintlig grupp med minst en ändrad egenskap. När du uppdaterar en behållar grupp startas alla behållare som körs i gruppen om på plats, vanligt vis på samma underliggande behållar värd.

> [!NOTE]
> Avslutade eller borttagna behållar grupper kan inte uppdateras. När en behållar grupp har avslut ATS (har statusen lyckad eller misslyckad) eller har tagits bort måste gruppen distribueras som ny. Se andra [begränsningar](#limitations).

## <a name="update-a-container-group"></a>Uppdatera en behållar grupp

Så här uppdaterar du en befintlig behållar grupp:

* Utfärda kommandot CREATE (eller Använd Azure Portal) och ange namnet på en befintlig grupp 
* Ändra eller Lägg till minst en egenskap för gruppen som har stöd för uppdatering när du distribuerar om. Vissa egenskaper [stöder inte uppdateringar](#properties-that-require-container-delete).
* Ange andra egenskaper med de värden du angav tidigare. Om du inte anger ett värde för en egenskap återställs värdet till standardvärdet.

> [!TIP]
> En [yaml-fil](./container-instances-container-groups.md#deployment) hjälper till att underhålla en behållar grupps distributions konfiguration och ger en start punkt för att distribuera en uppdaterad grupp. Om du använde en annan metod för att skapa gruppen kan du exportera konfigurationen till YAML med hjälp av [AZ container export][az-container-export], 

### <a name="example"></a>Exempel

Följande Azure CLI-exempel uppdaterar en behållar grupp med en ny DNS-benämning. Eftersom egenskapen DNS-namn på gruppen är en som kan uppdateras, omdistribueras behållar gruppen och dess behållare startas om.

Första distribution med DNS-namn etikett för *program-mellanlagring*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Uppdatera behållar gruppen med en ny DNS-benämning, mina *program* och ange de återstående egenskaperna med de värden som användes tidigare:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Uppdatera förmåner

Den främsta fördelen med att uppdatera en befintlig behållar grupp är snabbare distribution. När du distribuerar om en befintlig behållar grupp, hämtas dess behållar avbildnings lager från de som cachelagrats av den tidigare distributionen. I stället för att hämta alla bild lager från registret när det är gjort med nya distributioner, hämtas endast ändrade lager.

Program som baseras på större behållar avbildningar som Windows Server Core kan se avsevärd förbättring av distributions hastigheten när du uppdaterar i stället för att ta bort och distribuera nya.

## <a name="limitations"></a>Begränsningar

* Alla egenskaper för en behållar grupp har inte stöd för uppdateringar. Om du vill ändra vissa egenskaper för en behållar grupp måste du först ta bort och sedan distribuera om gruppen. Se [egenskaper som kräver borttagning av behållare](#properties-that-require-container-delete).
* Alla behållare i en behållar grupp startas om när du uppdaterar behållar gruppen. Det går inte att utföra en uppdatering eller omstart på plats av en speciell behållare i en grupp med flera behållare.
* IP-adressen för en behållar grupp behålls vanligt vis mellan uppdateringar, men är inte garanterat oförändrad. Så länge behållar gruppen distribueras till samma underliggande värd, behåller behållar gruppen sin IP-adress. Även om det är sällsynt finns det vissa Azure-interna händelser som kan leda till omdistribution till en annan värd. För att undvika det här problemet rekommenderar vi att du använder en DNS-benämning för behållar instanserna.
* Avslutade eller borttagna behållar grupper kan inte uppdateras. När en behållar grupp har stoppats (är i *avslutat* tillstånd) eller tas bort distribueras gruppen som ny.

## <a name="properties-that-require-container-delete"></a>Egenskaper som kräver borttagning av behållare

Det går inte att uppdatera alla egenskaper för container gruppen. Om du till exempel vill ändra en behållares omstarts princip måste du först ta bort behållar gruppen och sedan skapa den igen.

Ändringar av dessa egenskaper kräver att behållar gruppen tas bort innan omdistributionen:

* OS-typ
* PROCESSOR-, minnes-eller GPU-resurser
* Starta om princip
* Nätverksprofil

När du tar bort en behållar grupp och återskapar den, är den inte "omdistribuerad", men skapade ny. Alla bild lager hämtas från registret, inte från de som cachelagrats av en tidigare distribution. IP-adressen för behållaren kan också ändras på grund av att den distribueras till en annan underliggande värd.

## <a name="next-steps"></a>Nästa steg

Som nämns flera gånger i den här artikeln är **behållar gruppen**. Varje behållare i Azure Container Instances distribueras i en behållar grupp och behållar grupper kan innehålla fler än en behållare.

[Behållargrupper i Azure Container Instances](./container-instances-container-groups.md)

[Distribuera en grupp med flera containrar](container-instances-multi-container-group.md)

[Stoppa eller starta containrar manuellt i Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
