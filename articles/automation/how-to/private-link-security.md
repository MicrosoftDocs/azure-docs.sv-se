---
title: Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Automation
description: Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.openlocfilehash: f3c9197faaae89e0ffb238f987ee66dafea8abdd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579809"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Använd Azures privata länk för att på ett säkert sätt ansluta nätverk till Azure Automation

Azure Private Endpoint är ett nätverksgränssnitt som ger dig en privat och säker anslutning till en tjänst som drivs av Azure Private Link. Privat slut punkt använder en privat IP-adress från ditt VNet, vilket effektivt tar automatiserings tjänsten till ditt VNet. Nätverks trafiken mellan datorerna i VNet och automation-kontot passerar över VNet och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Du har till exempel ett VNet där du har inaktiverat utgående Internet åtkomst. Men du vill ha åtkomst till ditt Automation-konto privat och använda automatiserings funktioner som Webhooks, tillstånds konfiguration och Runbook-jobb i hybrid Runbook Worker. Dessutom vill du att användarna ska ha åtkomst till Automation-kontot enbart via VNET.  Distribution av privat slut punkt uppnår dessa mål.

Den här artikeln beskriver när du ska använda och hur du konfigurerar en privat slut punkt med ditt Automation-konto.

![Konceptuell översikt över privat länk för Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Stöd för privata länkar med Azure Automation är endast tillgängligt i molnet Azures kommersiella och Azure-myndigheter.

## <a name="advantages"></a>Fördelar

Med privat länk kan du:

- Anslut privat till Azure Automation utan att öppna någon offentlig nätverks åtkomst.
- Anslut privat till Azure Monitor Log Analytics arbets yta utan att öppna någon offentlig nätverks åtkomst.

    >[!NOTE]
    >En separat privat slut punkt för din Log Analytics-arbetsyta krävs om ditt Automation-konto är länkat till en Log Analytics arbets yta för att vidarebefordra jobb data och när du har aktiverat funktioner som Uppdateringshantering, Ändringsspårning och inventering, tillstånds konfiguration eller Starta/stoppa virtuella datorer när de inte används. Mer information om privat länk för Azure Monitor finns i [Använd Azure Private Link för att ansluta nätverk på ett säkert sätt till Azure Monitor](../../azure-monitor/logs/private-link-security.md).

- Se till att dina Automation-data endast nås via auktoriserade privata nätverk.
- Förhindra data exfiltrering från dina privata nätverk genom att definiera din Azure Automation-resurs som ansluter via din privata slut punkt.
- Anslut det privata lokala nätverket på ett säkert sätt för att Azure Automation med ExpressRoute och privat länk.
- Behåll all trafik i Microsoft Azure stamnät nätverket.

Mer information finns i  [viktiga fördelar med privat länk](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Begränsningar

- I den aktuella implementeringen av privat länk går det inte att komma åt Azure-resurser som skyddas med privat slut punkt i moln jobb för Automation-kontot. Till exempel Azure Key Vault, Azure SQL, Azure Storage-konto osv. Använd en [hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) i stället för att undvika detta.
- Du måste använda den senaste versionen av [Log Analytics agent](../../azure-monitor/agents/log-analytics-agent.md) för Windows eller Linux.
- [Log Analytics Gateway](../../azure-monitor/agents/gateway.md) stöder inte privat länk.

## <a name="how-it-works"></a>Så här fungerar det

Azure Automation Private Link ansluter en eller flera privata slut punkter (och därmed de virtuella nätverk som de finns i) till din Automation-konto resurs. Dessa slut punkter är datorer som använder Webhooks för att starta en Runbook, datorer som är värdar för Hybrid Runbook Worker-rollen och DSC-noder (Desired State Configuration).

När du har skapat privata slut punkter för Automation mappas var och en av de offentliga URL: erna för Automation till en privat slut punkt i ditt VNet. Du eller en dator kan kontakta automations webb adresser direkt.

### <a name="webhook-scenario"></a>Webhook-scenario

Du kan starta runbooks genom att göra ett inlägg på webhook-URL: en. URL: en ser till exempel ut så här: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Hybrid Runbook Worker scenario

Med användar Hybrid Runbook Worker funktionen i Azure Automation kan du köra Runbooks direkt på Azure-eller icke-Azure-datorer, inklusive servrar som är registrerade med Azure Arc-aktiverade servrar. Från datorn eller servern som är värd för rollen kan du köra Runbooks direkt på den och mot resurser i miljön för att hantera de lokala resurserna.

En JRDS-slutpunkt används av hybrid Worker för att starta/stoppa Runbooks, ladda ned Runbooks till Worker och skicka jobb logg strömmen tillbaka till Automation-tjänsten.När du har aktiverat JRDS-slutpunkten skulle URL: en se ut så här: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Detta säkerställer att Runbook-körningen på Hybrid Worker som är ansluten till Azure Virtual Network kan köra jobb utan att behöva öppna en utgående anslutning till Internet.  

> [!NOTE]
>Med den aktuella implementeringen av privata Länkar för Azure Automation, stöder den bara körning av jobb på Hybrid Runbook Worker som är anslutna till ett virtuellt Azure-nätverk och stöder inte moln jobb.

## <a name="hybrid-worker-scenario-for-update-management"></a>Hybrid Worker scenario för Uppdateringshantering  

System Hybrid Runbook Worker stöder en uppsättning dolda Runbooks som används av Uppdateringshantering-funktionen som har utformats för att installera användardefinierade uppdateringar på Windows-och Linux-datorer. När Azure Automation Uppdateringshantering är aktive rad konfigureras alla datorer som är anslutna till din Log Analytics-arbetsyta automatiskt som en system Hybrid Runbook Worker.

För att förstå & konfigurera Uppdateringshantering granskning av [uppdateringshantering](../update-management/overview.md). Uppdateringshantering funktionen har ett beroende på en Log Analytics arbets yta och måste därför länka arbets ytan till ett Automation-konto. En Log Analytics-arbetsyta lagrar data som samlas in av lösningen och som värd för loggs ökningar och vyer.

Om du vill att dina datorer ska konfigureras för uppdaterings hantering för att ansluta till Automation & Log Analytics arbets yta på ett säkert sätt via en privat länk kanal, måste du aktivera privat länk för Log Analytics arbets ytan som är länkad till Automation-kontot som kon figurer ATS med privat länk.

Du kan styra hur en Log Analytics arbets yta kan nås utanför de privata länk omfattningarna genom att följa stegen som beskrivs i [konfigurera Log Analytics](../../azure-monitor/logs/private-link-security.md#configure-log-analytics). Om du ställer in **Tillåt offentligt nätverks åtkomst för** inmatning till **Nej** kan inte datorer utanför de anslutna omfattningarna Ladda upp data till den här arbets ytan. Om du ställer in **Tillåt offentlig nätverks åtkomst för frågor** till **Nej**, kan datorer utanför omfattningarna inte komma åt data på den här arbets ytan.

Använd **DSCAndHybridWorker** Target-underresurs för att aktivera privat länk för användare & system hybrid Worker.

> [!NOTE]
> Datorer som ligger utanför Azure och som hanteras av Uppdateringshantering och är anslutna till Azure VNet via ExpressRoute privat peering, VPN-tunnlar och peer-kopplade virtuella nätverk som använder privata slut punkter stöder privat länk.

### <a name="state-configuration-agentsvc-scenario"></a>Scenario för tillstånds konfiguration (agentsvc)

Med tillstånds konfiguration får du Azure konfigurations hanterings tjänst som gör att du kan skriva, hantera och kompilera DSC-konfigurationer (Desired State Configuration) för noder i molnet eller det lokala data centret.

Agenten på datorn registreras med DSC-tjänsten och använder sedan tjänstens slut punkt för att hämta DSC-konfigurationen. Agent tjänstens slut punkt ser ut så här: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

URL: en för den offentliga & privata slut punkten skulle vara densamma, men den mappas till en privat IP-adress när den privata länken är aktive rad.

## <a name="planning-based-on-your-network"></a>Planera baserat på ditt nätverk

Innan du konfigurerar din resurs för Automation-kontot bör du ta hänsyn till kraven på nätverks isolering. Utvärdera dina virtuella nätverks åtkomst till offentligt Internet och åtkomst begränsningarna för ditt Automation-konto (inklusive att konfigurera en grupp för privata länk grupper till Azure Monitor loggar om det är integrerat med ditt Automation-konto). Ta även med en granskning av Automation-tjänstens [DNS-poster](./automation-region-dns-records.md) som en del av planen för att säkerställa att de funktioner som stöds fungerar utan problem.

### <a name="connect-to-a-private-endpoint"></a>Anslut till en privat slut punkt

Skapa en privat slut punkt för att ansluta vårt nätverk. Du kan skapa den i det [Azure Portal privata länk centret](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). När dina ändringar av publicNetworkAccess och privat länk har tillämpats kan det ta upp till 35 minuter innan de börjar gälla.

I det här avsnittet ska du skapa en privat slut punkt för ditt Automation-konto.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs > nätverk > Private Link Center**.

2. I **privat länk Center – översikt**, på alternativet för att **skapa en privat anslutning till en tjänst**, väljer du **Start**.

3. I **skapa en virtuell dator – grunderna** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKT INFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.  |
    | **INSTANS INFORMATION** |  |
    | Name | Ange din *PrivateEndpoint*. |
    | Region | Välj **YourRegion**. |
    |||

4. Välj **Nästa: resurs**.

5. I **skapa en privat slut punkt – resurs**, anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj Anslut till en Azure-resurs i min katalog.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. Automation/automationAccounts**. |
    | Resurs |Välj *myAutomationAccount*|
    |Mål under resurs |Välj *webhook* eller *DSCAndHybridWorker* beroende på ditt scenario.|
    |||

6. Välj **Nästa: konfiguration**.

7. I **skapa en privat slut punkt – konfiguration** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    |**NÄTVERK**| |
    | Virtuellt nätverk| Välj *MyVirtualNetwork*. |
    | Undernät | Välj *undernät*. |
    |**PRIVAT DNS-INTEGRATION**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(ny) privatelink. Azure-Automation.net* |
    |||

8. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

9. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

I det **privata länk centret** väljer du **privata slut punkter** för att visa din privata länk resurs.

![Privat länk till Automation-resurs](./media/private-link-security/private-link-automation-resource.png)

Välj resursen för att se all information. Detta skapar en ny privat slut punkt för ditt Automation-konto och tilldelar den en privat IP-adress från det virtuella nätverket. **Anslutnings statusen** visas som **godkänd**.

På samma sätt skapas ett unikt fullständigt kvalificerat domän namn (FQDN) för tillstånds konfigurationen (agentsvc) och för Hybrid Runbook Worker jobb Runtime (jrds). Var och en av dem tilldelas en separat IP-adress från ditt VNet och **anslutnings statusen** visas som **godkänd**.

Om tjänste konsumenten har Azure RBAC-behörighet för Automation-resursen kan de välja metoden för automatiskt godkännande. I detta fall krävs ingen åtgärd från tjänst leverantören när begäran når Automation Provider-resursen och anslutningen godkänns automatiskt.

## <a name="set-public-network-access-flags"></a>Ange åtkomst flaggor för offentliga nätverk

Du kan konfigurera ett Automation-konto för att neka alla offentliga konfigurationer och bara tillåta anslutningar via privata slut punkter för att ytterligare förbättra nätverks säkerheten. Om du vill begränsa åtkomsten till Automation-kontot enbart från det virtuella nätverket och inte tillåta åtkomst från offentliga Internet kan du ange `publicNetworkAccess` egenskapen till `$false` .

När åtkomst inställningen för **offentligt nätverk** är inställt på `$false` tillåts bara anslutningar via privata slut punkter och alla anslutningar via offentliga slut punkter nekas med ett obehörigt fel meddelande och HTTP-statusen 401.

Följande PowerShell-skript visar hur du `Get` och `Set` den **offentliga nätverks åtkomst** egenskapen på Automation-konto nivån:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

Du kan också styra den offentliga nätverks åtkomst egenskapen från Azure Portal. Från ditt Automation-konto väljer du **nätverks isolering** i rutan till vänster under avsnittet **konto inställningar** . När inställningen för offentlig nätverks åtkomst är inställd på **Nej** tillåts bara anslutningar via privata slut punkter och alla anslutningar över offentliga slut punkter nekas.

![Inställningar för offentlig nätverks åtkomst](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>DNS-konfiguration

När du ansluter till en privat länk resurs med hjälp av ett fullständigt kvalificerat domän namn (FQDN) som en del av anslutnings strängen, är det viktigt att konfigurera dina DNS-inställningar på rätt sätt för att matcha den allokerade privata IP-adressen. Befintliga Azure-tjänster kanske redan har en DNS-konfiguration som ska användas vid anslutning via en offentlig slut punkt. DNS-konfigurationen bör granskas och uppdateras för att ansluta med hjälp av din privata slut punkt.

Nätverks gränssnittet som är kopplat till den privata slut punkten innehåller den fullständiga uppsättningen information som krävs för att konfigurera din DNS, inklusive FQDN och privata IP-adresser som allokerats för en specifik privat länk resurs.

Du kan använda följande alternativ för att konfigurera dina DNS-inställningar för privata slut punkter:

* Använd värd filen (rekommenderas endast för testning). Du kan använda värd filen på en virtuell dator för att åsidosätta användningen av DNS för namn matchning först. DNS-posten bör se ut som i följande exempel: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Använd en [privat DNS-zon](../../dns/private-dns-privatednszone.md). Du kan använda privata DNS-zoner för att åsidosätta DNS-matchningen för en viss privat slut punkt. En privat DNS-zon kan länkas till det virtuella nätverket för att lösa vissa domäner. Om du vill aktivera agenten på den virtuella datorn för att kommunicera över den privata slut punkten skapar du en Privat DNS-post som `privatelink.azure-automation.net` . Lägg till en ny DNS *a* -post mappning till den privata slut punktens IP-adress.

* Använd DNS-vidarebefordraren (valfritt). Du kan använda DNS-vidarebefordraren för att åsidosätta DNS-matchningen för en viss privat länk resurs. Om din [DNS-Server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) finns i ett virtuellt nätverk kan du skapa en regel för vidarebefordran av DNS för att använda en privat DNS-zon för att förenkla konfigurationen för alla privata länk resurser.

Mer information finns i [Azures DNS-konfiguration för privat slut punkt](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Nästa steg

Mer information om privata slut punkter finns i [Vad är Azures privata slut punkt?](../../private-link/private-endpoint-overview.md).