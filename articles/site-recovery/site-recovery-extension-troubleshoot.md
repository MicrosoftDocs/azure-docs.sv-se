---
title: Felsök Azure VM-tillägget för haveri beredskap med Azure Site Recovery
description: Felsök problem med Azure VM-tillägget för haveri beredskap med Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009712"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Felsöka problem som gäller Azure VM-tillägg

Den här artikeln innehåller fel söknings steg som kan hjälpa dig att lösa Azure Site Recovery fel som rör VM-agenten och tillägget.

## <a name="low-system-resources"></a>Låga system resurser

Det här problemet uppstår när systemet har ont om ledigt minne och inte kan allokera minne för mobilitets tjänst installationen. Se till att tillräckligt med minne har frigjorts för att installationen ska kunna fortsätta och slutföras.

## <a name="azure-site-recovery-extension-time-out"></a>Timeout för Azure Site Recoverys tillägg  

Fel meddelande: "tids gränsen nåddes för aktivitets körningen under spårningen för att tilläggs åtgärden skulle startats"<br>
Felkod: "151076"

 Azure Site Recovery installerat ett tillägg på den virtuella datorn som en del av ett jobb för att aktivera skydd. Något av följande villkor kan förhindra att skyddet utlöses och att jobbet Miss fungerar. Slutför följande fel söknings steg och försök sedan igen:

- [Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery-tillägget kan inte uppdateras eller läsas in](#the-site-recovery-extension-fails-to-update-or-load)

Fel meddelande: "föregående Site Recovery tilläggs åtgärd tar längre tid än förväntat."<br>
Felkod: "150066"

- [Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Site Recovery tilläggets status är felaktig](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Skyddet Miss lyckas eftersom den virtuella dator agenten inte svarar

Fel meddelande: "tids gränsen nåddes för uppgifts körningen under spårningen för att tilläggs åtgärden skulle starta."<br>
Felkod: "151099"

Det här felet kan inträffa om Azure Guest-agenten i den virtuella datorn inte har statusen klar.

Du kan kontrol lera statusen för Azures gästa Gent i [Azure Portal](https://portal.azure.com/). Gå till den virtuella datorn som du försöker skydda och kontrol lera statusen i egenskaper för **VM**-  >  **Inställningar**  >    >  **agent**. I de flesta fall är statusen för agenten klar efter omstart av den virtuella datorn. Men om du inte kan starta om eller om du fortfarande har problem, kan du utföra följande fel söknings steg:

- [Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Fel meddelande: "tids gränsen nåddes för uppgifts körningen under spårningen för att tilläggs åtgärden skulle starta."<br>
Felkod: "151095"

Felet uppstår när Agent versionen på Linux-datorn är inaktuell. Slutför följande fel söknings steg:

- [Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten installeras på den virtuella datorn, men den svarar inte (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning
Den virtuella dator agenten kan ha skadats eller också har tjänsten stoppats. Genom att installera om VM-agenten får du den senaste versionen. Den hjälper också till att starta om kommunikationen med tjänsten.

1. Ta reda på om tjänsten Windows Azure gästa Gent körs i VM-tjänsterna (Services. msc). Starta om tjänsten Windows Azure gästa Gent.    
1. Om tjänsten Windows Azure gästa Gent inte visas i tjänster öppnar du kontroll panelen. Gå till **program och funktioner** för att se om tjänsten Windows gästa Gent är installerad.
1. Om Windows Azures gästa Gent visas i **program och funktioner** avinstallerar du Windows Azures gästa Gent.
1. Hämta och installera den [senaste versionen av agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörs behörighet för att slutföra installationen.
1. Kontrol lera att tjänsten Windows Azure gästa Gent visas i tjänster.
1. Starta om skydds jobbet.

Kontrol lera också att [Microsoft .NET 4,5 har installerats](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) på den virtuella datorn. Du behöver .NET 4,5 för att VM-agenten ska kunna kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad på den virtuella datorn är inaktuell (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning
De flesta Agent-relaterade eller felrelaterade fel för virtuella Linux-datorer orsakas av problem som påverkar en gammal VM-agent. Du kan felsöka det här problemet genom att följa dessa allmänna rikt linjer:

1. Följ anvisningarna för [att uppdatera Linux VM-agenten](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > Vi *rekommenderar starkt* att du bara uppdaterar agenten via en distributions lagrings plats. Vi rekommenderar inte att du laddar ned agent koden direkt från GitHub och uppdaterar den. Om den senaste agenten för distributionen inte är tillgänglig kan du kontakta distributions supporten för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten går du till sidan [Windows Azure Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) i GitHub-lagringsplatsen.

1. Se till att Azure-agenten körs på den virtuella datorn genom att köra följande kommando: `ps -e`

   Om processen inte körs startar du om den med hjälp av följande kommandon:

   - För Ubuntu: `service walinuxagent start`
   - För andra distributioner: `service waagent start`

1. [Konfigurera agenten för automatisk omstart](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Aktivera skydd av den virtuella datorn.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Site Recovery-tillägget kan inte uppdateras eller läsas in

Tilläggets status visas som "Tom", "notrappsteg" eller "över gång".

#### <a name="solution"></a>Lösning

Avinstallera tillägget och försök sedan igen.

Så här avinstallerar du tillägget:

1. I [Azure Portal](https://portal.azure.com/)går du till den virtuella dator som har problem med säkerhets kopieringen.
1. Välj **inställningar**.
1. Välj **Tillägg**.
1. Välj **Site Recovery tillägg**.
1. Välj **Avinstallera**.

För virtuella Linux-datorer, om VMSnapshot-tillägget inte visas i Azure Portal, [uppdaterar du Azure Linux-agenten](../virtual-machines/extensions/update-linux-agent.md). Kör sedan skyddet.

När du har slutfört de här stegen kommer tillägget att installeras om under skyddet.
