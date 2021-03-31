---
title: Etablera en gateway för egen värd i Azure API Management | Microsoft Docs
description: Lär dig hur du etablerar en gateway för egen värd i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: e79248e16ca21ae84022f8ac7f280d93f489c6a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87050351"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Etablera en gateway för egen värd i Azure API Management

Att tillhandahålla en gateway-resurs i Azure API Management-instansen är en förutsättning för att distribuera en gateway med egen värd. Den här artikeln vägleder dig genom stegen för att etablera en gateway-resurs i API Management.

## <a name="prerequisites"></a>Förutsättningar

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Etablera en gateway med egen värd

1. Välj **gatewayerna** under **distribution och infrastruktur**.
2. Klicka på **+ Lägg till**.
3. Ange **namn** och **region** för gatewayen.
> [!TIP]
> **Region** anger platsen för de Gateway-noder som ska associeras med denna gateway-resurs. Den är semantiskt likvärdig med en liknande egenskap som är kopplad till en Azure-resurs, men kan tilldelas ett godtyckligt sträng värde.

4. Alternativt kan du ange en **Beskrivning** av Gateway-resursen.
5. Alternativt kan du välja **+** under **API: er** för att associera en eller flera API: er med denna gateway-resurs.
> [!IMPORTANT]
> Som standard kommer inga befintliga API: er att associeras med den nya gateway-resursen. Försök att anropa dem via den nya gatewayen leder därför till `404 Resource Not Found` svar.

6. Klicka på **Lägg till**.

Nu har Gateway-resursen etablerats i API Management-instansen. Du kan fortsätta att distribuera gatewayen.

## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md)
* Lär dig mer om hur du [distribuerar en egen värd-Gateway till Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* Lär dig mer om hur du [distribuerar en egen värd-Gateway till Docker](how-to-deploy-self-hosted-gateway-docker.md)
