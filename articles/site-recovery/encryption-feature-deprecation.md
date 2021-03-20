---
title: Utfasning av Azure Site Recovery data krypterings funktion | Microsoft Docs
description: Information regarig Azure Site Recovery data krypterings funktion
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: sharrai
ms.openlocfilehash: 5860928d71c0e7431190908d5df5d7496e8ffb17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89426324"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Utfasning av Site Recovery data krypterings funktion

Det här dokumentet beskriver information om utfasning och den åtgärd som du måste vidta om du använder funktionen Site Recovery data kryptering när du konfigurerar haveri beredskap för virtuella Hyper-V-datorer till Azure. 

## <a name="deprecation-information"></a>Information om utfasning


Den Site Recovery data krypterings funktionen är tillgänglig för kunder som skyddar virtuella Hyper-V-datorer så att replikerade data skyddas mot säkerhetshot. funktionen kommer att föråldras den **30 April 2022**. Den ersätts av funktionen mer avancerad [kryptering i rest](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) , som använder [kryptering för lagringstjänst](../storage/common/storage-service-encryption.md) (SSE). Med SSE krypteras data innan de bevaras till lagring och dekrypteras vid hämtning, och vid redundansväxling till Azure kommer dina virtuella datorer att köras från de krypterade lagrings kontona, vilket ger ett förbättrat återställnings tids mål (RTO).

Observera att om du är en befintlig kund som använder den här funktionen, skulle du ha tagit emot kommunikation med information om utfasnings information och åtgärder. 


## <a name="what-are-the-implications"></a>Vilka är följderna?

Från och med den **30 April 2022** kommer alla virtuella datorer som fortfarande använder den inaktuella krypterings funktionen inte att kunna utföra redundans. 

## <a name="required-action"></a>Nödvändig åtgärd
Om du vill fortsätta med redundansväxlingen och replikeringar följer du stegen som beskrivs nedan:

Följ de här stegen för varje virtuell dator: 
1.  [Inaktivera replikering](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Skapa en ny replikeringsprincip](./hyper-v-azure-tutorial.md#set-up-a-replication-policy).
3.  [Aktivera replikering](./hyper-v-vmm-azure-tutorial.md#enable-replication) och välj ett lagrings konto med SSE aktiverat.

När du har slutfört den inledande replikeringen till lagrings konton med SSE aktiverat, kommer dina virtuella datorer att använda kryptering i vila med Azure Site Recovery.


## <a name="next-steps"></a>Nästa steg
Planera för att utföra reparations stegen och kör dem tidigast. Om du har frågor om den här utfasningen kan du kontakta Microsoft Support. Om du vill läsa mer om Hyper-V till Azure-scenariot kan du läsa [här](hyper-v-vmm-architecture.md).
