---
title: Ange Windows Virtual Desktop tömningsläge – Azure
description: Konfigurera och använda tömningsläge i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509129"
---
# <a name="set-drain-mode"></a>Ange tömningsläge

Tömningsläget isolerar en sessionsvärd när du vill tillämpa korrigeringar och utföra underhåll utan att störa användarsessioner. När den är isolerad accepterar sessionsvärden inte nya användarsessioner. Alla nya anslutningar omdirigeras till nästa tillgängliga sessionsvärd. Befintliga anslutningar i sessionsvärden fortsätter att fungera tills användaren loggar ut eller administratören avslutar sessionen. När sessionsvärden är i tömningsläge kan administratörer också fjärransluta till servern utan att gå Windows Virtual Desktop tjänsten. Du kan använda den här inställningen för både poolade och personliga skrivbord.

## <a name="set-drain-mode-using-the-azure-portal"></a>Ange tömningsläge med hjälp av Azure Portal

Så här aktiverar du tömningsläget i Azure Portal:

1. Öppna Azure Portal och gå till den värdpool som du vill isolera.

2. I navigeringsmenyn väljer du **Sessionsvärdar.**

3. Välj sedan de värdar som du vill aktivera tömningsläget för och välj **sedan Aktivera tömningsläge på**.

4. Om du vill inaktivera tömningsläget väljer du de värdpooler som har tömningsläget aktiverat och väljer **sedan Inaktivera tömningsläge.**

## <a name="set-drain-mode-using-powershell"></a>Ställ in tömningsläge med PowerShell

Du kan ange tömningsläge i PowerShell med *parametern AllowNewSessions,* som är en del av [kommandot Update-AzWvdSessionhost.](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true)

Kör den här cmdleten för att aktivera tömningsläge:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Kör den här cmdleten för att inaktivera tömningsläget:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>Du måste köra det här kommandot för varje sessionsvärd som du tillämpar inställningen på.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Azure Portal för Windows Virtual Desktop kan du läsa våra [självstudier.](create-host-pools-azure-marketplace.md) Om du redan är bekant med grunderna kan du kolla in några av de andra funktionerna som du kan använda med Azure Portal, till exempel [MSIX-app](app-attach-azure-portal.md) attach [och Azure Advisor](azure-advisor.md).

Om du använder PowerShell-metoden och vill se vad mer modulen kan göra kan du läsa Konfigurera [PowerShell-modulen](powershell-module.md) för Windows Virtual Desktop och vår [PowerShell-referens](/powershell/module/az.desktopvirtualization/).