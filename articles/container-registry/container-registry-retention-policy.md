---
title: Princip för att behålla otaggade manifest
description: Lär dig hur du aktiverar en kvarhållningsprincip i Ditt Azure-containerregister för automatisk borttagning av otaggade manifest efter en definierad period.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 81ce92a2533cc8a688be43da9406cd5b0e726509
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781351"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Ange en kvarhållningsprincip för otaggade manifest

Azure Container Registry ger dig möjlighet att  ange en bevarandeprincip för lagrade bildmanifest som inte har några associerade taggar *(otaggade manifest).* När en bevarandeprincip har aktiverats tas otaggade manifest i registret bort automatiskt efter ett antal dagar som du anger. Den här funktionen förhindrar att registret fylls med artefakter som inte behövs och hjälper dig att spara på lagringskostnaderna. Om attributet för ett otaggat manifest är inställt på kan manifestet inte tas bort `delete-enabled` `false` och kvarhållningsprincipen gäller inte.

Du kan använda Azure Cloud Shell lokal installation av Azure CLI för att köra kommandoexempel i den här artikeln. Om du vill använda den lokalt krävs version 2.0.74 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

En bevarandeprincip är  en funktion i Premium-containerregister. Information om registertjänstnivåer finns i [Azure Container Registry tjänstnivåer.](container-registry-skus.md)

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

> [!WARNING]
> Det går inte att återställa en bevarandeprincip med borttagna bilddata. Om du har system som hämtar avbildningar efter manifestavbildningar (till skillnad från avbildningens namn) bör du inte ange en bevarandeprincip för otaggade manifest. Om du tar bort otaggade avbildningar förhindras dessa system från att hämta avbildningarna från registret. I stället för att hämta efter manifest bör du överväga *att införa ett unikt taggningsschema,* vilket är en [rekommenderad metod.](container-registry-image-tag-version.md)

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Du kan bara ange en kvarhållningsprincip för otaggade manifest.
* Bevarandeprincipen gäller för närvarande endast för manifest som inte har taggats *efter* att principen har aktiverats. Befintliga otaggade manifest i registret omfattas inte av principen. Om du vill ta bort befintliga otaggade manifest kan du se exempel i [Ta bort containeravbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Om bevarandeprincipen

Azure Container Registry refererar till inventering för manifest i registret. När ett manifest inte har taggats kontrolleras bevarandeprincipen. Om en kvarhållningsprincip är aktiverad köas en manifeståtgärd för borttagning med ett visst datum, enligt det antal dagar som angetts i principen.

Ett separat köhanteringsjobb bearbetar kontinuerligt meddelanden och skalar efter behov. Anta till exempel att du har otaggat två manifest med en timmes mellanrum i ett register med en kvarhållningsprincip på 30 dagar. Två meddelanden skulle köas. Sedan, 30 dagar senare, ungefär 1 timme från varandra, hämtas meddelandena från kön och bearbetas, förutsatt att principen fortfarande gäller.

## <a name="set-a-retention-policy---cli"></a>Ange en bevarandeprincip – CLI

I följande exempel visas hur du använder Azure CLI för att ange en kvarhållningsprincip för otaggade manifest i ett register.

### <a name="enable-a-retention-policy"></a>Aktivera en bevarandeprincip

Som standard anges ingen bevarandeprincip i ett containerregister. Om du vill ange eller uppdatera en kvarhållningsprincip kör du kommandot [az acr config retention update][az-acr-config-retention-update] i Azure CLI. Du kan ange ett antal dagar mellan 0 och 365 för att behålla de otaggade manifesten. Om du inte anger ett antal dagar anger kommandot standardvärdet 7 dagar. Efter kvarhållningsperioden tas alla otaggade manifest i registret bort automatiskt.

I följande exempel anges en bevarandeprincip på 30 dagar för otaggade manifest i registret *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

I följande exempel anges en princip för att ta bort alla manifest i registret så fort de inte har taggats. Skapa den här principen genom att ange en kvarhållningsperiod på 0 dagar. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Verifiera en kvarhållningsprincip

Om du aktiverar föregående princip med en kvarhållningsperiod på 0 dagar kan du snabbt kontrollera att otaggade manifest tas bort:

1. Push-skicka en `hello-world:latest` testavbildning till registret eller ersätt en annan testavbildning som du väljer.
1. Ta bort `hello-world:latest` avbildningen, till exempel med hjälp av [kommandot az acr repository untag.][az-acr-repository-untag] Det otaggade manifestet finns kvar i registret.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Inom några sekunder tas det otaggade manifestet bort. Du kan verifiera borttagningen genom att visa manifest i databasen, till exempel med kommandot [az acr repository show-manifests.][az-acr-repository-show-manifests] Om testbilden var den enda på lagringsplatsen tas själva lagringsplatsen bort.

### <a name="disable-a-retention-policy"></a>Inaktivera en kvarhållningsprincip

Om du vill se kvarhållningsprincipen som angetts i ett register kör du kommandot [az acr config retention show:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Om du vill inaktivera en bevarandeprincip i ett register kör du [kommandot az acr config retention update][az-acr-config-retention-update] och anger `--status disabled` :

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Ange en bevarandeprincip – portalen

Du kan också ange kvarhållningsprincipen för ett register i [Azure Portal](https://portal.azure.com). I följande exempel visas hur du använder portalen för att ange en bevarandeprincip för otaggade manifest i ett register.

### <a name="enable-a-retention-policy"></a>Aktivera en kvarhållningsprincip

1. Gå till ditt Azure-containerregister. Under **Principer** väljer du **Kvarhållning** (förhandsversion).
1. I **Status** väljer du **Aktiverad.**
1. Välj ett antal dagar mellan 0 och 365 för att behålla de otaggade manifesten. Välj **Spara**.

![Aktivera en bevarandeprincip i Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Inaktivera en bevarandeprincip

1. Gå till ditt Azure-containerregister. Under **Principer** väljer du **Kvarhållning** (förhandsversion).
1. I **Status** väljer du **Inaktiverad.** Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

* Läs mer om alternativ för [att ta bort avbildningar och lagringsplatsen](container-registry-delete.md) i Azure Container Registry

* Lär dig hur [du automatiskt rensar](container-registry-auto-purge.md) valda avbildningar och manifest från ett register

* Läs mer om alternativ för [att låsa avbildningar och manifest](container-registry-image-lock.md) i ett register

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az_acr_config_retention_update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az_acr_config_retention_show
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
