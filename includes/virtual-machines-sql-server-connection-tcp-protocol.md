---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: f7af6962c3343cd9d3e35e96d88650aa6a42c6b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95559099"
---
1. Sök efter **Configuration Manager** medan du fortfarande är fjärrskrivbordsansluten till den virtuella datorn:

    ![Öppna SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Expandera **SQL Server-nätverkskonfiguration** i konsolfönstret i Konfigurationshanteraren för SQL Server.

1. I konsol fönstret klickar du på **protokoll för MSSQLSERVER** (standard instans namnet.) I informations fönstret högerklickar du på **TCP** och klickar på **Aktivera** om det inte redan är aktiverat.

    ![Aktivera TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. Klicka på **SQL Server-tjänster** i konsolfönstret. I informations fönstret högerklickar du på **SQL Server (*instans namn*)** (standard instansen är **SQL Server (MSSQLSERVER)**) och klickar sedan på **starta om** för att stoppa och starta om instansen av SQL Server.

    ![Starta om databasmotorn](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Stäng Konfigurationshanteraren för SQL Server.

Mer information om hur du aktiverar protokoll för SQL Server-databasmotorn finns i [Enable or Disable a Server Network Protocol](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) (Aktivera eller inaktivera ett servernätverksprotokoll).