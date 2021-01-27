---
title: Felsök problem med Azure VM RDP-anslutning efter händelse-ID | Microsoft Docs
description: 'Använd händelse-ID: n för att felsöka olika problem som förhindrar en RDP-anslutning (Remote Desktop Protocol) till en virtuell Azure-dator (VM).'
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: c293945a52dd810975b36144f224278163166ba8
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878451"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Felsöka Azure VM RDP-anslutningsfel efter händelse-ID 

Den här artikeln förklarar hur du använder händelse-ID: n för att felsöka problem som förhindrar en RDP-anslutning (Remote Desktop Protocol) till en virtuell Azure-dator (VM).

## <a name="symptoms"></a>Symtom

Du försöker använda en RDP-session (Remote Desktop Protocol) för att ansluta till en virtuell Azure-dator. När du har inmatat dina autentiseringsuppgifter, Miss lyckas anslutningen och du får följande fel meddelande:

**Datorn kan inte ansluta till fjärrdatorn. Försök ansluta igen. om problemet kvarstår kan du kontakta ägaren till fjärrdatorn eller nätverks administratören.**

Om du vill felsöka det här problemet granskar du händelse loggarna på den virtuella datorn och läser sedan följande scenarier.

## <a name="before-you-troubleshoot"></a>Innan du felsöker

### <a name="create-a-backup-snapshot"></a>Skapa en ögonblicks bild av säkerhets kopia

Om du vill skapa en ögonblicks bild av säkerhets kopia följer du stegen i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Fjärrans luta till den virtuella datorn

Om du vill ansluta till den virtuella datorn via fjärr anslutning använder du någon av metoderna i [hur du använder fjärrverktyg för att felsöka problem med virtuella Azure-datorer](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scenario 1

### <a name="event-logs"></a>Händelseloggar

I en CMD-instans kör du följande kommandon för att kontrol lera om händelse 1058 eller händelse 1057 loggas i system loggen under de senaste 24 timmarna:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logg namn:**      Säker <br />
**Källa:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tid* <br />
**Händelse-ID:**      1058 <br />
**Uppgifts kategori:** Alternativet <br />
**Nivå:**         Fels <br />
**Nyckelord:**      Form <br />
**Användare:**          EJ TILLÄMPLIGT <br />
**Dator:**      *dator* <br />
**Beskrivning:** Värd servern för FJÄRRSKRIVBORDSSESSION har inte bytt ut det inaktuella självsignerade certifikatet som användes för sessionsvärdservern för fjärrskrivbordssession på TLS-anslutningar. Åtkomst till relevant status kod nekades.

**Logg namn:**      Säker <br />
**Källa:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tid* <br />
**Händelse-ID:**      1058 <br />
**Uppgifts kategori:** Alternativet <br />
**Nivå:**         Fels <br />
**Nyckelord:**      Form <br />
**Användare:**          EJ TILLÄMPLIGT <br />
**Dator:**      *dator* <br />
**Beskrivning:** Det gick inte att skapa ett nytt självsignerat certifikat som ska användas för värd servern för fjärrskrivbordssession på TLS-anslutningar i värd servern för FJÄRRSKRIVBORDSSESSION. det finns redan en relevant status kod för objektet.

**Logg namn:**      Säker <br />
**Källa:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tid* <br />
**Händelse-ID:**      1057 <br />
**Uppgifts kategori:** Alternativet <br />
**Nivå:**         Fels <br />
**Nyckelord:**      Form <br />
**Användare:**          EJ TILLÄMPLIGT <br />
**Dator:**      *dator* <br />
**Beskrivning:** Värd servern för FJÄRRSKRIVBORDSSESSION kunde inte skapa ett nytt självsignerat certifikat som ska användas för värd serverns autentisering för FJÄRRSKRIVBORDSSESSION på TLS-anslutningar. Den relevanta status koden var nyckel uppsättning saknas

Du kan också söka efter SCHANNEL-felhändelser 36872 och 36870 genom att köra följande kommandon:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logg namn:**      Säker <br />
**Källa:**        Schannel <br />
**Datum:**          – <br />
**Händelse-ID:**      36870 <br />
**Uppgifts kategori:** Alternativet <br />
**Nivå:**         Fels <br />
**Reserverade**       <br />
**Användare:**          SÄKER <br />
**Dator:**      *dator* <br />
**Beskrivning:** Ett allvarligt fel uppstod vid försök att få åtkomst till den privata nyckeln för TLS-servern. Den felkod som returnerades från den kryptografiska modulen är 0x8009030D.  <br />
Det interna fel tillstånd är 10001.

### <a name="cause"></a>Orsak
Det här problemet beror på att lokala RSA-krypteringsnyckeln i mappen MachineKeys på den virtuella datorn inte kan nås. Det här problemet kan bero på någon av följande orsaker:

1. Fel behörighets konfiguration för mappen MachineKeys eller RSA-filerna.

2. RSA-nyckeln är skadad eller saknas.

### <a name="resolution"></a>Lösning

För att felsöka det här problemet måste du konfigurera rätt behörigheter på RDP-certifikatet med hjälp av dessa steg.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Bevilja behörighet till mappen MachineKeys

1. Skapa ett skript med hjälp av följande innehåll:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Kör det här skriptet för att återställa behörigheterna för mappen MachineKey och återställa RSA-filerna till standardvärdena.

3.  Försök att komma åt den virtuella datorn igen.

När du har kört skriptet kan du kontrol lera följande filer som har problem med behörigheten:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Förnya självsignerat RDP-certifikat

Om problemet kvarstår kör du följande skript för att se till att det självsignerade RDP-certifikatet förnyas:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Om du inte kan förnya certifikatet följer du dessa steg för att försöka ta bort certifikatet:

1. Öppna rutan **Kör** på en annan virtuell dator i samma VNet, Skriv **MMC** och tryck sedan på **OK**. 

2. På **Arkiv** -menyn väljer du **Lägg till/ta bort snapin-modul**.

3. I listan **Tillgängliga snapin-moduler** väljer du **certifikat** och väljer sedan **Lägg till**.

4. Välj **dator konto** och välj sedan **Nästa**.

5. Välj **en annan dator** och Lägg sedan till IP-adressen för den virtuella dator som har problem.
   >[!Note]
   >Försök att använda det interna nätverket för att undvika att använda en virtuell IP-adress.

6. Välj **Slutför** och välj sedan **OK**.

   ![Välj dator](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Expandera certifikaten, gå till mappen fjärr Desktop\Certificates, högerklicka på certifikatet och välj sedan **ta bort**.

8. Starta om tjänsten konfiguration av fjärr skrivbord:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >I det här läget visas certifikatet igen om du uppdaterar butiken från MMC. 

Försök att komma åt den virtuella datorn med hjälp av RDP igen.

#### <a name="update-tlsssl-certificate"></a>Uppdatera TLS/SSL-certifikat

Om du konfigurerar den virtuella datorn så att den använder ett TLS/SSL-certifikat kör du följande kommando för att hämta tumavtrycket. Kontrol lera sedan om det är detsamma som certifikatets tumavtryck:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Om den inte är det ändrar du tumavtrycket:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Du kan också försöka ta bort nyckeln så att RDP använder det självsignerade certifikatet för RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenario 2

### <a name="event-log"></a>Händelseloggen

I en CMD-instans kör du följande kommandon för att kontrol lera om SCHANNEL-felhändelse 36871 är inloggad i system loggen under de senaste 24 timmarna:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logg namn:**      Säker <br />
**Källa:**        Schannel <br />
**Datum:**          – <br />
**Händelse-ID:**      36871 <br />
**Uppgifts kategori:** Alternativet <br />
**Nivå:**         Fels <br />
**Reserverade**       <br />
**Användare:**          SÄKER <br />
**Dator:**      *dator* <br />
**Beskrivning:** Ett allvarligt fel inträffade när en TLS-serverns autentiseringsuppgifter skulle skapas. Det interna fel tillstånd är 10013.
 
### <a name="cause"></a>Orsak

Det här problemet orsakas av säkerhets principer. När äldre versioner av TLS (till exempel 1,0) är inaktiverade, Miss lyckas RDP-åtkomst.

### <a name="resolution"></a>Lösning

RDP använder TLS 1,0 som standard protokoll. Protokollet kan dock ändras till TLS 1,1, som är den nya standarden.

Information om hur du felsöker det här problemet finns i [Felsöka autentiseringsfel när du använder RDP för att ansluta till den virtuella Azure-datorn](/troubleshoot/azure/virtual-machines/cannot-connect-rdp-azure-vm#tls-version).

## <a name="scenario-3"></a>Scenario 3

Om du har installerat **anslutning till fjärrskrivbord Broker** -rollen på den virtuella datorn kontrollerar du om det finns händelse 2056 eller event 1296 under de senaste 24 timmarna. Kör följande kommandon i en CMD-instans: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logg namn:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Källa:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:**          *tid* <br />
**Händelse-ID:**      2056 <br />
**Uppgifts kategori:** (109) <br />
**Nivå:**         Fels <br />
**Reserverade**       <br />
**Användare:**          NÄTVERKS TJÄNST <br />
**Dator:**      *dator-FQDN* <br />
**Beskrivning:** Det går inte att hitta beskrivningen för händelse-ID 2056 från källan Microsoft-Windows-TerminalServices-SessionBroker. Antingen är komponenten som aktiverar den här händelsen inte installerad på den lokala datorn eller så är installationen skadad. Du kan installera eller reparera komponenten på den lokala datorn. <br />
Om händelsen kommer från en annan dator, var visnings informationen Sparad med händelsen. <br />
Följande information inkluderades i händelsen: <br />
NULL <br />
NULL <br />
Det gick inte att logga in på databasen.

**Logg namn:**      Microsoft-Windows-TerminalServices-SessionBroker-client/Operational <br />
**Källa:**        Microsoft-Windows-TerminalServices-SessionBroker-client <br />
**Datum:**          *tid* <br />
**Händelse-ID:**      1296 <br />
**Uppgifts kategori:** (104) <br />
**Nivå:**         Fels <br />
**Reserverade**       <br />
**Användare:**          NÄTVERKS TJÄNST <br />
**Dator:**      *dator-FQDN* <br />
**Beskrivning:** Det går inte att hitta beskrivningen för händelse-ID 1296 från källan Microsoft-Windows-TerminalServices-SessionBroker-client. Antingen är komponenten som aktiverar den här händelsen inte installerad på den lokala datorn eller så är installationen skadad. Du kan installera eller reparera komponenten på den lokala datorn.
Om händelsen kommer från en annan dator, var visnings informationen Sparad med händelsen.
Följande information inkluderades i händelsen:  <br />
*information* <br />
*information* <br />
Anslutning till fjärrskrivbord Broker är inte klar för RPC-kommunikation.

### <a name="cause"></a>Orsak

Det här problemet beror på att värd namnet för Anslutning till fjärrskrivbord Broker-servern ändras, vilket inte är en ändring som stöds. 

Värd namnet har poster och beroenden i den interna Windows-databasen, vilket krävs av Server grupp för fjärr skrivbord för att kunna fungera. Att ändra värdnamn efter att Server gruppen redan har skapats orsakar många fel och kan orsaka att Broker-servern slutar fungera.

### <a name="resolution"></a>Lösning 

För att åtgärda det här problemet måste Anslutning till fjärrskrivbord Broker-rollen och den interna Windows-databasen installeras om.

## <a name="next-steps"></a>Nästa steg

[Schannel-händelser](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786445(v=ws.11))

[Teknisk översikt av SSP med säker kanal](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786429(v=ws.11))

[RDP Miss lyckas med händelse-ID 1058 & händelse 36870 med värd certifikat för fjärrskrivbordssession & SSL-kommunikation](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)

[Schannel 36872 eller Schannel 36870 på en domänkontrollant](/archive/blogs/instan/schannel-36872-or-schannel-36870-on-a-domain-controller)

[Händelse-ID 1058 – Fjärrskrivbordstjänster autentisering och kryptering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee890862(v=ws.10))