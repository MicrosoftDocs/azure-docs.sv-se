---
title: Kryptera ditt innehåll med lagrings kryptering med AMS REST API
description: 'Lär dig hur du krypterar ditt innehåll med lagrings kryptering med hjälp av AMS REST API: er.'
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 885390d9246937247107128114e9242aa5e2dc01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015479"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Kryptera ditt innehåll med lagrings kryptering

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).   > inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Den här artikeln ger en översikt över AMS Storage Encryption och visar hur du överför det krypterade lagrings innehållet:

* Skapa en innehållsnyckel.
* Skapa en till gång. Ange AssetCreationOption till StorageEncryption när du skapar till gången.
  
     Krypterade till gångar är associerade med innehålls nycklar.
* Länka innehålls nyckeln till till gången.  
* Ange de krypterings bara parametrarna för AssetFile-entiteter.

## <a name="considerations"></a>Överväganden 

Om du vill leverera en lagrings krypterad till gång måste du konfigurera till gångens leverans princip. Innan din till gång kan strömmas tar streaming-servern bort lagrings krypteringen och strömmar ditt innehåll med den angivna leverans principen. Mer information finns i [Konfigurera till gångs leverans principer](media-services-rest-configure-asset-delivery-policy.md).

När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Kryptering på lagrings Sidan

|Krypterings alternativ|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services lagrings kryptering|AES-256-kryptering, nyckel som hanteras av Media Services|Stöds<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Kryptering för lagringstjänst för vilande data](../../storage/common/storage-service-encryption.md)|Kryptering på Server sidan som erbjuds av Azure Storage, nyckel som hanteras av Azure eller av kunden|Stöds|Stöds|
|[Kryptering av lagrings Client-Side](../../storage/common/storage-client-side-encryption.md)|Kryptering på klient sidan som erbjuds av Azure Storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|Stöds inte|

<sup>1</sup> medan Media Services stöder hantering av innehåll i Clear/utan någon form av kryptering. Detta rekommenderas inte.

<sup>2</sup> i Media Services v3 stöds inte lagrings kryptering (AES-256-kryptering) för bakåtkompatibilitet när dina till gångar skapades med Media Services v2. Det innebär att v3 fungerar med befintliga lagrings krypterade till gångar men tillåter inte att nya skapas.

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API: et finns i [komma åt Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Översikt över lagrings kryptering
Kryptering av AMS-lagring använder **AES-** netmode-kryptering för hela filen.  AES-netmode är ett block-chiffer som kan kryptera godtycklig längd data utan att behöva utfyllnad. Det fungerar genom att kryptera ett Räknare-block med AES-algoritmen och sedan använda XOR för att använda AES-utdata med data som ska krypteras eller dekrypteras.  Det använda räknar blocket konstrueras genom att värdet för InitializationVector ändras till byte 0 till 7 för räknarvärdet och byte 8 till 15 för räknarvärdet har värdet noll. Av räknaren 16 byte, byte 8 till 15 (det vill säga minst signifikanta byte) används som ett enkelt 64-bitars heltal som ökas med ett för varje efterföljande block med data som bearbetas och lagras i byte-ordningen för nätverket. Om det här heltalet når det maximala värdet (0xFFFFFFFFFFFFFFFF), återställer den block räknaren till noll (byte 8 till 15) utan att påverka de andra 64-bitarna i räknaren (det vill säga byte 0 till 7).   InitializationVector-värdet för en viss nyckel identifierare för varje innehålls nyckel måste vara unikt för varje fil och filer får innehålla färre än 2 ^ 64 block i stället för att det ska gå att upprätthålla säkerheten för kryptering med AES-polyläge.  Det unika värdet är att se till att ett räknar värde aldrig återanvänds med en specifik nyckel. Mer information om produktions läget finns på [den här wiki-sidan](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (wiki-artikeln använder termen "nonce" i stället för "InitializationVector").

Använd **lagrings kryptering** för att kryptera ditt tydliga innehåll lokalt med AES-256-bitars kryptering och ladda sedan upp det till Azure Storage där det lagras krypterade i vila. Till gångar som skyddas med lagrings kryptering krypteras automatiskt och placeras i ett krypterat fil system före kodning, och alternativt omkrypteras innan de överförs igen som en ny till gång. Det primära användnings fallet för lagrings kryptering är när du vill skydda dina mediefiler med hög kvalitet med stark kryptering i vila på disk.

För att kunna leverera en lagrings krypterad till gång måste du konfigurera till gångens leverans princip så Media Services vet hur du vill leverera ditt innehåll. Innan din till gång kan strömmas tar streaming-servern bort lagrings krypteringen och strömmar ditt innehåll med den angivna leverans principen (till exempel AES, common Encryption eller ingen kryptering).

## <a name="create-contentkeys-used-for-encryption"></a>Skapa ContentKeys som används för kryptering
Krypterade till gångar är associerade med lagrings krypterings nycklar. Skapa innehålls nyckeln som ska användas för kryptering innan du skapar till gångs filen. I det här avsnittet beskrivs hur du skapar en innehålls nyckel.

Följande är allmänna steg för att skapa innehålls nycklar som du associerar med till gångar som du vill ska krypteras. 

1. För lagrings kryptering ska du slumpmässigt generera en AES-nyckel på 32 byte. 
   
    AES-nyckeln 32-byte är innehålls nyckeln för din till gång, vilket innebär att alla filer som är kopplade till till gången måste använda samma innehålls nyckel under dekrypteringen. 
2. Anropa metoderna [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) och [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) för att hämta rätt X. 509-certifikat som måste användas för att kryptera din innehålls nyckel.
3. Kryptera din innehålls nyckel med den offentliga nyckeln för X. 509-certifikatet. 
   
   Media Services .NET SDK använder RSA med OAEP när krypteringen utförs.  Du kan se ett .NET-exempel i [EncryptSymmetricKeyData-funktionen](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Skapa ett värde för kontroll summa som beräknas med nyckel identifieraren och innehålls nyckeln. I följande .NET-exempel beräknas kontroll summan med hjälp av GUID-delen av nyckel identifieraren och rensa innehålls nyckeln.

    ```csharp
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
    {
        const int ChecksumLength = 8;
        const int KeyIdLength = 16;

        byte[] encryptedKeyId = null;

        // Checksum is computed by AES-ECB encrypting the KID
        // with the content key.
        using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
        {
            rijndael.Mode = CipherMode.ECB;
            rijndael.Key = contentKey;
            rijndael.Padding = PaddingMode.None;

            ICryptoTransform encryptor = rijndael.CreateEncryptor();
            encryptedKeyId = new byte[KeyIdLength];
            encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
        }

        byte[] retVal = new byte[ChecksumLength];
        Array.Copy(encryptedKeyId, retVal, ChecksumLength);

        return Convert.ToBase64String(retVal);
    }
    ```

5. Skapa innehålls nyckeln med **EncryptedContentKey** (konverterad till Base64-kodad sträng), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** och **kontroll Summa** som du har tagit emot i föregående steg.

    För lagrings kryptering ska följande egenskaper tas med i begär ande texten.

    Egenskap för begär ande brödtext    | Beskrivning
    ---|---
    Id | ContentKey-ID: t genereras med följande format: "OBS: barn: UUID: \<NEW GUID> ".
    ContentKeyType | Innehålls nyckel typen är ett heltal som definierar nyckeln. För lagrings krypterings format är värdet 1.
    EncryptedContentKey | Vi skapar ett nytt nyckel värde för innehåll som är 256-bitars (32 byte)-värde. Nyckeln krypteras med hjälp av det lagrings krypterings-X. 509-certifikat som vi hämtar från Microsoft Azure Media Services genom att köra en HTTP GET-begäran för GetProtectionKeyId-och GetProtectionKey-metoderna. Exempel finns i följande .NET-kod:  **EncryptSymmetricKeyData** -metoden som definieras [här](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Detta är skydds nyckel-ID: t för det lagrings krypterings-X. 509-certifikat som användes för att kryptera vår innehålls nyckel.
    ProtectionKeyType | Detta är krypterings typen för den skydds nyckel som användes för att kryptera innehålls nyckeln. Det här värdet är StorageEncryption (1) för vårt exempel.
    Kontrollsumma |Den beräknade MD5-kontroll summan för innehålls nyckeln. Den beräknas genom att du krypterar innehålls-ID: t med innehålls nyckeln. Exempel koden visar hur du beräknar kontroll summan.


### <a name="retrieve-the-protectionkeyid"></a>Hämta ProtectionKeyId
I följande exempel visas hur du hämtar ProtectionKeyId, ett tumavtryck för certifikatet, för det certifikat som du måste använda när du krypterar din innehålls nyckel. Gör det här steget för att kontrol lera att du redan har rätt certifikat på din dator.

Begäran:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
```

Svar:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 139
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Hämta ProtectionKey för ProtectionKeyId
I följande exempel visas hur du hämtar X. 509-certifikatet med hjälp av ProtectionKeyId som du fick i föregående steg.

Begäran:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```

Svar:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1227
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Thu, 05 Feb 2015 07:52:30 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

### <a name="create-the-content-key"></a>Skapa innehålls nyckeln
När du har hämtat X. 509-certifikatet och använt dess offentliga nyckel för att kryptera din innehålls nyckel, skapar du en **ContentKey** -entitet och anger dess egenskaps värden.

Ett av värdena som du måste ange när du skapar innehålls nyckeln är typen. När du använder lagrings kryptering ska värdet vara "1". 

I följande exempel visas hur du skapar en **ContentKey** med en **ContentKeyType** som är inställd för lagrings kryptering ("1") och **ProtectionKeyType** har angetts till "0" för att INDIKERA att skydds nyckelns ID är X. 509-certifikatets tumavtryck.  

Förfrågan

```console
POST https://media.windows.net/api/ContentKeys HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
{
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
"ContentKeyType":"1", 
"ProtectionKeyType":"0",
"EncryptedContentKey":"your encrypted content key",
"Checksum":"calculated checksum"
}
```

Svar:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 777
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
Server: Microsoft-IIS/8.5
request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:37:46 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
"LastModified":"2015-02-04T02:37:46.9684379Z",
"ContentKeyType":1,
"EncryptedContentKey":"your encrypted content key",
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
"ProtectionKeyType":0,
"Checksum":"calculated checksum"}
```

## <a name="create-an-asset"></a>Skapa en tillgång
I följande exempel visas hur du skapar en till gång.

**HTTP-begäran**

```console
POST https://media.windows.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"Name":"BigBuckBunny" "Options":1}
```

**HTTP-svar**

Om det lyckas returneras följande svar:

```console
HTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT
{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny.mp4",
   "Options":1,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

## <a name="associate-the-contentkey-with-an-asset"></a>Koppla ContentKey till en till gång
När du har skapat ContentKey associerar du den med din till gång genom att använda $links åtgärden, som du ser i följande exempel:

Begäran:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}
```

Svar:

```console
HTTP/1.1 204 No Content 
```

## <a name="create-an-assetfile"></a>Skapa en AssetFile
Entiteten [AssetFile](/rest/api/media/operations/assetfile) representerar en video-eller ljudfil som lagras i en BLOB-behållare. En till gångs fil är alltid kopplad till en till gång och en till gång kan innehålla en eller flera till gångs filer. Media Services Encoder-aktiviteten Miss lyckas om ett till gångs fil objekt inte är associerat med en digital fil i en BLOB-behållare.

**AssetFile** -instansen och den faktiska medie filen är två distinkta objekt. AssetFile-instansen innehåller metadata om medie filen, medan medie filen innehåller det faktiska medie innehållet.

När du har överfört den digitala medie filen till en BLOB-behållare använder du kommandot **slå samman** http för att uppdatera AssetFile med information om medie filen (visas inte i den här artikeln). 

**HTTP-begäran**

```console
POST https://media.windows.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
Content-Length: 164

{  
   "IsEncrypted":"true",
   "EncryptionScheme" : "StorageEncryption", 
   "EncryptionVersion" : "1.0",       
   "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector" : "397304628502661816</d:InitializationVector",
   "Options":0,
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP-svar**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion": "1.0",
   "EncryptionScheme": "StorageEncryption",
   "IsEncrypted":true,
   "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector":"397304628502661816</d:InitializationVector",
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```
