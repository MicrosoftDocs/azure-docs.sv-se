---
title: Översikt över underhålls kontroll för virtuella Azure-datorer med hjälp av Azure Portal
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med underhålls kontroll.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 290a1e8da4e9b3e8eff171ab2d5837bfc9c381b9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552430"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Hantera plattforms uppdateringar med underhålls kontroll 

Hantera plattforms uppdateringar, som inte kräver omstart, med hjälp av underhålls kontroll. Azure uppdaterar ofta sin infrastruktur för att förbättra tillförlitligheten, prestandan, säkerheten eller lansera nya funktioner. De flesta uppdateringar är transparenta för användarna. Vissa känsliga arbets belastningar, t. ex. spel, medie direkt uppspelning och ekonomiska transaktioner, kan inte tolerera ens några sekunder av en virtuell dator som fryser eller kopplar från för underhåll. Med underhålls kontrollen får du möjlighet att vänta på plattforms uppdateringar och tillämpa dem i ett rullande 35-dagars fönster. 

Med underhålls kontrollen kan du bestämma när du ska tillämpa uppdateringar på dina isolerade virtuella datorer och Azure-dedikerade värdar.

Med underhålls kontroll kan du:
- Batch-uppdateringar till ett uppdaterings paket.
- Vänta upp till 35 dagar för att installera uppdateringar. 
- Automatisera plattforms uppdateringar genom att konfigurera ett underhålls schema eller genom att använda [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Underhålls konfigurationer fungerar mellan prenumerationer och resurs grupper. 

## <a name="limitations"></a>Begränsningar

- Virtuella datorer måste finnas på en [dedikerad värd](./dedicated-hosts.md)eller skapas med en [isolerad VM-storlek](isolation.md).
- Om ett underhålls schema deklareras, måste det vara minst 2 timmar.
- Efter 35 dagar tillämpas en uppdatering automatiskt.
- Användaren måste ha åtkomst till **resurs deltagare** .

## <a name="management-options"></a>Hanterings alternativ

Du kan skapa och hantera underhålls konfigurationer med något av följande alternativ:

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure-portalen](maintenance-control-portal.md)

Ett Azure Functions-exempel finns i [Schemalägga underhålls uppdateringar med underhålls kontroll och Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [underhåll och uppdateringar](maintenance-and-updates.md).
