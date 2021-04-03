---
title: 'Felsök: arbets ytor som skapats av SDK kan inte starta Synapse Studio'
description: Steg för att lösa arbets ytor som skapats av SDK kan inte starta Synapse Studio
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466951"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Felsöka Azure Synapse Analytics-arbetsytor som skapats med SDK

Den här artikeln innehåller fel söknings steg för att starta Synapse Studio från en Synapse-arbetsyta som skapades med en Software Development Kit (SDK).


## <a name="prerequisites"></a>Förutsättningar

- Synapse-arbetsyta som skapats med SDK

## <a name="workaround"></a>Lösning

Slutför följande steg för att starta Synapse Studio från din SDK-skapade arbets yta: 
  1.    Skapa arbets yta genom att köra `az synapse workspace create` .
  2.    Extrahera ID för hanterad identitet genom att köra `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Bevilja rollen Storage BLOB data Contributor till det hanterade identitets lagrings kontot genom att köra ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Lägg till brand Väggs regel genom att köra ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Synapse](../overview-what-is.md)
* [Komma igång](../get-started.md)
