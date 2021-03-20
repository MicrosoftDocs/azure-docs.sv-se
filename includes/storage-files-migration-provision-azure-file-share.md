---
title: Att tänka på när du konfigurerar Azure-filresurser
description: Etablera Azure-filresurser för användning med Azure File Sync. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d48baba5ee60a2bf5a4cb5e4d1ce840fce8eec43
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82143577"
---
En Azure-filresurs lagras i molnet i ett Azure Storage-konto.
Det finns en annan nivå av prestanda överväganden här.

Om du har mycket aktiva resurser (resurser som används av många användare och/eller program) kan två Azure-filresurser uppnå prestanda gränsen för ett lagrings konto.

Ett bra tips är att distribuera lagrings konton med en enda fil resurs.
Du kan poola flera Azure-filresurser till samma lagrings konto, om du har lagrings resurser eller om du förväntar dig en låg dags-till-dag-aktivitet.

Dessa överväganden gäller mer för direkt åtkomst till molnet (via en virtuell Azure-dator) än för Azure File Sync. Om du endast planerar att använda Azure File Sync på dessa resurser är det bra att gruppera flera i ett enda Azure Storage-konto.

Om du har skapat en lista över dina resurser bör du mappa varje resurs till det lagrings konto som den kommer att finnas i.

I föregående fas fastställde du antalet resurser. I det här steget har du skapat en mappning av lagrings konton till fil resurser. Distribuera nu lämpligt antal Azure Storage-konton med rätt antal Azure-filresurser i dem.

Kontrol lera att regionen för var och en av dina lagrings konton är samma och matchar regionen i den tjänst resurs för lagrings synkronisering som du redan har distribuerat.

> [!CAUTION]
> Om du skapar en Azure-filresurs som har en 100-TiB-gräns kan den resursen endast använda lokalt redundant lagring eller zon-redundanta redundanta lagrings alternativ. Ta hänsyn till dina redundanta lagrings behov innan du använder 100-TiB fil resurser.

Azure-filresurser skapas fortfarande med en gräns på 5 TiB som standard. Eftersom du skapar nya lagrings konton, se till att följa [vägledningen för att skapa lagrings konton som tillåter Azure-filresurser med 100-TIB-gränser](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Ett annat övervägande när du distribuerar ett lagrings konto är redundansen för Azure Storage. Se [Azure Storage alternativ för redundans](../articles/storage/common/storage-redundancy.md).

Namnen på resurserna är också viktiga. Om du till exempel grupperar flera resurser för personalavdelningen till ett Azure Storage-konto bör du ge lagrings kontot ett namn. På samma sätt bör du använda namn som liknar de som används för sina lokala motsvarigheter när du namnger dina Azure-filresurser.