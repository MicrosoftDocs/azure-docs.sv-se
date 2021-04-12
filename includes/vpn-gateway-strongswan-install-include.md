---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 089ad704a466590a9649107fef245a88d6a4d6e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244456"
---
Följande konfiguration användes för stegen nedan:

- Dator: Ubuntu Server 18,04
- Beroenden: strongSwan


Använd följande kommandon för att installera den nödvändiga strongSwan-konfigurationen:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Använd följande kommando för att installera Azures kommando rads gränssnitt:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Ytterligare anvisningar om hur du installerar Azure CLI](/cli/azure/install-azure-cli-apt)