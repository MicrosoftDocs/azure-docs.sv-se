---
title: Anpassa RDP-egenskaper med PowerShell Windows Virtual Desktop (klassisk) – Azure
description: Anpassa RDP-egenskaperna för Windows Virtual Desktop (klassisk) med PowerShell-cmdletar.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5110e97e52939ea2115bb839768cc7ab96802961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020716"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Anpassa Remote Desktop Protocol egenskaper för en Windows Virtual Desktop (klassisk)-värd pool

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../customize-rdp-properties.md).

Genom att anpassa en värd Pools egenskaper för Remote Desktop Protocol (RDP), till exempel flera skärmar och omdirigering av ljud, kan du leverera en optimal upplevelse för dina användare utifrån deras behov. Du kan anpassa RDP-egenskaperna i Windows Virtual Desktop med parametern **-CustomRdpProperty** i cmdleten **set-RdsHostPool** .

Se [Inställningar för RDP-filer som stöds](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) för en fullständig lista över vilka egenskaper som stöds och deras standardvärden.

Börja med att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Standard egenskaper för RDP

Som standard innehåller publicerade RDP-filer följande egenskaper:

|RDP-egenskaper | Stationära datorer | RemoteApp |
|---|---| --- |
| Läge för flera övervakare | Enabled | Ej tillämpligt |
| Aktiverade enhets omdirigeringar | Enheter, Urklipp, skrivare, COM-portar, USB-enheter och smartkort| Enheter, urklipp och skrivare |
| Fjärr ljud läge | Spela lokalt | Spela lokalt |

De anpassade egenskaper som du definierar för poolen åsidosätter dessa standardvärden.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Lägga till eller redigera en enskild anpassad RDP-egenskap

Om du vill lägga till eller redigera en enskild anpassad RDP-egenskap kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat för att redigera en anpassad R D P-egenskap.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Lägga till eller redigera flera anpassade RDP-egenskaper

Om du vill lägga till eller redigera flera anpassade RDP-egenskaper kör du följande PowerShell-cmdletar genom att ange de anpassade RDP-egenskaperna som en semikolonavgränsad sträng:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![En skärm bild av PowerShell-cmdleten Set-RDSRemoteApp med namnet och FriendlyName markerat för att redigera en anpassad R D P-egenskap.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Återställ alla anpassade RDP-egenskaper

Du kan återställa enskilda anpassade RDP-egenskaper till sina standardvärden genom att följa anvisningarna i [lägga till eller redigera en enskild anpassad RDP-egenskap](#add-or-edit-a-single-custom-rdp-property), eller så kan du återställa alla anpassade RDP-egenskaper för en värd pool genom att köra följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat RDP-egenskaperna för en specifik adresspool kan du logga in på en Windows Virtual Desktop-klient för att testa dem som en del av en användarsession. Följande två instruktioner visar hur du ansluter till en session med valfri klient:

- [Ansluta med Windows-skrivbordsklienten](connect-windows-7-10-2019.md)
- [Ansluta med webbklienten](connect-web-2019.md)