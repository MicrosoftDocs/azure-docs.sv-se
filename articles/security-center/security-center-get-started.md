---
title: Uppgradera till Azure Defender – Azure Security Center
description: Den här snabb starten visar hur du uppgraderar till Security Center Azure Defender för ytterligare säkerhet.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099360"
---
# <a name="quickstart-set-up-azure-security-center"></a>Snabb start: Konfigurera Azure Security Center

Azure Security Center ger enhetlig säkerhets hantering och skydd mot hot i dina arbets belastningar för Hybrid-och flera moln. De kostnads fria funktionerna ger begränsad säkerhet för dina Azure-resurser, och gör att Azure Defender utökar dessa funktioner till lokala och andra moln. Azure Defender hjälper dig att hitta och åtgärda säkerhets problem, använda åtkomst-och program kontroller för att blockera skadlig aktivitet, identifiera hot med analys och intelligens och svara snabbt vid angrepp. Du kan testa Azure Defender utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

I det här snabb starts avsnittet får du stegvisa anvisningar om hur du aktiverar Azure Security Center och Azure Defender. När du har slutfört alla steg i snabb starten har du följande:

> [!div class="checklist"]
> * Security Center aktiverat på dina Azure-prenumerationer
> * Azure Defender aktiverat på dina Azure-prenumerationer
> * Konfiguration av automatisk data insamling
> * E-postaviseringar har kon figurer ATS för säkerhets aviseringar
> * Dina hybrid datorer och flera moln datorer som är anslutna till Azure

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Om du vill aktivera Azure Defender för en prenumeration måste du ha tilldelats rollen som prenumerations ägare, prenumerations deltagare eller säkerhets administratör.

## <a name="enable-security-center-on-your-azure-subscription"></a>Aktivera Security Center på din Azure-prenumeration

> [!TIP]
> Om du vill aktivera Security Center för alla prenumerationer i en hanterings grupp, se [aktivera Security Center på flera Azure-prenumerationer](onboard-management-group.md).

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).

1. Från portalens meny väljer du **Security Center**. 

    Översikts sidan för Security Center öppnas.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Översikts instrument panel för Security Center" lightbox="./media/security-center-get-started/overview.png":::

    **Security Center – Översikt** ger en enhetlig överblick över säkerhetstillståndet för dina hybridarbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för arbetsbelastningarna samt identifiera och minimera risker. Security Center automatiskt utan kostnad, aktiverar alla dina Azure-prenumerationer som inte tidigare har registrerats av dig eller någon annan prenumerations användare.

Du kan visa och filtrera listan över prenumerationer genom att välja meny alternativet **prenumerationer** . Security Center kommer att justera visningen så att den återspeglar säkerhets position för de valda prenumerationerna. 

Inom några minuter efter start Security Center första gången kan du se:

- **Rekommendationer** för hur du kan förbättra säkerheten för dina anslutna resurser.
- En förteckning över dina resurser som nu utvärderas av Security Center, tillsammans med säkerhets position för var och en.

Fortsätt med nästa steg i avsnittet snabb start för att dra full nytta av Security Center.



## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du aktiverat Azure Security Center. Nästa steg är att aktivera Azure Defender för enhetlig säkerhets hantering och skydd mot hot i dina hybrid moln arbets belastningar.

> [!div class="nextstepaction"]
> [Snabb start: Aktivera Azure Defender](enable-azure-defender.md)