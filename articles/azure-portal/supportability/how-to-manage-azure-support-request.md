---
title: Hantera en Azure-supportbegäran
description: Beskriver hur du kan visa support förfrågningar, skicka meddelanden, ändra allvarlighets grad för begäran, dela diagnostikinformation med Azure-support, öppna en stängd supportbegäran igen och ladda upp filer.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 4d0c03e0035f6b71a23891ac1691f5421c1bdb76
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502526"
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

| 0-9, A-C    | D-G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | . har        | . ODX  | . rar     | .uccapilog | .xlam   |
| . a          | . db   | .hwl        | . oft  | . RDL     | .uccplog   | .xlr    |
| . ABC        | . TAKT  | . ICS        | . Old  | .rdlc    | .udcx      | .xls    |
| . adm        | .do_  | . ini        | . One  | .re_     | .vb_       | .xlsb   |
| . aspx       | .doc  | .java       | . OSD  | . ta bort  | .vbs_      | .xlsm   |
| . ATF        | .docm | .jpg        | . GENOMFÖR  | . ren     | . vcf       | .xlsx   |
| . b          | .docx | . LDF        | . P1   | . Byt namn  | . vsd       | .xlt    |
| .ba_        | .dotm | . brevhuvuden | .pcap | .rft     | .wdb       | .xltx   |
| . bak        | .dotx | .lo_        | . pdb  | . rpt     | . WKS       | .xml    |
| . blg        | .dtsx | . log        | .pdf  | . RTE     | .wma       | . XMLA   |
| .CA_        | . EDS  | .lpk        | .piz  | .rtf     | .wmv       | .xps    |
| . FÖRA        | . EMF  | . manifest   | .pmls | . kör     | . wmz       | . xsd    |
| . Cap        | . eml  | . Master     | .png  | .saz     | . WPS       | . xsn    |
| .catx       | .emz  | .mdmp       | .potx | .sql     | .wpt       | . xxx    |
| . KONFIGURATION        | . err  | . MOF        | .ppt  | .sqlplan | . WSDL      | .z_     |
| . komprimerad | . etl  | .mp3        | .pptm | .stp     | . wsp       | .z01    |
| . Konfigurationsfil     | . evt  | .mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | . evtx | .ms_        | . prn  | .tdb     | -          | . Zi     |
| . cpp        | . PRISET   | . msg        | . polyesterstapelfibrer  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | . mso        | . pst  | . text    | -          | .zip    |
| . SKV        | .ex0  | . msu        | . pub  | .thmx    | -          | .zip_   |
| .cvr        | . FRD  | . NFO        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | . TRC     | -          | . zippa |
| -           | . GUID | -           | -     | . TDD     | -          | .zippy  |
| -           | . gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
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
