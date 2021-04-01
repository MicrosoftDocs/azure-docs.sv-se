---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678500"
---
Innan du börjar konfigurera installerar du och importerar de nödvändiga modulerna. Du måste ha administratörs behörighet för att installera moduler i PowerShell.

1. Installera och importera AZ-modulen.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installera och importera modulen AZ. peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Kontrol lera att modulerna har importer ATS korrekt med hjälp av det här kommandot:
    ```powershell
    Get-Module
    ```
1. Logga in på ditt Azure-konto med hjälp av det här kommandot:
    ```powershell
    Connect-AzAccount
    ```
1. Kontrol lera prenumerationerna för kontot och välj den prenumeration där du vill skapa en peering.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Om du inte redan har en resurs grupp måste du skapa en innan du skapar en peering. Du kan göra detta genom att köra följande kommando:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Om du inte redan har kopplat ditt ASN och din prenumeration följer du stegen i [associera peer-ASN](../howto-subscription-association-powershell.md). Den här åtgärden krävs för att begära en peering.

> [!NOTE]
> Platsen för en resurs grupp är oberoende av den plats där du väljer att konfigurera en peering.
&nbsp;
