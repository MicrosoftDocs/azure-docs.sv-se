---
title: Använd Azure Private Link för att ansluta nätverk till Azure Automation
description: Använd Azure Private Link för att ansluta nätverk till Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28f4c314b65a27c71c7620ff5941463b1ea68b55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831464"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Använd Azure Private Link för att ansluta nätverk till Azure Automation

Azure Private Endpoint är ett nätverksgränssnitt som ger dig en privat och säker anslutning till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt VNet, vilket effektivt tar Automation-tjänsten till ditt virtuella nätverk. Nätverkstrafik mellan datorerna i det virtuella nätverket och Automation-kontot passerar över det virtuella nätverket och en privat länk i Microsofts stamnätverk, vilket eliminerar exponeringen från det offentliga Internet.

Du kan till exempel ha ett VNet där du har inaktiverat utgående Internetåtkomst. Du vill dock komma åt ditt Automation-konto privat och använda Automation-funktioner som Webhooks, State Configuration och Runbook-jobb på Hybrid Runbook Workers. Dessutom vill du att användarna endast ska ha åtkomst till Automation-kontot via det virtuella nätverket.  Distribution av privat slutpunkt uppnår dessa mål.

Den här artikeln beskriver när du ska använda och hur du ställer in en privat slutpunkt med ditt Automation-konto.

![Konceptuell översikt över Private Link för Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Private Link support med Azure Automation är endast tillgängligt i Azure Commercial- och Azure US Government-moln.

## <a name="advantages"></a>Fördelar

Med Private Link kan du:

- Anslut privat till Azure Automation utan att öppna någon offentlig nätverksåtkomst.
- Anslut privat till en Azure Monitor Log Analytics-arbetsyta utan att öppna någon offentlig nätverksåtkomst.

    >[!NOTE]
    >En separat privat slutpunkt för Log Analytics-arbetsytan krävs om ditt Automation-konto är länkat till en Log Analytics-arbetsyta för att vidarebefordra jobbdata och när du har aktiverat funktioner som Uppdateringshantering, Ändringsspårning och inventering, State Configuration eller Starta/stoppa virtuella datorer när de inte används. Mer information om hur Private Link för Azure Monitor finns i Använda Azure Private Link för att ansluta nätverk [till Azure Monitor](../../azure-monitor/logs/private-link-security.md).

- Se till att dina Automation-data endast nås via auktoriserade privata nätverk.
- Förhindra data exfiltrering från dina privata nätverk genom att definiera Azure Automation som ansluter via din privata slutpunkt.
- Anslut ditt privata lokala nätverk på ett säkert sätt till Azure Automation ExpressRoute och Private Link.
- Håll all trafik i Microsoft Azure stamnätverk.

Mer information finns i  [Viktiga fördelar med att Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Begränsningar

- I den aktuella implementeringen av Private Link kan automation-kontomolnjobb inte komma åt Azure-resurser som skyddas med hjälp av privat slutpunkt. Till exempel Azure Key Vault, Azure SQL, Azure Storage konto osv. Du kan lösa problemet genom att använda [en Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) i stället.
- Du måste använda den senaste versionen av [Log Analytics-agenten](../../azure-monitor/agents/log-analytics-agent.md) för Windows eller Linux.
- [Log Analytics Gateway stöder](../../azure-monitor/agents/gateway.md) inte Private Link.

## <a name="how-it-works"></a>Så här fungerar det

Azure Automation Private Link ansluter en eller flera privata slutpunkter (och därmed de virtuella nätverk som de finns i) till din Automation-kontoresurs. Dessa slutpunkter är datorer som använder webhooks för att starta en runbook, datorer som är värdar för Hybrid Runbook Worker-rollen och Desired State Configuration-noder (DSC).

När du har skapat privata slutpunkter för Automation mappas var och en av de offentliga Automation-URL:erna till en privat slutpunkt i ditt virtuella nätverk. Du eller en dator kan kontakta Automation-URL:erna direkt.

### <a name="webhook-scenario"></a>Webhook-scenario

Du kan starta runbooks genom att göra en POST på webhook-URL:en. URL:en ser till exempel ut så här: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Hybrid Runbook Worker scenario

Med Hybrid Runbook Worker-funktionen i Azure Automation kan du köra runbooks direkt på Azure- eller icke-Azure-datorn, inklusive servrar som är registrerade med Azure Arc aktiverade servrar. Från den dator eller server som är värd för rollen kan du köra runbooks direkt på den och mot resurser i miljön för att hantera de lokala resurserna.

EnFCDS-slutpunkt används av Hybrid Worker för att starta/stoppa runbooks, ladda ned runbooks till arbetsrollen och för att skicka tillbaka jobbloggströmmen till Automation-tjänsten.När DU har aktivera ENDPOINTDS-slutpunkten skulle URL:en se ut så här: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Detta säkerställer att runbook-körningen på den Hybrid Worker som är ansluten till Azure Virtual Network kan köra jobb utan att behöva öppna en utgående anslutning till Internet.  

> [!NOTE]
>Med den aktuella implementeringen av privata länkar för Azure Automation stöder den endast jobb som körs på Hybrid Runbook Worker som är anslutna till ett virtuellt Azure-nätverk och stöder inte molnjobb.

## <a name="hybrid-worker-scenario-for-update-management"></a>Hybrid Worker scenario för Uppdateringshantering  

Systemprofilen Hybrid Runbook Worker en uppsättning dolda runbooks som används av Uppdateringshantering-funktionen som är utformad för att installera användar angivna uppdateringar på Windows- och Linux-datorer. När Azure Automation Uppdateringshantering har aktiverats konfigureras en dator som är ansluten till Log Analytics-arbetsytan automatiskt som en Hybrid Runbook Worker.

Information om hur & konfigurerar Uppdateringshantering finns [i Om Uppdateringshantering](../update-management/overview.md). Funktionen Uppdateringshantering är beroende av en Log Analytics-arbetsyta och kräver därför att arbetsytan länkas till ett Automation-konto. En Log Analytics-arbetsyta lagrar data som samlas in av lösningen och är värd för dess loggsökningar och vyer.

Om du vill att dina datorer ska konfigureras för uppdateringshantering för att ansluta till Automation & Log Analytics-arbetsytan på ett säkert sätt via Private Link-kanalen måste du aktivera Private Link för Log Analytics-arbetsytan som är länkad till Automation-kontot som konfigurerats med Private Link.

Du kan styra hur en Log Analytics-arbetsyta kan nås utanför Private Link genom att följa stegen som beskrivs i [Konfigurera Log Analytics.](../../azure-monitor/logs/private-link-security.md#configure-log-analytics) Om du anger **Tillåt offentlig nätverksåtkomst för inmatning** till **Nej** kan datorer utanför de anslutna omfången inte ladda upp data till den här arbetsytan. Om du anger **Tillåt offentlig nätverksåtkomst för** frågor till **Nej** kan datorer utanför omfången inte komma åt data på den här arbetsytan.

Använd **underresursen DSCAndHybridWorker** som mål för att Private Link för användare & systemhybridarbetare.

> [!NOTE]
> Datorer som hanteras utanför Azure som hanteras av Uppdateringshantering och som är anslutna till det virtuella Azure-nätverket via privat ExpressRoute-peering, VPN-tunnlar och peerkopplade virtuella nätverk med hjälp av privata slutpunkter stöder Private Link.

### <a name="state-configuration-agentsvc-scenario"></a>State Configuration (agentsvc)

State Configuration tillhandahåller azure-konfigurationshanteringstjänsten som gör att du kan skriva, hantera och kompilera PowerShell Desired State Configuration-konfigurationer (DSC) för noder i alla molndatacenter eller lokala datacenter.

Agenten på datorn registreras med DSC-tjänsten och använder sedan tjänstslutpunkten för att hämta DSC-konfigurationen. Agenttjänstslutpunkten ser ut så här: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

URL:en för & privata slutpunkten skulle vara densamma, men den skulle mappas till en privat IP-adress när Privat länk är aktiverad.

## <a name="planning-based-on-your-network"></a>Planera baserat på ditt nätverk

Innan du ställer in automationskontoresursen bör du tänka igenom kraven på nätverksisolering. Utvärdera dina virtuella nätverks åtkomst till offentligt Internet och åtkomstbegränsningarna för ditt Automation-konto (inklusive att konfigurera ett Private Link-gruppomfång för att Azure Monitor-loggar om de är integrerade med ditt Automation-konto). Ta även med en granskning av Automation-tjänstens [DNS-poster](./automation-region-dns-records.md) som en del av din plan för att se till att de funktioner som stöds fungerar utan problem.

### <a name="connect-to-a-private-endpoint"></a>Ansluta till en privat slutpunkt

Skapa en privat slutpunkt för att ansluta nätverket. Du kan skapa den i [Azure Portal Private Link center.](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) När ändringarna i publicNetworkAccess och Private Link har tillämpats kan det ta upp till 35 minuter för dem att gälla.

I det här avsnittet skapar du en privat slutpunkt för ditt Automation-konto.

1. Längst upp till vänster på skärmen väljer du Skapa en **resurs för > Nätverk > Private Link Center**.

2. I **Private Link Center – Översikt** väljer du Starta för **att** skapa en privat anslutning till en **tjänst.**

3. I **Skapa en virtuell dator – Grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.  |
    | **INSTANSINFORMATION** |  |
    | Name | Ange din *PrivateEndpoint*. |
    | Region | Välj **YourRegion**. |
    |||

4. Välj **Nästa: Resurs**.

5. I **Skapa en privat slutpunkt –** Resurs anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj Anslut till en Azure-resurs i min katalog.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft.Automation/automationKonto.** |
    | Resurs |Välj *myAutomationAccount*|
    |Målunderkälla |Välj *Webhook* eller *DSCAndHybridWorker* beroende på ditt scenario.|
    |||

6. Välj **Nästa: Konfiguration.**

7. I **Skapa en privat slutpunkt – Konfiguration** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    |**NÄTVERK**| |
    | Virtuellt nätverk| Välj *MyVirtualNetwork*. |
    | Undernät | Välj *mySubnet*. |
    |**PRIVAT DNS-INTEGRATION**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(Nytt)privatelink.azure-automation.net* |
    |||

8. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

9. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

I den **Private Link Center** väljer du **Privata slutpunkter för** att visa din privata länkresurs.

![Privat länk för Automation-resurs](./media/private-link-security/private-link-automation-resource.png)

Välj resursen för att se all information. Detta skapar en ny privat slutpunkt för ditt Automation-konto och tilldelar den en privat IP-adress från det virtuella nätverket. **Anslutningsstatusen** visas som **godkänd.**

På samma sätt skapas ett unikt fullständigt kvalificerat domännamn (FQDN) för State Configuration (agentsvc) och för Hybrid Runbook Worker jobbkörningen (jds). Var och en av dem tilldelas en separat IP-adress från ditt VNet och **anslutningsstatusen** visas som **godkänd.**

Om tjänstkonsumenten har Azure RBAC-behörigheter för Automation-resursen kan han eller hon välja metod för automatiskt godkännande. I det här fallet när begäran når Automation-providerresursen krävs ingen åtgärd från tjänstleverantören och anslutningen godkänns automatiskt.

## <a name="set-public-network-access-flags"></a>Ange flaggor för offentlig nätverksåtkomst

Du kan konfigurera ett Automation-konto för att neka alla offentliga konfigurationer och endast tillåta anslutningar via privata slutpunkter för att ytterligare förbättra nätverkssäkerheten. Om du vill begränsa åtkomsten till Automation-kontot från endast det virtuella nätverket och inte tillåta åtkomst från offentligt Internet kan du ange `publicNetworkAccess` egenskapen till `$false` .

När inställningen **Offentlig nätverksåtkomst** är inställd på tillåts endast anslutningar via privata slutpunkter och alla anslutningar via offentliga slutpunkter nekas med ett felmeddelande om obehörig åtkomst och `$false` HTTP-status 401.

Följande PowerShell-skript visar hur du `Get` gör och egenskapen Offentlig `Set` **nätverksåtkomst** på Automation-kontonivå:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

Du kan också styra egenskapen för offentlig nätverksåtkomst från Azure Portal. Från ditt Automation-konto väljer **du Nätverksisolering** i det vänstra fönstret under **avsnittet Kontoinställningar.** När inställningen Offentlig nätverksåtkomst är inställd på **Nej** tillåts endast anslutningar över privata slutpunkter och alla anslutningar över offentliga slutpunkter nekas.

![Inställning för offentlig nätverksåtkomst](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>DNS-konfiguration

När du ansluter till en privat länkresurs med ett fullständigt kvalificerat domännamn (FQDN) som en del av anslutningssträngen är det viktigt att konfigurera DNS-inställningarna korrekt så att de matchar den allokerade privata IP-adressen. Befintliga Azure-tjänster kanske redan har en DNS-konfiguration som ska användas vid anslutning via en offentlig slutpunkt. DIN DNS-konfiguration bör granskas och uppdateras för att ansluta med hjälp av din privata slutpunkt.

Nätverksgränssnittet som är associerat med den privata slutpunkten innehåller den fullständiga uppsättningen information som krävs för att konfigurera din DNS, inklusive FQDN och privata IP-adresser som allokerats för en viss privat länkresurs.

Du kan använda följande alternativ för att konfigurera DNS-inställningarna för privata slutpunkter:

* Använd värdfilen (rekommenderas endast för testning). Du kan använda värdfilen på en virtuell dator för att åsidosätta dns för namnmatchning först. DNS-posten bör se ut som i följande exempel: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Använd en [privat DNS-zon.](../../dns/private-dns-privatednszone.md) Du kan använda privata DNS-zoner för att åsidosätta DNS-upplösningen för en viss privat slutpunkt. En privat DNS-zon kan länkas till ditt virtuella nätverk för att lösa specifika domäner. Om du vill att agenten på den virtuella datorn ska kunna kommunicera via den privata slutpunkten skapar du en Privat DNS post som `privatelink.azure-automation.net` . Lägg till en ny DNS *A-postmappning* till IP-adressen för den privata slutpunkten.

* Använd din DNS-vidarebefordrare (valfritt). Du kan använda din DNS-vidarebefordrare för att åsidosätta DNS-upplösningen för en viss privat länkresurs. Om [DIN DNS-server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) finns i ett virtuellt nätverk kan du skapa en DNS-vidare vidarebefordransregel för att använda en privat DNS-zon för att förenkla konfigurationen för alla privata länkresurser.

Mer information finns i [DNS-konfiguration för privat slutpunkt i Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Nästa steg

Mer information om privat slutpunkt finns i [Vad är privat Azure-slutpunkt?](../../private-link/private-endpoint-overview.md).
