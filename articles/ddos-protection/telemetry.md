---
title: Visa och konfigurera DDoS Protection-telemetri för Azure DDoS Protection standard
description: Lär dig hur du visar och konfigurerar DDoS Protection-telemetri för Azure DDoS Protection standard.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a3f6c14b7ed2686a262f28510efb37068cfb9cb3
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787306"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Visa och konfigurera telemetri för DDoS-skydd

Azure DDoS Protection standard ger detaljerad information om angrepp och visualisering med DDoS-attack analys. Kunder som skyddar sina virtuella nätverk mot DDoS-attacker har detaljerad insyn i attack trafik och åtgärder som vidtas för att minska risken för angrepp med hjälp av angrepps minsknings rapporter & skydds flödes loggar. Avancerad telemetri exponeras via Azure Monitor inklusive detaljerade mått under ett DDoS-angrepp. Aviseringar kan konfigureras för alla Azure Monitor mått som visas av DDoS Protection. Loggning kan integreras ytterligare med [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Visa DDoS Protection-telemetri
> * Visa DDoS-principer för minskning
> * Verifiera och testa DDoS Protection-telemetri

### <a name="metrics"></a>Mått

> [!NOTE]
> När flera alternativ för **agg regering** visas på Azure Portal, stöds endast de agg regerings typer som anges i tabellen nedan för varje mått. Vi beklagar den här förvirringen och vi arbetar för att lösa problemet.

Följande [mått](../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses) är tillgängliga för Azure DDoS Protection standard. Dessa mått kan också exporteras via diagnostikinställningar (se [Visa och konfigurera DDoS diagnostisk loggning](diagnostic-logging.md)).


| Metric | Mått visnings namn | Enhet | Sammansättningstyp | Beskrivning |
| --- | --- | --- | --- | --- |
| ByteCount | Antal byte | Antal | Totalt | Totalt antal byte som skickats inom tids perioden |
| BytesDroppedDDoS | Ignorerade inkommande byte DDoS | BytesPerSecond | Maximal | Ignorerade inkommande byte DDoS| 
| BytesForwardedDDoS | Inkommande byte, vidarebefordrade DDoS | BytesPerSecond | Maximal | Inkommande byte, vidarebefordrade DDoS |
| BytesInDDoS | DDoS för inkommande byte | BytesPerSecond | Maximal | DDoS för inkommande byte |
| DDoSTriggerSYNPackets | Inkommande SYN paket för att utlösa DDoS-minskning | CountPerSecond | Maximal | Inkommande SYN paket för att utlösa DDoS-minskning |
| DDoSTriggerTCPPackets | Inkommande TCP-paket för att utlösa DDoS-minskning | CountPerSecond | Maximal | Inkommande TCP-paket för att utlösa DDoS-minskning |
| DDoSTriggerUDPPackets | Ingående UDP-paket för att utlösa DDoS-minskning | CountPerSecond | Maximal | Ingående UDP-paket för att utlösa DDoS-minskning |
| IfUnderDDoSAttack | Under DDoS-attack eller inte | Antal | Maximal | Under DDoS-attack eller inte |
| PacketCount | Antal paket | Antal | Totalt | Totalt antal överförda paket inom tids perioden |
| PacketsDroppedDDoS | Inkommande paket som släppts DDoS | CountPerSecond | Maximal | Inkommande paket som släppts DDoS |
| PacketsForwardedDDoS | Vidarebefordrade inkommande paket DDoS | CountPerSecond | Maximal | Vidarebefordrade inkommande paket DDoS |
| PacketsInDDoS | DDoS för inkommande paket | CountPerSecond | Maximal | DDoS för inkommande paket |
| SynCount | Antal SYN | Antal | Totalt | Totalt antal skickade SYN paket inom tids perioden |
| TCPBytesDroppedDDoS | Inkommande TCP-byte utelämnade DDoS | BytesPerSecond | Maximal | Inkommande TCP-byte utelämnade DDoS |
| TCPBytesForwardedDDoS | Inkommande TCP byte-vidarebefordrade DDoS | BytesPerSecond | Maximal | Inkommande TCP byte-vidarebefordrade DDoS |
| TCPBytesInDDoS | DDoS för inkommande TCP-byte | BytesPerSecond | Maximal | DDoS för inkommande TCP-byte |
| TCPPacketsDroppedDDoS | Inkommande TCP-paket ignorerade DDoS | CountPerSecond | Maximal | Inkommande TCP-paket ignorerade DDoS |
| TCPPacketsForwardedDDoS | Inkommande TCP-paket, vidarebefordrade DDoS | CountPerSecond | Maximal | Inkommande TCP-paket, vidarebefordrade DDoS |
| TCPPacketsInDDoS | DDoS inkommande TCP-paket | CountPerSecond | Maximal | DDoS inkommande TCP-paket |
| UDPBytesDroppedDDoS | Inkommande UDP-byte utelämnade DDoS | BytesPerSecond | Maximal | Inkommande UDP-byte utelämnade DDoS |
| UDPBytesForwardedDDoS | Inkommande UDP byte vidarebefordrade DDoS | BytesPerSecond | Maximal | Inkommande UDP byte vidarebefordrade DDoS |
| UDPBytesInDDoS | DDoS för inkommande UDP-byte | BytesPerSecond | Maximal | DDoS för inkommande UDP-byte |
| UDPPacketsDroppedDDoS | Ignorerade inkommande UDP-paket DDoS | CountPerSecond | Maximal | Ignorerade inkommande UDP-paket DDoS |
| UDPPacketsForwardedDDoS | Vidarebefordrade inkommande UDP-paket DDoS | CountPerSecond | Maximal | Vidarebefordrade inkommande UDP-paket DDoS |
| UDPPacketsInDDoS | DDoS för inkommande UDP-paket | CountPerSecond | Maximal | DDoS för inkommande UDP-paket |
| VipAvailability | Tillgänglighet för datasökvägar | Antal | Genomsnitt | Genomsnittlig tillgänglighet för IP-adress per tids längd |

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md) och DDoS Protection standard måste aktive ras på ett virtuellt nätverk.
- DDoS övervakar offentliga IP-adresser tilldelade till resurser i ett virtuellt nätverk. Om du inte har några resurser med offentliga IP-adresser i det virtuella nätverket måste du först skapa en resurs med en offentlig IP-adress. Du kan övervaka den offentliga IP-adressen för alla resurser som distribueras via Resource Manager (inte klassisk) som listas i [virtuellt nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inklusive Azure Load Balancer där de virtuella datorerna finns i det virtuella nätverket), förutom för Azure App Service miljöer. Om du vill fortsätta med den här självstudien kan du snabbt skapa en virtuell [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator.  

## <a name="view-ddos-protection-telemetry"></a>Visa DDoS Protection-telemetri

Telemetri för ett angrepp tillhandahålls via Azure Monitor i real tid. Telemetri är bara tillgängligt för den varaktighet som en offentlig IP-adress är under minskning. Du ser inte telemetri före eller efter att en attack har begränsats.

1. Logga in på [Azure Portal](https://portal.azure.com/) och bläddra till din DDoS Protection-plan.
2. Gå till **Övervakning** och välj **Mått**.
3. Välj **omfång**. Välj den **prenumeration** som innehåller den offentliga IP-adress som du vill logga, Välj **offentlig IP-adress** för **resurs typ** och välj sedan den angivna offentliga IP-adress som du vill logga mått för och välj sedan **Använd**.
4. Välj **agg regerings** typ som **Max**.

Mått namnen visar olika paket typer och byte jämfört med paket, med en grundläggande konstruktion av taggnamn på varje mått enligt följande:

- **Taggat taggnamn** (till exempel **inkommande paket som släppts DDoS**): antalet paket som tappas/rensas av DDoS Protection System.
- **Namn på vidarebefordrad tagg** (t. ex. **inkommande paket vidarebefordrade DDoS**): antalet paket som vidarebefordras av DDoS-systemet till den virtuella mål-VIP – trafik som inte har filtrerats.
- **Inget taggnamn** (till exempel **inkommande paket DDoS**): det totala antalet paket som ingår i skrubbnings systemet – som representerar summan av de paket som har släppts och vidarebefordrats.

## <a name="view-ddos-mitigation-policies"></a>Visa DDoS-principer för minskning

DDoS Protection standard tillämpar tre automatiskt justerade skydds principer (TCP-SYN, TCP & UDP) för varje offentlig IP-adress för den skyddade resursen, i det virtuella nätverk där DDoS har Aktiver ATS. Du kan visa princip tröskelvärdena genom att välja de  **inkommande TCP-paketen för att utlösa DDoS-minskning** och **inkommande UDP-paket för att utlösa DDoS** -hälsomått med **agg regerings** typ som "Max", som du ser i följande bild:

![Visa begränsnings principer](./media/manage-ddos-protection/view-mitigation-policies.png)

Princip tröskelvärden konfigureras automatiskt via Azure Machine Learning-baserad nätverks trafik profilering. Endast när princip tröskeln har brutits inträffar DDoS-lösningen för IP-adressen under angrepp.

## <a name="validate-and-test"></a>Validera och testa

Information om hur du simulerar en DDoS-attack för att verifiera DDoS Protection-telemetri finns i [validate DDoS Detection](test-through-simulations.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

- Konfigurera aviseringar för DDoS skydds mått
- Visa DDoS Protection-telemetri
- Visa DDoS-principer för minskning
- Verifiera och testa DDoS Protection-telemetri

Fortsätt till nästa självstudie om du vill lära dig hur du konfigurerar rapporter om attack minskning och flödes loggar.

> [!div class="nextstepaction"]
> [Visa och konfigurera diagnostisk loggning för DDoS-skydd](diagnostic-logging.md)