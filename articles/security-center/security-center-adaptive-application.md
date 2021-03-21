---
title: Anpassningsbara programkontroller i Azure Security Center
description: Det här dokumentet hjälper dig att använda anpassningsbar program kontroll i Azure Security Center för att tillåta att List program körs på Azure-datorer.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: de5c5fbb6673d022517bba2486ed4aa1f739d19b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439586"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Använd anpassningsbara program kontroller för att minska dina datorers attack ytor

Lär dig mer om fördelarna med Azure Security Centers anpassningsbara program kontroller och hur du kan förbättra säkerheten med den här data drivna, smarta funktionen.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Vad är Security Centers anpassningsbara program kontroller?

Anpassningsbara program kontroller är en intelligent och automatiserad lösning för att definiera tillåtna listor över kända säkra program för dina datorer. 

Organisationer har ofta samlingar av datorer som rutinmässigt kör samma processer. Security Center använder Machine Learning för att analysera de program som körs på dina datorer och skapa en lista över kända säkra program. Listor över tillåtna Azure-arbetsbelastningar baseras på dina Azure-arbetsbelastningar och du kan anpassa rekommendationerna ytterligare genom att följa anvisningarna nedan.

När du har aktiverat och konfigurerat anpassade program kontroller får du säkerhets aviseringar om andra program körs än de som du har definierat som säkra.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Vilka är fördelarna med anpassningsbara program kontroller?

Genom att definiera listor över kända säkra program och generera aviseringar när något annat utförs, kan du uppnå flera härdnings mål:

- Identifiera potentiell skadlig kod, även om de kan missas av lösningar mot skadlig kod
- Förbättra kompatibiliteten med lokala säkerhets principer som dikterar användningen av endast licensierad program vara
- Undvik att köra gamla eller program som inte stöds
- Förhindra en speciell program vara som har blockerats av din organisation
- Öka överblicken i appar som har åtkomst till känsliga data

Inga tvingande alternativ är tillgängliga för närvarande. Anpassningsbara program kontroller är avsedda att ge säkerhets aviseringar om andra program körs än de som du har definierat som säkra.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kräver [Azure Defender för servrar](defender-for-servers-introduction.md)|
|Datorer som stöds:|![Ja ](./media/icons/yes-icon.png) Azure-och icke-Azure-datorer som kör Windows och Linux<br>![Ja ](./media/icons/yes-icon.png) [Azure båg](../azure-arc/index.yml) -datorer|
|Nödvändiga roller och behörigheter:|**Säkerhets läsare** och **läsar** roller kan båda Visa grupper och listor över kända säkra program<br>Rollerna **deltagare** och **säkerhets administratör** kan både redigera grupper och listor över kända säkra program|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Aktivera program kontroller i en grupp datorer

Om Security Center har identifierat grupper av datorer i dina prenumerationer som konsekvent kör en liknande uppsättning program, uppmanas du att ange följande rekommendation: **anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer**.

Välj rekommendationen eller öppna sidan adaptiva program kontroller om du vill visa en lista över föreslagna kända säkra program och grupper av datorer.

1. Öppna Azure Defender-instrumentpanelen och välj **adaptiva program kontroller** i avsnittet Avancerat skydd.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Öppna adaptiva program kontroller från Azure-instrumentpanelen" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Sidan **adaptiva program kontroller** öppnas med de virtuella datorerna grupperade på följande flikar:

    - **Konfigurerad** – grupper av datorer som redan har en definierad lista över tillåtna program. Den konfigurerade fliken i varje grupp visar:
        - antalet datorer i gruppen
        - senaste aviseringar

    - **Rekommenderade** – grupper av datorer som kör samma program konsekvent och som inte har någon konfigurerad lista över tillåtna. Vi rekommenderar att du aktiverar anpassningsbara program kontroller för dessa grupper.
    
      > [!TIP]
      > Om du ser ett grupp namn med prefixet "REVIEWGROUP" innehåller den datorer med en delvis konsekvent lista över program. Security Center kan inte se ett mönster, men du rekommenderas att granska den här gruppen för att se om _du_ kan definiera vissa regler för anpassningsbara program kontroller manuellt enligt beskrivningen i [Redigera en grupps kontroll regel för anpassningsbara program kontroller](#edit-a-groups-adaptive-application-controls-rule).
      >
      > Du kan också flytta datorer från den här gruppen till andra grupper enligt beskrivningen i [flytta en dator från en grupp till en annan](#move-a-machine-from-one-group-to-another).

    - **Ingen rekommendation** – datorer utan en definierad lista över tillåtna program och som inte stöder funktionen. Datorn kan vara på den här fliken av följande orsaker:
      - En Log Analytics agent saknas
      - Log Analytics agenten skickar inte händelser
      - Det är en Windows-dator med en befintlig [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) -princip som Aktiver ATS av antingen ett grup princip objekt eller en lokal säkerhets princip

      > [!TIP]
      > Security Center behöver minst två veckors data för att definiera de unika rekommendationerna per grupp av datorer. Datorer som nyligen har skapats, eller som tillhör prenumerationer som nyligen har Aktiver ATS med Azure Defender, visas på fliken **Ingen rekommendation** .


1. Öppna den **rekommenderade** fliken. Grupper med datorer med rekommenderade listor över tillåtna listor visas.

   ![Rekommenderad flik](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Välj en grupp. 

1. Om du vill konfigurera din nya regel granskar du de olika avsnitten på sidan **Konfigurera program kontroll regler** och innehållet, som är unikt för den här specifika gruppen med datorer:

   ![Konfigurera en ny regel](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Välj datorer** – som standard väljs alla datorer i den angivna gruppen. Avmarkera alla om du vill ta bort dem från den här regeln.
   
   1. **Rekommenderade program** – granska den här listan över program som är gemensamma för datorerna i den här gruppen och som rekommenderas att köras.
   
   1. **Fler program** – granska den här listan över program som antingen setts mindre ofta på datorerna i den här gruppen eller som är kända för att kunna utnyttjas. En varnings ikon indikerar att ett enskilt program kan användas av en angripare för att kringgå en lista över tillåtna program. Vi rekommenderar att du läser igenom programmen noggrant.

      > [!TIP]
      > Båda program listorna innehåller alternativ för att begränsa ett visst program till vissa användare. Anta principen om minsta behörighet när det är möjligt.
      > 
      > Program definieras av deras utgivare, om ett program saknar utgivar information (det är osignerat) skapas en Sök vägs regel för den fullständiga sökvägen till det specifika programmet.

   1. Om du vill tillämpa regeln väljer du **granskning**. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Redigera en grupps regel för anpassningsbar program kontroll

Du kan välja att redigera listan över tillåtna för en grupp datorer på grund av kända ändringar i din organisation. 

Redigera reglerna för en grupp datorer:

1. Öppna Azure Defender-instrumentpanelen och välj **adaptiva program kontroller** i avsnittet Avancerat skydd.

1. Från fliken **konfigurerad** väljer du gruppen med den regel som du vill redigera.

1. Granska de olika avsnitten på sidan **Konfigurera program kontroll regler** enligt beskrivningen i [Aktivera anpassningsbara program kontroller i en grupp datorer](#enable-application-controls-on-a-group-of-machines).

1. Du kan också lägga till en eller flera anpassade regler:

   1. Välj **Lägg till regel**.

      ![Lägg till en anpassad regel](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Om du definierar en känd säker sökväg ändrar du **regel typen** till sökväg och anger en enda sökväg. Du kan inkludera jokertecken i sökvägen.
   
      > [!TIP]
      > Vissa scenarier för vilka jokertecken i en sökväg kan vara användbara:
      > 
      > * Använda jokertecken i slutet av en sökväg för att tillåta alla körbara filer i den här mappen och undermapparna.
      > * Använd ett jokertecken i mitten av en sökväg för att aktivera ett känt körbart namn med ett ändrat mappnamn (till exempel personliga användarmappar som innehåller en känd körbar fil, automatiskt genererade mappnamn osv.).
  
   1. Definiera tillåtna användare och skyddade fil typer.

   1. När du är klar med att definiera regeln väljer du **Lägg till**.

1. Om du vill tillämpa ändringarna väljer du **Spara**.


## <a name="review-and-edit-a-groups-settings"></a>Granska och redigera en grupps inställningar

1. Om du vill visa information och inställningar för gruppen väljer du **grupp inställningar**

    I det här fönstret visas namnet på gruppen (som kan ändras), OS-typen, platsen och annan relevant information.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Sidan grupp inställningar för anpassningsbara program kontroller" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. Du kan också ändra gruppens namn eller skydds lägen för filtyper.

1. Välj **tillämpa** och **Spara**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Svara på "tillåten regler i din princip för anpassningsbar program kontroll ska uppdateras" rekommendation

Den här rekommendationen visas när Security Center Machine Learning identifierar potentiellt legitima beteenden som inte tidigare har tillåtits. Rekommendationen föreslår nya regler för dina befintliga definitioner för att minska antalet falska positiva varningar.

Så här åtgärdar du problemen:

1. På sidan rekommendationer väljer du regler för **tillåten i din princip för anpassningsbar program kontroll bör du uppdatera** rekommendationen för att se grupper med nyligen identifierat, potentiellt legitima beteenden.

1. Välj gruppen med den regel som du vill redigera.

1. Granska de olika avsnitten på sidan **Konfigurera program kontroll regler** enligt beskrivningen i [Aktivera anpassningsbara program kontroller i en grupp datorer](#enable-application-controls-on-a-group-of-machines).

1. Om du vill tillämpa ändringarna väljer du **granskning**.




## <a name="audit-alerts-and-violations"></a>Granska aviseringar och överträdelser

1. Öppna Azure Defender-instrumentpanelen och välj **adaptiva program kontroller** i avsnittet Avancerat skydd.

1. Om du vill se grupper med datorer som har nya aviseringar granskar du grupperna som visas på fliken **konfigurerad** .

1. Välj en grupp för att undersöka ytterligare.

   ![Senaste aviseringar](./media/security-center-adaptive-application/recent-alerts.png)

1. Om du vill ha mer information och listan över berörda datorer väljer du en avisering.

    Sidan aviseringar visar mer information om aviseringarna och ger en **Åtgärds** länk med rekommendationer för hur du minimerar hotet.

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="Start tiden för aviseringar om anpassningsbara program kontroller är ":::

    > [!NOTE]
    > Anpassningsbara program kontroller beräknar händelser en gång var tolfte timme. "Aktivitetens start tid" som visas på sidan aviseringar är den tid som anpassade program kontroller skapade aviseringen, **inte** den tid då den misstänkta processen var aktiv.


## <a name="move-a-machine-from-one-group-to-another"></a>Flytta en dator från en grupp till en annan

När du flyttar en dator från en grupp till en annan så ändras den program kontroll princip som tillämpas på den till inställningarna för den grupp som du flyttade den till. Du kan också flytta en dator från en konfigurerad grupp till en icke-konfigurerad grupp. om du gör det tas alla program kontroll regler bort som har tillämpats på datorn.

1. Öppna Azure Defender-instrumentpanelen och välj **adaptiva program kontroller** i avsnittet Avancerat skydd.

1. På sidan **adaptiva program kontroller** går du till fliken **konfigurerad** och väljer den grupp som innehåller den dator som ska flyttas.

1. Öppna listan över  **konfigurerade datorer**.

1. Öppna menyn på datorn från tre punkter i slutet av raden och välj **Flytta**. Fönstret **flytta datorn till en annan grupp** öppnas.

1. Välj mål gruppen och välj **Flytta dator**.

1. Välj **Spara** för att spara ändringarna.





## <a name="manage-application-controls-via-the-rest-api"></a>Hantera program kontroller via REST API 

Använd vår REST API för att hantera dina anpassningsbara program kontroller program mässigt. 

Relevant API-dokumentation finns i [avsnittet adaptiva program kontroller i Security Center s API](/rest/api/securitycenter/adaptiveapplicationcontrols)-dokumentation.

Några av de funktioner som är tillgängliga från REST API:

* **Listan** hämtar alla dina grupp rekommendationer och tillhandahåller ett JSON-objekt med ett objekt för varje grupp.

* **Get** hämtar JSON med fullständiga rekommendations data (det vill säga en lista över datorer, utgivare/Sök vägs regler osv.).

* **Lägg** till konfigurerar din regel (Använd JSON som du hämtade med **Get** som brödtext för den här förfrågan).
 
   > [!IMPORTANT]
   > Funktionen **infört** förväntar sig färre parametrar än den JSON som returneras av Get-kommandot innehåller.
   >
   > Ta bort följande egenskaper innan du använder JSON i begäran om att skicka: recommendationStatus, configurationStatus, Issues, Location och sourceSystem.


## <a name="faq---adaptive-application-controls"></a>Vanliga frågor och anpassningsbara program kontroller

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>Finns det några alternativ för att genomdriva program kontrollerna?
Inga tvingande alternativ är tillgängliga för närvarande. Anpassningsbara program kontroller är avsedda att ge **säkerhets aviseringar** om andra program körs än de som du har definierat som säkra. De har ett antal fördelar ([vilka är fördelarna med anpassningsbara program kontroller?](#what-are-the-benefits-of-adaptive-application-controls)) och är mycket anpassningsbara som de visas på den här sidan.

 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du använder adaptiv-Programkontroll i Azure Security Center för att definiera listor över tillåtna program som körs på dina Azure-och icke-Azure-datorer. Mer information om några av Security Centers andra moln skydds funktioner finns i:

* [Förstå VM-åtkomst med just-in-time (JIT)](just-in-time-explained.md)
* [Skydda dina Azure Kubernetes-kluster](defender-for-kubernetes-introduction.md)