---
title: Uppdatera hot informations data
description: Threat Intelligence-datapaketet tillhandahålls med varje ny version av Defender för IoT, eller om det behövs mellan versioner.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ddf2eb2fab6281e76bac0a958024b639d18a7ccc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521415"
---
# <a name="threat-intelligence-research-and-packages"></a>Forsknings och paket för hot information

Säkerhets team i Microsoft genomför privat ICS Hot information och sårbarhets forskning. Dessa team inkluderar MSTIC (Microsoft Threat Intelligence Center), DART (Microsoft Detection and Response-teamet), DCU (Digital brottslighet Unit) och avsnitt 52 (IoT/inte/ICS-domän experter som spårar ICS-vissa noll dagar, reverse-teknik, kampanjer och angripare).

Teamen ger säkerhets identifiering, analys och svar till Microsofts:

- Moln infrastruktur och tjänster.
- Traditionella produkter och enheter.
- Interna företags resurser.

Säkerhets team får fördelarna med:

- Skydd mot kända och relevanta hot.
- Insikter som hjälper dig att prioritering och prioritera.
- En förståelse för den fullständiga kontexten av hot innan de påverkas.
- Mer relevanta, korrekta och åtgärds bara data.

Den här intelligensen lägger till sammanhangsbaserad information för att utöka Microsoft Platform Analytics och stöder företagets hanterade tjänster för incident svar och brotts utredning. Hot informations paket innehåller signaturer (inklusive signaturer för skadlig kod), CVEs och annat säkerhets innehåll.

Du kan ladda ned paket från sidan **uppdateringar** i Azure Defender för IoT-portalen.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Hämta uppdateringar via Azure Defender för IoT-portalen.":::

## <a name="update-threat-intelligence-data"></a>Uppdatera hot informations data

Hot information-paket tillhandahålls med varje ny version av Defender for IoT-versionen, eller om det behövs mellan versioner.

Paket som du hämtar från Defender för IoT-portalen kan laddas upp manuellt till enskilda sensorer. Om den lokala hanterings konsolen hanterar sensorer kan du hämta hot informations paket till hanterings konsolen och skicka dem till flera sensorer samtidigt.

Så här uppdaterar du ett paket på en enskild sensor:

1. Gå till sidan med **uppdateringar** för Azure Defender för IoT.

2. Hämta och spara **Threat Intelligence** -paketet.

3. Logga in på sensor konsolen.

4. På menyn på sidan väljer du **Systeminställningar**.

5. Välj **hot informations data** och välj sedan **Uppdatera**.

6. Ladda upp det nya paketet.

Så här uppdaterar du ett paket på flera sensorer samtidigt:

1. Gå till sidan med **uppdateringar** för Azure Defender för IoT.

2. Hämta och spara **Threat Intelligence** -paketet.

3. Logga in i hanterings konsolen.

4. På menyn på sidan väljer du **Systeminställningar**.

5. I avsnittet **konfiguration av sensor motor** väljer du de sensorer som ska ta emot de uppdaterade paketen.  

6. I avsnittet **Select Threat Intelligence-data** väljer du plus tecknet ( **+** ).

7. Ladda upp paketet.

## <a name="see-also"></a>Se även

[Uppdatera versioner](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
