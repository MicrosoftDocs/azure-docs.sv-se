---
title: Uppdateringar och komponent uppgraderingar i Azure Site Recovery
description: Innehåller en översikt över Azure Site Recovery tjänst uppdateringar och komponent uppgraderingar.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: ramamill
ms.openlocfilehash: a1ea8b6fb9800d796670161288be0d86ce6ffc42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89424947"
---
# <a name="service-updates-in-site-recovery"></a>Tjänst uppdateringar i Site Recovery

Den här artikeln innehåller en översikt över [Azure Site Recovery](site-recovery-overview.md) uppdateringar och beskriver hur du uppgraderar Site Recovery-komponenter.

Site Recovery publicerar regelbundet uppdateringar av tjänsten. Uppdateringarna innehåller nya funktioner, support förbättringar, komponent uppdateringar och fel korrigeringar. För att kunna dra nytta av de senaste funktionerna och korrigeringarna rekommenderar vi att du kör de senaste versionerna av Site Recovery-komponenter. 
 
 
## <a name="updates-support"></a>Stöd för uppdateringar

### <a name="support-statement-for-azure-site-recovery"></a>Support policy för Azure Site Recovery

Vi rekommenderar att du alltid uppgraderar till de senaste komponent versionerna:

**Med varje ny version av en Azure Site Recovery komponent som släpps, anses alla versioner under "n-4" inte vara av stöd**. 

> [!IMPORTANT]
> Statligt stöd är att uppgradera från > N-4-versionen till N-version. Om du till exempel kör du är i N-6 måste du först uppgradera till N-4 och sedan uppgradera till N.


### <a name="links-to-currently-supported-update-rollups"></a>Länkar till aktuella samlade uppdateringar som stöds

 Läs den senaste samlade uppdateringen (version N) i [den här artikeln](site-recovery-whats-new.md). Kom ihåg att Site Recovery ger stöd för N-4-versioner.



## <a name="component-expiry"></a>Komponentens förfallo datum

Site Recovery meddelar dig om utgångna komponenter (eller snart upphör ande) via e-post (om du prenumererar på e-postmeddelanden) eller på instrument panelen för valvet i portalen.

- När uppdateringar är tillgängliga i vyn infrastruktur för ditt scenario i portalen, visas dessutom en **uppdaterings** knapp bredvid komponenten. Med den här knappen omdirigeras du till en länk för att ladda ned den senaste komponent versionen.
-  Instrument panels meddelanden för valv är inte tillgängliga om du replikerar virtuella Hyper-V-datorer. 

E-postmeddelanden skickas på följande sätt.

**Tid** | **Frekvens**
--- | ---
60 dagar innan komponentens upphör Ande | En gång i veckan
Kommande 53 dagarna | En gång i veckan
Senaste 7 dagarna | En gång om dagen
Efter förfallo datum | En gång i veckan


### <a name="upgrading-outside-official-support"></a>Uppgradera utanför statsstöd

Om skillnaden mellan komponent versionen och den senaste versionen av versionen är större än fyra anses detta vara av stöd. I det här fallet uppgraderar du enligt följande: 

1. Uppgradera den aktuella installerade komponenten till din aktuella version plus fyra. Om din version till exempel är 9,16, så uppgradera sedan till 9,20.
2. Uppgradera sedan till nästa kompatibla version. I vårt exempel, efter att ha uppgraderat 9,16 till 9,20, uppgradera till 9,24. 

Följ samma process för alla relevanta komponenter.

### <a name="support-for-latest-operating-systemskernels"></a>Stöd för de senaste operativ systemen/kernelerna

> [!NOTE]
> Om du har schemalagt ett underhålls fönster och en omstart ingår i den, rekommenderar vi att du först uppgraderar Site Recovery-komponenter och fortsätter sedan med resten av de schemalagda aktiviteterna i underhålls fönstret.

1. Innan du uppgraderar operativ systemet/kernel-versioner kontrollerar du om mål versionen stöds Site Recovery. 

    - Stöd för [virtuella Azure-datorer](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) .
    - Stöd för [VMware/fysisk server](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Stöd för [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) .
2. Granska [tillgängliga uppdateringar](site-recovery-whats-new.md) för att ta reda på vad du vill uppgradera.
3. Uppgradera till den senaste versionen av Site Recovery.
4. Uppgradera operativ systemet/kärnan till de versioner som krävs.
5. Omstart.


Den här processen säkerställer att datorns operativ system/kernel uppgraderas till den senaste versionen och att de senaste Site Recovery ändringar som krävs för att stödja den nya versionen har lästs in på datorn.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Haveriberedskap för virtuell Azure-dator till Azure

I det här scenariot rekommenderar vi starkt att du [aktiverar automatiska uppdateringar](azure-to-azure-autoupdate.md). Du kan tillåta Site Recovery att hantera uppdateringar på följande sätt:

- Under aktiveringen av replikering.
- Genom att ange inställningar för tilläggs uppdatering i valvet.

Om du vill hantera uppdateringar manuellt kan du välja något av följande alternativ:

1. När en ny agent uppdatering är tillgänglig tillhandahåller Site Recovery ett meddelande i valvet mot sidans överkant. I valvet > **replikerade objekt** klickar du på det här meddelandet överst på skärmen: 
    
    **Det finns en ny Site Recovery uppdatering av replikeringsprincipen. Klicka om du vill installera >** <br/><br/>Välj de virtuella datorer som du vill använda uppdateringen för och klicka sedan på **OK**.

2. På översikts sidan för översikt över virtuella datorer hittar du fältet "agent status" som kommer att säga "kritisk uppgradering" om agenten upphör att gälla. Klicka på den och följ de efterföljande anvisningarna för att uppgradera den virtuella datorn manuellt.

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Katastrof återställning av VMware VM/fysisk server till Azure

1. Baserat på din aktuella version och [support instruktionen](#support-statement-for-azure-site-recovery)installerar du först uppdateringen på den lokala konfigurations servern med hjälp av [dessa instruktioner](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Om du har skalbara process servrar uppdaterar du dem sedan med hjälp av [de här anvisningarna](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Information om hur du uppdaterar mobilitets agenten på varje skyddad dator finns i [den här](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artikeln.

### <a name="reboot-after-mobility-service-upgrade"></a>Starta om efter uppgradering av mobilitets tjänsten

En omstart rekommenderas efter varje uppgradering av mobilitets tjänsten för att säkerställa att alla de senaste ändringarna läses in på käll datorn.

En omstart är inte obligatorisk, om inte skillnaden mellan agent versionen under den senaste omstarten och den aktuella versionen är större än fyra.

Exemplet i tabellen visar hur det fungerar.

|**Agent version (senaste omstart)** | **Uppgradera till** | **Obligatorisk omstart?**|
|---------|---------|---------|
|9,16 |  9,18 | Inte obligatoriskt|
|9,16 | 9,19 | Inte obligatoriskt|
| 9,16 | 9,20 | Inte obligatoriskt
 | 9,16 | 9,21 | Obligatorisk.<br/><br/> Uppgradera till 9,20 och starta sedan om innan du uppgraderar till 9,21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Haveriberedskap för virtuell Hyper-V-dator till Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Mellan en Hyper-V-plats och Azure

1. Hämta uppdateringen för Microsoft Azure Site Recovery-providern.
2. Installera providern på varje Hyper-V-server som registrerats i Site Recovery. Om du kör ett kluster uppgraderar du på alla klusternoder.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Mellan en lokal VMM-webbplats och Azure
1. Hämta uppdateringen för Microsoft Azure Site Recovery-providern.
2. Installera providern på VMM-servern. Om VMM är distribuerat i ett kluster installerar du providern på alla klusternoder.
3. Installera den senaste Microsoft Azure Recovery Services agenten på alla Hyper-V-värdar eller klusternoder.


## <a name="between-two-on-premises-vmm-sites"></a>Mellan två lokala VMM-webbplatser
1. Hämta den senaste uppdateringen för Microsoft Azure Site Recovery-providern.
2. Installera den senaste providern på VMM-servern som hanterar den sekundära återställnings platsen. Om VMM är distribuerat i ett kluster installerar du providern på alla klusternoder.
3. När återställnings platsen har uppdaterats installerar du providern på VMM-servern som hanterar den primära platsen.

## <a name="next-steps"></a>Nästa steg

Följ våra [Azure updates](https://azure.microsoft.com/updates/?product=site-recovery) -sidor för att spåra nya uppdateringar och versioner.
