---
title: Guide för Azure Storage Explorer BLOB-versioner | Microsoft Docs
description: Vägledning för BLOB-versioner för Azure Storage Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783835"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Guide för Azure Storage Explorer BLOB-versioner

Microsoft Azure Storage Explorer ger enkel åtkomst och hantering av BLOB-versioner. Den här guiden hjälper dig att förstå hur BLOB-versioner fungerar i Storage Explorer. Innan du fortsätter rekommenderar vi att du läser mer om [BLOB-versioner](../blobs/versioning-overview.md).

## <a name="terminology"></a>Terminologi

Det här avsnittet innehåller några definitioner som hjälper dig att förstå deras användning i den här artikeln.

- Mjuk borttagning: en alternativ funktion för automatisk data skydd. Du kan läsa mer om mjuk borttagning [här](../blobs/soft-delete-blob-overview.md).
- Aktiv BLOB: en BLOB-eller blob-version skapas i aktivt läge. Du kan bara använda blobbar eller BLOB-versioner i aktivt läge.
- Mjuk borttagning av BLOB: en BLOB-eller blob-version som marker ATS som mjuk-borttagen. Mjuk borttagning av blobar bevaras bara för dess kvarhållningsperiod.
- Blob-version: en blob som skapats med aktiverat BLOB-versioner. Varje blob-version är associerad med ett versions-ID.
- Aktuell version: en blob-version som marker ATS som den aktuella versionen.
- Tidigare version: en blob-version som inte är den aktuella versionen.
- BLOB som inte är version: en blob som skapats med BLOB-versioner inaktiverat. En blob som inte är en version saknar versions-ID.

## <a name="view-blob-versions"></a>Visa BLOB-versioner

Storage Explorer stöder fyra olika vyer för att Visa blobbar.

| Visa | Aktiva blobar som inte är av version | Mjuk borttagning av BLOB-blobar som inte är versions hantering | BLOB-versioner |
| ---- | :----------: | :-----------: | :------------------: |
| Aktiva blobbar | Ja | Inga | Endast aktuell version |
| Aktiva blobbar och mjuk borttagna blobar | Ja | Ja | Endast aktuell version |
| Aktiva blobbar och blobbar utan aktuell version | Ja | Inga | Aktuell version eller senaste aktiva version |
| Alla blobbar och blobbar utan aktuell version | Ja | Ja | Aktuell version eller senaste version |

### <a name="active-blobs"></a>Aktiva blobbar

I den här vyn visas Storage Explorer:

- Aktiva blobar som inte är av version
- Aktuella versioner

### <a name="active-blobs-and-soft-deleted-blobs"></a>Aktiva blobbar och mjuk borttagna blobar

I den här vyn visas Storage Explorer:

- Aktiva blobar som inte är av version
- Mjuk borttagning av BLOB-blobar som inte är versions hantering
- Aktuella versioner.

### <a name="active-blobs-and-blobs-without-current-version"></a>Aktiva blobbar och blobbar utan aktuell version

I den här vyn visas Storage Explorer:

- Aktiva blobar som inte är av version
- Aktuella versioner
- Senaste aktiva tidigare versioner. 

För blobbar som inte har en aktuell version men som har en aktiv tidigare version, visar Storage Explorer sin senaste aktiva tidigare version som en representation av denna blob.

### <a name="all-blobs-and-blobs-without-current-version"></a>Alla blobbar och blobbar utan aktuell version

I den här vyn visas Storage Explorer:

- Aktiva blobar som inte är av version
- Mjuk borttagning av BLOB-blobar som inte är versions hantering
- Aktuella versioner
- Senaste tidigare versioner. 

För blobbar som inte har en aktuell version visar Storage Explorer sin senaste tidigare version som en representation av denna blob.

> [!Note]
> På grund av begränsningar i tjänsten behöver Storage Explorer viss ytterligare bearbetning för att få en hierarkisk vy över dina virtuella kataloger när du visar BLOB-versioner. Det tar längre tid att lista blobbar i följande vyer:
> 
> - Aktiva blobbar och blobbar utan aktuell version
> - Alla blobbar och blobbar utan aktuell version

## <a name="manage-blob-versions"></a>Hantera BLOB-versioner

### <a name="view-versions-of-a-blob"></a>Visa versioner av en BLOB

Storage Explorer innehåller ett **hantera versioner** -kommando för att visa alla versioner av en blob. Om du vill visa en blobs versioner väljer du den blob som du vill visa versioner för och väljer **Hantera historik &rarr; hantera versioner** från antingen verktygsfältet eller snabb menyn.

### <a name="download-blob-versions"></a>Hämta BLOB-versioner

Om du vill ladda ned en eller flera BLOB-versioner väljer du de BLOB-versioner som du vill hämta och väljer **Hämta** i verktygsfältet eller snabb menyn.

Om du hämtar flera versioner av en BLOB har de hämtade filerna sina versions-ID: n i början av deras fil namn.

### <a name="delete-blob-versions"></a>Ta bort BLOB-versioner

Om du vill ta bort en eller flera BLOB-versioner väljer du de BLOB-versioner som du vill ta bort och väljer **ta bort** i verktygsfältet eller på snabb menyn.

BLOB-versioner omfattas av principen för mjuk borttagning. Om mjuk borttagning har Aktiver ATS tas BLOB-versioner bort. Ett specialfall är att ta bort en aktuell version. Om du tar bort en aktuell version blir den automatiskt en aktiv tidigare version i stället.

### <a name="promote-blob-version"></a>Befordra blob-version

Du kan återställa innehållet i en BLOB genom att uppgradera en tidigare version så att den blir den aktuella versionen. Välj den blob-version som du vill befordra och välj **befordra version** i verktygsfältet eller på snabb menyn.

Icke-versions-blobar skrivs över av den uppgraderade BLOB-versionen. Se till att du inte längre behöver data eller säkerhetskopiera dina data innan du bekräftar åtgärden. Aktuella versioner blir automatiskt tidigare versioner, så Storage Explorer inte uppmanas att bekräfta.

### <a name="undelete-blob-version"></a>Ångra borttagning av blob-version

BLOB-versioner kan inte tas bort separat. De måste ångras samtidigt. Om du vill ångra borttagningen av alla BLOB-versioner av en BLOB väljer du någon av BLOB-versionerna och väljer **ångra borttagning valt** i verktygsfältet eller i snabb menyn.

### <a name="change-access-tier-of-blob-versions"></a>Ändra åtkomst nivå för BLOB-versioner

Varje blob-version har sin egen åtkomst nivå. Om du vill ändra åtkomst nivån för BLOB-versioner väljer du de BLOB-versioner som du vill ändra åtkomst nivå och väljer **ändra åtkomst nivå...** från snabb menyn.

## <a name="see-also"></a>Se även

* [BLOB-versioner](../blobs/versioning-overview.md)
* [Mjuk borttagning för blobar](../blobs/soft-delete-blob-overview.md)