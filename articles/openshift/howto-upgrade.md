---
title: Uppgradera ett Azure Red Hat OpenShift-kluster
description: Lär dig hur du uppgraderar ett Azure Red Hat OpenShift-kluster som kör OpenShift 4
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720893"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Uppgradera ett Azure Red Hat OpenShift-kluster (ARO)

En del av ARO-klustrets livs cykel omfattar att utföra periodiska uppgraderingar till den senaste OpenShift-versionen. Det är viktigt att du tillämpar de senaste säkerhets versionerna eller uppgraderar för att få de senaste funktionerna. Den här artikeln visar hur du uppgraderar alla komponenter i ett OpenShift-kluster med hjälp av webb konsolen OpenShift.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI-version 2.0.65 av senare. Kör `az --version` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli)

I den här artikeln förutsätter vi att du har till gång till ett befintligt kluster för att öppna i Azure Red Hat som en användare med `admin` behörighet.

## <a name="check-for-available-aro-cluster-upgrades"></a>Sök efter tillgängliga ARO-kluster uppgraderingar

I OpenShift-webbkonsolen väljer du inställningar för **administrations**  >  **kluster** och öppnar fliken **information** .

Om **uppdaterings statusen** för klustret visar **tillgängliga uppdateringar** kan du uppdatera klustret.

## <a name="upgrade-your-aro-cluster"></a>Uppgradera ditt ARO-kluster

Från webb konsolen i föregående steg anger du **kanalen** till rätt kanal för den version som du vill uppdatera till, till exempel `stable-4.5` .

Välj en version att uppdatera till och välj **Uppdatera**. Du ser uppdaterings status ändringen till: `Update to <product-version> in progress` . Du kan granska förloppet för kluster uppdateringen genom att titta på förlopps indikatorerna för operatorerna och noderna.

## <a name="next-steps"></a>Nästa steg
- [Lär dig att uppgradera ett ARO-kluster med hjälp av OC CLI](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Du hittar information om tillgängliga OpenShift container Platform-rådgivare och uppdateringar i [avsnittet errata](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) i kund portalen.
