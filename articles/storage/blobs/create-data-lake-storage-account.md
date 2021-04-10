---
title: Skapa ett lagrings konto för Azure Data Lake Storage Gen2
description: Lär dig hur du skapar ett lagrings konto för användning med Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624345"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2

Om du vill använda Data Lake Storage Gen2 funktioner skapar du ett lagrings konto med ett hierarkiskt namn område.

## <a name="choose-a-storage-account-type"></a>Välj en lagrings konto typ

Data Lake Storage funktioner stöds i följande typer av lagrings konton:

- General-purpose v2
- BlockBlobStorage

Information om hur du väljer mellan dem finns i [Översikt över lagrings konto](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Skapa ett lagrings konto med ett hierarkiskt namn område

Skapa antingen ett [Allmänt-Purpose v2-konto](../common/storage-account-create.md) eller ett [BlockBlobStorage](storage-blob-create-account-block-blob.md) -konto med inställningen **hierarkiskt namn område** aktive rad.

Lås upp Data Lake Storage-funktioner när du skapar kontot genom att aktivera inställningen **hierarkiskt namn område** på fliken **Avancerat** på sidan **skapa lagrings konto** . Du måste aktivera den här inställningen när du skapar kontot. Du kan inte aktivera det senare.

Följande bild visar den här inställningen på sidan **skapa lagrings konto** .

> [!div class="mx-imgBorder"]
> ![Inställning av hierarkiskt namn område](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Om du har ett befintligt lagrings konto som du vill använda med Data Lake Storage och inställningen hierarkiskt namn område är inaktive rad måste du migrera data till ett nytt lagrings konto där inställningen är aktive rad.

> [!NOTE]
> **Data skydd** och **hierarkiskt namn område** kan inte aktive ras samtidigt.

## <a name="next-steps"></a>Nästa steg

- [Översikt över lagringskonto](../common/storage-account-overview.md)
- [Använda Azure Data Lake Storage Gen2 för Big data-krav](data-lake-storage-data-scenarios.md)
- [Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
