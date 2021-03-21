---
title: Översikt över Power BI skapande av appar – Microsoft AppSource
description: Den här artikeln beskriver de övergripande stegen för att publicera en Power BI-app till Microsoft AppSource. De tekniska och affärs kraven som Power BI appen måste uppfylla för att publiceras på den kommersiella marknaden tillhandahålls också.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 04/02/2020
ms.openlocfilehash: b02c5bbf2e6accbea9c7a33dc26c4e89e46bb697
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97679147"
---
# <a name="power-bi-app-creation-overview"></a>Översikt över skapande av Power BI app

Den här artikeln beskriver hur du publicerar en Power BI-app till Microsoft [AppSource](https://appsource.microsoft.com/). Ett Power BI-appaket för att anpassa innehåll, inklusive data uppsättningar, rapporter och instrument paneler. Du kan sedan använda appen med andra Power BI plattformar med AppSource, utföra de justeringar och anpassningar som tillåts av utvecklaren och ansluta den till dina egna data.

## <a name="publishing-benefits"></a>Publicera förmåner

Fördelar med att publicera till den kommersiella marknads platsen:

- Marknadsför företaget genom att använda Microsoft varumärke.
- Kan komma åt fler än 100 000 000 Microsoft 365-och Dynamics 365-användare på AppSource och fler än 200 000-organisationer via Azure Marketplace.
- Ta emot leads med hög kvalitet från dessa marknads platser.
- Få dina tjänster upphöjda av Microsoft-fälten och inköps gruppen för köp.

## <a name="overview"></a>Översikt

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Översikt över stegen för att publicera en Power BI app" border="false":::

Dessa är viktiga publicerings steg:

1. Skapa ditt program i Power BI. Du får en paket installations länk, som är den viktigaste tekniska till gången för erbjudandet. Skicka test paketet till för produktion innan du skapar erbjudandet i Partner Center. Mer information finns i [Vad är Power BI appar?](/power-bi/service-template-apps-overview)
2. Lägg till marknadsförings material, till exempel officiellt namn, beskrivning och logo typer.
3. Ta med erbjudandena juridiska och stöd dokument, till exempel användnings villkor, sekretess policy, support princip och användar hjälp.
4. Skapa erbjudandet – Använd partner Center för att redigera informationen, inklusive erbjudande beskrivning, marknadsförings material, juridisk information, supportinformation och till gångs uppgifter.
5. Skicka den för publicering.
6. Övervaka processen i Partner Center där AppSource onboarding-teamet testar, validerar och certifierar appen.
7. När det är certifierat granskar du appen i dess test miljö och släpper den. Detta visar den på AppSource (den "är Live").
8. Skicka paketet till produktion i Power BI. Mer information finns i [hantera Power BI app-versionen](/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Innan du börjar

Granska länkarna nedan, som innehåller mallar, tips och exempel.

- [Skapa en Power BI-app](/power-bi/service-template-apps-create)
- [Tips för att redigera en Power BI app](/power-bi/service-template-apps-tips)
- [Exempel](/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Krav

För att kunna publiceras på den kommersiella marknads platsen måste ditt Power BIs programs erbjudande uppfylla följande tekniska och affärs krav.

### <a name="technical-requirements"></a>Tekniska krav

Den viktigaste tekniska till gången du behöver är en [Power BI app](/power-bi/connect-data/service-template-apps-overview). Detta är en samling primära data uppsättningar, rapporter eller instrument paneler. Den innehåller också valfria anslutna tjänster och inbäddade data uppsättningar, tidigare kallade [innehålls paket](/power-bi/service-organizational-content-pack-introduction). Mer information om hur du utvecklar den här typen av app finns i [Vad är Power BI appar?](/power-bi/connect-data/service-template-apps-overview).

#### <a name="get-an-installation-web-address"></a>Hämta en installations webb adress

Du kan bara bygga en Power BI-app i [Power BIs](https://powerbi.microsoft.com/) miljön.

1. Logga in med en [Power BI Pro-licens](/power-bi/service-admin-purchasing-power-bi-pro).
2. Skapa och testa din app i Power BI.
3. När du får appens installations webb adress lägger du till den på sidan **teknisk konfiguration** i Partner Center.

När din app har skapats och testats i Power BI sparar du webb adressen för programinstallationen, eftersom du behöver den för att [skapa ett erbjudande för Power BI app](create-power-bi-app-offer.md).

### <a name="business-requirements"></a>Affärskrav

Affärs kraven omfattar procedur mässiga, avtals liga och juridiska skyldigheter. Du måste:

- Vara en registrerad kommersiell Marketplace-utgivare. Om du inte har registrerat dig följer du stegen i [skapa ett kommersiellt marknads plats konto i Partner Center](create-account.md).
- Ange innehåll som uppfyller kriterierna för ditt erbjudande som ska listas på AppSource. Mer information finns i [ha en app för att visa en lista på AppSource? Så här gör](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)du.
- Godkänn och följ [Microsofts sekretess policy](https://privacy.microsoft.com/privacystatement).

## <a name="next-step"></a>Nästa steg

- [Skapa ett erbjudande för Power BI app i Partner Center](create-power-bi-app-offer.md)
