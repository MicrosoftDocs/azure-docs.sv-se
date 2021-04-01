---
title: Azure IoT Hub klassiska IP-filter (inaktuellt) | Microsoft Docs
description: Så här uppgraderar du från klassiskt IP-filter och vilka är fördelarna
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101661162"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT Hub klassiskt IP-filter och hur du uppgraderar 

Det uppgraderade IP-filtret för IoT Hub skyddar den inbyggda slut punkten och är säker som standard. Vi strävar aldrig efter att göra några ändringar, men den förbättrade säkerhets modellen för det uppgraderade IP-filtret är inte kompatibel med det klassiska IP-filtret, så vi meddelar sin pension. Läs mer om det nya uppgraderade IP-filtret i [Nyheter](#whats-new) och [IoT Hub IP-filter](iot-hub-ip-filtering.md).

För att undvika avbrott i tjänsten måste du utföra den guidade uppgraderingen innan tids gränsen för migreringen sker, då utförs uppgraderingen automatiskt. Mer information om tids linjen för migrering finns i [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="how-to-upgrade"></a>Så här uppgraderar du

1.  Besök Azure Portal
2.  Gå till IoT-hubben.
3.  Välj **nätverk** på menyn på den vänstra sidan.
4.  Du bör se en banderoll som meddelar dig att du ska uppgradera IP-filtret till den nya modellen. Fortsätt genom att välja **Ja**.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="Bild som visar banderoll-prompten för att uppgradera från det klassiska IP-filtret":::
5.  Eftersom det nya IP-filtret blockerar all IP som standard, tar uppgraderingen bort de enskilda reglerna för att neka, men ger dig möjlighet att granska dem innan du sparar. Granska reglerna noggrant för att se till att de fungerar.
6.  Slutför uppgraderingen genom att följa anvisningarna.

## <a name="whats-new"></a>Nyheter

### <a name="secure-by-default"></a>Säker som standard

Det klassiska IP-filtret tillåter implicit att alla IP-adresser ansluter till IoT Hub som standard, vilket inte passar bra med de vanligaste nätverks säkerhets scenarierna. Vanligt vis vill du bara att betrodda IP-adresser ska kunna ansluta till din IoT-hubb och förkasta allt annat. För att uppnå det här målet med klassiskt IP-filter är det en process med flera steg. Om du till exempel bara vill acceptera trafik från `192.168.100.0/22` måste du

1. Konfigurera en enskild regel för *att tillåta* för `192.168.100.0/22` .
1. Konfigurera en annan *block* regel för `0.0.0.0/0` (regeln "blockera alla")
1. Kontrol lera att reglerna har sorterats korrekt, med regeln för att tillåta att regeln beställs ovanför block regeln.

I praktiken orsakar den här multi-steg-processen förvirring. Användare konfigurerade inte regeln "blockera alla" eller beställer inte reglerna korrekt, vilket resulterar i oavsiktlig exponering. 

Det nya IP-filtret blockerar alla IP-adresser som standard. Endast de IP-intervall som du uttryckligen lägger till får ansluta till IoT Hub. I exemplet ovan behövs inte steg 2 och 3 längre. Det här nya beteendet fören klar konfigurationen och följer [principen säker enligt standard](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>Skydda den inbyggda Event Hub-kompatibla slut punkten

Det går inte att använda det klassiska IP-filtret på den inbyggda slut punkten. Den här begränsningen innebär att om en händelse med en blockera alla regel (block `0.0.0.0/0` ) har kon figurer ATS är den inbyggda slut punkten fortfarande tillgänglig från alla IP-adresser.

Det nya IP-filtret tillhandahåller ett alternativ för att tillämpa regler på den inbyggda slut punkten, vilket minskar risken för nätverks säkerhetshot.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Bild som visar växlingen som ska användas för den inbyggda slut punkten eller inte":::

> [!NOTE]
> Det här alternativet är inte tillgängligt för kostnads fria (F1) IoT-hubbar. Använd en betald IoT-hubb om du vill använda IP-filterlistor på den inbyggda slut punkten.

### <a name="api-impact"></a>API-påverkan

Det uppgraderade IP-filtret är tillgängligt i IoT Hub Resource API från `2020-08-31` (samt `2020-08-31-preview` ) och därefter. Klassiskt IP-filter är fortfarande tillgängligt i alla API-versioner, men kommer att tas bort i en framtida API-version nära migreringens tids gräns. Mer information om tids linjen för migrering finns i [Azure Update](https://aka.ms/ipfilterv2azupdate).

## <a name="tip-try-the-changes-before-they-apply"></a>Tips: prova ändringarna innan de tillämpas

Eftersom det nya IP-filtret blockerar alla IP-adresser som standard är de enskilda block reglerna inte längre kompatibla. Den guidade uppgraderings processen tar därmed bort de enskilda spärr reglerna. 

Så här försöker du ändra i med klassiskt IP-filter:

1. Gå till fliken **nätverk** i IoT Hub
1. Anteckna den befintliga konfigurationen för IP-filter (klassisk) om du vill återställa
1. Bredvid regler med **block** väljer du pappers korgs ikonen för att ta bort dem
1. Lägg till en annan regel längst ned med `0.0.0.0/0` och välj **blockera**
1. Välj **Spara**

Den här konfigurationen efterliknar hur det nya IP-filtret beter sig efter att ha uppgraderat från klassisk. Ett undantag är det inbyggda slut punkts skyddet, vilket inte är möjligt att försöka använda klassiskt IP-filter. Den funktionen är dock valfri, så du behöver inte använda den om du tror att den kan bryta något.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Tips: kontrol lera diagnostikloggar för alla IP-anslutningar till din IoT Hub

Kontrol lera dina diagnostikloggar under kategorin anslutningar för att säkerställa en smidig över gång. Leta efter `maskedIpAddress` egenskapen för att se om intervallet är som du förväntar dig. Kom ihåg: det nya IP-filtret blockerar alla IP-adresser som inte har lagts till explicit.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>IoT Hub klassisk IP-filter dokumentation (dras tillbaka)

> [!IMPORTANT]
> Nedan visas den ursprungliga dokumentationen för klassiskt IP-filter, som dras tillbaka.

Säkerhet är en viktig aspekt av alla IoT-lösningar baserade på Azure IoT Hub. Ibland måste du uttryckligen ange de IP-adresser som enheter kan ansluta till som en del av din säkerhetskonfiguration. Med funktionen *IP-filter* kan du konfigurera regler för att avvisa eller acceptera trafik från vissa IPv4-adresser.

### <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användnings fall när det är användbart att blockera IoT Hub slut punkter för vissa IP-adresser:

* Din IoT Hub bör endast ta emot trafik från ett visst intervall med IP-adresser och förkasta allt annat. Du använder till exempel din IoT Hub med [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) för att skapa privata anslutningar mellan en IoT-hubb och din lokala infrastruktur.

* Du måste avvisa trafik från IP-adresser som har identifierats som misstänkt av IoT Hub-administratören.

### <a name="how-filter-rules-are-applied"></a>Så här tillämpas filterregler

IP-filter regler tillämpas på IoT Hub service nivå. Därför gäller IP-filter reglerna för alla anslutningar från enheter och backend-appar med valfritt protokoll som stöds. Klienter som läser direkt från den [inbyggda Event Hub-kompatibla slut punkten](iot-hub-devguide-messages-read-builtin.md) (inte via IoT Hub anslutnings strängen) är dock inte kopplade till IP-filter reglerna. 

Alla anslutnings försök från en IP-adress som matchar en IP-regel som avvisar i din IoT-hubb får en otillåten 401 status kod och beskrivning. IP-regeln nämns inte i svarsmeddelandet. Att avvisa IP-adresser kan förhindra andra Azure-tjänster, till exempel Azure Stream Analytics, Azure-Virtual Machines eller Device Explorer i Azure Portal att interagera med IoT Hub.

> [!NOTE]
> Om du måste använda Azure Stream Analytics (ASA) för att läsa meddelanden från en IoT Hub med aktiverat IP-filter använder du det Event Hub-kompatibla namnet och slut punkten för din IoT Hub för att manuellt lägga till en [Event Hubs Stream-indata](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) i ASA.

### <a name="default-setting"></a>Standardinställning

Som standard är **IP-filter** rutnätet i portalen för en IoT Hub tomt. Den här standardinställningen innebär att navet accepterar anslutningar från alla IP-adresser. Standardvärdet motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0.

Gå till sidan Inställningar för IP-filter genom att välja **nätverk**, **offentlig åtkomst** och sedan välja **valda IP-intervall**:

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="IoT Hub inställningar för standard-IP-filter":::

### <a name="add-or-edit-an-ip-filter-rule"></a>Lägga till eller redigera en IP-filterregel

Du lägger till en IP-filterregel genom att välja **+ Lägg till IP-filterregel**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="Lägga till en IP-filterlista till en IoT-hubb":::

Fyll i fälten när du har valt **Lägg till IP-filterregel**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="När du har valt Lägg till IP-filterregel":::

* Ange ett **namn** för IP-filterregeln. Namnet måste vara en unik, skiftlägeskänslig, alfanumerisk sträng på upp till 128 tecken. Du kan endast använda 7-bitars alfanumeriska ASCII-tecken samt `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Ange en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation representerar exempelvis 192.168.100.0/22 de 1 024 IPv4-adresserna från 192.168.100.0 till 192.168.103.255.

* Välj **Tillåt** eller **Blockera** som **åtgärd** för IP-filterregeln.

När du har fyllt i fälten sparar du regeln genom att välja **Spara**. Ett meddelande visas som anger att uppdateringen pågår.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="Meddelande när en IP-filterregel sparas":::

Alternativet **Lägg till** inaktiveras när du når den högsta gränsen på tio IP-filterregler.

Om du vill redigera en befintlig regel väljer du de data som du vill ändra, gör ändringen och sparar sedan ändringen genom att välja **Spara**.

### <a name="delete-an-ip-filter-rule"></a>Ta bort en IP-filterregel

Om du vill ta bort en IP-filterregel väljer du papperskorgsikonen på den aktuella raden och väljer sedan **Spara**. Regeln tas bort och ändringen sparas.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="Ta bort en IoT Hub IP-filterlista":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Hämta och uppdatera IP-filter med Azure CLI

Din IoT Hubs IP-filter kan hämtas och uppdateras via [Azure CLI](/cli/azure/).

Om du vill hämta aktuella IP-filter för din IoT Hub kör du:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Detta kommer att returnera ett JSON-objekt där dina befintliga IP-filter visas under `properties.ipFilterRules` nyckeln:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Om du vill lägga till ett nytt IP-filter för din IoT Hub kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Om du vill ta bort ett befintligt IP-filter i IoT Hub kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Observera att `<ipFilterIndexToRemove>` måste motsvara ordningen av IP-filter i din IoT Hubs `properties.ipFilterRules` .

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Hämta och uppdatera IP-filter med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Din IoT Hubs IP-filter kan hämtas och anges via [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>Uppdatera IP filter regler med REST

Du kan också hämta och ändra din IoT Hubs IP-filter med hjälp av Azure Resource providers REST-slutpunkt. Mer information finns i `properties.ipFilterRules` i [createorupdate-metoden](/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>Utvärdering av IP-filterregler

IP-filterregler tillämpas i ordning och den första regel som matchar IP-adressen avgör huruvida trafiken accepteras eller avvisas.

Om du till exempel vill acceptera adresser i intervallet 192.168.100.0/22 och avvisa allt annat, bör den första regeln i tabellen acceptera adressintervallet 192.168.100.0/22. Nästa regel bör avvisa alla adresser genom att använda intervallet 0.0.0.0/0.

Om du vill ändra ordning på dina IP-filterregler i tabellen klickar du på de tre lodräta punkterna i början av en rad och drar och släpper.

Sedan sparar du IP-filterreglernas nya ordning genom att klicka på **Spara**.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="Ändra ordning på IoT Hub IP-filter regler":::

## <a name="next-steps"></a>Nästa steg

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Använda IP-filter](iot-hub-ip-filtering.md)