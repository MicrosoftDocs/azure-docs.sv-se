---
title: Kryptering på klient sidan med python
titleSuffix: Azure Storage
description: Azure Storage klient bibliotek för python stöder kryptering på klient sidan för maximal säkerhet för dina Azure Storage-program.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ffdfd4dc8a81587d757e3f9853f1bb34e0b93c0d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043753"
---
# <a name="client-side-encryption-with-python"></a>Kryptering på klient sidan med python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Översikt
[Azure Storage klient bibliotek för python](https://pypi.python.org/pypi/azure-storage) stöder kryptering av data i klient program innan du laddar upp till Azure Storage och dekrypterar data vid hämtning till klienten.

> [!NOTE]
> Azure Storage python-biblioteket är i för hands version.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering och dekryptering via kuvert tekniken
Processerna för kryptering och dekryptering följer kuvert tekniken.

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via kuvert teknik
Kryptering via kuvert tekniken fungerar på följande sätt:

1. Klient biblioteket för Azure Storage genererar en innehålls krypterings nyckel (CEK), som är en symmetrisk nyckel som används i ett tillfälle.
2. Användar data krypteras med den här CEK.
3. CEK omsluts sedan (krypteras) med nyckel krypterings nyckeln (KEK). KEK identifieras av en nyckel identifierare och kan vara ett asymmetriskt nyckel par eller en symmetrisk nyckel som hanteras lokalt.
   Själva lagrings klient biblioteket har aldrig åtkomst till KEK. Biblioteket anropar den nyckel brytnings algoritm som tillhandahålls av KEK. Användarna kan välja att använda anpassade providers för nyckel brytningar/unwrap om så önskas.
4. Krypterade data överförs sedan till Azure Storages tjänsten. Den omslutna nyckeln tillsammans med vissa ytterligare krypterings-metadata lagras antingen som metadata (på en BLOB) eller interpoleras med krypterade data (köa meddelanden och tabell enheter).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via kuvert tekniken
Dekryptering via kuvert tekniken fungerar på följande sätt:

1. Klient biblioteket förutsätter att användaren hanterar nyckel krypterings nyckeln (KEK) lokalt. Användaren behöver inte känna till den speciella nyckel som användes för kryptering. I stället kan en nyckel lösare, som matchar olika nyckel identifierare till nycklar, konfigureras och användas.
2. Klient biblioteket laddar ned krypterade data tillsammans med alla krypterings material som lagras på tjänsten.
3. Den omslutna innehålls krypterings nyckeln (CEK) packas sedan om (dekrypterad) med nyckel krypterings nyckeln (KEK). Här igen har klient biblioteket inte åtkomst till KEK. Den anropar bara den anpassade providerns Wrapping-algoritm.
4. Innehålls krypterings nyckeln (CEK) används sedan för att dekryptera krypterade användar data.

## <a name="encryption-mechanism"></a>Krypterings metod
Lagrings klient biblioteket använder [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) för att kryptera användar data. Särskilt [CBC-läge (cipher block Chaining)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) med AES. Varje tjänst fungerar något annorlunda, så vi diskuterar var och en av dem här.

### <a name="blobs"></a>Blobar
Klient biblioteket har för närvarande endast stöd för kryptering av hela blobbar. Mer specifikt stöds kryptering när användarna använder metoderna **create***. För hämtningar stöds både fullständig och intervall hämtningar, och parallellisering av både överföring och nedladdning är tillgängligt.

Under krypteringen genererar klient biblioteket en slumpmässig initierings vektor (IV) av 16 byte, tillsammans med en slumpmässig innehålls krypterings nyckel (CEK) på 32 byte och utför kuvert kryptering av BLOB-data med hjälp av den här informationen. Den omslutna CEK och vissa ytterligare krypterings-metadata lagras sedan som BLOB-metadata tillsammans med den krypterade blobben i tjänsten.

> [!WARNING]
> Om du redigerar eller laddar upp dina egna metadata för blobben måste du se till att dessa metadata bevaras. Om du laddar upp nya metadata utan dessa metadata går de omslutna CEK, IV och andra metadata förlorade och blob-innehållet kan inte hämtas igen.
> 
> 

Genom att hämta en krypterad BLOB måste du hämta innehållet i hela blobben med hjälp av **Get***-bekvämlighets metoderna. Den omslutna CEK är unwrap och används tillsammans med IV (lagras som BLOB-metadata i det här fallet) för att returnera dekrypterade data till användarna.

Att ladda ned ett godtyckligt intervall (**Get*** metoder med intervall parametrar som skickas in) i den krypterade blobben innefattar justering av intervallet som anges av användarna för att få en liten mängd ytterligare data som kan användas för att dekryptera det begärda intervallet.

Block-blobbar och Page blobbar kan bara krypteras/dekrypteras med det här schemat. Det finns för närvarande inget stöd för kryptering av tilläggs-blobar.

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
3. Den omslutna CEK och vissa ytterligare krypterings-metadata lagras sedan som två ytterligare reserverade egenskaper. Den första reserverade egenskapen ( \_ ClientEncryptionMetadata1) är en sträng egenskap som innehåller information om IV, version och rad bruten nyckel. Den andra reserverade egenskapen ( \_ ClientEncryptionMetadata2) är en binär egenskap som innehåller information om de egenskaper som är krypterade. Informationen i den här andra egenskapen ( \_ ClientEncryptionMetadata2) är själva krypterad.
4. På grund av dessa ytterligare reserverade egenskaper som krävs för kryptering kan användarna nu bara ha 250 anpassade egenskaper i stället för 252. Entitetens totala storlek måste vara mindre än 1 MB.

   Observera att endast sträng egenskaper kan krypteras. Om andra typer av egenskaper ska krypteras måste de konverteras till strängar. De krypterade strängarna lagras i tjänsten som binära egenskaper och konverteras tillbaka till strängar (obehandlade strängar, inte EntityProperties med typen EdmType. STRING) efter dekrypteringen.

   För tabeller, förutom krypterings principen, måste användarna ange vilka egenskaper som ska krypteras. Detta kan göras genom att antingen lagra egenskaperna i TableEntity-objekt med typen inställt på EdmType. STRING och kryptera ange värdet true eller ange encryption_resolver_function på tableservice-objektet. En krypterings lösare är en funktion som tar en partitionsnyckel, rad nyckel och egenskaps namn och returnerar ett booleskt värde som anger om den egenskapen ska krypteras. Under krypteringen använder klient biblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till kabeln. Delegaten ger också möjlighet att logiskt kringgå hur egenskaper krypteras. (Till exempel om X, krypterar sedan egenskap A, annars krypterar egenskaper A och B.) Observera att du inte behöver ange den här informationen samtidigt som du läser eller frågar entiteter.

### <a name="batch-operations"></a>Batch-åtgärder
En krypterings princip gäller för alla rader i gruppen. Klient biblioteket genererar internt en ny slumpmässig IV-och slumpmässig CEK per rad i gruppen. Användare kan också välja att kryptera olika egenskaper för varje åtgärd i batchen genom att definiera det här beteendet i krypterings lösaren.
Om en batch skapas som en kontext hanterare via metoden tableservice batch (), kommer tableservices krypterings princip att tillämpas automatiskt på batchen. Om en batch skapas explicit genom att anropa konstruktorn, måste krypterings principen skickas som en parameter och lämnas oförändrad under gruppens livstid.
Observera att entiteter krypteras när de infogas i batchen med hjälp av batchens krypterings princip (entiteter krypteras inte vid tidpunkten för att genomföra batchen med hjälp av tableservice krypterings princip).

### <a name="queries"></a>Frågor
> [!NOTE]
> Eftersom entiteterna är krypterade kan du inte köra frågor som filtrerar på en krypterad egenskap.  Om du provar blir resultatet felaktigt, eftersom tjänsten försöker jämföra krypterade data med okrypterade data.
> 
> 
> Om du vill utföra frågor måste du ange en nyckel lösare som kan matcha alla nycklar i resultat uppsättningen. Om en entitet som ingår i frågeresultatet inte kan matchas till en provider, kommer klient biblioteket att utlösa ett fel. För alla frågor som utför Server sidans projektioner, lägger klient biblioteket till de särskilda egenskaperna för krypterings-metadata ( \_ ClientEncryptionMetadata1 och \_ ClientEncryptionMetadata2) som standard i de markerade kolumnerna.
> 
> [!IMPORTANT]
> Tänk på följande viktiga punkter när du använder kryptering på klient sidan:
> 
> * När du läser från eller skriver till en krypterad BLOB använder du kommandona för att ladda upp hela bloben och hämta intervall/hela BLOB-kommandon. Undvik att skriva till en krypterad BLOB med hjälp av protokoll åtgärder som till exempel spärra block, lista över blockerade sidor, skriv sidor eller rensa sidor. Annars kan du skada den krypterade blobben och göra den oläslig.
> * För tabeller finns det liknande villkor. Var noga med att inte uppdatera krypterade egenskaper utan att uppdatera metadata för kryptering.
> * Om du ställer in metadata på den krypterade blobben kan du skriva över de krypterings-relaterade metadata som krävs för dekryptering, eftersom inställning av metadata inte är additiv. Detta gäller även för ögonblicks bilder. Undvik att ange metadata när du skapar en ögonblicks bild av en krypterad blob. Om metadata måste anges måste du anropa metoden **get_blob_metadata** först för att hämta aktuella krypterings-metadata och undvika samtidiga skrivningar medan metadata anges.
> * Aktivera **require_encryption** -flaggan på serviceobjektet för användare som endast ska arbeta med krypterade data. Se nedan för mer information.

Lagrings klient biblioteket förväntar sig att den angivna KEK och nyckel lösa ren implementerar följande gränssnitt. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stöd för python KEK-hantering väntar och kommer att integreras i det här biblioteket när det är klart.

## <a name="client-api--interface"></a>Klient-API/gränssnitt
När ett lagrings tjänst objekt (t. ex. blockblobservice) har skapats kan användaren tilldela värden till fälten som utgör en krypterings princip: key_encryption_key, key_resolver_function och require_encryption. Användare kan bara tillhandahålla en KEK, bara en lösare eller både och. key_encryption_key är den grundläggande nyckel typen som identifieras med hjälp av en nyckel identifierare och som ger logiken för att figursättas/avfigurning. key_resolver_function används för att matcha en nyckel under avkrypterings processen. Den returnerar en giltig KEK med ett nyckel-ID. Detta ger användarna möjlighet att välja mellan flera nycklar som hanteras på flera platser.

KEK måste implementera följande metoder för att kunna kryptera data:

* wrap_key (CEK): omsluter angiven CEK (byte) med hjälp av en algoritm för användarens val. Returnerar den figursatta nyckeln.
* get_key_wrap_algorithm (): returnerar algoritmen som används för att figursätta nycklar.
* get_kid (): returnerar sträng nyckel-ID: t för den här KEK.
  KEK måste implementera följande metoder för att kunna dekryptera data:
* unwrap_key (CEK, algoritm): returnerar den omslutna formen av den angivna CEK med hjälp av den sträng-specificerade algoritmen.
* get_kid (): returnerar ett sträng nyckel-ID för den här KEK.

Nyckel lösare måste minst implementera en metod som, med ett nyckel-ID, returnerar motsvarande KEK som implementerar gränssnittet ovan. Endast den här metoden är kopplad till egenskapen key_resolver_function i serviceobjektet.

* För kryptering används nyckeln alltid och frånvaron av en nyckel leder till ett fel.
* För dekryptering:

  * Nyckel lösa ren anropas om den anges för att hämta nyckeln. Om matcharen har angetts men inte har någon mappning för nyckel identifieraren genereras ett fel.
  * Om ingen lösare anges men en nyckel anges, används nyckeln om dess identifierare matchar den nödvändiga nyckel identifieraren. Om identifieraren inte matchar genereras ett fel.

    Krypterings exemplen i Azure. Storage. Samples demonstrerar ett mer detaljerat scenario från slut punkt till slut punkt för blobbar, köer och tabeller.
      Exempel på implementeringar av KEK och nyckel lösa ren finns i exempelfilerna som nyckel omslutningar och nyckel matchare.

### <a name="requireencryption-mode"></a>RequireEncryption-läge
Användare kan välja att aktivera ett läge med en åtgärd där alla överföringar och nedladdningar måste krypteras. I det här läget kommer försök att överföra data utan en krypterings princip eller ladda ned data som inte är krypterade på tjänsten att Miss lyckas på klienten. Flaggan **require_encryption** på serviceobjektet styr det här beteendet.

### <a name="blob-service-encryption"></a>Blob Service kryptering
Ange krypterings princip fält för blockblobservice-objektet. Allt annat kommer att hanteras av klient biblioteket internt.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```
---

### <a name="queue-service-encryption"></a>Kötjänst kryptering
Ange krypterings princip fält för queueservice-objektet. Allt annat kommer att hanteras av klient biblioteket internt.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```
---

### <a name="table-service-encryption"></a>Table service kryptering
Förutom att skapa en krypterings princip och ställa in den på begär ande alternativ måste du antingen ange en **encryption_resolver_function** på **tableservice** eller ange attributet kryptera på EntityProperty.

### <a name="using-the-resolver"></a>Använda matcharen

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```
---

### <a name="using-attributes"></a>Använda attribut
Som nämnts ovan kan en egenskap markeras för kryptering genom att den lagras i ett EntityProperty-objekt och inställning av fältet kryptera.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Vi arbetar för närvarande för att skapa kodfragment som återspeglar version 12. x av Azure Storage klient bibliotek. Mer information finns i avsnittet [om att presentera Azure Storage V12-klient bibliotek](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```
---

## <a name="encryption-and-performance"></a>Kryptering och prestanda
Observera att kryptering av lagrings data ger ytterligare prestanda. Innehålls nyckeln och IV måste genereras, själva innehållet måste vara krypterat och ytterligare metadata måste formateras och överföras. Den här omkostnaderna varierar beroende på mängden data som krypteras. Vi rekommenderar att kunderna alltid testar sina program för prestanda under utvecklingen.

## <a name="next-steps"></a>Nästa steg
* Ladda ned [Azure Storage klient bibliotek för Java PyPi-paket](https://pypi.python.org/pypi/azure-storage)
* Ladda ned [Azure Storage klient bibliotek för python-källkod från GitHub](https://github.com/Azure/azure-storage-python)
