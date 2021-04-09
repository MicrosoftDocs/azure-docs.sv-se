---
title: Azure Portal-begränsa import/export-åtkomst till hanterade diskar med privata länkar
description: Aktivera privata Länkar för dina hanterade diskar med Azure Portal. Gör det möjligt att exportera och importera diskar på ett säkert sätt i det virtuella nätverket.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b80100216003e91fde54b5e555bafb755c942810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682927"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Använd Azure Portal för att begränsa import/export-åtkomst för hanterade diskar med privata länkar

Med stöd för privata Länkar för hanterade diskar kan du begränsa exporten och importen av hanterade diskar så att det bara sker i det virtuella Azure-nätverket. Du kan skapa en tidsbunden URL för signatur för delad åtkomst (SAS) för ej anslutna hanterade diskar och ögonblicks bilder för att exportera data till annan region för regional expansion, haveri beredskap och läsa data för kriminal tekniska-analys. Du kan också använda SAS-URI: n för att ladda upp den virtuella hård disken direkt till en tom disk från din lokala plats. Nätverks trafik mellan klienter på sina virtuella nätverk och hanterade diskar passerar bara över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponeringen för det offentliga Internet.

Du kan skapa en disk åtkomst resurs och länka den till ditt virtuella nätverk i samma prenumeration genom att skapa en privat slut punkt. Du måste associera en disk eller en ögonblicks bild med disk åtkomst för att exportera och importera data via privata länkar. Du måste också ange egenskapen NetworkAccessPolicy för disken eller ögonblicks bilden till `AllowPrivate` . 

Du kan ställa in egenskapen NetworkAccessPolicy på `DenyAll` för att förhindra att vem genererar SAS-URI: n för en disk eller en ögonblicks bild. Standardvärdet för egenskapen NetworkAccessPolicy är `AllowAll` .

## <a name="limitations"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Skapa en disk åtkomst resurs

1. Logga in på Azure Portal och navigera till **disk åtkomst** med [den här länken](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > Du måste använda den [angivna länken](https://aka.ms/disksprivatelinks) för att navigera till bladet disk åtkomst. Den är för närvarande inte synlig i den offentliga portalen utan att använda länken.

1. Välj **+ Lägg** till för att skapa en ny disk åtkomst resurs.
1. På bladet skapa väljer du din prenumeration, en resurs grupp, anger ett namn och väljer en region.
1. Välj **Granska + skapa**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Skärm bild av bladet för att skapa disk åtkomst. Fyll i önskat namn, Välj en region, Välj en resurs grupp och fortsätt":::

När din resurs har skapats går du direkt till den.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Skärm bild av knappen gå till resurs i portalen":::

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

Nu när du har en disk åtkomst resurs kan du använda den för att hantera åtkomst till din disks export/import, detta görs via privata slut punkter. Därför måste du skapa en privat slut punkt och konfigurera den för disk åtkomst.

1. Välj **anslutningar för privata slut punkter** från disk åtkomst resursen.
1. Välj **+ privat slut punkt**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Skärm bild av översikts bladet för din disk åtkomst resurs. Anslutningar för privata slut punkter är markerade.":::

1. Välj en resursgrupp
1. Fyll i namnet och välj samma region som din disk åtkomst resurs skapades i.
1. Välj **Nästa: resurs >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Skärm bild av arbets flödet för skapande av privat slut punkt, första bladet. Om du inte väljer rätt region kan du stöta på problem senare.":::

1. På **resurs** bladet väljer du **Anslut till en Azure-resurs i min katalog**.
1. För **resurs typ** väljer du **Microsoft. Compute/diskAccesses**
1. För **resurs** väljer du den disk åtkomst resurs du skapade tidigare
1. Lämna **mål under resursen** som **diskar**
1. Välj **Nästa: konfigurations >**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Skärm bild av arbets flödet för skapande av privat slut punkt, andra bladet. Med alla markerade värden (resurs typ, resurs, mål under resurs)":::

1. Välj det virtuella nätverk som du vill begränsa disk export till, och andra virtuella nätverk kommer inte att kunna exportera disken.

    > [!NOTE]
    > Om du har en nätverks säkerhets grupp (redigera) aktive rad för det valda under nätet kommer den endast att inaktive ras för privata slut punkter i det här under nätet. Andra resurser i det här under nätet kommer fortfarande att ha NSG-tvång.

1. Välj lämpligt undernät
1. Välj **Granska + skapa**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Skärm bild av arbets flödet för skapande av privat slut punkt, tredje bladet. Det virtuella nätverket och under nätet har framhävts.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Aktivera privat slut punkt på disken

1. Navigera till den disk som du vill konfigurera
1. Välj **nätverk**
1. Välj **privat slut punkt (via disk åtkomst)** och välj disk åtkomst som du skapade tidigare.
1. Välj **Spara**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Skärm bild av bladet Managed disk Networking. Markera den privata slut punkts valet och den valda disk åtkomsten. När du sparar det här alternativet konfigureras disken för den här åtkomsten.":::

Nu har du slutfört konfigurationen av privata länkar som du kan använda när du importerar/exporterar den hanterade disken.

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om privata länkar](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportera/kopiera hanterade ögonblicksbilder som VHD till ett lagringskonto i en annan region med PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)