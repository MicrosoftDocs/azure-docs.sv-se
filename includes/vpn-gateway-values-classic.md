---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103245"
---
När du skapar en klassisk virtuella nätverk i Azure Portal, är det namn som du visar inte det fullständiga namnet som du använder för PowerShell. Ett VNet som verkar ha namnet **TestVNet1** i portalen kan till exempel ha ett mycket längre namn i nätverks konfigurations filen. För ett VNet i resurs gruppen "ClassicRG" kan det se ut ungefär så här: **Group ClassicRG TestVNet1**. När du skapar dina anslutningar är det viktigt att använda de värden som visas i nätverks konfigurations filen.

I följande steg ska du ansluta till ditt Azure-konto och hämta och Visa nätverks konfigurations filen för att hämta de värden som krävs för dina anslutningar.

1. Hämta och installera den senaste versionen av PowerShell-cmdletarna för Azure Service Management (SM). De flesta har Resource Manager-modulerna installerade lokalt, men har inte några Service Management-moduler. Service Management-moduler är äldre och måste installeras separat. Mer information finns i [Installera Service Management-cmdletar](/powershell/azure/servicemanagement/install-azure-ps).

1. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta. Du måste köra dessa kommandon lokalt med PowerShell Service Management-modulen. Anslut till ditt konto. Använd följande exempel för att ansluta:

   ```powershell
   Add-AzureAccount
   ```
1. Kontrollera prenumerationerna för kontot.

   ```powershell
   Get-AzureSubscription
   ```
1. Om du har mer än en prenumeration väljer du den du vill använda.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Skapa en katalog på datorn. Till exempel C:\AzureVNet
1. Exportera nätverks konfigurations filen till katalogen. I det här exemplet exporteras nätverks konfigurations filen till **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Öppna filen med en text redigerare och visa namnen på dina virtuella nätverk och platser. Namnen är de namn som du använder när du skapar dina anslutningar.<br>**VNet** -namn visas som **VirtualNetworkSite namn =**<br>**Plats** namn anges som **LocalNetworkSiteRef namn =**