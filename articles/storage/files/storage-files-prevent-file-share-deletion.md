---
title: Förhindra oavsiktlig borttagning – Azure-filresurser
description: Lär dig mer om mjuk borttagning för Azure-filresurser och hur du kan använda det för att återställa data och förhindra oavsiktlig borttagning.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 0fecc9fc954a1ac648e8f60badf69ad1d2e8f1cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93126948"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Förhindra oavsiktlig borttagning av Azure-filresurser

Azure Storage har nu mjuk borttagning för fil resurser. Med mjuk borttagning kan du återställa fil resursen när den tas bort av misstag av ett program eller någon annan lagrings konto användare.

## <a name="how-soft-delete-works"></a>Så här fungerar mjuk borttagning

När mjuk borttagning för Azure-filresurser är aktiverat, om en fil resurs tas bort, övergår den över till ett mjukt borttaget tillstånd i stället för att raderas permanent. Du kan konfigurera hur lång tid det tar för mjuka borttagna data att återställas innan den tas bort permanent och ta bort resursen när den kvarhålls. När det har tagits bort återställs delningen och allt innehåll, inklusive ögonblicks bilder, till det tillstånd som den var i innan borttagning. Mjuk borttagning fungerar bara på en fil resurs nivå – enskilda filer som tas bort kommer fortfarande att raderas permanent.

Mjuk borttagning kan aktive ras på antingen nya eller befintliga fil resurser. Mjuk borttagning är också bakåtkompatibelt, så du behöver inte göra några ändringar i dina program för att dra nytta av skyddet av mjuk borttagning. 

Om du vill ta bort en fil resurs permanent i läget för mjuk borttagning innan förfallo tiden måste du ta bort resursen, inaktivera mjuk borttagning och sedan ta bort resursen igen. Sedan bör du aktivera mjuk borttagning igen eftersom andra fil resurser på lagrings kontot blir sårbara för oavsiktlig borttagning medan mjuk borttagning är avstängd.

För mjuk borttagning av Premium-filresurser används fil resurs kvoten (den allokerade storleken på en fil resurs) i den totala kvot beräkningen för lagrings kontot tills den förfallna resursen upphör att gälla när resursen har tagits bort helt.

## <a name="configuration-settings"></a>Konfigurationsinställningar

### <a name="enabling-or-disabling-soft-delete"></a>Aktivera eller inaktivera mjuk borttagning

Mjuk borttagning för fil resurser har Aktiver ATS på lagrings konto nivå, på grund av detta gäller inställningarna för mjuk borttagning för alla fil resurser i ett lagrings konto. Du kan aktivera eller inaktivera mjuk borttagning när som helst. När du skapar ett nytt lagrings konto är mjuk borttagning av fil resurser inaktive rad som standard, så du kan aktivera det under distributionen eller när som helst efteråt. Mjuk borttagning är inaktiverat som standard för befintliga lagrings konton. Om du har konfigurerat [Azure-filresursens säkerhets kopiering](../../backup/azure-file-share-backup-overview.md) för en Azure-filresurs, aktive ras mjuk borttagning för Azure-filresurser automatiskt på den resursens lagrings konto.

Om du aktiverar mjuk borttagning för fil resurser tar du bort vissa fil resurser och inaktiverar sedan mjuk borttagning, om resurserna har sparats under den tiden kan du fortfarande komma åt och återställa dessa fil resurser. När du aktiverar mjuk borttagning måste du också konfigurera kvarhållningsperioden.

### <a name="retention-period"></a>Kvarhållningsperiod

Kvarhållningsperioden är den tid som en mjuk borttagen fil resurs lagras och är tillgänglig för återställning. För fil resurser som tas bort uttryckligen startar kvarhållningsperioden för kvarhållningsperioden när data tas bort. För närvarande kan du ange en kvarhållningsperiod mellan 1 och 365 dagar. Du kan när som helst ändra lagrings perioden för mjuk borttagning. En uppdaterad kvarhållningsperiod gäller bara för resurser som tagits bort efter att kvarhållningsperioden har uppdaterats. Resurser som tagits bort innan uppdaterings perioden upphör att gälla baseras på den kvarhållningsperiod som konfigurerades när data togs bort.

## <a name="pricing-and-billing"></a>Priser och fakturering

Både standard-och Premium-filresurser debiteras på den använda kapaciteten när den är mjuk borttagen, i stället för en etablerad kapacitet. Dessutom debiteras Premium-filresurser enligt ögonblicks bilds taxan i läget för mjuk borttagning. Standard fil resurser debiteras enligt normal taxa i läget för mjuk borttagning. Du debiteras inte för data som tas bort permanent efter den konfigurerade kvarhållningsperioden.

Mer information om priser för Azure File Storage i allmänhet finns på sidan med [priser för azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/).

När du först aktiverar mjuk borttagning rekommenderar vi att du använder en liten kvarhållningsperiod för att bättre förstå hur funktionen påverkar din faktura.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du aktiverar och använder mjuk borttagning fortsätter du med att [Aktivera mjuk borttagning](storage-files-enable-soft-delete.md).
