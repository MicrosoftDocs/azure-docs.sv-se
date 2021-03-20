---
title: Migrera Windows Server 2008-servrar till Azure med Azure Migrate/Site Recovery
description: I den här artikeln beskrivs hur du migrerar lokala Windows Server 2008-datorer till Azure och rekommenderar Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6cc0855d3a4540de780a566a4613b4dbc647cfc5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369496"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Migrera servrar som kör Windows Server 2008 till Azure

Den här självstudien visar hur du migrerar lokala servrar som kör Windows Server 2008 eller 2008 R2 till Azure med hjälp av Azure Site Recovery. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbered din lokala miljö för migrering.
> * Konfigurera målmiljön.
> * Konfigurera en replikeringsprincip.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Redundansväxla till Azure och Slutför migreringen.

## <a name="migrate-with-azure-migrate"></a>Migrera med Azure Migrate

Vi rekommenderar att du migrerar datorer till Azure med hjälp av tjänsten [Azure Migrate](../migrate/migrate-services-overview.md) . 

- Azure Migrate är syftet – byggd för Server-migrering.
- Azure Migrate tillhandahåller en central hubb för identifiering, utvärdering och migrering av lokala datorer till Azure. Azure Site Recovery bör endast användas för haveri beredskap och inte migrering.
- Azure Migrate stöder migrering av servrar som kör Windows Server 2008.


## <a name="migrate-with-site-recovery"></a>Migrera med Site Recovery

### <a name="supported-operating-systems"></a>Operativsystem som stöds


|Operativsystem  | Miljö  |
|---------|---------|
|Windows Server 2008 SP2 – 32- och 64-bitars (IA-32 och x86-64)</br>– Standard</br>– Enterprise</br>– Datacenter   |     VMware-VM, Hyper-V-VM och fysiska servrar    |
|Windows Server 2008 R2 SP1 – 64-bitars</br>– Standard</br>– Enterprise</br>– Datacenter     |     VMware-VM, Hyper-V-VM och fysiska servrar|

> [!WARNING]
> - Migrering av servrar som kör Server Core stöds inte.
> - Se till att du har senaste service pack och Windows-uppdateringar installerade innan du migrerar.


### <a name="prerequisites"></a>Förutsättningar

Innan du börjar är det bra att granska Azure Site Recovery arkitekturen för migrering av [VMware och fysisk server](vmware-azure-architecture.md) eller [migrering av virtuella Hyper-V-datorer](hyper-v-azure-architecture.md) 

Om du vill migrera virtuella Hyper-V-datorer som kör Windows Server 2008 eller Windows Server 2008 R2 följer du stegen i självstudiekursen om [migrering av lokala datorer till Azure](migrate-tutorial-on-premises-azure.md).

Resten av den här självstudiekursen visar hur du kan migrera lokala virtuella VMware-datorer och fysiska servrar som kör Windows Server 2008 eller 2008 R2.
> [!TIP]
> Letar du efter ett agent effektivt sätt att migrera virtuella VMware-datorer till Azure? [Klicka här](../migrate/tutorial-migrate-vmware.md)


### <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

- Konfigurationsservern, ytterligare processervrar och mobilitetstjänsten som används för att migrera Windows Server 2008 SP2-servrar ska köra version 9.19.0.0 eller senare av Azure Site Recovery-programvaran.

- Programkonsekventa återställningspunkter och konsekvensfunktionen för flera virtuella datorer stöds inte replikering av servrar som kör Windows Server 2008 SP2. Windows Server 2008 SP2-servrar ska migreras till en kraschkonsekvent återställningspunkt. Kraschkonsekventa återställningspunkter genereras var 5:e minut som standard. Om du använder en replikeringsprincip med ett konfigurerad programkonsekvent ögonblicksbildsfrekvens gör så att replikeringsstatusen blir kritisk på grund av bristen på programkonsekventa återställningspunkter. Om du vill undvika falska positiva identifieringar ställer du in den programkonsekventa ögonblicksbildfrekvensen i replikeringsprincipen på ”Av”.

- Servrarna som migreras ska ha .NET Framework 3.5 Service Pack 1 för at mobilitetstjänsten ska fungera.

- Om servern har dynamiska diskar kan du märka i vissa konfigurationer att dessa diskar på den rendundansväxlade servern är markerade offline eller visas som externa diskar. Du kan även märka att speglade set-statusen för speglade volymer på dynamiska diskar markeras ”Redundansåtgärden misslyckades”. Du kan åtgärda problemet från diskmgmt.msc genom att manuellt importera dessa diskar och återaktivera dem.

- Servrarna som migreras ska ha drivrutinen vmstorfl.sys. Redundansen kan misslyckas om drivrutin inte finns på servern som migreras. 
  > [!TIP]
  >Kontrollera om drivrutinen finns på "C:\Windows\system32\drivers\vmstorfl.sys" . Om drivrutinen inte hittas kan du lösa problemet genom att skapa en dummy-fil på plats. 
  >
  > Öppna kommandotolken (run > cmd) och kör följande: "copy nul c:\Windows\system32\drivers\vmstorfl.sys"

- Du kanske inte kan RDP-ansluta till Windows Server 2008 SP2-servrar som kör 32-bitars operativsystemet när de har redundansväxlat eller testredundansväxlat över till Azure. Starta om den redundansväxlade virtuella datorn via Azure-portalen och försök ansluta igen. Om du fortfarande inte kan ansluta kontrollerar du om servern har konfigurerats att tillåta fjärrskrivbordsanslutningar och att det inte finns några brandväggsregler eller nätverkssäkerhetsgrupper som blockerar anslutningen. 
  > [!TIP]
  > Ett redundanstest rekommenderas starkt innan du migrerar servrar. Se till att du har utfört minst ett lyckat redundanstest på varje server du migrerar. Som en del av redundanstestet ansluter du till den testredundansväxlade datorn och kontrollera att allt fungerar som förväntat.
  >
  >Redundanstestet är avbrottsfritt och hjälper dig att testa migreringar genom att skapa virtuella datorer på ett valfritt isolerat nätverk. Till skillnad från redundansåtgärden fortsätter datareplikeringen under ett redundanstest. Du kan utföra så många redundanstest du vill innan du är redo att migrera. 
  >
  


### <a name="get-started"></a>Kom igång

Utför följande uppgifter för att förbereda Azure-prenumerationen och den lokala VMware/fysiska miljön:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. Förbered lokal [VMware](vmware-azure-tutorial-prepare-on-premises.md)


### <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. Logga in på [Azure Portal](https://portal.azure.com)  >  **Recovery Services**.
2. Klicka på **skapa ett resurs**  >  **hanterings verktyg**  >  **säkerhets kopiering och Site Recovery**.
3. I **Namn** anger du det egna namnet **W2K8-migrering**. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp, **w2k8migrate**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För att snabbt komma åt valvet från instrumentpanelen klickar du på **Fäst på instrumentpanelen** och sedan på **Skapa**.

   ![Skärm bild som visar nya alternativ för att skapa valv.](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.


### <a name="prepare-your-on-premises-environment-for-migration"></a>Förbereda din lokala miljö för migrering

- Om du vill migrera virtuella Windows Server 2008-datorer som körs på VMware [konfigurera du den lokala konfigurationsservern på VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Om konfigurationsservern inte kan konfigureras som en virtuell VMware-dator [konfigurerar du konfigurationsservern på en lokal fysisk server eller virtuell dator](physical-azure-disaster-recovery.md#set-up-the-source-environment).

### <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur**  >  **mål** och välj den Azure-prenumeration som du vill använda.
2. Ange Resource Manager-distributionsmodellen.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


### <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Om du vill skapa en ny replikeringsprincip klickar du på Site Recovery principer för **infrastruktur**  >  **replikering**  >  **+ replikeringsprincip**.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO). En avisering skapas om replikeringens RPO överskrider gränsen.
4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Replikerade servrar kan återställas till valfri punkt i det här fönstret. Upp till 24 timmars kvarhållning stöds för datorer replikerade till premiumlagring och 72 timmar för standardlagring.
5. I **Frekvens för appkonsekvent ögonblicksbild** anger du **Av**. Klicka på **OK** för att skapa principen.

Principen associeras automatiskt med konfigurationsservern.

> [!WARNING]
> Se till att du anger **AV** i inställningen Frekvens för appkonsekvent ögonblicksbild för replikeringsprincipen. Endast kraschkonsekventa återställningspunkter stöds vid replikering av servrar som kör Windows Server 2008. Om du anger ett annat värde i Frekvens för appkonsekvent ögonblicksbild resulterar i falska aviseringar genom replikeringsstatusen för servern blir kritisk på grund av bristen på appkonsekventa återställningspunkter.

   ![Skärm bild som visar alternativ för att skapa replikeringsprincipen.](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>Aktivera replikering

[Aktivera replikering](physical-azure-disaster-recovery.md#enable-replication) för Windows Server 2008 SP2-/Windows Server 2008 R2 SP1-servern som ska migreras.
   
   ![Skärm bild som visar alternativ för att lägga till fysiska datorer.](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Skärm bild som visar alternativ för att aktivera replikering.](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>Kör en testmigrering

Du kan utföra ett redundanstest för servrar som ska replikeras när den inledande replikeringen slutförs och serverstatusen övergår till **Skyddad**.

Kör en [testredundansväxling](tutorial-dr-drill-azure.md) till Azure för att kontrollera att allt fungerar som förväntat.

   ![Skärm bild som visar kommandot testa redundans.](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>Migrera till Azure

Kör en redundansväxling för de datorer som du vill migrera.

1. I **Inställningar**  >  **replikerade objekt** klickar du på datorn > **redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Välj den senaste återställningspunkten.
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av servern innan redundansväxlingen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundansväxlingen på **jobb** sidan.
4. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
5. I **Replikerade objekt** högerklickar du på servern > **Slutför migrering**. Det här gör följande:

    - Avslutar migreringsprocessen, stoppar replikeringen för servern och stoppar Site Recovery-debitering för servern.
    - Det här steget rensar replikeringsdata. Men det raderar inte de migrerade virtuella datorerna.

   ![Skärm bild som visar kommandot fullständig migrering.](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Server replikering stoppas innan redundansväxlingen startar. Om du avbryter en pågående redundans stoppas redundansen, men serverreplikeringen fortsätter inte att replikeras.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Läs vanliga frågor](../migrate/resources-faq.md) om Azure Migrate.