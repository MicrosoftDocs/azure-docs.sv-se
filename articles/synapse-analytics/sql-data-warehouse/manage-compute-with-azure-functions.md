---
title: 'Självstudie: hantera beräkning med Azure Functions'
description: Använda Azure Functions för att hantera beräkningen av din dedikerade SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f0731f0deaf46ec419cfe43037804e10f2b73fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96448383"
---
# <a name="use-azure-functions-to-manage-compute-resources-for-your-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Använd Azure Functions för att hantera beräknings resurser för din dedikerade SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

I den här självstudien används Azure Functions för att hantera beräknings resurser för en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.

Om du vill använda en Azure-Funktionsapp med en dedikerad SQL-pool (tidigare SQL DW) måste du skapa ett [konto för tjänstens huvud namn](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Tjänstens huvud konto måste ha deltagar åtkomst under samma prenumeration som din dedikerade SQL-pool (tidigare SQL DW)-instans.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Distribuera timer-baserad skalning med en Azure Resource Manager-mall

Om du vill distribuera mallen behöver du följande information:

- Namnet på resurs gruppen som din dedikerade SQL-pool (tidigare SQL DW)-instans finns i
- Namnet på servern som din dedikerade SQL-pool (tidigare SQL DW)-instans finns i
- Namnet på din dedikerade SQL-pool (tidigare SQL DW)-instans
- Klient-ID (katalog-ID) för din Azure Active Directory
- Prenumerations-ID:t
- Program-ID för tjänstens huvudkonto
- Hemlig nyckel för tjänstens huvudkonto

När du har föregående information distribuerar du den här mallen:

[![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

När du har distribuerat mallen bör du hitta tre nya resurser: ett kostnads fritt Azure App Service plan, en förbruknings-baserad Funktionsapp-plan och ett lagrings konto som hanterar loggningen och drift kön. Fortsätta att läsa mer i andra avsnitt om du vill se hur du ändrar de distribuerade funktionerna så att de passar dina behov.

## <a name="change-the-compute-level"></a>Ändra beräknings nivån

1. Gå till funktionsapptjänsten. Om du har distribuerat mallen med standardvärden ska tjänsten ha namnet *DWOperations*. När funktionsappen är öppen bör du se fem funktioner som har driftsatts inom ramen för din funktionsapptjänst.

   ![Funktioner som distribueras med mallen](./media/manage-compute-with-azure-functions/five-functions.png)

2. Välj antingen *DWScaleDownTrigger* eller *DWScaleUpTrigger* för att skala upp eller ned. Välj integrera i den nedrullningsbara menyn.

   ![Välj Integrera för funktionen](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Det aktuella värdet ska antingen vara *%ScaleDownTime%* eller *%ScaleUpTime%*. Dessa värden anger att schemat baseras på värden som definierats i dina [programinställningar](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). För närvarande kan du ignorera det här värdet och ändra schemat till önskad tid baserat på nästa steg.

4. I schema-ytan lägger du till CRON-uttrycket som du vill visa hur ofta du vill att Azure Synapse Analytics ska skalas upp.

   ![Ändra funktionsschemat](./media/manage-compute-with-azure-functions/change-schedule.png)

   Värdet för `schedule` är ett [CRON-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression) som innehåller dessa sex fält:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Exempel: *"0 30 9 * * 1-5"* visar en utlösare varje vardag kl. 9:10:30. Mer information finns i Azure Functions [schemaexempel](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example).

## <a name="change-the-time-of-the-scale-operation"></a>Ändra tid för skalnings åtgärden

1. Gå till funktionsapptjänsten. Om du har distribuerat mallen med standardvärden ska tjänsten ha namnet *DWOperations*. När funktionsappen är öppen bör du se fem funktioner som har driftsatts inom ramen för din funktionsapptjänst.

2. Välj antingen *DWScaleDownTrigger* eller *DWScaleUpTrigger* för att skala upp eller ned Compute-värdet. När du har valt funktionerna ska fönstret innehålla filen *index.js*.

   ![Ändra beräkningsnivån för funktionsutlösaren](././media/manage-compute-with-azure-functions/index-js.png)

3. Ändra värdet för *ServiceLevelObjective* till den nivå som du vill använda och välj Spara. *ServiceLevelObjective* är den beräknings nivå som data lager instansen skalar till baserat på det schema som definierats i avsnittet integrera.

## <a name="use-pause-or-resume-instead-of-scale"></a>Använd funktionerna Pausa eller Återuppta istället för Skala

Standardfunktionerna är för närvarande *DWScaleDownTrigger* och *DWScaleUpTrigger*. Om du vill använda funktionerna för att pausa och återuppta istället kan du aktivera *DWPauseTrigger* eller *DWResumeTrigger*.

1. Gå till  funktionsfönstret.

   ![Funktionsfönstret](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Välj vid glidningen för de motsvarande utlösare som du vill aktivera.

3. Navigera till fliken *Integrera* för respektive utlösare om du vill ändra schemat.

   > [!NOTE]
   > Den funktionella skillnaden mellan skalnings utlösare och paus-/återställnings utlösare är meddelandet som skickas till kön. Mer information finns i [lägga till en ny utlösare](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Lägg till en ny utlösare

För närvarande ingår bara två skalningsfunktioner i mallen. Med dessa funktioner, under en dag, kan du bara skala ned en gång och en gång. Om du vill ha mer detaljerad kontroll, till exempel skala ned flera gånger per dag eller om du har olika skalnings beteende på helgerna, måste du lägga till en annan utlösare.

1. Skapa en ny tom funktion. Välj *+* knappen nära funktions platsen för att visa fönstret funktion mall.

   ![Skärm bild som visar menyn "funktions program" med ikonen "plus" bredvid "Functions" markerade.](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Från språk väljer du *Java Script* och väljer sedan *TimerTrigger*.

   ![Skapa ny funktion](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Namnge din funktion och konfigurera ett schema. Bilden visar hur man utlöser en funktion varje lördag vid midnatt (sent fredag kväll).

   ![Skala ned på lördag](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Kopiera innehållet i *index.js* från någon av de andra utlösarfunktionerna.

   ![Kopiera index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Ställ in din åtgärds variabel på önskat beteende enligt följande:

   ```JavaScript
   // Resume the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the dedicated SQL pool (formerly SQL DW)l instance to DW600c
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600c"
   }
   ```

## <a name="complex-scheduling"></a>Komplex schemaläggning

Det här avsnittet visar kortfattat vad som krävs för att få en mer komplex schemaläggning av funktioner för att pausa, återuppta och skala.

### <a name="example-1"></a>Exempel 1

Dags att skala upp till 8.00 på DW600c och skala ned till DW200c.

| Funktion  | Schema     | Åtgärd                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600c"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-2"></a>Exempel 2

Dags att skala upp på 8.00 till DW1000c, skala ned en gång till DW600 vid 4pm och skala ned på 10pm till DW200c.

| Funktion  | Schema     | Åtgärd                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-3"></a>Exempel 3

Skala upp på 8.00 till DW1000c, skala ned en gång till DW600c vid 4pm på vardagarna. Pausas fredagar kl. 23.00, återupptas kl. 07.00 på måndag morgon.

| Funktion  | Schema       | Åtgärd                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 1    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Nästa steg

Läs mer om [timer-utlösare](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure Functions.

Se dedikerad SQL-pool (tidigare SQL DW) [exempel lagrings plats](https://github.com/Microsoft/sql-data-warehouse-samples).
