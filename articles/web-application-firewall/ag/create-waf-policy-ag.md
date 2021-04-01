---
title: Skapa WAF-principer (Web Application Firewall) för Application Gateway
description: Lär dig hur du skapar brand Väggs principer för webb program för Application Gateway.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 26078c3757e42c3e290a5f4122461b287582fb80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518829"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Skapa brand Väggs principer för webb program för Application Gateway

Att associera en WAF-princip med lyssnare tillåter att flera platser bakom en enda WAF skyddas av olika principer. Om det till exempel finns fem platser bakom din WAF, kan du ha fem separata WAF-principer (en för varje lyssnare) för att anpassa undantag, anpassade regler och hanterade rulesets för en plats utan att påverka de andra fyra. Om du vill att en enda princip ska tillämpas på alla platser kan du associera principen med Application Gateway, i stället för de enskilda lyssnarna, så att den tillämpas globalt. Principer kan också tillämpas på en Sök vägs baserad regel för routning. 

Du kan skapa så många principer du vill. När du har skapat en princip måste den kopplas till en Application Gateway för att börja gälla, men den kan associeras med valfri kombination av programgatewayer och lyssnare. 

Om din Application Gateway har en princip tillämpad och du tillämpar en annan princip för en lyssnare på den Application Gateway, kommer lyssnarens princip att gälla, men bara för de lyssnare som de är kopplade till. Den Application Gateway principen gäller fortfarande alla andra lyssnare som inte har tilldelats någon speciell princip. 

   > [!NOTE]
   > När en brand Väggs princip är kopplad till en WAF måste det alltid finnas en princip som är kopplad till den WAF. Du kan skriva över principen, men det finns inget stöd för att avassociera en princip från WAF. 

Alla nya WAF-inställningar för webb program brand väggen (anpassade regler, hanterade rulset-konfigurationer, undantag osv.) i en WAF-princip. Om du har en befintlig WAF kan de här inställningarna fortfarande finnas i WAF-konfigurationen. Anvisningar om hur du flyttar till den nya WAF-principen finns i [migrera din WAF-konfiguration till WAF-princip](#migrate) senare i den här artikeln. 

## <a name="create-a-policy"></a>Skapa en princip

Börja med att skapa en grundläggande WAF-princip med en hanterad standard regel uppsättning (DRS) med hjälp av Azure Portal.

1. Välj **skapa en resurs** på den övre vänstra sidan i portalen. Sök efter **WAF**, Välj **brand vägg för webbaserade program** och välj sedan **skapa**.
2. På sidan **skapa en princip för WAF** , fliken **grundläggande** , ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**:

   |Inställning  |Värde  |
   |---------|---------|
   |Princip för     |Regional WAF (Application Gateway)|
   |Prenumeration     |Välj ditt prenumerations namn|
   |Resursgrupp     |Välj din resursgrupp|
   |Principnamn     |Ange ett unikt namn för WAF-principen.|
3. Ange någon av följande inställningar på fliken **Association** och välj sedan **Lägg till**:

   |Inställning  |Värde  |
   |---------|---------|
   |Associera Application Gateway     |Välj ditt Application Gateway profil namn.|
   |Associera lyssnare     |Välj namnet på din Application Gateway lyssnare och välj sedan **Lägg till**.|

   > [!NOTE]
   > Om du tilldelar en princip till din Application Gateway (eller lyssnare) som redan har en princip på plats skrivs den ursprungliga principen över och ersätts av den nya principen.
4. Välj **Granska + skapa** och välj sedan **Skapa**.

   ![Grundläggande om WAF-principer](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Konfigurera WAF-regler (valfritt)

När du skapar en WAF-princip är den i *identifierings* läge som standard. I identifierings läge blockerar WAF inte några förfrågningar. I stället loggas de matchande WAF-reglerna i WAF-loggarna. Om du vill se WAF i praktiken kan du ändra läges inställningarna till *förebyggande*. I skydds läge blockeras matchnings regler som definierats i ruleset som du har valt och/eller loggas i WAF-loggarna.

## <a name="managed-rules"></a>Hanterade regler

Azure-hanterade OWASP-regler är aktiverade som standard. Om du vill inaktivera en enskild regel i en regel grupp expanderar du reglerna inom den regel gruppen, markerar kryss rutan framför regel numret och väljer **inaktivera** på fliken ovan.

[![Hanterade regler ](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Anpassade regler

Om du vill skapa en anpassad regel väljer du **Lägg till anpassad regel** under fliken **anpassade regler** . Då öppnas sidan anpassad regel konfiguration. Följande skärm bild visar ett exempel på en anpassad regel som kon figurer ATS för att blockera en begäran om frågesträngen innehåller texten *blockme*.

[![Redigera anpassad regel ](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migrera WAF-konfigurationen till en WAF-princip

Om du har en befintlig WAF kan du ha lagt märke till några ändringar i portalen. Först måste du identifiera vilken typ av princip du har aktiverat på din WAF. Det finns tre möjliga tillstånd:

- Ingen WAF-princip
- Princip för anpassade regler
- WAF-princip

Du kan se vilket tillstånd din WAF finns i genom att titta på den i portalen. Om WAF-inställningarna är synliga och kan ändras från den Application Gateway visar din WAF i läge 1.

[![WAF-konfiguration ](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Om du väljer **brand vägg för webbaserade program** och visar en associerad princip, är WAF i läge 2 eller State 3. När du har navigerat till principen, om den **bara** visar anpassade regler och associerade programgatewayer, är det bara en princip för anpassad regel.

![Anpassade WAF-regler](../media/create-waf-policy-ag/waf-custom-rules.png)

Om den också visar princip inställningar och hanterade regler, är det en fullständig brand Väggs princip för webb program. 

![Princip inställningar för WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrera till WAF-princip

Om du bara har en princip för WAF för anpassade regler kan du vilja flytta till den nya WAF-principen. Brand Väggs principen kommer att ha stöd för WAF, hanterade rulesets, undantag och inaktiverade regel grupper för att gå vidare. I princip är alla WAF-konfigurationer som tidigare genomfördes i Application Gateway nu utförda genom WAF-principen. 

Ändringar av WAF-principen för anpassade regler är inaktiverade. Om du vill redigera inställningar för WAF, till exempel inaktivera regler, lägga till undantag osv. du måste migrera till en ny brand Väggs princip resurs på högsta nivån.

Det gör du genom att skapa en *brand Väggs princip för webb program* och koppla den till dina Application gateways och lyssnare (er). Den här nya principen måste vara exakt samma som den aktuella WAF-konfigurationen, vilket innebär att varje anpassad regel, exkludering, inaktive rad regel osv. måste kopieras till den nya principen som du skapar. När du har en princip som är associerad med din Application Gateway kan du fortsätta att göra ändringar i dina WAF-regler och-inställningar. Du kan också göra detta med Azure PowerShell. Mer information finns i [associera en WAF-princip med en befintlig Application Gateway](associate-waf-policy-existing-gateway.md).

Du kan också använda ett migreringsjobb för att migrera till en WAF-princip. Mer information finns i [migrera brand Väggs principer för webb program med hjälp av Azure PowerShell](migrate-policy.md).

## <a name="force-mode"></a>Framtvinga läge

Om du inte vill kopiera allting till en princip som är exakt samma som den aktuella konfigurationen kan du ange WAF i "tvång"-läge. Kör följande Azure PowerShell kod och WAF kommer att vara i läget framtvinga. Sedan kan du associera en WAF-princip till din WAF, även om den inte har exakt samma inställningar som din konfiguration. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

ProCeeS sedan med stegen för att koppla en WAF-princip till Application Gateway. Mer information finns i [associera en WAF-princip med en befintlig Application Gateway.](associate-waf-policy-existing-gateway.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om [regel grupper och regler för program vara för brand vägg för webb program brand vägg](application-gateway-crs-rulegroups-rules.md).
