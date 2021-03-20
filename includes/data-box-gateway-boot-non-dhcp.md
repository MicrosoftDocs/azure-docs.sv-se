---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: db4e27c0972a93d870d0a6565f1bd3212146df62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582736"
---
Om du startar i en icke-DHCP-miljö följer du de här stegen för att distribuera den virtuella datorn för Data Box Gateway.

1. [Anslut till Windows PowerShell-gränssnittet för enheten](#connect-to-the-powershell-interface).
2. Använd `Get-HcsIpAddress` cmdleten för att visa en lista över nätverks gränssnitt som är aktiverade på den virtuella enheten. Om enheten har ett enda nätverksgränssnitt aktiverad är det tilldelade standardnamnet för gränssnittet `Ethernet`.

    I följande exempel visas användningen av denna cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Använd cmdleten `Set-HcsIpAddress` för att konfigurera nätverket. Se följande exempel:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

