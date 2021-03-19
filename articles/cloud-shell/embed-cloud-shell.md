---
title: Bädda in Azure Cloud Shell | Microsoft Docs
description: Lär dig att bädda in Azure Cloud Shell.
services: cloud-shell
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
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "60614314"
---
# <a name="embed-azure-cloud-shell"></a>Bädda in Azure Cloud Shell

Genom att bädda in Cloud Shell kan utvecklare och innehålls skrivare direkt öppna Cloud Shell från en dedikerad URL, [Shell.Azure.com](https://shell.azure.com). Detta ger omedelbart full kraft för Cloud Shellens autentisering, verktyg och uppdaterade Azure CLI/Azure PowerShell-verktyg för dina användare.

Knappen normal storlek

[![Vanlig start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

Knappen stor storlek

[![Stor start](https://shell.azure.com/images/launchcloudshell@2x.png "Starta Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Så här gör du

Integrera Cloud Shells Start knapp i markdown-filer genom att kopiera följande:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

HTML-filen för att bädda in ett popup-Cloud Shell är nedan:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Anpassa upplevelse

Ange en viss Shell-upplevelse genom att utöka din URL.

|Erfarenhet   |URL   |
|---|---|
|Det senast använda gränssnittet   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell snabb start](quickstart.md)<br>
[PowerShell i Cloud Shell snabb start](quickstart-powershell.md)
