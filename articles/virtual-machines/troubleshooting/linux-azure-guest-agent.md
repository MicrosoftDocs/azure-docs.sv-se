---
title: Felsöka Azure Linux-agenten
description: Lös problem med Azure Linux-agenten.
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878705"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Felsöka Azure Linux-agenten

[Azure Linux-agenten](../extensions/agent-linux.md) gör det möjligt för en virtuell dator (VM) att kommunicera med infrastruktur styrenheten (den underliggande fysiska server som den virtuella datorn finns på) på IP-168.63.129.16.

>[!NOTE]
>Den här IP-adressen är en virtuell offentlig IP-adress som underlättar kommunikationen och inte ska blockeras. Mer information finns i [Vad är IP-168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera agentens status och version för att kontrol lera att den fortfarande stöds. Se [lägsta versions stöd för virtuella dator agenter i Azure](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) för att kontrol lera versions supporten eller se [vanliga frågor och svar om WALinuxAgent](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) för att hitta status och version.

## <a name="troubleshoot-a-not-ready-status"></a>Felsöka statusen inte klar

1. Kontrol lera tjänst status för Azure Linux-agenten för att kontrol lera att den körs. Tjänst namnet kan vara **walinuxagent** eller **waagent**.

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   Om tjänsten körs startar du om den för att lösa problemet. Om tjänsten har stoppats startar du den, väntar några minuter och kontrollerar sedan statusen igen.

1. Se till att automatisk uppdatering är aktiverat. Kontrol lera inställningen för automatisk uppdatering i **/etc/waagent.conf**.

   ```
   AutoUpdate.Enabled=y
   ```

   Mer information om hur du uppdaterar Azure Linux-agenten finns i [så här uppdaterar du Azure Linux-agenten på en virtuell dator](../extensions/update-linux-agent.md).

1. Kontrol lera att den virtuella datorn kan ansluta till infrastruktur styrenheten. Använd ett verktyg som sväng för att testa om den virtuella datorn kan ansluta till 168.63.129.16 på portarna 80, 443 och 32526. Om den virtuella datorn inte ansluter som förväntat kontrollerar du om utgående kommunikation över portarna 80, 443 och 32526 är öppen i din lokala brand vägg på den virtuella datorn. Om den här IP-adressen är blockerad kan den virtuella dator agenten Visa oväntad funktion.

## <a name="advanced-troubleshooting"></a>Avancerad felsökning

Händelser för att felsöka Azure Linux-agenten registreras i **/var/log/waagent.log** -filen.

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Det går inte att ansluta till WireServer IP (värd-IP)

Följande fel visas i **/var/log/waagent.log** -filen när den virtuella datorn inte kan komma åt WireServer-IP: en på värd servern.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Lös problemet så här:

* Anslut till den virtuella datorn med SSH och försök sedan att komma åt följande URL från svängen: http://168.63.129.16/?comp=versions .
* Sök efter eventuella problem som kan ha orsakats av en brand vägg, en proxy eller en annan källa som kan blockera åtkomst till IP-168.63.129.16.
* Kontrol lera om Linux-program varan iptables eller en brand vägg från tredje part blockerar åtkomsten till portarna 80, 443 och 32526.

## <a name="next-steps"></a>Nästa steg

[Kontakta Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)för ytterligare fel sökning av problem med Azure Linux-agenten.