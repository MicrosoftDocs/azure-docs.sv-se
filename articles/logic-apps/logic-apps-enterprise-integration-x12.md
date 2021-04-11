---
title: Skicka och ta emot X12-meddelanden för B2B
description: Exchange X12-meddelanden för B2B Enterprise integration-scenarier med hjälp av Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 87a2bcc386ec5688fadb68aabdd2e5239e205516
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077481"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-meddelanden för B2B Enterprise-integration i Azure Logic Apps med Enterprise-integrationspaket

Om du vill arbeta med X12-meddelanden i Azure Logic Apps kan du använda X12-anslutningen, som innehåller utlösare och åtgärder för att hantera X12-kommunikation. Mer information om EDIFACT-meddelanden finns i avsnittet om [Exchange EDIFACT-meddelanden](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic-app från vilken du vill använda X12-anslutaren och en utlösare som startar din Logic app-arbetsflöde. X12-anslutaren tillhandahåller endast åtgärder, inte utlösare. Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är associerat med din Azure-prenumeration och länkat till Logic-appen där du planerar att använda X12-anslutningen. Både din Logic app och ditt integrations konto måste finnas på samma plats eller i Azure-regionen.

* Minst två [handels partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som du redan har definierat i integrations kontot med X12-identitets kvalificeraren.

* De [scheman](../logic-apps/logic-apps-enterprise-integration-schemas.md) som ska användas för XML-verifiering som du redan har lagt till i ditt integrations konto. Om du arbetar med HIPAA-scheman kan du läsa mer i [HIPAA-scheman](#hipaa-schemas).

* Innan du kan använda X12-anslutningsprogrammet måste du skapa ett X12- [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md) mellan dina handels partner och lagra avtalet i ditt integrations konto. Om du arbetar med HIPAA-scheman (Health Insurance-och ansvars Act) måste du lägga till ett `schemaReferences` avsnitt i ditt avtal. Mer information finns i [HIPAA-scheman](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Ta emot inställningar

När du har angett avtals egenskaperna kan du konfigurera hur det här avtalet identifierar och hanterar inkommande meddelanden som du får från din partner genom detta avtal.

1. Under **Lägg till** väljer du **ta emot inställningar**.

1. Konfigurera dessa egenskaper utifrån ditt avtal med den partner som utbyter meddelanden med dig. **Mottagnings inställningarna** är ordnade i följande avsnitt:

   * [Identifierare](#inbound-identifiers)
   * [Bekräftelse](#inbound-acknowledgement)
   * [Scheman](#inbound-schemas)
   * [Kuvert](#inbound-envelopes)
   * [Kontroll nummer](#inbound-control-numbers)
   * [Valideringar](#inbound-validations)
   * [Interna inställningar](#inbound-internal-settings)

   För egenskaps beskrivningar, se tabellerna i det här avsnittet.

1. När du är klar, se till att spara inställningarna genom att välja **OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Ta emot inställningar – identifierare

![Identifiera egenskaper för inkommande meddelanden](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Egenskap | Beskrivning |
|----------|-------------|
| **ISA1 (kvalificering av autentisering)** | Det kvalificerande värde för auktorisering som du vill använda. Standardvärdet är **00 – det finns ingen autentiseringsinformation**. <p>**Obs**: om du väljer andra värden anger du ett värde för egenskapen **ISA2** . |
| **ISA2** | Värdet för auktoriseringsinformation som ska användas när **ISA1** -egenskapen inte är **00 – ingen autentiseringsinformation finns**. Det här egenskap svärdet måste innehålla minst ett alfanumeriskt tecken och högst 10. |
| **ISA3 (säkerhets kvalificerare)** | Det säkerhets kvalificerande värde som du vill använda. Standardvärdet är **00 – ingen säkerhets information finns**. <p>**Obs**: om du väljer andra värden anger du ett värde för egenskapen **ISA4** . |
| **ISA4** | Värdet för säkerhets information som ska användas när **ISA3** -egenskapen inte är **00 – ingen säkerhets information finns**. Det här egenskap svärdet måste innehålla minst ett alfanumeriskt tecken och högst 10. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Ta emot inställningar – bekräftelse

![Bekräftelse för inkommande meddelanden](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Egenskap | Beskrivning |
|----------|-------------|
| **TA1 förväntades** | Returnera en teknisk bekräftelse (TA1) till Interchange-avsändaren. |
| **Anl. förväntas** | Returnera en funktionell bekräftelse (FA) till Interchange-avsändaren. <p>För egenskapen **Anl-version** , baserat på schema versionen, väljer du bekräftelser för 997 eller 999. <p>Om du vill aktivera generering av AK2-slingor i funktions bekräftelser för godkända transaktions uppsättningar väljer du **Inkludera AK2/IK2-loop**. |

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Ta emot inställningar – scheman

![Scheman för inkommande meddelanden](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

I det här avsnittet väljer du ett [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) från [integrations kontot](./logic-apps-enterprise-integration-create-integration-account.md) för varje transaktions typ (ST01) och avsändar program (GS02). Den insamlade EDI-pipeline Disassemblerar det inkommande meddelandet genom att matcha värdena och schemat som du har angett i det här avsnittet med värdena för ST01 och GS02 i det inkommande meddelandet och med schemat för det inkommande meddelandet. När du har slutfört varje rad visas en ny tom rad automatiskt.

| Egenskap | Beskrivning |
|----------|-------------|
| **Version** | X12-versionen för schemat |
| **Transaktions typ (ST01)** | Transaktions typ |
| **Avsändar program (GS02)** | Avsändar programmet |
| **Schema** | Den schema fil som du vill använda |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Ta emot inställningar-kuvert

![Avgränsningar som ska användas i transaktions uppsättningar för inkommande meddelanden](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Egenskap | Beskrivning |
|----------|-------------|
| **ISA11-användning** | Avgränsaren som ska användas i en transaktions uppsättning: <p>- **Standard identifierare**: Använd en punkt (.) för decimal format i stället för decimal tecknet för det inkommande dokumentet i EDI-datapipeline. <p>- **Upprepnings avgränsare**: Ange avgränsare för upprepade förekomster av ett enkelt data element eller en upprepad data struktur. Till exempel används vanligt vis cirkumflex (^) som upprepnings avgränsare. För HIPAA-scheman kan du bara använda cirkumflex. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Ta emot inställningar-kontroll nummer

![Hantering av kontroll nummer dubbletter för inkommande meddelanden](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Egenskap | Beskrivning |
|----------|-------------|
| **Tillåt inte dubbletter av utbytes kontroll nummer** | Blockera dubbla ändringar. Kontrol lera utbytes kontroll numret (ISA13) för det mottagna utbytes kontroll numret. Om en matchning upptäcks bearbetas inte utbytet av EDI Receive-pipeline. <p><p>Om du vill ange antalet dagar då kontrollen ska utföras anger du ett värde för egenskapen **Sök efter dubbletter ISA13 varje (dagar)** . |
| **Tillåt inte dubbletter av grupp kontroll nummer** | Blockera ändringar som har duplicerade grupp kontroll nummer. |
| **Tillåt inte dubbletter av transaktions uppsättnings nummer** | Blockera ändringar som har duplicerade kontroll nummer för transaktions uppsättningar. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Ta emot inställningar – valideringar

![Valideringar för inkommande meddelanden](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

**Standard** raden visar de validerings regler som används för en typ av EDI-meddelande. Om du vill definiera olika regler markerar du varje ruta där du vill att regeln ska ha värdet **Sant**. När du har slutfört varje rad visas en ny tom rad automatiskt.

| Egenskap | Beskrivning |
|----------|-------------|
| **Meddelande typ** | EDI-meddelandets typ |
| **EDI-verifiering** | Utföra EDI-verifiering på data typer som definieras av schemats EDI-egenskaper, längd begränsningar, tomma data element och avslutande avgränsare. |
| **Utökad verifiering** | Om data typen inte är EDI, är verifiering av data elementets krav och tillåten upprepning, uppräkningar och verifiering av data element längd (min eller max). |
| **Tillåt inledande/avslutande nollor** | Behåll eventuella ytterligare inledande eller avslutande noll och blank stegs tecken. Ta inte bort de här tecknen. |
| **Trimma inledande/avslutande nollor** | Ta bort eventuella inledande eller avslutande noll och blank stegs tecken. |
| **Avslutande avgränsnings princip** | Generera avslutande avgränsare. <p>- **Tillåts inte**: förhindra avslutande avgränsare och avgränsare i inkommande Interchange. Om Interchange har avslutande avgränsare och avgränsare, deklareras inte Interchange som giltig. <p>- **Valfritt**: acceptera ändringar med eller utan avslutande avgränsare och avgränsare. <p>- **Obligatoriskt**: inkommande Interchange måste ha avslutande avgränsare och avgränsare. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Ta emot inställningar – interna inställningar

![Interna inställningar för inkommande meddelanden](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Egenskap | Beskrivning |
|----------|-------------|
| **Omvandla det underförstådda decimal formatet NN till ett bas 10-numeriskt värde** | Konvertera ett EDI-tal som anges med formatet "NN" till ett numeriskt bas värde. |
| **Skapa tomma XML-taggar om avslutande avgränsare tillåts** | Låt Interchange Sender ta med tomma XML-taggar för efterföljande avgränsare. |
| **Dela upp utbyte som transaktions uppsättningar – inaktivera transaktions uppsättningar vid fel** | Parsa varje transaktions uppsättning som är i ett utbyte i ett separat XML-dokument genom att använda rätt kuvert i transaktions uppsättningen. Pausa bara transaktioner där valideringen Miss lyckas. |
| **Dela upp utbyte som transaktions uppsättningar – pausa utbyte vid fel** | Parsa varje transaktions uppsättning som är i ett utbyte i ett separat XML-dokument genom att använda rätt kuvert. Pausa hela utbytet när en eller flera transaktions uppsättningar i överförings testet inte kunde verifieras. |
| **Bevara Interchange – pausa transaktions uppsättningar vid fel** | Lämna korsningen intakt och skapa ett XML-dokument för hela det batchade utbytet. Pausa bara de transaktions uppsättningar som inte kan verifieras, men fortsätt att bearbeta alla andra transaktions uppsättningar. |
| **Bevara Interchange – pausa utbyte vid fel** |Lämnar utväxlingen intakt, skapar ett XML-dokument för hela det batchade utbytet. Pausar hela utbytet när en eller flera transaktions uppsättningar i överförings körningen inte verifieras. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Skicka inställningar

När du har angett avtals egenskaperna kan du konfigurera hur det här avtalet identifierar och hanterar utgående meddelanden som du skickar till din partner via detta avtal.

1. Under **Lägg till** väljer du **Skicka inställningar**.

1. Konfigurera dessa egenskaper utifrån ditt avtal med den partner som utbyter meddelanden med dig. För egenskaps beskrivningar, se tabellerna i det här avsnittet.

   **Sändnings inställningarna** är ordnade i följande avsnitt:

   * [Identifierare](#outbound-identifiers)
   * [Bekräftelse](#outbound-acknowledgement)
   * [Scheman](#outbound-schemas)
   * [Kuvert](#outbound-envelopes)
   * [Kontroll versions nummer](#outbound-control-version-number)
   * [Kontroll nummer](#outbound-control-numbers)
   * [Teckenuppsättningar och avgränsare](#outbound-character-sets-separators)
   * [Signaturverifiering](#outbound-validation)

1. När du är klar, se till att spara inställningarna genom att välja **OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Skicka inställningar – identifierare

![ID-egenskaper för utgående meddelanden](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Egenskap | Beskrivning |
|----------|-------------|
| **ISA1 (kvalificering av autentisering)** | Det kvalificerande värde för auktorisering som du vill använda. Standardvärdet är **00 – det finns ingen autentiseringsinformation**. <p>**Obs**: om du väljer andra värden anger du ett värde för egenskapen **ISA2** . |
| **ISA2** | Värdet för auktoriseringsinformation som ska användas när **ISA1** -egenskapen inte är **00 – ingen autentiseringsinformation finns**. Det här egenskap svärdet måste innehålla minst ett alfanumeriskt tecken och högst 10. |
| **ISA3 (säkerhets kvalificerare)** | Det säkerhets kvalificerande värde som du vill använda. Standardvärdet är **00 – ingen säkerhets information finns**. <p>**Obs**: om du väljer andra värden anger du ett värde för egenskapen **ISA4** . |
| **ISA4** | Värdet för säkerhets information som ska användas när **ISA3** -egenskapen inte är **00 – ingen säkerhets information finns**. Det här egenskap svärdet måste innehålla minst ett alfanumeriskt tecken och högst 10. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Skicka inställningar – bekräftelse

![Bekräftelse egenskaper för utgående meddelanden](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Egenskap | Beskrivning |
|----------|-------------|
| **TA1 förväntades** | Returnera en teknisk bekräftelse (TA1) till Interchange-avsändaren. <p>Den här inställningen anger att värd partnern, som skickar meddelandet, begär en bekräftelse från gäst partnern i avtalet. Dessa bekräftelser förväntas av värd partnern baserat på avtalets mottagnings inställningar. |
| **Anl. förväntas** | Returnera en funktionell bekräftelse (FA) till Interchange-avsändaren. För egenskapen **Anl-version** , baserat på schema versionen, väljer du bekräftelser för 997 eller 999. <p>De här inställningarna anger att värd partnern, som skickar meddelandet, begär en bekräftelse från gäst partnern i avtalet. Dessa bekräftelser förväntas av värd partnern baserat på avtalets mottagnings inställningar. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Skicka inställningar – scheman

![Scheman för utgående meddelanden](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

I det här avsnittet väljer du ett [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) från [integrations kontot](./logic-apps-enterprise-integration-create-integration-account.md) för varje transaktions typ (ST01). När du har slutfört varje rad visas en ny tom rad automatiskt.

| Egenskap | Beskrivning |
|----------|-------------|
| **Version** | X12-versionen för schemat |
| **Transaktions typ (ST01)** | Transaktions typ för schemat |
| **Schema** | Den schema fil som du vill använda. Om du väljer schemat först anges version och transaktions typ automatiskt. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Skicka inställningar-kuvert

![Avgränsare i en transaktion som ska användas för utgående meddelanden](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Egenskap | Beskrivning |
|----------|-------------|
| **ISA11-användning** | Avgränsaren som ska användas i en transaktions uppsättning: <p>- **Standard identifierare**: Använd en punkt (.) för decimal format i stället för decimal tecknet för det utgående dokumentet i EDI-sändningen. <p>- **Upprepnings avgränsare**: Ange avgränsare för upprepade förekomster av ett enkelt data element eller en upprepad data struktur. Till exempel används vanligt vis cirkumflex (^) som upprepnings avgränsare. För HIPAA-scheman kan du bara använda cirkumflex. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Skicka inställningar-kontroll versions nummer

![Kontroll versions nummer för utgående meddelanden](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

I det här avsnittet väljer du ett [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) från [integrations kontot](./logic-apps-enterprise-integration-create-integration-account.md) för varje utbyte. När du har slutfört varje rad visas en ny tom rad automatiskt.

| Egenskap | Beskrivning |
|----------|-------------|
| **Kontroll versions nummer (ISA12)** | Versionen av X12-standarden |
| **Användnings indikator (ISA15)** | Kontexten för ett utbyte, som antingen **testar** data, **informations** data eller **produktions** data |
| **Schema** | Det schema som ska användas för att generera GS-och ST-segmenten för ett X12-kodat utbyte som skickas till en pipeline för att skicka EDI. |
| **GS1** | Valfritt väljer du den funktionella koden. |
| **GS2** | Valfritt anger du avsändarens program. |
| **GS3** | Valfritt, ange program mottagaren. |
| **GS4** | Valfritt väljer du **CCYYMMDD** eller **yymmdd**. |
| **GS5** | Valfritt väljer du **HHMM**, **HHMMSS** eller **HHMMSSdd**. |
| **GS7** | Valfritt väljer du ett värde för ansvarig myndighet. |
| **GS8** | Valfritt anger du schema dokument versionen. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Skicka inställningar-kontroll nummer

![Kontroll nummer för utgående meddelanden](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Egenskap | Beskrivning |
|----------|-------------|
| **Utbytes kontroll nummer (ISA13)** | Värde intervallet för Interchange-kontrollens nummer, som kan ha ett minsta värde på 1 och ett max värde på 999999999 |
| **Grupp kontroll nummer (GS06)** | Värde intervallet för grupp kontroll numret, som kan ha det lägsta värdet 1 och Max värdet 999999999 |
| **Kontroll nummer för transaktions uppsättning (ST02)** | Värde intervallet för transaktions uppsättningens kontroll nummer, som kan ha ett minsta värde på 1 och ett max värde på 999999999 <p>- **Prefix**: valfritt, ett alfanumeriskt värde <br>- **Suffix**: valfritt, ett alfanumeriskt värde |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Skicka inställningar – teckenuppsättningar och avgränsare

![Avgränsare för meddelande typer i utgående meddelanden](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

**Standard** raden visar den teckenuppsättning som används som avgränsare för ett meddelande schema. Om du inte vill använda **standard** teckenuppsättningen kan du ange en annan uppsättning avgränsare för varje meddelande typ. När du har slutfört varje rad visas en ny tom rad automatiskt.

> [!TIP]
> Om du vill ange specialtecken redigerar du avtalet som JSON och anger ASCII-värdet för specialtecknet.

| Egenskap | Beskrivning |
|----------|-------------|
| **Teckenuppsättning som ska användas** | X12 teckenuppsättning, som är antingen **Basic**, **Extended** eller **utf8**. |
| **Schema** | Det schema som du vill använda. När du har valt schemat väljer du den teckenuppsättning som du vill använda, baserat på avgräns beskrivningarna nedan. |
| **Indatatyp** | Indatatypen för teckenuppsättningen |
| **Komponent avgränsare** | Ett enda Character som avgränsar sammansatta data element |
| **Data Elements avgränsare** | Ett enda Character som avgränsar enkla data element i sammansatta data |
| **ersättnings tecken avgränsare** | Ett ersättnings tecken som ersätter alla avgränsnings tecken i nytto Last data när du genererar utgående X12-meddelande |
| **Segment begränsare** | Ett enda Character som visar slutet på ett EDI-segment |
| **Huvudnamnssuffix** | Det tangent som ska användas med segment identifieraren. Om du anger ett suffix kan data elementet för segment begränsas vara tomt. Om avgränsarna för segment lämnas tomma måste du ange ett suffix. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Skicka inställningar – verifiering

![Verifierings egenskaper för utgående meddelanden](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

**Standard** raden visar de validerings regler som används för en typ av EDI-meddelande. Om du vill definiera olika regler markerar du varje ruta där du vill att regeln ska ha värdet **Sant**. När du har slutfört varje rad visas en ny tom rad automatiskt.

| Egenskap | Beskrivning |
|----------|-------------|
| **Meddelande typ** | EDI-meddelandets typ |
| **EDI-verifiering** | Utföra EDI-verifiering på data typer som definieras av schemats EDI-egenskaper, längd begränsningar, tomma data element och avslutande avgränsare. |
| **Utökad verifiering** | Om data typen inte är EDI, är verifiering av data elementets krav och tillåten upprepning, uppräkningar och verifiering av data element längd (min eller max). |
| **Tillåt inledande/avslutande nollor** | Behåll eventuella ytterligare inledande eller avslutande noll och blank stegs tecken. Ta inte bort de här tecknen. |
| **Trimma inledande/avslutande nollor** | Ta bort eventuella inledande eller avslutande noll och blank stegs tecken. |
| **Avslutande avgränsnings princip** | Generera avslutande avgränsare. <p>- **Tillåts inte**: förhindra avslutande avgränsare och avgränsare i utgående utbyte. Om Interchange har avslutande avgränsare och avgränsare, deklareras inte Interchange som giltig. <p>- **Valfritt**: skicka ändringar med eller utan avslutande avgränsare och avgränsare. <p>- **Obligatoriskt**: utgående Interchange måste ha avslutande avgränsare och avgränsare. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA-scheman och meddelande typer

När du arbetar med HIPAA-scheman och meddelande typerna 277 eller 837 måste du utföra några extra steg. [Dokument versions nummer (GS8)](#outbound-control-version-number) för dessa meddelande typer har fler än 9 tecken, till exempel "005010X222A1". Vissa dokument versions nummer mappar också till typer av variant-meddelanden. Om du inte refererar till rätt meddelande typ i schemat och i ditt avtal får du följande fel meddelande:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

I den här tabellen visas de typer av meddelanden som påverkas, alla varianter och dokument versions nummer som mappas till dessa meddelande typer:

| Meddelande typ eller variant |  Beskrivning | Dokument versions nummer (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Status meddelande för hälso vårds information | 005010X212 |
| 837_I | Hälso vårds anspråk institutionellt | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Tandvård av hälso vårds anspråk | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Health Care-anspråket Professional | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Du måste också inaktivera EDI-verifiering när du använder dessa dokument versions nummer, eftersom de resulterar i ett fel meddelande om att tecken längden är ogiltig.

Följ dessa steg om du vill ange dessa dokument versions nummer och meddelande typer:

1. I HIPAA-schemat ersätter du den aktuella meddelande typen med meddelande typen variant för det dokument versions nummer som du vill använda.

   Anta till exempel att du vill använda dokument versions nummer `005010X222A1` med `837` meddelande typen. Ersätt varje `"X12_00501_837"` värde med värdet i stället i schemat `"X12_00501_837_P"` .

   Följ dessa steg om du vill uppdatera schemat:

   1. Gå till integrations kontot i Azure Portal. Hitta och hämta ditt schema. Ersätt meddelande typen och Byt namn på schema filen och ladda upp det ändrade schemat till ditt integrations konto. Mer information finns i [Redigera scheman](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. I ditt avtals meddelande inställningar väljer du det ändrade schemat.

1. I ditt avtals `schemaReferences` objekt lägger du till en annan post som anger typen av variant-meddelande som matchar ditt dokument versions nummer.

   Anta till exempel att du vill använda dokument versions nummer `005010X222A1` för `837` meddelande typen. Ditt avtal har ett `schemaReferences` avsnitt med dessa egenskaper och värden:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   I det här `schemaReferences` avsnittet lägger du till en annan post med följande värden:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   När du är klar ser ditt `schemaReferences` avsnitt ut så här:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. I ditt avtals meddelande inställningar inaktiverar du EDI-verifiering genom att avmarkera kryss rutan för **EDI-verifiering** för varje meddelande typ eller för alla meddelande typer om du använder **standardvärdena** .

   ![Inaktivera verifiering för alla meddelande typer eller varje meddelande typ](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel åtgärder och begränsningar som beskrivs i kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/x12/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version de [B2B-meddelande gränserna för ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [anslutningar för Logic Apps](../connectors/apis-list.md)
