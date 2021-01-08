---
title: Konfigurera Azure Stream Analytics verktyg för Visual Studio
description: Den här artikeln beskriver installations krav och hur du konfigurerar Azure Stream Analytics verktyg för Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018708"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installera Azure Stream Analytics verktyg för Visual Studio

Visual Studio 2019 och Visual Studio 2017 stöder Azure Data Lake och Stream Analytics verktyg. Den här artikeln beskriver hur du installerar och avinstallerar verktygen.

Mer information om hur du använder verktygen finns i [snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Installera

Visual Studio Enterprise (Ultimate/Premium), Professional och community-utgåvor har stöd för verktygen. Express Edition och Visual Studio för Mac stöder dem inte.

Vi rekommenderar Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Installera för Visual Studio 2019 och 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake och Stream Analytics verktyg är en del av **Azure-utveckling** och **data lagring och bearbetning** av arbets belastningar. Aktivera antingen en av dessa två arbets belastningar under installationen. Om Visual Studio redan är installerat väljer du **verktyg**  >  **Hämta verktyg och funktioner** för att lägga till arbets belastningar.

Hämta [Visual studio 2019 (för hands version 2 eller senare) eller Visual Studio 2017 (15,3 eller senare)](https://www.visualstudio.com/) och följ anvisningarna för att installera.

Välj arbets belastningen **data lagring och bearbetning** som det visas:

![Arbets belastningen för data lagring och bearbetning är vald](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Välj arbets belastningen **Azure Development** som det visas:

![Arbets belastningen Azure Development är vald](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

När du har lagt till arbets belastningen uppdaterar du verktygen. Den här proceduren refererar till Visual Studio 2019:

1. Välj **tillägg**  >  **Hantera tillägg**.

1. I **Hantera tillägg** väljer du **uppdateringar** och sedan **Azure Data Lake och Stream Analytics verktyg**.

1. Välj **Uppdatera** för att installera det senaste tillägget.

![Visual Studio-tillägg och uppdateringar](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Installera för Visual Studio 2015 och 2013<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise (Ultimate/Premium), Professional och community-utgåvor har stöd för verktygen. Express-versionen stöder inte dem.

* Installera Visual Studio 2015 eller Visual Studio 2013 uppdatering 4.
* Installera Microsoft Azure SDK för .NET version 2.7.1 eller senare med hjälp av [installations programmet för webb plattformen](https://www.microsoft.com/web/downloads/platform.aspx).
* Installera [Microsoft Azure Data Lake och Stream Analytics verktyg för Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Uppdatera<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

För Visual Studio 2019 och Visual Studio 2017 visas en ny versions påminnelse som Visual Studio-meddelande.

För Visual Studio 2015 och Visual Studio 2013 söker verktygen automatiskt efter nya versioner. Följ anvisningarna för att installera den senaste versionen.

## <a name="uninstall"></a>Avinstallera

Du kan avinstallera Azure Data Lake-och Stream Analytics-verktyg. För Visual Studio 2019 eller Visual Studio 2017 väljer du **verktyg**  >  **Hämta verktyg och funktioner**. I **ändra** avmarkerar du **Azure Data Lake och Stream Analytics verktyg**. Den visas under arbets belastningen **data lagring och bearbetning** eller arbets belastningen **Azure Development** .

Om du vill avinstallera från Visual Studio 2015 eller Visual Studio 2013 går du till **kontroll panelens**  >  **program och funktioner**. Avinstallera **Microsoft Azure Data Lake-och Stream Analytics-verktyg för Visual Studio**.
