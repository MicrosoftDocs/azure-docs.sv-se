---
title: Expandera den befintliga poolen med nya sessionsbaserade värdar – Azure
description: Hur du expanderar en befintlig adresspool med nya sessionsbaserade värdar i Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b65560a3b10d04887040c4da1e137912810b3095
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929599"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Expandera en befintlig adresspool med nya värdbaserade sessioner

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).

När du ramperar användningen i din värd pool kan du behöva expandera den befintliga poolen med nya sessionsbaserade värdar för att hantera den nya belastningen.

I den här artikeln får du lära dig hur du kan expandera en befintlig adresspool med nya sessionsbaserade värdar.

## <a name="what-you-need-to-expand-the-host-pool"></a>Vad du behöver för att expandera poolen

Innan du börjar ska du kontrol lera att du har skapat en adresspool och en sessions värd för virtuella datorer (VM) med någon av följande metoder:

- [Azure-portalen](./create-host-pools-azure-marketplace.md)
- [Skapa en värdpool med PowerShell](./create-host-pools-powershell.md)

Du behöver också följande information från första gången du skapade värddatorn och de virtuella datorerna i sessionen:

- Namn på virtuell dator, avbildning och namn
- Autentiseringsuppgifter för domän anslutning
- Namn på virtuellt nätverk och undernät

## <a name="add-virtual-machines-with-the-azure-portal"></a>Lägg till virtuella datorer med Azure Portal

Expandera din värddator genom att lägga till virtuella datorer:

1. Logga in på Azure-portalen.

2. Sök efter och välj **virtuellt skriv bord i Windows**.

3. I menyn till vänster på skärmen väljer du **lagringspooler** och väljer sedan namnet på den värddator som du vill lägga till virtuella datorer i.

4. Välj **sessions-värdar** på menyn till vänster på skärmen.

5. Välj **+ Lägg** till för att börja skapa din värd pool.

6. Ignorera fliken grundläggande inställningar och välj sedan fliken **VM-information** . Här kan du Visa och redigera information om den virtuella datorn (VM) som du vill lägga till i poolen.

7. Välj den resurs grupp som du vill skapa de virtuella datorerna under och välj sedan regionen. Du kan välja den aktuella region som du använder eller en ny region.

8. Ange antalet värdar för sessionen som du vill lägga till i din värddator i **antal virtuella datorer**. Om du till exempel expanderar din värd-pool med fem värdar anger du **5**.

    >[!NOTE]
    >Även om det är möjligt att redigera avbildningen och prefixet för de virtuella datorerna, rekommenderar vi inte att du redigerar dem om du har virtuella datorer med olika avbildningar i samma adresspool. Redigera bara avbildningen och prefixet om du planerar att ta bort virtuella datorer med äldre avbildningar från den berörda poolen.

9. För information om det **virtuella nätverket** väljer du det virtuella nätverk och undernät som du vill att de virtuella datorerna ska anslutas till. Du kan välja samma virtuella nätverk som befintliga datorer som används eller välja en annan som passar den region som du valde i steg 7.

10. För **Administratörs kontot** anger du Active Directory domän användar namn och lösen ord som är associerat med det virtuella nätverk som du har valt. Dessa autentiseringsuppgifter används för att ansluta de virtuella datorerna till det virtuella nätverket.

      >[!NOTE]
      >Se till att dina administratörs namn överensstämmer med informationen som lämnas här. Och det finns inget MFA aktiverat för kontot.

11. Välj fliken **taggar** om du har några taggar som du vill gruppera de virtuella datorerna med. Annars hoppar du över den här fliken.

12. Välj fliken **Granska + skapa** . Granska dina val och om allt ser bra ut väljer du **skapa**.

## <a name="next-steps"></a>Nästa steg

Nu när du har expanderat den befintliga poolen kan du logga in på en Windows Virtual Desktop-klient för att testa dem som en del av en användarsession. Du kan ansluta till en session med någon av följande klienter:

- [Ansluta med Windows-skrivbordsklienten](./connect-windows-7-10.md)
- [Ansluta med webbklienten](./connect-web.md)
- [Ansluta med Android-klienten](./connect-android.md)
- [Ansluta med macOS-klienten](./connect-macos.md)
- [Ansluta med iOS-klienten](./connect-ios.md)
