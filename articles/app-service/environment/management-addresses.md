---
title: Hanteringsadresser
description: Hitta de hanterings adresser som används för att styra en App Service-miljön. Konfigurerade dem i en routningstabell för att undvika problem med asymmetrisk routning.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 03/22/2021
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 25bdfb7a0301af472baa89d3d9e73aacf8cff139
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954623"
---
# <a name="app-service-environment-management-addresses"></a>App Service-miljön hanterings adresser

App Service-miljön (ASE) är en enda klient distribution av Azure App Service som körs i Azure-Virtual Network (VNet).  Medan ASE körs i ditt VNet, måste det fortfarande vara tillgängligt från ett antal dedikerade IP-adresser som används av Azure App Service för att hantera tjänsten.  När det gäller en ASE, passerar hanterings trafiken det användar kontrollerade nätverket. Om den här trafiken är blockerad eller feldirigerad blir ASE inaktive rad. Mer information om ASE nätverks beroenden finns i avsnittet [om nätverks överväganden och App Service-miljön][networking]. För allmän information om ASE kan du börja med [Introduktion till App Service-miljön][intro].

Alla ASE har en offentlig VIP som hanterings trafiken kommer till. Inkommande hanterings trafik från dessa adresser kommer in från till portarna 454 och 455 på den offentliga VIP-adressen för din ASE. I det här dokumentet visas App Service käll adresser för hanterings trafik till ASE. De här adresserna finns också i IP-taggen med namnet AppServiceManagement.

De adresser som anges nedan kan konfigureras i en routningstabell för att undvika problem med asymmetrisk routning med hanterings trafiken. Vägar agerar på trafik på IP-nivå och har ingen medvetenhet om trafik riktning eller som trafiken är en del av ett TCP-svarsmeddelande. Om svars adressen för en TCP-begäran skiljer sig från den adress den skickades till har du ett problem med asymmetrisk routning. För att undvika problem med asymmetrisk routning med ASE hanterings trafik måste du se till att svaren skickas tillbaka från samma adress som de skickades till. Mer information om hur du konfigurerar din ASE så att den fungerar i en miljö där utgående trafik skickas lokalt, finns i [Konfigurera din ASE med Tvingad tunnel trafik][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista över hanterings adresser ##

| Region | Adresser |
|--------|-----------|
| Alla offentliga regioner | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 20.37.74.128, 23.96.195.3, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.107.58.192, 51.107.154.192, 51.116.58.192, 51.116.155.0, 51.120.99.0, 51.120.219.0, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 65.52.250.128, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.50.128, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure Kina | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Konfigurera en nätverks säkerhets grupp

Med nätverks säkerhets grupper behöver du inte bekymra dig om de enskilda adresserna eller upprätthålla din egen konfiguration. Det finns en IP-tjänstetagg med namnet AppServiceManagement som hålls uppdaterad med alla adresser. Om du vill använda den här IP-tjänstetaggen i din NSG går du till portalen, öppnar användar gränssnittet för nätverks säkerhets grupper och väljer inkommande säkerhets regler. Om du har en befintlig regel för inkommande hanterings trafik redigerar du den. Om den här NSG inte har skapats med din ASE, eller om det är nytt, väljer du **Lägg till**. Under List rutan källa väljer du **service tag**.  Under käll tjänst tag gen väljer du **AppServiceManagement**. Ange käll port intervall till \* , mål Port till **alla**, mål port intervall till **454-455**, protokoll till **TCP** och åtgärd att **tillåta**. Om du skapar regeln måste du ange prioriteten. 

![skapa en NSG med tjänst tag gen][1]

## <a name="configuring-a-route-table"></a>Konfigurera en routningstabell

Hanterings adresserna kan placeras i en routningstabell med nästa hopp på Internet för att säkerställa att all inkommande hanterings trafik kan gå tillbaka genom samma sökväg. De här vägarna behövs när du konfigurerar Tvingad tunnel trafik. Du kan skapa routningstabellen med hjälp av portalen, PowerShell eller Azure CLI.  Kommandona för att skapa en routningstabell med hjälp av Azure CLI från en PowerShell-prompt visas nedan. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "20.37.74.128", "23.96.195.3", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.107.58.192", "51.107.154.192", "51.116.58.192", "51.116.155.0", "51.120.99.0", "51.120.219.0", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "65.52.250.128", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.50.128", "191.233.203.64", "191.236.154.88"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

När du har skapat routningstabellen måste du ange den i ASE-undernätet.  

## <a name="get-your-management-addresses-from-api"></a>Hämta dina hanterings adresser från API ##

Du kan visa en lista med de hanterings adresser som matchar din ASE med följande API-anrop.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

API: et returnerar ett JSON-dokument som innehåller alla inkommande adresser för din ASE. I listan med adresser ingår hanterings adresserna, VIP-adressen som används av din ASE och själva ASE under nätets adress intervall.  

Använd följande kommandon för att anropa API: et med [armclient](https://github.com/projectkudu/ARMClient) , men Ersätt i PRENUMERATIONS-ID, resurs grupp och ASE namn.  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
