---
title: Konfigurera virtuell nätverks installation i Azure HDInsight
description: Lär dig hur du konfigurerar ett antal ytterligare funktioner för din virtuella nätverks installation i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943974"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurera virtuell nätverks installation i Azure HDInsight

> [!Important]
> Följande information krävs **bara** om du vill konfigurera en annan virtuell nätverks installation (NVA) än [Azure Firewall](./hdinsight-restrict-outbound-traffic.md).

Azure Firewall FQDN-taggen konfigureras automatiskt för att tillåta trafik för många av de vanliga viktiga FQDN: erna. Om du använder en annan virtuell nätverks installation måste du konfigurera ett antal ytterligare funktioner. Tänk på följande faktorer när du konfigurerar din virtuella nätverks installation:

* Tjänster för tjänst slut punkt som kan konfigureras med tjänst slut punkter som leder till att NVA kringgås, vanligt vis för kostnads-eller prestanda överväganden.
* Om ResourceProviderConnection är inställt på *utgående* kan du använda privata slut punkter för lagrings-och SQL-servrar för metastores och du behöver inte lägga till dem i NVA.
* IP-adress beroenden är för trafik som inte är HTTP/S (både TCP-och UDP-trafik).
* FQDN HTTP/HTTPS-slutpunkter kan godkännas i din NVA-enhet.
* Tilldela den routningstabell som du skapar till ditt HDInsight-undernät.

## <a name="service-endpoint-capable-dependencies"></a>Tjänst slut punkt kompatibla beroenden

Du kan välja att aktivera en eller flera av följande tjänst slut punkter, vilket leder till att NVA ignoreras. Det här alternativet kan vara användbart för stora mängder data överföringar för att spara pengar och även för prestanda optimering. 

| **Slutpunkt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP-adress beroenden

| **Slutpunkt** | **Information** |
|---|---|
| IP-adresser publicerade [här](hdinsight-management-ip-addresses.md) | De här IP-adresserna är för HDInsight-resurs leverantör och bör ingå i UDR för att undvika asymmetrisk routning. Den här regeln behövs bara om ResourceProviderConnection är inställt på *inkommande*. Om ResourceProviderConnection är inställt på *utgående* behövs inte dessa IP-adresser i UDR.  |
| AAD – DS privata IP-adresser | Krävs endast för ESP-kluster, om virtuella nätverk inte är peer-datorer.|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden

Du kan hämta listan över FQDN-beroenden (de flesta Azure Storage och Azure Service Bus) för att konfigurera din NVA [i den här lagrings platsen](https://github.com/Azure-Samples/hdinsight-fqdn-lists/). Information om den regionala listan finns [här](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional). Dessa beroenden används av HDInsight Resource Provider (RP) för att skapa och övervaka/hantera kluster. Detta inkluderar telemetri/diagnostikloggar, etablering av metadata, klustrade konfigurationer, skript osv. Den här FQDN-beroende listan kan ändras med att släppa framtida HDInsight-uppdateringar.

Listan nedan innehåller bara några fullständiga domän namn som kan behövas för operativ system-och säkerhets korrigeringar eller certifikat verifieringar *när* klustret har skapats och under kluster drifts tiden:

| **FQDN-beroenden för körning**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Nästa steg

* [Använda brandväggen för att begränsa utgående trafik](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)
