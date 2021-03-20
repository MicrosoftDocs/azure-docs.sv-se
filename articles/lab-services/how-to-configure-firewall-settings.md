---
title: Brandväggsinställningarna för Azure Lab Services
description: Lär dig hur du fastställer den offentliga IP-adressen och port nummer intervallet för virtuella datorer i ett labb så att information kan läggas till i brand Väggs regler.
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: 067a2c9672b87974557f650cef07b0394e7d5a63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85445856"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Brandväggsinställningarna för Azure Lab Services

Varje organisation eller skola kommer att konfigurera sina egna nätverk på ett sätt som bäst passar deras behov.  Ibland ingår det att ställa in brand Väggs regler som blockerar Remote Desktop Protocol (RDP) eller SSH-anslutningar (Secure Shell) till datorer utanför deras egna nätverk.  Eftersom Azure Lab Services körs i det offentliga molnet, kan en del extra konfiguration behöva göra det möjligt för studenter att komma åt sin virtuella dator vid anslutning från campus-nätverket.

Varje labb använder en enskild offentlig IP-adress och flera portar.  Alla virtuella datorer, både den virtuella datorn och elevens virtuella datorer, kommer att använda den här offentliga IP-adressen.  Den offentliga IP-adressen ändras inte under Labbets livs längd.  Varje virtuell dator kommer dock att ha ett annat port nummer.  Port numren sträcker sig från 49152 till 65535.  Kombinationen av den offentliga IP-adressen och port numret används för att ansluta lärare och studenter till rätt virtuell dator.  Den här artikeln beskriver hur du hittar den enskilda offentliga IP-adressen som används av ett labb.  Den informationen kan användas för att uppdatera inkommande och utgående brand Väggs regler så att eleverna kan komma åt sina virtuella datorer.

>[!IMPORTANT]
>Varje labb kommer att ha en annan offentlig IP-adress.

## <a name="find-public-ip-for-a-lab"></a>Hitta en offentlig IP-adress för ett labb

De offentliga IP-adresserna för varje labb visas på sidan **alla** labb i labb tjänst labb kontot.  Anvisningar om hur du hittar sidan **alla labb** finns i [Visa labb i ett labb konto](manage-labs.md#view-labs-in-a-lab-account).  

> [!div class="mx-imgBorder"]
> ![Sidan alla labb](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Du ser inte den offentliga IP-adressen om mallens dator för ditt labb inte har publicerats ännu.

## <a name="conclusion"></a>Slutsats

Nu vet vi den offentliga IP-adressen för labbet.  Regler för inkommande och utgående trafik kan skapas för organisationens brand vägg för den offentliga IP-adressen och port intervallet 49152-65535.  När reglerna har uppdaterats kan studenter komma åt sina virtuella datorer utan att nätverks brand väggen blockerar åtkomsten.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Tillåt labbets skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md)
- [Anslut ditt labb nätverk till ett peer-virtuellt nätverk](how-to-connect-peer-virtual-network.md)
- [Koppla ett delat avbildnings galleri till ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägg till en användare som labb ägare](how-to-add-user-lab-owner.md)
- [Visa brand Väggs inställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)
