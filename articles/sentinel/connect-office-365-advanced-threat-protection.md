---
title: Anslut Microsoft Defender för Office 365-data (tidigare Office 365 ATP) till Azure Sentinel | Microsoft Docs
description: Mata in Microsoft Defender för Office 365-data i Azure Sentinel för att få insyn och bygga automatiserade svars scenarier.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: d5140bcd85606213f00185d4ba1f50dbe70dba63
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655502"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Ansluta aviseringar från Microsoft Defender för Office 365 

> [!IMPORTANT]
>
> - **Microsoft Defender för Office 365** kallades tidigare **Office 365 Advanced Threat Protection (ATP)**.
>
>     Du kan se att det gamla namnet fortfarande används i produkten (inklusive dess data koppling i Azure Sentinel) under en viss tids period.
>
> - Inmatningen av aviseringar från Microsoft Defender för Office 365 är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
[Microsoft Defender för Office 365](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) skyddar din organisation mot noll dagar och andra avancerade hot som är av okänd skadlig kod i e-postmeddelanden, skadliga URL-länkar och samarbets verktyg. Genom att mata in Microsoft Defender för Office 365-aviseringar i Azure Sentinel kan du använda information om e-post, fildelning-och URL-baserade hot i dina säkerhets åtgärder. Du kan sedan mer utförligt analysera säkerhets händelser i organisationen och bygga spel böcker för effektiva och omedelbara svar.

Anslutningen importerar följande aviseringar:

- En potentiellt skadlig URL klickning upptäcktes 

- E-postmeddelanden som innehåller skadlig kod tas bort efter leverans

- E-postmeddelanden som innehåller Phish-URL: er tas bort efter leverans 

- E-post som rapporter ATS av användaren som skadlig kod eller Phish 

- Misstänkt e-post som skickar mönster upptäcktes 

- Användare begränsas från att skicka e-post 

De här aviseringarna kan ses av Office-kunder i **Office Security and Compliance Center**.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan när du aktiverar anslutningen.

- Du måste vara global administratör eller en säkerhets administratör på Azure Sentinel-arbetsytans klient.

- Du måste ha en giltig licens för [office 365 ATP-plan 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (ingår i Office 365 E5, Office 365 A5 och Microsoft 365 E5-licenser och kan köpas separat). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Ansluta till Microsoft Defender för Office 365

Om Microsoft Defender för Office 365 distribueras och om principer har kon figurer ATS kan aviseringarna enkelt matas in i Azure Sentinel.

1. I Azure Sentinel väljer du **data kopplingar** på navigerings menyn.

1. Välj **Microsoft Defender för Office 365** (kan fortfarande kallas *Office 365 Avancerat skydd*) i kopplings galleriet och välj **Öppna kopplings sida**.

1. I **konfigurations** avsnittet klickar du på **Anslut**. 

1. I avsnittet **skapa incidenter** klickar du på **Aktivera**.

1. Om du vill använda det relevanta schemat för att fråga Office 365 ATP-aviseringar söker du efter **SecurityAlert** och anger **Providerns namn** som **OATP**.

1. Välj fliken **Nästa steg** om du vill visa och använda de fråge-och analys regel mallar som paketerats med Microsoft Defender för Office 365-anslutaren.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Microsoft Defender för Office 365 till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).