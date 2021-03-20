---
title: Översikt över virtuella datorer
titleSuffix: Azure VMware Solution by CloudSimple
description: Lär dig mer om CloudSimple Virtual Machines och deras fördelar. Du kan hantera virtuella VMware-datorer från Azure Portal.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88141984"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Översikt över virtuella CloudSimple-datorer

Med CloudSimple kan du hantera virtuella VMware-datorer (VM) från Azure Portal.  Ett kluster eller en resurspool från ditt vSphere-kluster hanteras via Azure genom att mappas till din prenumeration.

För att skapa en virtuell CloudSimple-dator från Azure måste det finnas en VM-mall i ditt privata moln vCenter.  Mallen används för att anpassa operativ system och program.  Mallen VM kan vara skärpd för att uppfylla företagets säkerhets principer.  Du kan använda mallen för att skapa virtuella datorer och sedan använda dem från Azure Portal med en självbetjänings modell.

## <a name="benefits"></a>Fördelar

CloudSimple virtuella datorer från Azure Portal ger en självbetjänings funktion som användare kan använda för att skapa och hantera virtuella VMware-datorer.

* Skapa en virtuell CloudSimple-dator i ditt privata moln vCenter
* Hantera VM-egenskaper
  * Lägg till/ta bort diskar
  * Lägg till/ta bort nätverkskort
* Energi åtgärder för din virtuella CloudSimple-dator
  * Ström och avstängning
  * Återställ virtuell dator
* Ta bort virtuell dator

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [använder virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Lär dig hur du [mappar din Azure-prenumeration](azure-subscription-mapping.md)
