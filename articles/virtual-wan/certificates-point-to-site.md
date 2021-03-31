---
title: Skapa och exportera certifikat för användares VPN-anslutningar | Azure Virtual WAN
description: Skapa ett självsignerat rot certifikat, exportera den offentliga nyckeln och generera klient certifikat för användares VPN-anslutningar med hjälp av PowerShell på Windows 10 eller Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328046"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Skapa och exportera certifikat för användares VPN-anslutningar

Användares VPN-anslutningar (punkt-till-plats) använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rot certifikat och genererar klient certifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016.

Du måste utföra stegen i den här artikeln på en dator som kör Windows 10 eller Windows Server 2016. PowerShell-cmdletar som du använder för att generera certifikat är en del av operativ systemet och fungerar inte i andra versioner av Windows. Datorn med Windows 10 eller Windows Server 2016 krävs bara för att skapa certifikaten. När certifikaten har skapats kan du ladda upp dem eller installera dem på alla klient operativ system som stöds.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt med de [virtuella WAN-stegen för användares VPN-anslutning](virtual-wan-about.md)
