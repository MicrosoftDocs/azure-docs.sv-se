---
title: Konfigurera mål miljön för fysiska servrar i Azure Site Recovery
description: Den här artikeln beskriver hur du konfigurerar Azure-miljön för haveri beredskap för fysiska servrar med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953906"
---
# <a name="prepare-target-vmware-to-azure"></a>Förbered mål (VMware till Azure)

Den här artikeln beskriver hur du förbereder Azure-miljön för att börja replikera fysiska servrar (x64) som kör Windows eller Linux till Azure.

## <a name="prerequisites"></a>Förutsättningar

Artikeln förutsätter:
- Du har skapat ett Recovery Services valv för att skydda dina fysiska servrar. Du kan skapa ett Recovery Services valv från [Azure Portal](https://portal.azure.com "Azure Portal").
- Du har [konfigurerat din lokala miljö](physical-azure-disaster-recovery.md) för att replikera fysiska servrar till Azure.

## <a name="prepare-target"></a>Förbered målet

När du har slutfört **steg 1: Välj skydds mål** och **steg 2: förbereda källan** går du till **steg 3: mål**

![Förbered målet](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Prenumeration:** På den nedrullningsbara menyn väljer du den prenumeration som du vill replikera dina fysiska servrar till.
2. **Distributions modell:** Välj distributions modell (klassisk eller Resource Manager)

Baserat på den valda distributions modellen körs en verifiering för att se till att du har minst ett kompatibelt lagrings konto och virtuellt nätverk i mål prenumerationen för att replikera och redundansväxla dina fysiska servrar till.

När verifieringen har slutförts klickar du på OK för att gå till nästa steg.

Om du inte har ett kompatibelt Resource Manager-lagrings konto eller virtuellt nätverk kan du skapa ett genom att klicka på knapparna **+ lagrings konto** eller **+ nätverks** överst på sidan.

## <a name="next-steps"></a>Nästa steg
[Konfigurera replikeringsinställningar](vmware-azure-set-up-replication.md).
