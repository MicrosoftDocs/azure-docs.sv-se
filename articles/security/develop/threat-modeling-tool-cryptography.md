---
title: Kryptografi – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Läs om kryptografiska åtgärder för hot som exponeras i Threat Modeling Tool. Se information om minskning och Visa kod exempel.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 6af4fba8ddf50b795d847a2c7b4e2fbc02fe593f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587187"
---
# <a name="security-frame-cryptography--mitigations"></a>Säkerhets ram: kryptografi | Åtgärder 

| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webb program** | <ul><li>[Använd endast godkända symmetriska block chiffer och nyckel längder](#cipher-length)</li><li>[Använd godkända block krypterings lägen och initierings vektorer för symmetriska chiffer](#vector-ciphers)</li><li>[Använda godkända asymmetriska algoritmer, nyckel längder och utfyllnad](#padding)</li><li>[Använd godkända slumpmässiga nummer generatorer](#numgen)</li><li>[Använd inte symmetriska Stream-chiffer](#stream-ciphers)</li><li>[Använd godkända MAC-/HMAC-/HMAC-hash-algoritmer](#mac-hash)</li><li>[Använd endast godkända kryptografiska hash-funktioner](#hash-functions)</li></ul> |
| **Databas** | <ul><li>[Använd starka krypteringsalgoritmer för att kryptera data i databasen](#strong-db)</li><li>[SSIS-paket ska vara krypterade och digitalt signerade](#ssis-signed)</li><li>[Lägg till digital signatur till en kritisk databas skydd bara objekt](#securables-db)</li><li>[Använd SQL Server EKM för att skydda krypterings nycklar](#ekm-keys)</li><li>[Använd funktionen AlwaysEncrypted om krypterings nycklar inte ska visas för databas motorn](#keys-engine)</li></ul> |
| **IoT-enhet** | <ul><li>[Lagra kryptografiska nycklar säkert på IoT-enheter](#keys-iot)</li></ul> | 
| **IoT Cloud Gateway** | <ul><li>[Generera en slumpmässig symmetrisk nyckel med tillräcklig längd för autentisering till IoT Hub](#random-hub)</li></ul> | 
| **Dynamics CRM Mobile-klient** | <ul><li>[Se till att en princip för enhets hantering är på plats som kräver en PIN-kod för användning och tillåter fjärrrensning](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook-klient** | <ul><li>[Se till att en princip för enhets hantering är på plats som kräver PIN/Password/Auto Lock och krypterar alla data (t. ex. BitLocker)](#bitlocker)</li></ul> | 
| **Identitets Server** | <ul><li>[Se till att signerings nycklar överförs när du använder en identitets Server](#rolled-server)</li><li>[Se till att det kryptografiskt starka klient-ID: t, klient hemligheten används i identitets servern](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Använd endast godkända symmetriska block chiffer och nyckel längder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Produkter får bara använda de symmetriska block chiffer och tillhör ande nyckel längder som uttryckligen har godkänts av kryptografi Advisor i din organisation. Godkända symmetriska algoritmer på Microsoft inkluderar följande block-chiffer:</p><ul><li>För nya code AES-128, AES-192 och AES-256 är acceptabla</li><li>För bakåtkompatibilitet med befintlig kod godkänns tre nycklars-3DES</li><li>För produkter som använder symmetrisk block chiffrering:<ul><li>Advanced Encryption Standard (AES) krävs för ny kod</li><li>Tre nycklar Triple Data Encryption Standard (3DES) är tillåtet i befintlig kod för bakåtkompatibilitet</li><li>Alla andra block-chiffer, inklusive RC2, DES, 2 Key 3DES, DESX och Skipjack, kan bara användas för att dekryptera gamla data och måste ersättas om de används för kryptering</li></ul></li><li>För symmetriska block krypteringsalgoritmer krävs en minsta nyckel längd på 128 bitar. Den enda block krypteringsalgoritm som rekommenderas för ny kod är AES (AES-128, AES-192 och AES-256 är alla acceptabla)</li><li>3DES för tre nycklar är för närvarande acceptabelt om den redan används i befintlig kod. över gång till AES rekommenderas. DES, DESX, RC2 och Skipjack anses inte längre vara säkra. Dessa algoritmer kan endast användas för dekryptering av befintliga data för bakåtkompatibilitet och data bör krypteras på nytt med hjälp av ett rekommenderat block chiffer</li></ul><p>Observera att alla symmetriska block-chiffer måste användas med ett godkänt chiffer, vilket kräver att en lämplig initierings vektor (IV) används. Ett lämpligt IV är vanligt vis ett slumptal och aldrig ett konstant värde</p><p>Användningen av äldre eller icke godkända krypteringsalgoritmer och mindre nyckel längder för att läsa befintliga data (till skillnad från att skriva nya data) kan tillåtas efter att organisationens kryptografi granskats. Du måste dock filen för ett undantag mot detta krav. I företags distributioner bör dessutom produkter överväga varnings administratörer när svag kryptering används för att läsa data. Sådana varningar bör vara för klar ande och åtgärdade. I vissa fall kan det vara lämpligt att grupprincip kontrol lera användningen av svag kryptering</p><p>Tillåtna .NET-algoritmer för smidig hantering av kryptografi (i prioritetsordning)</p><ul><li>AesCng (FIPS-kompatibel)</li><li>AuthenticatedAesCng (FIPS-kompatibel)</li><li>AESCryptoServiceProvider (FIPS-kompatibel)</li><li>AESManaged (icke-FIPS-kompatibel)</li></ul><p>Observera att ingen av dessa algoritmer kan anges via `SymmetricAlgorithm.Create` `CryptoConfig.CreateFromName` metoderna eller utan att göra ändringar i machine.configs filen. Observera också att AES i versioner av .NET före .NET 3,5 heter `RijndaelManaged` , och `AesCng` `AuthenticatedAesCng` är >tillgängliga via CODEPLEX och kräver CNG i det underliggande operativ systemet</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Använd godkända block krypterings lägen och initierings vektorer för symmetriska chiffer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Alla symmetriska block-chiffer måste användas med ett godkänt symmetriskt krypterings läge. De enda godkända lägena är CBC och CTS. I synnerhet bör den elektroniska kod boken (ECB) åtgärds läge undvikas. för användning av ECB krävs organisationens granskning av kryptografi tavlan. All användning av OFB, CFB,, CCM och GCM eller något annat krypterings läge måste granskas av organisationens kryptografi tavla. Att återanvända samma initierings vektor (IV) med block-chiffer i lägen för strömmande chiffer, som t. ex. en grupp, kan göra att krypterade data visas. Alla symmetriska block-chiffer måste också användas med en lämplig initierings vektor (IV). En lämplig IV är en kryptografiskt stark, slumpmässig siffra och aldrig ett konstant värde. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Använda godkända asymmetriska algoritmer, nyckel längder och utfyllnad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Användningen av förbjudna kryptografiska algoritmer medför betydande risk för produkt säkerhet och måste undvikas. Produkter får bara använda de kryptografiska algoritmer och tillhör ande nyckel längd och utfyllnad som uttryckligen har godkänts av organisationens kryptografi tavla.</p><ul><li>**RSA –** kan användas för kryptering, nyckel utbyte och signatur. RSA-kryptering får endast använda OAEP-eller RSA-KEM utfyllnad. Befintlig kod får använda för endast kompatibilitet för PKCS #1 v 1.5-utfyllnad. Användning av null-utfyllnad är uttryckligen förbjuden. Nycklar >= 2048 bitar krävs för ny kod. Befintlig kod kan bara ha stöd för nycklar < 2048 bitar för bakåtkompatibilitet efter en granskning av organisationens kryptografi tavla. Nycklar < 1024 bitar kan bara användas för att dekryptera/verifiera gamla data och måste ersättas om de används för kryptering eller signerings åtgärder</li><li>**ECDSA –** kan endast användas för signatur. ECDSA med >= 256-bitars nycklar krävs för ny kod. ECDSA-baserade signaturer måste använda någon av de tre NIST godkända kurvorna (P-256, P-384 eller P521). Kurvor som har analyser ATS noggrant får bara användas efter en granskning med organisationens kryptografi tavla.</li><li>**ECDH –** kan endast användas för nyckel utbyte. ECDH med >= 256-bitars nycklar krävs för ny kod. ECDH-baserade nyckel utbyte måste använda någon av de tre NIST godkända kurvorna (P-256, P-384 eller P521). Kurvor som har analyser ATS noggrant får bara användas efter en granskning med organisationens kryptografi tavla.</li><li>**DSA –** kan vara acceptabelt efter granskning och godkännande från organisationens kryptografiska tavla. Kontakta din säkerhets rådgivare för att schemalägga granskning av organisationens kryptografi tavla. Om din användning av DSA är godkänd, Observera att du måste förhindra att nycklar som är mindre än 2048 bitar får användas. CNG stöder 2048-bitars och större nyckel längder från och med Windows 8.</li><li>**Diffie-Hellman –** kan endast användas för hantering av sessionsnycklar. Nyckel längden >= 2048 bitar krävs för ny kod. Befintlig kod kan ha stöd för nyckel längder < 2048-bitar endast för bakåtkompatibilitet efter en granskning av organisationens kryptografi tavla. Nycklar < 1024 bitar får inte användas.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Använd godkända slumpmässiga nummer generatorer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Produkterna måste använda godkända slumpmässiga nummer generatorer. Pseudorandom-funktioner som C runtime-funktionen rand, .NET Framework Class system. Random eller system functions, t. ex. GetTickCount, får därför aldrig användas i sådan kod. Det är inte tillåtet att använda den dubbla Elliptic-kurvan slump tals Generator (DUAL_EC_DRBG)</p><ul><li>**CNG-** BCryptGenRandom (Använd av BCRYPT_USE_SYSTEM_PREFERRED_RNG flagga rekommenderas om inte anroparen kan köras på en IRQL som är större än 0 [PASSIVE_LEVEL])</li><li>**Capi-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nya implementeringar bör använda BCryptGenRandom eller CryptGenRandom) * rand_s * SystemPrng (för kernelläge)</li><li>**. NET-** RNGCryptoServiceProvider eller RNGCng</li><li>**Windows Store-appar –** Windows. Security. Cryptography. CryptographicBuffer. GenerateRandom eller. GenerateRandomNumber</li><li>**Apples OS X (10.7 +)/iOS (2.0 +)-** int SecRandomCopyBytes (SecRandomRef slumpmässig, size_t antal, uint8_t \* byte)</li><li>**Apple OS X (<10,7) –** Använd/dev/Random för att hämta slumpmässiga siffror</li><li>**Java (inklusive Google Android Java-kod)-** Java. Security. SecureRandom-klass. Observera att för Android 4,3 (Gelée bönor) måste utvecklare följa den rekommenderade Android-lösningen och uppdatera sina program för att explicit initiera PRNG med entropi från/dev/urandom eller/dev/Random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Använd inte symmetriska Stream-chiffer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Symmetriska Stream-chiffer, till exempel RC4, får inte användas. I stället för symmetriska Stream-chiffer bör produkter använda ett block chiffer, särskilt AES med en nyckel längd på minst 128 bitar. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Använd godkända MAC-/HMAC-/HMAC-hash-algoritmer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Produkterna måste endast använda godkända algoritmer för meddelandeautentisering (MAC) eller hash-baserade algoritmer för meddelande verifiering (HMAC).</p><p>En Message Authentication Code (MAC) är en del av informationen som är kopplad till ett meddelande som gör det möjligt för mottagaren att verifiera både meddelandets äkthet och meddelandets integritet med hjälp av en hemlig nyckel. Användningen av antingen en hash-baserad MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) eller [block-cipher-baserad Mac](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) är tillåten så länge alla underliggande hash-algoritmer eller symmetriska krypteringsalgoritmer också godkänns för användning. för närvarande inkluderar det HMAC-SHA2-funktionerna (HMAC-SHA256, HMAC-SHA384 och HMAC-SHA512) och CMAC/OMAC1 och OMAC2 block cipher-baserade Mac-datorer (de är baserade på AES).</p><p>Användningen av HMAC-SHA1 kan vara tillåten för plattforms kompatibilitet, men du måste ange ett undantag till den här proceduren och genomgå organisationens kryptografi granskning. Trunkering av HMAC till mindre än 128 bitar är inte tillåtet. Att använda kund metoder för att Hasha en nyckel och data är inte godkända och måste genomgå organisationens kryptografi granskare innan de används.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Använd endast godkända kryptografiska hash-funktioner

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Produkterna måste använda SHA-2-familjen för hash-algoritmer (SHA256, SHA384 och SHA512). Om en kortare hash behövs, till exempel en 128-bitars utmatnings längd för att passa en data struktur som utformats med en kortare MD5-hash i åtanke, kan produkt teamen trunkera en av SHA2-hasharna (vanligt vis SHA256). Observera att SHA384 är en trunkerad version av SHA512. Trunkering av kryptografiska hashar av säkerhets syfte till mindre än 128 bitar tillåts inte. Den nya koden får inte använda MD2-, MD4-, MD5-, SHA-0-, SHA-1-eller RIPEMD-hash-algoritmer. Hash-kollisioner är av en beräknings genomförbarhet för de här algoritmerna, vilket gör dem effektivt.</p><p>Tillåtna .NET-hash-algoritmer för smidig hantering av kryptografi (i prioritetsordning):</p><ul><li>SHA512Cng (FIPS-kompatibel)</li><li>SHA384Cng (FIPS-kompatibel)</li><li>SHA256Cng (FIPS-kompatibel)</li><li>SHA512Managed (icke-FIPS-kompatibel) (Använd SHA512 som algoritmens namn i anrop till HashAlgorithm. Create eller CryptoConfig. CreateFromName)</li><li>SHA384Managed (icke-FIPS-kompatibel) (Använd SHA384 som algoritmens namn i anrop till HashAlgorithm. Create eller CryptoConfig. CreateFromName)</li><li>SHA256Managed (icke-FIPS-kompatibel) (Använd SHA256 som algoritmens namn i anrop till HashAlgorithm. Create eller CryptoConfig. CreateFromName)</li><li>SHA512CryptoServiceProvider (FIPS-kompatibel)</li><li>SHA256CryptoServiceProvider (FIPS-kompatibel)</li><li>SHA384CryptoServiceProvider (FIPS-kompatibel)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Använd starka krypteringsalgoritmer för att kryptera data i databasen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Välja en krypteringsalgoritm](/sql/relational-databases/security/encryption/choose-an-encryption-algorithm) |
| **Steg** | Krypteringsalgoritmer definierar data transformationer som inte enkelt kan ångras av obehöriga användare. SQL Server låter administratörer och utvecklare välja mellan flera algoritmer, inklusive DES, tredubbel DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bitars RC4, DESX, 128-bitars AES, 192-bitars AES och 256-bitars AES |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>SSIS-paket ska vara krypterade och digitalt signerade

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Identifiera källan till paket med digitala signaturer](/sql/integration-services/security/identify-the-source-of-packages-with-digital-signatures), [hot och sårbarhets minskning (integrerings tjänster)](/sql/integration-services/security/security-overview-integration-services) |
| **Steg** | Källan till ett paket är den person eller organisation som skapade paketet. Det kan vara riskabelt att köra ett paket från en okänd eller obetrodd källa. För att förhindra obehörig manipulering av SSIS-paket ska du använda digitala signaturer. För att säkerställa konfidentialiteten för paket under lagring/överföring måste SSIS-paket krypteras |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Lägg till digital signatur till en kritisk databas skydd bara objekt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Lägg till signatur (Transact-SQL)](/sql/t-sql/statements/add-signature-transact-sql) |
| **Steg** | I de fall där integriteten för en kritisk databas kan skyddas måste du använda digitala signaturer. Databas skydd bara objekt, till exempel en lagrad procedur, funktion, sammansättning eller utlösare kan signeras digitalt. Nedan visas ett exempel på när detta kan vara användbart: Låt oss säga att en ISV (oberoende program varu leverantör) har tillhandahållit stöd för en program vara som levereras till en av sina kunder. Innan den ger support skulle ISV: en vara säker på att en databas som skyddas i program varan inte manipulerades av misstag eller av ett skadligt försök. Om den skydd bara är digitalt signerad kan ISV verifiera den digitala signaturen och verifiera dess integritet.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Använd SQL Server EKM för att skydda krypterings nycklar

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [SQL Server utöknings bar nyckel hantering (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm), [utöknings bar nyckel hantering med Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server) |
| **Steg** | SQL Server utöknings bar nyckel hantering gör det möjligt för krypterings nycklarna som skyddar databasfilerna att lagras i en annan enhet, till exempel en SmartCard, USB-enhet eller en EKM/HSM-modul. Detta gör det också möjligt att skydda data från databas administratörer (förutom medlemmar i sysadmin-gruppen). Data kan krypteras med hjälp av krypterings nycklar som bara databas användaren har åtkomst till i den externa EKM/HSM-modulen. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>Använd funktionen AlwaysEncrypted om krypterings nycklar inte ska visas för databas motorn

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | SQL-version – V12, MsSQL2016 |
| **Referenser**              | [Always Encrypted (databas motor)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |
| **Steg** | Always Encrypted är en funktion som har utformats för att skydda känsliga data, t. ex. kreditkorts nummer eller nationella identifierings nummer (t. ex. amerikanska socialförsäkrings nummer) som lagras i Azure SQL Database eller SQL Server databaser. Always Encrypted tillåter klienter att kryptera känsliga data i klient program och aldrig avslöja krypterings nycklarna till databas motorn (SQL Database eller SQL Server). Det innebär att Always Encrypted ger en separation mellan de som äger data (och kan visa det) och de som hanterar data (men inte har åtkomst) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Lagra kryptografiska nycklar säkert på IoT-enheter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Enhetens operativ system – Windows IoT Core, enhets anslutning – Azure IoT-enhets-SDK: er |
| **Referenser**              | [TPM på Windows IoT Core](/windows/iot-core/secure-your-device/TPM), [Konfigurera TPM på Windows IoT Core](/windows/iot-core/secure-your-device/setuptpm), [Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Steg** | Symmetriska eller certifikat privata nycklar på ett säkert sätt i en skyddad lagrings plats som TPM eller Smart Card-chips. Windows 10 IoT Core stöder användaren av en TPM och det finns flera kompatibla TPM: er som kan användas: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm . Vi rekommenderar att du använder en inbyggd program vara eller en diskret TPM. En program-TPM bör endast användas för utvecklings-och testnings ändamål. När en TPM är tillgänglig och nycklarna har definierats i den, ska den kod som genererar token skrivas utan hård kodning av känslig information i den. | 

### <a name="example"></a>Exempel
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Som du kan se är enhetens primär nyckel inte tillgänglig i koden. I stället lagras den i TPM: en vid fack 0. TPM-enheten genererar en SAS-token med kort livs längd som sedan används för att ansluta till IoT Hub. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Generera en slumpmässig symmetrisk nyckel med tillräcklig längd för autentisering till IoT Hub

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Gateway-val – Azure-IoT Hub |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | IoT Hub innehåller ett enhets identitets register och när du konfigurerar en enhet genererar automatiskt en slumpmässig symmetrisk nyckel. Vi rekommenderar att du använder den här funktionen i Azure IoT Hub Identity-registret för att generera nyckeln som används för autentisering. IoT Hub också tillåta att en nyckel anges när enheten skapas. Om en nyckel genereras utanför IoT Hub under enhets etableringen rekommenderar vi att du skapar en slumpmässig symmetrisk nyckel eller minst 256 bitar. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Se till att en princip för enhets hantering är på plats som kräver en PIN-kod för användning och tillåter fjärrrensning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM Mobile-klient | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att en princip för enhets hantering är på plats som kräver en PIN-kod för användning och tillåter fjärrrensning |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Se till att en princip för enhets hantering är på plats som kräver PIN/Password/Auto Lock och krypterar alla data (t. ex. BitLocker)

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Dynamics CRM Outlook-klient | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Se till att en princip för enhets hantering är på plats som kräver PIN/Password/Auto Lock och krypterar alla data (t. ex. BitLocker) |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Se till att signerings nycklar överförs när du använder en identitets Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitets Server | 
| **SDL-fas**               | Distribution |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Identitets Server – nycklar, signaturer och kryptografi](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Steg** | Se till att signerings nycklarna överförs när du använder identitets servern. Länken i avsnittet referenser förklarar hur detta bör planeras utan att orsaka avbrott i program som förlitar sig på identitets servern. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Se till att det kryptografiskt starka klient-ID: t, klient hemligheten används i identitets servern

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitets Server | 
| **SDL-fas**               | Skapa |  
| **Tillämpliga tekniker** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Se till att ett kryptografiskt starkt klient-ID, klient hemlighet, används i identitets servern. Följande rikt linjer bör användas när du genererar ett klient-ID och en hemlighet:</p><ul><li>Generera ett slumpmässigt GUID som klient-ID</li><li>Generera en kryptografiskt slumpmässig 256-bitars nyckel som hemlighet</li></ul>|