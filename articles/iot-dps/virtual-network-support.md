---
title: Stöd för Azure IoT Device Provisioning service (DPS) för virtuella nätverk
description: Så här använder du anslutnings mönster för virtuella nätverk med Azure IoT Device Provisioning-tjänsten (DPS)
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94959789"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Stöd för Azure IoT Hub Device Provisioning Service (DPS) för virtuella nätverk

Den här artikeln beskriver det virtuella nätverkets (VNET) anslutnings mönster för IoT-enheters etablering med IoT-hubbar med hjälp av DPS. Det här mönstret ger privat anslutning mellan enheterna, DPS och IoT Hub i ett kundägda Azure VNET. 

I de flesta fall där DPS konfigureras med ett VNET, konfigureras IoT Hub också i samma VNET. Mer detaljerad information om VNET-stöd och konfiguration för IoT-hubbar finns i [IoT Hub stöd för virtuella nätverk](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Introduktion

DPS-värdnamn mappas som standard till en offentlig slut punkt med en offentligt dirigerad IP-adress via Internet. Den här offentliga slut punkten är synlig för alla kunder. Åtkomst till den offentliga slut punkten kan utföras av IoT-enheter över WAN-nätverk och lokala nätverk.

Av flera skäl kan kunderna vilja begränsa anslutningarna till Azure-resurser, t. ex. DPS. Dessa orsaker är:

* Förhindra anslutnings exponering via det offentliga Internet. Exponeringen kan minskas genom att införa ytterligare säkerhets nivåer via isolering på nätverks nivå för IoT Hub och DPS-resurser

* Att aktivera en privat anslutning från dina lokala nätverks resurser och se till att dina data och trafik skickas direkt till Azure stamnät nätverket.

* Förhindra exfiltrering attacker från känsliga lokala nätverk. 

* Följande etablerade Azure-wide-anslutnings mönster använder [privata slut punkter](../private-link/private-endpoint-overview.md).

Vanliga metoder för att begränsa anslutningen är [IP-filter för DPS](./iot-dps-ip-filtering.md) och virtuella nätverk (VNet) med [privata slut punkter](../private-link/private-endpoint-overview.md). Målet med den här artikeln är att beskriva VNET-metoden för DPS med hjälp av privata slut punkter. 

Enheter som arbetar i lokala nätverk kan använda [virtuella privata nätverk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privata peering för att ansluta till ett VNet i Azure och få åtkomst till DPS-resurser via privata slut punkter. 

En privat slut punkt är en privat IP-adress som tilldelas i ett kundägda VNET som en Azure-resurs kan komma åt. Genom att ha en privat slut punkt för din DPS-resurs kommer du att kunna tillåta att enheter som körs i ditt VNET för att begära etablering av DPS-resursen utan att tillåta trafik till den offentliga slut punkten.


## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att följande krav uppfylls:

* Din DPS-resurs har redan skapats och länkats till dina IoT-hubbar. Vägledning om hur du konfigurerar en ny DPS-resurs finns i, [konfigurera IoT Hub Device Provisioning service med Azure Portal](./quick-setup-auto-provision.md)

* Du har skapat ett Azure VNET med ett undernät där den privata slut punkten ska skapas. Mer information finns i [skapa ett virtuellt nätverk med Azure CLI](../virtual-network/quick-create-cli.md).

* För enheter som arbetar i lokala nätverk ställer du in [VPN (virtuellt privat nätverk)](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privat peering i ditt Azure VNet.

## <a name="private-endpoint-limitations"></a>Begränsningar för privata slut punkter

Observera följande aktuella begränsningar för DPS när du använder privata slut punkter:

* Privata slut punkter fungerar inte med DPS när DPS-resursen och det länkade navet finns i olika moln. Till exempel [Azure Government och Global Azure](../azure-government/documentation-government-welcome.md).

* För närvarande fungerar inte [anpassade allokeringsregler med Azure Functions](how-to-use-custom-allocation-policies.md) för DPS när Azure-funktionen är låst till ett VNet och privata slut punkter. 

* Nuvarande stöd för DPS-VNET är endast för data insikter i DPS. Utgående data, vilket är trafiken från DPS till IoT Hub, använder en intern tjänst-till-tjänst-mekanism i stället för ett dedikerat VNET. Stöd för fullständig VNET-baserad utgående låsning mellan DPS och IoT Hub är för närvarande inte tillgängligt.

* Den lägsta tilldelnings principen för fördröjning används för att tilldela IoT Hub en enhet till den lägsta svars tiden. Den här allokeringsregeln är inte tillförlitlig i en virtuell nätverks miljö. 

## <a name="set-up-a-private-endpoint"></a>Konfigurera en privat slut punkt

Följ dessa steg om du vill konfigurera en privat slut punkt:

1. Öppna din DPS-resurs i [Azure Portal](https://portal.azure.com/)och klicka på fliken **nätverk** . Klicka på **anslutningar för privata slut punkter** och **+ privat slut punkt**.

    ![Lägg till en ny privat slut punkt för DPS](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. På sidan _skapa en privat slut punkt_ anger du den information som anges i tabellen nedan.

    ![Grunderna om att skapa privata slut punkter](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Fält | Värde |
    | :---- | :-----|
    | **Prenumeration** | Välj önskad Azure-prenumeration som ska innehålla den privata slut punkten.  |
    | **Resursgrupp** | Välj eller skapa en resurs grupp som innehåller den privata slut punkten |
    | **Namn**       | Ange ett namn för din privata slut punkt |
    | **Region**     | Den valda regionen måste vara samma som den region som innehåller VNET, men det behöver inte vara samma som DPS-resursen. |

    Klicka på **Nästa: resurs** för att konfigurera den resurs som den privata slut punkten ska peka på.

3. På sidan _skapa en privat slut punkt resurs_ anger du informationen som anges i tabellen nedan.

    ![Skapa privat slut punkts resurs](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Fält | Värde |
    | :---- | :-----|
    | **Prenumeration**        | Välj den Azure-prenumeration som innehåller den DPS-resurs som din privata slut punkt ska peka på.  |
    | **Resurstyp**       | Välj **Microsoft. Devices/ProvisioningServices**. |
    | **Resurs**            | Välj den DPS-resurs som den privata slut punkten ska mappas till. |
    | **Målunderresurs** | Välj **iotDps**. |

    > [!TIP]
    > Information om inställningen **Anslut till en Azure-resurs per resurs-ID eller alias** finns i avsnittet [begär en privat slut punkt](#request-a-private-endpoint) i den här artikeln.


    Klicka på **Nästa: konfiguration** för att konfigurera VNet för den privata slut punkten.

4. På sidan _skapa en privat slut punkts konfiguration_ väljer du ditt virtuella nätverk och undernät för att skapa den privata slut punkten i.
 
    Klicka på **Nästa: Taggar** och om du vill kan du även ange taggar för resursen.

    ![Konfigurera privat slut punkt](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Klicka på **Granska + skapa** och **skapa** för att skapa din privata slut punkts resurs.


## <a name="request-a-private-endpoint"></a>Begär en privat slut punkt

Du kan begära en privat slut punkt till en DPS-resurs per resurs-ID. För att kunna utföra den här begäran behöver du resursens ägare för att tillhandahålla resurs-ID: t. 

1. Resurs-ID: t anges på fliken Egenskaper för DPS-resursen som visas nedan.

    ![Fliken DPS-egenskaper](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Tänk på att resurs-ID: t innehåller prenumerations-ID: t. 

2. När du har resurs-ID följer du stegen ovan i [Konfigurera en privat slut punkt](#set-up-a-private-endpoint) till steg 3 på resurs sidan _skapa en privat slut punkt_ . Klicka på **Anslut till en Azure-resurs efter resurs-ID eller alias** och ange informationen i följande tabell. 

    | Fält | Värde |
    | :---- | :-----|
    | **Resurs-ID eller alias** | Ange resurs-ID för DPS-resursen. |
    | **Målunderresurs** | Ange **iotDps** |
    | **Begär ande meddelande** | Ange ett begär ande meddelande för DPS-resursens ägare.<br>Exempel: <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Klicka på **Nästa: konfiguration** för att konfigurera VNet för den privata slut punkten.

3. På sidan _skapa en privat slut punkts konfiguration_ väljer du det virtuella nätverket och under nätet för att skapa den privata slut punkten i.
 
    Klicka på **Nästa: Taggar** och om du vill kan du även ange taggar för resursen.

4. Klicka på **Granska + skapa** och **skapa** för att skapa din begäran om privat slut punkt.

5. DPS-ägaren ser den privata slut punkts förfrågan i listan över **anslutningar för privata slut punkter** på fliken DPS-nätverk. På den sidan kan ägaren **godkänna** eller **avvisa** den privata slut punkts förfrågan som visas nedan.

    ![DPS-godkännande](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Prissättning – privata slut punkter

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Nästa steg

Använd länkarna nedan för att lära dig mer om DPS-säkerhetsfunktioner:

* [Säkerhet](./concepts-service.md#attestation-mechanism)
* [Stöd för TLS 1,2](tls-support.md)