---
title: Azure Lab Services i Microsoft Teams
description: Ger en översikt över hur du använder Azure Lab Services i Microsoft Teams.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433927"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Azure Lab Services i Microsoft Teams

Azure Lab Services kan utnyttjas i Microsoft Teams med hjälp av **Azure Lab Services** Teams-appen. Alla team ägare med behörigheten ägare/deltagare/skapare till labb kontona kan skapa labb och etablera virtuella datorer till alla i teamet.

Den här artikeln beskriver fördelarna med att använda Azure Lab Services i team och innehåller länkar till andra artiklar för instruktioner om hur du skapar och hanterar labb i team. 

> [!NOTE]
>**Azure Lab Services** Team-appen kan bara läggas till i ett team, den kan inte läggas till i enskilda chattar eller gruppchattar.

## <a name="benefits"></a>Fördelar

Azure Lab Services integrering med Microsoft Teams hjälper lärare att ställa in en klass rums miljö och tillhandahålla virtuella labb miljöer i teamet (klass): 

* Lärare kan ställa in labb så att eleverna kan komma åt sina virtuella datorer inifrån team, utan att lämna lag och behöva navigera till [Azure Lab Services webbplats](https://labs.azure.com).
* Enkel inloggning (SSO) från team till Azure Lab Services.
* Team-och labb ägare behöver inte underhålla klass listor i två olika system – labb användar lista fylls i automatiskt från grupp medlemskapet och en synkronisering utförs automatiskt var 24: e timme. 
* När den första publiceringen av mallen VM har labb kapaciteten (det vill säga antalet virtuella datorer i labbet) justeras automatiskt baserat på tillägg/borttagning av användare från grupp medlemskapet. 
* Team-och labb ägare kommer bara att visa de labb som är relaterade till teamet och studenter kommer bara att se de virtuella datorerna, som är etablerade för det aktuella teamet. 
* Användare registreras automatiskt i labbet och de virtuella datorerna tilldelas automatiskt vid den första inloggningen när labbet har publicerats. Lärare behöver inte skicka inbjudningar och studenter behöver inte registrera sig för labbet separat.  

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Kom igång och skapa ett labb i Teams](how-to-get-started-create-lab-within-teams.md)
- [Hantera användar listor för labb i team](how-to-manage-user-lists-within-teams.md)
- [Hantera Labbets VM-pool i Teams](how-to-manage-vm-pool-within-teams.md)
- [Skapa och hantera labb scheman i team](how-to-create-schedules-within-teams.md)
- [Åtkomst till en virtuell dator i Teams – elev visning](how-to-access-vm-for-students-within-teams.md)
