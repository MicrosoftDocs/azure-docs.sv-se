---
title: Övervaka Azure Site Recovery | Microsoft Docs
description: Övervaka och felsöka problem med Azure Site Recovery replikering och åtgärder med hjälp av portalen
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: d441284b265ab11dd5ece42ec3737e455d662435
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023575"
---
# <a name="monitor-site-recovery"></a>Övervaka Site Recovery

I den här artikeln lär du dig hur du övervakar Azure- [Site Recovery](site-recovery-overview.md)med Site Recovery inbyggd övervakning.  Du kan övervaka:

- Hälso tillstånd och status för datorer som replikeras av Site Recovery
- Testa redundans status för datorer.
- Problem och fel som påverkar konfigurationen och replikeringen.
- Infrastruktur komponenter som lokala servrar.


## <a name="before-you-start"></a>Innan du börjar

Du kanske vill se [vanliga övervaknings frågor](monitoring-common-questions.md) innan du börjar.

## <a name="monitor-in-the-dashboard"></a>Övervaka på instrument panelen

1. Klicka på **Översikt** i valvet. På instrument panelen för Recovery Services samlas all övervaknings information för valvet in på en enda plats. Det finns sidor för både Site Recovery och Azure Backup-tjänsten och du kan växla mellan dem.

    ![Site Recovery instrument panel](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Gå nedåt i instrument panelen i olika områden. 

    ![Skärm bild som visar områdena på instrument panelen där du kan öka detalj nivån.](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. I **replikerade objekt** klickar du på **Visa alla** för att visa alla servrar i valvet.
4. Klicka på status detaljer i varje avsnitt för att öka detalj nivån.
5. I **vyn infrastruktur** sorterar du övervaknings information efter den typ av datorer som du replikerar.

## <a name="monitor-replicated-items"></a>Övervaka replikerade objekt

I **replikerade objekt** övervakar du hälsan för alla datorer i valvet där replikering är aktiverat.

**Tillstånd** | **Information**
--- | ---
Felfri | Replikering pågår normalt. Inga fel eller varnings symtom har identifierats.
Varning | Ett eller flera varnings symtom som kan påverka replikeringen identifieras.
Kritiskt | Ett eller flera problem med kritisk replikering har upptäckts.<br/><br/> De här fel symptomen är vanligt vis indikatorer som har fastnat eller som inte går upp lika snabbt som data ändrings takten.
Inte tillämpligt | Servrar som inte är förväntade att replikeras. Detta kan inkludera datorer som har redundansväxlats.

## <a name="monitor-test-failovers"></a>Övervaka redundanstest

I redundanstestning **lyckades** övervaka redundansväxlingen för datorer i valvet.

- Vi rekommenderar att du kör en redundanstest på replikerade datorer minst en gång var sjätte månad. Det är ett sätt att kontrol lera att redundansväxlingen fungerar som förväntat, utan att störa produktions miljön. 
- Ett redundanstest betraktas som lyckat efter redundansväxlingen och rensningen efter redundans har slutförts.

**Tillstånd** | **Information**
--- | ---
Test rekommenderas | Datorer som inte har haft en redundanstest sedan skyddet aktiverades.
Har genomförts | Datorer med eller flera lyckade redundanstest.
Inte tillämpligt | Datorer som för närvarande inte är berättigade till redundanstest. Till exempel om datorer som inte är redundansväxla, har inledande replikering/redundanstest/redundansväxling pågår.

## <a name="monitor-configuration-issues"></a>Övervaka konfigurations problem

I **konfigurations problem** övervakar du eventuella problem som kan påverka din möjlighet att redundansväxla.

- Konfigurations problem (förutom för program uppdaterings tillgänglighet) identifieras av en regelbunden validator-åtgärd som körs var 12: e timme som standard. Du kan tvinga validerings åtgärden att köras omedelbart genom att klicka på uppdaterings ikonen bredvid avsnitts rubriken **konfigurations problem** .
- Klicka på länkarna om du vill ha mer information. För problem som påverkar specifika datorer klickar du på **behöver uppmärksamhet** i kolumnen **mål konfiguration** . Information innehåller reparations rekommendationer.

**Tillstånd** | **Information**
--- | ---
Konfigurationer som saknas | En nödvändig inställning saknas, till exempel ett återställnings nätverk eller en resurs grupp.
Resurser som saknas | Det går inte att hitta en angiven resurs eller så är den inte tillgänglig i prenumerationen. Till exempel har resursen tagits bort eller migrerats. Övervakade resurser inkluderade mål resurs gruppen, målets virtuella nätverk/undernät, logg-/mål lagrings konto, mål tillgänglighets uppsättning, mål-IP-adress.
Prenumerations kvot |  Antalet tillgängliga prenumerations resurs kvoter jämförs med den balans som krävs för att redundansväxla alla datorer i valvet.<br/><br/> Om det inte finns tillräckligt med resurser rapporteras ett otillräckligt kvot belopp.<br/><br/> Kvoter övervakas för antal virtuella dator kärnor, antal virtuella datorer i serien, nätverkskort (Network Interface Card).
Programuppdateringar | Tillgängligheten för nya program uppdateringar och information om upphör ande av program varu versioner.


## <a name="monitor-errors"></a>Övervaka fel

I **fel Sammanfattning** kan du övervaka aktuella aktiva fel som kan påverka replikeringen av servrar i valvet och övervaka antalet påverkade datorer.

- Fel som påverkar komponenter för lokal infrastruktur visas i början av avsnittet. Till exempel utebliven pulsslag från Azure Site Recovery-providern på den lokala konfigurations servern eller Hyper-V-värden.
- Sedan visas fel i replikeringen som påverkar replikerade servrar.
- Tabell posterna sorteras genom fallande ordning efter allvarlighets grad och sedan efter fallande ordning för de påverkade datorerna.
- Antalet påverkade servrar är ett användbart sätt att förstå om ett enda underliggande problem kan påverka flera datorer. Ett nätverks fel kan till exempel påverka alla datorer som replikeras till Azure. 
- Flera replikeringsfel kan uppstå på en enskild server. I det här fallet räknar varje fel problem den servern i listan över de servrar som påverkas. När problemet har åtgärd ATS förbättras replikeringsinställningarna och felet tas bort från datorn.

## <a name="monitor-the-infrastructure"></a>Övervaka infrastrukturen.

I **vyn infrastruktur** övervakar du de infrastruktur komponenter som ingår i replikeringen och anslutnings hälsan mellan servrar och Azure-tjänsterna.

- En grön linje indikerar att anslutningen är felfri.
- En röd linje med ikonen för överlappande fel indikerar att det finns ett eller flera fel som påverkar anslutningen.
-  Hovra med mus pekaren över fel ikonen för att visa fel och antalet påverkade entiteter. Klicka på ikonen för en filtrerad lista över påverkade entiteter.

    ![Site Recovery Infrastructure View (valv)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tips för att övervaka infrastrukturen

- Kontrol lera att komponenterna i den lokala infrastrukturen (konfigurations Server, process servrar, VMM-servrar, Hyper-V-värdar, VMware-datorer) kör de senaste versionerna av Site Recovery-providern och/eller agenter.
- Om du vill använda alla funktioner i vyn infrastruktur ska du köra samlad [uppdatering 22](https://support.microsoft.com/help/4072852) för dessa komponenter.
- Om du vill använda vyn infrastruktur väljer du lämpligt scenario för replikering i din miljö. Du kan öka detalj nivån i vyn om du vill ha mer information. I följande tabell visas vilka scenarier som visas.

    **Scenario** | **Tillstånd**  | **Vyn är tillgänglig?**
    --- |--- | ---
    **Replikering mellan lokala platser** | Alla delstater | Inga 
    **Azure VM-replikering mellan Azure-regioner**  | Replikering har Aktiver ATS/inledande replikering pågår | Ja
    **Azure VM-replikering mellan Azure-regioner** | Misslyckad/misslyckad återställning | Inga   
    **Replikering av VMware till Azure** | Replikering har Aktiver ATS/inledande replikering pågår | Ja     
    **Replikering av VMware till Azure** | Misslyckad/misslyckad återställning | Inga      
    **Hyper-V-replikering till Azure** | Misslyckad/misslyckad återställning | Inga

- Om du vill se vyn infrastruktur för en enskild replikerande dator klickar du på **replikerade objekt** i menyn valv och väljer en server.  




## <a name="monitor-recovery-plans"></a>Övervaka återställnings planer

I **återställnings planer** kan du övervaka antalet planer, skapa nya planer och ändra befintliga.  

## <a name="monitor-jobs"></a>Övervaka jobb

I **jobb** övervakar du statusen för Site Recovery åtgärder.

- De flesta åtgärder i Azure Site Recovery körs asynkront, med ett spårnings jobb som skapas och används för att spåra förloppet för åtgärden. 
- Jobbobjektet innehåller all information som du behöver för att spåra status och åtgärdens förlopp. 

Övervaka jobb enligt följande:

1. I avsnittet instrument panels > **jobb** kan du se en sammanfattning av jobb som har slutförts, som pågår eller väntar på inmatade under de senaste 24 timmarna. Du kan klicka på valfritt tillstånd om du vill ha mer information om relevanta jobb.
2. Klicka på **Visa alla** för att visa alla jobb under de senaste 24 timmarna.

    > [!NOTE]
    > Du kan också komma åt jobb information från menyn valv > **Site Recovery jobb**. 

2. I listan **Site Recovery jobb** visas en lista över jobb. På den översta menyn kan du få fel information för ett särskilt jobb, filtrera jobb listan baserat på vissa kriterier och Exportera valda jobb Detaljer till Excel.
3. Du kan öka detalj nivån för ett jobb genom att klicka på det. 

## <a name="monitor-virtual-machines"></a>Övervakning av virtuella datorer

I **replikerade objekt** hämtar du en lista över replikerade datorer. 
    ![Vy över Site Recovery replikerade objekt](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Du kan visa och filtrera information. På Åtgärd-menyn längst upp kan du utföra åtgärder för en viss dator, inklusive köra ett redundanstest eller visa specifika fel.
3. Klicka på **kolumner** om du vill visa ytterligare kolumner, till exempel för att visa problem med konfiguration av återställnings mål, mål konfiguration och replikering.
4. Klicka på **filter** om du vill visa information som baseras på specifika parametrar, till exempel replikeringsstatus eller en viss replikeringsprincip.
5. Högerklicka på en dator om du vill initiera åtgärder som redundanstest för den, eller om du vill visa detaljerad fel information som är kopplad till den.
6. Klicka på en dator om du vill gå vidare till mer information om den. Informationen omfattar:
   - **Replikeringsinformation**: aktuell status och hälsa för datorn.
   -  Återställnings punkt mål: aktuell återställnings punkt för den virtuella datorn och tiden då återställningen senast beräknades.
   - **Återställnings punkter**: senaste tillgängliga återställnings punkter för datorn.
   - **Beredskap för redundans**: anger om ett redundanstest har körts för datorn, agent versionen som körs på datorn (för datorer som kör mobilitets tjänsten) och eventuella konfigurations problem.
   - **Fel**: lista över replikeringsfel som för närvarande observerats på datorn och möjliga orsaker/åtgärder.
   - **Händelser**: en kronologisk lista över de senaste händelserna som påverkar datorn. Fel information visar de problem som är synliga för närvarande, medan händelser är en historisk post för problem som har påverkat datorn.
   - **Vyn infrastruktur**: visar infrastrukturens tillstånd för scenariot när datorer replikeras till Azure.

     ![Information om Site Recovery replikerat objekt/översikt](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Prenumerera på e-postmeddelanden

Du kan prenumerera på att ta emot e-postmeddelanden för följande kritiska händelser:
 
- Kritiskt tillstånd för replikerad dator.
- Ingen anslutning mellan komponenterna i den lokala infrastrukturen och Site Recovery tjänsten. Anslutning mellan Site Recovery och lokala servrar som registrerats i ett valv identifieras med en pulsslags funktion.
- Redundansväxlings fel.

Prenumerera på följande sätt:

I avsnittet för valv > **övervakning** klickar du på **Site Recovery händelser**.
1. Klicka på **E-postmeddelanden**.
1. I **e-postavisering** aktiverar du meddelanden och anger vem som ska skickas till. Du kan skicka till alla prenumerations administratörer om du vill skicka meddelanden och eventuellt speciella e-postadresser.

    ![E-postmeddelanden](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](monitor-log-analytics.md) att övervaka Site Recovery med Azure Monitor.
