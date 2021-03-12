---
title: Använd portalen för underhålls meddelanden
description: Visa underhålls meddelanden för virtuella datorer som körs i Azure och starta självbetjänings underhållet med hjälp av portalen.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557717"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Hantera meddelanden för planerat underhåll med hjälp av portalen

**Den här artikeln gäller för virtuella datorer som kör både Linux och Windows.**

När du har schemalagt en [planerad underhålls](maintenance-notifications.md) våg kan du söka efter en lista över virtuella datorer som påverkas. 

Du kan använda Azure Portal och leta efter virtuella datorer som är schemalagda för underhåll.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Klicka på **Virtual Machines** i det vänstra navigerings fältet.

3. Öppna listan med tillgängliga kolumner genom att välja **Redigera kolumner** i rutan Virtual Machines.

4. Markera och Lägg till följande kolumner:

   **Underhålls status**: visar underhålls status för den virtuella datorn. Följande är de potentiella värdena:
      
    | Värde | Beskrivning |
    |-------|-------------|
    | Starta nu | Den virtuella datorn är i fönstret för självbetjänings underhåll där du kan starta underhållet själv. Se nedan om hur du startar underhåll på den virtuella datorn. | 
    | Schemalagd | Den virtuella datorn är schemalagd för underhåll utan möjlighet att initiera underhåll. Du kan lära dig underhålls fönstret genom att välja den underhålls schemalagda perioden i den här vyn eller genom att klicka på den virtuella datorn. | 
    | Redan uppdaterat | Den virtuella datorn har redan uppdaterats och ingen ytterligare åtgärd krävs för tillfället. | 
    | Försök igen senare | Du har initierat underhåll utan att lyckas. Du kommer att kunna använda alternativet för självbetjänings underhåll vid ett senare tillfälle. | 
    | Försök igen nu | Du kan försöka med ett tidigare misslyckat självinitierat underhåll. | 
    | - | Din virtuella dator ingår inte i en planerad underhålls våg. |

   **Underhåll – självbetjänings fönstret**: visar tidsfönstret när du kan starta underhållet på dina virtuella datorer själv.
   
   **Underhåll – schemalagt fönster**: visar tidsfönstret när Azure ska underhålla din virtuella dator för att slutföra underhållet. 



## <a name="notification-and-alerts-in-the-portal"></a>Aviseringar och aviseringar i portalen

Azure kommunicerar ett schema för planerat underhåll genom att skicka ett e-postmeddelande till Prenumerationens ägare och gruppen med medägare. Du kan lägga till ytterligare mottagare och kanaler i den här kommunikationen genom att skapa Azure aktivitets logg aviseringar. Mer information finns i [skapa aktivitets logg aviseringar för tjänst meddelanden](../service-health/alerts-activity-log-service-notifications-portal.md).

Se till att ange **händelse typ** som **planerat underhåll** och **tjänster** som **Virtual Machine Scale Sets** och/eller **Virtual Machines**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Starta underhåll på den virtuella datorn från portalen

När du tittar på informationen om den virtuella datorn kommer du att kunna se mer underhålls information.  
Längst upp i vyn VM-information läggs ett nytt meddelande band till om din virtuella dator ingår i en planerad underhålls våg. Dessutom läggs ett nytt alternativ till för att starta underhåll när det är möjligt. 


Klicka på underhålls meddelandet för att se underhålls sidan med mer information om planerat underhåll. Därifrån kommer du att kunna **Starta underhåll** på den virtuella datorn.

När du startar underhållet underhålls den virtuella datorn och underhålls statusen uppdateras för att avspegla resultatet inom några minuter.

Om du missade fönstret för självbetjäning kommer du fortfarande att kunna se fönstret när den virtuella datorn kommer att behållas av Azure. 


## <a name="next-steps"></a>Nästa steg

Du kan också hantera planerat underhåll med hjälp av [Azure CLI](maintenance-notifications-cli.md) eller [PowerShell](maintenance-notifications-powershell.md).
