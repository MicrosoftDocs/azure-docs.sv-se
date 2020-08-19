---
title: Azure Key Vault övervakning och aviseringar | Microsoft Docs
description: Skapa en instrument panel för att övervaka hälso tillståndet för ditt nyckel valv och konfigurera aviseringar.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 6b179dd2662bf6fa545ea44b723671f1499b9e35
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589018"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Övervakning och avisering för Azure Key Vault

## <a name="overview"></a>Översikt

När du har börjat använda Key Vault för att lagra dina produktions hemligheter är det viktigt att du övervakar hälso tillståndet för nyckel valvet för att se till att tjänsten fungerar som den ska. När du börjar skala din tjänst kommer antalet förfrågningar som skickas till ditt nyckel valv att öka. Detta har möjlighet att öka svars tiden för dina begär Anden och i extrema fall, orsaka att dina förfrågningar begränsas som påverkar tjänstens prestanda. Du måste också vara aviserad om nyckel valvet skickar ett ovanligt antal felkoder, så att du snabbt kan få aviseringar om eventuella åtkomst principer eller brand Väggs konfigurations problem. I det här dokumentet behandlas följande ämnen:

+ Grundläggande Key Vault mått som ska övervakas
+ Konfigurera mått och skapa en instrument panel 
+ Så här skapar du aviseringar vid angivna tröskelvärden 

## <a name="basic-key-vault-metrics-to-monitor"></a>Grundläggande Key Vault mått som ska övervakas

+ Valv tillgänglighet  
+ Valvets mättnad 
+ Service API-svars tid 
+ Totalt antal tjänst-API-träffar (filtrera efter aktivitets typ) 
+ Felkoder (filtrera efter status kod) 

**Valv tillgänglighet** – måttet ska alltid vara på 100% det här är ett viktigt mått att övervaka, eftersom det snabbt kan visa dig om ditt nyckel valv har råkat ut för ett avbrott. 

**Valvets mättnad** – antalet begär Anden per sekund som ett nyckel valv kan betjäna baseras på den typ av åtgärd som utförs. Vissa valv åtgärder har lägre tröskelvärde per sekund. Det här måttet aggregerar den totala användningen av ditt nyckel valv över alla åtgärds typer för att komma upp med ett procent värde som anger din aktuella nyckel valvs användning. En fullständig lista över begränsningar för Key Vault-tjänsten finns i följande dokument. [Begränsningar för tjänsten Azure Key Vault](service-limits.md)

**Svars tid för service API** – det här måttet visar den genomsnittliga svars tiden för ett anrop till Key Vault. Även om nyckel valvet kan ligga inom tjänstens gränser, kan en hög användning av Key Vault introducera svars tid som gör att program upphör att fungera. 

**Totalt antal API-träffar** – det här måttet visar alla anrop som gjorts till ditt nyckel valv. Detta hjälper dig att identifiera vilka program som anropar ditt nyckel valv. 

**Felkoder** – det här måttet visar om ditt nyckel valv har drabbats av en ovanlig mängd fel. En fullständig lista över felkoder och fel söknings vägledning finns i följande dokument. [Fel koder för Azure Key Vault REST API](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Konfigurera mått och skapa en instrument panel

1. Logga in på Azure-portalen
2. Navigera till din Key Vault
3. Välj **mått** under **övervakning** 

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-1.png)

4. Uppdatera rubriken för diagrammet till det du vill se på instrument panelen. 
5. Välj en omfattning. I det här exemplet ska vi välja ett enda nyckel valv. 
6. Välj måttets **övergripande tillgänglighet för valv** och sammansättning **Genomsnittligt** 
7. Uppdatera tidsintervallet till de senaste 24 timmarna och uppdatera tids kornigheten till 1 minut. 

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-2.png)

8. Upprepa stegen ovan för måtten för valv-mättnad och service API-fördröjning. Välj **Fäst på instrument panelen** för att spara dina mått på en instrument panel. 

> [!IMPORTANT]
> Välj Fäst på instrument panelen och spara varje mått som du konfigurerar. Om du lämnar sidan och återgår till den utan att spara, kommer konfigurations ändringarna att gå förlorade. 

9. Om du vill övervaka alla typer av åtgärder i nyckel valvet använder du måttet **Total service API-träffar** och väljer **tillämpa delning efter aktivitets typ**

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-3.png)

10. Om du vill övervaka fel koderna i nyckel valvet använder du måttet **Total service API-resultat** och väljer **Använd delning efter aktivitets typ**

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-4.png)

Nu har du en instrument panel som ser ut så här. Du kan klicka på de tre punkterna längst upp till höger i varje panel och du kan arrangera om och ändra storlek på panelerna efter behov. 

När du har sparat och publicerat instrument panelen kommer den att skapa en ny resurs i din Azure-prenumeration. Du kommer att kunna se det när som helst genom att söka efter "delad instrument panel". 

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Så här konfigurerar du aviseringar på Key Vault 

I det här avsnittet visas hur du konfigurerar aviseringar i nyckel valvet så att du kan varna ditt team att vidta åtgärder omedelbart om nyckel valvet är i ett ohälsosamt tillstånd. Du kan konfigurera aviseringar som skickar ett e-postmeddelande, helst till en team DL, utlösa ett evenemangs rutnäts meddelande eller anropa eller text ett telefonnummer. Du kan också välja statiska aviseringar baserat på ett fast värde eller en dynamisk avisering som varnar dig om ett övervakat mått överskrider genomsnitts gränsen för nyckel valvet ett visst antal gånger inom ett angivet tidsintervall. 

> [!IMPORTANT]
> Observera att det kan ta upp till 10 minuter innan nyligen konfigurerade aviseringar börjar skicka meddelanden. 

### <a name="configure-an-action-group"></a>Konfigurera en åtgärds grupp 

En åtgärds grupp är en konfigurerbar lista över meddelanden och egenskaper.

1. Logga in på Azure-portalen
2. Sök efter **aviseringar** i sökrutan
3. Välj **Hantera åtgärder**

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-6.png)

4. Välj **+ Lägg till åtgärds grupp**

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-7.png)

5. Välj **Åtgärds typ** för din åtgärds grupp. I det här exemplet ska vi skapa en e-postavisering.

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-9.png)

6. Klicka på **OK** längst ned på sidan. Du har skapat en åtgärds grupp. 

Nu när du har konfigurerat en åtgärds grupp kommer vi att konfigurera tröskelvärden för nyckel valvs aviseringar. 

### <a name="configure-alert-thresholds"></a>Konfigurera tröskelvärden för aviseringar 

1. Välj din Key Vault-resurs i Azure Portal och välj **aviseringar** under **övervakning**

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-10.png)

2. Välj **ny aviserings regel**

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-11.png)

3. Välj omfattningen för aviserings regeln. Du kan välja ett enda valv eller flera. 

> [!IMPORTANT]
> Observera att alla valda valv måste finnas i samma region när du väljer flera valv för aviseringens omfattning. Du måste konfigurera separata varnings regler för valv i olika regioner. 

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-12.png)

4. Välj villkor för dina aviseringar. Du kan välja någon av följande signaler och definiera din logik för aviseringar. Key Vaults teamet rekommenderar att du konfigurerar följande tröskelvärden för aviseringar. 

    + Key Vault tillgänglighet sjunker under 100% (statisk tröskel)
    + Key Vault svars tiden är större än 500ms (statiskt tröskelvärde) 
    + Den övergripande överbeläggningen för valvet är större än 75% (statisk tröskel) 
    + Den övergripande överbeläggningen för valvet överskrider genomsnittet (dynamiskt tröskelvärde)
    + Totalt antal felkoder som är större än genomsnittet (dynamiskt tröskelvärde) 

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Exempel 1: Konfigurera en statisk aviserings tröskel för svars tid

Välj **övergripande service API-latens** som signal namn
> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-14.png)

Se följande konfigurations parametrar.

+ Ange tröskelvärdet som **statiskt** 
+ Ange att operatorn ska vara **större än**
+ Ange agg regerings typ till **genomsnitt**
+ Ange tröskelvärdet till **500**
+ Ställ in agg regerings period på **5 minuter**
+ Ange utvärderings frekvensen till **1 minut**
+ Välj **färdig**  

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Exempel 2: Konfigurera ett tröskelvärde för dynamisk avisering för valvets mättnad 

När du använder en dynamisk avisering kommer du att kunna se historiska data för det nyckel valv som du har valt. Det blå utrymmet representerar den genomsnittliga användningen av ditt nyckel valv. Det röda fältet visar toppar som skulle ha utlöst en avisering om att andra kriterier i aviserings konfigurationen är uppfyllda. De röda punkterna visar instanser av överträdelser där villkoren för aviseringen uppfylldes under den sammanställda tids perioden. Du kan ställa in en avisering för brand efter ett visst antal överträdelser inom en angiven tid. Om du inte vill inkludera tidigare data kan du välja att undanta gamla data nedan i avancerade inställningar. 

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-16.png)

Se följande konfigurations parametrar.

+ Ange tröskelvärdet till **dynamisk** 
+ Ange att operatorn ska vara **större än**
+ Ange agg regerings typ till **genomsnitt**
+ Ange tröskelvärdet känslighet för **medel**
+ Ställ in agg regerings period på **5 minuter**
+ Ange utvärderings frekvensen till **1 minut**
+ **Valfritt** Konfigurera avancerade inställningar 
+ Välj **färdig**

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-17.png)

5. Lägg till den åtgärds grupp som du har konfigurerat

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-18.png)

6. Aktivera aviseringen och tilldela en allvarlighets grad

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-19.png)

7. Skapa aviseringen 


## <a name="next-steps"></a>Nästa steg

Grattis, nu har du skapat en övervaknings instrument panel och konfigurerat aviseringar för nyckel valvet! När du har följt alla steg ovan bör du få e-postaviseringar när nyckel valvet uppfyller de aviserings villkor som du har konfigurerat. Ett exempel på detta visas nedan. Använd de verktyg som du har skapat i den här artikeln för att aktivt övervaka hälso tillståndet för ditt nyckel valv. 

### <a name="example-email-alert"></a>Exempel på e-postavisering 

> [!div class="mx-imgBorder"]
> ![Skärm bild av Azure Portal](../media/alert-20.png)
