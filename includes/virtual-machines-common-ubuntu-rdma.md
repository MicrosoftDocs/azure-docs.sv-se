---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016142"
---
1. Installera DAPL, rdmacm, ibverbs och mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. I/etc/waagent.conf aktiverar du RDMA genom att ta bort kommentaren till följande konfigurations rader. Du behöver rot åtkomst för att redigera den här filen.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Lägg till eller ändra följande minnes inställningar i KB i/etc/Security/Limits.conf-filen. Du behöver rot åtkomst för att redigera den här filen. I test syfte kan du ange Memlock till obegränsad. Exempel: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Installera Intel MPI-biblioteket. [Köp och ladda ned](https://software.intel.com/intel-mpi-library/) biblioteket från Intel eller ladda ned den [kostnads fria utvärderings versionen](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Det finns bara stöd för Intel MPI 5. x-körningar.
 
   Installations anvisningar finns i [installations guiden för Intel MPI Library](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Aktivera ptrace för processer som inte har rot fel sökning (behövs för de senaste versionerna av Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
