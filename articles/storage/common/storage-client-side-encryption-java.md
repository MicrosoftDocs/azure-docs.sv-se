---
title: Client-Side kryptering med Java för Microsoft Azure Storage | Microsoft Docs
description: Azure Storage klient bibliotek för Java stöder kryptering på klient sidan och integration med Azure Key Vault för maximal säkerhet för dina Azure Storage program.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-java
ms.openlocfilehash: 78baaa3f794bed870b40fb3975f6b80ff37e90f0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043736"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Client-Side kryptering och Azure Key Vault med Java för Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Översikt
[Azure Storage klient bibliotek för Java](https://search.maven.org/artifact/com.azure/azure-storage-blob-cryptography) stöder kryptering av data i klient program innan du laddar upp till Azure Storage och dekrypterar data vid hämtning till klienten. Biblioteket har även stöd för integrering med [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av lagrings konto nycklar.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering och dekryptering via kuvert tekniken
Processerna för kryptering och dekryptering följer kuvert tekniken.  

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via kuvert teknik
Kryptering via kuvert tekniken fungerar på följande sätt:  

1. Klient biblioteket för Azure Storage genererar en innehålls krypterings nyckel (CEK), som är en symmetrisk nyckel som används i ett tillfälle.  
2. Användar data krypteras med den här CEK.   
3. CEK omsluts sedan (krypteras) med nyckel krypterings nyckeln (KEK). KEK identifieras av en nyckel identifierare och kan vara ett asymmetriskt nyckel par eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i Azure Key Vault.  
   Själva lagrings klient biblioteket har aldrig åtkomst till KEK. Biblioteket anropar den nyckel brytnings algoritm som tillhandahålls av Key Vault. Användarna kan välja att använda anpassade providers för nyckel brytningar/unwrap om så önskas.  
4. Krypterade data överförs sedan till Azure Storages tjänsten. Den omslutna nyckeln tillsammans med vissa ytterligare krypterings-metadata lagras antingen som metadata (på en BLOB) eller interpoleras med krypterade data (köa meddelanden och tabell enheter).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via kuvert tekniken
Dekryptering via kuvert tekniken fungerar på följande sätt:  

1. Klient biblioteket förutsätter att användaren hanterar nyckel krypterings nyckeln (KEK) antingen lokalt eller i Azure Key Vaults. Användaren behöver inte känna till den speciella nyckel som användes för kryptering. I stället kan en nyckel lösare som matchar olika nyckel identifierare till nycklar konfigureras och användas.  
2. Klient biblioteket laddar ned krypterade data tillsammans med alla krypterings material som lagras på tjänsten.  
3. Den omslutna innehålls krypterings nyckeln (CEK) packas sedan om (dekrypterad) med nyckel krypterings nyckeln (KEK). Här igen har klient biblioteket inte åtkomst till KEK. Den anropar bara den anpassade algoritmen eller Key Vault providern.  
4. Innehålls krypterings nyckeln (CEK) används sedan för att dekryptera krypterade användar data.

## <a name="encryption-mechanism"></a>Krypterings metod
Lagrings klient biblioteket använder [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) för att kryptera användar data. Särskilt [CBC-läge (cipher block Chaining)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) med AES. Varje tjänst fungerar något annorlunda, så vi diskuterar var och en av dem här.

### <a name="blobs"></a>Blobar
Klient biblioteket har för närvarande endast stöd för kryptering av hela blobbar. Mer specifikt stöds kryptering när användare använder metoderna **upload _ upload** eller _ *openOutputStream**. För hämtnings bara filer stöds både fullständig och intervall hämtning.  

Under krypteringen genererar klient biblioteket en slumpmässig initierings vektor (IV) av 16 byte, tillsammans med en slumpmässig innehålls krypterings nyckel (CEK) på 32 byte och utför kuvert kryptering av BLOB-data med hjälp av den här informationen. Den omslutna CEK och vissa ytterligare krypterings-metadata lagras sedan som BLOB-metadata tillsammans med den krypterade blobben i tjänsten.

> [!WARNING]
> Om du redigerar eller laddar upp dina egna metadata för blobben måste du se till att dessa metadata bevaras. Om du laddar upp nya metadata utan dessa metadata går de omslutna CEK, IV och andra metadata förlorade och blob-innehållet kan inte hämtas igen.
> 
> 

Genom att hämta en krypterad BLOB måste du hämta innehållet i hela blobben med hjälp av **hämtnings** / metoder för **openInputStream** . Den omslutna CEK är unwrap och används tillsammans med IV (lagras som BLOB-metadata i det här fallet) för att returnera dekrypterade data till användarna.

Genom att ladda ned ett godtyckligt intervall (**downloadRange** -metoder) i den krypterade blobben måste du justera intervallet som anges av användarna för att få en liten mängd ytterligare data som kan användas för att dekryptera det begärda intervallet.  

Alla BLOB-typer (block blobbar, Page blobbar och bifogade blobbar) kan krypteras/dekrypteras med det här schemat.

### <a name="queues"></a>Köer
Eftersom Kömeddelanden kan ha ett format definierar klient biblioteket ett anpassat format som innehåller initierings vektorn (IV) och den krypterade innehålls krypterings nyckeln (CEK) i meddelande texten.  

Under krypteringen genererar klient biblioteket ett slumpmässigt IV av 16 byte tillsammans med en slumpmässig CEK på 32 byte och utför en kuvert kryptering av köns meddelande text med hjälp av den här informationen. Den omslutna CEK och vissa ytterligare krypterings-metadata läggs sedan till i det krypterade meddelandet i kön. Detta ändrade meddelande (visas nedan) lagras i tjänsten.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Under dekrypteringen extraheras den omslutna nyckeln från kösystemet och packas upp. IV extraheras också från meddelandet i kön och används tillsammans med den icke-omslutna nyckeln för att dekryptera köns meddelande data. Observera att krypterings-metadata är små (under 500 byte), så om det sker mot 64 KB-gränsen för ett Queue meddelande bör påverkan vara hanterbar.

### <a name="tables"></a>Tabeller
Klient biblioteket stöder kryptering av enhets egenskaper för åtgärderna Infoga och ersätt.

> [!NOTE]
> Sammanslagning stöds inte för närvarande. Eftersom en delmängd av egenskaperna kan ha krypterats tidigare med en annan nyckel, så resulterar det i data förlust genom att bara sammanfoga de nya egenskaperna och uppdatera metadata. Sammanslagning av kräver att du gör extra tjänst anrop för att läsa den befintliga entiteten från tjänsten, eller genom att använda en ny nyckel per egenskap, som båda inte passar av prestanda skäl.
> 
> 

Tabell data kryptering fungerar på följande sätt:  

1. Användarna anger vilka egenskaper som ska krypteras.  
2. Klient biblioteket genererar en slumpmässig initierings vektor (IV) av 16 byte tillsammans med en slumpmässig innehålls krypterings nyckel (CEK) på 32 byte för varje entitet och utför kuvert kryptering på de enskilda egenskaperna som ska krypteras genom att härleda en ny IV per egenskap. Den krypterade egenskapen lagras som binära data.  
3. Den omslutna CEK och vissa ytterligare krypterings-metadata lagras sedan som två ytterligare reserverade egenskaper. Den första reserverade egenskapen (_ClientEncryptionMetadata1) är en sträng egenskap som innehåller information om IV, version och rad bruten nyckel. Den andra reserverade egenskapen (_ClientEncryptionMetadata2) är en binär egenskap som innehåller information om de egenskaper som är krypterade. Informationen i den här andra egenskapen (_ClientEncryptionMetadata2) är själva krypterad.  
4. På grund av dessa ytterligare reserverade egenskaper som krävs för kryptering kan användarna nu bara ha 250 anpassade egenskaper i stället för 252. Entitetens totala storlek måste vara mindre än 1 MB.  
   
   Observera att endast sträng egenskaper kan krypteras. Om andra typer av egenskaper ska krypteras måste de konverteras till strängar. De krypterade strängarna lagras i tjänsten som binära egenskaper, och de konverteras tillbaka till strängar efter dekrypteringen.
   
   För tabeller, förutom krypterings principen, måste användarna ange vilka egenskaper som ska krypteras. Detta kan göras genom att antingen ange ett [kryptera]-attribut (för POCO-entiteter som härleds från TableEntity) eller en krypterings lösare i alternativ för begäran. En krypterings lösare är ett ombud som tar en partitionsnyckel, rad nyckel och egenskaps namn och returnerar ett booleskt värde som anger om den egenskapen ska krypteras. Under krypteringen använder klient biblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till kabeln. Delegaten ger också möjlighet att logiskt kringgå hur egenskaper krypteras. (Till exempel om X, krypterar sedan egenskap A, annars krypterar egenskaper A och B.) Observera att du inte behöver ange den här informationen samtidigt som du läser eller frågar entiteter.

### <a name="batch-operations"></a>Batch-åtgärder
I batch-åtgärder används samma KEK över alla rader i den batch-åtgärden eftersom klient biblioteket bara tillåter ett alternativ-objekt (och därmed en princip/KEK) per batch-åtgärd. Klient biblioteket genererar dock internt en ny slumpmässig IV-och slumpmässig CEK per rad i gruppen. Användare kan också välja att kryptera olika egenskaper för varje åtgärd i batchen genom att definiera det här beteendet i krypterings lösaren.

### <a name="queries"></a>Frågor
> [!NOTE]
> Eftersom entiteterna är krypterade kan du inte köra frågor som filtrerar på en krypterad egenskap.  Om du provar blir resultatet felaktigt, eftersom tjänsten försöker jämföra krypterade data med okrypterade data.
> 
> 
> Om du vill utföra frågor måste du ange en nyckel lösare som kan matcha alla nycklar i resultat uppsättningen. Om en entitet som ingår i frågeresultatet inte kan matchas till en provider, kommer klient biblioteket att utlösa ett fel. För alla frågor som utför Server sidans projektioner, lägger klient biblioteket till de särskilda egenskaperna för krypterings-metadata (_ClientEncryptionMetadata1 och _ClientEncryptionMetadata2) som standard i de markerade kolumnerna.

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Genom att använda Azure Key Vault kan användare kryptera nycklar och hemligheter (till exempel autentiseringsnyckel, lagrings konto nycklar, data krypterings nycklar). PFX-filer och lösen ord) med hjälp av nycklar som skyddas av HSM: er (Hardware Security modules). Mer information finns i [Vad är en Azure Key Vault?](../../key-vault/general/overview.md).

Lagrings klient biblioteket använder Key Vault kärn bibliotek för att tillhandahålla ett gemensamt ramverk för hantering av nycklar i Azure. Användarna får också den ytterligare fördelen med att använda biblioteket för Key Vault tillägg. Tilläggs biblioteket innehåller användbara funktioner runt enkla och sömlösa och sömlösa, lokala och molnbaserade och moln nyckel leverantörer samt med agg regering och cachelagring.

### <a name="interface-and-dependencies"></a>Gränssnitt och beroenden
Det finns tre Key Vaults paket:  

* Azure-nyckel valv – kärnan innehåller IKey och IKeyResolver. Det är ett litet paket utan beroenden. Lagrings klient biblioteket för Java definierar det som ett beroende.
* Azure-nyckel valvet innehåller Key Vault REST-klienten.  
* Azure-nyckel valv – tillägg innehåller tilläggs kod som innehåller implementeringar av krypteringsalgoritmer och en RSAKey och en SymmetricKey. Det beror på namn områdena Core och Key Vault och ger funktioner för att definiera en agg regerings lösare (när användare vill använda flera nyckel leverantörer) och en nyckel lösare för cachelagring. Även om lagrings klient biblioteket inte är direkt beroende av det här paketet, om användarna vill använda Azure Key Vault för att lagra sina nycklar eller använda Key Vault tillägg för att använda de lokala och molnbaserade kryptografiproviders, behöver de paketet.  
  
  Key Vault är utformad för huvud nycklar med högt värde och begränsnings gränser per Key Vault har utformats med detta i åtanke. När du utför kryptering på klient sidan med Key Vault, är den föredragna modellen att använda symmetriska huvud nycklar som lagras som hemligheter i Key Vault och cachelagras lokalt. Användarna måste göra följande:  

1. Skapa en hemlighet offline och ladda upp den till Key Vault.  
2. Använd hemlighetens bas-ID som en parameter för att matcha den aktuella versionen av hemligheten för kryptering och cachelagra den här informationen lokalt. Använda CachingKeyResolver för cachelagring; användare förväntas inte implementera sin egen cachelagring Logic.  
3. Använd Caching-matcharen som indatatyp när du skapar krypterings principen.
   Mer information om hur du Key Vault användning finns i krypterings kod exemplen.

## <a name="best-practices"></a>Bästa praxis
Krypterings stödet är bara tillgängligt i lagrings klient biblioteket för Java.

> [!IMPORTANT]
> Tänk på följande viktiga punkter när du använder kryptering på klient sidan:
> 
> * När du läser från eller skriver till en krypterad BLOB använder du kommandona för att ladda upp hela bloben och hämta intervall/hela BLOB-kommandon. Undvik att skriva till en krypterad BLOB med hjälp av protokoll åtgärder som till exempel list block, list Blocks lista, skriv sidor, rensa sidor eller Lägg till block. Annars kan du skada den krypterade blobben och göra den oläslig.
> * För tabeller finns det liknande villkor. Var noga med att inte uppdatera krypterade egenskaper utan att uppdatera metadata för kryptering.  
> * Om du ställer in metadata på den krypterade blobben kan du skriva över de krypterings-relaterade metadata som krävs för dekryptering, eftersom inställning av metadata inte är additiv. Detta gäller även för ögonblicks bilder. Undvik att ange metadata när du skapar en ögonblicks bild av en krypterad blob. Om metadata måste anges måste du anropa **downloadAttributes** -metoden först för att hämta aktuella krypterings-metadata och undvika samtidiga skrivningar medan metadata anges.  
> * Aktivera flaggan **requireEncryption** i standard alternativen för begäran för användare som endast ska arbeta med krypterade data. Se nedan för mer information.  
> 
> 

## <a name="client-api--interface"></a>Klient-API/gränssnitt
När du skapar ett EncryptionPolicy-objekt kan användare bara tillhandahålla en nyckel (implementera IKey), bara en lösare (implementera IKeyResolver) eller båda. IKey är den grundläggande nyckel typen som identifieras med hjälp av en nyckel identifierare och som ger logiken för att figursättas/avfigurning. IKeyResolver används för att matcha en nyckel under avkrypterings processen. Den definierar en ResolveKey-metod som returnerar en IKey som har fått en nyckel identifierare. Detta ger användarna möjlighet att välja mellan flera nycklar som hanteras på flera platser.

* För kryptering används nyckeln alltid och frånvaron av en nyckel leder till ett fel.  
* För dekryptering:  
  
  * Nyckel lösa ren anropas om den anges för att hämta nyckeln. Om matcharen har angetts men inte har någon mappning för nyckel identifieraren genereras ett fel.  
  * Om ingen lösare anges men en nyckel anges, används nyckeln om dess identifierare matchar den nödvändiga nyckel identifieraren. Om identifieraren inte matchar genereras ett fel.  
    
    [Krypterings exemplen](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) demonstrerar ett mer detaljerat end-to-end-scenario för blobbar, köer och tabeller, tillsammans med Key Vault-integrering.

### <a name="requireencryption-mode"></a>RequireEncryption-läge
Användare kan välja att aktivera ett läge med en åtgärd där alla överföringar och nedladdningar måste krypteras. I det här läget kommer försök att överföra data utan en krypterings princip eller ladda ned data som inte är krypterade på tjänsten att Miss lyckas på klienten. Flaggan **requireEncryption** för objektet alternativ för begäran styr det här beteendet. Om ditt program ska kryptera alla objekt som lagras i Azure Storage, kan du ange egenskapen **requireEncryption** för standardbegärans alternativen för tjänstens klient objekt.   

Använd till exempel **CloudBlobClient. getDefaultRequestOptions (). setRequireEncryption (true)** om du vill kräva kryptering för alla BLOB-åtgärder som utförs via det klient objektet.

### <a name="blob-service-encryption"></a>Blob Service kryptering
Skapa ett **BlobEncryptionPolicy** -objekt och ange det i alternativ för begäran (per API eller på klient nivå med hjälp av **DefaultRequestOptions**). Allt annat kommer att hanteras av klient biblioteket internt.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```
---

### <a name="queue-service-encryption"></a>Kötjänst kryptering
Skapa ett **QueueEncryptionPolicy** -objekt och ange det i alternativ för begäran (per API eller på klient nivå med hjälp av **DefaultRequestOptions**). Allt annat kommer att hanteras av klient biblioteket internt.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```
---

### <a name="table-service-encryption"></a>Table service kryptering
Förutom att skapa en krypterings princip och ställa in den på begär ande alternativ måste du antingen ange en **EncryptionResolver** i **TableRequestOptions** eller ange attributet [kryptera] för entitetens get och set.

### <a name="using-the-resolver"></a>Använda matcharen

# <a name="java-v12"></a>[Java-V12](#tab/java)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```
---

### <a name="using-attributes"></a>Använda attribut
Som nämnts ovan, om entiteten implementerar TableEntity, kan egenskaperna get och set anges med attributet [kryptera] i stället för att ange **EncryptionResolver**.

# <a name="java-v12"></a>[Java-V12](#tab/java)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java-V8](#tab/java8)

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```
---

## <a name="encryption-and-performance"></a>Kryptering och prestanda

Observera att kryptering av lagrings data ger ytterligare prestanda. Innehålls nyckeln och IV måste genereras, själva innehållet måste vara krypterat och ytterligare meta-data måste formateras och överföras. Den här omkostnaderna varierar beroende på mängden data som krypteras. Vi rekommenderar att kunderna alltid testar sina program för prestanda under utvecklingen.

## <a name="next-steps"></a>Nästa steg

* Ladda ned [Azure Storage klient bibliotek för Java maven-paket](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Ladda ned [Azure Storage klient bibliotek för Java-källkod från GitHub](https://github.com/Azure/azure-storage-java)
* Ladda ned Azure Key Vault maven-biblioteket för Java maven-paket:
  * [Kärn](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) paket
  * [Klient](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) paket
* Besök [Azure Key Vault-dokumentationen](../../key-vault/general/overview.md)
