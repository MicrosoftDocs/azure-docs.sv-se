---
title: Azure IoT Connector för FHIR (för hands version) – fel söknings guide och anvisningar
description: Så här felsöker du vanliga Azure IoT Connector för FHIR (för hands version) fel meddelanden och villkor och kopierar mappnings filer
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 3eef7354f7197f60e8abd1b5522393bf00a6203f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020589"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Fel söknings guide för Azure IoT Connector för FHIR (för hands version)

Den här artikeln innehåller steg för fel sökning av vanliga Azure IoT-anslutningar för snabba hälso samverkans resurser (FHIR&#174;) * fel meddelanden och villkor.  

Du lär dig också hur du skapar kopior av Azure IoT Connector för FHIR Conversion mappnings-JSON (till exempel: enhet och FHIR).  

Du kan använda konverterings mappnings-JSON-kopior för att redigera och arkivera utanför Azure Portal.  

> [!TIP]
> Om du kommer att öppna ett [Azures tekniska support](https://azure.microsoft.com/support/create-ticket/) ärende för Azure IoT-ANSLUTNINGSPROGRAMMET för FHIR, måste du ta med kopior av konverterings-JSON-JSON för att hjälpa till med fel söknings processen.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Mappning av JSON-mall för enhets-och FHIR konvertering för Azure IoT Connector för FHIR (för hands version)
I det här avsnittet får du lära dig om validerings processen som Azure IoT Connector för FHIR utför för att validera JSON-mallarna för enhets-och FHIR konverterings mappning innan de tillåts att sparas för användning.  Dessa element krävs i JSON för enhets-och FHIR-konverterings mappning.

**Enhets mappning**

|Element|Obligatorisk|
|:-------|:------|
|Typnamn|Sant|
|TypeMatchExpression|Sant|
|DeviceIdExpression|Sant|
|TimestampExpression|Sant|
|Värden []. Värdets namn|Sant|
|Värden []. ValueExpression|Sant|

> [!NOTE]
> Värden []. ValueName och värden []. ValueExpression
>
> De här elementen krävs endast om du har en värde post i matrisen. det är inte tillåtet att mappa några värden. Detta används när Telemetrin som skickas är en händelse. Exempel: när en Wearable IoMT-enhet placeras eller tas bort. Elementen (-erna) har inga värden förutom ett namn som Azure IoT Connector för FHIR matchar och genererar. I FHIR-konverteringen mappar Azure IoT Connector för FHIR till ett kod möjligt koncept baserat på den semantiska typen – inga faktiska värden fylls i.

**FHIR-mappning**

|Element|Obligatorisk|
|:------|:-------|
|Typnamn|Sant|

> [!NOTE]
> Detta är det enda obligatoriska mappnings elementet för FHIR verifieras just nu.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Fel meddelanden och korrigeringar för Azure IoT Connector för FHIR (för hands version)

|Meddelande|Visat|Villkor|Åtgärda| 
|-------|---------|---------|---|
|Ogiltigt mappnings namn, mappnings namn ska vara enhets-eller FHIR.|API|Den angivna mappnings typen är inte enhets-eller FHIR.|Använd en av de två mappnings typer som stöds (till exempel: enhet eller FHIR).|
|Verifieringen misslyckades. Nödvändig information saknas eller är ogiltig.|API och Azure Portal|Försök att spara en konverterings mappning saknar nödvändig information eller element.|Lägg till information om konverterings mappning som saknas eller element och försök att spara konverterings mappningen igen.|
|Återskapande av nyckel parametrar har inte definierats.|API|Återskapa nyckel förfrågan.|Inkludera parametrarna i begäran om att skapa en nyckel.|
|Det maximala antalet IoT Connector-instanser som kan tillhandahållas i den här prenumerationen har uppnåtts.|API och Azure Portal|Prenumerations kvoten för Azure IoT Connector för FHIR har nåtts (standard är (2) per prenumeration).|Ta bort en av de befintliga instanserna av Azure IoT Connector för FHIR.  Använd en annan prenumeration som inte har nått prenumerations kvoten.  Begär en ökning av prenumerations kvot.|
|Flytt av resurs stöds inte för IoT Connector aktiverat Azure API för FHIR-resursen.|API och Azure Portal|Försöker utföra en flyttnings åtgärd på en Azure API för FHIR-resurs som har en eller flera instanser av Azure IoT-anslutaren för FHIR.|Ta bort befintliga instanser av Azure IoT Connector för FHIR för att utföra flytt åtgärden.|
|IoT-kopplingen har inte tillhandahållits.|API|Försök att använda underordnade tjänster (anslutningar & mappningar) när överordnad (Azure IoT Connector för FHIR) inte har etablerats.|Etablera en Azure IoT-anslutning för FHIR.|
|Begäran stöds inte.|API|En speciell API-begäran stöds inte.|Använd rätt API-begäran.|
|Kontot finns inte.|API|Försök att lägga till en Azure IoT-anslutning för FHIR och Azure API för FHIR-resursen finns inte.|Skapa Azure API för FHIR-resursen och försök sedan igen.|
|Azure API för FHIR Resource FHIR-versionen stöds inte för IoT Connector.|API|Försök att använda en Azure IoT-anslutning för FHIR med en inkompatibel version av Azure API för FHIR-resursen.|Skapa en ny Azure API för FHIR-resurs (version R4) eller Använd en befintlig Azure API för FHIR-resurs (version R4).

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Varför visas inte min Azure IoT Connector för FHIR-data (för hands version) i Azure API för FHIR?

|Potentiella problem|Korrigeringar|
|----------------|-----|
|Data bearbetas fortfarande.|Data skickas till Azure-API: et för FHIR i batchar (var 15: e minut).  Det är möjligt att data fortfarande bearbetas och att ytterligare tid krävs för att data ska sparas i Azure API för FHIR.|
|Mappnings-JSON för enhets konvertering har inte kon figurer ATS.|Konfigurera och spara omvandlings-JSON för enhets konvertering.|
|FHIR Conversion mappnings-JSON har inte kon figurer ATS.|Konfigurera och spara FHIR Conversion Mapping JSON.|
|Enhets meddelandet innehåller inte ett förväntat uttryck som definierats i enhets mappningen.|Verifiera JsonPath-uttryck som definierats i enhets mappningens matchnings-token som definierats i enhets meddelandet.|
|En enhets resurs har inte skapats i Azure API för FHIR (lösnings typ: endast sökning) *.|Skapa en giltig enhets resurs i Azure API för FHIR. Se till att enhets resursen innehåller en identifierare som matchar den enhets identifierare som anges i det inkommande meddelandet.|
|En patient resurs har inte skapats i Azure API för FHIR (lösnings typ: endast sökning) *.|Skapa en giltig patient resurs i Azure API för FHIR.|
|Referensen för enheten. patient har inte angetts eller så är referensen ogiltig (lösnings typ: endast sökning) *.|Kontrol lera att enhets resursen innehåller en giltig [referens](https://www.hl7.org/fhir/device-definitions.html#Device.patient) till en patient resurs.| 

* Referens [snabb start: Distribuera Azure IoT Connector (för hands version) med Azure Portal](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) för en funktionell beskrivning av Azure IoT Connector för FHIR lösnings typer (till exempel lookup eller Create).

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>Använd mått för att felsöka problem i Azure IoT Connector för FHIR (för hands version)

Azure IoT Connector för FHIR genererar flera mått för att ge insikter om data flödes processen. Ett av de mått som stöds kallas för *totala fel*, vilket ger antalet fel som inträffar i en instans av Azure IoT Connector för FHIR.

Varje fel loggas med ett antal tillhör ande egenskaper. Varje egenskap ger en annorlunda aspekt av felet, som kan hjälpa dig att identifiera och felsöka problem. I det här avsnittet listas olika egenskaper som har registrerats för varje fel i måttet för *totala antalet fel* och möjliga värden för dessa egenskaper.

> [!NOTE]
> Du kan navigera till måttet *Total Error* för en instans av Azure IoT Connector för FHIR (för hands version) enligt beskrivningen på [mått sidan för Azure IoT Connector för FHIR (för hands version)](iot-metrics-display.md).

Klicka på diagrammet *Totalt antal fel* och klicka sedan på knappen *Lägg till filter* för att segmentera och sortera fel måttet med någon av egenskaperna som anges nedan.

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>Åtgärden som utförs av Azure IoT Connector för FHIR (för hands version)

Den här egenskapen representerar åtgärden som utförs av IoT Connector när felet har inträffat. En åtgärd representerar vanligt vis data flödes fasen medan ett enhets meddelande bearbetas. Här är listan över möjliga värden för den här egenskapen.

> [!NOTE]
> Du kan läsa mer om olika stadier i data flödet i Azure IoT Connector för FHIR (för hands version) [här](iot-data-flow.md).

|Data flödes steg|Beskrivning|
|---------------|-----------|
|Installation|Åtgärd som är speciell för att konfigurera din instans av IoT Connector|
|Normaliserings|Data flödes fas där enhets data får normaliseras|
|Baserat|Data flödes fas där normaliserade data grupperas|
|FHIRConversion|Steg för data flöde där grupperade normaliserade data omvandlas till en FHIR-resurs|
|Okänt|Åtgärds typen är okänd när ett fel inträffade|

### <a name="the-severity-of-the-error"></a>Felets allvarlighets grad

Den här egenskapen representerar allvarlighets graden för det inträffade felet. Här är listan över möjliga värden för den här egenskapen.

|Allvarlighetsgrad|Beskrivning|
|---------------|-----------|
|Varning|Det finns vissa mindre problem i data flödes processen, men bearbetningen av enhets meddelandet stoppas inte|
|Fel|Bearbetningen av ett särskilt enhets meddelande har stött på ett fel och andra meddelanden kan fortsätta att köras som förväntat|
|Kritiskt|Det finns vissa system nivå problem med IoT Connector och inga meddelanden förväntas bearbeta|

### <a name="the-type-of-the-error"></a>Typ av fel

Den här egenskapen anger en kategori för ett angivet fel, vilket i princip representerar en logisk gruppering för liknande typ av fel. Här är listan över möjliga värden för den här egenskapen.

|Typ av fel|Beskrivning|
|----------|-----------|
|DeviceTemplateError|Fel som rör enhets mappnings mallar|
|DeviceMessageError|Fel uppstod vid bearbetning av ett enskilt enhets meddelande|
|FHIRTemplateError|Fel som rör FHIR mappar|
|FHIRConversionError|Fel uppstod när ett meddelande skulle omvandlas till en FHIR-resurs|
|FHIRResourceError|Fel som rör befintliga resurser i FHIR-servern som IoT Connector refererar till|
|FHIRServerError|Fel som uppstår vid kommunikation med FHIR-servern|
|GeneralError|Alla andra typer av fel|

### <a name="the-name-of-the-error"></a>Namnet på felet

Den här egenskapen anger namnet på ett angivet fel. Här är listan över alla fel namn med beskrivning och tillhör ande feltyper, allvarlighets grad och data flödes steg.

|Fel namn|Beskrivning|Fel typ (er)|Allvarlighets grad för fel|Data flödes steg|
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|Ett fel uppstod när flera patient-eller enhets resurser påträffades i FHIR-servern för de respektive identifierare som finns i enhets meddelandet|FHIRResourceError|Fel|FHIRConversion|
|TemplateNotFoundException|En mall för enhets-eller FHIR har inte kon figurer ATS med instansen av IoT Connector|DeviceTemplateError, FHIRTemplateError|Kritiskt|Normalisering, FHIRConversion|
|CorrelationIdNotDefinedException|Korrelations-ID har inte angetts i enhets mappnings mal len. CorrelationIdNotDefinedException är ett villkorligt fel som bara uppstår när FHIR-observationen måste gruppera enhets måtten med ett korrelations-ID, men det har inte kon figurer ATS korrekt|DeviceMessageError|Fel|Normaliserings|
|PatientDeviceMismatchException|Felet uppstår när enhets resursen på FHIR-servern har en referens till en patient resurs, som inte överensstämmer med patient-ID: n som finns i meddelandet|FHIRResourceError|Fel|FHIRConversionError|
|PatientNotFoundException|Det gick inte att referera till FHIR-resursen som är kopplad till den enhets-ID som finns i enhets meddelandet. Obs! det här felet uppstår bara när IoT Connector-instansen konfigureras med *uppslags* matchnings typ|FHIRConversionError|Fel|FHIRConversion|
|DeviceNotFoundException|Det finns ingen enhets resurs på den FHIR-server som är associerad med enhets identifieraren som finns i enhets meddelandet|DeviceMessageError|Fel|Normaliserings|
|PatientIdentityNotDefinedException|Det här felet uppstår när uttryck för att parsa patient-ID från enhets meddelandet inte har kon figurer ATS i enhets mappnings mal len eller eftersom det inte finns någon identifierare i enhets meddelandet. Observera att det här felet bara uppstår när IoT-anslutningens lösnings typ är inställd på att *skapas*|DeviceTemplateError|Kritiskt|Normaliserings|
|DeviceIdentityNotDefinedException|Felet uppstår när uttryck för att parsa enhets identifierare från enhets meddelandet inte har kon figurer ATS i enhets mappnings mal len eller på att enhetens identifierare inte finns i enhets meddelandet|DeviceTemplateError|Kritiskt|Normaliserings|
|NotSupportedException|Ett fel uppstod när enhets meddelandet med format som inte stöds togs emot|DeviceMessageError|Fel|Normaliserings|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Skapa kopior av Azure IoT Connector för FHIR (förhands granskning) konverterings mappnings-JSON

Kopieringen av Azure IoT Connector för FHIR-mappar kan vara användbar för att redigera och arkivera utanför Azure Portal webbplats.

Mappnings filens kopior bör tillhandahållas till teknisk support för Azure när du öppnar ett support ärende för att under lätta fel sökningen.

> [!NOTE]
> JSON är det enda format som stöds för enhets-och FHIR-mappning av filer för tillfället.

> [!TIP]
> Lär dig mer om Azure IoT Connector för FHIR- [enhet och FHIR Conversion Mapping JSON](iot-mapping-templates.md)

1. Välj **"IoT Connector (förhands granskning)"** på den nedre vänstra sidan av Azure-API: t för FHIR resurs instrument panel i avsnittet **"tillägg"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Välj **"Connector"** som du vill kopiera konverterings-JSON från.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Den här processen kan också användas för att kopiera och spara innehållet i JSON-filen **"Konfigurera FHIR-mappning"** .

3. Välj **Konfigurera enhets mappning**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Välj innehållet i JSON och gör en kopierings åtgärd (till exempel: Välj CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Gör en Inklistrings åtgärd (till exempel: Välj Ctrl + v) i en ny fil i en redigerare (till exempel Visual Studio Code, Notepad) och spara filen med ett *. JSON-tillägg.

> [!TIP]
> Om du kommer att öppna ett [Azures tekniska support](https://azure.microsoft.com/support/create-ticket/) ärende för Azure IoT-ANSLUTNINGSPROGRAMMET för FHIR, måste du ta med kopior av konverterings-JSON-JSON för att hjälpa till med fel söknings processen.

## <a name="next-steps"></a>Nästa steg

Ta en titt på vanliga frågor om Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Vanliga frågor och svar om Azure IoT Connector för FHIR](fhir-faq.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version). FHIR är ett registrerat varumärke som tillhör HL7 och används med behörigheten för HL7.