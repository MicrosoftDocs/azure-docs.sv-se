---
title: Horisont-SDK
titleSuffix: Azure Defender for IoT
description: Med hjälp av Horisont-SDK kan Azure Defender för IoT-utvecklare utforma DISSEKTOR-plugin-program som avkodar nätverks trafik så att den kan bearbetas av automatiserade Defender för IoT-nätverk.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: d6105f65508eff59164246020d9a3f286b68c5a1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210751"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Avsektor för Horisont av patentskyddat protokoll

Horisont är en öppna utvecklings miljö (ODE) som används för att skydda IoT-och ICS-enheter som kör patentskyddade protokoll.

Den här miljön innehåller följande lösningar för kunder och teknik partner:

- Obegränsad, fullständig support för vanliga, tillverkarspecifika, anpassade protokoll eller protokoll som avviker från valfri standard. 

- En ny nivå av flexibilitet och omfattning för DPI-utveckling.

- Ett verktyg som exponentiellt utökar synlig synlighet och kontroll, utan att behöva uppgradera Defender för IoT Platform-versioner.

- Säkerheten för att tillåta patentskyddad utveckling utan att avslöja känslig information.

Med hjälp av Horisont-SDK kan Azure Defender för IoT-utvecklare utforma DISSEKTOR-plugin-program som avkodar nätverks trafik så att den kan bearbetas av automatiserade Defender för IoT-nätverk.

Protokoll från sektorer utvecklas som externa plugin-program och är integrerade med en omfattande uppsättning Defender för IoT-tjänster. Till exempel tjänster som tillhandahåller funktioner för övervakning, avisering och rapportering.

## <a name="secure-development-environment"></a>Säker utvecklings miljö 

Med horisonten ODE kan du utveckla anpassade eller patentskyddade protokoll som inte kan delas utanför en organisation. Till exempel på grund av juridiska föreskrifter eller företags principer.

Utveckla insektoriella plugin-program utan: 

- avslöja all ägande information om hur dina protokoll definieras.

- dela alla känsliga PCAPs.

- brott mot regler för efterlevnad.

## <a name="customization-and-localization"></a>Anpassning och lokalisering  

SDK stöder olika anpassnings alternativ, inklusive:

  - Text för funktions koder. 

  - Fullständig lokaliserings text för aviseringar, händelser och protokoll parametrar. Mer information finns i [create Mapping Files (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Visa fullständigt lokaliserade aviseringar.":::

## <a name="horizon-architecture"></a>Horisont arkitektur

Arkitektur modellen innehåller tre produkt lager.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender för IoT Platform Layer

Möjliggör omedelbar integrering och real tids övervakning av anpassade avsöknings-plugin-program i Defender för IoT-plattformen, utan att behöva uppgradera version av Defender för IoT-plattformen.

## <a name="defender-for-iot-services-layer"></a>Defender för IoT Services Layer

Varje tjänst är utformad som en pipeline som är frikopplad från ett särskilt protokoll, vilket möjliggör mer effektiv och oberoende utveckling.

Varje tjänst är utformad som en pipeline som är frikopplad från ett särskilt protokoll. Tjänster lyssnar efter trafik i pipelinen. De interagerar med plugin-data och den trafik som fångas av sensorer för att indexera distribuerade protokoll och analysera nytto lasten för trafiken, och möjliggör en mer effektiv och oberoende utveckling.

## <a name="custom-dissector-layer"></a>Anpassat DISSEKTOR lager 

Aktiverar skapande av plugin-program med hjälp av Defender för IoT patentskyddad SDK (inklusive C++-implementering och JSON-konfiguration) till: 

- Definiera hur protokollet ska identifieras

- Definiera hur du mappar de fält som du vill extrahera från trafiken och extrahera dem 

- Definiera hur du ska integrera med Defender för IoT-tjänster

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="Inbyggda lager.":::

Defender för IoT innehåller grundläggande dissektorer för vanliga protokoll. Du kan bygga dina dissektorer ovanpå de här protokollen.

## <a name="before-you-begin"></a>Innan du börjar

## <a name="what-this-sdk-contains"></a>Vad detta SDK innehåller 

Det här paketet innehåller de huvud-filer som behövs för utveckling. Utvecklings processen kräver grundläggande steg och valfria avancerade steg som beskrivs i den här SDK: n.

Kontakta <support@cyberx-labs.com> för att få information om att ta emot huvudfiler och andra resurser.

## <a name="about-the-environment-and-setup"></a>Om miljön och konfigurationen 

### <a name="requirements"></a>Krav 

- Den önskade utvecklings miljön är Linux. Om du utvecklar i en Windows-miljö bör du överväga att använda en virtuell dator med ett Linux-system.

- För Compilation-processen använder du GCC 7.4.0 eller högre. Använd valfri standard klass från stdlib som stöds i C++ 17.

- Defender för IoT version 3,0 och senare.

### <a name="process"></a>Process

1. [Ladda ned](https://www.eclipse.org/) Sol förmörkelse IDE för C/C++-utvecklare. Du kan använda valfri annan IDE som du föredrar. Det här dokumentet vägleder dig genom konfigurationen med hjälp av Sol förmörkelse IDE.

1. När du har lanserat Sol förmörkelse IDE och konfigurerat arbets ytan (där dina projekt ska lagras) trycker du på **CTRL + n** och skapar det som ett C++-projekt.

1. På nästa skärm anger du namnet på det protokoll som du vill utveckla och väljer projekt typen som `Shared Library` och `AND Linux GCC` .

1. Redigera projekt egenskaperna, under **C/C++ build**  >  **Settings**  >  **Tool Settings**  >  **gcc C++ compiler**,  >    >  **oberoende kod**.

1. Klistra in exempel koderna som du fick med SDK och kompilera den.

1. Lägg till artefakterna (biblioteket, config.jspå och metadata) till filen tar. gz och ändra fil namns tillägget till \<XXX> . HDP, där är \<XXX> namnet på plugin-programmet.

### <a name="research"></a>Söker 

Innan du börjar ska du kontrol lera att du:

- Läs protokoll specifikationen om det är tillgängligt.

- Ta reda på vilka protokoll fält du planerar att extrahera.

- Ha planerat dina mappnings mål.

## <a name="about-plugin-files"></a>Om plugin-filer 

Tre filer definieras under utvecklings processen.

### <a name="json-configuration-file-required"></a>JSON-konfigurationsfil (krävs) 

Den här filen bör definiera insamlings-ID och deklarationer, beroenden, integrerings krav, validerings parametrar och mappnings definitioner för att översätta värden till namn, siffror till text. Mer information finns i följande länkar:

- [Förbereda konfigurations filen (JSON)](#prepare-the-configuration-file-json)

- [Förbereda implementerings kod valideringar](#prepare-implementation-code-validations)

- [Extrahera metadata för enhet](#extract-device-metadata)

- [Ansluta till en indexerings tjänst (bas linje)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Implementerings kod: C++ (krävs)

Implementerings koden (CPP) tolkar rå trafik och mappar den till värden som tjänster, klasser och funktions koder. Den extraherar skikt fälten och mappar dem till deras index namn från filerna för JSON-konfiguration. De fält som ska extraheras från CPP definieras i konfigurations filen. Mer information finns i [förbereda implementerings kod (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Mappar filer (valfritt)

Du kan anpassa plugin-text för att uppfylla behoven i din företags miljö.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migreringsarkivet":::

Du kan definiera och uppdatera mappning av filer för att uppdatera text utan att ändra koden. Varje fil kan mappa ett eller flera fält:

  - Mappning av fält värden till namn, till exempel 1: Återställ, 2: Start, 3: stoppa.

  - Mappa text för att stödja flera språk.

Mer information finns i [create Mapping Files (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Skapa ett insektors-plugin-program (översikt)

1. Läs avsnittet [om miljö och installation](#about-the-environment-and-setup) .

2.  [Förbered implementerings koden (C++)](#prepare-the-implementation-code-c). Kopiera **template.jspå** filen och redigera den så att den passar dina behov. Ändra inte nycklarna. 

3. [Förbered konfigurations filen (JSON)](#prepare-the-configuration-file-json). Kopiera filen **Template. cpp** och implementera en override-metod. Mer information finns i [Horisont::p rotocol:: BaseParser](#horizonprotocolbaseparser) för mer information.

4. [Förbered implementerings kod valideringar](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Förbereda implementerings koden (C++)

CPP-filen är en parser som ansvarar för:

- Verifierar paket huvudet och nytto lasten (till exempel rubrik längd eller nytto Last struktur).

- Extraherar data från rubriken och nytto lasten i definierade fält.

- Implementering av konfigurerade fält extrahering av JSON-filen.

### <a name="what-to-do"></a>Vad bör jag göra

Kopiera filen Template **. cpp** och implementera en override-metod. Mer information finns i [Horisont::p rotocol:: BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Exempel på grundläggande C++-mall 

Det här avsnittet innehåller grundläggande protokoll mal len, med standard funktioner för ett exempel på ett IoT-protokoll.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Beskrivning av grundläggande C++-mall  

Det här avsnittet innehåller en grundläggande protokoll mal len med en beskrivning av standard funktioner för ett exempel på ett protokoll för IoT-horisont. 

### <a name="include-pluginpluginh"></a>#include "plugin/plugin. h"

Den definition som används av plugin-programmet. Rubrik filen innehåller allt som krävs för att slutföra utvecklingen.

### <a name="horizonprotocolbaseparser"></a>Horisont::p rotocol:: BaseParser

Kommunikations gränssnittet mellan horisont infrastrukturen och plugin-skiktet. Mer information finns i [Horisont arkitektur](#horizon-architecture) för en översikt över skikten.

ProcessLayer är den metod som används för att bearbeta data.

- Den första parametern i funktions koden är det bearbetnings verktyg som används för att hämta data som tidigare har bearbetats, skapa nya fält och lager.

- Den andra parametern i funktions koden är den aktuella data som skickas från föregående parser.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Använd för att skapa instansen av parsern.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Exempel på protokoll funktion kod 

Det här avsnittet innehåller ett exempel på hur kod numret (2 byte) och meddelande längden (4 byte) extraheras.

Detta görs enligt vad som anges i JSON-konfigurationsfilen, vilket innebär att om protokollet är *lite endian* och sensorn körs på en dator med lite endian, konverteras den.

Ett lager skapas också för att lagra data. Använd *fieldsManager* från bearbetnings-utils-filen för att skapa nya fält. Ett fält kan bara ha en av följande typer: *sträng*, *nummer*, *rå data*, *matris* (av en speciell typ) eller *komplex*. Det här lagret kan innehålla ett tal, en rå data eller en sträng med ID.

I exemplet nedan extraheras följande två fält:

- `function_code_number`

- `headerLength`

Ett nytt lager skapas och det extraherade fältet kopieras till det.

Exemplet nedan beskriver en speciell funktion, som är huvud logiken som implementeras för plugin-bearbetning.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Relaterat JSON-fält 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="Relaterat JSON-fält.":::

## <a name="prepare-the-configuration-file-json"></a>Förbereda konfigurations filen (JSON)

Horisont-SDK: n använder standard JavaScript Object Notation (JSON), ett enkelt format för lagring och överföring av data och kräver inte företagsspecifika skript språk.

Det här avsnittet beskriver minimala JSON-konfigurationsinställningar, den relaterade strukturen och innehåller en exempel konfigurations fil som definierar ett protokoll. Det här protokollet är automatiskt integrerat med enhets identifierings tjänsten.

## <a name="file-structure"></a>Filstruktur

Exemplet nedan beskriver fil strukturen.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="Exemplet på fil strukturen.":::

### <a name="what-to-do"></a>Vad bör jag göra

Kopiera mallfilen `config.json` och redigera den så att den passar dina behov. Ändra inte nyckeln. Nycklar markeras i rött i [exempel filen JSON-konfigurationsfilen](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Fil namns krav 

JSON-konfigurationsfilen måste sparas som `config.json` .

### <a name="json-configuration-file-fields"></a>Fält för JSON-konfigurationsfilen

I det här avsnittet beskrivs de JSON-konfigurations fält som du ska definiera. Ändra inte fält *Etiketter*.

### <a name="basic-parameters"></a>Grundläggande parametrar

I det här avsnittet beskrivs grundläggande parametrar.

| Parameter etikett | Beskrivning | Typ |
|--|--|--|
| **ID** | Namnet på protokollet. Ta bort standard namnet och Lägg till namnet på ditt protokoll som det visas. | Sträng |
| **endianess** | Definierar hur data för flera byte kodas. Använd bara termen "liten" eller "Big". Hämtas från protokoll specifikationen eller trafik registreringen | Sträng |
| **sanity_failure_codes** | Detta är de koder som returneras från parsern om det finns en Sanity konflikt angående identiteten för koden. Se magic number verifiering i C++-avsnittet. | Sträng |
| **malformed_codes** | Detta är koder som har identifierats korrekt, men ett fel har upptäckts. Till exempel om fält längden är för kort eller lång, eller om ett värde är ogiltigt. | Sträng |
| **dissect_as** | En matris som definierar var den angivna protokoll trafiken ska tas emot. | TCP/UDP, Port osv. |
| **fält** | Deklarationen för vilka fält som ska extraheras från trafiken. Varje fält har sitt eget ID (namn) och typ (numerisk, sträng, rå data, matris, komplex). Till exempel är fält [funktionen](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) som extraheras i implementerings parsern-filen. Fälten som skrivs i konfigurations filen är de enda som kan läggas till i lagret. |  |

### <a name="other-advanced-fields"></a>Andra avancerade fält 

I det här avsnittet beskrivs andra fält.

| Parameter etikett | Description |
|-----------------|--------|
| **lista över tillåtna** | Du kan indexera protokoll värden och visa dem i data utvinnings rapporter. Dessa rapporter återspeglar nätverks bas linjen. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Ett exempel på data utvinnings vyn."::: <br /> Mer information finns i [ansluta till en indexerings tjänst (bas linje)](#connect-to-an-indexing-service-baseline) för mer information. |
| **inbyggd program vara** | Du kan extrahera information om inbyggd program vara, definiera index värden och utlösa varningar för inbyggd program vara för plugin-protokollet. Mer information finns i [extrahera firmware-data](#extract-firmware-data) för mer information. |
| **value_mapping** | Du kan anpassa plugin-text för att uppfylla behoven i din företags miljö genom att definiera och uppdatera mappnings filen. Till exempel mappas till språkfiler. Det går lätt att implementera ändringar i text utan att ändra eller påverka koden. Mer information finns i [create Mapping Files (JSON)](#create-mapping-files-json) för information. |

## <a name="sample-json-configuration-file"></a>Exempel på JSON-konfigurationsfil

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Förbereda implementerings kod valideringar

I det här avsnittet beskrivs implementering av kod validerings funktioner i C++ och innehåller exempel kod. Det finns två validerings nivåer:

- Sanity.

- Felaktig kod.

Du behöver inte skapa någon validerings kod för att skapa ett fungerande plugin-program. Om du inte förbereder validerings koden kan du granska sensor data utvinnings rapporter som en indikation på lyckad bearbetning.

Fält värden kan mappas till texten i mappade filer och sömlöst uppdateras utan att det påverkar bearbetningen.

## <a name="sanity-code-validations"></a>Sanity kod valideringar 

Detta validerar att det överförda paketet matchar protokollets validerings parametrar, vilket hjälper dig att identifiera protokollet i trafiken.

Använd till exempel de första 8 byten som *Magic number*. Om Sanity Miss lyckas returneras ett Sanity fel svar.

Exempel:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Om andra relevanta plugin-program har distribuerats kommer paketet att verifieras mot dem.

## <a name="malformed-code-validations"></a>Felaktig kod validering 

Felformulerade verifieringar används när protokollet har verifierats positivt.

Om det uppstår ett problem med att bearbeta paketen baserat på protokollet returneras ett felsvar.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="felaktig kod":::

## <a name="c-sample-with-validations"></a>C++-exempel med verifieringar

Enligt funktionen utförs processen på det sätt som visas i exemplet nedan.

### <a name="function-20"></a>Funktion 20 

- Den bearbetas som inbyggd program vara.

- Fälten läses enligt funktionen.

- Fälten läggs till i lagret.

### <a name="function-10"></a>Funktion 10 

- Funktionen innehåller en annan sub-funktion, som är en mer speciell åtgärd

- Underfunktionen läses och läggs till i lagret.

När detta är klart slutförs bearbetningen. Returvärdet anger om DISSEKTOR lagret har bearbetats. Om det var så blir lagret användbart.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Extrahera metadata för enhet

Du kan extrahera följande metadata på till gångar:

  - `Is_distributed_control_system` -Anger om protokollet är en del av ett distribuerat kontroll system. (exempel: SCADA-protokollet ska vara falskt)

  - `Has_protocol_address` – Anger om det finns en protokoll adress; den speciella adressen för det aktuella protokollet, t. ex. MODBUS enhets identifierare

  - `Is_scada_protocol` -Anger om protokollet är speciellt för nätverksbaserade nätverk

  - `Is_router_potential` -Anger om protokollet används huvudsakligen av routrar. Till exempel LLDP, CDP eller STP.

För att uppnå detta måste JSON-konfigurationsfilen uppdateras med hjälp av egenskapen metadata.

## <a name="json-sample-with-metadata"></a>JSON-exempel med metadata 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Extrahera programmerings kod 

När programmerings händelsen inträffar kan du extrahera kod innehållet. Med det extraherade innehållet kan du:

- Jämför kod fil innehåll i olika programmerings händelser.

- Utlös en avisering om otillåten programmering.  

- Utlös en händelse för mottagning av programmerings kod fil.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Program ändrings loggen.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Visa programmeringen genom att klicka på knappen.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="Den otillåtna PLC-programmerings aviseringen.":::

För att uppnå detta måste JSON-konfigurationsfilen uppdateras med hjälp av `code_extraction` egenskapen. 

### <a name="json-configuration-fields"></a>Fält för JSON-konfiguration 

I det här avsnittet beskrivs fält för JSON-konfiguration. 

- **metodsignatur**

  Anger hur de programmerade filerna tas emot. 

  ALLA (varje programmerings åtgärd gör att alla kod filer tas emot även om det finns filer utan ändringar).

- **file_type**  

  Anger kodens innehålls typ.  

  TEXT (varje kod fil innehåller text information).

- **code_data_field**
  
  Anger det implementerings fält som ska användas för att tillhandahålla kod innehållet.

  Sidfält.

- **code_name_field**

  Anger det implementerings fält som ska användas för att ange namnet på kodnings filen.

  Sidfält.

- **size_limit**

  Anger storleks gränsen för varje kodnings fil innehåll i byte, om en kod fil överskrider den angivna gränsen kommer den att tas bort. Om det här fältet inte anges kommer standardvärdet 15 000 000 som är 15 MB.

  Många.

- **metadatatjänst**

  Anger ytterligare information om en kod fil.

  Matris som innehåller objekt med två egenskaper:

    - Name (sträng)-anger att XSense för närvarande endast stöder nyckeln username.
 
    - värde (fält) – anger det implementerings fält som ska användas för att tillhandahålla metadata.

## <a name="json-sample-with-programming-code"></a>JSON-exempel med programmerings kod

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Anpassade Horisont varningar

Vissa protokoll funktions koder kan tyda på ett fel. Om protokollet exempelvis styr en behållare med en speciell kemikalie som måste lagras alltid vid en bestämd temperatur. I det här fallet kan det finnas funktions kod som indikerar ett fel i termometern. Om funktions koden till exempel är 25 kan du utlösa en avisering i webb konsolen som indikerar att det finns ett problem med behållaren. I så fall kan du definiera varningar för djup paket.

Lägg till parametern **Alerts** i `config.json` plugin-programmet.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Fält för JSON-konfiguration

I det här avsnittet beskrivs fält för JSON-konfiguration. 

| Fältnamn | Description | Möjliga värden |
|--|--|--|
| **ID** | Representerar ett enda varnings-ID. Det måste vara unikt i den här kontexten. | Numeriskt värde 0-10000 |
| **meddelande** | Information som visas för användaren. I det här fältet kan du använda olika fält. | Använd alla fält från protokollet eller något av de lägre lager protokollen. |
| **avdelning** | Aviserings rubriken |  |
| **expression** | När du vill att den här aviseringen ska visas. | Använd ett numeriskt fält som finns i lägre lager eller det aktuella lagret.</br></br> Varje fält måste placeras i för att `{}` SDK: n ska kunna identifiera det som ett fält är de logiska operatörer som stöds</br> = =-Equal</br> <=-mindre än eller lika med</br> >=-mer än eller lika med</br> >-fler än</br> <-mindre än</br> ~ =-Inte lika med |

## <a name="more-about-expressions"></a>Mer om uttryck

Varje gång som Horisont SDK anropar uttrycket och det blir *Sant* utlöses en avisering i sensorn.

Flera uttryck kan inkluderas under samma avisering. Exempel:

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Det här uttrycket validerar bara funktions koden när paketets IPv4 src är 10.10.10.10. Som är en RAW-representation av IP-adressen i en numerisk representation.

Du kan använda `and` eller för `or` att ansluta uttryck.

## <a name="json-sample-custom-horizon-alerts"></a>JSON-exempel, anpassade Horisont varningar

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Ansluta till en indexerings tjänst (bas linje)

Du kan indexera protokoll värden och visa dem i data utvinnings rapporter.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="En vy över data utvinnings alternativet.":::

Dessa värden kan senare mappas till specifika texter, till exempel mappnings nummer som text eller lägga till information, på valfritt språk.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migreringsarkivet":::

Mer information finns i [create Mapping Files (JSON)](#create-mapping-files-json) för information.

Du kan också använda värden från protokoll som tidigare har parsats för att extrahera ytterligare information.

För värdet, som baseras på TCP, kan du till exempel använda värdena från IPv4-skiktet. Från det här lagret kan du extrahera värden som källa för paketet och målet.

För att uppnå detta måste JSON-konfigurationsfilen uppdateras med hjälp av `whitelist` egenskapen.

## <a name="allow-list-data-mining-fields"></a>Fält för att tillåta List (Data utvinning)

Följande lista över tillåtna fält är tillgängliga:

- Namn – namnet som används för indexering.

- alert_title – en kort, unik rubrik som förklarar evenemanget.

- alert_text – ytterligare information

Du kan lägga till flera tillåtna listor, vilket möjliggör fullständig flexibilitet i indexeringen.

## <a name="json-sample-with-indexing"></a>JSON-exempel med indexering 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Extrahera firmware-data

Du kan extrahera information om inbyggd program vara, definiera index värden och utlösa varningar för inbyggd program vara för plugin-protokollet. Exempel:

- Extrahera den inbyggda program varans modell eller version. Den här informationen kan användas ytterligare för att identifiera CVEs.

- Utlös en avisering när en ny version av inbyggd program vara upptäcks.

För att uppnå detta måste JSON-konfigurationsfilen uppdateras med hjälp av egenskapen firmware.

## <a name="firmware-fields"></a>Fält för inbyggd program vara

I det här avsnittet beskrivs konfigurations fälten för JSON inbyggd program vara.

- **Namn**
  
  Anger hur fältet presenteras i sensor konsolen.

- **värde**

 Anger det implementerings fält som ska användas för att tillhandahålla data. 

- **firmware_index:**

  Välj en:  
  - **modell**: enhets modellen. Aktiverar identifiering av CVEs.
  - **serie**: enhetens serie nummer. Serie numret är inte alltid tillgängligt för alla protokoll. Det här värdet är unikt per enhet.
  - **rack**: anger rack-ID, om enheten ingår i ett rack.
  - **fack**: plats identifieraren, om enheten är en del av en rack.  
  - **module_address**: används för att presentera en hierarki om modulen kan visas bakom en annan enhet. Gäller istället för en kombination av rack platser, vilket är en enklare presentation.  
  - **firmware_version**: anger enhets version. Aktiverar identifiering av CVEs.
  - **alert_text**: anger text som beskriver avvikelser för inbyggd program vara, till exempel versions ändringar.  
  - **index_by**: anger fälten som används för att identifiera och indexera enheten. I exemplet nedan identifieras enheten med dess IP-adress. I vissa protokoll kan ett mer komplext index användas. Till exempel om en annan enhet är ansluten och du vet hur du extraherar den interna sökvägen. Till exempel kan MODBUS enhets-ID: t användas som en del av indexet som en annan kombination av IP-adress och enhets-ID.
  - **firmware_fields**: anger vilka fält som är fält för enhetens metadata. I det här exemplet används följande: modell, revision och namn. Varje protokoll kan definiera egna inbyggda data för inbyggd program vara.

## <a name="json-sample-with-firmware"></a>JSON-exempel med inbyggd program vara 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Extrahera enhets attribut 

Du kan förbättra enheten information som är tillgänglig i enheten i inventering, Data utvinning och andra rapporter.

- Namn

- Typ

- Leverantör

- Operativsystem

För att uppnå detta måste JSON-konfigurationsfilen uppdateras med egenskapen **Properties** . 

Det kan du göra genom att skriva Basic-plugin-programmet och extrahera nödvändiga fält.

## <a name="properties-fields"></a>Egenskaps fält

I det här avsnittet beskrivs konfigurations fälten för JSON-egenskaper. 

**config_file** 

Innehåller fil namnet som definierar hur du bearbetar varje nyckel i `key` fälten. Själva konfigurations filen bör vara i JSON-format och inkluderas som en del av plugin-protokollets mapp.

Varje nyckel i JSON definierar den uppsättning åtgärder som ska utföras när du extraherar den här nyckeln från ett paket.

Varje nyckel kan ha:

- **Paket data** – anger de egenskaper som skulle uppdateras baserat på de data som extraheras från paketet (det implementerings fält som används för att tillhandahålla dessa data).

- **Statiska data** – anger en fördefinierad uppsättning `property-value` åtgärder som ska uppdateras.

De egenskaper som kan konfigureras i den här filen är: 

- **Namn** – anger enhetens namn.

- **Typ** – anger enhets typ.

- **Leverantör** – anger enhets leverantör.

- **Beskrivning** – anger enhetens inbyggda enhets modell (lägre prioritet än modell).

- **Opera ting system** – anger enhetens operativ system.

### <a name="fields"></a>Fält

| Fält | Beskrivning |
|--|--|
| key | Anger nyckeln. |
| värde | Anger det implementerings fält som ska användas för att tillhandahålla data. |
| is_static_key | Anger om `key` fältet härleds som ett värde från paketet eller är det ett fördefinierat värde. |

### <a name="working-with-static-keys-only"></a>Arbeta med enbart statiska nycklar

Om du arbetar med statiska nycklar behöver du inte konfigurera `config.file` . Du kan bara konfigurera JSON-filen.

## <a name="json-sample-with-properties"></a>JSON-exempel med egenskaper 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Skapa mappade filer (JSON)

Du kan anpassa plugin-text för att uppfylla behoven i företags miljön genom att definiera och uppdatera mappade filer. Det går lätt att implementera ändringar i text utan att ändra eller påverka koden. Varje fil kan mappa ett eller flera fält.

- Mappning av fält värden till namn, till exempel 1: Återställ, 2: Start, 3: stoppa.

- Mappa text för att stödja flera språk.

Det går att definiera två typer av mappnings filer.

  - [Enkel mappnings fil](#simple-mapping-file).

  - [Fil för beroende mappning](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="eter-net":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="En vy över aviseringar som inte hanteras.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="En lista över kända princip överträdelser.":::

## <a name="file-naming-and-storage-requirements"></a>Fil namns-och lagrings krav 

Mappade filer ska sparas under metadata-mappen.

Namnet på filen ska matcha JSON-konfigurationens fil-ID.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Ett exempel på en JSON-konfigurationsfil.":::

## <a name="simple-mapping-file"></a>Enkel mappnings fil 

Följande exempel visar en grundläggande JSON-fil som ett nyckel värde.

När du skapar en lista över tillåtna och innehåller en eller flera av de mappade fälten. Värdet konverteras från ett tal, en sträng eller någon typ, i till formaterad text som visas i mappnings filen.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Fil för beroende mappning 

Om du vill ange att filen är en beroende fil lägger du till nyckelordet `dependency` i mappnings konfigurationen.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

Filen innehåller en mappning mellan beroende fältet och funktions fältet. Till exempel mellan funktionen och sub-funktionen. Funktionen sub ändras enligt den angivna funktionen.

I listan över tillåtna som tidigare har kon figurer ATS finns det ingen beroende konfiguration, som du ser nedan.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

Beroendet kan baseras på ett angivet värde eller i ett fält. I exemplet nedan baseras det på ett fält. Om du baserar det på ett värde definierar du det extraherings värde som ska läsas från mappnings filen.

I exemplet nedan visas beroendet som efter samma värde för fältet.

I sub-funktionen fem ändras innebörden till exempel baserat på funktionen.

  - Om det är en Read-funktion, innebär det att fem innebär att läsa minnet.

  - Om det är en Skriv funktion används samma värde för att läsa från en fil.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Exempelfil

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>JSON-exempel med mappning

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Paketera, ladda upp och övervaka plugin-programmet 

I det här avsnittet beskrivs hur du

  - Paketera ditt plugin-program.

  - Ladda upp ditt plugin-program.

  - Övervaka och Felsök plugin-programmet för att utvärdera hur bra det presterar.

Paketera plugin-programmet:

1. Lägg till **artefakten** (kan vara, bibliotek, config.jseller metadata) till en `tar.gz` fil.

1. Ändra fil tillägget till \<XXX.hdp> , där är \<XXX> namnet på plugin-programmet.

Så här loggar du in på Horisont konsolen:

1.  Logga in i sensor CLI som administratör, CyberX eller support användare.

2.  I filen: `/var/cyberx/properties/horizon.properties` ändra egenskapen **UI. enabled** till **True** ( `horizon.properties:ui.enabled=true` ).

3.  Logga in på sensor konsolen.

4.  Välj alternativet **Horisont** på huvud menyn.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Välj alternativet för Horisont i det vänstra fönstret.":::

    Horisont konsolen öppnas.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="En vy av Horisont konsolen och alla dess plugin-program.":::

## <a name="plugins-pane"></a>Fönstret plugin-program

Rutan plugin-fönster visar:

  - Plugin-program för infrastruktur: infrastruktur-plugin-program installeras som standard med Defender för IoT.

  - Programpluginer: programplugin-program installeras som standard med Defender för IoT och andra plugin-program som har utvecklats av Defender för IoT eller externa utvecklare.
    
Aktivera och inaktivera plugin-program som har laddats upp med växlingen.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CIP växlar.":::

### <a name="uploading-a-plugin"></a>Laddar upp ett plugin-program

När du har skapat och paketerat ditt plugin-program kan du ladda upp det till Defender för IoT-sensorn. För att få full täckning av ditt nätverk bör du ladda upp plugin-programmet till varje sensor i din organisation.

Att ladda upp:

1.  Logga in på sensorn.


2. Välj **Överför**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Ladda upp dina plugin-program.":::

3. Bläddra till ditt plugin-program och dra det till dialog rutan plugin-program. Kontrol lera att prefixet är `.hdp` . Plugin-programmet läses in.

## <a name="plugin-status-overview"></a>Översikt över plugin-status 

**Översikts** fönstret i Horisont konsolen innehåller information om det plugin-program som du överförde och låter dig inaktivera och aktivera dem.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Översikt över Horisont konsolen.":::

| Fält | Beskrivning |
|--|--|
| Program | Namnet på plugin-programmet som du laddade upp. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="Växeln på och av."::: | Aktivera **eller** **inaktivera** plugin-programmet. Defender för IoT hanterar inte protokoll trafik som definierats i plugin-programmet när du växlar plugin-programmet. |
| Tid | Tiden då data analyserades senast. Uppdateras var femte sekund. |
| PPS | Antal paket per sekund. |
| Bandbredd | Genomsnittlig bandbredd som identifierats under de senaste 5 sekunderna. |
| Malforms | Felformulerade verifieringar används när protokollet har verifierats positivt. Om det uppstår ett problem med att bearbeta paketen baserat på protokollet returneras ett felsvar.   <br><br>Den här kolumnen anger antalet malform-fel under de senaste 5 sekunderna. Mer information finns i avsnittet om [felaktig kod validering](#malformed-code-validations) . |
| Varningar | Paketen matchar strukturen och specifikationen, men det uppstår ett oväntat beteende baserat på varnings konfigurationen för plugin-programmet. |
| Fel | Antal paket som inte godkänts i Basic Protocol-validering. Verifierar att paketet matchar protokoll definitionerna. Siffran som visas här indikerar att antalet fel som har upptäckts under de senaste 5 sekunderna. Mer information finns i [Sanity Code](#sanity-code-validations) retails for details. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="Ikonen övervakare."::: | Läs mer om malform och varningar som har identifierats för ditt plugin-program. |

## <a name="plugin-details"></a>Information om plugin

Du kan övervaka programmets beteende i real tid genom att analysera antalet *Malform* och *varningar* som har identifierats för ditt plugin-program. Det finns ett alternativ för att låsa skärmen och export för vidare undersökning

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Skärmen SNMP Monitor.":::

Övervaka:

Välj knappen övervaka för ditt plugin-program från översikten.

Nästa steg

Konfigurera ditt [Horisont-API](references-horizon-api.md)
