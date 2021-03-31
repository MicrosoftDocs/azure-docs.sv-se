---
title: Förbereda käll datorer för att installera mobilitets tjänsten via push-installation för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure | Microsoft Docs
description: Lär dig hur du förbereder servern för att installera mobilitets agenten via push-installation för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av tjänsten Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 78fddb5b4512883f8e78d6ed53f6e3dbbeba0e4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90525005"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Förbered käll datorn för push-installation av mobilitets agenten

När du konfigurerar haveri beredskap för virtuella VMware-datorer och fysiska servrar med hjälp av [Azure Site Recovery](site-recovery-overview.md)installerar du [Site Recovery mobilitets tjänsten](vmware-physical-mobility-service-overview.md) på varje lokal virtuell VMware-dator och fysisk server.  Mobilitets tjänsten samlar in data skrivningar på datorn och vidarebefordrar dem till Site Recovery processerver.

## <a name="install-on-windows-machine"></a>Installera på Windows-dator

Gör följande på varje Windows-dator som du vill skydda:

1. Kontrol lera att det finns en nätverks anslutning mellan datorn och processervern. Om du inte har konfigurerat en separat processerver körs den som standard på konfigurations servern.
1. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste ha administratörs behörighet, antingen lokal eller domän. Använd endast det här kontot för push-installation och för agent uppdateringar.
2. Om du inte använder ett domän konto inaktiverar du åtkomst kontroll för fjärran vändare på den lokala datorn på följande sätt:
    - Lägg till ett nytt DWORD-värde under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System register nyckel: **LocalAccountTokenFilterPolicy**. Ange värdet till **1**.
    -  Kör följande kommando för att göra detta i en kommando tolk:
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. I Windows-brandväggen på den dator som du vill skydda väljer du **Tillåt en app eller funktion genom brand väggen**. Aktivera **fil-och skrivar delning** och **Windows Management Instrumentation (WMI)**. För datorer som tillhör en domän kan du konfigurera brand Väggs inställningarna med hjälp av ett grupprincip objekt (GPO).

   ![Brandväggsinställningar](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Lägg till kontot som du skapat i CSPSConfigtool. Det gör du genom att logga in på konfigurations servern.
5. Öppna **cspsconfigtool.exe**. Den är tillgänglig som en genväg på Skriv bordet och i mappen%ProgramData%\ASR\home\svsystems\bin.
6. På fliken **Hantera konton** väljer du **Lägg till konto**.
7. Lägg till kontot som du skapade.
8. Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.

## <a name="install-on-linux-machine"></a>Installera på Linux-datorn

Gör följande på varje Linux-dator som du vill skydda:

1. Se till att det finns en nätverks anslutning mellan Linux-datorn och processervern.
2. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste vara en **rotanvändare** på Linux-källservern. Använd endast det här kontot för push-installation och för uppdateringar.
3. Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
4. Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
5. Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
4. Aktivera SFTP-undersystem och lösenordsautentisering i sshd_config-filen. Det gör du genom att logga in som **rot**.
5. I **/etc/ssh/sshd_config** -filen letar du reda på raden som börjar med **PasswordAuthentication**.
6. Ta bort kommentaren till raden och ändra värdet till **Ja**.
7. Hitta raden som börjar med **under systemet** och ta bort kommentaren mellan linjen.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Starta om **sshd**-tjänsten.
9. Lägg till kontot som du skapat i CSPSConfigtool. Det gör du genom att logga in på konfigurations servern.
10. Öppna **cspsconfigtool.exe**. Den är tillgänglig som en genväg på Skriv bordet och i mappen%ProgramData%\home\svsystems\bin.
11. På fliken **Hantera konton** väljer du **Lägg till konto**.
12. Lägg till kontot som du skapade.
13. Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.
1. Ytterligare steg för att uppdatera eller skydda SUSE Linux Enterprise Server 11 SP3-eller RHEL 5-eller CentOS 5-eller Debian 7-datorer. [Se till att den senaste versionen är tillgänglig på konfigurations servern](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

## <a name="anti-virus-on-replicated-machines"></a>Virus skydd på replikerade datorer

Om datorer som du vill replikera har ett aktivt antivirus program som kör, se till att du undantar installationsmappen för mobilitets tjänsten från anti-virus-åtgärder (*C:\ProgramData\ASR\agent*). Detta säkerställer att replikeringen fungerar som förväntat.

## <a name="next-steps"></a>Nästa steg

När mobilitets tjänsten har installerats går du till Azure Portal och väljer **+ Replikera** för att börja skydda de virtuella datorerna. Läs mer om hur du aktiverar replikering för [virtuella VMware-datorer](vmware-azure-enable-replication.md) och [fysiska servrar](physical-azure-disaster-recovery.md#enable-replication).


