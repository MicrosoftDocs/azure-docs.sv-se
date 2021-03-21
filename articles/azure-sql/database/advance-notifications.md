---
title: Förskotts aviseringar (för hands version) för planerade underhålls händelser
description: Få ett meddelande innan planerat underhåll för Azure SQL Database.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 07f6267a14a4604e1a43dd1a1a9930d63a419336
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690897"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Förskotts aviseringar för planerade underhålls händelser (för hands version)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Förhands aviseringar (för hands version) är tillgängligt för databaser som kon figurer ATS för [underhålls period](maintenance-window.md) Med aviseringar kan kunder Konfigurera meddelanden så att de skickas upp till 24 timmar innan eventuella planerade händelser.

Aviseringar kan konfigureras så att du kan hämta texter, e-postmeddelanden, Azure push-meddelanden och röst meddelanden när planerat underhåll är på grund av att det har börjat att börja under de närmaste 24 timmarna. Ytterligare meddelanden skickas när underhållet påbörjas och underhålls slut.

> [!Note]
> Även om möjligheten att välja en underhålls period är tillgänglig för Azure SQL-hanterade instanser, är aviseringar för hands meddelanden för närvarande inte tillgängliga för Azure SQL-hanterade instanser.

## <a name="create-an-advance-notification"></a>Skapa ett förskotts meddelande

Det finns avancerade aviseringar för Azure SQL-databaser som har kon figurer ATS för underhålls perioden. 

Utför följande steg för att aktivera en avisering.  

1. Gå till sidan [planerat underhåll](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) , Välj **hälso aviseringar** och **Lägg till tjänstens hälso avisering**.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="meny alternativet Skapa en ny hälso avisering":::

2. I avsnittet **åtgärder** väljer du **Lägg till åtgärds grupper**. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="Lägg till meny alternativ för åtgärds grupp":::

3. Slutför formuläret **skapa åtgärds grupp** och välj sedan **Nästa: meddelanden**.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="formulär för att skapa åtgärds grupp":::

1. På fliken **meddelanden** väljer du **meddelande typ**. Alternativet **e-post/SMS/push/Voice** erbjuder den mest flexibla och är det rekommenderade alternativet. Konfigurera meddelandet genom att välja pennan.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="Konfigurera meddelanden":::



   1. Slutför formuläret för att *lägga till eller redigera meddelande* som öppnas och välj **OK**: 

   2. Åtgärder och taggar är valfria. Här kan du konfigurera ytterligare åtgärder som ska utlösas eller använda taggar för att kategorisera och organisera dina Azure-resurser. 

   4. Kontrol lera informationen på fliken **Granska + skapa** och välj **skapa**. 

7. När du har valt skapa visas sidan för varnings regel konfigurationen och åtgärds gruppen väljs. Ange ett namn på den nya varnings regeln och välj sedan resurs gruppen för den och välj **skapa aviserings regel**. 

8. Klicka på meny alternativet **hälso aviseringar** igen så innehåller listan över aviseringar nu din nya avisering. 


Då var allt klart. Nästa gång det finns en planerad händelse för Azure SQL-underhåll får du ett meddelande om förskott.

## <a name="receiving-notifications"></a>Ta emot meddelanden

I följande tabell visas de allmänna informations meddelanden som du kan få: 

|Status|Beskrivning|
|:---|:---|
|**Planerad distribution**| Tog emot 24 timmar före underhålls händelsen. Underhållet planeras på DATUMET mellan 17 – 8.00 (lokal tid) för DB xyz.|
|**Pågår** | Underhåll för databas *XYZ*   startas.| 
|**Klart** | Databasen *XYZ* har underhåll ATS. |

I följande tabell visas ytterligare meddelanden som kan skickas medan underhåll pågår: 

|Status|Beskrivning|
|:---|:---|
|**Utökad** | Underhåll pågår men slutfördes inte för databas *XYZ*. Underhåll fortsätter i nästa underhålls period.| 
|**Avbrutna**| Underhåll för Database *XYZ* avbryts och kommer att schemaläggas om senare. |
|**Blockerad**|Ett problem uppstod under underhållet av databasen *XYZ*. Vi meddelar dig när vi fortsätter.| 
|**Återupptas**|Problemet har lösts och underhållet fortsätter i nästa underhålls period.|


## <a name="next-steps"></a>Nästa steg

- [Underhålls period](maintenance-window.md)
- [Vanliga frågor och svar om underhålls fönstret](maintenance-window-faq.yml)
- [Översikt över aviseringar i Microsoft Azure](../../azure-monitor/platform/alerts-overview.md)
- [E-posta Azure Resource Manager-rollen](../../azure-monitor/platform/action-groups.md#email-azure-resource-manager-role)