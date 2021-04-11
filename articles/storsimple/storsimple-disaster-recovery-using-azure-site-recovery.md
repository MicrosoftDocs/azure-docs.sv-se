---
title: Automatisera StorSimple-fileshare DR med Azure Site Recovery | Microsoft Docs
description: Beskriver stegen och bästa praxis för att skapa en katastrof återställnings lösning för fil resurser som finns på Microsoft Azure StorSimple Storage.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: c6152d4b9ee28554efcb5b08b7a2d161a0723852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670912"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatiserad katastrof återställnings lösning med Azure Site Recovery för fil resurser som finns på StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
Microsoft Azure StorSimple är en hybrid moln lagrings lösning som hanterar de komplexa data som är ofta kopplade till fil resurser. StorSimple använder moln lagring som en förlängning av den lokala lösningen och data skiktas automatiskt i lokal lagring och moln lagring. Integrerat data skydd med lokala och molnbaserade ögonblicks bilder, eliminerar behovet av en Sprawling lagrings infrastruktur.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) är en Azure-baserad tjänst som tillhandahåller funktioner för haveri beredskap (Dr) genom att dirigera replikering, redundans och återställning av virtuella datorer. Azure Site Recovery har stöd för ett antal olika replikerings tekniker för att konsekvent replikera, skydda och sömlöst redundansväxla virtuella datorer och program till privata/offentliga eller värdbaserade moln.

Med hjälp av Azure Site Recovery, replikering av virtuella datorer och StorSimple-funktioner i molnet kan du skydda den kompletta fil Server miljön. I händelse av ett avbrott kan du använda ett enda klick för att ta dina fil resurser online i Azure på bara några minuter.

Det här dokumentet beskriver i detalj hur du kan skapa en katastrof återställnings lösning för dina fil resurser som finns på StorSimple-lagring och utföra planerade, oplanerat och redundanstest med en återställnings plan med ett klick. I grunden visar det hur du kan ändra återställnings planen i Azure Site Recovery-valvet för att aktivera StorSimple redundans under katastrof scenarier. Dessutom beskrivs konfigurationer och krav som stöds. Det här dokumentet förutsätter att du är bekant med grunderna i Azure Site Recovery-och StorSimple-arkitekturer.

## <a name="supported-azure-site-recovery-deployment-options"></a>Azure Site Recovery distributions alternativ som stöds
Kunder kan distribuera fil servrar som fysiska servrar eller virtuella datorer som körs på Hyper-V eller VMware och sedan skapa fil resurser från volymer hämtas out of StorSimple Storage. Azure Site Recovery kan skydda både fysiska och virtuella distributioner till antingen en sekundär plats eller till Azure. Det här dokumentet beskriver information om en DR-lösning med Azure som återställnings plats för en virtuell fil server som finns på Hyper-V och med fil resurser på StorSimple-lagring. Andra scenarier där fil serverns virtuella dator finns på en virtuell VMware-dator eller en fysisk dator kan implementeras på samma sätt.

## <a name="prerequisites"></a>Förutsättningar
Att implementera en lösning för haveri beredskap med en klickning som använder Azure Site Recovery för fil resurser som finns på StorSimple-lagringen har följande krav:

   - Lokal Windows Server 2012 R2-baserad virtuell dator på Hyper-V eller VMware eller en fysisk dator
   - StorSimple lagrings enhet lokalt registrerad med Azure StorSimple Manager
   - StorSimple Cloud Appliance som skapats i Azure StorSimple Manager. Installationen kan behållas i avstängnings tillstånd.
   - Fil resurser som finns på de volymer som kon figurer ATS på StorSimple lagrings enhet
   - [Azure Site Recovery Services-valvet](../site-recovery/hyper-v-vmm-azure-tutorial.md) har skapats i en Microsoft Azure-prenumeration

Om Azure är din återställnings plats kör du dessutom [verktyget för beredskap för virtuella Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) -datorer på virtuella datorer för att säkerställa att de är kompatibla med virtuella Azure-datorer och Azure Site Recovery-tjänster.

Se till att du skapar StorSimple Cloud Appliance, Automation-konto och lagrings konto i samma region för att undvika latens problem (vilket kan leda till högre kostnader).

## <a name="enable-dr-for-storsimple-file-shares"></a>Aktivera DR för StorSimple fil resurser
Varje komponent i den lokala miljön måste skyddas för att aktivera fullständig replikering och återställning. I det här avsnittet beskrivs hur du:
    
   - Konfigurera Active Directory och DNS-replikering (valfritt)
   - Använd Azure Site Recovery för att aktivera skydd av den virtuella fil servern
   - Aktivera skydd av StorSimple-volymer
   - Konfigurera nätverket

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurera Active Directory och DNS-replikering (valfritt)
Om du vill skydda datorerna som kör Active Directory och DNS så att de är tillgängliga på DR-platsen måste du uttryckligen skydda dem (så att fil servrarna är tillgängliga efter redundansväxlingen med autentisering). Det finns två rekommenderade alternativ beroende på hur komplex kundens lokala miljö är.

#### <a name="option-1"></a>Alternativ 1
Om kunden har ett litet antal program, en enda domänkontrollant för hela den lokala platsen och kommer att Miss sen över hela platsen, rekommenderar vi att du använder Azure Site Recovery replikering för att replikera domänkontrollantens dator till en sekundär plats (Detta gäller både för plats-till-plats och plats-till-Azure).

#### <a name="option-2"></a>Alternativ 2
Om kunden har ett stort antal program, kör en Active Directory skog och kommer att Miss par över några program i taget, rekommenderar vi att du konfigurerar ytterligare en domänkontrollant på DR-platsen (antingen en sekundär plats eller i Azure).

Läs den [automatiserade Dr-lösningen för Active Directory och DNS med Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) för instruktioner när du gör en domänkontrollant tillgänglig på Dr-webbplatsen. För resten av det här dokumentet förutsätter vi att en domänkontrollant är tillgänglig på DR-webbplatsen.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Använd Azure Site Recovery för att aktivera skydd av den virtuella fil servern
Det här steget kräver att du förbereder den lokala fil Server miljön, skapar och förbereder ett Azure Site Recovery valv och aktiverar fil skydd för den virtuella datorn.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Så här förbereder du den lokala fil Server miljön
1. Ange **User Account Control** att **aldrig meddela**. Detta krävs för att du ska kunna använda Azure Automation-skript för att ansluta iSCSI-målen efter redundansväxlingen genom att Azure Site Recovery.
   
   1. Tryck på Windows-tangenten + Q och Sök efter **UAC**.  
   1. Välj **ändra User Account Control inställningar**.  
   1. Dra fältet till längst ned mot **Meddela aldrig**.  
   1. Klicka på **OK** och välj sedan **Ja** när du uppmanas att göra det.  
   
      ![User Account Control inställningar](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Installera VM-agenten på alla virtuella datorer i fil servern. Detta krävs för att du ska kunna köra Azure Automation-skript på misslyckade virtuella datorer.
   
   1. [Ladda ned agenten](https://aka.ms/vmagentwin) till `C:\\Users\\<username>\\Downloads` .
   1. Öppna Windows PowerShell i administratörs läge (kör som administratör) och ange följande kommando för att navigera till nedladdnings platsen:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Fil namnet kan ändras beroende på version.
      
1. Klicka på **Nästa**.
1. Godkänn **villkoren i avtalet** och klicka sedan på **Nästa**.
1. Klicka på **Finish**.
1. Skapa fil resurser med hjälp av volymer hämtas out of StorSimple Storage. Mer information finns i [använda tjänsten StorSimple Manager för att hantera volymer](./index.yml).
   
   1. På dina lokala virtuella datorer trycker du på Windows-tangenten + Q och söker efter **iSCSI**.
   1. Välj **iSCSI-initierare**.
   1. Välj fliken **konfiguration** och kopiera initierarens namn.
   1. Logga in på [Azure Portal](https://portal.azure.com/).
   1. Välj fliken **StorSimple** och välj sedan den StorSimple Manager tjänst som innehåller den fysiska enheten.
   1. Skapa volym containrar och skapa sedan volym (er). (Dessa volymer är för fil resurserna på fil serverns virtuella datorer). Kopiera initierarens namn och ange ett lämpligt namn för Access Control poster när du skapar volymerna.
   1. Välj fliken **Konfigurera** och anteckna enhetens IP-adress.
   1. På dina lokala virtuella datorer går du till iSCSI- **initieraren** igen och anger IP i avsnittet snabb anslutning. Klicka på **snabb anslutning** (enheten bör nu vara ansluten).
   1. Öppna Azure Portal och välj fliken **volymer och enheter** . Klicka på **Konfigurera automatiskt**. Volymen som du skapade bör visas.
   1. Välj fliken **enheter** i portalen och välj sedan **skapa en ny virtuell enhet.** (Den här virtuella enheten kommer att användas om en redundansväxling inträffar). Den här nya virtuella enheten kan försättas i offline-tillstånd för att undvika extra kostnader. Om du vill koppla från den virtuella enheten går du till avsnittet **Virtual Machines** på portalen och stänger den.
   1. Gå tillbaka till lokala virtuella datorer och öppna disk hantering (tryck på Windows-tangenten + X och välj **disk hantering**).
   1. Du kommer att märka vissa extra diskar (beroende på antalet volymer som du har skapat). Högerklicka på den första, Välj **initiera disk** och välj **OK**. Högerklicka på avsnittet inte **tilldelat** , Välj **Ny enkel volym**, tilldela en enhets beteckning och Slutför guiden.
   1. Upprepa steg l för alla diskar. Nu kan du se alla diskar på **den här datorn** i Utforskaren.
   1. Använd rollen fil-och lagrings tjänster för att skapa fil resurser på dessa volymer.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Skapa och förbereda ett Azure Site Recovery-valv
Läs Azure Site Recovery- [dokumentationen](../site-recovery/index.yml) för att komma igång med Azure Site Recovery innan du skyddar den virtuella fil servern.

#### <a name="to-enable-protection"></a>Aktivera skydd
1. Koppla från iSCSI-målen från de lokala virtuella datorer som du vill skydda genom att Azure Site Recovery:
   
   1. Tryck på Windows-tangenten + Q och Sök efter **iSCSI**.
   1. Välj **Konfigurera iSCSI-initierare**.
   1. Koppla från StorSimple-enheten som du anslöt tidigare. Alternativt kan du stänga av fil servern i några minuter när skyddet aktive ras.
      
   > [!NOTE]
   > Detta gör att fil resurserna är tillfälligt otillgängliga.
   
1. [Aktivera skydd av virtuell dator](../site-recovery/hyper-v-azure-tutorial.md) för den virtuella fil serverdatorn från Azure Site Recovery-portalen.
1. När den första synkroniseringen börjar kan du återansluta målet igen. Gå till iSCSI-initieraren, Välj StorSimple-enheten och klicka på **Anslut**.
1. När synkroniseringen är klar och statusen för den virtuella datorn är **skyddad** väljer du den virtuella datorn, väljer fliken **Konfigurera** och uppdaterar nätverket för den virtuella datorn i enlighet med detta (det nätverk som det misslyckade över VM: ar kommer att ingå i). Om nätverket inte visas innebär det att synkroniseringen fortfarande pågår.

### <a name="enable-protection-of-storsimple-volumes"></a>Aktivera skydd av StorSimple-volymer
Om du inte har valt alternativet **Aktivera en standard säkerhets kopiering för den här volymen** för StorSimple-volymerna går du till **säkerhets kopierings principer** i StorSimple managers Tjänsten och skapar en lämplig säkerhets kopierings princip för alla volymer. Vi rekommenderar att du anger frekvensen för säkerhets kopieringar till det återställnings punkt mål (återställnings punkt mål) som du vill se för programmet.

### <a name="configure-the-network"></a>Konfigurera nätverket
Konfigurera nätverks inställningar i Azure Site Recovery så att de virtuella dator nätverken är kopplade till rätt DR-nätverk efter redundans för den virtuella fil servern.

Du kan välja den virtuella datorn på fliken **replikerade objekt** för att konfigurera nätverks inställningarna, som du ser i följande bild.

![Beräkning och nätverk](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
Du kan skapa en återställnings plan i ASR för att automatisera växlings processen för fil resurserna. Om ett avbrott inträffar kan du ta med fil resurserna på några minuter med bara ett enda klick. Om du vill aktivera den här automatiseringen behöver du ett Azure Automation-konto.

#### <a name="to-create-an-automation-account"></a>Skapa ett Automation-konto
1. Gå till avsnittet Azure Portal &gt; **Automation** .
1. Klicka på **+ Lägg till** knappen, öppnas under bladet.
   
   ![Lägga till ett Automation-konto](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Namn – Ange ett nytt Automation-konto
   - Prenumeration – Välj prenumeration
   - Resurs grupp – skapa ny/Välj en befintlig resurs grupp
   - Plats – Välj plats, behåll den i samma geo/region där StorSimple Cloud Appliance-och lagrings kontona skapades.
   - Skapa Kör som-konto i Azure – Välj **Ja** -alternativ.
   
1. Gå till Automation-kontot och klicka på **Runbooks** &gt; **Bläddra i Galleri** för att importera alla Runbook-flöden som krävs till Automation-kontot.
1. Lägg till följande runbooks genom att hitta **Disaster Recovery** -tag gen i galleriet:
   
   - Rensa upp StorSimple-volymer efter redundanstest (TFO)
   - StorSimple volym behållare
   - Montera volymer på StorSimple-enheten efter redundans
   - Avinstallera anpassat skript tillägg i virtuell Azure-dator
   - Starta StorSimple Virtual installation
   
      ![Bläddra i galleriet](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Publicera alla skript genom att välja Runbook i Automation-kontot och klicka på **Redigera** &gt; **publicera** och sedan på **Ja** till verifierings meddelandet. Efter det här steget ser fliken **Runbooks** ut så här:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. I Automation-kontot klickar du på **variabler** &gt; **Lägg till en variabel** och lägger till följande variabler. Du kan välja att kryptera dessa till gångar. Dessa variabler är en unik återställnings plan. Om din återställnings plan, som du kommer att skapa i nästa steg, är TestPlan, så bör variablerna vara TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName och så vidare.

   - **BaseUrl**: Resource Manager-URL för Azure-molnet. Hämta med **Get-AzEnvironment | Select-Object namn, ResourceManagerUrl-** cmdlet.
   - _RecoveryPlanName_**– ResourceGroupName**: Resource Manager-gruppen som har resursen StorSimple.
   - _RecoveryPlanName_**-ManagerName**: den StorSimple-resurs som har StorSimple-enheten.
   - _RecoveryPlanName_**-enhets** namn: den StorSimple-enhet som måste växlas över.
   - _RecoveryPlanName_**-DeviceIpAddress**: enhetens IP-adress (detta finns på fliken **enheter** under StorSimple Enhetshanteraren avsnittet nätverks inställningar för &gt;  &gt;  &gt; **DNS-inställningar** ).
   - _RecoveryPlanName_**-VolumeContainers**: en kommaavgränsad sträng med volym behållare som finns på den enhet som måste redundansväxla. till exempel: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: StorSimple Cloud Appliance som behållarna ska redundansväxla till.
   - _RecoveryPlanName_**-TargetDeviceIpAddress**: IP-adressen för mål enheten (detta finns på  &gt; fliken nätverks **inställnings** grupp i avsnittet virtuell dator &gt;  ).
   - _RecoveryPlanName_**-StorageAccountName**: namnet på det lagrings konto där skriptet (som måste köras på den virtuella datorn som har redundansväxlats) kommer att lagras. Det kan vara valfritt lagrings konto som har lite utrymme för att lagra skriptet tillfälligt.
   - _RecoveryPlanName_**– StorageAccountKey**: åtkomst nyckeln för lagrings kontot ovan.
   - _RecoveryPlanName_**-VMGUIDS**: när du skyddar en virtuell dator tilldelar Azure Site Recovery varje virtuell dator ett unikt ID som ger information om den misslyckade virtuella datorn. Hämta VMGUID genom att välja fliken **Recovery Services** och klicka på **skyddade objekt** &gt; **skydds grupper** &gt;  &gt; **Egenskaper** för datorer. Om du har flera virtuella datorer lägger du till GUID som en kommaavgränsad sträng.

     Om namnet på återställnings planen till exempel är fileServerpredayRP, ska fliken **variabler**, **anslutningar** och **certifikat** visas på följande sätt när du har lagt till alla till gångar.

      ![Tillgångar](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Ladda upp Runbook-modulen StorSimple 8000-serien i ditt Automation-konto. Använd stegen nedan för att lägga till en modul:
   
   1. Öppna PowerShell, skapa en ny mapp & ändra katalogen till mappen.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Hämta NuGet CLI under samma mapp i steg 1.
      Det finns olika versioner av nuget.exe på [NuGet-nedladdningar](https://www.nuget.org/downloads). Varje nedladdnings länk pekar direkt till en. exe-fil, så se till att högerklicka och spara filen på datorn i stället för att köra den från webbläsaren.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Ladda ned den beroende SDK: n
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Skapa en Azure Automation Runbook-modul för enhets hantering i StorSimple 8000-serien. Använd kommandona nedan för att skapa en zip-fil för Automation-modulen.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Importera zip-filen för Azure Automation modul (Microsoft.Azure.Management.StorSimple8000Series.zip) som skapades i ovan-steget. Det kan du göra genom att välja Automation-kontot, klicka på **moduler** under delade resurser och sedan klicka på **Lägg till en modul**.
   
   När du har importerat modulen StorSimple 8000-serien bör fliken **moduler** visas på följande sätt:
   
      ![Moduler](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Gå till avsnittet **Recovery Services** och välj det Azure Site Recovery-valv som du skapade tidigare.
1. Välj alternativet **återställnings planer (Site Recovery)** från **Hantera** grupp och skapa en ny återställnings plan enligt följande:
   
   - Klicka på + knappen för att **återställa plan** , öppnas under bladet.
      
      ![Skapa en återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Ange ett namn på återställnings plan, Välj källa, mål & distributions modell värden.
   
   - Välj de virtuella datorer i skydds gruppen som du vill inkludera i återställnings planen och klicka på **OK** .
   
   - Välj återställnings plan som du skapade tidigare, klicka på knappen **Anpassa** för att öppna vyn anpassning av återställnings plan.
   
   - Högerklicka på **alla grupper Stäng** av och klicka på **Lägg till för att vidta åtgärder**.
   
   - Öppnar bladet infoga åtgärd, anger ett namn, väljer **primärt** alternativ i rutan var du vill köra, väljer du Automation-konto (där du har lagt till Runbooks) och väljer sedan Runbook- **behållaren redundans-StorSimple-container** .
   
   - Högerklicka på **grupp 1: starta** och klicka på **Lägg till skyddade objekt** , välj sedan de virtuella datorer som ska skyddas i återställnings planen och klicka på **OK** -knappen. Valfritt, om det redan är valt virtuella datorer.
   
   - Högerklicka på **grupp 1: Start** och klicka på alternativet **post åtgärd** och Lägg sedan till följande skript:  
      
      - Start-StorSimple – Runbook med virtuella enheter  
      - Redundansväxla-StorSimple-container-container  
      - Montering-volymer-efter-redundansväxling Runbook  
      - Avinstallera – anpassad – skript tillägg Runbook  
        
   - Lägg till en manuell åtgärd efter de 4 skripten ovan i samma **grupp 1: post-steg-** avsnittet. Den här åtgärden är den punkt där du kan kontrol lera att allt fungerar som det ska. Den här åtgärden behöver bara läggas till som en del av redundanstest (så Markera kryss rutan **testa redundans** ).
    
   - Efter den manuella åtgärden lägger du till **rensnings** skriptet med samma procedur som du använde för de andra Runbooks. **Spara** återställnings planen.
    
   > [!NOTE]
   > När du kör ett redundanstest bör du kontrol lera allt i det manuella åtgärds steget, eftersom de StorSimple-volymer som har klonas på mål enheten tas bort som en del av rensningen när den manuella åtgärden har slutförts.
       
      ![Återställningsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Utför ett redundanstest
I guiden [Active Directory Dr-lösnings](../site-recovery/site-recovery-active-directory.md) assistenten hittar du information om hur du Active Directory under redundanstest. Den lokala installationen störs inte alls när redundanstestning inträffar. De StorSimple-volymer som var anslutna till den lokala virtuella datorn klonas till StorSimple Cloud Appliance på Azure. En virtuell dator för test syfte hämtas i Azure och de klonade volymerna är anslutna till den virtuella datorn.

#### <a name="to-perform-the-test-failover"></a>Utföra redundanstest
1. I Azure Portal väljer du Site Recovery valvet.
1. Klicka på den återställnings plan som skapades för den virtuella fil servern.
1. Klicka på **testa redundans**.
1. Välj det virtuella Azure-nätverket som de virtuella Azure-datorerna ska anslutas till När redundansväxlingen sker.
   
   ![Starta redundans](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper eller på **jobbet testa redundans** i valv namn &gt; **jobb** &gt; **Site Recovery jobb**.
1. När redundansväxlingen är klar bör du även kunna se repliken av Azure-datorn i Azure Portal &gt; **Virtual Machines**. Du kan utföra dina verifieringar.
1. När verifieringen är klar klickar du på **valideringar slutfört**. Detta tar bort StorSimple-volymerna och stänger av StorSimple Cloud Appliance.
1. När du är klar klickar du på **rensning** av redundanstest i återställnings planen. I Notes-posten och spara alla observationer som är kopplade till redundanstest. Detta tar bort den virtuella dator som skapades under redundanstest.

## <a name="perform-a-planned-failover"></a>Utföra en planerad redundansväxling
   Under en planerad redundansväxling stängs den lokala fil serverns virtuella dator på ett smidigt sätt och en ögonblicks bild av en moln säkerhets kopia av volymerna på StorSimple-enheten tas bort. StorSimple-volymerna har redundansväxlats till den virtuella enheten, en virtuell replik dator skapas på Azure och volymerna är anslutna till den virtuella datorn.

#### <a name="to-perform-a-planned-failover"></a>Utföra en planerad redundansväxling
1. I Azure Portal väljer du återställnings planer för **Recovery Services** &gt; **-valv (Site Recovery)** &gt; **recoveryplan_name** som skapats för den virtuella fil servern.
1. På bladet återställnings plan klickar du på **mer** &gt; **planerad redundansväxling**.  

   ![Skärm bild som visar de planerade alternativen för redundans och redundans.](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. På bladet **Bekräfta Planerad redundans** väljer du käll-och mål platserna och väljer mål nätverk och klickar sedan på kryss ✓ för att starta redundansväxlingen.
1. När de virtuella replik datorerna har skapats är de i ett väntande tillstånd. Klicka på **genomför** för att genomföra redundansväxlingen.
1. När replikeringen är klar startar de virtuella datorerna på den sekundära platsen.

## <a name="perform-a-failover"></a>Utföra en redundansväxling
Under en oplanerad redundansväxling växlar StorSimple-volymer över till den virtuella enheten, en virtuell replik dator skapas på Azure och volymerna är anslutna till den virtuella datorn.

#### <a name="to-perform-a-failover"></a>Utföra en redundansväxling
1. I Azure Portal väljer du återställnings planer för **Recovery Services** &gt; **-valv (Site Recovery)** &gt; **recoveryplan_name** som skapats för den virtuella fil servern.
1. Klicka på **mer** redundans på bladet återställnings plan &gt; .  
1. På bladet **Bekräfta redundans** väljer du käll-och mål platserna.
1. Välj **Stäng virtuella datorer och synkronisera senaste data** för att ange att Site Recovery ska försöka stänga av den skyddade virtuella datorn och synkronisera data så att den senaste versionen av data kommer att Miss lyckas.
1. Efter redundansväxlingen är de virtuella datorerna i ett väntande tillstånd. Klicka på **genomför** för att genomföra redundansväxlingen.


## <a name="perform-a-failback"></a>Utföra en återställning efter fel
Under en återställning efter fel växlar StorSimple volym behållare tillbaka till den fysiska enheten när en säkerhets kopia har gjorts.

#### <a name="to-perform-a-failback"></a>Utföra en återställning efter fel
1. I Azure Portal väljer du återställnings planer för **Recovery Services** &gt; **-valv (Site Recovery)** &gt; **recoveryplan_name** som skapats för den virtuella fil servern.
1. På bladet återställnings plan klickar du på **mer** &gt; **planerad redundansväxling**.  
1. Välj käll-och mål platserna, Välj lämplig data synkronisering och alternativ för att skapa virtuella datorer.
1. Klicka på **OK** för att starta processen för återställning efter fel.
   
   ![Starta återställning efter fel](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Metodtips
### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitets planering och beredskaps bedömning
#### <a name="hyper-v-site"></a>Hyper-V-plats
Använd [verktyget för användar kapacitets planering](https://www.microsoft.com/download/details.aspx?id=39057) för att utforma Server-, lagrings-och nätverks infrastrukturen för din Hyper-V-replik miljö.

#### <a name="azure"></a>Azure
Du kan köra [verktyget för bedömning av beredskap för virtuella Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) -datorer på virtuella datorer för att säkerställa att de är kompatibla med virtuella Azure-datorer och Azure Site Recovery-tjänster. Verktyget readiness Assessment kontrollerar VM-konfigurationer och varnar när konfigurationer inte är kompatibla med Azure. Det kan till exempel vara en varning om en C: enhet är större än 127 GB.

Kapacitets planering består av minst två viktiga processer:

   - Mappa lokala virtuella Hyper-V-datorer till Azure VM-storlekar (till exempel A6, A7, A8 och A9).
   - Fastställer nödvändig Internet bandbredd.

## <a name="limitations"></a>Begränsningar
- För närvarande kan endast 1 StorSimple-enhet växlas över (till en enda StorSimple Cloud Appliance). Scenariot för en fil server som sträcker sig över flera StorSimple-enheter stöds inte ännu.
- Om du får ett fel när du aktiverar skydd för en virtuell dator bör du kontrol lera att du har kopplat från iSCSI-målen.
- Alla volym behållare som har grupper ATS tillsammans på grund av säkerhets kopierings principer som sträcker sig över volym behållare kommer att Miss lyckas tillsammans.
- Alla volymer i de volym behållare som du har valt kommer att växlas över.
- Volymer som lägger upp till mer än 64 TB kan inte redundansväxla eftersom den maximala kapaciteten för en enskild StorSimple Cloud Appliance är 64 TB.
- Om den planerade/oplanerade redundansväxlingen Miss lyckas och de virtuella datorerna skapas i Azure ska du inte rensa de virtuella datorerna. Gör i stället en återställning efter fel. Om du tar bort de virtuella datorerna kan de lokala virtuella datorerna inte aktive ras igen.
- Om du inte kan se volymerna går du till de virtuella datorerna, öppnar disk hantering, skannar om diskarna och ansluter dem sedan igen.
- I vissa fall kan enhets beteckningarna på DR-platsen skilja sig från de lokala bokstäverna. Om detta inträffar måste du korrigera problemet manuellt när redundansväxlingen är klart.
- Timeout för redundans: StorSimple-skriptet tar slut om redundansväxlingen av volym behållare tar längre tid än Azure Site Recovery gränsen per skript (för närvarande 120 minuter).
- Timeout för säkerhets kopierings jobb: StorSimple-skriptet nådde tids gränsen om säkerhets kopieringen av volymer tar längre tid än Azure Site Recovery gränsen per skript (för närvarande 120 minuter).
   
  > [!IMPORTANT]
  > Kör säkerhets kopieringen manuellt från Azure Portal och kör sedan återställnings planen igen.
   
- Tids gräns för klon jobb: StorSimple-skriptet nådde tids gränsen om kloningen av volymer tar längre tid än Azure Site Recovery gränsen per skript (för närvarande 120 minuter).
- Tidssynkroniseringsfel: StorSimple skript fel som säger att säkerhets kopiorna misslyckades trots att säkerhets kopieringen lyckades i portalen. En möjlig orsak kan vara att StorSimple-apparatens tid kanske inte är synkroniserad med den aktuella tiden i tids zonen.
   
  > [!IMPORTANT]
  > Synkronisera apparatens tid med den aktuella tiden i tids zonen.
   
- Fel vid redundansväxling: StorSimple-skriptet kan Miss Miss inaktivt om det sker en redundansväxling när återställnings planen körs.
   
  > [!IMPORTANT]
  > Kör återställnings planen igen när installationen av enheten är klar.


## <a name="summary"></a>Sammanfattning
Med hjälp av Azure Site Recovery kan du skapa en fullständig automatiserad katastrof återställnings plan för en virtuell fil server som har fil resurser som finns på StorSimple-lagring. Du kan initiera redundansväxlingen inom några sekunder från var som helst i händelse av ett avbrott och få programmet igång på några få minuter.