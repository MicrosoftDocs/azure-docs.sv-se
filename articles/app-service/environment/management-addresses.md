---
title: Hanteringsadresser
description: Hitta de hanteringsadresser som används för att styra App Service-miljön. Konfigurerade dem i en routningstabell för att undvika problem med asymmetrisk routning.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 03/22/2021
ms.author: ccompy
ms.custom: seodec18, references_regions, devx-track-azurecli
ms.openlocfilehash: 796ee38140e72a56f1f22b0594dd904a43ac53c0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865233"
---
# <a name="app-service-environment-management-addresses"></a>App Service-miljön hanteringsadresser

Ase App Service-miljön (Single Tenant Deployment) är en enskild klientdistribution av Azure App Service som körs i ditt Azure Virtual Network (VNet).  Ase körs i ditt virtuella nätverk, men det måste fortfarande vara tillgängligt från ett antal dedikerade IP-adresser som används av Azure App Service för att hantera tjänsten.  När det gäller en ASE passerar hanteringstrafiken det användarkontrollerade nätverket. Om den här trafiken blockeras eller dirigeras felaktigt pausas ASE:en. Mer information om ASE-nätverksberoenden finns i [Nätverksöverväganden och App Service-miljön][networking]. Om du vill ha allmän information om ASE kan du börja [med Introduktion till App Service-miljön][intro].

Alla ASE:er har en offentlig VIP som hanteringstrafik kommer till. Inkommande hanteringstrafik från dessa adresser kommer från till portarna 454 och 455 på din ASE:s offentliga VIP. Det här dokumentet innehåller App Service källadresser för hanteringstrafik till ASE. Dessa adresser finns också i IP-tjänsttaggen med namnet AppServiceManagement.

Adresserna som anges nedan kan konfigureras i en routningstabell för att undvika problem med asymmetrisk routning med hanteringstrafiken. Vägar fungerar på trafik på IP-nivå och har ingen medvetenhet om trafikriktningen eller att trafiken är en del av ett TCP-svarsmeddelande. Om svarsadressen för en TCP-begäran skiljer sig från den adress som den skickades till har du ett problem med asymmetrisk routning. För att undvika problem med asymmetrisk routning med ASE-hanteringstrafiken måste du se till att svar skickas tillbaka från samma adress som de skickades till. Mer information om hur du konfigurerar din ASE för att fungera i en miljö där utgående trafik skickas lokalt finns i Konfigurera ASE med [tvingad tunneltrafik][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lista över hanteringsadresser ##

| Region | Adresser |
|--------|-----------|
| Alla offentliga regioner | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 20.37.74.128, 23.96.195.3, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.107.58.192, 51.107.154.192, 51.116.58.192, 51.116.155.0, 51.120.99.0, 51.120.219.0, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 65.52.250.128, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.50.128, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure Kina | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Konfigurera en nätverkssäkerhetsgrupp

Med nätverkssäkerhetsgrupper behöver du inte bekymra dig om enskilda adresser eller att underhålla din egen konfiguration. Det finns en IP-tjänsttagg med namnet AppServiceManagement som hålls uppdaterad med alla adresser. Om du vill använda den här IP-tjänsttaggen i din NSG går du till portalen, öppnar användargränssnittet för nätverkssäkerhetsgrupper och väljer Inkommande säkerhetsregler. Om du har en befintlig regel för inkommande hanteringstrafik redigerar du den. Om den här NSG:n inte skapades med din ASE, eller om den är helt ny, väljer du Lägg **till**. Under listrutan Källa väljer du **Tjänsttagg**.  Under Källtjänsttagg väljer du **AppServiceManagement**. Ange källportintervallen till , Mål till \* **Alla,** Målportintervall till **454–455,** Protokoll till **TCP** och Åtgärd **för att tillåta**. Om du gör regeln måste du ange Prioritet. 

![skapa en NSG med tjänsttaggen][1]

## <a name="configuring-a-route-table"></a>Konfigurera en vägtabell

Hanteringsadresserna kan placeras i en vägtabell med ett nästa hopp till Internet för att säkerställa att all inkommande hanteringstrafik kan gå tillbaka via samma sökväg. Dessa vägar behövs när du konfigurerar tvingad tunneling. Du kan skapa vägtabellen med hjälp av portalen, PowerShell eller Azure CLI.  Kommandona för att skapa en vägtabell med Azure CLI från en PowerShell-kommandotolk visas nedan. 

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

När du har skapat din vägtabell måste du ange den i ASE-undernätet.  

## <a name="get-your-management-addresses-from-api"></a>Hämta dina hanteringsadresser från API:et ##

Du kan lista hanteringsadresserna som matchar din ASE med följande API-anrop.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

API:et returnerar ett JSON-dokument som innehåller alla inkommande adresser för din ASE. Listan över adresser innehåller hanteringsadresserna, den VIP som används av din ASE och ase-undernätets adressintervall.  

Om du vill anropa API:et [med armclient använder](https://github.com/projectkudu/ARMClient) du följande kommandon men ersätter i ditt prenumerations-ID, resursgrupp och ASE-namn.  

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
