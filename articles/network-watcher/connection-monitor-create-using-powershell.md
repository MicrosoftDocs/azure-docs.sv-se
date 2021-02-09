---
title: Skapa en anslutnings övervakare – PowerShell
titleSuffix: Azure Network Watcher
description: Lär dig hur du skapar en anslutnings övervakare med hjälp av PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833039"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Skapa en anslutnings övervakare med hjälp av PowerShell

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kommer inte heller att kunna lägga till nya anslutnings övervakare i anslutnings övervakaren (klassisk). Du kan fortsätta att använda de tester och anslutnings Övervakare som skapades före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda](migrate-to-connection-monitor-from-network-performance-monitor.md) eller [Migrera från anslutnings övervakaren (klassisk)](migrate-to-connection-monitor-from-connection-monitor-classic.md) till den nya anslutnings övervakaren i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.


Lär dig hur du använder funktionen anslutnings övervakaren i Azure Network Watcher för att övervaka kommunikationen mellan dina resurser.


## <a name="before-you-begin"></a>Innan du börjar

I anslutnings Övervakare som du skapar med anslutnings övervakaren kan du lägga till både lokala datorer och virtuella Azure-datorer (VM) som källor. Dessa anslutnings övervakare kan också övervaka anslutningar till slut punkter. Slut punkterna kan vara på Azure eller någon annan URL eller IP-adress.

En anslutnings övervakare innehåller följande entiteter:

* **Anslutnings övervaknings resurs**: en landsspecifika Azure-resurs. Alla följande entiteter är egenskaper för anslutnings övervaknings resursen.
* **Slut punkt**: en källa eller ett mål som deltar i anslutnings kontroller. Exempel på slut punkter är virtuella Azure-datorer, lokala agenter, URL: er och IP-adresser.
* **Test konfiguration**: en protokoll-speciell konfiguration för ett test. Utifrån det protokoll du väljer kan du definiera port, tröskelvärden, test frekvens och andra parametrar.
* **Test grupp**: gruppen som innehåller käll slut punkter, mål slut punkter och testkonfigurationer. En anslutnings övervakare kan innehålla fler än en test grupp.
* **Test**: kombinationen av en käll slut punkt, mål slut punkt och test konfiguration. Ett test är den mest detaljerade nivån där övervaknings data är tillgängliga. Övervaknings data innehåller procent andelen kontroller som misslyckades och tur och retur-tiden.

    ![Diagram som visar en anslutnings Övervakare som definierar relationen mellan test grupper och tester.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Steg för att skapa en anslutnings övervakare

Använd följande kommandon för att skapa en anslutnings övervakare med hjälp av PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Beskrivning av egenskaper

* **ConnectionMonitorName**: namnet på anslutnings övervaknings resursen.

* **Sub**: PRENUMERATIONS-ID för den prenumeration där du vill skapa en anslutnings övervakare.

* **NW**: Network Watcher resurs-ID som en anslutnings övervakare skapas i.

* **Plats**: region där en anslutnings övervakare skapas.

* **Slutpunkter**
    * **Namn**: unikt namn för varje slut punkt.
    * **Resurs-ID**: för Azure-slutpunkter refererar resurs-id till Azure Resource Manager resurs-ID för virtuella datorer. För icke-Azure-slutpunkter refererar resurs-ID till Azure Resource Manager resurs-ID för arbets ytan Log Analytics som är länkad till icke-Azure-agenter.
    * **Adress**: används endast när resurs-ID inte har angetts eller om resurs-ID: t finns i Log Analytics arbets ytan. Om det används utan ett resurs-ID kan detta vara webb adressen eller IP-adressen för en offentlig slut punkt. Om det används med ett Log Analytics resurs-ID, refererar detta till det fullständiga domän namnet för övervaknings agenten.
    * **Filter**: för icke-Azure-slutpunkter använder du filter för att välja övervaknings agenter från arbets ytan Log Analytics i anslutnings övervaknings resursen. Om inga filter anges kan alla agenter som hör till Log Analytics arbets ytan användas för övervakning.
        * **Typ**: Ange som **agent adress**.
        * **Adress**: Ange som FQDN för den lokala agenten.

* **Test grupper**
    * **Namn**: ge test gruppen ett namn.
    * **Källor**: Välj från de slut punkter som du skapade tidigare. Azure-baserade käll slut punkter måste ha Azure Network Watcher-tillägget installerat. en Azure-Log Analytics-agent måste vara installerad för käll slut punkter som inte är Azure-baserade. Information om hur du installerar en agent för din källa finns i [Installera övervaknings agenter](./connection-monitor-overview.md#install-monitoring-agents).
    * **Mål**: Välj från de slut punkter som du skapade tidigare. Du kan övervaka anslutningar till virtuella Azure-datorer eller en slut punkt (en offentlig IP-adress, URL eller FQDN) genom att ange dem som mål. I en enda test grupp kan du lägga till virtuella Azure-datorer, Office 365-URL: er, Dynamics 365-URL: er och anpassade slut punkter.
    * **Inaktivera**: inaktivera övervakning för alla källor och mål som test gruppen anger.

* **Testa konfigurationer**
    * **Namn**: ge test konfigurationen ett namn.
    * **TestFrequencySec**: Ange hur ofta ping-mål ska skickas till det protokoll och den port som du har angett. Du kan välja 30 sekunder, 1 minut, 5 minuter, 15 minuter eller 30 minuter. Källor testar anslutningen till destinationer utifrån det värde som du väljer. Om du till exempel väljer 30 sekunder, kontrollerar källorna anslutningen till målet minst en gång under en 30-sekunders period.
    * **Protokoll**: Välj TCP, ICMP, http eller https. Beroende på protokollet kan du också välja följande protokoll-/regionsspecifika konfigurationer:
        * **preferHTTPS**: Ange om https ska användas över http.
        * **port**: Ange den valda mål porten.
        * **disableTraceRoute**: stoppa källor från att upptäcka topologi och Sök efter hopp. Detta gäller för test grupper med TCP eller ICMP.
        * **metod**: Välj metoden för http-begäran (Get eller post). Detta gäller för att testa konfigurationer med HTTP.
        * **sökväg**: Ange Sök vägs parametrar som ska läggas till i URL: en.
        * **validStatusCodes**: Välj tillämpliga status koder. Om svars koden inte matchar visas ett diagnostiskt meddelande.
        * **requestHeaders**: Ange anpassade rubrik strängar för begäran som skickas till målet.
    * **Tröskelvärde för lyckade**: ange tröskelvärden för följande nätverks parametrar:
        * **checksFailedPercent**: Ange procent andelen kontroller som kan utföras när källor kontrollerar anslutningen till destinationer med de villkor som du har angett. Procent andelen misslyckade kontroller för TCP-eller ICMP-protokollet kan vara lika med procent andelen paket förlust. För HTTP-protokoll representerar det här fältet den procent andel av HTTP-begäranden som inte fick något svar.
        * **roundTripTimeMs**: Ange hur lång tid källorna kan ta för att ansluta till målet via test konfigurationen i millisekunder.

## <a name="scale-limits"></a>Skalnings gränser

Anslutnings övervakare har följande skalnings gränser:

* Högsta antal anslutnings övervakare per prenumeration per region: 100
* Högsta antal test grupper per anslutnings Övervakare: 20
* Högsta antal källor och mål per anslutnings Övervakare: 100
* Högsta antal testkonfigurationer per anslutnings Övervakare: 20

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du analyserar övervaknings data och ställer in aviseringar](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Lär dig [hur du diagnostiserar problem i nätverket](./connection-monitor-overview.md#diagnose-issues-in-your-network).
