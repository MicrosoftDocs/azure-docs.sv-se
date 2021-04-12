---
title: Publicera inbyggda appar i Windows Virtual Desktop (klassisk) – Azure
description: Publicera inbyggda appar i Windows Virtual Desktop (klassisk).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: f9cde1c31140a04d00f62350a056c5558d806531
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444385"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop-classic"></a>Publicera inbyggda appar i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../publish-apps.md).

I den här artikeln får du lära dig hur du publicerar appar i din Windows-miljö för virtuella skriv bord.

## <a name="publish-built-in-apps"></a>Publicera inbyggda appar

Publicera en inbyggd app:

1. Anslut till en av de virtuella datorerna i din värd bassäng.
2. Hämta **PackageFamilyName** för den app som du vill publicera genom att följa anvisningarna i [den här artikeln](/powershell/module/appx/get-appxpackage).
3. Kör slutligen följande cmdlet med `<PackageFamilyName>` ersatt av de **PackageFamilyName** som du hittade i föregående steg:

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Det virtuella Windows-skrivbordet stöder endast publicering av appar med installations platser som börjar med `C:\Program Files\Windows Apps` .

## <a name="update-app-icons"></a>Uppdatera app-ikoner

När du har publicerat en app kommer den att ha standard ikonen för Windows-appen i stället för dess vanliga ikon bild. Om du vill ändra ikonen till dess vanliga ikon, ska du placera avbildningen av den ikon som du vill använda på en nätverks resurs. Bild formaten som stöds är PNG, BMP, GIF, JPG, JPEG och ICO.

## <a name="publish-microsoft-edge"></a>Publicera Microsoft Edge

Processen du använder för att publicera Microsoft Edge skiljer sig lite från publicerings processen för andra appar. Om du vill publicera Microsoft Edge med standard start sidan kör du denna cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar feeds för att ordna hur appar visas för användare i [Anpassa feed för virtuella Windows-användare](customize-feed-virtual-desktop-users-2019.md).
- Lär dig mer om MSIX app Attach-funktionen i [Konfigurera MSIX app Attach](../app-attach.md).

