---
title: ta med fil
description: ta med fil
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521549"
---
1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och välj sedan **Anslut**. Välj **skydds** i list rutan.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Välj skydds":::

1. När du har valt skydds i list rutan visas ett sido fält med tre flikar: RDP, SSH och skydds. Eftersom skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Välj **Använd skydds**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Välj Använd skydds":::

1. På sidan **Anslut med Azure-skydds** anger du användar namn och lösen ord för den virtuella datorn och väljer sedan **Anslut**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Anslut":::

1. RDP-anslutningen till den virtuella datorn via skydds öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Anslut med port 443":::
