---
title: Förbered målet för VMware VM-replikering i Azure Site Recovery
description: Den här artikeln beskriver hur du förbereder din Azure-miljö för VMware VM-replikering till Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "73693161"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Förbered mål miljön för haveri beredskap för virtuella VMware-datorer eller fysiska servrar till Azure

Den här artikeln beskriver hur du förbereder din Azure-miljö så att du kan börja replikera virtuella VMware-datorer eller fysiska servrar till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter:
- Du har skapat ett Recovery Services-valv på [Azure Portal](https://portal.azure.com "Azure Portal") för att skydda dina käll datorer
- Du har konfigurerat din lokala miljö för att replikera [virtuella VMware-datorer](vmware-azure-set-up-source.md) eller [fysiska servrar](physical-azure-set-up-source.md) till Azure.

## <a name="prepare-target"></a>Förbered målet

När du har slutfört **steg 1: Välj skydds mål** och **steg 2: förbereda källan** går du till **steg 3: mål**

![Förbered målet](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Prenumeration:** På den nedrullningsbara menyn väljer du den prenumeration som du vill replikera dina virtuella datorer eller fysiska servrar till.
2. **Distributions modell:** Välj distributions modell (klassisk eller Resource Manager)

Baserat på den valda distributions modellen körs en verifiering för att se till att du har minst ett virtuellt nätverk i mål prenumerationen att replikera och Redundansväxla din virtuella dator eller fysiska server till.

När verifieringen har slutförts klickar du på OK för att gå till nästa steg.

Om du inte har ett virtuellt nätverk kan du skapa ett genom att klicka på knappen **+ nätverk** överst på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
