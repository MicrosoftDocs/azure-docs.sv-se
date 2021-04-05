---
title: Exempel program för åtkomst till kommersiella Marketplace Analytics-data
description: Använd exempel programmet för att bygga ett eget program för kommersiell marknads plats analys.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584223"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Exempel program för åtkomst till kommersiella Marketplace Analytics-data

Exempel program skapas i C# och JAVA-språk och är tillgängliga på [GitHub](https://github.com/partneranalytics).

- [Exempel program för C#](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Exempel program för JAVA](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Du kan välja att ta inspiration från exempel programmet och skapa ditt eget program på valfritt språk.

Exempel programmet uppnår följande mål:

- Genererar en Azure Active Directory-token (Azure AD).
- Hämtar tillgängliga data uppsättningar.
- Skapar användardefinierade frågor.
- Hämtar användardefinierade och system frågor.
- Schemalägger en rapport.

Exempel programmet behandlar inte metoden för att anropa API: er för andra funktioner. Processen för att anropa andra API: er är dock densamma som beskrivs ovan.

## <a name="how-to-run-the-application"></a>Så här kör du programmet

1. Klona lagrings platsen till ett lokalt system med hjälp av det här kommandot:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Mer information finns `ProgrammaticExportSampleAppISV/README.md` i filen i GitHub- [lagringsplatsen](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git).

1. Om du snabbt vill köra appen uppdaterar du klient-ID och klient hemlighet i **appsettings.Development.jspå**

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Visar ett kodfragment till appsettings.Development.jsi filen.":::

Att köra appen startar en lokal webb server och en sida öppnas ( `https://localhost:44365/ProgrammaticExportSampleApp/sample` ).

[![Visar sidan schema rapport.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

På den här sidan kommer API-anrop till den webb server som körs på den lokala datorn, som i sin tur gör de faktiska API-anropen för programmering.

## <a name="code-snippets"></a>Kodfragment

Den grundläggande strukturen i C#-koden för att göra programmerings anropen för API: er är följande:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Skärm bild av API-anrop.":::

## <a name="next-steps"></a>Nästa steg

- [API: er för åtkomst till kommersiella data för Marketplace-analys](analytics-available-apis.md)
