---
title: azcopy login | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4861749d66e466bc3302553af8b3ed4919a72e0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503243"
---
# <a name="azcopy-login"></a>azcopy inloggning

Loggar in på Azure Active Directory åtkomst till Azure Storage resurser.

## <a name="synopsis"></a>Synopsis

Logga in på Azure Active Directory för att komma Azure Storage resurser.

För att kunna auktorisera till ditt Azure Storage-konto måste du tilldela rollen **Storage Blob Data-deltagare** till ditt användarkonto i kontexten för lagringskontot, den överordnade resursgruppen eller den överordnade prenumerationen.

Det här kommandot cachelagrar krypterad inloggningsinformation för den aktuella användaren med hjälp av de inbyggda mekanismerna i operativsystemet.

> [!IMPORTANT]
> Om du anger en miljövariabel med hjälp av kommandoraden kan variabeln läsas i kommandoradshistoriken. Överväg att rensa variabler som innehåller autentiseringsuppgifter från kommandoradshistoriken. Om du inte vill att variabler ska visas i historiken kan du använda ett skript för att uppmana användaren att ange sina autentiseringsuppgifter och för att ange miljövariabeln.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="examples"></a>Exempel

Logga in interaktivt med standard-AAD-klientorganisations-ID inställt på vanligt:

```azcopy
azcopy login
```

Logga in interaktivt med ett angivet klientorganisations-ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Logga in med hjälp av den system tilldelade identiteten för en virtuell dator (VM):

```azcopy
azcopy login --identity
```

Logga in med den användar tilldelade identiteten för en virtuell dator och ett klient-ID för tjänstidentiteten:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Logga in med hjälp av den användar tilldelade identiteten för en virtuell dator och ett objekt-ID för tjänstidentiteten:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Logga in med hjälp av den användar tilldelade identiteten för en virtuell dator och ett resurs-ID för tjänstidentiteten:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Logga in som ett huvudnamn för tjänsten med hjälp av en klienthemlighet: Ange miljövariabeln som AZCOPY_SPA_CLIENT_SECRET till klienthemligheten för autentisering med hemligt huvudnamn för tjänsten.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Logga in som ett huvudnamn för tjänsten med hjälp av ett certifikat och dess lösenord:

Ange miljövariabeln AZCOPY_SPA_CERT_PASSWORD certifikatets lösenord för certifikatbaserad autentisering av tjänstens huvudnamn:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Behandla `/path/to/my/cert` som en sökväg till en PEM- eller PKCS12-fil. AzCopy når inte systemcertifikatarkivet för att hämta certifikatet.

`--certificate-path` är obligatoriskt när du utför certifikatbaserad autentisering av tjänstens huvudnamn.

## <a name="options"></a>Alternativ

**--aad-endpoint string** Den Azure Active Directory slutpunkt som ska användas. Standardvärdet ( https://login.microsoftonline.com) är korrekt för det globala Azure-molnet. Ange den här parametern vid autentisering i ett nationellt moln. Krävs inte för hanterad tjänstidentitet.

**--application-id string** Program-ID för användar-tilldelad identitet. Krävs för autentisering av tjänstens huvudnamn.

**--certificate-path string** Sökväg till certifikat för SPN-autentisering. Krävs för certifikatbaserad autentisering av tjänstens huvudnamn.

**--help**   help for the `azcopy login` command.

**--identity**   Logga in med den virtuella datorns identitet, även kallat hanterad tjänstidentitet (MSI).

**--identity-client-id string** Klient-ID för användar-tilldelad identitet.

**--identity-object-id string** Object ID of user-assigned identity.

**--identity-resource-id string** Resource ID of user-assigned identity.

**--service-principal**   Logga in via tjänstens huvudnamn (SPN) med hjälp av ett certifikat eller en hemlighet. Klienthemligheten eller certifikatlösenordet måste placeras i lämplig miljövariabel. Skriv AzCopy env för att se namn och beskrivningar av miljövariabler.

**--tenant-id string** Den här Azure Active Directory klientorganisations-ID som ska användas för interaktiv inloggning för OAuth-enhet.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps float|Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.|
|--output-type string|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|
|--trusted-microsoft-suffixes string   |Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net.* core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som visas här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
