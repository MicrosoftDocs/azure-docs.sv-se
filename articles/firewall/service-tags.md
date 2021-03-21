---
title: Översikt över Azure Firewall service-Taggar
description: En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97031587"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall service-Taggar

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar, och du kan inte heller ange vilka IP-adresser som ska finnas i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Azure Firewall service-taggar kan användas i nätverks reglernas mål fält. Du kan använda dem i stället för vissa IP-adresser.

## <a name="supported-service-tags"></a>Service märken som stöds

Se [tjänst taggar för virtuella nätverk](../virtual-network/service-tags-overview.md#available-service-tags) för en lista över service märken som är tillgängliga för användning i Azure Firewall Network rules.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Firewall-regler finns i [regel bearbetnings logik för Azure-brandvägg](rule-processing.md).
