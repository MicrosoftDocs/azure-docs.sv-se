---
title: Snabbstart – Konfigurera Azure Analysis Services serverbrandväggen | Microsoft Docs
description: Den här snabbstarten hjälper dig att konfigurera en brandvägg Azure Analysis Services en server med hjälp av Azure Portal.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/12/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom:
- mode-portal
ms.openlocfilehash: dd8324607667c6d1f5e397a3266026cf7f2dcc93
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536717"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Snabbstart: Konfigurera serverbrandvägg – portalen

Den här snabbstarten hjälper dig att konfigurera en brandvägg för Azure Analysis Services-servern. Att kunna aktivera en brandvägg och konfigurera IP-adressintervall enbart för de datorer som har åtkomst till servern är en viktig del i skyddet av din server och dina data.

## <a name="prerequisites"></a>Förutsättningar

- En Analysis Services-server i din prenumeration. Mer information finns i [Snabbstart: Skapa en server – Portal](analysis-services-create-server.md) eller [Snabbstart: Skapa en server – PowerShell](analysis-services-create-powershell.md)
- Ett eller flera IP-adressintervall för klientdatorer (om så behövs).

> [!NOTE]
> Anslutningar för dataimport (uppdatering) och sidnumrerade rapporter från Power BI Premium i Microsoft Cloud Tyskland stöds för närvarande inte när en brandvägg är aktiverad, även om inställningen Tillåt åtkomst från Power BI är inställd på På.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal 

[Logga in på portalen](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurera en brandvägg

1. Öppna sidan Översikt genom att klicka på servern. 
2. I **INSTÄLLNINGAR**  >    >  **Brandväggen Aktivera brandvägg** väljer du **På**.
3. Om du vill aktivera anslutningar Power BI och Power BI Premium i **Tillåt åtkomst från Power BI** väljer du **På**.  
4. (Valfritt) Ange ett eller flera IP-adressintervall. Ange ett namn, och start- och slut-IP-adress för varje intervall. Brandväggsregelns namn ska vara begränsat till 128 tecken och får bara innehålla versaler, gemener, siffror, understreck och bindestreck. Blanksteg och andra specialtecken tillåts inte.
5. Klicka på **Spara**.

     ![Brandväggsinställningar](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort IP-adressintervallen eller inaktivera brandväggen när behovet inte längre finns.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten du har lärt dig hur du konfigurerar en brandvägg för servern. Nu när du har en server och har skyddat den med en brandvägg kan du lägga till en grundläggande exempeldatamodell till den från portalen. Att använda en exempelmodell är en bra idé om du vill lära dig mer om hur man konfigurerar modelldatabasroller och testar klientanslutningar. Fortsätt till och lägg till en exempelmodell om du vill lära dig mer.

> [!div class="nextstepaction"]
> [Självstudier: Lägg till en exempelmodell till servern](analysis-services-create-sample-model.md)
