---
title: Felsöka fel i virtuella datorer och miljöer Azure DevTest Labs
description: Lär dig hur du felsöker fel i virtuella datorer och miljö skapande i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85476486"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Felsöka fel i den virtuella datorn (VM) och miljö skapande i Azure DevTest Labs
DevTest Labs ger dig varningar om ett dator namn är ogiltigt eller om du är på väg att bryta mot en labb princip. Ibland visas röda `X` intill din labb-VM eller miljö status som informerar dig om att något har gått fel.  Den här artikeln innehåller några knep som du kan använda för att hitta det underliggande problemet och kan förhoppnings vis undvika problemet i framtiden.

## <a name="portal-notifications"></a>Portal meddelanden
Om du använder Azure Portal, är det första stället att titta på **meddelande panelen**.  Panelen meddelanden, som är tillgänglig i huvud kommando fältet genom att klicka på **klock ikonen**, visar om den virtuella labb datorn eller miljön har skapats.  Om ett fel uppstår visas ett fel meddelande som är kopplat till felet. Informationen ger ofta ytterligare information som kan hjälpa dig att lösa problemet. I följande exempel gick det inte att skapa den virtuella datorn eftersom det inte gick att köra kärnor. I det detaljerade meddelandet får du information om hur du löser problemet och begär en ökad kärn kvot.

![Azure Portal meddelande](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM i skadat läge
Om du ser statusen för din virtuella dator i labbet som **skadad** kan den underliggande virtuella datorn ha tagits bort från den **virtuella dator** sidan som användaren kan navigera till från sidan **Virtual Machines** (inte från sidan DevTest Labs). Rensa ditt labb i DevTest Labs genom att ta bort den virtuella datorn från labbet. Återskapa sedan den virtuella datorn i labbet. 

![Virtuell dator i skadat tillstånd](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Aktivitetsloggar
Titta på aktivitets loggarna om du undersöker ett haveri när du har försökt att skapa en virtuell dator eller miljö. Det här avsnittet visar hur du hittar loggar för virtuella datorer och miljöer.

## <a name="activity-logs-for-virtual-machines"></a>Aktivitets loggar för virtuella datorer

1. På Start sidan för ditt labb väljer du den virtuella dator där du vill starta sidan för den **virtuella datorn** .
2. På sidan **virtuell dator** går du till avsnittet **övervakning** på den vänstra menyn och väljer **aktivitets logg** för att se alla loggar som är associerade med den virtuella datorn.
3. I aktivitets logg objekt väljer du den åtgärd som misslyckades. Normalt kallas den misslyckade åtgärden `Write Virtualmachines` .
4. Växla till fliken JSON i den högra rutan. Du ser informationen i loggens JSON-vy.

    ![Aktivitets logg för en virtuell dator](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Titta igenom JSON-loggen tills du hittar `statusMessage` egenskapen. Det ger dig huvud fel meddelandet och ytterligare detaljerad information, om tillämpligt. Följande JSON är ett exempel på ett fel som har inträffat i den här artikeln.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Aktivitets logg för en miljö

Följ dessa steg om du vill se aktivitets loggen för att skapa en miljö:

1. På Start sidan för ditt labb väljer du **konfiguration och principer** på den vänstra menyn.
2. på sidan **konfiguration och principer** väljer du **aktivitets loggar** på menyn.
3. Leta efter felen i aktivitets listan i loggen och markera den.
4. I den högra rutan växlar du till fliken JSON och letar efter **statusMessage**.

    ![Miljö aktivitets logg](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Distributions loggar för Resource Manager-mall
Om din miljö eller virtuella dator har skapats via Automation finns det en sista plats för att se fel information. Det är distributions loggen för Azure Resource Manager mallar. När en labb resurs skapas via Automation görs det ofta genom en Azure Resource Manager mall-distribution. Se [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) exempel Azure Resource Manager mallar som skapar DevTest Labs-resurser.

Följ dessa steg om du vill se distributions loggar för labb mal len:

1. Starta sidan för resurs gruppen där labbet finns.
2. Välj **distributioner** på den vänstra menyn under **Inställningar**.
3. Sök efter distributioner med statusen Misslyckad och markera den.
4. På sidan **distribution** väljer du länken **Åtgärds information** för den åtgärd som misslyckades.
5. Du ser information om den åtgärd som misslyckades i fönstret **Åtgärds information** .

## <a name="next-steps"></a>Nästa steg
Se [fel sökning av artefakt fel](devtest-lab-troubleshoot-artifact-failure.md)
