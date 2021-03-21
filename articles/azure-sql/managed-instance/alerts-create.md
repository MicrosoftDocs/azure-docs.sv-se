---
title: Konfigurera aviseringar och meddelanden för en hanterad instans (Azure Portal)
description: Använd Azure Portal för att skapa SQL-hanterade instans varningar, som kan utlösa meddelanden eller automatisering när de villkor du anger är uppfyllda.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: d37465223000399aa42895626c48426ddadc0c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592717"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Skapa aviseringar för Azure SQL Managed Instance i Azure-portalen
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln visar hur du konfigurerar aviseringar för databaser i Azure SQL Managed instance-databasen med hjälp av Azure Portal. Med aviseringar kan du få ett e-postmeddelande, anropa en webhook, köra en Azure-funktion eller en runbook, anropa ett externt ITSM-kompatibelt biljettsystem, bli uppringd på telefonen eller få ett SMS när ett mått, till exempel lagringsstorleken för en instans eller CPU-användning, når ett fördefinierat tröskelvärde. Den här artikeln innehåller också metod tips för att ställa in aviserings perioder.


## <a name="overview"></a>Översikt

Du kan få en avisering baserat på övervaknings mått för, eller händelser på, dina Azure-tjänster.

* **Mått värden** – aviseringen utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar i båda riktningarna. Det innebär att den utlöses både när villkoret först uppfylls och sedan efteråt när det villkoret inte längre uppfylls.

Du kan konfigurera en avisering för att göra följande när den utlöser:

* Skicka e-postaviseringar till tjänst administratör och-administratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Ring ett telefonnummer med röst meddelande
* Skicka SMS till ett telefonnummer
* Anropa en webbhook
* Anropa Azure Function
* Anropa Azure Runbook
* Anropa en extern biljett ITSM-kompatibel system

Du kan konfigurera och få information om aviserings regler med hjälp av [Azure Portal, PowerShell eller Azure CLI](../../azure-monitor/alerts/alerts-classic-portal.md) eller [Azure Monitor REST API](/rest/api/monitor/alertrules). 

## <a name="alerting-metrics-available-for-managed-instance"></a>Aviserings mått som är tillgängliga för hanterade instanser

> [!IMPORTANT]
> Aviserings mått är endast tillgängliga för hanterade instanser. Aviserings mått för enskilda databaser i den hanterade instansen är inte tillgängliga. Telemetri för databas diagnostik är tillgängligt på andra sidan i form av [diagnostikloggar](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export). Aviseringar för diagnostikloggar kan ställas in inifrån [SQL Analytics](../../azure-monitor/insights/azure-sql.md) -produkten med [logg aviserings skript](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) för hanterade instanser.

Följande mått för hanterade instanser är tillgängliga för aviserings konfiguration:

| Metric | Beskrivning | Mått enhet \ möjliga värden |
| :--------- | --------------------- | ----------- |
| Genomsnittlig CPU-procent | Genomsnittlig procent andel CPU-användning under den valda tids perioden. | 0-100 (procent) |
| Lästa IO-byte | IO-lästa byte i den valda tids perioden. | Byte |
| Skrivna IO-byte | IO-byte skrivna under den valda tids perioden. | Byte |
| Antal IO-begäranden | Antal IO-begäranden inom den valda tids perioden. | Numeriska |
| Reserverat lagrings utrymme | Aktuellt Max lagrings utrymme reserverat för den hanterade instansen. Ändringar med resurs skalnings åtgärd. | MB (megabyte) |
| Använt lagrings utrymme | Det lagrings utrymme som används under den valda perioden. Ändringar med lagrings förbrukning per databaser och instansen. | MB (megabyte) |
| Antal virtuella kärnor | Virtuella kärnor har tillhandahållits för den hanterade instansen. Ändringar med resurs skalnings åtgärd. | 4-80 (virtuella kärnor) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Skapa en varnings regel för ett mått med Azure Portal

1. I Azure [Portal](https://portal.azure.com/)letar du reda på den hanterade instans som du är intresse rad av övervakning och markerar den.

2. Meny alternativet Välj **mått** i avsnittet övervakning.

   ![Övervakning](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. På den nedrullningsbara menyn väljer du ett av de mått som du vill ställa in din avisering på (det lagrings utrymme som används visas i exemplet).

4. Välj agg regerings period – genomsnitt, lägsta eller högsta nåtts under den angivna tids perioden (i genomsnitt, min eller max). 

5. Välj **ny aviserings regel**

6. Klicka på **villkors namn** (det lagrings utrymme som används visas i exemplet) i fönstret Skapa aviserings regel.

   ![Definiera villkor](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. I fönstret Konfigurera signal logik definierar du Operator, sammansättnings typ och tröskelvärde

   * Alternativ för Operator typ är större än, lika med eller mindre än (tröskelvärdet)
   * Alternativen för agg regerings typ är min, Max eller genomsnitt (i den sammanställda granularitet perioden)
   * Tröskelvärde är det aviserings värde som ska utvärderas baserat på operatorn och agg regerings villkoren
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   I exemplet som visas i skärm bilden används värdet 1840876 MB för att representera ett tröskelvärde på 1,8 TB. Eftersom operatorn i exemplet är inställt på större än, skapas aviseringen om lagrings utrymmes förbrukningen på den hanterade instansen går över 1,8 TB. Observera att tröskelvärdet för lagrings utrymmes mått måste anges i MB.

8. Ange utvärderings perioden – agg regerings precision i minuter och utvärderings frekvens. Utvärderings frekvensen anger hur lång tid varnings systemet kommer att kontrol lera regelbundet om tröskel villkoret har uppfyllts.

9. Välj åtgärds grupp. Fönstret åtgärds grupp visas genom vilket du kommer att kunna välja en befintlig eller skapa en ny åtgärd. Den här åtgärden definierar vad som händer när en avisering aktive ras (till exempel att skicka e-post, anropa dig på telefonen, köra en webhook, Azure Function eller en Runbook, till exempel).

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Om du vill skapa en ny åtgärds grupp väljer du **+ skapa åtgärds grupp**

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Definiera hur vill du bli aviserad: Ange åtgärds grupp namn, kort namn, åtgärds namn och välj åtgärds typ. Åtgärds typen definierar om du kommer att meddelas via e-post, textmeddelande, röst samtal eller om kanske webhook, Azure Function, Runbook ska köras eller om ITSM-biljetten kommer att skapas i det kompatibla systemet.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Fyll i aviserings regel informationen för dina poster, Välj allvarlighets GRADS typ.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * Slutför skapandet av aviserings regeln genom att klicka på knappen **skapa aviserings regel** .

Den nya varnings regeln aktive ras inom några minuter och utlöses utifrån dina inställningar.

## <a name="verifying-alerts"></a>Verifierar aviseringar

> [!NOTE]
> Information om hur du Dölj brus aviseringar finns i [utelämningsintervallet-aviseringar med åtgärds regler](../../azure-monitor/alerts/alerts-action-rules.md#suppression-of-alerts).

När du ställer in en varnings regel kontrollerar du att du är nöjd med aviserings utlösaren och dess frekvens. I exemplet som visas på den här sidan för att ställa in en varning om lagrings utrymme som används, om aviserings alternativet var e-post, kan du få e-post, som visas nedan.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

E-postmeddelandet visar aviserings namnet, information om tröskeln och varför aviseringen utlöstes, vilket hjälper dig att verifiera och felsöka aviseringen. Du kan använda **se i Azure Portal** knappen för att visa aviseringar som mottagits via e-post i Azure Portal. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Visa, inaktivera, aktivera, ändra och ta bort befintliga aviserings regler

> [!NOTE]
> Befintliga aviseringar måste hanteras från menyn aviseringar från Azure Portal instrument panelen. Det går inte att ändra befintliga aviseringar från resurs bladet för en hanterad instans.

Visa, inaktivera, aktivera, ändra och ta bort befintliga aviseringar:

1. Sök efter aviseringar med hjälp av Azure Portal search. Klicka på aviseringar.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Du kan också klicka på aviseringar i navigerings fältet i Azure om du har konfigurerat det.

2. I fönstret aviseringar väljer du hantera aviserings regler.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   Lista över befintliga aviseringar visas. Välj en enskild befintlig aviserings regel för att hantera den. Befintliga aktiva regler kan ändras och justeras efter dina önskemål. Aktiva regler kan också pausas utan att tas bort. 

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Monitor varnings systemet, se [Översikt över aviseringar i Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
* Lär dig mer om mått varningar, se [förstå hur mått varningar fungerar i Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md)
* Läs om hur du konfigurerar en webhook i aviseringar i [anropa en webhook med en klassisk måtta avisering](../../azure-monitor/alerts/alerts-webhooks.md)
* Läs om hur du konfigurerar och hanterar aviseringar med hjälp av PowerShell, se [Åtgärds regler](/powershell/module/az.monitor/add-azmetricalertrulev2)
* Läs mer om hur du konfigurerar och hanterar aviseringar med hjälp av API, se [Azure Monitor REST API referens](/rest/api/monitor/)