---
title: Azure Cloud Shell begränsningar | Microsoft Docs
description: Översikt över begränsningar för Azure Cloud Shell
services: azure
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
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74951487"
---
# <a name="limitations-of-azure-cloud-shell"></a>Begränsningar för Azure Cloud Shell

Azure Cloud Shell har följande kända begränsningar:

## <a name="general-limitations"></a>Allmänna begränsningar

### <a name="system-state-and-persistence"></a>System tillstånd och persistence

Datorn som tillhandahåller Cloud Shell-sessionen är temporär och återvinns när sessionen är inaktiv i 20 minuter. Cloud Shell kräver att en Azure-filresurs monteras. Därför måste prenumerationen kunna konfigurera lagrings resurser för att få åtkomst till Cloud Shell. Andra överväganden är:

* Med monterad lagring sparas endast ändringar i `$Home` katalogen.
* Azure-filresurser kan bara monteras inifrån din [tilldelade region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * I bash kör `env` du för att hitta din regions uppsättning som `ACC_LOCATION` .

### <a name="browser-support"></a>Stöd för webbläsare

Cloud Shell stöder de senaste versionerna av Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox och Apple Safari. Safari i privat läge stöds inte.

### <a name="copy-and-paste"></a>Kopiera och klistra in

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>För en specifik användare kan endast ett gränssnitt vara aktivt

Användare kan bara starta en typ av gränssnitt i taget, antingen **bash** eller **PowerShell**. Du kan dock ha flera instanser av bash eller PowerShell som körs samtidigt. Växling mellan bash eller PowerShell med hjälp av menyn leder till att Cloud Shell startas om, vilket avslutar befintliga sessioner. Du kan också köra bash i PowerShell genom att skriva `bash` , och du kan köra PowerShell inuti bash genom att skriva `pwsh` .

### <a name="usage-limits"></a>Användningsgränser

Cloud Shell är avsedd för interaktiva användnings fall. Det innebär att alla tids krävande sessioner som inte är interaktiva avslutas utan varning.

## <a name="bash-limitations"></a>Bash-begränsningar

### <a name="user-permissions"></a>Användarbehörigheter

Behörigheter anges som vanliga användare utan sudo-åtkomst. Ingen installation utanför `$Home` katalogen har sparats.

### <a name="editing-bashrc-or-profile"></a>Redigera. bashrc eller $PROFILE

Var försiktig när du redigerar. bashrc eller PowerShell $PROFILE-filen. Detta kan orsaka oväntade fel i Cloud Shell.

## <a name="powershell-limitations"></a>PowerShell-begränsningar

### <a name="azuread-module-name"></a>`AzureAD` Modulnamn

`AzureAD`Modulnamnet är för närvarande `AzureAD.Standard.Preview` samma funktion.

### <a name="sqlserver-module-functionality"></a>`SqlServer` modul funktioner

`SqlServer`Modulen som ingår i Cloud Shell har bara för hands versions stöd för PowerShell Core. I synnerhet `Invoke-SqlCmd` är det inte tillgängligt ännu.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standard fil Sök väg när den skapas från Azure Drive:

Med hjälp av PowerShell-cmdlets kan användarna inte skapa filer under Azure: Drive. När användarna skapar nya filer med andra verktyg, till exempel vim eller nano, sparas filerna som `$HOME` standard. 

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

Om användaren kör ett kommando som skapar en fönster dialog ruta, ser ett fel meddelande, till exempel: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` .

### <a name="large-gap-after-displaying-progress-bar"></a>Stort mellanrum när förlopps indikatorn visas

Om användaren utför en åtgärd som visar en förlopps indikator, till exempel en flik som är slutförd i `Azure:` enheten, är det möjligt att markören inte är korrekt inställd och att ett mellanrum visas där förlopps indikatorn användes.

## <a name="next-steps"></a>Nästa steg

[Felsöka Cloud Shell](troubleshooting.md) <br>
[Snabbstart för Bash](quickstart.md) <br>
[Snabbstart för PowerShell](quickstart-powershell.md)
