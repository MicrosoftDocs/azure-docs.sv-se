---
title: Hantera en processerver för haveri beredskap för virtuella VMware-datorer/fysiska servrar i Azure Site Recovery
description: I den här artikeln beskrivs hantera en processerver för haveri beredskap för virtuella VMware-datorer/fysiska servrar med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019213"
---
# <a name="manage-process-servers"></a>Hantera processervrar

I den här artikeln beskrivs vanliga åtgärder för att hantera Site Recovery processervern.

Processervern används för att ta emot, optimera och skicka replikeringsdata till Azure. Den utför också en push-installation av mobilitets tjänsten på virtuella VMware-datorer och fysiska servrar som du vill replikera och utför automatisk identifiering av lokala datorer. För replikering av lokala virtuella VMware-datorer eller fysiska servrar till Azure installeras processervern som standard på konfigurations servern. 

- För stora distributioner kan du behöva ytterligare lokala processervern för att skala kapaciteten.
- För återställning efter fel från Azure till lokalt måste du konfigurera en tillfällig processerver i Azure. Du kan ta bort den här virtuella datorn när återställning efter fel är slutförd. 

Läs mer om processervern.


## <a name="upgrade-a-process-server"></a>Uppgradera en processerver

När du distribuerar en processerver lokalt, eller som en virtuell Azure-dator för återställning efter fel, installeras den senaste versionen av processervern. Site Recovery Teams versions korrigeringar och förbättringar regelbundet, och vi rekommenderar att du håller process servrarna uppdaterade. Du kan uppgradera en processerver på följande sätt:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Flytta virtuella datorer för att utjämna belastningen på processervern

Utjämna belastningen genom att flytta virtuella datorer mellan två process servrar enligt följande:

1. I valvet under **Hantera** klickar du på **Site Recovery infrastruktur**. Under **för fysiska VMware & fysiska datorer** klickar du på **konfigurations servrar**.
2. Klicka på den konfigurations server som process servrarna är registrerade på.
3. Klicka på den processerver som du vill belastningsutjämna trafik för.

    ![Skärm bild som visar en Processerver för vilken du kan belastningsutjämna trafik.](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klicka på **belastnings utjämning** och välj den mål process server som du vill flytta datorer till. Klicka sedan på **OK**

    ![Skärm bild som visar fönstret belastnings utjämning med Välj mål process Server vald.](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klicka på **Välj datorer** och välj de datorer som du vill flytta från den aktuella till mål process servern. Information om genomsnittlig data ändring visas för varje virtuell dator. Klicka sedan på **OK**. 
3. I valvet övervakar du förloppet för jobbet under **övervakning**  >  **Site Recovery jobb**.

Det tar ungefär 15 minuter innan ändringarna visas i portalen. [Uppdatera konfigurations servern](vmware-azure-manage-configuration-server.md#refresh-configuration-server)för en snabbare inverkan.

## <a name="switch-an-entire-workload-to-another-process-server"></a>Växla en hel arbets belastning till en annan processerver

Flytta hela arbets belastningen som hanteras av en processerver till en annan processerver enligt följande:

1. I valvet under **Hantera** klickar du på **Site Recovery infrastruktur**. Under **för fysiska VMware & fysiska datorer** klickar du på **konfigurations servrar**.
2. Klicka på den konfigurations server som process servrarna är registrerade på.
3. Klicka på den processerver som du vill byta arbets belastningen från.
4. Klicka på **växel** och välj den mål process server som du vill flytta arbets belastningen till. Klicka sedan på **OK**

    ![Skärm bild som visar fönstret Välj mål process Server.](media/vmware-azure-manage-process-server/Switch.PNG)

5. I valvet övervakar du förloppet för jobbet under **övervakning**  >  **Site Recovery jobb**.

Det tar ungefär 15 minuter innan ändringarna visas i portalen. [Uppdatera konfigurations servern](vmware-azure-manage-configuration-server.md#refresh-configuration-server)för en snabbare inverkan.

## <a name="register-a-master-target-server"></a>Registrera en huvud mål Server

Huvud mål servern finns på konfigurations servern och de skalbara process servrarna. Det måste registreras med konfigurations servern. Om registreringen Miss lyckas kan det påverka hälsan hos skyddade objekt. Om du vill registrera huvud mål servern med konfigurations servern loggar du in på den specifika konfigurations servern/den skalbara processervern där registreringen krävs. Gå till mappen **%programdata%\ASR\Agent** och kör följande på administratörs kommando tolken.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Registrera om en processerver

Registrera om en processerver som körs lokalt eller på en virtuell Azure-dator med konfigurations servern enligt följande:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

När du har sparat inställningarna gör du följande:

1. Öppna en administratörs kommando tolk på processervern.
2. Bläddra till mappen **%programdata%\ASR\Agent** och kör kommandot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Ändra proxyinställningar för en lokal processerver

Om en lokal processerver använder en proxyserver för att ansluta till Azure, kan du ändra inställningarna för proxy enligt följande:

1. Logga in på processervern. 
2. Öppna kommando fönstret admin PowerShell och kör följande kommando:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Bläddra till mappen **%programdata%\ASR\Agent** och kör följande kommando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Ta bort en processerver

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Undanta mappar från antivirus program

Om antivirus program körs på en skalbar processerver (eller en huvud mål server) utesluter du följande mappar från Antivirus åtgärder:


- C:\Program\Microsoft Azure Recovery Services agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Installations katalog för processerver. Exempel: C:\Program Files (x86) \Microsoft Azure Site Recovery