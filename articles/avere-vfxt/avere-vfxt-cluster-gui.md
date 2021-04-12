---
title: Öppna kontroll panelen aver vFXT – Azure
description: Så här ansluter du till vFXT-klustret och den webbläsarbaserade AVERT-kontroll panelen för att konfigurera AVERT vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 79e7c5db2a2c445ae740a21744a0bdfe0736c01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342441"
---
# <a name="access-the-vfxt-cluster"></a>Komma åt vFXT-klustret

Om du vill justera kluster inställningarna och övervaka klustret använder du kontroll panelen aver. AVERT-kontroll panelen är ett webbläsarbaserat grafiskt gränssnitt till klustret.

Eftersom vFXT-klustret finns i ett privat virtuellt nätverk måste du skapa en SSH-tunnel eller använda en annan metod för att uppnå klustrets hanterings-IP-adress.

Det finns två grundläggande steg:

1. Skapa en anslutning mellan din arbets Station och det privata virtuella nätverket
1. Läsa in klustrets kontroll panel i en webbläsare

> [!NOTE]
> Den här artikeln förutsätter att du har angett en offentlig IP-adress på kluster styrenheten eller en annan virtuell dator i klustrets virtuella nätverk. Den här artikeln beskriver hur du använder den virtuella datorn som värd för att komma åt klustret. Om du använder en VPN-eller ExpressRoute för åtkomst till virtuella nätverk går du vidare till [Anslut till kontroll panelen aver](#connect-to-the-avere-control-panel-in-a-browser).

Innan du ansluter måste du kontrol lera att det offentliga/privata SSH-nyckelpar som du använde när du skapade kluster styrenheten är installerad på den lokala datorn. Läs dokumentationen om SSH-nycklar för [Windows](../virtual-machines/linux/ssh-from-windows.md) eller för [Linux](../virtual-machines/linux/mac-create-ssh-keys.md) om du behöver hjälp. Om du använder ett lösen ord i stället för en offentlig nyckel uppmanas du att ange det när du ansluter.

## <a name="create-an-ssh-tunnel"></a>Skapa en SSH-tunnel

Du kan skapa en SSH-tunnel från kommando raden i ett Linux-baserat eller ett Windows 10-klient system.

Använd ett SSH-tunnel kommando med det här formuläret:

SSH-L- *local_port*:*cluster_mgmt_ip*: 443 *controller_username* \@ *controller_public_IP*

Det här kommandot ansluter till klustrets hanterings-IP-adress via kluster styrenhetens IP-adress.

Exempel:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Autentisering sker automatiskt om du använde din offentliga SSH-nyckel för att skapa klustret och den matchande nyckeln är installerad på klient systemet. Om du använder ett lösen ord uppmanas du att ange det i systemet.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Ansluta till kontroll panelen aver i en webbläsare

Det här steget använder en webbläsare för att ansluta till konfigurations verktyget i vFXT-klustret.

* För en SSH-tunnel-anslutning öppnar du webbläsaren och går till `https://127.0.0.1:8443` .

  Du är ansluten till klustrets IP-adress när du skapade tunneln, så du behöver bara använda den lokala IP-adressen i webbläsaren. Om du har använt en annan lokal port än 8443 använder du Port numret i stället.

* Om du använder en VPN-eller ExpressRoute för att komma åt klustret går du till kluster hanteringens IP-adress i webbläsaren. Exempel: ``https://203.0.113.51``

Beroende på din webbläsare kan du behöva klicka på **Avancerat** och kontrol lera att det är säkert att gå vidare till sidan.

Ange användar namn `admin` och administratörs lösen ord som du angav när du skapade klustret.

![Skärm bild av inloggnings sidan för AVERT ifyllt med användar namnet admin och ett lösen ord](media/avere-vfxt-gui-login.png)

Klicka på **Logga in** eller tryck på RETUR på tangent bordet.

## <a name="next-steps"></a>Nästa steg

När du har loggat in på klustrets kontroll panel aktiverar du [stöd](avere-vfxt-enable-support.md).