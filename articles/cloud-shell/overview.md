---
title: Översikt över Azure Cloud Shell | Microsoft Docs
description: Översikt över Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/20/2020
ms.author: damaerte
ms.openlocfilehash: f824bddf833a1e2c01a3b779abc2c5252d8e0547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89468665"
---
# <a name="overview-of-azure-cloud-shell"></a>Översikt över Azure Cloud Shell

Azure Cloud Shell är ett interaktivt, autentiserat skal för hantering av Azure-resurser via webbläsare. Den ger dig flexibilitet att välja den gränssnittsmiljö som passar bäst för ditt sätt att arbeta, antingen Bash eller PowerShell.

Du kan komma åt Cloud Shell på tre sätt:

- **Direkt länk**: öppna en webbläsare till [https://shell.azure.com](https://shell.azure.com) .

- **Azure Portal**: välj ikonen Cloud Shell på [Azure Portal](https://portal.azure.com):

    ![Ikon för att starta Cloud Shell från Azure Portal](media/overview/portal-launch-icon.png)

- **Kodfragment: på** [docs.Microsoft.com]() och [Microsoft Learn](/learn/)väljer du knappen **testa** som visas med Azure CLI och Azure PowerShell kodfragment:

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    Knappen **prova** öppnar Cloud Shell direkt tillsammans med dokumentationen med hjälp av bash (för Azure CLI-kodfragment) eller PowerShell (för Azure PowerShell kodfragment).

    Om du vill köra kommandot använder du **Kopiera** i kodfragmentet, använder **CTRL** + **Shift** + **v** (Windows/Linux) eller **cmd** + **Shift** + **v** (MacOS) för att klistra in kommandot och trycker sedan på **RETUR**.

## <a name="features"></a>Funktioner

### <a name="browser-based-shell-experience"></a>Webbläsarbaserad skal upplevelse

Cloud Shell ger åtkomst till en webbläsarbaserad kommando rads upplevelse som skapats med Azures hanterings uppgifter i åtanke. Använd Cloud Shell för att arbeta lokalt från en lokal dator på ett sätt som bara molnet kan tillhandahålla.

### <a name="choice-of-preferred-shell-experience"></a>Val av önskad Shell-upplevelse

Användarna kan välja mellan bash eller PowerShell.

1. Välj **Cloud Shell**.

    ![Cloud Shell ikon](media/overview/overview-cloudshell-icon.png)

2. Välj **bash** eller **PowerShell**.

    ![Välj antingen bash eller PowerShell](media/overview/overview-choices.png)

    Efter den första starten kan du använda List rutan Shell-typ för att växla mellan bash och PowerShell:

    ![List Rute kontroll för att välja bash eller PowerShell](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Autentiserad och konfigurerad Azure-arbetsstation

Cloud Shell hanteras av Microsoft så att det levereras med populära kommando rads verktyg och språk stöd. Cloud Shell autentiseras också automatiskt för omedelbar åtkomst till dina resurser via Azure CLI-eller Azure PowerShell-cmdletar.

Visa en fullständig [lista över verktyg som är installerade i Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrerad Cloud Shell redigerare

Cloud Shell erbjuder en integrerad grafisk text redigerare som baseras på redigeraren för öppen källkod i Monaco. Skapa och redigera bara konfigurationsfiler genom att köra `code .` för sömlös distribution via Azure CLI eller Azure PowerShell.

[Läs mer om Cloud Shell redigeraren](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Flera åtkomstpunkter

Cloud Shell är ett flexibelt verktyg som kan användas från:

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI-dokumentation](/cli/azure)
* [Azure PowerShell-dokumentation](/powershell/azure/)
* [Azure-mobilapp](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Tillägg för Visual Studio Code Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Anslut lagringen för Microsoft Azure filer

Cloud Shell datorer är tillfälliga, men dina filer sparas på två sätt: via en disk avbildning och via en monterad fil resurs med namnet `clouddrive` . När du startar Cloud Shell för första gången uppmanas du att skapa en resursgrupp, ett lagringskonto och en Azure Files-resurs. Det här är ett engångs steg och kommer automatiskt att bifogas för alla sessioner. En enda fil resurs kan mappas och kommer att användas av både bash och PowerShell i Cloud Shell.

Läs mer om hur du monterar ett [nytt eller befintligt lagrings konto](persisting-shell-storage.md) eller Lär dig mer om de [beständiga mekanismer som används i Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Azure Storage-brandväggen stöds inte för moln gränssnitts lagrings konton.

## <a name="concepts"></a>Begrepp

* Cloud Shell körs på en tillfällig värd som tillhandahålls per-session per användare
* Cloud Shell timeout efter 20 minuter utan interaktiv aktivitet
* Cloud Shell kräver att en Azure-filresurs monteras
* Cloud Shell använder samma Azure-filresurs för både bash och PowerShell
* Cloud Shell tilldelas en dator per användar konto
* Cloud Shell kvarstår $HOME att använda en 5 GB-avbildning som lagras i fil resursen
* Behörigheter anges som en vanlig Linux-användare i bash

Lär dig mer om funktioner i [bash i Cloud Shell](features.md) och [PowerShell i Cloud Shell](./features.md).

## <a name="pricing"></a>Priser

Datorn som är värd för Cloud Shell är kostnads fri, med en för hands version av en monterad Azure Files-resurs. Vanliga lagrings kostnader gäller.

## <a name="next-steps"></a>Nästa steg

[Bash i Cloud Shell snabb start](quickstart.md) <br>
[PowerShell i Cloud Shell snabb start](quickstart-powershell.md)