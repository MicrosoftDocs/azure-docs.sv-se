---
title: Felsöka Azure Site Recovery processervern
description: Den här artikeln beskriver hur du felsöker problem med Azure Site Recovery processervern
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: ad1bec66edaa3fcc6049f4911684f6e6d6c3e366
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999202"
---
# <a name="troubleshoot-the-process-server"></a>Felsöka processervern

[Site Recovery](site-recovery-overview.md) processervern används när du konfigurerar haveri beredskap till Azure för lokala virtuella VMware-datorer och fysiska servrar. Den här artikeln beskriver hur du felsöker problem med processervern, inklusive problem med replikering och anslutningar.

[Läs mer](vmware-physical-azure-config-process-server-overview.md) om processervern.

## <a name="before-you-start"></a>Innan du börjar

Innan du påbörjar fel sökningen:

1. Se till att du förstår hur du [övervakar process servrar](vmware-physical-azure-monitor-process-server.md).
2. Läs igenom metod tipsen nedan.
3. Se till att du följer [kapacitets överväganden](site-recovery-plan-capacity-vmware.md#capacity-considerations)och Använd storleks vägledningen för [konfigurations servern](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) eller [fristående process servrar](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Metod tips för distribution av process Server

För optimala prestanda för processervern har vi sammanfattat ett antal allmänna bästa metoder.

**Bästa praxis** | **Information**
--- |---
**Användning** | Kontrol lera att konfigurations servern/fristående processervern endast används för avsett syfte. Kör inte något annat på datorn.
**IP-adress** | Kontrol lera att processervern har en statisk IPv4-adress och att NAT inte har kon figurer ATS.
**Kontroll av minne/CPU-användning** |Behåll processor-och minnes användning under 70%.
**Säkerställ ledigt utrymme** | Ledigt utrymme avser cache-disk utrymmet på processervern. Replikeringsdata lagras i cacheminnet innan den överförs till Azure.<br/><br/> Behåll ledigt utrymme över 25%. Om det går under 20% begränsas replikeringen för replikerade datorer som är associerade med processervern.

## <a name="check-process-server-health"></a>Kontrol lera process serverns hälsa

Det första steget i fel sökning är att kontrol lera process serverns hälso tillstånd och status. Det gör du genom att granska alla aviseringar, kontrol lera att nödvändiga tjänster körs och kontrol lera att det finns ett pulsslag från processervern. De här stegen sammanfattas i följande bild, följt av procedurer som hjälper dig att utföra stegen.

![Felsöka process Server hälsa](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Steg 1: Felsök process serverns hälso aviseringar

Processervern genererar ett antal hälso aviseringar. De här aviseringarna och rekommenderade åtgärder sammanfattas i följande tabell.

**Aviseringstyp** | **Fel** | **Felsöka**
--- | --- | --- 
![Felfri][green] | Inget  | Processervern är ansluten och felfritt.
![Varning][yellow] | De angivna tjänsterna körs inte. | 1. kontrol lera att tjänsterna körs.<br/> 2. om tjänsterna körs som förväntat följer du anvisningarna nedan för att [Felsöka problem med anslutningen och replikeringen](#check-connectivity-and-replication).
![Varning][yellow]  | PROCESSOR användning > 80% under de senaste 15 minuterna. | 1. Lägg inte till nya datorer.<br/>2. kontrol lera att antalet virtuella datorer som använder processervern motsvarar [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations)och Överväg att konfigurera [ytterligare en processerver](vmware-azure-set-up-process-server-scale.md).<br/>3. Följ instruktionerna nedan för att [Felsöka problem med anslutningen och replikeringen](#check-connectivity-and-replication).
![Kritiskt][red] |  PROCESSOR användning > 95% under de senaste 15 minuterna. | 1. Lägg inte till nya datorer.<br/>2. kontrol lera att antalet virtuella datorer som använder processervern motsvarar [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations)och Överväg att konfigurera [ytterligare en processerver](vmware-azure-set-up-process-server-scale.md).<br/>3. Följ instruktionerna nedan för att [Felsöka problem med anslutningen och replikeringen](#check-connectivity-and-replication).<br/> 4. om problemet kvarstår kör du [distributions planeraren](./site-recovery-deployment-planner.md) för VMware/fysisk server-replikering.
![Varning][yellow] | Minnes användning > 80% under de senaste 15 minuterna. |  1. Lägg inte till nya datorer.<br/>2. kontrol lera att antalet virtuella datorer som använder processervern motsvarar [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations)och Överväg att konfigurera [ytterligare en processerver](vmware-azure-set-up-process-server-scale.md).<br/>3. Följ eventuella instruktioner som är associerade med varningen.<br/> 4. om problemet kvarstår följer du anvisningarna nedan för att [Felsöka problem med anslutningen och replikeringen](#check-connectivity-and-replication).
![Kritiskt][red] | Minnes användning > 95% under de senaste 15 minuterna. | 1. Lägg inte till nya datorer och fundera över att skapa en [ytterligare processerver](vmware-azure-set-up-process-server-scale.md).<br/> 2. Följ eventuella instruktioner som är associerade med varningen.<br/> 3.4. Om problemet kvarstår följer du anvisningarna nedan för att [Felsöka problem med anslutningen och replikeringen](#check-connectivity-and-replication).<br/> 4. om problemet kvarstår kör du [distributions planeraren](./site-recovery-deployment-planner.md) för replikering av VMware/fysisk server.
![Varning][yellow] | Ledigt utrymme i cache-mappen < 30% under de senaste 15 minuterna. | 1. Lägg inte till nya datorer och Överväg att konfigurera en [ytterligare processerver](vmware-azure-set-up-process-server-scale.md).<br/>2. kontrol lera att antalet virtuella datorer som använder processervern överensstämmer med [rikt linjerna](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Följ instruktionerna nedan för att [Felsöka problem med anslutningen och replikeringen](#check-connectivity-and-replication).
![Kritiskt][red] |  Ledigt utrymme < 25% under de senaste 15 minuterna | 1. Följ instruktionerna som är kopplade till varningen för det här problemet.<br/> 2.3. Följ anvisningarna nedan för att [Felsöka problem med anslutningen och replikeringen](#check-connectivity-and-replication).<br/> 3. om problemet kvarstår kör du [distributions planeraren](./site-recovery-deployment-planner.md) för VMware/fysisk server-replikering.
![Kritiskt][red] | Inget pulsslag från processervern i 15 minuter eller mer. Tmansvs-tjänsten kommunicerar inte med konfigurations servern. | 1) kontrol lera att processervern är igång.<br/> 2. kontrol lera att tmassvc körs på processervern.<br/> 3. Följ instruktionerna nedan för att [Felsöka problem med anslutningen och replikeringen](#check-connectivity-and-replication).


![Tabell nyckel](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Steg 2: kontrol lera process Server tjänster

Tjänster som ska köras på processervern sammanfattas i följande tabell. Det finns mindre skillnader i tjänsterna, beroende på hur processervern distribueras. 

För alla tjänster utom Microsoft Azure Recovery Services agent (obengine) kontrollerar du att StartType är inställt på **Automatisk** eller **Automatisk (fördröjd start)**.
 
**Distribution** | **Köra tjänster**
--- | ---
**Processerver på konfigurations servern** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage PushInstall; Logg uppladdnings tjänst (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services agent (obengine); InMage Scout VX agent-Sentinel/utpost (svagents); tmansvc World Wide Web Publishing Service (W3SVC); MySQL Microsoft Azure Site Recovery tjänst (dra)
**Processerver som körs som en fristående server** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage PushInstall; Logg uppladdnings tjänst (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services agent (obengine); InMage Scout VX agent-Sentinel/utpost (svagents); tmansvc.
**Processerver distribuerad i Azure för återställning efter fel** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage PushInstall; Logg uppladdnings tjänst (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Steg 3: kontrol lera pulsslag för processervern

Om det inte finns något pulsslag från processervern (felkod 806) gör du följande:

1. Kontrol lera att den virtuella datorns process Server är igång.
2. Kontrol lera om det finns fel i loggarna.

    C:\ProgramData\ASR\home\svsystems\eventmanager *. log c\programdata\asr\home\svsystems\ monitor_protection*. log

## <a name="check-connectivity-and-replication"></a>Kontrol lera anslutningen och replikeringen

 Inledande och pågående replikeringsfel orsakas ofta av anslutnings problem mellan käll datorer och processervern, eller mellan processervern och Azure. De här stegen sammanfattas i följande bild, följt av procedurer som hjälper dig att utföra stegen.

![Flödes schema som visar steg för att felsöka anslutningar och replikering.](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Steg 4: verifiera tidssynkronisering på käll datorn

Se till att system datum/-tiden för den replikerade datorn är synkroniserad. [Läs mer](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Steg 5: kontrol lera antivirus program på käll datorn

Kontrol lera att inget antivirus program på den replikerade datorn blockerar Site Recovery. Om du behöver utesluta Site Recovery från antivirus program kan du läsa [den här artikeln](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Steg 6: kontrol lera anslutningen från käll datorn


1. Installera [Telnet-klienten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10)) på käll datorn om du behöver. Använd inte ping.
2. Pinga processervern på HTTPS-porten med Telnet från käll datorn. Som standard är 9443 HTTPS-porten för replikeringstrafik.

    `telnet <process server IP address> <port>`

3. Kontrol lera om anslutningen lyckades.


**Anslutningsmöjligheter** | **Information** | **Åtgärd**
--- | --- | ---
**Lyckad** | Telnet visar en tom skärm och processervern kan komma åt den. | Ingen ytterligare åtgärd krävs.
**Misslyckade** | Du kan inte ansluta | Kontrol lera att den inkommande port 9443 är tillåten på processervern. Om du till exempel har ett perimeternätverk eller ett skärmat undernät. Kontrol lera anslutningen igen.
**Delvis genomförd** | Du kan ansluta, men käll datorn rapporterar att processervern inte kan nås. | Fortsätt med nästa fel söknings procedur.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Steg 7: Felsök en processerver som inte kan kontaktas

Om processervern inte går att komma åt från käll datorn visas fel 78186. Om det här problemet inte åtgärdas leder det till att både programkonsekventa och kraschbaserade återställnings punkter inte genereras som förväntat.

Felsök genom att kontrol lera om käll datorn kan komma åt IP-adressen för processervern och köra cxpsclient-verktyget på käll datorn för att kontrol lera slut punkt till slut punkt-anslutningen.


### <a name="check-the-ip-connection-on-the-process-server"></a>Kontrol lera IP-anslutningen på processervern

Om Telnet lyckas, men käll datorn rapporterar att processervern inte går att nå, kontrollerar du om du kan nå IP-adressen för processervern.

1. I en webbläsare försöker du komma åt IP-https://<PS_IP>: <PS_Data_Port>/.
2. Om den här kontrollen visar ett HTTPS-certifikat fel, är det normalt. Om du ignorerar felet bör du se en 400-Felaktig begäran. Det innebär att servern inte kan hantera webb läsar förfrågan och att standard-HTTPS-anslutningen är korrekt.
3. Om den här kontrollen inte fungerar noterar du webb läsar fel meddelandet. Ett 407-fel indikerar till exempel ett problem med proxyautentisering.

### <a name="check-the-connection-with-cxpsclient"></a>Kontrol lera anslutningen med cxpsclient

Dessutom kan du köra verktyget cxpsclient för att kontrol lera slut punkt till slut punkt-anslutningen.

1. Kör verktyget enligt följande:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. På processervern kontrollerar du de genererade loggarna i följande mappar:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Kontrol lera de virtuella käll dator loggarna för överförings fel (fel 78028)

Problem med data överföringar som blockeras från käll datorer till process tjänsten kan leda till att både kraschbaserade och programkonsekventa återställnings punkter inte genereras. 

1. Om du vill felsöka problem med nätverks uppladdning kan du söka efter fel i den här loggen:

    C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents *. log 

2. Använd resten av procedurerna i den här artikeln för att lösa problem med data uppladdning.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Steg 8: kontrol lera om processervern skickar data

Kontrol lera om processervern aktivt skickar data till Azure.

  1. Öppna aktivitets hanteraren på processervern (tryck på Ctrl + Shift + Esc).
  2. Välj fliken **prestanda** > **öppna resursövervakare**.
  3. På sidan **resursövervakare** väljer du fliken **nätverk** . Under **processer med nätverks aktivitet** kontrollerar du om cbengine.exe aktivt skickar en stor mängd data.

       ![Skärm bild som visar ett stort antal volymer under processer med nätverks aktivitet.](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Om cbengine.exe inte skickar en stor mängd data, slutför du stegen i följande avsnitt.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Steg 9: kontrol lera anslutningen till processervern i Azure Blob Storage

1. I resursövervakare väljer du **cbengine.exe**.
2. Under **TCP-anslutningar** kontrollerar du om det finns en anslutning från processervern till Azure Storage.

  ![Skärm bild som visar anslutningen mellan cbengine.exe och Azure Blob Storage-URL: en.](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Kontrol lera tjänster

Om det inte finns någon anslutning från processervern till Azure Blob Storage-URL: en, kontrollerar du att tjänsterna körs.

1. I kontroll panelen väljer du **tjänster**.
2. Kontrol lera att följande tjänster körs:

    - cxprocessserver
    - InMage Scout VX agent – Sentinel/utpost
    - Microsoft Azure Recovery Services agent
    - Microsoft Azure Site Recovery tjänst
    - tmansvc

3. Starta eller starta om alla tjänster som inte körs.
4. Kontrol lera att processervern är ansluten och kan kontaktas. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Steg 10: kontrol lera anslutningen till processervern för Azures offentliga IP-adress

1. Öppna den senaste CBEngineCurr. errlog-filen i **%program%\Microsoft Azure Recovery Services Agent\Temp** på processervern.
2. I filen söker du efter **443** eller för **anslutnings försöket misslyckades**.

  ![Fel loggar i Temp-mappen](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Om du ser problem hittar du din offentliga Azure-IP-adress i filen CBEngineCurr. currLog genom att använda port 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. På kommando raden på processervern använder du Telnet för att pinga din offentliga Azure-IP-adress.
6. Om du inte kan ansluta, Följ nästa procedur.

## <a name="step-11-check-process-server-firewall-settings"></a>Steg 11: kontrol lera brand Väggs inställningarna för process Server. 

Kontrol lera om den IP-adressbaserade brand väggen på processervern blockerar åtkomsten.

1. För IP-adressbaserade brand Väggs regler:

    a) Ladda ned den fullständiga listan över [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Lägg till IP-adressintervall i brand Väggs konfigurationen för att säkerställa att brand väggen tillåter kommunikation till Azure (och till standard-HTTPS-porten 443).

    c) Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för regionen Azure västra USA (används för åtkomst kontroll och identitets hantering).

2. För URL-baserade brand väggar lägger du till URL: erna som anges i följande tabell i brand Väggs konfigurationen.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Steg 12: Verifiera inställningar för processerver 

1. Om du använder en proxyserver måste du kontrol lera att namnet på proxyservern har matchats av DNS-servern. Kontrol lera värdet som du angav när du konfigurerade konfigurations servern i register nyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Se till att samma inställningar används av den Azure Site Recovery agenten för att skicka data.

    a) Sök efter **Microsoft Azure Backup**.

    b) öppna **Microsoft Azure Backup** och välj Egenskaper för **Åtgärds**  >  **ändring**.

    c) på fliken **proxykonfiguration** ska adressen vara samma som den proxyadress som visas i register inställningarna. Om inte, ändra den till samma adress.

## <a name="step-13-check-bandwidth"></a>Steg 13: kontrol lera bandbredd

Öka bandbredden mellan processervern och Azure och kontrol lera sedan om problemet fortfarande uppstår.


## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du publicera din fråga på [sidan Microsoft Q&en fråga för Azure Site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png