---
title: Övervaka Surface Hub med Azure Monitor | Microsoft Docs
description: Använd Surface Hub lösning för att spåra hälso tillståndet för dina Surface Hub och förstå hur de används.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: e38214174fd4e3aa6859f05fd24737d9e9d1f963
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587219"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Övervaka Surface Hub med Azure Monitor för att spåra deras hälsa

![Surface Hub symbol](./media/surface-hubs/surface-hub-symbol.png)

I den här artikeln beskrivs hur du kan använda Surface Hub lösning i Azure Monitor för att övervaka Microsoft Surface Hub-enheter. Lösningen hjälper dig att spåra hälso tillståndet för dina Surface Hub och förstå hur de används.

Varje Surface Hub har Microsoft Monitoring Agent installerad. Genom agenten kan du skicka data från Surface Hub till en Log Analytics arbets yta i Azure Monitor. Loggfilerna läses från dina Surface Hub och skickas sedan till Azure Monitor. Problem som servrar som är offline, kalender som inte synkroniseras eller om enhets kontot inte kan logga in på Skype visas i Surface Hub instrument panelen i Azure Monitor. Genom att använda data i instrument panelen kan du identifiera enheter som inte kör eller som har andra problem och eventuellt tillämpa korrigeringar för de problem som upptäckts.

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen. För att kunna hantera Surface Hub i Azure Monitor behöver du följande:

* En [Log Analytics prenumerations](https://azure.microsoft.com/pricing/details/log-analytics/) nivå som stöder antalet enheter som du vill övervaka. Log Analytics prissättningen varierar beroende på hur många enheter som har registrerats och hur mycket data de bearbetar. Du bör tänka på detta när du planerar distributionen av Surface Hub.

Sedan kan du antingen lägga till en befintlig Log Analytics arbets yta eller skapa en ny. Detaljerade instruktioner för hur du använder någon av metoderna finns i [skapa en Log Analytics arbets yta i Azure Portal](../logs/quick-create-workspace.md). När Log Analytics arbets ytan har kon figurer ATS finns det två sätt att registrera dina Surface Hub enheter:

* Automatiskt via Intune
* Manuellt via **inställningarna** på Surface Hubs enheten.

## <a name="set-up-monitoring"></a>Konfigurera övervakning
Du kan övervaka hälsa och aktivitet för din Surface Hub med hjälp av Azure Monitor. Du kan registrera Surface Hub med hjälp av Intune eller lokalt genom att använda **inställningarna** på Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Anslut Surface Hub-Azure Monitor via Intune
Du behöver arbets ytans ID och arbets ytans nyckel för Log Analytics arbets ytan som hanterar Surface Hub. Du kan hämta dem från inställningarna för arbets ytan i Azure Portal.

Intune är en Microsoft-produkt som gör att du kan hantera konfigurations inställningarna för Log Analytics arbets ytan centralt som tillämpas på en eller flera av dina enheter. Följ dessa steg om du vill konfigurera dina enheter via Intune:

1. Logga in på Intune.
2. Navigera till **Inställningar**  >  **anslutna källor**.
3. Skapa eller redigera en princip baserat på Surface Hub-mallen.
4. Gå till avsnittet Azure-Operational Insights i principen och Lägg till Log Analytics *arbetsyte-ID* och *arbets ytans nyckel* till principen.
5. Spara principen.
6. Koppla principen till en lämplig enhets grupp.

   ![Intune-princip](./media/surface-hubs/intune.png)

Intune synkroniserar sedan Log Analytics inställningarna med enheterna i mål gruppen och registrerar dem i arbets ytan Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Anslut Surface Hub-Azure Monitor med appen Inställningar
Du behöver arbets ytans ID och arbets ytans nyckel för Log Analytics arbets ytan som hanterar Surface Hub. Du kan hämta dem från inställningarna för arbets ytan Log Analytics i Azure Portal.

Om du inte använder Intune för att hantera din miljö kan du registrera enheter manuellt via **Inställningar** på varje Surface Hub:

1. Öppna **Inställningar** från Surface Hub.
2. Ange autentiseringsuppgifter för enhets administratören när du uppmanas till det.
3. Klicka på **den här enheten** och klicka på **Konfigurera Log Analytics inställningar** under **övervakning**.
4. Välj **Aktivera övervakning**.
5. I dialog rutan Log Analytics inställningar anger du Log Analytics **arbetsyte-ID** och anger **arbets ytans nyckel**.  
   ![Skärm bild som visar Microsoft Operations Manager Suite-inställningar med Aktivera övervakning valt och text rutor för arbetsyte-ID och arbets ytans nyckel.](./media/surface-hubs/settings.png)
6. Slutför konfigurationen genom att klicka på **OK** .

En bekräftelse visas som talar om huruvida konfigurationen har tillämpats på enheten eller inte. I så fall visas ett meddelande om att agenten har anslutits till Azure Monitor. Enheten börjar sedan skicka data till Azure Monitor där du kan visa och agera på den.

## <a name="monitor-surface-hubs"></a>Övervaka Surface Hub
Att övervaka dina Surface Hub med Azure Monitor är mycket som att övervaka andra registrerade enheter.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

När du klickar på panelen Surface Hub visas enhetens hälsa.

   ![Surface Hub instrument panel](./media/surface-hubs/surface-hub-dashboard.png)

Du kan skapa [aviseringar](../alerts/alerts-overview.md) baserat på befintliga eller anpassade loggs ökningar. Med hjälp av data Azure Monitor som samlas in från dina Surface Hub kan du söka efter problem och aviseringar om de villkor som du definierar för dina enheter.

## <a name="next-steps"></a>Nästa steg
* Använd [logg frågor i Azure Monitor](../logs/log-query-overview.md) om du vill visa detaljerade Surface Hub data.
* Skapa [aviseringar](../alerts/alerts-overview.md) för att meddela dig när problem uppstår med Surface Hub.
