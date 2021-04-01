---
title: Schemalägg dina jobb
description: Använd jobb schemaläggning för att hantera dina uppgifter.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 7da3c78e00f5d7e41a5396603cf4885a50cb6e5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89146359"
---
# <a name="schedule-jobs-for-efficiency"></a>Schemalägg jobb för bättre effektivitet

Genom att schemalägga batch-jobb kan du prioritera de jobb som du vill köra först och samtidigt ta hänsyn till uppgifter som är beroende av andra uppgifter. Genom att schemalägga jobben kan du se till att du använder minst en mängd resurser. Noder kan inaktive ras när de inte behövs, men uppgifter som är beroende av andra aktiviteter är att du ska kunna optimera arbets flödena i tid. Endast ett jobb i taget körs. Det går inte att starta en ny förrän den tidigare en är klar. Du kan ställa in jobbet till Autoavsluta. 

## <a name="benefit-of-job-scheduling"></a>Förmån för jobb schemaläggning

Fördelen med att schemalägga jobb är att du kan ange ett schema för skapande av jobb. Aktiviteterna som du schemalägger med jobb Manager-aktiviteten är kopplade till ett jobb. Åtgärden Job Manager skapar aktiviteter för jobbet. För att göra det måste jobb hanterarens uppgift autentisera med batch-kontot. Använd AZ_BATCH_AUTHENTICATION_TOKEN åtkomsttoken. Token kommer att tillåta åtkomst till resten av jobbet. 

## <a name="use-the-portal-to-schedule-a-job"></a>Använda portalen för att schemalägga ett jobb

   1. Logga in på [Azure Portal](https://portal.azure.com/).

   2. Välj det batch-konto som du vill schemalägga jobb i.

   3. Välj **Lägg till** för att skapa ett nytt jobb schema och slutför **formuläret grundläggande**.



![Schemalägga ett jobb][1]

**Jobb schema-ID**: den unika identifieraren för detta jobb schema.

**Visnings namn**: visnings namnet för jobbet behöver inte vara unikt men får innehålla högst 1024 tecken.

**Kör inte förrän**: anger den tidigaste tiden som jobbet ska köras. Om du inte anger detta blir schemat redo att köra jobb direkt.

**Kör inte efter**: inga jobb körs efter den tid som du har angett här. Om du inte anger någon tid skapar du ett återkommande jobb schema som är aktivt tills du avslutar det explicit.

**Upprepnings intervall**: du kan ange hur lång tid som ska vara mellan jobben. Du kan bara ha ett jobb vid en schemalagd tidpunkt, så om det är dags att skapa ett nytt jobb under ett jobb schema, men det tidigare jobbet körs, kommer batch-tjänsten inte att skapa det nya jobbet förrän det tidigare jobbet har slutförts.  

**Start fönster**: här anger du tidsintervallet, med början från den tidpunkt då schemat anger att ett jobb ska skapas tills det ska slutföras. Nästa jobb startar inte om det aktuella jobbet inte slutförs under fönstret.

Längst ned i formuläret Basic kommer du att ange den pool som du vill att jobbet ska köras på. Välj **Uppdatera** för att hitta din pool-ID-information. 

![Ange pool][2]


**Pool-ID**: identifiera poolen som du ska köra jobbet på.

**Jobb konfigurations aktivitet**: Välj **Uppdatera** för att namnge jobb Manager-aktiviteten samt jobb förberedelse-och publicerings uppgifter, om du använder dem.

**Prioritet**: ge jobbet en prioritet.

**Maximal tid för vägg klocka**: Ange den maximala tid som jobbet kan köras för. Om den inte slutförs inom tids perioden avslutar batch jobbet. Om du inte anger detta finns det ingen tids gräns för jobbet.

**Max antal nya försök för aktiviteter**: Ange hur många gånger en aktivitet kan upprepas upp till högst fyra gånger. Detta är inte samma som antalet nya försök som ett API-anrop kan ha.

**När alla aktiviteter har slutförts**: standard är ingen åtgärd.

**När en aktivitet Miss lyckas**: standardvärdet är ingen åtgärd. En aktivitet Miss lyckas om antalet återförsök är förbrukat eller när aktiviteten startades. 

När du har valt **Spara**, om du går till **jobb scheman** i det vänstra navigerings fönstret, kan du spåra körningen av jobbet genom att välja **körnings information**.


## <a name="for-more-information"></a>Mer information

Information om hur du hanterar ett jobb med hjälp av Azure CLI finns i [AZ batch job-Schedule](/cli/azure/batch/job-schedule).

## <a name="next-steps"></a>Nästa steg

[Skapa aktivitets beroenden för att köra uppgifter som är beroende av andra uppgifter](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


