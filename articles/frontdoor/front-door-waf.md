---
title: 'Självstudie: Skala och skydda en webbapp med hjälp Azure Front Door och Azure Web Application Firewall (WAF)'
description: Den här självstudien visar hur du använder Azure Web Application Firewall med Azure Front Door tjänsten.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: d315fa5b588c6e5f2e4643ca18626e400e6ca01b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785657"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Självstudie: Skala snabbt och skydda en webbapp med hjälp av Azure Front Door och Azure Web Application Firewall (WAF)

Många webbprogram har haft en snabb ökning av trafiken under de senaste veckorna på grund av COVID-19. Dessa webbprogram har också en ökning av skadlig trafik, inklusive doS-attacker. Det finns ett effektivt sätt att både skala ut ditt program för trafiktoppar och skydda dig mot attacker: konfigurera Azure Front Door med Azure WAF som acceleration, cachelagring och säkerhetslager framför webbappen. Den här artikeln innehåller råd om hur du Azure Front Door med Azure WAF konfigurerat för alla webbappar som körs i eller utanför Azure. 

Vi använder Azure CLI för att konfigurera WAF i den här självstudien. Du kan göra samma sak med hjälp av Azure Portal, Azure PowerShell, Azure Resource Manager eller Azure REST API:er. 

I den här självstudien får du lära dig att:
> [!div class="checklist"]
> - Skapa en Front Door.
> - Skapa en Azure WAF-princip.
> - Konfigurera regeluppsättningar för en WAF-princip.
> - Associera en WAF-princip med Front Door.
> - Konfigurera en anpassad domän.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Anvisningarna i den här självstudien använder Azure CLI. [Se den här guiden](/cli/azure/get-started-with-azure-cli) för att komma igång med Azure CLI.

  > [!TIP] 
  > Ett enkelt och snabbt sätt att komma igång med Azure CLI är med [Bash i Azure Cloud Shell](../cloud-shell/quickstart.md).

- Kontrollera att `front-door` tillägget har lagts till i Azure CLI:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Mer information om de kommandon som används i den här självstudien finns [i Azure CLI reference for Front Door](/cli/azure/ext/front-door).

## <a name="create-an-azure-front-door-resource"></a>Skapa en Azure Front Door resurs

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: Det fullständiga domännamnet (FQDN) för det program som du vill skydda. Till exempel `myapplication.contoso.com`.

`--accepted-protocols`: Anger de protokoll som du Azure Front Door ska stödja för webbappen. Till exempel `--accepted-protocols Http Https`.

`--name`: Namnet på din Azure Front Door resurs.

`--resource-group`: Den resursgrupp som du vill placera den här Azure Front Door resurs i. Mer information om resursgrupper finns i [Hantera resursgrupper i Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

Leta efter nyckeln i svaret du får när du kör det här `hostName` kommandot. Du behöver det här värdet i ett senare steg. `hostName`är DNS-namnet för den Azure Front Door som du skapade.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Skapa en Azure WAF-profil som ska användas med Azure Front Door resurser

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: Namnet på den nya Azure WAF-principen.

`--resource-group`: Den resursgrupp som du vill placera WAF-resursen i. 

Föregående CLI-kod skapar en WAF-princip som är aktiverad och som är i skyddsläge. 

> [!NOTE] 
> Du kanske vill skapa WAF-principen i identifieringsläge och se hur den identifierar och loggar skadliga begäranden (utan att blockera dem) innan du väljer att använda skyddsläge.

Leta efter nyckeln i svaret du får när du kör det här `ID` kommandot. Du behöver det här värdet i ett senare steg. 

Fältet `ID` ska ha det här formatet:

/subscriptions/**subscription id**/resourcegroups/ resource group **name**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF policy name**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Lägga till hanterade regeluppsättningar i WAF-principen

Du kan lägga till hanterade regeluppsättningar i en WAF-princip. En hanterad regeluppsättning är en uppsättning regler som skapats och hanteras av Microsoft och som hjälper dig att skydda dig mot en klass av hot. I det här exemplet lägger vi till två regeluppsättningar:
- Standardregeluppsättningen, som hjälper dig att skydda dig mot vanliga webbhot. 
- Regeluppsättningen för robotskydd, som hjälper dig att skydda dig mot skadliga robotar.

Lägg till standardregeluppsättningen:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Lägg till regeluppsättningen för robotskydd:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: Det namn som du angav för din Azure WAF-resurs.

`--resource-group`: Den resursgrupp som du placerade WAF-resursen i.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Associera WAF-principen med Azure Front Door resursen

I det här steget associerar vi WAF-principen som vi skapade Azure Front Door den resurs som finns framför webbappen:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: Det namn som du angav för Azure Front Door resurs.

`--resource-group`: Den resursgrupp som du placerade Azure Front Door resurs i.

`--set`: Är där du uppdaterar `WebApplicationFirewallPolicyLink` attributet för som är `frontendEndpoint` associerat med Azure Front Door resurs med den nya WAF-principen. Du bör ha ID:t för WAF-principen från svaret du fick när du skapade WAF-profilen tidigare i den här självstudien.

 > [!NOTE] 
> Föregående exempel gäller när du inte använder en anpassad domän. Om du inte använder några anpassade domäner för att få åtkomst till dina webbprogram kan du hoppa över nästa avsnitt. I så fall ger du dina kunder den som `hostName` du fick när du skapade Azure Front Door resurs. De använder detta för `hostName` att gå till webbappen.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Konfigurera den anpassade domänen för webbappen

Det anpassade domännamnet för din webbapp är det som kunderna använder för att referera till ditt program. Till exempel www.contoso.com. Till en början pekade det här anpassade domännamnet på den plats där det kördes innan du introducerade Azure Front Door. När du har Azure Front Door och WAF framför programmet ska DNS-posten som motsvarar den anpassade domänen peka på den Azure Front Door resursen. Du kan göra den här ändringen genom att mappa om posten i DNS-servern till den Azure Front Door du antecknade när du skapade `hostName` Azure Front Door resurs.

Specifika steg för att uppdatera DINA DNS-poster beror på din DNS-tjänstleverantör. Om du använder Azure DNS som värd för ditt DNS-namn kan du läsa i dokumentationen om hur du uppdaterar en [DNS-post](../dns/dns-operations-recordsets-cli.md) och pekar på Azure Front Door `hostName` . 

Det finns en viktig sak att observera om du behöver dina kunder för att komma till din webbplats med hjälp av zonens toppdomän (till exempel contoso.com). I det här fallet måste du använda Azure DNS och dess [aliasposttyp som värd](../dns/dns-alias.md) för ditt DNS-namn. 

Du måste också uppdatera din Azure Front Door för att [lägga](./front-door-custom-domain.md) till den anpassade domänen i den så att den är medveten om den här mappningen.

Slutligen, om du använder en anpassad domän för att nå webbappen och vill aktivera HTTPS-protokollet. Du måste konfigurera [certifikaten för din anpassade domän i Azure Front Door](./front-door-custom-domain-https.md). 

## <a name="lock-down-your-web-application"></a>Låsa webbappen

Vi rekommenderar att du ser till Azure Front Door kanterna kan kommunicera med din webbapp. Detta säkerställer att ingen kan kringgå Azure Front Door och komma åt ditt program direkt. Om du vill utföra den här låsning Hur gör jag för att du låsa åtkomsten till min [backend till endast Azure Front Door?](./front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som används i den här självstudien använder du kommandot [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, Front Door och WAF-principen:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: Namnet på resursgruppen för alla resurser som används i den här självstudien.

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker Front Door finns i felsökningsguiderna:

> [!div class="nextstepaction"]
> [Felsöka vanliga routningsproblem](front-door-troubleshoot-routing.md)