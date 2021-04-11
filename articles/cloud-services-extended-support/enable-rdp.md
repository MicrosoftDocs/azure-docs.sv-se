---
title: Använd fjärr skrivbordet i Cloud Services (utökad support)
description: Aktivera fjärr skrivbord för Cloud Services (utökad support)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 94827916f28c9028d46bf7b5461a4fbd941b2a96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773410"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Använda fjärr skrivbords tillägget till Azure Cloud Services (utökad support)

Azure Portal använder fjärr skrivbords tillägget för att aktivera fjärr skrivbord även när programmet har distribuerats. Med fjärr skrivbords inställningarna för moln tjänsten kan du aktivera fjärr skrivbord, uppdatera det lokala administratörs kontot, välja de certifikat som används i autentisering och ange förfallo datum för dessa certifikat. 

## <a name="apply-remote-desktop--extension"></a>Använd fjärr skrivbords tillägg
1. Navigera till den moln tjänst som du vill aktivera fjärr skrivbord för och välj **"fjärr skrivbord"** i det vänstra navigerings fönstret.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Bild visar hur du väljer alternativet fjärr skrivbord i Azure Portal":::

2. Välj **Lägg till**.
3. Välj de roller som du vill aktivera fjärr skrivbord för.
4. Fyll i de obligatoriska fälten för användar namn, lösen ord, förfallo datum och certifikat (krävs inte).
> Lägg Lösen ordet för fjärr skrivbord måste vara mellan 8-123 tecken och måste uppfylla minst tre krav på lösen ords komplexitet från följande: 1) innehåller ett versal tecken 2) innehåller ett gement tecken 3) innehåller en siffer siffra 4). det är inte tillåtet att kontrol lera tecken.

   :::image type="content" source="media/remote-desktop-2.png" alt-text="Bilden visar information som krävs för att ansluta till fjärr skrivbordet.":::

5. När du är färdig väljer du **Spara**. Det tar en stund innan roll instanserna är redo att ta emot anslutningar.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Ansluta till roll instanser med fjärr skrivbord aktiverat
När fjärr skrivbord är aktiverat på rollerna kan du starta en anslutning direkt från Azure Portal.

1. Klicka på **roller och instanser** för att öppna instans inställningarna.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="Bild visas som väljer alternativet roller och instanser på bladet konfiguration.":::

2. Välj en roll instans som har fjärr skrivbord konfigurerat.
3. Klicka på **Anslut** för att ladda ned en fjärr skrivbords anslutnings fil.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Bilden visar hur du väljer arbets Rolls instans i Azure Portal.":::
    
4. Öppna filen för att ansluta till roll instansen.


## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
