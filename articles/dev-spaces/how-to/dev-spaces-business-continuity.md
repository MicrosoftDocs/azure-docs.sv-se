---
title: Affärskontinuitet och haveriberedskap
services: azure-dev-spaces
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 01/28/2019
ms.topic: conceptual
description: Lär dig att använda Azure dev Spaces och Azure Kubernetes Services för att ge affärs kontinuitet och förbereda för haveri beredskap
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
manager: gwallace
ms.openlocfilehash: 77425d294dcea0546259b152b793cec291940c53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929607"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Verksamhets kontinuitet och haveri beredskap i Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Granska katastrof återställnings vägledning för Azure Kubernetes service (AKS)

Azure dev Spaces är en funktion i Azure Kubernetes service (AKS). Du bör vara medveten om rikt linjerna för haveri beredskap i AKS och fundera över om de gäller för de AKS-kluster som du använder för dev Spaces. Mer information finns [i metod tips för verksamhets kontinuitet och haveri beredskap i Azure Kubernetes service (AKS)](../../aks/operator-best-practices-multi-region.md)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Aktivera dev Spaces på AKS-kluster i olika regioner

Genom att aktivera dev Spaces på AKS-kluster i olika regioner kan du återuppta användningen av dev-utrymmen direkt efter ett Azure-regions fel.

Allmän information om distributioner av flera regioner i AKS finns i [Planera för distribution i flera regioner](../../aks/operator-best-practices-multi-region.md#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Aktivera dev Spaces via Azure CLI

Du kan också aktivera dev Spaces på kommando raden:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Distribuera teamets bas linje till varje kluster

När du arbetar med dev-utrymmen distribuerar du normalt hela programmet till ett överordnat dev-utrymme på ditt Kubernetes-kluster. Som standard `default` används utrymmet. Den första distributionen omfattar alla tjänster och de externa resurser som tjänsterna är beroende av, till exempel databaser eller köer. Detta kallas för *bas linjen*. När du har skapat en bas linje i det överordnade dev-utrymmet itererar du och felsöker enskilda tjänster inom underordnade dev-utrymmen.

Du bör distribuera de senaste versionerna av din bas linje uppsättning av tjänster till kluster i flera regioner. Genom att uppdatera dina bas linje tjänster på det här sättet ser du till att du kan fortsätta att använda dev-utrymmen om det uppstår ett Azure-regions haveri. Om du till exempel distribuerar din bas linje via en CI/CD-pipeline ändrar du pipelinen så att den distribueras till flera kluster i olika regioner.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Välj rätt AKS-kluster som ska användas för dev Spaces

När du har konfigurerat ett säkerhets kopierings kluster som kör din grupps bas linje kan du snabbt växla över till säkerhets kopierings klustret när som helst. Sedan kan du köra om de enskilda tjänster som du arbetar med i underordnade dev-utrymmen.

Välj ett annat kluster med följande CLI-kommando:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Du kan visa en lista över tillgängliga dev Spaces på det nya klustret med följande kommando:

```cmd
azds space list
```

Du kan skapa ett nytt dev-utrymme för att arbeta i eller välja ett befintligt dev-utrymme med följande kommando:

```cmd
azds space select -n <space name>
```

När du har kört de här kommandona används det valda klustret och dev-utrymmet för efterföljande CLI-åtgärder och för fel sökning av projekt med Visual Studio Code-tillägget för Azure dev Spaces.

Om du använder Visual Studio kan du växla klustret som används av ett befintligt projekt genom följande steg:

1. Öppna ditt projekt i Visual Studio.
1. Högerklicka på projekt namnet i Solution Explorer och klicka på **Egenskaper**
1. I den vänstra rutan klickar du på **Felsök**
1. På sidan fel söknings egenskaper klickar du på list rutan **profil** och väljer **Azure dev Spaces**.
1. Klicka på knappen **ändra** .
1. I dialog rutan som visas väljer du det AKS-kluster som du vill använda. Om du vill kan du välja ett annat dev Space för att arbeta i eller skapa ett nytt dev-utrymme genom att välja lämpligt alternativ i list rutan **utrymme** .

När du har valt rätt kluster och utrymme kan du köra tjänsten i dev Spaces genom att trycka på F5.

Upprepa de här stegen för alla andra projekt som har kon figurer ATS för att använda det ursprungliga klustret.

## <a name="access-a-service-on-a-backup-cluster"></a>Få åtkomst till en tjänst i ett säkerhets kopierings kluster

Om du har konfigurerat tjänsten att använda ett offentligt DNS-namn, kommer tjänsten att ha en annan URL om du kör den på ett säkerhets kopierings kluster. Offentliga DNS-namn är alltid i formatet `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` . Om du växlar till ett annat kluster ändras kluster-GUID och eventuellt regionen.

Dev Spaces visar alltid rätt URL för tjänsten när den körs `azds up` , eller i fönstret utdata i Visual Studio under **Azure dev Spaces**.

Du kan också hitta URL: en genom att köra `azds list-uris` kommandot:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Använd den här URL: en vid åtkomst till tjänsten.
