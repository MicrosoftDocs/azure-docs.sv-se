---
title: Brandväggsregler på servernivå
description: Brandväggsregler på servernivå
keywords: SQL-anslutning, anslutnings sträng
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187422"
---
1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. I listan till vänster väljer du **alla tjänster**.

3. Bläddra och välj **SQL-servrar**.

    ![Hitta din Azure SQL Database-Server i portalen][b21-FindServerInPortal]
5. I text rutan Filter börjar du skriva namnet på servern. Raden visas.

6. Välj raden för servern. Ett blad för servern visas.

7. Välj **Inställningar** på Server bladet.

8. Välj **brand vägg**.

    ![Välj inställningar > brand vägg][b31-SettingsFirewallNavig]
9. Välj **Lägg till klient-IP**. Ange ett namn för den nya regeln i den första text rutan.

10. Ange de låga och höga IP-värdena för det intervall som du vill aktivera.

    * Det kan vara praktiskt att ha ett lågt värde för **DPM\DPM\ProtectionAgents\RA\3.0.** och det höga värdet slutar med **. 255**.

11. Välj **Spara**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
