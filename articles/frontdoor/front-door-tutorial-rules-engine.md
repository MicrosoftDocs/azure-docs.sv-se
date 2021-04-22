---
title: 'Självstudie: Konfigurera regelmotor – Azure Front Door'
description: Den här artikeln innehåller en självstudiekurs om hur du konfigurerar regelmotorn i både Azure Portal och CLI.
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
ms.openlocfilehash: e64f27480126fdf444f8dd72abc2d66c78b30407
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868815"
---
# <a name="tutorial-configure-your-rules-engine"></a>Självstudie: Konfigurera regelmotorn

Den här självstudien visar hur du skapar en regelmotorkonfiguration och din första regel i både Azure Portal och CLI. 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Konfigurera regelmotorn med hjälp av portalen.
> - Konfigurera regelmotorn med Azure CLI

## <a name="prerequisites"></a>Förutsättningar

* Innan du kan slutföra stegen i den här kursen behöver du skapa en Front Door. Mer information finns i [Snabbstart: Skapa en Front Door](quickstart-create-front-door.md).

## <a name="configure-rules-engine-in-azure-portal"></a>Konfigurera regelmotorn i Azure Portal
1. I din Front door-resurs går du **till Inställningar** och väljer **Regelmotorkonfiguration.** Klicka **på** Lägg till, ge konfigurationen ett namn och börja skapa din första regelmotorkonfiguration.

    ![menyn Front Door inställningar](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Klicka **på Lägg till** regel för att skapa din första regel. Genom att klicka på **Lägg till villkor** eller Lägg till **åtgärd** kan du definiera regeln.
    
    > [!NOTE]
    >- Om du vill ta bort ett villkor eller en åtgärd från en regel använder du papperskorgen till höger om det specifika villkoret eller åtgärden.
    > - Om du vill skapa en regel som gäller för all inkommande trafik ska du inte ange några villkor.
    > - Om du vill sluta utvärdera regler när det första matchningsvillkoret har uppfyllts markerar **du Sluta utvärdera återstående regel**. Om detta kontrolleras och alla matchningsvillkor för en viss regel är uppfyllda körs inte de återstående reglerna i konfigurationen.  

    ![Regelmotorkonfiguration](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Fastställ prioriteten för reglerna i konfigurationen med hjälp av knapparna Flytta upp, Flytta ned och Flytta till överst. Prioriteten är i stigande ordning, vilket innebär att regeln som först visas är den viktigaste regeln.

1. När du har skapat en eller flera regler trycker du på **Spara**. Den här åtgärden skapar din regelmotorkonfiguration.

1. När du har skapat en eller flera konfigurationer associerar du en regelmotorkonfiguration med en vägregel. En enda konfiguration kan tillämpas på många vägregler, men en vägregel kan bara innehålla en regelmotorkonfiguration. Om du vill skapa associationen går du till **Front Door**  >  **route-designern .** Välj den vägregel som du vill lägga till regelmotorkonfigurationen till, gå till Konfiguration av väginformationRegelmotor och välj den konfiguration   >  som du vill associera.

    ![Konfigurera till en routningsregel](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Konfigurera regelmotorn i Azure CLI

1. Installera Azure CLI om du inte redan [gjort det.](/cli/azure/install-azure-cli) Lägg till tillägget "front-door":- az extension add --name front-door. Logga sedan in och växla till prenumerationen az account set --subscription <name_or_Id>.

1. Börja med att skapa en regelmotor – det här exemplet visar en regel med en rubrikbaserad åtgärd och ett matchningsvillkor. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Visa en lista över alla regler. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Lägg till en åsidosättningsåtgärd för vidarebefordrande väg. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Visa en lista över alla åtgärder i en regel. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Länka en regelmotorkonfiguration till en routningsregel.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Ta bort länk till regelmotor. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

Mer information finns i en fullständig lista över AFD-regelmotorkommandon [här.](/cli/azure/network/front-door/rules-engine)   

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg konfigurerade du och associerade regelmotorkonfigurationen till dina routningsregler. Om du inte längre vill att regelmotorns konfiguration ska associeras Front Door kan du ta bort konfigurationen genom att utföra följande steg:

1. Ta bort associationen till eventuella routningsregler från regelmotorns konfiguration genom att klicka på de tre punkterna bredvid regelmotorns namn.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Associera routningsregler":::

1. Avmarkera alla routningsregler som regelmotorns konfiguration är associerad med och klicka på Spara.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Koppling av routningsregel":::

1. Nu kan du ta bort regelmotorns konfiguration från Front Door.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Ta bort regelmotorkonfiguration":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en regelmotorkonfiguration
* Associera konfigurationen med dina Front Door routningsregler.

Om du vill lära dig hur du lägger till säkerhetsrubriker med regelmotorn fortsätter du till nästa självstudie.

> [!div class="nextstepaction"]
> [Säkerhetshuvuden med regelmotor](front-door-security-headers.md)