---
title: Funktionell validering av en AppSource Dynamics 365-ekonomi och drifts erbjudande på Azure Marketplace.
description: Hur man kan verifiera en Dynamics 365-ekonomi och ett drift erbjudande på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: 9be90cdac742a581c6346f923f44e769c8a70f76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102613637"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>AppSource Dynamics 365 finansiering och drift funktions validering

För att du ska kunna slutföra en första publicering i [partner Center](https://partner.microsoft.com/dashboard/home), krävs det två funktionella valideringar för Dynamics 365-finanser och-åtgärder:

- Ladda upp en demonstrations video från Dynamics 365-miljön som visar grundläggande funktioner.
- Presentera skärm bilder som demonstrerar lösningens [livs cykel tjänst](https://lcs.dynamics.com/) (LCS)-miljö.

> [!NOTE]
> Efterföljande omcertifierings publiceringar kräver inte demonstration. Läs mer i [AppSource-princip dokumentet](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Så här verifierar du

Det finns två alternativ för funktions validering:

- Håll ett konferens samtal på 30 minuter med US (PST) i Stilla havs tid (PST) för att demonstrera och registrera [LCS](https://lcs.dynamics.com/) -miljön och-lösningen, eller
- I Partner Center går du till [](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)  >  **Översikt över** kommersiell Marketplace och laddar upp en demonstrations video-URL och LCS skärm bilder på fliken tilläggs innehåll i erbjudandet.

Microsofts certifierings team granskar videon och filerna och godkänner sedan antingen lösningen eller e-postmeddelanden om nästa steg.

### <a name="option-1-30-minute-conference-call"></a>Alternativ 1:30 – minut konferens samtal

Om du vill schemalägga ett slutligt gransknings samtal kontaktar du [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) med namnet på ditt erbjudande och några potentiella tids korts fack mellan 8 och 17:00 Stilla havs tid.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Alternativ 2: Ladda upp en demo video och LCS skärm bilder

1. Spela in en video och överför adressen till den värdbaserade plats du önskar. Följ dessa rikt linjer:

    - Synligt av Microsofts certifierings team.
    - Mindre än 20 minuter långt.
    - Innehåller upp till tre huvud funktioner i din lösning i Dynamics 365-miljön.

    > [!NOTE]
    > Det är acceptabelt att använda en befintlig marknadsförings video om den uppfyller rikt linjerna.

2. Ta följande skärm bilder av [LCS](https://lcs.dynamics.com/) -miljön som matchar det erbjudande eller den lösning som du vill publicera. De måste vara tillräckligt tydliga för att certifierings teamet ska kunna läsa texten. Spara skärm bilderna som JPG-filer. Du kan ge [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) behörighet till din LCS-miljö så att vi kan verifiera installationen i stället för att tillhandahålla skärm bilder.

    1. Gå till **LCS** för  >  **affärs process modelleraren**  >  . Ta skärm bilder av alla process steg. Ta med **diagrammen** och **granskade** kolumner, som du ser här:

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="Visar projekt biblioteks fönstret.":::

      2. Gå till **LCS**  >  **lösnings hanterings**  >  **test lösnings paket**. Ta skärm bilder som innehåller paket översikt och innehåll som visas i följande exempel:

    | Fält | Bild |
    | --- | --- |
    | Paketöversikt | [![Skärm bild som visar fönstret "paket översikt".](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>Lösnings god kännare</li></ul> | [![Paket översikts skärmen](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | Paket innehåll<ul><li>Modell</li><li>Paket för program varu distribution</li></ul> | [![Sidan paket innehåll ett](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>TY konfiguration</li><li>Säkerhets kopiering av databas</li></ul><br>Artefakter krävs inte i avsnittet om **konfiguration av Tyskland** . | [![Paket innehåll, skärm två](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI rapport modell</li><li>BPM-artefakt</li></ul><br>Artefakter krävs inte i **Power BI** -avsnittet. | [![Skärm för paket innehåll tre](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>Bearbeta data paket</li><li>Lösnings licens avtal och sekretess policy</li></ul><br>Avsnittet **ty-konfiguration** och **Power BI rapport modell** är valfria att inkludera för ekonomi-och drift erbjudanden. | [![Paket innehåll, skärm fyra](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    Mer information om varje avsnitt i LCS-portalen finns i [användar handboken för LCS](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide).

3. Ladda upp till Partner Center.

    1. Skapa ett text dokument som innehåller demonstrations video adressen och skärm bilderna eller spara skärm bilderna som separata JPG-filer.
    2. Lägg till text-och JPG-filer i en. zip-fil på den [kommersiella Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i Partner Center på sidan för ekonomi-och åtgärds erbjudanden, **kompletterande innehåll** .

    [![Visar fönstret projekt bibliotek](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar ett erbjudande finns i: [skapa en Dynamics 365 för drift erbjudande](./partner-center-portal/create-new-operations-offer.md).