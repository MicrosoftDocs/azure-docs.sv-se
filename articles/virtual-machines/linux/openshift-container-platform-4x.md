---
title: Distribuera OpenShift container Platform 4. x i Azure
description: Distribuera OpenShift container Platform 4. x i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: e8650802b4add9b33664205367bb3242b32b9754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670387"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Distribuera OpenShift container Platform 4. x i Azure

Distribution av OpenShift container Platform (OCP) 4,2 stöds nu i Azure via modellen Installer-Provisioned Infrastructure (IPI).  Landnings sidan för att testa OpenShift 4 är [try.OpenShift.com](https://try.openshift.com/). Om du vill installera OCP 4,2 i Azure går du till sidan [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Det krävs autentiseringsuppgifter för Red Hat för att få åtkomst till den här webbplatsen.


## <a name="notes"></a>Kommentarer 

 - Ett Azure Active Directory (AAD) tjänstens huvud namn (SP) krävs för att installera och köra OCP 4. x i Azure
     - SP måste beviljas API-behörighet för **Application. readwrite. OwnedBy** för Azure Active Directory Graph
     - En AAD-innehavaradministratör måste bevilja administrativt medgivande för att denna API-behörighet ska börja gälla
     - SP måste beviljas **deltagare** och **Administratörs roller för användar åtkomst** till prenumerationen
 - Installations modellen för OCP 4. x skiljer sig från 3. x och det finns inga Azure Resource Manager mallar som är tillgängliga för distribution av OCP 4. x i Azure
 - Om problem påträffas under installations processen, kontakta lämpligt företag (Microsoft eller Red Hat)

| Problembeskrivning | Kontakt punkt |
|-------------------|---------------|
| Specifika Azure-problem (AAD, SP, Azure-prenumeration osv.)                              | Microsoft |
| OpenShift-specifika problem (installations fel/fel, Red Hat-prenumeration osv.) |  Red Hat  |




## <a name="next-steps"></a>Nästa steg

- [Kom igång med OpenShift container Platform](https://docs.openshift.com)
