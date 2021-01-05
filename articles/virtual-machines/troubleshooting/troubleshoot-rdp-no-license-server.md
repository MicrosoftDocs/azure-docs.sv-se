---
title: Servern för fjärr skrivbords licenser är inte tillgänglig när du ansluter till en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker RDP-problem eftersom ingen licens Server för fjärr skrivbord är tillgänglig | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 6b5864d40d2a4d8f8d6cf404df29f909a73f04e2
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832051"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Servern för fjärr skrivbords licenser är inte tillgänglig när du ansluter till en virtuell Azure-dator

Den här artikeln hjälper till att lösa problemet när du inte kan ansluta till en virtuell Azure-dator eftersom ingen licens Server för fjärr skrivbord är tillgänglig för att tillhandahålla en licens.

## <a name="symptoms"></a>Symtom

När du försöker ansluta till en virtuell dator (VM) uppstår följande scenarier:

- Skärm bilden för den virtuella datorn visar att operativ systemet är fullständigt inläst och väntar på autentiseringsuppgifter.
- Du får följande fel meddelanden när du försöker skapa en RDP-anslutning (Microsoft Fjärrskrivbord Protocol):

  - Fjärrsessionen kopplades från eftersom det inte finns några fjärr skrivbords licens servrar tillgängliga för att tillhandahålla en licens.

  - Det finns ingen tillgänglig licens Server för fjärr skrivbord. Fjärrskrivbordstjänster upphör att fungera eftersom den här datorn har passerat sin respitperiod och inte har kontaktat minst en giltig Windows Server 2008-licens Server. Välj det här meddelandet för att öppna konfiguration av värd Server för FJÄRRSKRIVBORDSSESSION för att använda Licensieringsdiagnostik.

Du kan dock ansluta till den virtuella datorn normalt genom att använda en administrativ session:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Orsak

Det här problemet uppstår om en server för fjärr skrivbords licenser inte är tillgänglig för att tillhandahålla en licens för att starta en fjärrsession. Det kan orsakas av flera scenarier, även om en värd roll för fjärrskrivbordssession har kon figurer ATS på den virtuella datorn:

- Det fanns aldrig någon fjärr skrivbords licensierings roll i miljön och Grace-perioden 180 dagar är över.
- En fjärr skrivbords licens installerades i miljön, men den har aldrig Aktiver ATS.
- En fjärr skrivbords licens i miljön har ingen klient åtkomst licens (CAL) som matats in för att konfigurera anslutningen.
- En fjärr skrivbords licens har installerats i miljön. Det finns tillgängliga Cal, men de har inte kon figurer ATS korrekt.
- En fjärr skrivbords licens har CAL och Aktiver ATS. Vissa andra problem på servern för fjärr skrivbords licenser förhindrar dock att den tillhandahåller licenser i miljön.

## <a name="solution"></a>Lösning

Lös problemet genom att [säkerhetskopiera OS-disken](../windows/snapshot-copy-managed-disk.md) och följ dessa steg:

1. Ansluta till den virtuella datorn med hjälp av en administrativ session:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Om du inte kan ansluta till den virtuella datorn med hjälp av en administrativ session kan du använda [serie konsolen för virtuella datorer på Azure](serial-console-windows.md) för att få åtkomst till den virtuella datorn på följande sätt:

    1. Öppna serie konsolen genom att välja **support & fel söknings**  >  **seriell konsol (för hands version)**. Om funktionen är aktive rad på den virtuella datorn kan du ansluta den virtuella datorn.

    2. Skapa en ny kanal för en CMD-instans. Ange **cmd** för att starta kanalen och hämta kanal namnet.

    3. Växla till kanalen som kör CMD-instansen. I det här fallet ska det vara kanal 1:

       ```
       ch -si 1
       ```

    4. Välj **RETUR** igen och ange ett giltigt användar namn och lösen ord, lokalt eller domän-ID för den virtuella datorn.

2. Kontrol lera om den virtuella datorn har en värd roll för fjärrskrivbordssession aktive rad. Om rollen är aktive rad kontrollerar du att den fungerar korrekt. Öppna en upphöjd kommando instans och följ dessa steg:

    1. Använd följande kommando för att kontrol lera status för värd rollen värd för fjärrskrivbordssession:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Om det här kommandot returnerar värdet 0 innebär det att rollen är inaktive rad och att du kan gå till steg 3.

    2. Använd följande kommando för att kontrol lera principerna och konfigurera om efter behov:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode

        reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Om värdet **LicensingMode** är inställt på något annat värde än 4, per användare, anger du värdet till 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Om värdet **SpecifiedLicenseServers** inte finns, eller om det har felaktig licens Server information, ändrar du den på följande sätt:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. När du har gjort ändringar i registret startar du om den virtuella datorn.

    4. Om du inte har Cal tar du bort rollen värd för fjärrskrivbordssession. Sedan kommer RDP att ställas in på normal igen. Det tillåter bara två samtidiga RDP-anslutningar till den virtuella datorn:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Om den virtuella datorn har rollen fjärr skrivbords licensiering och den inte används kan du också ta bort rollen:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Kontrol lera att den virtuella datorn kan ansluta till servern för fjärr skrivbords licenser. Du kan testa anslutningen till porten 135 mellan den virtuella datorn och licens servern: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Om det inte finns någon fjärr skrivbords licens server i miljön och du vill ha en, kan du [installera en roll tjänst för fjärr skrivbords licensiering](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc731765(v=ws.11)). [Konfigurera sedan fjärr skrivbords licensiering](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Om en server för fjärr skrivbords licenser har kon figurer ATS och är felfritt kontrollerar du att servern för fjärr skrivbords licenser är aktive rad med klient åtkomst licenser.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet.
