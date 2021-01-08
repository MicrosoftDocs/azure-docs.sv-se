---
title: Horisont-API
description: I den här guiden beskrivs vanliga metoder för Horisont.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6d2e3fccd6a61fe129050faa29cb7bb77674ccfe
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976917"
---
# <a name="horizon-api"></a>Horisont-API 

I den här guiden beskrivs vanliga metoder för Horisont.

### <a name="getting-more-information"></a>Hämta mer information

Mer information om hur du arbetar med horisont och CyberX-plattformen finns i följande:

- Kontakta CyberX-representanten om du vill ha ett ODE-SDK (Open Development Environment) för Horisont.
- Kontakta om du vill ha support och felsöknings information <support@cyberx-labs.com> .
- För att få åtkomst till användar handboken för Cyberx från CyberX-konsolen väljer du :::image type="icon" source="media/references-horizon-api/profile-icon.png"::: och väljer sedan **Hämta användar guide**.

## `horizon::protocol::BaseParser`

Abstrakt för alla plugin-program. Detta består av två metoder:

- För att bearbeta de plugin-filter som definierats ovan. På så sätt vet horisonten att kommunicera med parsern
- För bearbetning av faktiska data.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

Den första funktionen som anropas för plugin-programmet skapar en instans av parsern för Horisont för att identifiera den och registrera den.

### <a name="parameters"></a>Parametrar 

Ingen

### <a name="return-value"></a>Returvärde

shared_ptr till din parser-instans.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Den här funktionen kommer att anropas för varje plugin-program som registrerats ovan. 

I de flesta fall är detta tomt. Utlös ett undantag för Horisont för att veta något dåligt hände.

### <a name="parameters"></a>Parametrar 

- En karta som innehåller strukturen för dissect_as, enligt definitionen i config.jspå ett annat plugin-program som vill registrera dig för dig.

### <a name="return-value"></a>Returvärde 

En matris med uint64_t som bearbetas av en typ av uint64_t. Det innebär att du har en lista över portar i kartan, vars värden är uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Huvud funktionen. Mer specifikt är plugin-programmets logik, varje gången ett nytt paket når din parser. Den här funktionen kommer att anropas. allt som är relaterat till paket bearbetning bör göras här.

### <a name="considerations"></a>Överväganden

Ditt plugin-program bör vara tråd säkra, eftersom den här funktionen kan anropas från olika trådar. En lämplig metod är att definiera allt i stacken.

### <a name="parameters"></a>Parametrar

- Den SDK-kontroll enhet som ansvarar för att lagra data och skapa SDK-relaterade objekt, till exempel ILayer, fält osv.
- En hjälp för att läsa data i RAW-paketet. Den har redan angetts med den byte ordning som du definierade i config.jspå.

### <a name="return-value"></a>Returvärde 

Resultatet av bearbetningen. Detta kan vara antingen lyckades/felaktigt/Sanity.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Markerar bearbetningen som sanering-haveri, vilket innebär att paketet inte känns igen av det aktuella protokollet, och att horisonten ska skicka det till andra parser, om något är registrerat på samma filter.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Konstruktor

### <a name="parameters"></a>Parametrar 

- Definierar den felkod som används av horisonten för loggning, enligt definitionen i config.jspå.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Felaktigt resultat, angivet att vi redan identifierat paketet som vårt protokoll, men en del verifieringen gick fel (reserverade bitar är på, ett fält saknas osv.)

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Konstruktor

### <a name="parameters"></a>Parametrar  

- Felkod, enligt definitionen i config.jspå.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Meddelar horisonten för lyckad bearbetning. När det lyckas godtogs paketet. data tillhör oss och alla data har extraherats.

## `horizon::protocol::SuccessResult()`

Konstruktorn. Ett Basic-lyckat resultat har skapats. Det innebär att vi inte känner till riktningen eller andra metadata för paketet.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Konstruktor

### <a name="parameters"></a>Parametrar 

- Paketets riktning, om det identifieras. Värden kan vara REQUEST, RESPONSe

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Konstruktor

### <a name="parameters"></a>Parametrar

- Paketets riktning, om vi har identifierat det, kan vara begäran, svar
- Varna. De här händelserna kommer inte att Miss lyckas, men horisonten kommer att meddelas.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Konstruktor

### <a name="parameters"></a>Parametrar 

-  Varna. De här händelserna kommer inte att Miss lyckas, men horisonten kommer att meddelas.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Konverterar en sträng baserad referens till ett fält namn (t. ex. function_code) till HorizonID

### <a name="parameters"></a>Parametrar 

- Sträng som ska konverteras

### <a name="return-value"></a>Returvärde

- HorizonID har skapats från strängen.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Skapar ett nytt lager så Horisont vet att plugin-programmet vill lagra data. Det här är bas lagrings enheten som du bör använda.

### <a name="return-value"></a>Returvärde

En referens till ett skapat lager så att du kan lägga till data i den.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Hämtar Field Management-objektet, som ansvarar för att skapa fält på olika objekt, t. ex. på ILayer

### <a name="return-value"></a>Returvärde

En referens till chefen

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Skapar ett nytt numeriskt fält på 64 bitar på lagret med det begärda ID: t.

### <a name="parameters"></a>Parametrar 

- Lagret som du skapade tidigare
- HorizonID som skapats av makrot för HORIZON_FIELD
- Det råa värde som du vill lagra

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Skapar ett nytt sträng fält för på lagret med det begärda ID: t. Minnet kommer att flyttas, så var försiktig. Du kommer inte att kunna använda det här värdet igen.

### <a name="parameters"></a>Parametrar  

- Lagret som du skapade tidigare
- HorizonID som skapats av makrot för HORIZON_FIELD
- Det råa värde som du vill lagra

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Skapar ett nytt oformaterat värde (matris med byte) i skiktet, med det begärda ID: t. Minnet kommer att flyttas, så var försiktig. du kommer inte att kunna använda det här värdet igen

### <a name="parameters"></a>Parametrar

- Lagret som du skapade tidigare
- HorizonID som skapats av makrot för HORIZON_FIELD
- Det råa värde som du vill lagra

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Skapar ett mat ris värde fält (matris) i skiktet av den angivna typen med det begärda ID: t.

### <a name="parameters"></a>Parametrar

- Lagret som du skapade tidigare
- HorizonID som skapats av makrot för HORIZON_FIELD
- Den typ av värden som ska lagras i matrisen

### <a name="return-value"></a>Returvärde

Referens till en matris som du bör lägga till värden i

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Lägger till ett nytt heltals värde i matrisen som skapades tidigare

### <a name="parameters"></a>Parametrar

- Matrisen skapades tidigare
- Det råa värde som ska lagras i matrisen

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Lägger till ett nytt sträng värde i matrisen som skapades tidigare. Minnet kommer att flyttas, så var försiktig. du kommer inte att kunna använda det här värdet igen

### <a name="parameters"></a>Parametrar

- Matrisen skapades tidigare
- RAW-värde som ska lagras i matrisen

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Lägger till ett nytt oformaterat värde i matrisen som skapades tidigare. Minnet kommer att flyttas, så var försiktig. du kommer inte att kunna använda det här värdet igen

### <a name="parameters"></a>Parametrar

- Matrisen skapades tidigare
- RAW-värde som ska lagras i matrisen

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Kontrollerar att bufferten innehåller minst X byte.

### <a name="parameters"></a>Parametrar

Antalet byte bör finnas 

### <a name="return-value"></a>Returvärde

Sant om bufferten innehåller minst X byte. Annars FALSE.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Läser UInt8-värdet (1 byte), från bufferten, enligt byte ordningen.

### <a name="return-value"></a>Returvärde

Värdet som läses från bufferten.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Läser UInt16-värdet (2 byte) från bufferten enligt byte ordningen.

### <a name="return-value"></a>Returvärde

Värdet som läses från bufferten.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Läser UInt32-värde (4 byte) från bufferten enligt byte ordningen.

### <a name="return-value"></a>Returvärde

Värdet som läses från bufferten.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Läser UInt64-värdet (8 byte), från bufferten, enligt byte ordningen.

### <a name="return-value"></a>Returvärde

Värdet som läses från bufferten.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

Läsningar i förallokerat minne, med en angiven storlek, kopierar själva data till din minnes region.

### <a name="parameters"></a>Parametrar 

- Den minnes region som data ska kopieras till
- Storleken på minnes området, den här parametern definierar också hur många byte som ska kopieras

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Läser in en sträng från bufferten

### <a name="parameters"></a>Parametrar 

- Antalet byte som ska läsas.

### <a name="return-value"></a>Returvärde

Referens till strängens minnes region.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Visar hur många byte kvar i bufferten.

### <a name="return-value"></a>Returvärde

Buffertens återstående storlek.

## `void horizon::general::IDataBuffer::skip(size_t)`

Hoppar över X byte i bufferten.

### <a name="parameters"></a>Parametrar

- Antal byte som ska hoppas över.
