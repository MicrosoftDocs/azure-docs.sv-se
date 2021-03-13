---
title: IP-filter för Azure IoT Hub | Microsoft Docs
description: Hur du använder IP-filtrering för att tillåta anslutningar från vissa IP-adresser till Azure IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: jlian
ms.openlocfilehash: 62292a2ee51f8e1838e9cf3376367a02964cad47
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418750"
---
# <a name="use-ip-filters"></a>Använda IP-filter

Säkerhet är en viktig aspekt av alla IoT-lösningar baserade på Azure IoT Hub. Ibland måste du uttryckligen ange de IP-adresser som enheter kan ansluta till som en del av din säkerhetskonfiguration. Med funktionen *IP-filter* kan du konfigurera regler för att avvisa eller acceptera trafik från vissa IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Använd IP-filter för att ta emot trafik enbart från ett angivet intervall av IP-adresser och ignorera allt annat. Du använder till exempel din IoT Hub med [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) för att skapa privata anslutningar mellan en IoT-hubb och din lokala infrastruktur.

## <a name="default-setting"></a>Standardinställning

Gå till sidan Inställningar för IP-filter genom att välja **nätverk**, **offentlig åtkomst** och sedan välja **valda IP-intervall**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub inställningar för standard-IP-filter":::

Som standard är **IP-filter** rutnätet i portalen för en IoT Hub tomt. Denna standard inställning innebär att navet blockerar anslutningar från alla IP-adresser. Standardvärdet motsvarar en regel som blockerar `0.0.0.0/0` IP-adressintervallet.

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägga till eller redigera en IP-filterregel

Du lägger till en IP-filterregel genom att välja **+ Lägg till IP-filterregel**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Lägga till en IP-filterlista till en IoT-hubb":::

Fyll i fälten när du har valt **Lägg till IP-filterregel**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="När du har valt Lägg till IP-filterregel":::

* Ange ett **namn** för IP-filterregeln. Namnet måste vara en unik, SKIFT läges okänslig, alfanumerisk sträng på upp till 128 tecken. Du kan endast använda 7-bitars alfanumeriska ASCII-tecken samt `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Ange en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation representerar exempelvis 192.168.100.0/22 de 1 024 IPv4-adresserna från 192.168.100.0 till 192.168.103.255.

När du har fyllt i fälten sparar du regeln genom att välja **Spara**. Ett meddelande visas som anger att uppdateringen pågår.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Meddelande när en IP-filterregel sparas":::

Alternativet **Lägg till** inaktiveras när du når den högsta gränsen på tio IP-filterregler.

Om du vill redigera en befintlig regel väljer du de data som du vill ändra, gör ändringen och sparar sedan ändringen genom att välja **Spara**.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en IP-filterregel

Om du vill ta bort en IP-filterregel väljer du papperskorgsikonen på den aktuella raden och väljer sedan **Spara**. Regeln tas bort och ändringen sparas.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Ta bort en IoT Hub IP-filterlista":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Använd IP-filterlistorna för den inbyggda Event Hub-kompatibla slut punkten

Om du vill tillämpa IP-filterlistan på den inbyggda Event Hub-kompatibla slut punkten markerar du kryss rutan bredvid **Använd IP-filter till den inbyggda slut punkten?** och väljer sedan **Spara**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Bild som visar växlingen för den inbyggda slut punkten och spara":::

> [!NOTE]
> Det här alternativet är inte tillgängligt för kostnads fria (F1) IoT-hubbar. Använd en betald IoT-hubb om du vill använda IP-filterlistor på den inbyggda slut punkten.

Genom att aktivera det här alternativet replikeras IP-filterlistorna till den inbyggda slut punkten, så att endast betrodda IP-adressintervall har åtkomst till den.

Om du inaktiverar det här alternativet är den inbyggda slut punkten tillgänglig för alla IP-adresser. Det här beteendet kan vara användbart om du vill läsa från slut punkten med tjänster med ändring av IP-adresser som Azure Stream Analytics. 

## <a name="how-filter-rules-are-applied"></a>Så här tillämpas filterregler

IP-filter regler tillämpas på IoT Hub service nivå. Därför gäller IP-filter reglerna för alla anslutningar från enheter och backend-appar med valfritt protokoll som stöds. Du kan också välja om den [inbyggda slut punkten för händelsehubben](iot-hub-devguide-messages-read-builtin.md) (inte via IoT Hub anslutnings sträng) är kopplad till dessa regler. 

Alla anslutnings försök från en IP-adress som inte uttryckligen tillåts får en otillåten 401 status kod och beskrivning. IP-regeln nämns inte i svarsmeddelandet. Att avvisa IP-adresser kan förhindra andra Azure-tjänster, till exempel Azure Stream Analytics, Azure-Virtual Machines eller Device Explorer i Azure Portal att interagera med IoT Hub.

> [!NOTE]
> Om du måste använda Azure Stream Analytics (ASA) för att läsa meddelanden från en IoT Hub med aktiverat IP-filter **inaktiverar** du alternativet **Använd IP-filter till det inbyggda slut punkts** alternativet och använder sedan det Event Hub-kompatibla namnet och slut punkten för din IoT Hub för att manuellt lägga till en [Event Hubs Stream-indata](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) i ASA.

### <a name="ordering"></a>Ordna profiler

IP-filter regler är *tillåtna* regler och tillämpas utan sortering. Endast IP-adresser som du lägger till får ansluta till IoT Hub. 

Om du till exempel vill acceptera adresser i intervallet `192.168.100.0/22` och förkasta allt annat behöver du bara lägga till en regel i rutnätet med adress intervall `192.168.100.0/22` .

### <a name="azure-portal"></a>Azure Portal 

IP filter regler tillämpas också när du använder IoT Hub via Azure Portal. Detta beror på att API-anrop till tjänsten IoT Hub görs direkt med din webbläsare med dina autentiseringsuppgifter, vilket är konsekvent med andra Azure-tjänster. Om du vill komma åt IoT Hub att använda Azure Portal när IP-filtret är aktiverat lägger du till datorns IP-adress i listan över tillåtna. 

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Hämta och uppdatera IP-filter med Azure CLI

Din IoT Hubs IP-filter kan hämtas och uppdateras via [Azure CLI](/cli/azure/).

Om du vill hämta aktuella IP-filter för din IoT Hub kör du:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Detta kommer att returnera ett JSON-objekt där dina befintliga IP-filter visas under `properties.networkRuleSets` nyckeln:

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Om du vill lägga till ett nytt IP-filter för din IoT Hub kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Om du vill ta bort ett befintligt IP-filter i IoT Hub kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Här, `<ipFilterIndexToRemove>` måste motsvara ordningen av IP-filter i din IoT Hubs `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Hämta och uppdatera IP-filter med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Din IoT Hubs IP-filter kan hämtas och anges via [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Uppdatera IP filter regler med REST


Du kan också hämta och ändra din IoT Hubs IP-filter med hjälp av Azure Resource providers REST-slutpunkt. Mer information finns i `properties.networkRuleSets` i [createorupdate-metoden](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>IP-filter (klassisk) pensionering

Det klassiska IP-filtret har dragits tillbaka. Läs mer i [IoT Hub klassiska IP-filter och hur du uppgraderar](iot-hub-ip-filter-classic.md).

## <a name="next-steps"></a>Nästa steg

För att ytterligare utforska funktionerna i IoT Hub, se:

* [IoT Hub mått](./monitor-iot-hub.md)
* [IoT Hub stöd för virtuella nätverk med privat länk och hanterad identitet](virtual-network-support.md)
* [Hantera offentlig nätverks åtkomst för IoT Hub](iot-hub-public-network-access.md)
* [Övervaka IoT Hub](monitor-iot-hub.md)
