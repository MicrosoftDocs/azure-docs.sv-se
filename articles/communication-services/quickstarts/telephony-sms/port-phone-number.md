---
title: Snabb start – port ett telefonnummer i Azure Communication Services
description: Lär dig att Porta ett telefonnummer i kommunikations tjänst resursen
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729934"
---
# <a name="quickstart-port-a-phone-number"></a>Snabb start: port ett telefonnummer

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Kom igång med Azure Communication Services genom att Porta ditt telefonnummer i Azure Communication Services-resursen. Avgiftsfria och geografiska nummer som baseras på USA är berättigade till porting. Mer information om telefonnummer typer finns i [dokumentationen om telefonnummer](../../concepts/telephony-sms/plan-solution.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [En Active Communication Services-resurs.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Samla in information om Azure-resurser

Innan du påbörjar en port-begäran går du till Azure Portal och väljer kommunikations tjänst resursen. När `Overview` fönstret är markerat klickar du på `JSON View` länken i det övre högra hörnet:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Skärm bild som visar valet av JSON-vy.":::

Registrera resursens **Azure-ID** och **oåterkalleligt resurs-ID**:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Skärm bild som visar hur du väljer JSON-egenskaper.":::

## <a name="initiate-the-port-request"></a>Initiera port förfrågan

Avgiftsfria och geografiska nummer som baseras på USA är berättigade till porting. Använd något av följande formulär för att skicka in din port förfrågan:

- För avgiftsfritt nummer: [avgiftsfri nummer för port förfrågan](https://aka.ms/acs-port-form-tollfree)
- För geografiska nummer som är baserade på US: [geografisk nummer port förfrågan](https://aka.ms/acs-port-form-geographic)

När du är klar skickar du ditt slutförda port fråge formulär till acsporting@microsoft.com . Kontrol lera att e-postmeddelandets ämnesrad börjar med "ACS Port-In-begäran".

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att:

> [!div class="checklist"]
> * Hämta dina resurs metadata för kommunikations tjänster
> * Skicka en begäran om att Porta ditt telefonnummer

> [!div class="nextstepaction"]
> [Skicka ett SMS](../telephony-sms/send.md) 
>  [Kom igång med att anropa](../voice-video-calling/getting-started-with-calling.md)
