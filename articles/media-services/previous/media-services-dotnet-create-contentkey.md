---
title: Skapa ContentKeys med .NET
description: Den här artikeln visar hur du skapar innehålls nycklar med hjälp av .NET. Dessa nycklar ger säker åtkomst till till gångar.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 05bf928490e94f43b755e1958213899e9e1e98e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014187"
---
# <a name="create-contentkeys-with-net"></a>Skapa ContentKeys med .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Med Media Services kan du skapa och leverera krypterade till gångar. En **ContentKey** ger säker åtkomst till din **till gångs** användare. 

När du skapar en ny till gång (till exempel innan du [överför filer](media-services-dotnet-upload-files.md)) kan du ange följande krypterings alternativ: **StorageEncrypted**, **CommonEncryptionProtected** eller **EnvelopeEncryptionProtected**. 

När du levererar till gångar till dina klienter kan du [Konfigurera för att till gångar dynamiskt ska krypteras](media-services-dotnet-configure-asset-delivery-policy.md) med något av följande två krypteringar: **DynamicEnvelopeEncryption** eller **DynamicCommonEncryption**.

Krypterade till gångar måste kopplas till **ContentKey** s. Den här artikeln beskriver hur du skapar en innehålls nyckel.

> [!NOTE]
> När du skapar en ny **StorageEncrypted** -till gång med hjälp av Media Services .NET SDK skapas **ContentKey** automatiskt och länkas till till gången.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Ett av de värden som du måste ange när du skapar en innehålls nyckel är typen av innehålls nyckel. Välj något av följande värden. 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Skapa kuvert typen ContentKey
Följande kodfragment skapar en innehålls nyckel för typ av kuvert kryptering. Sedan associeras nyckeln med den angivna till gången.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Skapa ContentKey av vanlig typ
Följande kodfragment skapar en innehålls nyckel för den gemensamma krypterings typen. Sedan associeras nyckeln med den angivna till gången.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

