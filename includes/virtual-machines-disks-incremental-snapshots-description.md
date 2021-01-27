---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39750a86ccf781a10109e299e27a55a03173acb6
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901213"
---
Stegvisa ögonblicks bilder är tidpunkter för säkerhets kopiering av hanterade diskar som, när de tas, bara består av ändringarna sedan den senaste ögonblicks bilden. När du återställer en disk från en stegvis ögonblicks bild, konstruerar systemet om den fullständiga disken som representerar tidpunkten för säkerhets kopiering av disken när den stegvisa ögonblicks bilden togs. Den här nya funktionen för ögonblicks bilder av hanterade diskar kan eventuellt vara mer kostnads effektivt, eftersom du inte behöver lagra hela disken med varje enskild ögonblicks bild, om du inte väljer att göra det. Precis som med fullständiga ögonblicks bilder kan stegvisa ögonblicks bilder användas för att antingen skapa en fullständig hanterad disk eller en fullständig ögonblicks bild.

Det finns några skillnader mellan en stegvis ögonblicks bild och en fullständig ögonblicks bild. Stegvisa ögonblicks bilder använder alltid standard lagring av hård diskar, oavsett diskens lagrings typ, medan fullständiga ögonblicks bilder kan använda Premium-SSD. Om du använder fullständiga ögonblicks bilder på Premium Storage för att skala upp VM-distributioner rekommenderar vi att du använder anpassade avbildningar på standard lagring i det [delade avbildnings galleriet](../articles/virtual-machines/shared-image-galleries.md). Det hjälper dig att få en mer enorm skala med lägre kostnad. Dessutom kan stegvisa ögonblicks bilder erbjuda bättre tillförlitlighet med [zon redundant lagring](../articles/storage/common/storage-redundancy.md) (ZRS). Om ZRS är tillgängligt i den valda regionen kommer en stegvis ögonblicks bild att använda ZRS automatiskt. Om ZRS inte är tillgängligt i regionen kommer ögonblicks bilden att vara [Lokalt Redundant lagring](../articles/storage/common/storage-redundancy.md) (LRS) som standard. Du kan åsidosätta det här beteendet och välja ett manuellt, men vi rekommenderar inte detta.

Stegvisa ögonblicks bilder erbjuder också en differentiell funktion som bara är tillgänglig för hanterade diskar. De gör att du kan hämta ändringarna mellan två stegvisa ögonblicks bilder av samma hanterade diskar, ned till block nivån. Du kan använda den här funktionen för att minska dina data när du kopierar ögonblicks bilder över flera regioner.  Du kan till exempel hämta den första stegvisa ögonblicks bilden som en bas-BLOB i en annan region. För efterföljande stegvisa ögonblicks bilder kan du bara kopiera ändringarna sedan den senaste ögonblicks bilden till bas-bloben. När du har kopierat ändringarna kan du ta ögonblicks bilder av bas-blobben som representerar din tidpunkt för säkerhets kopiering av disken i en annan region. Du kan återställa disken antingen från bas-blobben eller från en ögonblicks bild på bas-bloben i en annan region.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagram som illustrerar stegvisa ögonblicks bilder som kopieras mellan regioner. Ögonblicks bilder gör olika API-anrop tills du slutligen skapar sid blobbar per ögonblicks bild.":::

Stegvisa ögonblicks bilder debiteras endast för den använda storleken. Du kan hitta den använda storleken på dina ögonblicks bilder genom att titta på [Azures användnings rapport](../articles/cost-management-billing/understand/review-individual-bill.md). Om den använda data storleken för en ögonblicks bild till exempel är 10 GiB, visar rapporten **daglig** användning 10 GIB/(31 dagar) = 0,3226 som förbrukad kvantitet.