---
title: IP-anslutningsfilter för Azure IoT DPS | Microsoft Docs
description: Lär dig hur du använder IP-filtrering för att blockera anslutningar från specifika IP-adresser till din Azure IoT DPS-instans. Du kan blockera anslutningar från enskilda IP-adresser eller IP-adressintervall.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 4067d157a13618b008e2cb1a5fee31259bfd43a9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094360"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Använda IP-anslutningsfilter för Azure IoT DPS

Säkerheten är en viktig aspekt i alla IoT-lösningar. Ibland måste du uttryckligen ange de IP-adresser som enheter kan ansluta till som en del av din säkerhetskonfiguration. Med *IP-filterfunktionen* för Azure IoT Hub Device Provisioning Service (DPS) kan du konfigurera regler för att avvisa eller acceptera trafik från specifika IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika scenarier då det kan vara bra att blockera anslutningar till en DPS-slutpunkt från särskilda IP-adresser:

* DPS bör endast ta emot trafik från ett visst intervall med IP-adresser och avvisa allt annat. Till exempel använder du DPS med [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) för att skapa privata anslutningar mellan en DPS och dina enheter.

* Du måste avvisa trafik från IP-adresser som har identifierats som misstänkta av DPS-administratören.

## <a name="how-filter-rules-are-applied"></a>Så här tillämpas filterregler

IP-filterregler tillämpas på DPS-instansnivån. Det betyder att IP-filterreglerna tillämpas på alla anslutningar från enheter och backend-appar som använder ett protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en IP-regel som avvisar trafik på din DPS-instans får en 401-statuskod och tillhörande beskrivning (behörighet saknas). IP-regeln nämns inte i svarsmeddelandet.

## <a name="default-setting"></a>Standardinställning

Som standard är **IP-filtertabellen** på portalen för DPS tom. Med den här standardinställningen accepterar DPS anslutningar från alla IP-adresser. Standardvärdet motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0.

![Förvalda IP-filterinställningar för IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägga till eller redigera en IP-filterregel

Du lägger till en IP-filterregel genom att välja **+ Lägg till IP-filterregel**.

![Lägga till en IP-filterregel till en IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Fyll i fälten när du har valt **Lägg till IP-filterregel**.

![När du har valt Lägg till IP-filterregel](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Ange ett **namn** för IP-filterregeln. Namnet måste vara en unik, skiftlägeskänslig, alfanumerisk sträng på upp till 128 tecken. Du kan endast använda 7-bitars alfanumeriska ASCII-tecken samt `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Ange en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation representerar exempelvis 192.168.100.0/22 de 1 024 IPv4-adresserna från 192.168.100.0 till 192.168.103.255.

* Välj **Tillåt** eller **Blockera** som **åtgärd** för IP-filterregeln.

När du har fyllt i fälten sparar du regeln genom att välja **Spara**. Ett meddelande visas som anger att uppdateringen pågår.

![Meddelande när en IP-filterregel sparas](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Alternativet **Lägg till** inaktiveras när du når den högsta gränsen på tio IP-filterregler.

Om du vill redigera en befintlig regel väljer du de data som du vill ändra, gör ändringen och sparar sedan ändringen genom att välja **Spara**.

> [!NOTE]
> Om du avvisar IP-adresser kan detta förhindra att andra Azure-tjänster interagerar med DPS-instansen.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en IP-filterregel

Om du vill ta bort en IP-filterregel väljer du papperskorgsikonen på den aktuella raden och väljer sedan **Spara**. Regeln tas bort och ändringen sparas.

![Ta bort en IP-filterregel för IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Uppdatera IP-filterregler i kod

Du kan hämta och ändra ett IP-filter för DPS med hjälp av Azure-resursproviderns REST-slutpunkt. Mer information finns i `properties.ipFilterRules` i [createorupdate-metoden](/rest/api/iot-dps/iotdpsresource/createorupdate).

Det går för närvarande inte att uppdatera IP-filterregler för DPS med Azure CLI eller Azure PowerShell. Det kan däremot göras med Azure Resource Manager-mallar. Hjälp med att använda Resource Manager-mallar finns i avsnittet om [Azure Resource Manager-mallar](../azure-resource-manager/templates/overview.md). Mallexemplen nedan visar hur du skapar, redigerar och tar bort IP-filterregler för DPS.

### <a name="add-an-ip-filter-rule"></a>Lägga till en IP-filterregel

I följande mallexempel skapas en ny IP-filterregel med namnet ”AllowAll” som accepterar all trafik.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Uppdatera mallens attribut för IP-filterregler baserat på dina krav.

| Attribut                | Beskrivning |
| ------------------------ | ----------- |
| **FilterName**           | Ange ett namn för IP-filterregeln. Namnet måste vara en unik, skiftlägeskänslig, alfanumerisk sträng på upp till 128 tecken. Endast ASCII 7-bitars alfanumeriska tecken plus  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`   godkänns. |
| **Åtgärd**               | Värden som stöds är **Acceptera** eller  **Avvisa** som åtgärden för IP-filterregeln. |
| **ipMask**               | Ange en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation representerar exempelvis 192.168.100.0/22 de 1 024 IPv4-adresserna från 192.168.100.0 till 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Uppdatera en IP-filterregel

I följande mallexempel uppdateras IP-filterregeln med namnet ”AllowAll”, som du såg tidigare, så att all trafik avvisas.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Ta bort en IP-filterregel

I följande mallexempel tas alla IP-filterregler bort för DPS-instansen.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Utvärdering av IP-filterregler

IP-filterregler tillämpas i ordning och den första regel som matchar IP-adressen avgör huruvida trafiken accepteras eller avvisas.

Om du till exempel vill acceptera adresser i intervallet 192.168.100.0/22 och avvisa allt annat, bör den första regeln i tabellen acceptera adressintervallet 192.168.100.0/22. Nästa regel bör avvisa alla adresser genom att använda intervallet 0.0.0.0/0.

Om du vill ändra ordning på dina IP-filterregler i tabellen klickar du på de tre lodräta punkterna i början av en rad och drar och släpper.

Sedan sparar du IP-filterreglernas nya ordning genom att klicka på **Spara**.

![Ändra ordning på IP-filterregler för DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar DPS finns i:

* [Förstå IP-adresser för IoT DPS](iot-dps-understand-ip-address.md)
* [Konfigurera DPS med hjälp av Azure CLI](how-to-manage-dps-with-cli.md)
* [Kontrollera åtkomsten till DPS](how-to-control-access.md)
