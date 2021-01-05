---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) säker export i Azure Monitor-konfiguration med BMC
description: Den här artikeln visar hur du ansluter dina ITSM-produkter/-tjänster med BMC vid säker export i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 598ce86aef41dd791099b49edccd6a55b3e43cdd
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843630"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>Ansluta BMC-Helix till Azure Monitor

Följande avsnitt innehåller information om hur du ansluter din BMC Helix-produkt och säker export i Azure.

## <a name="prerequisites"></a>Förutsättningar

Se till att du uppfyller följande krav:

* Azure AD har registrerats.
* Du har den version av BMC Helix som stöds för flera moln tjänster (version 19,08 eller senare).

## <a name="configure-the-bmc-helix-connection"></a>Konfigurera BMC Helix-anslutningen

1. Använd följande procedur i BMC Helix-miljön för att hämta URI: n för säker export:

   1. Logga in på integration Studio.
   1. Sök efter **varnings flödet Skapa incident från Azure** .
   1. Kopiera webhook-URL: en.
   
   ![Skärm bild av webhooken U R L i integration Studio.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. Följ instruktionerna enligt versionen:
   * [Aktivera inbyggd integrering med Azure Monitor för version 20,02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Aktivera inbyggd integrering med Azure Monitor för version 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. Som en del av konfigurationen av anslutningen i BMC Helix, går du till integrations-BMC-instansen och följer de här anvisningarna:

   1. Välj **katalog**.
   2. Välj **Azure-aviseringar**.
   3. Välj **kopplingar**.
   4. Välj **konfiguration**.
   5. Välj **Lägg till ny anslutnings** konfiguration.
   6. Fyll i informationen för konfigurations avsnittet:
      - **Namn**: skapa egna.
      - **Typ av auktorisering**: **ingen**
      - **Beskrivning**: skapa en egen.
      - **Webbplats**: **moln**
      - **Antal instanser**: **2**, standardvärdet.
      - **Kontrol lera**: är markerat som standard för att aktivera användning.
      - ID för Azure-klient organisation och Azure-program hämtas från det program som du definierade tidigare.

![Skärm bild som visar BMC-konfiguration.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)
