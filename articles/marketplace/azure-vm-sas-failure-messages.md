---
title: Virtuella dator SAS-felmeddelanden – Azure Marketplace
description: Vanliga frågor och svar när du arbetar med signaturer för delad åtkomst (SAS).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126846"
---
# <a name="virtual-machine-sas-failure-messages"></a>SAS-felmeddelanden för virtuella datorer

Följande är vanliga problem som uppstår när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de uppladdade virtuella hård diskarna för din lösning), tillsammans med föreslagna lösningar.

| Problem | Fel meddelande | Åtgärda |
| --------- | ------------------- | ------- |
| *Det gick inte att kopiera avbildningar* |  |  |
| "?" finns inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI med rekommenderade verktyg. |
| parametrarna "St" och "se" inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n med korrekta **start datum** och **slutdatum** -värden. |
| "SP = huvudwebbadress" inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n med behörigheter angivna som `Read` och `List` . |
| SAS-URI har tomma utrymmen i VHD-namn | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n för att ta bort blank steg. |
| SAS-URI-auktoriseringsfel | `Failure: Copying Images. Not able to download blob due to authorization error.` | Granska och korrigera SAS URI-formatet. Återskapa vid behov. |
| SAS-URI: n "St" och "se"-parametrarna har ingen fullständig datum-och tids specifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS-URI **start datum** och **slutdatum** parametrar ( `st` och `se` del strängar) måste ha ett fullständigt datum-och tids format, t. ex `11-02-2017T00:00:00Z` .. Förkortade versioner är ogiltiga (vissa kommandon i Azure CLI kan generera förkortade värden som standard). |
|  |  |  |

Mer information finns i [använda signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Generera SAS-URI](azure-vm-get-sas-uri.md)