---
title: 'Kopiera och klistra in till och från en virtuell dator: Azure skydds'
description: I den här artikeln får du lära dig hur du kopierar och klistrar in till och från en virtuell Azure-dator med skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: 4b0c2b734366f9a74a9b007ab9450ab4b4f51feb
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800439"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Kopiera och klistra in på en virtuell dator: Azure skydds

Den här artikeln hjälper dig att kopiera och klistra in text till och från virtuella datorer när du använder Azure-skydds. Innan du arbetar med en virtuell dator bör du kontrol lera att du har följt stegen för att [skapa en skydds-värd](./tutorial-create-host-portal.md). Anslut sedan till den virtuella dator som du vill arbeta med via [RDP](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

För webbläsare som har stöd för den avancerade API-åtkomsten till Urklipp, kan du kopiera och klistra in text mellan den lokala enheten och fjärrsessionen på samma sätt som du kopierar och klistrar in mellan program på din lokala enhet. För andra webbläsare kan du använda skydds urklipps Access verktyg-paletten.

>[!NOTE]
>Endast text kopiering/inklistring stöds för närvarande.
>

   ![Tillåt Urklipp](./media/bastion-vm-manage/allow.png)

Endast text kopiering och inklistring stöds. För direkt kopiering och inklistring kan webbläsaren uppmana dig att få åtkomst till Urklipp när skydds-sessionen initieras. **Tillåt** att webb sidan får åtkomst till Urklipp. Om du arbetar från en Mac är kortkommandot för att klistra in **Shift-Ctrl-V**.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Kopiera till en fjärrsession

När du har anslutit till den virtuella datorn med [Azure Portal ](https://portal.azure.com)utför du följande steg:

1. Kopiera texten/innehållet från den lokala enheten till lokalt Urklipp.
1. Under fjärrsessionen startar du paletten skydds urklipps åtkomst verktyg genom att välja de två pilarna. Pilarna finns i mitten till vänster i sessionen.

   ![Skärm bild som visar startpilarna för verktygs-paletten markerat på vänster sida i fönstret.](./media/bastion-vm-manage/left.png)

   ![Skärm bild som visar ett urklipp för text som kopieras i skydds.](./media/bastion-vm-manage/clipboard.png)
1. Normalt visas den kopierade texten automatiskt på skydds kopiera klistra in-palett. Om texten inte finns där klistrar du in texten i text rutan på paletten.
1. När texten finns i text områden kan du klistra in den i fjärrsessionen.

   ![Skärm bild som visar knappen Kopiera/klistra in markerad och en exempel text sträng som kopierats till fjärrsessionen.](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Kopiera från en fjärran sluten session

När du har anslutit till den virtuella datorn med [Azure Portal ](https://portal.azure.com)utför du följande steg:

1. Kopiera texten/innehållet från fjärrsessionen till fjärran sluten Urklipp (med CTRL-C).

   ![verktygs palett](./media/bastion-vm-manage/remote.png)
1. Under fjärrsessionen startar du paletten skydds urklipps åtkomst verktyg genom att välja de två pilarna. Pilarna finns i mitten till vänster i sessionen.

   ![skrivskiva](./media/bastion-vm-manage/clipboard2.png)
1. Normalt visas den kopierade texten automatiskt på skydds kopiera klistra in-palett. Om texten inte finns där klistrar du in texten i text rutan på paletten.
1. När texten finns i text område kan du klistra in den på den lokala enheten.

   ![Inklistringsfel](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).
