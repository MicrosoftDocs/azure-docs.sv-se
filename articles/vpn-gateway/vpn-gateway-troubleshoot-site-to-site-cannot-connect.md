---
title: 'Felsöka en Azure plats-till-plats VPN-anslutning som inte kan ansluta titleSuffix: Azure VPN Gateway'
description: Lär dig hur du felsöker en plats-till-plats-VPN-anslutning som plötsligt slutar fungera och inte kan återanslutas.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: cebb05b35379573fc9797e89dee3c0c2bf3de6e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867295"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Fel sökning: en Azure plats-till-plats-VPN-anslutning kan inte ansluta och sluta fungera

När du har konfigurerat en plats-till-plats-VPN-anslutning mellan ett lokalt nätverk och ett virtuellt Azure-nätverk slutar VPN-anslutningen plötsligt att fungera och går inte att ansluta igen. Den här artikeln innehåller fel söknings steg som hjälper dig att lösa det här problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Lös problemet genom att först försöka [återställa Azure VPN-gatewayen](./reset-gateway.md) och återställa tunneln från den lokala VPN-enheten. Om problemet kvarstår följer du stegen nedan för att identifiera orsaken till problemet.

### <a name="prerequisite-step"></a>Förutsättnings steg

Kontrol lera typen av Azure VPN-gateway.

1. Gå till [Azure-portalen](https://portal.azure.com).

2. På sidan **Översikt** i VPN-gatewayen finns typ information.
    
    ![Översikt över gatewayen](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Steg 1. Kontrol lera om den lokala VPN-enheten har verifierats

1. Kontrol lera om du använder en [verifierad VPN-enhet och operativ system version](vpn-gateway-about-vpn-devices.md#devicetable). Om enheten inte är en verifierad VPN-enhet kan du behöva kontakta enhets tillverkaren för att se om det finns något kompatibilitetsproblem.

2. Kontrol lera att VPN-enheten är korrekt konfigurerad. Mer information finns i [Redigera exempel på enhets konfiguration](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Steg 2. Verifiera den delade nyckeln

Jämför den delade nyckeln för den lokala VPN-enheten med Azure Virtual Network VPN för att kontrol lera att nycklarna matchar. 

Använd någon av följande metoder för att visa den delade nyckeln för Azure VPN-anslutningen:

**Azure-portalen**

1. Gå till VPN gateway plats-till-plats-anslutningen som du skapade.

2. I avsnittet **Inställningar** klickar du på **delad nyckel**.
    
    ![Delad nyckel](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För Azure Resource Manager distributions modell:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

För den klassiska distributions modellen:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>Steg 3. Verifiera VPN peer IP-adresser

-   IP-definitionen i objektet **lokal** nätverksgateway i Azure måste matcha IP-adressen för den lokala enheten.
-   IP-definitionen för Azure gateway som anges på den lokala enheten ska matcha IP-adressen för Azure Gateway.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Steg 4. Kontrol lera UDR och NSG: er i Gateway-undernätet

Sök efter och ta bort användardefinierad routning (UDR) eller nätverks säkerhets grupper (NSG: er) i Gateway-undernätet och testa sedan resultatet. Om problemet är löst kontrollerar du inställningarna som UDR eller NSG tillämpas på.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Steg 5. Kontrol lera den lokala VPN-enhetens externa gränssnitts adress

Om VPN-enhetens IP-adress på Internet ingår i den **lokala nätverks** definitionen i Azure kan det uppstå sporadiska från kopplingar.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Steg 6. Kontrol lera att undernät matchar exakt (Azure policy-baserade gatewayer)

-   Kontrol lera att adress utrymmena för det virtuella nätverket matchar exakt mellan det virtuella Azure-nätverket och de lokala definitionerna.
-   Kontrol lera att under näten matchar exakt mellan den **lokala** Nätverksgatewayen och lokala definitioner för det lokala nätverket.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Steg 7. Verifiera hälso avsökningen för Azure Gateway

1. Öppna hälso avsökningen genom att bläddra till följande URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Klicka dig igenom certifikat varningen.
3. Om du får ett svar betraktas VPN-gatewayen som felfri. Om du inte får något svar kan det hända att gatewayen inte är felfri eller att en NSG i Gateway-undernätet orsakar problemet. Följande text är ett exempel svar:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Steg 8. Kontrol lera om den lokala VPN-enheten har aktiverat funktionen för PFS (Perfect Forward Secrecy)

Funktionen Perfect Forward Secrecy kan orsaka anslutnings problem. Om VPN-enheten har Perfect Forward Secrecy aktiverat inaktiverar du funktionen. Uppdatera sedan IPsec-principen för VPN gateway.

## <a name="next-steps"></a>Nästa steg

-   [Konfigurera en plats-till-plats-anslutning till ett virtuellt nätverk](./tutorial-site-to-site-portal.md)
-   [Konfigurera en IPsec/IKE-princip för plats-till-plats-VPN-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md)