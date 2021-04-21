---
title: Azure App Service åtkomstbegränsningar
description: Lär dig hur du skyddar din app Azure App Service genom att konfigurera åtkomstbegränsningar.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 541af6d0051d06de5721b22616fbf1e2867b71d6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833372"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Konfigurera Azure App Service åtkomstbegränsningar

Genom att konfigurera åtkomstbegränsningar kan du definiera en lista med prioritetsbeställda tillåtna/nekade som styr nätverksåtkomsten till din app. Listan kan innehålla IP-adresser eller Azure Virtual Network undernät. När det finns en eller flera poster finns *ett* implicit nekande i slutet av listan.

Funktionen för åtkomstbegränsning fungerar med alla Azure App Service värdbaserade arbetsbelastningar. Arbetsbelastningarna kan vara webbappar, API-appar, Linux-appar, Linux-containerappar och Functions.

När en begäran görs till din app utvärderas FROM-adressen mot reglerna i listan över åtkomstbegränsningar. Om FROM-adressen finns i ett undernät som har konfigurerats med tjänstslutpunkter för Microsoft.Web jämförs källundernätet med reglerna för virtuellt nätverk i listan över åtkomstbegränsningar. Om adressen inte tillåts åtkomst baserat på reglerna i listan svarar tjänsten med en [HTTP 403-statuskod.](https://en.wikipedia.org/wiki/HTTP_403)

Funktionen för åtkomstbegränsning implementeras i App Service roller i frontend, som är uppströms från de arbetsvärdar där koden körs. Därför är åtkomstbegränsningar effektivt åtkomstkontrollistor för nätverk (ACL:er).

Möjligheten att begränsa åtkomsten till din webbapp från ett virtuellt Azure-nätverk aktiveras av [tjänstslutpunkter][serviceendpoints]. Med tjänstslutpunkter kan du begränsa åtkomsten till en tjänst för flera innehavare från valda undernät. Det fungerar inte att begränsa trafiken till appar som finns i en App Service-miljön. Om du är i en App Service-miljön kan du styra åtkomsten till din app genom att använda IP-adressregler.

> [!NOTE]
> Tjänstslutpunkterna måste aktiveras både på nätverkssidan och för den Azure-tjänst som de aktiveras med. En lista över Azure-tjänster som stöder tjänstslutpunkter finns i [Virtual Network tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagram över flödet av åtkomstbegränsningar.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Hantera regler för åtkomstbegränsning i portalen

Gör följande om du vill lägga till en regel för åtkomstbegränsning i din app:

1. Logga in på Azure-portalen.

1. Välj Nätverk i den vänstra **rutan.**

1. I fönstret **Nätverk** går du till **Åtkomstbegränsningar och** väljer **Konfigurera åtkomstbegränsningar.**

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Skärmbild av fönstret App Service nätverksalternativ i Azure Portal.":::

1. På sidan **Åtkomstbegränsningar** granskar du listan över regler för åtkomstbegränsningar som har definierats för din app.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Skärmbild av sidan Åtkomstbegränsningar i Azure Portal visar listan över regler för åtkomstbegränsningar som definierats för den valda appen.":::

   I listan visas alla aktuella begränsningar som tillämpas på appen. Om du har en begränsning för virtuellt nätverk i din app visar tabellen om tjänstslutpunkterna är aktiverade för Microsoft.Web. Om inga begränsningar har definierats för din app är appen tillgänglig var som helst.

### <a name="add-an-access-restriction-rule"></a>Lägga till en regel för åtkomstbegränsning

Om du vill lägga till en regel för åtkomstbegränsning i din app går du till **fönstret Åtkomstbegränsningar** och väljer **Lägg till regel.** När du har lagt till en regel börjar den gälla omedelbart. 

Regler tillämpas i prioritetsordning, med början från det lägsta talet i **kolumnen** Prioritet. Ett implicit *nekande gäller* när du har lagt till en enda regel.

I fönstret **Lägg till åtkomstbegränsning** gör du följande när du skapar en regel:

1. Under **Åtgärd** väljer du **antingen Tillåt** eller **Neka**.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Skärmbild av fönstret Lägg till åtkomstbegränsning.":::

1. Du kan också ange ett namn och en beskrivning av regeln.
1. I rutan **Prioritet** anger du ett prioritetsvärde.
1. I **listrutan** Typ väljer du typ av regel.

De olika typerna av regler beskrivs i följande avsnitt.

> [!NOTE]
> - Det finns en gräns på 512 regler för åtkomstbegränsning. Om du behöver fler än 512 regler för åtkomstbegränsning föreslår vi att du installerar en fristående säkerhetsprodukt, till exempel Azure Front Door, Azure App Gateway eller en alternativ WAF.
>
#### <a name="set-an-ip-address-based-rule"></a>Ange en IP-adressbaserad regel

Följ proceduren som beskrivs i föregående avsnitt, men med följande tillägg:
* För steg 4 väljer **du** **IPv4** eller **IPv6** i listrutan Typ. 

Ange **IP-adressblocket i** CIDR-notationen (Classless Inter-Domain Routing) för både IPv4- och IPv6-adresserna. Om du vill ange en adress kan du använda något som *1.2.3.4/32,* där de första fyra oktetterna representerar din IP-adress och */32* är masken. IPv4 CIDR-notationen för alla adresser är 0.0.0.0/0. Mer information om CIDR-notation finns i [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

#### <a name="set-a-service-endpoint-based-rule"></a>Ange en tjänstslutpunktsbaserad regel

* För steg 4 går **du till** listrutan Typ och väljer **Virtual Network**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Skärmbild av fönstret Lägg till begränsning med Virtual Network vald.":::

Ange **listrutan** **Prenumeration, Virtual Network** **och Undernät** som matchar det du vill begränsa åtkomsten till.

Genom att använda tjänstslutpunkter kan du begränsa åtkomsten till valda undernät för virtuella Azure-nätverk. Om tjänstslutpunkter inte redan är aktiverade med Microsoft.Web för det undernät som du har valt aktiveras de automatiskt såvida du inte markerar kryssrutan Ignorera saknade **Microsoft.Web-tjänstslutpunkter.** Scenariot där du kanske vill aktivera tjänstslutpunkter i appen men inte undernätet beror huvudsakligen på om du har behörighet att aktivera dem i undernätet. 

Om du behöver någon annan för att aktivera tjänstslutpunkter i undernätet markerar du kryssrutan Ignorera saknade **Microsoft.Web-tjänstslutpunkter.** Din app konfigureras för tjänstslutpunkter på grund av att de aktiveras senare i undernätet. 

Du kan inte använda tjänstslutpunkter för att begränsa åtkomsten till appar som körs i en App Service-miljön. När din app finns i en App Service-miljön kan du styra åtkomsten till den genom att tillämpa IP-åtkomstregler. 

Med tjänstslutpunkter kan du konfigurera din app med programgatewayer eller andra waf-enheter (brandvägg för webbaserade program). Du kan också konfigurera flernivåprogram med säkra backend-ändarna. Mer information finns i [Nätverksfunktioner och App Service](networking-features.md) och [Application Gateway integrering med tjänstslutpunkter.](networking/app-gateway-with-service-endpoints.md)

> [!NOTE]
> - Tjänstslutpunkter stöds för närvarande inte för webbappar som använder IP Secure Sockets Layer (SSL) virtuell IP (VIP).
>
#### <a name="set-a-service-tag-based-rule"></a>Ange en tjänsttaggbaserad regel

* För steg 4 går du **till** listrutan Typ och väljer **Tjänsttagg**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Skärmbild av fönstret Lägg till begränsning med tjänsttaggtypen vald.":::

Varje tjänsttagg representerar en lista över IP-intervall från Azure-tjänster. En lista över dessa tjänster och länkar till de specifika intervallen finns i dokumentationen [för tjänsttaggar.][servicetags]

Alla tillgängliga tjänsttaggar stöds i regler för åtkomstbegränsning. För enkelhetens skull är endast en lista över de vanligaste taggarna tillgängliga via Azure Portal. Använd Azure Resource Manager eller skript för att konfigurera mer avancerade regler som regionala omfångsregler. Det här är de taggar som är tillgängliga via Azure Portal:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Redigera en regel

1. Om du vill börja redigera en befintlig regel för åtkomstbegränsning går **du till** sidan Åtkomstbegränsningar och väljer den regel som du vill redigera.

1. I fönstret **Redigera åtkomstbegränsningar** gör du dina ändringar och väljer sedan **Uppdatera regel.** Redigeringar börjar gälla omedelbart, inklusive ändringar i prioritetsordning.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Skärmbild av fönstret &quot;Redigera åtkomstbegränsning&quot; i Azure Portal som visar fälten för en befintlig regel för åtkomstbegränsning.":::

   > [!NOTE]
   > När du redigerar en regel kan du inte växla mellan regeltyper. 

### <a name="delete-a-rule"></a>Ta bort en regel

Om du vill ta  bort en regel går du till sidan Åtkomstbegränsningar och väljer ellipsen (**...**) bredvid den regel som du vill ta bort och väljer sedan **Ta bort**.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Skärmbild av sidan &quot;Åtkomstbegränsningar&quot; med ellipsen &quot;Ta bort&quot; bredvid regeln för åtkomstbegränsning som ska tas bort.":::

## <a name="access-restriction-advanced-scenarios"></a>Avancerade scenarier för åtkomstbegränsning
I följande avsnitt beskrivs några avancerade scenarier med åtkomstbegränsningar.

### <a name="filter-by-http-header"></a>Filtrera efter http-rubrik

Som en del av en regel kan du lägga till ytterligare http-huvudfilter. Följande http-huvudnamn stöds:
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

För varje rubriknamn kan du lägga till upp till 8 värden avgränsade med kommatecken. HTTP-huvudfiltren utvärderas efter själva regeln och båda villkoren måste vara sanna för att regeln ska gälla.

### <a name="multi-source-rules"></a>Regler för flera källor

Med regler för flera källor kan du kombinera upp till 8 IP-intervall eller 8 tjänsttaggar i en enda regel. Du kan använda det här om du har fler än 512 IP-intervall eller om du vill skapa logiska regler där flera IP-intervall kombineras med ett enda http-huvudfilter.

Regler för flera källor definieras på samma sätt som du definierar regler för en enskild källa, men med varje intervall avgränsat med kommatecken.

PowerShell-exempel:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Blockera en enskild IP-adress

När du lägger till din första regel för åtkomstbegränsning lägger tjänsten till en explicit *Neka alla-regel* med prioriteten 2147483647. I praktiken är den *explicita* regeln Neka alla den sista regeln som ska köras och blockerar åtkomst till alla IP-adresser som inte uttryckligen tillåts av en *Tillåt-regel.*

För ett scenario där du uttryckligen vill blockera en enskild IP-adress eller ett block med IP-adresser, men tillåta åtkomst till allt annat, lägger du till en *explicit tillåt alla-regel.*

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Skärmbild av sidan &quot;Åtkomstbegränsningar&quot; i Azure Portal visar en enda blockerad IP-adress.":::

### <a name="restrict-access-to-an-scm-site"></a>Begränsa åtkomsten till en SCM-webbplats 

Förutom att kunna styra åtkomsten till din app kan du begränsa åtkomsten till SCM-webbplatsen som används av din app. SCM-webbplatsen är både webbdelsslutpunkten och Kudu-konsolen. Du kan tilldela åtkomstbegränsningar till SCM-webbplatsen från appen separat eller använda samma uppsättning begränsningar för både appen och SCM-webbplatsen. När du markerar **kryssrutan \<app name> Samma begränsningar** som är allt tomt. Om du avmarkerar kryssrutan tillämpas dina SCM-platsinställningar på nytt. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Skärmbild av sidan &quot;Åtkomstbegränsningar&quot; i Azure Portal som visar att inga åtkomstbegränsningar har angetts för SCM-webbplatsen eller appen.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Begränsa åtkomsten till en Azure Front Door instans
Trafik från Azure Front Door till ditt program kommer från en välkänd uppsättning IP-intervall som definierats i tjänsttaggen AzureFrontDoor.Backend. Med hjälp av en begränsningsregel för tjänsttaggar kan du begränsa trafiken så att den endast kommer Azure Front Door. För att säkerställa att trafiken endast kommer från din specifika instans behöver du ytterligare filtrera inkommande begäranden baserat på det unika HTTP-huvud som Azure Front Door skickar.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Skärmbild av sidan &quot;Åtkomstbegränsningar&quot; i Azure Portal visar hur du lägger till Azure Front Door begränsning.":::

PowerShell-exempel:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Hantera regler för åtkomstbegränsningar programmatiskt

Du kan lägga till åtkomstbegränsningar programmatiskt genom att göra något av följande: 

* Använd [Azure CLI.](/cli/azure/webapp/config/access-restriction) Exempel:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Använd [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule). Exempel:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > Att arbeta med tjänsttaggar, HTTP-huvuden eller regler för flera källor kräver minst version 5.7.0. Du kan verifiera versionen av den installerade modulen med: **Get-InstalledModule -Name Az**

Du kan också ange värden manuellt genom att göra något av följande:

* Använd en [Azure REST API](/rest/api/azure/) PUT-åtgärd för appkonfigurationen i Azure Resource Manager. Platsen för den här informationen i Azure Resource Manager är:

  management.azure.com/subscriptions/**prenumerations-ID**/resourceGroups/**resursgrupper**/providers/Microsoft.Web/sites/**webbappens** namn /config/web?api-version=2020-06-01

* Använd en Resource Manager mall. Du kan till exempel använda resources.azure.com redigera blocket ipSecurityRestrictions för att lägga till den JSON som krävs.

  JSON-syntaxen för det tidigare exemplet är:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  JSON-syntaxen för ett avancerat exempel med tjänsttagg och begränsning av HTTP-huvud är:
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Konfigurera Azure Functions åtkomstbegränsningar

Åtkomstbegränsningar är också tillgängliga för funktionsappar med samma funktioner som App Service abonnemang. När du aktiverar åtkomstbegränsningar inaktiverar du även Azure Portal för otillåtna IP-adresser.

## <a name="next-steps"></a>Nästa steg
[Åtkomstbegränsningar för Azure Functions](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Application Gateway integrering med tjänstslutpunkter](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md
