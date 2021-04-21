---
title: Azure Key Vault övervakning och | Microsoft Docs
description: Skapa en instrumentpanel för att övervaka hälsotillståndet för nyckelvalvet och konfigurera aviseringar.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: f8f9dd6d51b974ebd31804daf0402ca5535ffc92
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751588"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Övervakning och avisering för Azure Key Vault

## <a name="overview"></a>Översikt

När du har börjat använda nyckelvalvet för att lagra dina produktionshemligheter är det viktigt att övervaka hälsotillståndet för nyckelvalvet för att se till att tjänsten fungerar som avsett. När du börjar skala tjänsten ökar antalet begäranden som skickas till ditt nyckelvalv. Detta kan öka svarstiden för dina begäranden och i extrema fall kan dina begäranden begränsas, vilket påverkar tjänstens prestanda. Du måste också få en avisering om nyckelvalvet skickar ett ovanligt antal felkoder, så att du snabbt kan meddelas om eventuella problem med åtkomstprinciper eller brandväggskonfigurationer. Det här dokumentet innehåller följande avsnitt:

+ Grundläggande Key Vault mått att övervaka
+ Så här konfigurerar du mått och skapar en instrumentpanel
+ Så här skapar du aviseringar vid angivna tröskelvärden

Azure Monitor för Key Vault kombinerar både loggar och mått för att tillhandahålla en global övervakningslösning. [Läs mer om Azure Monitor för Key Vault här](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview#introduction-to-azure-monitor-for-key-vault)

## <a name="basic-key-vault-metrics-to-monitor"></a>Grundläggande Key Vault mått att övervaka

+ Valvtillgänglighet  
+ Valvmättnad 
+ Svarstid för tjänst-API 
+ Totalt antal service-API-träffar (filtrera efter aktivitetstyp) 
+ Felkoder (filtrera efter statuskod) 

**Valvtillgänglighet** – Det här måttet bör alltid vara på 100 % det här är ett viktigt mått att övervaka, eftersom det snabbt kan visa dig om ditt nyckelvalv har drabbats av ett avbrott. 

**Valvmättnad** – Antalet begäranden per sekund som ett nyckelvalv kan betjäna baseras på vilken typ av åtgärd som utförs. Vissa valvåtgärder har ett lägre tröskelvärde för begäranden per sekund. Det här måttet aggregerar den totala användningen av nyckelvalvet för alla åtgärdstyper för att få fram ett procentvärde som anger din aktuella nyckelvalvsanvändning. En fullständig lista över tjänstbegränsningar för nyckelvalv finns i följande dokument. [Azure Key Vault tjänstbegränsningar](service-limits.md)

**Tjänst-API-svarstid** – Det här måttet visar den genomsnittliga svarstiden för anrop till nyckelvalvet, mätt i tjänsten. Den omfattar inte tid som förbrukas av klienten eller av nätverket mellan klienten och tjänsten.

**Totalt antal API-träffar** – Det här måttet visar alla anrop som görs till ditt nyckelvalv. På så sätt kan du identifiera vilka program som anropar ditt nyckelvalv. 

**Felkoder** – Det här måttet visar om det uppstår ovanliga fel i nyckelvalvet. En fullständig lista över felkoder och felsökningsvägledning finns i följande dokument. [Azure Key Vault REST API felkoder](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Så här konfigurerar du mått och skapar en instrumentpanel

1. Logga in på Azure-portalen
2. Gå till Key Vault
3. Välj **Mått** under **Övervakning** 

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar alternativet Mått i avsnittet Övervakning.](../media/alert-1.png)

4. Uppdatera diagrammets rubrik till det du vill se på instrumentpanelen. 
5. Välj omfånget. I det här exemplet väljer vi ett enda nyckelvalv. 
6. Välj Mått för **övergripande valvtillgänglighet och** aggregeringsav **medelvärde** 
7. Uppdatera tidsintervallet till De senaste 24 timmarna och uppdatera tidskornigheten till 1 minut. 

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar måttet Tillgänglighet för det övergripande valvet.](../media/alert-2.png)

8. Upprepa stegen ovan för måtten Valvmättnad och Svarstid för tjänst-API. Välj **Fäst på instrumentpanelen** för att spara dina mått på en instrumentpanel. 

> [!IMPORTANT]
> Välj "Fäst på instrumentpanelen" och spara alla mått som du konfigurerar. Om du lämnar sidan och återgår till den utan att spara kommer dina konfigurationsändringar att gå förlorade. 

9. Om du vill övervaka alla typer av åtgärder i nyckelvalvet använder du måttet Total Service API Hits (Totalt **antal service-API-träffar)** och väljer Apply Splitting by Activity Type (Tillämpa **uppdelning efter aktivitetstyp)**

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar knappen Tillämpa delning.](../media/alert-3.png)

10. Om du vill övervaka felkoder i nyckelvalvet använder du **måttet Total Service API Results** (Resultatmått för Total Service API) och väljer Apply Splitting by Activity Type **(Tillämpa uppdelning efter aktivitetstyp)**

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar det valda måttet Total Service API Results (Resultat för Totalt antal service-API:er).](../media/alert-4.png)

Nu har du en instrumentpanel som ser ut så här. Du kan klicka på de tre punkterna längst upp till höger på varje panel och du kan ordna om och ändra storlek på panelerna efter behov. 

När du sparar och publicerar instrumentpanelen skapas en ny resurs i din Azure-prenumeration. Du kan se den när som helst genom att söka efter "delad instrumentpanel". 

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar den publicerade instrumentpanelen.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Så här konfigurerar du aviseringar på Key Vault 

Det här avsnittet visar hur du konfigurerar aviseringar i nyckelvalvet så att du kan avisering till ditt team att vidta åtgärder omedelbart om ditt nyckelvalv är i ett felaktigt tillstånd. Du kan konfigurera aviseringar som skickar ett e-postmeddelande, helst till ett team-DL, skickar ett Event Grid-meddelande eller ringer eller sms:ar ett telefonnummer. Du kan också välja statiska aviseringar baserat på ett fast värde eller en dynamisk avisering som varnar dig om ett övervakat mått överskrider den genomsnittliga gränsen för ditt nyckelvalv ett visst antal gånger inom ett definierat tidsperiod. 

> [!IMPORTANT]
> Observera att det kan ta upp till 10 minuter innan nyligen konfigurerade aviseringar börjar skicka meddelanden. 

### <a name="configure-an-action-group"></a>Konfigurera en åtgärdsgrupp 

En åtgärdsgrupp är en konfigurerbar lista över meddelanden och egenskaper.

1. Logga in på Azure-portalen
2. Sök **efter aviseringar** i sökrutan
3. Välj **Hantera åtgärder**

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar knappen Hantera åtgärder.](../media/alert-6.png)

4. Välj **+ Lägg till åtgärdsgrupp**

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar knappen + Lägg till åtgärdsgrupp.](../media/alert-7.png)

5. Välj **åtgärdstyp** för din åtgärdsgrupp. I det här exemplet skapar vi en e-postavisering.

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar de fält som behövs för att lägga till en åtgärdsgrupp.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar vad som behövs för att lägga till en avisering via e-post eller SMS.](../media/alert-9.png)

6. Klicka på **OK** längst ned på sidan. Du har skapat en åtgärdsgrupp. 

Nu när du har konfigurerat en åtgärdsgrupp konfigurerar vi tröskelvärdena för nyckelvalvsaviseringar. 

### <a name="configure-alert-thresholds"></a>Konfigurera tröskelvärden för aviseringar 

1. Välj din nyckelvalvsresurs i Azure Portal och välj **Aviseringar** under **Övervakning**

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar menyalternativet Aviseringar under avsnittet Övervakning.](../media/alert-10.png)

2. Välj **Ny aviseringsregel**

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar knappen + Ny aviseringsregel.](../media/alert-11.png)

3. Välj omfånget för din aviseringsregel. Du kan välja ett enda valv eller flera. 

> [!IMPORTANT]
> Observera att när du väljer flera valv för aviseringarna måste alla valda valv finnas i samma region. Du måste konfigurera separata aviseringsregler för valv i olika regioner. 

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar hur du kan välja ett valv.](../media/alert-12.png)

4. Välj villkor för dina aviseringar. Du kan välja någon av följande signaler och definiera logiken för aviseringar. Teamet Key Vault rekommenderar att du konfigurerar följande tröskelvärden för aviseringar. 

    + Key Vault tillgänglighet sjunker under 100 % (statiskt tröskelvärde)
    + Key Vault svarstiden är större än 500 ms (statiskt tröskelvärde) 
    + Övergripande valvmättnad är större än 75 % (statiskt tröskelvärde) 
    + Övergripande valvmättnad överskrider genomsnittet (dynamiskt tröskelvärde)
    + Totalt antal felkoder som är högre än genomsnittet (dynamiskt tröskelvärde) 

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar var du väljer villkor för aviseringar.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Exempel 1: Konfigurera ett tröskelvärde för statiska aviseringar för svarstid

Välj **Övergripande svarstid för tjänst-API** som signalnamn
> [!div class="mx-imgBorder"]
> ![Skärmbild som visar signalnamnet För övergripande tjänst-API-svarstid.](../media/alert-14.png)

Se följande konfigurationsparametrar.

+ Ange Tröskelvärde till **Statisk** 
+ Ange operatorn till **Större än**
+ Ange Sammansättningstyp till **Genomsnitt**
+ Ange tröskelvärdet till **500**
+ Ange aggregeringsperiod **till 5 minuter**
+ Ange Utvärderingsfrekvens till **1 minut**
+ Välj **Klar**  

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar den konfigurerade aviseringslogiken.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Exempel 2: Konfigurera ett tröskelvärde för dynamisk avisering för valvmättnad 

När du använder en dynamisk avisering kan du se historiska data för nyckelvalvet som du har valt. Det blå området representerar den genomsnittliga användningen av nyckelvalvet. Det röda området visar toppar som skulle ha utlöst en avisering förutsatt att andra kriterier i aviseringskonfigurationen uppfylls. De röda punkterna visar instanser av överträdelser där kriterierna för aviseringen uppfylldes under den aggregerade tidsperioden. Du kan ange att en avisering ska utbranda efter ett visst antal överträdelser inom en viss tid. Om du inte vill inkludera tidigare data finns det ett alternativ för att undanta gamla data nedan i avancerade inställningar. 

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar ett diagram över den övergripande valvmättnad.](../media/alert-16.png)

Se följande konfigurationsparametrar.

+ Ange Tröskelvärde till **Dynamisk** 
+ Ange operatorn till **Större än**
+ Ange Sammansättningstyp till **Genomsnitt**
+ Ange Tröskelvärdeskänslighet till **Medel**
+ Ange aggregeringsperiod **till 5 minuter**
+ Ange Utvärderingsfrekvens till **1 minut**
+ **Valfritt** Konfigurera avancerade inställningar 
+ Välj **Klar**

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure Portal](../media/alert-17.png)

5. Lägg till den åtgärdsgrupp som du har konfigurerat

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar hur du lägger till en åtgärdsgrupp.](../media/alert-18.png)

6. Aktivera aviseringen och tilldela en allvarlighetsgrad

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar var du aktiverar aviseringen och tilldelar en allvarlighetsgrad.](../media/alert-19.png)

7. Skapa aviseringen 

### <a name="example-email-alert"></a>Exempel på e-postavisering 

> [!div class="mx-imgBorder"]
> ![Skärmbild som visar den information som behövs för att konfigurera en e-postavisering.](../media/alert-20.png)

## <a name="next-steps"></a>Nästa steg

Grattis, nu har du skapat en instrumentpanel för övervakning och konfigurerat aviseringar för nyckelvalvet! När du har följt alla steg ovan bör du få e-postaviseringar när ditt nyckelvalv uppfyller de aviseringsvillkor som du har konfigurerat. Ett exempel på detta visas nedan. Använd de verktyg som du har skapat i den här artikeln för att aktivt övervaka hälsotillståndet för ditt nyckelvalv. 


