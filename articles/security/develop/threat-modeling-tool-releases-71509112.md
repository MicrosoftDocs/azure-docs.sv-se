---
title: Microsoft Threat Modeling Tool version 9/12/2018
titleSuffix: Azure
description: Läs viktig information om de Microsoft Threat Modeling Tool som lanserades den 9/12/2018. Anteckningarna innehåller funktions ändringar och fel korrigeringar.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: 7b0afdd90f2a1413c7f8364fc4518c4d1116454a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913593"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA Release 7.1.50911.2 – 9/12/2018

Vi är glada över att kunna meddela att Microsoft Threat Modeling Tool nu är tillgängligt att ladda ned som en allmänt tillgänglig (GA) version som stöds. Den här versionen innehåller viktiga sekretess-och säkerhets uppdateringar, samt fel korrigeringar, funktions uppdateringar och stabilitets förbättringar. Befintliga användare av 2017 Preview-versionen uppmanas att uppdatera till den senaste versionen via ClickOnce-tekniken när klienten öppnas. För nya användare av verktyget kan du hämta- [klienten](https://aka.ms/threatmodelingtool).

I den här versionen har vi slut på support för för hands versionen av 2017 och rekommenderar alla användare av förhands gransknings uppdateringen till GA-versionen. Från och med oktober 15 2018 kommer vi att ange minimi kravet för ClickOnce-versionen för Threat Modeling Tool och alla för hands versions klienter måste uppgraderas.

Microsoft Threat Modeling Tool 2016, som är tillgänglig från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=49168), finns kvar fram till den 1 2019 oktober för kritiska säkerhets korrigeringar.

## <a name="feature-changes"></a>Funktions ändringar

### <a name="azure-stencil-updates"></a>Uppdateringar av Azure-stencil

Ytterligare Azure-stenciler och tillhör ande hot och lösningar har lagts till i stencil uppsättningen som levereras med den här versionen. Betydande ändringar har gjorts i fokus områdena i "Azure App Services", "Azure Database-erbjudanden" och "Azure Storage".

![Uppdateringar av Azure-stencil](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>OneDrive-integrerings funktionen har tagits bort

Funktionerna "Spara till OneDrive", "öppna från OneDrive" och "dela en länk" i förhands granskningen har tagits bort. Användare av OneDrive uppmuntras att använda Microsofts [OneDrive för Windows](https://onedrive.live.com/about/en-us/download/) -klient för att komma åt sina filer som lagras på OneDrive via standard dialog rutorna Spara och öppna.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Viktiga fasta buggar som rapporter ATS av kunder

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>I TMT Preview kraschar verktyget när du använder standard mal len

- När en allmän stencil (till exempel "generiskt data flöde") läggs till i arbets ytan och genererar hot, kan verktyget krascha. Det här problemet har åtgärd ATS.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>I TMT för hands version, när jag sparar en rapport eller kopierar hoten, är risk nivåerna felaktiga

- Om en användare ändrar risk nivån för vissa hot och sedan sparar en rapport eller kopierar riskerna, kan risk nivån återgå till "hög". Det här problemet har åtgärd ATS.

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Användare av högupplösta skärmar kan uppleva liten text i Hot egenskaperna

#### <a name="issue"></a>Problem

I analys visningen av verktyget, om användaren har en skärm med hög upplösning som är inställd på förstora för läsbarhet i Windows, kan avsnittet "möjliga minsknings åtgärder" i ett hot visas med liten text.

![Känt problem med skärmar med hög upplösning](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Lösning

Användaren kan klicka på minsknings texten och använda Windows-standardkontrollen för zoomning (Crtl) för att öka förstoringen av avsnittet.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Filer i avsnittet "nyligen öppnade modeller" i huvud fönstret kan inte öppnas

#### <a name="issue"></a>Problem

Funktionen "öppna från OneDrive" i för hands versionen har tagits bort. Användare med nyligen öppnade modeller som sparats på OneDrive får följande fel meddelande.

![Skärm dum par visar ett fel: fel vid öppning av fil objekts referensen har inte angetts till en instans av ett objekt.](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Lösning

Användare av OneDrive uppmuntras att använda Microsofts [OneDrive för Windows](https://onedrive.live.com/about/en-us/download/) -klient för att komma åt sina filer som lagras på OneDrive via dialog rutan standard och "öppna en modell".

![Skärm bild som visar vald OneDrive i dialog rutan Öppna en modell.](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Min organisation använder 2016-versionen av verktyget, kan jag använda Azures stencil uppsättning?

Ja, du kan! [Azures stencil uppsättning är tillgänglig på GitHub](https://github.com/Microsoft/threat-modeling-templates/)och kan läsas in i 2016-versionen av verktyget. Om du vill skapa en ny modell med Azures stencil uppsättning använder du dialog rutan "mall för nya modeller" på huvud meny skärmen. TMT 2016 kan inte återge länkar som finns i fälten "möjliga åtgärder" i Azures stencil uppsättning. därför kan du se länkar som visas som HTML-taggar.

![Azure stencil-uppdateringar i 2016-klienten](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - Microsoft Windows 10
- .NET-version krävs
  - .NET-3.5.2
- Ytterligare krav
  - En Internet anslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentationen för Threat Modeling Tool finns på [docs.Microsoft.com](threat-modeling-tool.md)och innehåller information [om hur du använder verktyget](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
