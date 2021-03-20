---
title: Ladda ned Azure Virtual wide global eller hubb-baserade VPN-profiler | Microsoft Docs
description: Lär dig hur Azure Virtual WAN erbjuder två typer av anslutningar för fjärran vändare och hur du hämtar en profil.
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: 6d5c4ba0f1f55119d1ec38296e67ae3e90c52650
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91313730"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Ladda ned en global eller hubb-baserad profil för användares VPN-klienter

Azure Virtual WAN erbjuder två typer av anslutningar för fjärran vändare: globalt och nav-baserat. Använd följande avsnitt om du vill veta mer om och hämta en profil. 

> [!IMPORTANT]
> RADIUS-autentisering stöder bara den Hub-baserade profilen.

## <a name="global-profile"></a>Global profil

Profilen pekar på en belastningsutjämnare som innehåller alla aktiva användares VPN-hubbar. Användaren dirigeras till hubben som är närmast användarens geografiska plats. Den här typen av anslutning är användbar när användare reser till olika platser ofta. Så här hämtar du den **globala** profilen:

1. Navigera till det virtuella WAN-nätverket.
2. Klicka på **VPN-konfiguration för användare**.
3. Markera den konfiguration som du vill ladda ned profilen för.
4. Klicka på **Hämta VPN-profil för virtuell WAN-användare**.

   ![Global profil](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>NAV baserad profil

Profilen pekar på ett enda nav. Användaren kan bara ansluta till den specifika hubben med den här profilen. Så här hämtar du den **navbaserade** profilen:

1. Navigera till det virtuella WAN-nätverket.
2. Klicka på **hubb** på sidan Översikt.

    ![Hubb profil 1](./media/global-hub-profile/hub1.png)
3. Klicka på **användare VPN (peka på plats)**.
4. Klicka på **Hämta VPN-profil för virtuella NAV användare**.

   ![Hubb profil 2](./media/global-hub-profile/hub2.png)
5. Kontrol lera **EAPTLS**.
6. Klicka på **skapa och ladda ned profil**.

   ![Hubb profil 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
