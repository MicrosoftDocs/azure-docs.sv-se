---
title: Konfigurera tjänst aviseringar för Windows Virtual Desktop – Azure
description: Konfigurera Azure Service Health för att ta emot tjänst meddelanden för virtuella Windows-datorer.
author: Heidilohr
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ca5f225265b42ffc67a41a5252b2e789f7bcf500
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445643"
---
# <a name="tutorial-set-up-service-alerts"></a>Självstudie: Konfigurera tjänst aviseringar

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md).

Du kan använda Azure Service Health för att övervaka tjänst problem och hälso rekommendationer för virtuella Windows-datorer. Azure Service Health kan meddela dig om olika typer av aviseringar (t. ex. e-post eller SMS), hjälpa dig att förstå resultatet av ett problem och hålla dig uppdaterad när problemet löses. Azure Service Health kan också hjälpa dig att minska stillestånds tiden och förbereda för planerat underhåll och ändringar som kan påverka tillgängligheten för dina resurser.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa och konfigurera tjänst aviseringar.

Mer information om Azure Service Health finns i [Azure Health-dokumentationen](../service-health/index.yml).

## <a name="create-service-alerts"></a>Skapa tjänst aviseringar

I det här avsnittet visas hur du konfigurerar Azure Service Health och hur du konfigurerar aviseringar som du kan komma åt på Azure Portal. Du kan ställa in olika typer av aviseringar och schemalägga dem för att meddela dig inom rimlig tid.

### <a name="recommended-service-alerts"></a>Rekommenderade tjänst aviseringar

Vi rekommenderar att du skapar tjänst aviseringar för följande typer av hälso händelser:

- **Tjänst problem:** Ta emot meddelanden om större problem som påverkar anslutningarna för dina användare med tjänsten eller med möjligheten att hantera din Windows-klient för virtuella skriv bord.
- **Hälso rådgivning:** Ta emot meddelanden som kräver din uppmärksamhet. Följande är några exempel på den här typen av avisering:
    - Virtual Machines (VM) har inte kon figurer ATS på ett säkert sätt som öppen port 3389
    - Utfasning av funktioner

### <a name="configure-service-alerts"></a>Konfigurera tjänst aviseringar

Konfigurera tjänst aviseringar:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **service Health.**
3. Följ anvisningarna i [skapa aktivitets logg aviseringar på tjänst aviseringar](../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json) för att konfigurera aviseringar och meddelanden.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du konfigurerar och använder Azure Service Health för att övervaka tjänst problem och hälso rekommendationer för virtuella Windows-datorer. Om du vill veta mer om hur du loggar in på det virtuella Windows-skrivbordet fortsätter du till instruktionen Anslut till Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Ansluta till fjärr skrivbords klienten på Windows 7 och Windows 10](./connect-windows-7-10.md)