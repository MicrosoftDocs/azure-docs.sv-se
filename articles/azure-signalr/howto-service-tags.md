---
title: Använda service märken
titleSuffix: Azure SignalR Service
description: Använd service märken för att tillåta utgående trafik till Azure SignalR-tjänsten
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152326"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Använda service märken för Azure SignalR service

Du kan använda [service märken](../virtual-network/network-security-groups-overview.md#service-tags) för Azure SignalR service när du konfigurerar [nätverks säkerhets gruppen](../virtual-network/network-security-groups-overview.md#network-security-groups). Det gör att du kan definiera utgående nätverks säkerhets regler till Azure SignalR service-slutpunkter utan att behöva hårdkoda IP-adresser.

Tjänsten Azure SignalR hanterar dessa service märken. Du kan inte skapa en egen service tag eller ändra en befintlig. Microsoft hanterar dessa adressprefix som matchar tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

## <a name="use-service-tag-on-portal"></a>Använd service tag på portalen

Du kan tillåta utgående trafik till Azure SignalR-tjänsten genom att lägga till en ny utgående nätverks säkerhets regel:

1. Gå till nätverks säkerhets gruppen.

1. Klicka på Inställningar-menyn med namnet **utgående säkerhets regler**.

1. Klicka på knappen **+ Lägg till** överst.

1. Välj **service tag** under **mål**.

1. Välj **AzureSignalR** under **destination service tag**.

1. Fyll i **443** i **mål ports intervall**.

    ![Skapa en utgående säkerhets regel](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Justera andra fält efter dina behov.

1. Klicka på **Lägg till**.


## <a name="next-steps"></a>Nästa steg

- [Nätverks säkerhets grupper: service Taggar](../virtual-network/network-security-groups-overview.md#security-rules)