---
title: Översikt över klassiska varningar i Azure Monitor
description: Klassiska varningar är inaktuella. Med aviseringar kan du övervaka Azures resurs mått, händelser eller loggar och meddelas när ett villkor som du anger är uppfyllt.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: 82923b461980fbc0740cad08a9e699765f17abc7
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525525"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Vad är klassiska aviseringar i Microsoft Azure?

> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre klassiska mått varningar. Azure Monitor har nu stöd för [nyare aviseringar i nästan real tid och nya aviserings upplevelser](./alerts-overview.md). Klassiska aviseringar [dras tillbaka](./monitoring-classic-retirement.md) för offentliga moln användare, men fortfarande i begränsad användning för resurser som ännu inte stöder de nya aviseringarna.
>

Med aviseringar kan du konfigurera villkor över data och bli informerad när villkoren stämmer överens med de senaste övervaknings data.

## <a name="old-and-new-alerting-capabilities"></a>Gamla och nya aviserings funktioner

I tidigare Azure Monitor, Application Insights, Log Analytics och Service Health hade separata aviserings funktioner. Övertid, Azure förbättrar och kombinerar både användar gränssnittet och olika metoder för aviseringar. Konsolideringen pågår fortfarande.

Du kan bara Visa klassiska aviseringar på skärmen klassiska aviseringar på Azure-portalen. Du får den här skärmen från knappen **Visa klassiska aviseringar** på skärmen aviseringar. 

 ![Aviserings alternativ i Azure Portal](media/alerts-classic.overview/monitor-alert-screen2.png)

Användar upplevelsen för nya aviseringar har följande fördelar jämfört med den klassiska aviserings upplevelsen:
- **Bättre meddelande system** – alla nya aviseringar använder åtgärds grupper, som heter grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar. Klassiska mått varningar och äldre Log Analytics aviseringar använder inte åtgärds grupper.
- **En enhetlig redigerings miljö** – alla aviseringar som skapas för mått, loggar och aktivitets logg i Azure Monitor, Log Analytics och Application Insights är på ett och samma ställe.
- **Visa utlöst Log Analytics aviseringar i Azure Portal** – nu kan du även se aktiverade Log Analytics aviseringar i din prenumeration. Tidigare fanns dessa i en separat Portal.
- **Separering av utlösta aviseringar och varnings regler** – varnings regler (definitionen av villkor som utlöser en avisering) och utlösta aviseringar (en instans av varnings regeln utlöses) särskiljs, så att vyerna för drift och konfiguration separeras.
- **Bättre arbets flöde** – med den nya redigerings guiden för aviseringar kan användaren konfigurera en varnings regel, vilket gör det enklare att upptäcka de rätta sakerna för att få aviseringar.
- **Smarta aviseringar konsoliderings** -och **inställnings aviserings tillstånd**  – nya aviseringar inkluderar funktionen för automatisk gruppering som visar liknande varningar tillsammans för att minska överbelastningen i användar gränssnittet. 

De nyare mått aviseringarna har följande fördelar jämfört med de klassiska måtten:
- **Förbättrad svars tid**: nya mått aviseringar kan köras så ofta som var som än en minut. Äldre mått varningar körs alltid med en frekvens på 5 minuter. Nyare aviseringar har ökat mindre fördröjning från ärende förekomst till meddelande eller åtgärd (3 till 5 minuter). Äldre aviseringar är 5 till 15 minuter beroende på typ.  Logg aviseringar har vanligt vis 10 till 15 minuters fördröjning på grund av den tid det tar att mata in loggarna, men nyare bearbetnings metoder minskar den tiden. 
- **Stöd för** flerdimensionella mått: du kan varna på dimensionella mått så att du kan övervaka ett intressant segment av måttet.
- **Mer kontroll över mått villkor**: du kan definiera omfattande aviserings regler. De nya aviseringarna har stöd för övervakning av de högsta, lägsta, genomsnittliga och totala värdena för mått.
- **Kombinerad övervakning av flera mått**: du kan övervaka flera mått (för närvarande upp till två mått) med en enda regel. En avisering utlöses om båda måtten bryter mot respektive tröskelvärde för den angivna tids perioden.
- **Bättre meddelande system**: alla nyare aviseringar använder [Åtgärds grupper](./action-groups.md), som heter grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar.  Klassiska mått varningar och äldre Log Analytics aviseringar använder inte åtgärds grupper. 
- **Mått från loggar** (offentlig för hands version): loggdata som går till Log Analytics kan nu extraheras och omvandlas till Azure Monitor mått och sedan aviseras på precis som andra mått. Se [aviseringar (klassisk)]() för terminologin som är speciell för klassiska aviseringar. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klassiska varningar om Azure Monitor data
Det finns två typer av klassiska aviseringar tillgängliga – mått aviseringar och aktivitets logg aviseringar.

* **Klassiska mått varningar** – den här varningen utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Aviseringen genererar ett meddelande när tröskelvärdet överskrids och varnings villkoret är uppfyllt. Nu anses aviseringen vara "aktive rad". Den genererar ett nytt meddelande när det är "löst" – det vill säga när tröskelvärdet överskrids igen och villkoret inte längre uppfylls.

* **Klassisk aktivitets logg aviseringar** – en strömmande logg avisering som utlöser en händelse post i aktivitets loggen som matchar filter villkoren. De här aviseringarna har bara ett tillstånd, "aktiverat". Aviserings motorn tillämpar bara filter villkoren för nya händelser. Den söker inte efter äldre poster. De här aviseringarna kan meddela dig när en ny Service Health incident inträffar eller när en användare eller ett program utför en åtgärd i din prenumeration, till exempel "ta bort virtuell dator".

För resurs logg data som är tillgängliga via Azure Monitor dirigerar du data till Log Analytics och använder en logg frågas avisering. Log Analytics använder nu den [nya varnings metoden](./alerts-overview.md) 

I följande diagram sammanfattas data källor i Azure Monitor och hur du kan få aviseringar från dessa data.

![Förklarade aviseringar](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomi för aviseringar (klassisk)
Azure använder följande termer för att beskriva klassiska aviseringar och deras funktioner:
* **Varning** – en definition av kriterier (en eller flera regler eller villkor) som aktive ras när de är uppfyllda.
* **Aktiv** – tillstånd när villkoren som definieras av en klassisk avisering är uppfyllda.
* **Löst** – tillstånd när de villkor som definierats av en klassisk avisering inte längre uppfylls efter att de har uppfyllts.
* **Notification** – åtgärden som vidtas baserat på en klassisk avisering aktive ras.
* **Åtgärd** – ett enskilt samtal som skickas till en mottagare av ett meddelande (till exempel e-posta en adress eller en post till en webhook-URL). Meddelanden kan vanligt vis utlösa flera åtgärder.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hur gör jag för att får du ett meddelande från en Azure Monitor klassisk avisering?
Tidigare var Azure-aviseringar från olika tjänster använda sina egna inbyggda aviserings metoder. 

Azure Monitor skapade en återanvändbar meddelande grupp som kallas *Åtgärds grupper*. Åtgärds grupper anger en uppsättning mottagare för ett meddelande. När en avisering aktive ras som refererar till åtgärds gruppen får alla mottagare den aviseringen. Med åtgärds grupper kan du återanvända en gruppering av mottagare (till exempel listan över samtals tekniker) över flera aviserings objekt. Åtgärds grupper har stöd för meddelanden genom att publicera till en webhook-URL förutom e-postadresser, SMS-nummer och ett antal andra åtgärder.  Mer information finns i [Åtgärds grupper](./action-groups.md). 

Äldre klassiska aktivitets logg varningar använder åtgärds grupper.

Äldre mått varningar använder dock inte åtgärds grupper. I stället kan du konfigurera följande åtgärder: 
- Skicka e-postaviseringar till tjänst administratören, till administratörer eller till ytterligare e-postadresser som du anger.
- Anropa en webhook, som gör att du kan starta ytterligare automatiserings åtgärder.

Webhooks möjliggör automatisering och reparation, till exempel med hjälp av:
- Azure Automation – Runbook
- Azure-funktion
- Azure Logic-app
- en tjänst från tredje part

## <a name="next-steps"></a>Nästa steg
Hämta information om aviserings regler och konfigurera dem med hjälp av:

* Lär dig mer om [mått](data-platform.md)
* Konfigurera [klassiska mått varningar via Azure Portal](alerts-classic-portal.md)
* Konfigurera [klassiska mått varningar PowerShell](alerts-classic-portal.md)
* Konfigurera [klassiska mått varningar kommando rads gränssnitt (CLI)](alerts-classic-portal.md)
* Konfigurera [klassiska mått varningar Azure Monitor REST API](/rest/api/monitor/alertrules)
* Läs mer om [aktivitets logg](platform-logs-overview.md)
* Konfigurera [aktivitets logg aviseringar via Azure Portal](activity-log-alerts.md)
* Konfigurera [aktivitets logg aviseringar via Resource Manager](alerts-activity-log.md)
* Granska [aktivitets logg aviseringens webhook-schema](activity-log-alerts-webhook.md)
* Läs mer om [Åtgärds grupper](action-groups.md)
* Konfigurera [nyare aviseringar](alerts-metric.md)

