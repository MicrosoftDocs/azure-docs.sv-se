---
title: Hantera en Azure-supportbegäran
description: Beskriver hur du kan visa support förfrågningar, skicka meddelanden, ändra allvarlighets grad för begäran, dela diagnostikinformation med Azure-support, öppna en stängd supportbegäran igen och ladda upp filer.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: d6c68dd341e0794a690b41b73ecc4be954db7359
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653857"
---
# <a name="manage-an-azure-support-request"></a>Hantera en Azure-supportbegäran

När du har [skapat en support förfrågan för Azure](how-to-create-azure-support-request.md)kan du hantera den i [Azure Portal](https://portal.azure.com), som beskrivs i den här artikeln. Du kan också skapa och hantera begär Anden via programmering, med hjälp av [Azures support biljett REST API](/rest/api/support)eller med hjälp av [Azure CLI](/cli/azure/azure-cli-support-request).

## <a name="view-support-requests"></a>Visa supportförfrågningar

Visa information och status för support förfrågningar genom att gå till **Hjälp + Support** förfrågningar  >   **alla support förfrågningar**.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Alla support förfrågningar":::

På den här sidan kan du söka efter, filtrera och sortera support förfrågningar. Välj en supportbegäran om du vill visa information, inklusive dess allvarlighets grad och eventuella meddelanden som är associerade med begäran.

## <a name="send-a-message"></a>Skicka ett meddelande

1. På sidan **alla support förfrågningar** väljer du support förfrågan.

1. På sidan **support förfrågan** väljer du **nytt meddelande**.

1. Ange ditt meddelande och välj **Skicka**.

## <a name="change-the-severity-level"></a>Ändra allvarlighets grad

> [!NOTE]
> Den högsta allvarlighets graden beror på [support avtalet](https://azure.microsoft.com/support/plans).
>

1. På sidan **alla support förfrågningar** väljer du support förfrågan.

1. På sidan **support förfrågan** väljer du **ändra**.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Ändra allvarlighets grad för support ärende":::

1. Azure Portal visar en av två skärmar, beroende på om din begäran redan har tilldelats till en support tekniker:

    - Om din begäran inte har tilldelats ser du en skärm som liknar följande. Välj en ny allvarlighets grad och välj sedan **ändra**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Välj en ny allvarlighets grad":::

    - Om din begäran har tilldelats ser du en skärm som liknar följande. Välj **OK** och skapa sedan ett [nytt meddelande](#send-a-message) för att begära en ändring av allvarlighets graden.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Det går inte att välja en ny allvarlighets grad":::

## <a name="share-diagnostic-information-with-azure-support"></a>Dela diagnostisk information med Azure-support

När du skapar en support förfrågan är som standard alternativet **dela diagnostisk information** valt. Detta gör att Azure-supporten kan samla in [diagnostikinformation](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) från dina Azure-resurser:

* Du kan inte avmarkera det här alternativet när en begäran har skapats.

* Om du har rensat alternativet när du skapar en begäran kan du välja det efter att begäran har skapats.

    1. På sidan **alla support förfrågningar** väljer du support förfrågan.
    
    1. På sidan **support förfrågan** väljer du **bevilja behörighet** och väljer sedan **Ja** och **OK**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Bevilja behörighet för diagnostikinformation":::

## <a name="upload-files"></a>Ladda upp filer

Du kan använda fil överförings alternativet för att ladda upp diagnostikfiler eller andra filer som du tror är relevanta för en support förfrågan.

1. På sidan **alla support förfrågningar** väljer du support förfrågan.

1. På sidan **supportbegäran** bläddrar du för att hitta filen och väljer sedan **Ladda upp**. Upprepa processen om du har flera filer.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Ladda upp fil":::

### <a name="file-upload-guidelines"></a>Rikt linjer för fil uppladdning

Följ dessa rikt linjer när du använder fil överförings alternativet:

* För att skydda din integritet ska du inte inkludera någon personlig information i överföringen.
* Fil namnet får inte vara längre än 110 tecken.
* Du kan inte ladda upp fler än en fil.
* Filerna får inte vara större än 4 MB.
* Alla filer måste ha ett fil namns tillägg, till exempel *. docx* eller *. xlsx*. I följande tabell visas de fil namns tillägg som tillåts för uppladdning.

| 0-9, A-C     | D-G   | H-M         | N-P   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | . har        | . ODX  | . rar     | .tdb       | .xlam   |
| . a          | . db   | .hwl        | . oft  | . RDL     | .tdf       | .xlr    |
| . ABC        | . TAKT  | . ICS        | . Old  | .rdlc    | . text      | .xls    |
| . adm        | .do_  | . ini        | . One  | .re_     | .thmx      | .xlsb   |
| . aspx       | .doc  | .java       | . OSD  | . reg     | .tif       | .xlsm   |
| . ATF        | .docm | .jpg        | . GENOMFÖR  | . ta bort  | . TRC       | .xlsx   |
| . b          | .docx | . LDF        | . P1   | . ren     | . TDD       | .xlt    |
| .ba_        | .dotm | . brevhuvuden | .pcap | . Byt namn  | .tx_       | .xltx   |
| . bak        | .dotx | . lnk        | . pdb  | .rft     | .txt       | .xml    |
| .bat        | .dtsx | .lo_        | .pdf  | . rpt     | .uccapilog | . XMLA   |
| . blg        | . EDS  | . log        | .piz  | . RTE     | .uccplog   | .xps    |
| .CA_        | . EMF  | .lpk        | .pmls | .rtf     | .udcx      | . xsd    |
| . FÖRA        | . eml  | . manifest   | .png  | . kör     | .vb_       | . xsn    |
| . Cap        | .emz  | . Master     | .potx | .saz     | .vbs_      | . xxx    |
| .catx       | . err  | .mdmp       | .ppt  | .sql     | . vcf       | .z_     |
| . KONFIGURATION        | . etl  | . MOF        | .pptm | .sqlplan | . vsd       | .z01    |
| . komprimerad | . evt  | .mp3        | .pptx | .stp     | .wdb       | .z02    |
| . Konfigurationsfil     | . evtx | .mpg        | . prn  | .svclog  | . WKS       | . Zi     |
| .cpk        | . PRISET   | .ms_        | . polyesterstapelfibrer  | -        | .wma       | .zi_    |
| . cpp        | .ex_  | . msg        | . pst  | -        | .wmv       | .zip    |
| .cs         | .ex0  | .msi        | . pub  | -        | . wmz       | .zip_   |
| . SKV        | . FRD  | . mso        | -     | -        | . WPS       | .zipp   |
| .cvr        | .gif  | . msu        | -     | -        | .wpt       | . zippa |
| -           | . GUID | . NFO        | -     | -        | . WSDL      | .zippy  |
| -           | . gz   | -           | -     | -        | . wsp       | .zipx   |
| -           | -     | -           | -     | -        | .wtl       | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | . zzz    |

## <a name="close-a-support-request"></a>Stänga en support förfrågan

Om du behöver stänga en support förfrågan, [Skicka ett meddelande](#send-a-message) som frågar att begäran ska stängas.

## <a name="reopen-a-closed-request"></a>Öppna en stängd begäran igen

Om du behöver öppna en stängd supportbegäran igen, skapar du ett [nytt meddelande](#send-a-message)som automatiskt öppnar begäran på nytt.

## <a name="cancel-a-support-plan"></a>Avsluta ett supportavtal

Om du behöver avbryta ett support avtal kan du läsa mer i [avbryta en Support plan](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Nästa steg

[Skapa en supportbegäran för Azure](how-to-create-azure-support-request.md)

[REST-API för Azure-supportbegäranden](/rest/api/support)
