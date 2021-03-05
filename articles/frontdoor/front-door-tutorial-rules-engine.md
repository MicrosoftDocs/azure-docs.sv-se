---
title: 'Självstudie: Konfigurera regel motor – Azure-front dörr'
description: Den här artikeln innehåller en själv studie kurs om hur du konfigurerar regel motorn i både Azure Portal och CLI.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5d26d36c9f4ecb4aa0c7114b1ebef066e104f175
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198871"
---
# <a name="tutorial-configure-your-rules-engine"></a>Självstudie: Konfigurera en regel motor

Den här självstudien visar hur du skapar en regel Motors konfiguration och din första regel i både Azure Portal och CLI. 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Konfigurera regel motor med hjälp av portalen.
> - Konfigurera regel motor med Azure CLI

## <a name="prerequisites"></a>Förutsättningar

* Innan du kan slutföra stegen i den här kursen behöver du skapa en Front Door. Mer information finns i [Snabbstart: Skapa en Front Door](quickstart-create-front-door.md).

## <a name="configure-rules-engine-in-azure-portal"></a>Konfigurera regel motor i Azure Portal
1. I din frontend-resurs går du till **Inställningar** och väljer **regel motor konfiguration**. Klicka på **Lägg till**, ge konfigurationen ett namn och börja skapa din första regel motor konfiguration.

    ![Meny för inställningar för front dörr](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Klicka på **Lägg till regel** för att skapa din första regel. Sedan kan du definiera regeln genom att klicka på **Lägg till villkor** eller **Lägg till åtgärd** .
    
    > [!NOTE]
    >- Om du vill ta bort ett villkor eller en åtgärd från en regel kan du använda pappers korgen till höger om det aktuella villkoret eller åtgärden.
    > - Om du vill skapa en regel som gäller för all inkommande trafik ska du inte ange några villkor.
    > - Om du vill sluta utvärdera reglerna när det första matchnings villkoret är uppfyllt, kontrollerar du **stoppa utvärderingen av återstående regel**. Om detta är markerat och alla matchnings villkor för en viss regel uppfylls, utförs inte de återstående reglerna i konfigurationen.  

    ![Konfiguration av regel motor](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Fastställ prioriteten för reglerna i konfigurationen med knapparna Flytta upp, flytta ned och flytta till överst. Prioriteten är i stigande ordning, vilket innebär att regeln först anges är den viktigaste regeln.

1. När du har skapat en eller flera regler trycker du på **Spara**. Den här åtgärden skapar en regel motor konfiguration.

1. När du har skapat en eller flera konfigurationer associerar du en regel motor konfiguration med en väg regel. Även om en enskild konfiguration kan tillämpas på många väg regler får en väg regel bara innehålla en regel motor konfiguration. Om du vill skapa en Association går du till  >  **Dirigerings reglerna** för din front dörr design. Välj den flödes regel som du vill lägga till regel motor konfigurationen till, gå till **flödes information**  >  **regel motor konfiguration** och välj den konfiguration som du vill koppla.

    ![Konfigurera till en regel för routning](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Konfigurera regel motor i Azure CLI

1. Installera [Azure CLI](/cli/azure/install-azure-cli)om du inte redan gjort det. Lägg till tillägget "frontend-dörr":-AZ Extension Add--Name front-dörr. Logga sedan in och växla till prenumerationen AZ Account set--Subscription <name_or_Id>.

1. Börja med att skapa en regel motor – det här exemplet visar en regel med en rubrik-baserad åtgärd och ett matchnings villkor. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Visa en lista med alla regler. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Lägg till en åtgärd för åsidosättning av vidarebefordrande vägar. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Visa en lista med alla åtgärder i en regel. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Länka en regel motor konfiguration till en regel för routning.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Ta bort länk till regel motor. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

För mer information, finns en fullständig lista över AFD-regler för motor kommandon som du hittar [här](/cli/azure/ext/front-door/network/front-door/rules-engine).   

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg konfigurerade du och associerade regel motor konfigurationen till dina routningsregler. Om du inte längre vill att regel motor konfigurationen ska vara kopplad till din front dörr kan du ta bort konfigurationen genom att utföra följande steg:

1. Ta bort alla routningsregler från regel motor konfigurationen genom att klicka på de tre punkterna bredvid regel motorns namn.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Koppla routningsregler":::

1. Avmarkera alla routningsregler som den här regel motor konfigurationen är kopplad till och klicka på Spara.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Regel koppling för routning":::

1. Nu kan du ta bort regel motor konfigurationen från din front dörr.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Ta bort regel motor konfiguration":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en regel motor konfiguration
* Koppla konfigurationen till reglerna för routning av front dörren.

Fortsätt till nästa självstudie om du vill lära dig hur du lägger till säkerhets rubriker med regel motor.

> [!div class="nextstepaction"]
> [Säkerhets rubriker med regel motor](front-door-security-headers.md)