---
title: Krypterings omfång för Blob Storage
description: Krypterings omfång ger möjlighet att hantera kryptering på nivån för behållaren eller en enskild blob. Du kan använda krypterings omfång för att skapa säkra gränser mellan data som finns i samma lagrings konto men som tillhör olika kunder.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640463"
---
# <a name="encryption-scopes-for-blob-storage"></a>Krypterings omfång för Blob Storage

Med krypterings omfång kan du hantera kryptering med en nyckel som är begränsad till en behållare eller en enskild blob. Du kan använda krypterings omfång för att skapa säkra gränser mellan data som finns i samma lagrings konto men som tillhör olika kunder.

Mer information om hur du arbetar med krypterings omfattningar finns i [skapa och hantera krypterings omfattningar](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Så här fungerar krypterings omfång

Som standard krypteras ett lagrings konto med en nyckel som är begränsad till hela lagrings kontot. När du definierar ett krypterings omfång anger du en nyckel som kan begränsas till en behållare eller en enskild blob. När krypterings omfånget tillämpas på en BLOB krypteras blobben med den nyckeln. När krypterings omfånget används för en behållare fungerar det som standard omfånget för blobbar i den behållaren, så att alla blobbar som överförs till behållaren kan krypteras med samma nyckel. Behållaren kan konfigureras för att genomdriva standard krypterings omfånget för alla blobar i behållaren, eller för att tillåta att en enskild BLOB överförs till behållaren med ett annat krypterings omfång än standardvärdet.

Läs åtgärder på en blob som skapats med ett krypterings omfång sker transparent, så länge som krypterings omfånget inte har inaktiverats.

### <a name="key-management"></a>Nyckelhantering

När du definierar en krypterings omfattning kan du ange om omfattningen är skyddad med en Microsoft-hanterad nyckel eller med en kundhanterad nyckel som lagras i Azure Key Vault. Olika krypterings omfång på samma lagrings konto kan använda antingen Microsoft-hanterade eller kund hanterade nycklar. Du kan också byta typ av nyckel som används för att skydda ett krypterings omfång från en kundhanterad nyckel till en Microsoft-hanterad nyckel eller vice versa, när som helst. Mer information om Kundhanterade nycklar finns i [kund hanterade nycklar för Azure Storage kryptering](../common/customer-managed-keys-overview.md). Mer information om Microsoft-hanterade nycklar finns i [om hantering av krypterings nycklar](../common/storage-service-encryption.md#about-encryption-key-management).

Om du definierar en krypterings omfattning med en kundhanterad nyckel kan du välja att uppdatera nyckel versionen antingen automatiskt eller manuellt. Om du väljer att automatiskt uppdatera nyckel versionen kontrollerar Azure Storage nyckel valvet eller hanterad HSM dagligen för en ny version av den Kundhanterade nyckeln och uppdaterar nyckeln automatiskt till den senaste versionen. Mer information om hur du uppdaterar nyckel versionen för en kundhanterad nyckel finns i [Uppdatera nyckel versionen](../common/customer-managed-keys-overview.md#update-the-key-version).

Ett lagrings konto kan ha upp till 10 000 krypterings områden som skyddas med Kundhanterade nycklar som nyckel versionen uppdateras för automatiskt. Om ditt lagrings konto redan har 10 000 krypterings omfång som skyddas med Kundhanterade nycklar som uppdateras automatiskt, måste nyckel versionen uppdateras manuellt för eventuella ytterligare krypterings områden som skyddas med Kundhanterade nycklar.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>Krypterings omfång för behållare och blobbar

När du skapar en behållare kan du ange ett standard krypterings omfång för de blobbar som sedan överförs till den behållaren. När du anger ett standard krypterings omfång för en behållare kan du bestämma hur standard krypterings omfattningen ska tillämpas:

- Du kan kräva att alla blobar som överförts till behållaren använder standard krypterings omfånget. I det här fallet krypteras varje BLOB i behållaren med samma nyckel.
- Du kan tillåta att en klient åsidosätter standard krypterings omfånget för behållaren, så att en BLOB kan överföras med ett annat krypterings omfång än standard omfånget. I det här fallet kan blobarna i behållaren krypteras med olika nycklar.

I följande tabell sammanfattas beteendet för en BLOB upload-åtgärd, beroende på hur standard krypterings omfånget är konfigurerat för behållaren:

| Krypterings omfånget som definierats för behållaren är... | Laddar upp en blob med standard krypterings omfånget... | Laddar upp en blob med en annan krypterings omfattning än standard omfånget... |
|--|--|--|
| Ett standard krypterings omfång med tillåtna åsidosättningar | Lyckas | Lyckas |
| Ett standard krypterings omfång med förbjudna åsidosättningar | Lyckas | Ned |

Ett standard krypterings omfång måste anges för en behållare vid den tidpunkt då behållaren skapas.

Om ingen standard krypterings omfattning har angetts för behållaren kan du ladda upp en blob med hjälp av alla krypterings omfattningar som du har definierat för lagrings kontot. Krypterings omfånget måste anges vid den tidpunkt då blobben laddas upp.

## <a name="disabling-an-encryption-scope"></a>Inaktivera ett krypterings omfång

När du inaktiverar en krypterings omfattning kommer eventuella efterföljande Läs-eller Skriv åtgärder som görs med krypterings omfånget att Miss Missing med HTTP-felkoden 403 (tillåts inte). Om du aktiverar krypterings omfånget igen, kommer Läs-och skriv åtgärder att fortsätta normalt igen.

När en krypterings omfattning är inaktive rad debiteras du inte längre. Inaktivera eventuella krypterings områden som inte behövs för att undvika onödiga kostnader.

Om krypterings omfattningen är skyddad med en kundhanterad nyckel och du tar bort nyckeln i nyckel valvet blir data otillgängliga. Se även till att inaktivera krypterings omfånget för att undvika att debiteras för det.

Tänk på att Kundhanterade nycklar skyddas av mjuk borttagnings-och rensnings skydd i nyckel valvet, och en borttagen nyckel omfattas av det beteende som definierats för dessa egenskaper. Mer information finns i något av följande avsnitt i Azure Key Vault-dokumentationen:

- [Använda mjuk borttagning med PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Använda mjuk borttagning med CLI](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Det går inte att ta bort en krypterings omfattning.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](../common/storage-service-encryption.md)
- [Skapa och hantera krypterings omfång](encryption-scope-manage.md)
- [Kundhanterade nycklar för Azure Storage kryptering](../common/customer-managed-keys-overview.md)
- [Vad är Azure Key Vault?](../../key-vault/general/overview.md)