---
title: AzCopy-inloggning | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878366"
---
# <a name="azcopy-login"></a>azcopy inloggning

Loggar in på Azure Active Directory för att få åtkomst till Azure Storage resurser.

## <a name="synopsis"></a>Sammanfattning

Logga in på Azure Active Directory för att få åtkomst till Azure Storage resurser.

Om du vill ha behörighet till ditt Azure Storage-konto måste du tilldela rollen **Storage BLOB data Contributor** till ditt användar konto i kontexten för antingen lagrings kontot, den överordnade resurs gruppen eller den överordnade prenumerationen.

Det här kommandot cachelagrar krypterad inloggnings information för den aktuella användaren med hjälp av inbyggda OS-mekanismer.

> [!IMPORTANT]
> Om du anger en miljö variabel med hjälp av kommando raden, kommer den variabeln att läsas i kommando rads historiken. Överväg att ta bort variabler som innehåller autentiseringsuppgifter från din kommando rads historik. Om du vill att variablerna ska visas i historiken kan du använda ett skript för att uppmana användaren att ange sina autentiseringsuppgifter och ange miljövariabeln.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

Logga in interaktivt med standard-ID för AAD-klient har angetts till common:

```azcopy
azcopy login
```

Logga in interaktivt med ett angivet klient-ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Logga in med den systemtilldelade identiteten för en virtuell dator (VM):

```azcopy
azcopy login --identity
```

Logga in med den användardefinierade identiteten för en virtuell dator och klient-ID: t för tjänstens identitet:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Logga in med den användardefinierade identiteten för en virtuell dator och ett objekt-ID för tjänst identiteten:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Logga in med den användardefinierade identiteten för en virtuell dator och resurs-ID: t för tjänstens identitet:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Logga in som tjänstens huvud namn genom att använda en klient hemlighet: ange miljövariabeln AZCOPY_SPA_CLIENT_SECRET till klient hemligheten för den hemliga autentiseringen för tjänstens huvud namn.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Logga in som tjänstens huvud namn genom att använda ett certifikat och dess lösen ord:

Ange miljövariabeln AZCOPY_SPA_CERT_PASSWORD till certifikatets lösen ord för cert-baserad tjänstens huvud namns-auth:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Behandla `/path/to/my/cert` som en sökväg till en PEM-eller PKCS12-fil. AzCopy når inte till system certifikat arkivet för att hämta ditt certifikat.

`--certificate-path` är obligatoriskt när du gör en cert-baserad tjänstens huvud namns-auth.

## <a name="options"></a>Alternativ

**--AAD-slutpunkt-** sträng som Azure Active Directory slut punkten som ska användas. Standardvärdet ( https://login.microsoftonline.com) är korrekt för det globala Azure-molnet. Ange den här parametern vid autentisering i ett nationellt moln. Krävs inte för Hanterad tjänstidentitet.

**--program-ID** sträng program-ID för användar tilldelad identitet. Krävs för tjänstens huvud namns autentisering.

**--** sökväg till certifikat Sök väg till certifikat för SPN-autentisering. Krävs för certifikatbaserad tjänstens huvud namns-auth.

**--Hjälp**   för `azcopy login` kommandot.

**--identitet**   Logga in med den virtuella datorns identitet, även kallat hanterad tjänst identitet (MSI).

**--Identity-Client-ID** sträng klient-ID för användar tilldelad identitet.

**--identitet-objekt-ID** sträng objekt-ID för användardefinierad identitet.

**--identitet-resurs-ID** sträng resurs-ID för användar tilldelad identitet.

**--tjänst-huvud konto**   Logga in via tjänstens huvud namn (SPN) genom att använda ett certifikat eller en hemlighet. Klient hemligheten eller certifikat lösen ordet måste placeras i lämplig miljö variabel. Skriv AzCopy-kuvert för att se namn och beskrivningar för miljövariabler.

**--ID för klient organisation** Azure Active Directory klient-ID som ska användas för interaktiv inloggning i OAuth-enhet.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s Float|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|
|--sträng för betrodd-Microsoft-suffix   |Anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
