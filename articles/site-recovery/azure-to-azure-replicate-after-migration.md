---
title: Konfigurera haveri beredskap efter migrering till Azure med Azure Site Recovery
description: Den här artikeln beskriver hur du förbereder datorer för att konfigurera haveri beredskap mellan Azure-regioner efter migrering till Azure med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a71e476a214c44514c7d57c54a09a38218ad6d2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135666"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurera haveriberedskap för virtuella Azure-datorer efter migrering till Azure 


Följ den här artikeln om du har [migrerat lokala datorer till virtuella Azure-](./migrate-tutorial-on-premises-azure.md) datorer med hjälp av [Site Recovery](site-recovery-overview.md) tjänsten och du nu vill hämta de virtuella datorerna för haveri beredskap till en sekundär Azure-region. Artikeln beskriver hur du ser till att Azure VM-agenten är installerad på migrerade virtuella datorer och hur du tar bort den Site Recovery mobilitets tjänsten som inte längre behövs efter migreringen.



## <a name="verify-migration"></a>Verifiera migrering

Innan du konfigurerar haveri beredskap ser du till att migreringen har slutförts som förväntat. Om du vill slutföra en migrering efter redundansväxlingen väljer du alternativet **fullständig migrering** för varje dator som du vill migrera. 

## <a name="verify-the-azure-vm-agent"></a>Verifiera Azure VM-agenten

[Azure VM-agenten](../virtual-machines/extensions/agent-windows.md) måste vara installerad på varje virtuell Azure-dator. Site Recovery installerar ett tillägg på agenten för att replikera virtuella Azure-datorer.

- Om datorn kör version 9.7.0.0 eller senare av tjänsten Site Recovery mobilitet installeras Azure VM-agenten automatiskt av mobilitets tjänsten på virtuella Windows-datorer. I tidigare versioner av mobilitets tjänsten installerar du agenten manuellt.
- För virtuella Linux-datorer måste du installera Azure VM-agenten manuellt. Du behöver bara installera Azure VM-agenten om mobilitets tjänsten som är installerad på den migrerade datorn är v 9.6 eller tidigare.


### <a name="install-the-agent-on-windows-vms"></a>Installera agenten på virtuella Windows-datorer

Om du kör en version av tjänsten Site Recovery mobilitet som är tidigare än 9.7.0.0, eller om du har några andra behov av att installera agenten manuellt, gör du följande:  

1. Se till att du har administratörs behörighet på den virtuella datorn.
2. Hämta [installations programmet för VM-agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Kör installations filen.

#### <a name="validate-the-installation"></a>Verifiera installationen
Kontrol lera att agenten är installerad:

1. På den virtuella Azure-datorn ska du se WaAppAgent.exe-filen i mappen C:\WindowsAzure\Packages.
2. Högerklicka på filen och i **Egenskaper**, Välj fliken **information** .
3. Kontrol lera att fältet **produkt version** visar 2.6.1198.718 eller högre.

[Lär dig mer](../virtual-machines/extensions/agent-windows.md) om agent installation för Windows.

### <a name="install-the-agent-on-linux-vms"></a>Installera agenten på virtuella Linux-datorer

Installera [Azure Linux VM](../virtual-machines/extensions/agent-linux.md) -agenten manuellt på följande sätt:

1. Kontrol lera att du har administratörs behörighet på datorn.
2. Vi rekommenderar starkt att du installerar Linux VM-agenten med ett RPM-eller DEB-paket från distributionens paket lagrings plats. Alla godkända [distributions leverantörer](../virtual-machines/linux/endorsed-distros.md) integrerar Azure Linux Agent-paketet i sina avbildningar och databaser.
    - Vi rekommenderar starkt att du bara uppdaterar agenten via en distributions lagrings plats.
    - Vi rekommenderar inte att du installerar Linux VM-agenten direkt från GitHub och uppdaterar den.
    -  Om den senaste agenten för distributionen inte är tillgänglig kan du kontakta distributions supporten för instruktioner om hur du installerar den. 

#### <a name="validate-the-installation"></a>Verifiera installationen 

1. Kör det här kommandot: **PS-e** för att säkerställa att Azure-agenten körs på den virtuella Linux-datorn.
2. Om processen inte körs startar du om den med hjälp av följande kommandon:
    - För Ubuntu: **tjänsten walinuxagent start**
    - För andra distributioner: **tjänsten waagent start**


## <a name="uninstall-the-mobility-service"></a>Avinstallera mobilitets tjänsten

1. Avinstallera mobilitets tjänsten manuellt från den virtuella Azure-datorn med någon av följande metoder. 
    - För Windows går du till kontroll panelen > **Lägg till/ta bort program**, avinstallerar **Microsoft Azure Site Recovery mobilitets tjänsten/huvud mål servern**. Kör följande i en upphöjd kommando tolk:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - För Linux loggar du in som en rot användare. I en Terminal går du till **/User/Local/ASR** och kör följande kommando:
        ```
        ./uninstall.sh -Y
        ```
2. Starta om den virtuella datorn innan du konfigurerar replikering.

## <a name="next-steps"></a>Nästa steg

[Läs fel sökning](site-recovery-extension-troubleshoot.md) för tillägget Site Recovery på Azure VM-agenten.
[Replikera snabbt](azure-to-azure-quickstart.md) en virtuell Azure-dator till en sekundär region.
