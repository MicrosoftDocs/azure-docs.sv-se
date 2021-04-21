---
title: Anpassningsbara programkontroller i Azure Security Center
description: Det här dokumentet hjälper dig att använda anpassningsbar programkontroll i Azure Security Center för att tillåta att lista program som körs på Azure-datorer.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 9e2dcace673a1c7215634434f9e89ddc6b953a63
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834632"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Använda anpassningsbara programkontroller för att minska datorernas angreppsytor

Lär dig mer om fördelarna Azure Security Center med anpassningsbara programkontroller och hur du kan förbättra din säkerhet med den här datadrivna, intelligenta funktionen.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Vad är Security Center anpassningsbara programkontroller?

Anpassningsbara programkontroller är en intelligent och automatiserad lösning för att definiera listor över kända säkra program för dina datorer. 

Organisationer har ofta samlingar med datorer som regelbundet kör samma processer. Security Center använder maskininlärning för att analysera de program som körs på dina datorer och skapa en lista över kända säkra program. Listorna Över tillåtna baseras på dina specifika Azure-arbetsbelastningar och du kan anpassa rekommendationerna ytterligare med hjälp av anvisningarna nedan.

När du har aktiverat och konfigurerat anpassningsbara programkontroller får du säkerhetsaviseringar om något annat program körs än de som du har definierat som säkra.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Vilka är fördelarna med anpassningsbara programkontroller?

Genom att definiera listor över kända säkra program och generera aviseringar när något annat körs kan du uppnå flera härdningsmål:

- Identifiera potentiell skadlig kod, även de som kan missas av lösningar mot skadlig kod
- Förbättra efterlevnaden av lokala säkerhetsprinciper som kräver att endast licensierad programvara används
- Undvik att köra gamla eller program som inte stöds
- Förhindra specifika program som är förbjudna av din organisation
- Öka översikten över appar som har åtkomst till känsliga data

Det finns för närvarande inga tvingande alternativ. Anpassningsbara programkontroller är avsedda att tillhandahålla säkerhetsaviseringar om något annat program körs än de som du har definierat som säkra.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Utgivningstillstånd:|Allmän tillgänglighet (GA)|
|Prissättning:|Kräver [Azure Defender för servrar](defender-for-servers-introduction.md)|
|Datorer som stöds:|![Ja, ](./media/icons/yes-icon.png) Azure- och icke-Azure-datorer som kör Windows och Linux<br>![Ja ](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/index.yml) datorer|
|Nödvändiga roller och behörigheter:|**Rollerna** **Säkerhetsläsare** och Läsare kan både visa grupper och listor över kända säkra program<br>**Rollerna** **Deltagare och Säkerhetsadministratör** kan både redigera grupper och listor över kända säkra program|
|Moln:|![Yes](./media/icons/yes-icon.png) Kommersiella moln<br>![Yes](./media/icons/yes-icon.png) National/Sovereign (US Gov, China Gov, Other Gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Aktivera programkontroller på en grupp datorer

Om Security Center har identifierat grupper av datorer i dina prenumerationer som konsekvent kör en liknande uppsättning program, uppmanas du att följa rekommendationen: **Anpassningsbara** programkontroller för att definiera säkra program ska aktiveras på dina datorer .

Välj rekommendationen eller öppna sidan för anpassningsbara programkontroller för att visa listan över föreslagna kända säkra program och grupper av datorer.

1. Öppna Azure Defender instrumentpanel och välj Anpassningsbara programkontroller **i området avancerat skydd.**

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Öppna anpassningsbara programkontroller från Azure-instrumentpanelen" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Sidan **Anpassningsbara programkontroller** öppnas med dina virtuella datorer grupperade i följande flikar:

    - **Konfigurerad** – Grupper av datorer som redan har en definierad lista över tillåtna program. För varje grupp visar den konfigurerade fliken:
        - antalet datorer i gruppen
        - senaste aviseringar

    - **Rekommenderat** – Grupper av datorer som konsekvent kör samma program och som inte har en lista över tillåtna konfigurerade. Vi rekommenderar att du aktiverar anpassningsbara programkontroller för dessa grupper.
    
      > [!TIP]
      > Om du ser ett gruppnamn med prefixet "REVIEWGROUP" innehåller det datorer med en delvis konsekvent lista över program. Security Center kan inte se ett mönster, men rekommenderar att  du granskar den här gruppen för att se om du manuellt kan definiera vissa regler för anpassningsbara programkontroller enligt beskrivningen i Redigera en grupps regel för anpassningsbara [programkontroller.](#edit-a-groups-adaptive-application-controls-rule)
      >
      > Du kan också flytta datorer från den här gruppen till andra grupper enligt beskrivningen i [Flytta en dator från en grupp till en annan.](#move-a-machine-from-one-group-to-another)

    - **Ingen rekommendation** – Datorer utan en definierad lista över tillåtna program och som inte stöder funktionen. Datorn kan finnas på den här fliken av följande skäl:
      - Det saknas en Log Analytics-agent
      - Log Analytics-agenten skickar inte händelser
      - Det är en Windows-dator med en befintlig [AppLocker-princip](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) som antingen aktiveras av ett grupprincipobjekt eller en lokal säkerhetsprincip

      > [!TIP]
      > Security Center behöver minst två veckors data för att definiera de unika rekommendationerna per grupp med datorer. Datorer som nyligen har skapats, eller som tillhör prenumerationer som endast nyligen har aktiverats med Azure Defender, visas under **fliken Ingen** rekommendation.


1. Öppna **fliken** Rekommenderas. De grupper av datorer med rekommenderade listor över tillåtna visas.

   ![Rekommenderad flik](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Välj en grupp. 

1. Om du vill konfigurera den nya  regeln granskar du de olika avsnitten på sidan Konfigurera regler för programkontroll och innehållet, som är unikt för den här specifika gruppen med datorer:

   ![Konfigurera en ny regel](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Välj datorer** – Som standard är alla datorer i den identifierade gruppen markerade. Avmarkera alla för att ta bort dem från den här regeln.
   
   1. **Rekommenderade program** – Granska den här listan över program som är gemensamma för datorerna i den här gruppen och som rekommenderas att kunna köras.
   
   1. **Fler program** – Granska den här listan över program som antingen visas mindre ofta på datorerna i den här gruppen eller som är kända för att vara exploaterbara. En varningsikon anger att ett visst program kan användas av en angripare för att kringgå en lista över tillåtna program. Vi rekommenderar att du noggrant granskar dessa program.

      > [!TIP]
      > Båda programlistorna innehåller alternativet att begränsa ett visst program till vissa användare. Anta principen om minsta behörighet när det är möjligt.
      > 
      > Program definieras av sina utgivare. Om ett program inte har utgivarinformation (det är osignerat) skapas en sökvägsregel för det specifika programmets fullständiga sökväg.

   1. Om du vill tillämpa regeln väljer du **Granska.** 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Redigera en grupps regel för anpassningsbara programkontroller

Du kan välja att redigera listan över tillåtna för en grupp datorer på grund av kända ändringar i din organisation. 

Så här redigerar du reglerna för en grupp datorer:

1. Öppna Azure Defender instrumentpanel och välj Anpassningsbara programkontroller **i området avancerat skydd.**

1. På fliken **Konfigurerad** väljer du gruppen med den regel som du vill redigera.

1. Läs de olika avsnitten på **sidan Konfigurera regler för programkontroll** enligt beskrivningen i Aktivera [anpassningsbara programkontroller på en grupp datorer.](#enable-application-controls-on-a-group-of-machines)

1. Du kan också lägga till en eller flera anpassade regler:

   1. Välj **Lägg till regel**.

      ![Lägga till en anpassad regel](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Om du definierar en känd säker sökväg ändrar du **Regeltyp till** Sökväg och anger en enda sökväg. Du kan inkludera jokertecken i sökvägen.
   
      > [!TIP]
      > Vissa scenarier där jokertecken i en sökväg kan vara användbara:
      > 
      > * Använda ett jokertecken i slutet av en sökväg för att tillåta alla körbara filer i den här mappen och undermappar.
      > * Använda ett jokertecken mitt i en sökväg för att aktivera ett känt körbart namn med ett föränderligt mappnamn (till exempel personliga användarmappar som innehåller en känd körbar fil, automatiskt genererade mappnamn osv.).
  
   1. Definiera tillåtna användare och skyddade filtyper.

   1. När du är klar med att definiera regeln väljer du Lägg **till**.

1. Om du vill tillämpa ändringarna väljer du **Spara**.


## <a name="review-and-edit-a-groups-settings"></a>Granska och redigera en grupps inställningar

1. Om du vill visa information och inställningar för din grupp väljer du **Gruppinställningar**

    I det här fönstret visas namnet på gruppen (som kan ändras), typ av operativsystem, plats och annan relevant information.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Sidan gruppinställningar för anpassningsbara programkontroller" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Du kan också ändra gruppens namn eller skyddslägen för filtyper.

1. Välj **Tillämpa** och **Spara.**



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Svara på rekommendationen "Allowlist rules in your adaptive application control policy should be updated" (Tillåtlista regler i din princip för anpassningsbar programkontroll bör uppdateras)

Du ser den här rekommendationen när Security Center maskininlärning identifierar potentiellt legitimt beteende som inte tidigare har tillåtits. Rekommendationen föreslår nya regler för dina befintliga definitioner för att minska antalet falska positiva aviseringar.

Så här åtgärdar du problemen:

1. På sidan med rekommendationer väljer du **reglerna i** listan över tillåtna i din princip för anpassningsbar programkontroll bör uppdateras, så att du ser grupper med nyligen identifierat, potentiellt legitimt beteende.

1. Välj gruppen med den regel som du vill redigera.

1. Läs de olika avsnitten på **sidan Konfigurera regler för programkontroll** enligt beskrivningen i Aktivera [anpassningsbara programkontroller på en grupp datorer.](#enable-application-controls-on-a-group-of-machines)

1. Om du vill tillämpa ändringarna väljer du **Granska**.




## <a name="audit-alerts-and-violations"></a>Granska aviseringar och överträdelser

1. Öppna instrumentpanelen Azure Defender och välj Anpassningsbara programkontroller i **området avancerat skydd.**

1. Om du vill se grupper med datorer som har de senaste aviseringarna granskar du de grupper som visas **på fliken Konfigurerad.**

1. Om du vill undersöka vidare väljer du en grupp.

   ![Senaste aviseringar](./media/security-center-adaptive-application/recent-alerts.png)

1. Om du vill ha mer information och en lista över berörda datorer väljer du en avisering.

    På aviseringssidan visas mer information om aviseringarna och en **Åtgärdslänk för** att vidta åtgärder innehåller rekommendationer om hur du åtgärdar hotet.

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="Starttiden för aviseringar för anpassningsbara programkontroller är ":::

    > [!NOTE]
    > Anpassningsbara programkontroller beräknar händelser var 12:e timme. Den "aktivitetens starttid" som visas på aviseringssidan är  den tid då anpassningsbara programkontroller skapade aviseringen, inte den tid då den misstänkta processen var aktiv.


## <a name="move-a-machine-from-one-group-to-another"></a>Flytta en dator från en grupp till en annan

När du flyttar en dator från en grupp till en annan ändras den programkontrollprincip som tillämpas på den till inställningarna för den grupp som du flyttade den till. Du kan också flytta en dator från en konfigurerad grupp till en icke-konfigurerad grupp. Då tas alla regler för programkontroll som tillämpades på datorn bort.

1. Öppna Azure Defender instrumentpanel och välj Anpassningsbara programkontroller **i området avancerat skydd.**

1. På sidan **Anpassningsbara programkontroller** går du till **fliken Konfigurerad** och väljer den grupp som innehåller den dator som ska flyttas.

1. Öppna listan över **konfigurerade datorer.**

1. Öppna datorns meny från tre punkter i slutet av raden och välj **Flytta**. Fönstret **Flytta dator till en annan** grupp öppnas.

1. Välj målgruppen och välj **Flytta dator.**

1. Välj **Spara** för att spara ändringarna.





## <a name="manage-application-controls-via-the-rest-api"></a>Hantera programkontroller via REST API 

Om du vill hantera dina anpassningsbara programkontroller programmatiskt använder du REST API. 

Relevant API-dokumentation finns i [avsnittet Anpassningsbara programkontroller i Security Center API-dokument](/rest/api/securitycenter/adaptiveapplicationcontrols).

Några av de funktioner som är tillgängliga från REST API:

* **Listan** hämtar alla grupprekommendationer och tillhandahåller en JSON med ett -objekt för varje grupp.

* **Get** hämtar JSON med fullständiga rekommendationsdata (det vill säga lista över datorer, utgivar-/sökvägsregler och så vidare).

* **Put** konfigurerar din regel (använd den JSON som du hämtade med **Get** som brödtext för den här begäran).
 
   > [!IMPORTANT]
   > **Put-funktionen** förväntar sig färre parametrar än JSON som returneras av Kommandot Get innehåller.
   >
   > Ta bort följande egenskaper innan du använder JSON i Put-begäran: recommendationStatus, configurationStatus, issues, location och sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Vanliga frågor och svar – Anpassningsbara programkontroller

- [Finns det några alternativ för att framtvinga programkontrollerna?](#are-there-any-options-to-enforce-the-application-controls)
- [Varför visas en Qualys-app i mina rekommenderade program?](#why-do-i-see-a-qualys-app-in-my-recommendeded-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Finns det några alternativ för att framtvinga programkontrollerna?
Det finns för närvarande inga tvingande alternativ. Anpassningsbara programkontroller är avsedda att **tillhandahålla säkerhetsaviseringar** om något annat program körs än de som du har definierat som säkra. De har en mängd fördelar ( Vilka är fördelarna med[anpassningsbara programkontroller?](#what-are-the-benefits-of-adaptive-application-controls)) och är mycket anpassningsbara som visas på den här sidan.

### <a name="why-do-i-see-a-qualys-app-in-my-recommendeded-applications"></a>Varför visas en Qualys-app i mina rekommenderade program?
[Azure Defender för servrar](defender-for-servers-introduction.md) omfattar sårbarhetsgenomsökning för dina datorer utan extra kostnad. Du behöver inte en Qualys-licens eller ens ett Qualys-konto – allt hanteras sömlöst i Security Center. Mer information om den här skannern och anvisningar för hur du distribuerar den finns [i Defender's integrated vulnerability assessment solution (Defender:s integrerade lösning för sårbarhetsbedömning).](deploy-vulnerability-assessment-vm.md)

För att säkerställa att inga aviseringar genereras Security Center distribuerar skannern, innehåller listan över tillåtna anpassningsbara programkontroller skannern för alla datorer. 


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du använder anpassningsbar programkontroll i Azure Security Center för att definiera listor över tillåtna program som körs på dina Azure- och icke-Azure-datorer. Mer information om några av Security Center andra funktioner för skydd av molnarbetsbelastningar finns i:

* [Förstå VM-åtkomst med just-in-time (JIT)](just-in-time-explained.md)
* [Skydda dina Azure Kubernetes-kluster](defender-for-kubernetes-introduction.md)