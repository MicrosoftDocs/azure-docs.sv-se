---
title: Krypterings omfång för Blob Storage (för hands version)
description: Krypterings omfång ger möjlighet att hantera kryptering på nivån för behållaren eller en enskild blob. Du kan använda krypterings omfång för att skapa säkra gränser mellan data som finns i samma lagrings konto men som tillhör olika kunder.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 35395a30f7d58b9edb3aa7622a35e8c4a62dc76f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211370"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Krypterings omfång för Blob Storage (för hands version)

Krypterings omfång ger möjlighet att hantera kryptering på nivån för behållaren eller en enskild blob. Du kan använda krypterings omfång för att skapa säkra gränser mellan data som finns i samma lagrings konto men som tillhör olika kunder.

Som standard krypteras ett lagrings konto med en nyckel som är begränsad till hela lagrings kontot. Med en krypterings omfattning kan du ange att en eller flera behållare krypteras med en nyckel som endast är begränsad till dessa behållare.

Du kan välja att använda antingen Microsoft-hanterade nycklar eller Kundhanterade nycklar som lagras i Azure Key Vault för att skydda och kontrol lera åtkomsten till nyckeln som krypterar dina data. Olika krypterings omfång på samma lagrings konto kan använda antingen Microsoft-hanterade eller kund hanterade nycklar.

När du har skapat ett krypterings omfång kan du ange att krypterings omfång på en begäran om att skapa en behållare eller en blob. Mer information om hur du skapar en krypterings omfattning finns i [skapa och hantera krypterings omfattningar (för hands version)](encryption-scope-manage.md).

> [!IMPORTANT]
> Krypterings omfång är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.
>
> Undvik oväntade kostnader genom att inaktivera eventuella krypterings omfattningar som du inte behöver.
>
> Krypterings omfång stöds inte med Read-Access Geo-redundant lagring (RA-GRS) eller GZRS-konton (Read-Access geo-Zone-redundant Storage) under för hands versionen.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Skapa en behållare eller BLOB med en krypterings omfattning

Blobbar som skapas under en krypterings omfattning krypteras med den nyckel som angetts för det aktuella omfånget. Du kan ange ett krypterings omfång för en enskild BLOB när du skapar blobben, eller så kan du ange en standard krypterings omfattning när du skapar en behållare. När en standard krypterings omfattning anges på nivån för en behållare krypteras alla blobbar i behållaren med den nyckel som är kopplad till standard omfånget.

När du skapar en BLOB i en behållare som har ett standard krypterings omfång kan du ange en krypterings omfattning som åsidosätter standard krypterings omfånget om behållaren har kon figurer ATS för att tillåta åsidosättningar av standard krypterings omfattningen. För att förhindra åsidosättningar av standard krypterings omfånget konfigurerar du behållaren för att neka åsidosättningar för en enskild blob.

Läs åtgärder på en blob som tillhör ett krypterings område sker transparent, så länge som krypterings omfånget inte är inaktiverat.

## <a name="disable-an-encryption-scope"></a>Inaktivera ett krypterings omfång

När du inaktiverar en krypterings omfattning kommer eventuella efterföljande Läs-eller Skriv åtgärder som görs med krypterings omfånget att Miss Missing med HTTP-felkoden 403 (tillåts inte). Om du aktiverar krypterings omfånget igen, kommer Läs-och skriv åtgärder att fortsätta normalt igen.

När en krypterings omfattning är inaktive rad debiteras du inte längre. Inaktivera eventuella krypterings områden som inte behövs för att undvika onödiga kostnader.

Om krypterings omfattningen är skyddad med Kundhanterade nycklar kan du också ta bort den tillhör ande nyckeln i nyckel valvet för att inaktivera krypterings omfånget. Tänk på att Kundhanterade nycklar skyddas av mjuk borttagnings-och rensnings skydd i nyckel valvet, och en borttagen nyckel omfattas av det beteende som definierats för dessa egenskaper. Mer information finns i något av följande avsnitt i Azure Key Vault-dokumentationen:

- [Använda mjuk borttagning med PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Använda mjuk borttagning med CLI](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Det går inte att ta bort en krypterings omfattning.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](../common/storage-service-encryption.md)
- [Skapa och hantera krypterings omfång (förhands granskning)](encryption-scope-manage.md)
- [Kundhanterade nycklar för Azure Storage kryptering](../common/customer-managed-keys-overview.md)
- [Vad är Azure Key Vault?](../../key-vault/general/overview.md)