---
title: Ansluta till en skalnings uppsättning för virtuella Windows-datorer med hjälp av Azure skydds | Microsoft Docs
description: I den här artikeln får du lära dig hur du ansluter till en skalnings uppsättning i en virtuell Azure-dator med hjälp av Azure skydds.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: charwen
ms.openlocfilehash: 001d2ff6789ec4cfcc391171f0859b67ab1ee0a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077787"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Ansluta till en skalnings uppsättning för virtuella datorer med hjälp av Azure skydds

Den här artikeln visar hur du på ett säkert och smidigt sätt kan använda RDP för den virtuella Windows-datorns skalnings uppsättnings instans i ett virtuellt Azure-nätverk med Azure skydds. Du kan ansluta till en instans av en skalnings uppsättning för virtuella datorer direkt från Azure Portal. När du använder Azure skydds kräver inte virtuella datorer en klient, agent eller ytterligare program vara. Mer information om Azure-skydds finns i [översikten](bastion-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har konfigurerat en Azure skydds-värd för det virtuella nätverk där den virtuella datorns skalnings uppsättning finns. Mer information finns i [skapa en Azure skydds-värd](./tutorial-create-host-portal.md). När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en instans av en skalnings uppsättning för virtuella datorer i det här virtuella nätverket. Skydds förutsätter att du använder RDP för att ansluta till en skalnings uppsättning för virtuella Windows-datorer och SSH för att ansluta till din skalnings uppsättning för virtuella Linux-datorer. Information om hur du ansluter till en virtuell Linux-dator finns i [ansluta till en VM – Linux](bastion-connect-vm-ssh.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Anslut via RDP

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den skalnings uppsättning för virtuella datorer som du vill ansluta till.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Gå till den instans av skalnings uppsättningen för virtuell dator som du vill ansluta till och välj sedan **Anslut**. När du använder en RDP-anslutning bör skalnings uppsättningen för den virtuella datorn vara en skalnings uppsättning för virtuella Windows-datorer.

   ![skalnings uppsättning för virtuell dator](./media/bastion-connect-vm-scale-set/2.png)
3. När du har valt **Anslut** visas ett sido fält med tre flikar – RDP, SSH och skydds. Välj fliken **skydds** i sido fältet. Om du inte etablerar skydds för det virtuella nätverket kan du välja länken för att konfigurera skydds. Konfigurations anvisningar finns i [Konfigurera skydds](./tutorial-create-host-portal.md).

   ![Fliken skydds](./media/bastion-connect-vm-scale-set/3.png)
4. På fliken skydds anger du användar namn och lösen ord för skalnings uppsättningen för den virtuella datorn och väljer sedan **Anslut**.

   ![connect](./media/bastion-connect-vm-scale-set/4.png)
5. RDP-anslutningen till den virtuella datorn via skydds öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).