---
title: 'Självstudie: skala och skydda en webbapp med hjälp av Azures frontend-dörr och Azure WebApplication-brandvägg (WAF)'
description: I den här självstudien visas hur du använder Azures brand vägg för webbaserade program med Azures frontend-tjänst.
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
ms.openlocfilehash: f894fd5ca7bb98c48b106c13b8fe6130b2c76c6e
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201064"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Självstudie: snabbt skala och skydda ett webb program med hjälp av Azures frontend och brand vägg för Azure Web Application (WAF)

Många webb program har en snabb ökning av trafiken i de senaste veckorna på grund av COVID-19. Dessa webb program har också en överspänning i skadlig trafik, inklusive denial-of-Service-attacker. Det finns ett effektivt sätt att både skala upp ditt program för trafik toppar och skydda dig mot attacker: Konfigurera Azures frontend-dörr med Azure WAF som en acceleration, cachelagring och säkerhets nivå framför din webbapp. Den här artikeln innehåller rikt linjer för hur du får Azure-WAF som kon figurer ATS för alla webbappar som körs i eller utanför Azure. 

Vi använder Azure CLI för att konfigurera WAF i den här självstudien. Du kan utföra samma sak genom att använda Azure Portal, Azure PowerShell, Azure Resource Manager eller Azure REST-API: er. 

I den här självstudien får du lära dig att:
> [!div class="checklist"]
> - Skapa en frontend-dörr.
> - Skapa en Azure WAF-princip.
> - Konfigurera regel uppsättningar för en WAF-princip.
> - Koppla en WAF-princip till front dörren.
> - Konfigurera en anpassad domän.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Anvisningarna i den här självstudien använder Azure CLI. [Visa den här guiden](/cli/azure/get-started-with-azure-cli) för att komma igång med Azure CLI.

  > [!TIP] 
  > Ett enkelt och snabbt sätt att komma igång med Azure CLI är med [bash i Azure Cloud Shell](../cloud-shell/quickstart.md).

- Kontrol lera att `front-door` tillägget har lagts till i Azure CLI:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Mer information om de kommandon som används i den här självstudien finns i [Azure CLI-referens för front dörren](/cli/azure/ext/front-door).

## <a name="create-an-azure-front-door-resource"></a>Skapa en resurs för Azures frontend-dörr

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: Det fullständigt kvalificerade domän namnet (FQDN) för det program som du vill skydda. Till exempel `myapplication.contoso.com`.

`--accepted-protocols`: Anger de protokoll som du vill att Azures frontend-dörr ska stödja för ditt webb program. Till exempel `--accepted-protocols Http Https`.

`--name`: Namnet på din Azure-frontend-resurs.

`--resource-group`: Den resurs grupp som du vill placera den här resursen i Azures frontend-dörr i. Mer information om resurs grupper finns i [Hantera resurs grupper i Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

I svaret som du får när du kör det här kommandot ska du leta efter nyckeln `hostName` . Du behöver det här värdet i ett senare steg. `hostName`Är DNS-namnet på den Azure-frontend-resurs som du skapade.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Skapa en Azure WAF-profil som ska användas med resurser i Azures frontend-dörr

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: Namnet på den nya Azure WAF-principen.

`--resource-group`: Den resurs grupp som du vill placera den här WAF-resursen i. 

Föregående CLI-kod skapar en WAF-princip som är aktive rad och som är i förebyggande läge. 

> [!NOTE] 
> Du kanske vill skapa WAF-principen i identifierings läge och se hur den identifierar och loggar skadliga begär Anden (utan att blockera dem) innan du bestämmer dig för att använda skydds läget.

I svaret som du får när du kör det här kommandot ska du leta efter nyckeln `ID` . Du behöver det här värdet i ett senare steg. 

`ID`Fältet ska ha följande format:

/Subscriptions/**prenumerations-ID**/ResourceGroups/**resurs grupp namn**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF princip namn**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Lägg till hanterade regel uppsättningar i WAF-principen

Du kan lägga till hanterade regel uppsättningar i en WAF-princip. En hanterad regel uppsättning är en uppsättning regler som skapats och hanteras av Microsoft och som hjälper dig att skydda dig mot en klass av hot. I det här exemplet ska vi lägga till två regel uppsättningar:
- Standard regel uppsättningen, som hjälper dig att skydda dig mot vanliga webb hot. 
- Regel uppsättningen för bot-skydd, som hjälper dig att skydda dig mot skadliga robotar.

Lägg till standard regel uppsättningen:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Lägg till regel uppsättningen för bot-skydd:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: Det namn du angav för din Azure WAF-resurs.

`--resource-group`: Den resurs grupp som du placerade WAF-resursen i.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Koppla WAF-principen till Azures frontend-resurs

I det här steget associerar vi den WAF-princip som vi skapade med Azures frontend-resurs som är framför ditt webb program:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: Det namn du angav för din Azure-frontend-resurs.

`--resource-group`: Den resurs grupp som du placerade Azures frontend-resurs i.

`--set`: Är den plats där du uppdaterar `WebApplicationFirewallPolicyLink` attributet för `frontendEndpoint` som är associerat med din Azure-frontend-resurs med den nya WAF-principen. Du bör ha ID: t för WAF-principen från svaret du fick när du skapade WAF-profilen tidigare i den här självstudien.

 > [!NOTE] 
> Föregående exempel är tillämpbara när du inte använder en anpassad domän. Om du inte använder några anpassade domäner för att komma åt dina webb program kan du hoppa över nästa avsnitt. I så fall får du kunderna som `hostName` du fick när du skapade Azures frontend-resurs. De använder detta `hostName` för att gå till ditt webb program.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Konfigurera en anpassad domän för ditt webb program

Det anpassade domän namnet för ditt webb program är det som kunderna använder för att referera till ditt program. Till exempel www.contoso.com. Inlednings vis pekade det här anpassade domän namnet på den plats där det kördes innan du introducerade Azures front dörr. När du har lagt till Azures frontend-och WAF för att framgångs punkten ska den DNS-post som motsvarar den anpassade domänen peka på resursen för Azures front dörr. Du kan göra den här ändringen genom att mappa om posten i DNS-servern till den Azure-hemdörr `hostName` som du noterade när du skapade Azures frontend-resurs.

De olika stegen för att uppdatera dina DNS-poster beror på din DNS-tjänstleverantör. Om du använder Azure DNS för att vara värd för ditt DNS-namn kan du läsa mer i dokumentationen om [hur du uppdaterar en DNS-post](../dns/dns-operations-recordsets-cli.md) och pekar på Azures front dörr `hostName` . 

Det finns ett viktigt saker att notera om du behöver dina kunder för att komma till din webbplats med hjälp av Zone Apex (till exempel contoso.com). I det här fallet måste du använda Azure DNS och dess [aliasresurspost](../dns/dns-alias.md) för att vara värd för ditt DNS-namn. 

Du måste också uppdatera konfigurationen för din Azure-frontend för att [lägga till den anpassade domänen](./front-door-custom-domain.md) så att den är medveten om den här mappningen.

Slutligen, om du använder en anpassad domän för att komma åt ditt webb program och vill aktivera HTTPS-protokollet. Du måste [Konfigurera certifikaten för din anpassade domän i Azures front dörr](./front-door-custom-domain-https.md). 

## <a name="lock-down-your-web-application"></a>Låsa ditt webb program

Vi rekommenderar att du endast ser till att Azures front dörrs kanter kan kommunicera med ditt webb program. På så sätt ser du till att ingen kan kringgå Azures front dörrs skydd och få åtkomst till ditt program direkt. Om du vill göra detta kan du läsa mer i [Hur gör jag för att låsa ned åtkomsten till min server del till Azures front dörr?](./front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som används i den här självstudien använder du kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen, frontend-dörren och WAF-principen:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: Namnet på resurs gruppen för alla resurser som används i den här självstudien.

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker din front dörr finns i fel söknings guider:

> [!div class="nextstepaction"]
> [Felsöka vanliga problem med Routning](front-door-troubleshoot-routing.md)