---
title: Vanliga frågor och svar om virtuella Windows-datorer för VM Connect – Azure
description: Vanliga frågor och bästa metoder för att använda funktionen starta virtuell dator vid anslutning.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0fd6edf3fbcd6fea7503acd7265850723b5ec09f
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080494"
---
# <a name="start-vm-on-connect-faq-preview"></a>Vanliga frågor och svar om att starta VM på Connect

> [!IMPORTANT]
> Funktionen starta virtuell dator vid anslutning är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln beskriver vanliga frågor om funktionen starta virtuell dator (VM) i Connect (för hands version) för Windows-pooler för virtuella skriv bord.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Frigörs virtuella datorer automatiskt när en användare slutar använda dem?

Nej. Du måste konfigurera ytterligare principer för att logga ut användare från sina sessioner och köra Azure Automation-skript för att frigöra virtuella datorer.

Så här konfigurerar du principen för frigörning:

1. Fjärrans luta till den virtuella dator som du vill ange principen för.

2. Öppna **grupprincip redigeraren** och gå sedan till dator konfiguration för **lokal dator princip**  >    >  **administrativa mallar**  >  **Windows-komponenter**  >  **Fjärrskrivbordstjänster**  >    >  sessionsvärdservern för fjärrskrivbordssession.

3. Hitta principen som säger **angiven tids gräns för frånkopplade sessioner** och ändra värdet till **aktive rad**.

4. När du har aktiverat principen väljer du **Avsluta en frånkopplad session**.

>[!NOTE]
>Se till att ange tids gränsen för principen "avsluta en frånkopplad session" till ett värde som är större än fem minuter. En låg tids gräns kan orsaka att användarnas sessioner upphör om nätverket förlorar anslutningen för länge, vilket leder till förlorat arbete.

Signering av användare frigör inte de virtuella datorerna. Information om hur du frigör virtuella datorer finns i [starta eller stoppa virtuella datorer under flera timmar](../automation/automation-solution-vm-management.md).

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Kan användarna stänga av den virtuella datorn från sina klienter?

Ja. Användare kan stänga av den virtuella datorn genom att använda Start-menyn i sin session, precis som med en fysisk dator. Att stänga av den virtuella datorn kommer dock inte att frigöra den virtuella datorn. Information om hur du frigör virtuella datorer finns i [starta eller stoppa virtuella datorer under flera timmar](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar starta virtuell dator vid anslutning finns i [Starta virtuell dator vid anslutning (för hands version)](start-virtual-machine-connect.md).

Om du har fler allmänna frågor om virtuellt Windows-skrivbord kan du titta närmare på våra allmänna [vanliga frågor och svar](faq.md).
