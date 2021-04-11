---
title: Felsöka tjänst anslutning Windows Virtual Desktop (klassisk) – Azure
description: Så här löser du problem när du konfigurerar klient anslutningar i en Windows Virtual Desktop (klassisk) klient miljö.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: afd5ba23838f2771825511c4aa67b39934e757f6
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444198"
---
# <a name="windows-virtual-desktop-classic-service-connections"></a>Windows Virtual Desktop (klassiska) tjänst anslutningar

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../troubleshoot-service-connection.md).

Använd den här artikeln för att lösa problem med klient anslutningar för virtuella Windows-datorer.

## <a name="provide-feedback"></a>Ge feedback

Du kan ge oss feedback och diskutera Windows Virtual Desktop-tjänsten med produkt teamet och andra aktiva community-medlemmar i [Tech-communityn för Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Användaren ansluter men inget visas (ingen feed)

En användare kan starta fjärr skrivbords klienter och kan autentisera, men användaren ser inga ikoner i webb identifierings flödet.

Bekräfta att användaren som rapporterar problemen har tilldelats program grupper genom att använda den här kommando raden:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Bekräfta att användaren loggar in med rätt autentiseringsuppgifter.

Om webb klienten används kontrollerar du att det inte finns några problem med cachelagrade autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview-2019.md).
- Information om hur du felsöker problem när du skapar en klient och en adresspool i en Windows Virtual Desktop-miljö finns i [skapa innehavare och skapa värdar för pooler](troubleshoot-set-up-issues-2019.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration-2019.md).
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
