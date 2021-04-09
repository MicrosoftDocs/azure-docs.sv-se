---
title: 'Anvisningar: utveckla anpassade kommandon program – tal service'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du utvecklar och anpassar program för anpassade kommandon. Dessa appar för röst kommandon lämpar sig bäst för slut för ande av aktiviteter eller kommando-och-kontroll-scenarier.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97935826"
---
# <a name="develop-custom-commands-applications"></a>Utveckla anpassade kommandon program

I den här instruktions artikeln får du lära dig hur du utvecklar och konfigurerar anpassade kommando program. Med funktionen för anpassade kommandon kan du bygga rika appar för röst kommandon som är optimerade för röst-och första interaktions upplevelser. Funktionen passar bäst för att slutföra aktiviteter eller kommando-och kontroll scenarier. Den är särskilt lämplig för Sakernas Internet-enheter (IoT) och för omgivande och omdirigerings enheter.

I den här artikeln skapar du ett program som kan aktivera och inaktivera en TV, ange temperatur och ange ett larm. När du har skapat de här grundläggande kommandona får du lära dig om följande alternativ för att anpassa kommandon:

* Lägga till parametrar till kommandon
* Lägga till konfigurationer till kommando parametrar
* Skapa interaktions regler
* Skapa mallar för språk skapande för tal svar
* Använda anpassade röst verktyg

## <a name="create-an-application-by-using-simple-commands"></a>Skapa ett program med hjälp av enkla kommandon

Börja med att skapa ett tomt anpassat kommandon-program. Mer information finns i [snabb](quickstart-custom-commands-application.md)starten. I det här programmet, i stället för att importera ett projekt, skapar du ett tomt projekt.

1. I rutan **namn** anger du projekt namnet *Smart-Room-lite* (eller ett annat namn som du väljer).
1. I listan **språk** väljer du **engelska (USA)**.
1. Välj eller skapa en LUIS-resurs.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild som visar fönstret nytt projekt.](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Uppdatera LUIS-resurser (valfritt)

Du kan uppdatera den redigerings resurs som du har valt i fönstret **nytt projekt** . Du kan också ange en förutsägelse resurs. 

En förutsägelse resurs används för att känna igen när ditt anpassade kommando program publiceras. Du behöver ingen förutsägelse resurs under utvecklings-och test faserna.

### <a name="add-a-turnon-command"></a>Lägg till ett TurnOn-kommando

I det tomma programmet för smart-Room-lite anpassade kommandon som du har skapat lägger du till ett kommando. Kommandot bearbetar en uttryck, `Turn on the tv` . Det kommer att svara med meddelandet `Ok, turning the tv on` .

1. Skapa ett nytt kommando genom att välja **nytt kommando** längst upp i det vänstra fönstret. Det **nya kommando** fönstret öppnas.
1. I fältet **namn** anger du värdet `TurnOn` .
1. Välj **Skapa**.

I fönstret i mitten visas egenskaperna för kommandot. 

I följande tabell förklaras kommandots konfigurations egenskaper. Mer information finns i [begrepp och definitioner för anpassade kommandon](./custom-commands-references.md).

| Konfiguration            | Beskrivning                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Exempel på meningar | Exempel yttranden användaren kan säga att utlösa det här kommandot.                                                                 |
| Parametrar       | Information som krävs för att slutföra kommandot.                                                                                |
| Regler för slut för ande | Åtgärder som ska vidtas för att utföra kommandot. Exempel: svara på användaren eller kommunicera med en webb tjänst. |
| Interaktions regler   | Andra regler för att hantera mer detaljerade eller komplexa situationer.                                                              |


> [!div class="mx-imgBorder"]
> ![Skärm bild som visar var du skapar ett kommando.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Lägg till exempel meningar

I avsnittet **exempel meningar** anger du ett exempel på vad användaren kan säga.

1. I fönstret i mitten väljer du **exempel meningar**.
1. I rutan till höger lägger du till exempel:

    ```
    Turn on the tv
    ```

1.  Klicka på **Spara** längst upp i fönstret.

Du har inte parametrar än, så du kan gå vidare till avsnittet **regler för slut för ande** .

#### <a name="add-a-completion-rule"></a>Lägg till en regel för slut för ande

Därefter behöver kommandot en regel för slut för ande. Den här regeln anger användaren att en uppfyllelse åtgärd utförs. 

Mer information om regler och regler för slut för ande finns i [anpassade kommandon och definitioner](./custom-commands-references.md).

1. Välj **standard regeln för** slut för ande. Redigera den på följande sätt:

    
    | Inställning    | Föreslaget värde                          | Beskrivning                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Namn**       | `ConfirmationResponse`                  | Ett namn som beskriver syftet med regeln          |
    | **Villkor** | Inget                                     | Villkor som avgör när regeln kan köras    |
    | **Åtgärder**    | **Skicka tal svar**  >  **Enkelt redigerings program**  >  **Första variationen** > `Ok, turning the tv on` | Den åtgärd som ska vidtas när regel villkoret är sant |

   > [!div class="mx-imgBorder"]
   > ![Skärm bild som visar var du ska skapa ett tal svar.](media/custom-commands/create-speech-response-action.png)

1. Spara åtgärden genom att välja **Spara** .
1. Gå tillbaka till avsnittet **regler för slut för ande** och välj **Spara** för att spara alla ändringar. 

    > [!NOTE]
    > Du behöver inte använda standard regeln för slut för ande som medföljer kommandot. Du kan ta bort standard regeln för slut för ande och lägga till en egen regel.

### <a name="add-a-settemperature-command"></a>Lägg till ett SetTemperature-kommando

Lägg nu till ytterligare ett kommando, `SetTemperature` . Det här kommandot tar en enda uttryck, `Set the temperature to 40 degrees` och svarar med meddelandet `Ok, setting temperature to 40 degrees` .

Skapa det nya kommandot genom att följa de steg som du använde för `TurnOn` kommandot, men Använd exempel meningen `Set the temperature to 40 degrees` .

Redigera sedan **de befintliga reglerna för färdig komplettering** enligt följande:

| Inställning    | Föreslaget värde                          |
| ---------- | ---------------------------------------- |
| **Namn**  | `ConfirmationResponse`                  |
| **Villkor** | Inget                                     |
| **Åtgärder**    | **Skicka tal svar**  >  **Enkelt redigerings program**  >  **Första variationen** > `Ok, setting temperature to 40 degrees` |

Välj **Spara** för att spara alla ändringar i kommandot.

### <a name="add-a-setalarm-command"></a>Lägg till ett SetAlarm-kommando

Skapa ett nytt `SetAlarm` kommando. Använd exempel meningen `Set an alarm for 9 am tomorrow` . Redigera sedan **de befintliga reglerna för färdig komplettering** enligt följande:

| Inställning    | Föreslaget värde                          |
| ---------- | ---------------------------------------- |
| **Namn**  | `ConfirmationResponse`                  |
| **Villkor** | Inget                                     |
| **Åtgärder**    | **Skicka tal svar**  >  **Enkelt redigerings program**  >  **Första variationen** > `Ok, setting an alarm for 9 am tomorrow` |

Välj **Spara** för att spara alla ändringar i kommandot.

### <a name="try-it-out"></a>Prova

Testa programmets beteende med hjälp av test fönstret: 

1. I det övre högra hörnet i fönstret väljer du ikonen **träna** . 
1. När utbildningen är klar väljer du **test**. 

Prova följande uttryck-exempel med hjälp av röst eller text:

- Du skriver: *Ange temperatur till 40 grader*
- Förväntat svar: OK, ställer in temperatur på 40 grader
- Du skriver: *Aktivera TV: n*
- Förväntat svar: OK och aktivera TV på
- Du skriver: *Ange ett larm för 9 am imorgon*
- Förväntat svar: OK, ange ett larm för 9 am imorgon

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar testet i ett webb chatt gränssnitt.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> I test fönstret kan du välja **Aktivera Detaljer** för information om hur röst indata eller text indata har bearbetats.

## <a name="add-parameters-to-commands"></a>Lägga till parametrar till kommandon

I det här avsnittet får du lära dig hur du lägger till parametrar till dina kommandon. Kommandon kräver parametrar för att slutföra en uppgift. I komplexa scenarier kan parametrar användas för att definiera villkor som utlöser anpassade åtgärder.

### <a name="configure-parameters-for-a-turnon-command"></a>Konfigurera parametrar för ett TurnOn-kommando

Börja med att redigera det befintliga `TurnOn` kommandot för att aktivera och inaktivera flera enheter.

1. Nu när kommandot kommer att hantera både på-och-scenarier, byter du namn på kommandot till *TurnOnOff*.
   1. I rutan till vänster väljer du kommandot **TurnON** . Välj sedan knappen med tre punkter (**...**) bredvid **nytt kommando** överst i fönstret.
   
   1. Välj **Byt namn**. Ändra namnet till *TurnOnOff* i **kommando fönstret Byt** namn.

1. Lägg till en ny parameter i kommandot. Parametern anger om användaren vill aktivera eller inaktivera enheten.
   1. Överst i mitten av fönstret väljer du  **Lägg till**. I den nedrullningsbara menyn väljer du **parameter**.
   1. I rutan till höger i avsnittet **parametrar** , i rutan **namn** , Lägg till `OnOff` .
   1. Välj **obligatoriskt**. I fönstret **Lägg till svar för en obligatorisk parameter** väljer du **enkel redigerare**. I det **första Variations** fältet, Lägg till *på eller av?*.
   1. Välj **Uppdatera**.

       > [!div class="mx-imgBorder"]
       > ![Skärm bild som visar avsnittet "lägga till svar för en obligatorisk parameter" med fliken "enkel redigerare" vald.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Konfigurera parameterns egenskaper med hjälp av följande tabell. Information om alla konfigurations egenskaper för ett kommando finns i [begrepp och definitioner för anpassade kommandon](./custom-commands-references.md).
      

       | Konfiguration      | Föreslaget värde     | Beskrivning                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Namn**               | `OnOff`           | Ett beskrivande namn för parametern                                                                           |
       | **Är global**          | Omarkerade       | Kryss ruta som anger om ett värde för den här parametern används globalt för alla kommandon i programmet.|
       | **Obligatoriskt**           | Markerat         | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs. |
       | **Svar för obligatorisk parameter**      |**Enkelt redigerings program** > `On or Off?`      | En uppmaning att fråga efter värdet för den här parametern när den inte är känd. |
       | **Typ**               | **Sträng**          | Parameter typ, till exempel Number, String, datum tid eller geografi.   |
       | **Konfiguration**      | **Acceptera fördefinierade indatavärden från en intern katalog** | För strängar begränsar den här inställningen indata till en uppsättning möjliga värden. |
       | **Fördefinierade indatavärden**     | `on`, `off`           | Uppsättning möjliga värden och deras alias.         |
       
        
   1. Om du vill lägga till fördefinierade indatavärden väljer du **Lägg till en fördefinierad indatamängd**. I fönstret **nytt objekt**  skriver du det *namn* som visas i tabellen ovan. I det här fallet använder du inte alias, så du kan lämna fältet tomt.
   
      > [!div class="mx-imgBorder"]
      > ![Skärm bild som visar hur du skapar en parameter.](media/custom-commands/create-on-off-parameter.png)

   1. Välj **Spara** för att spara alla konfigurationer för parametern.
 
#### <a name="add-a-subjectdevice-parameter"></a>Lägg till en SubjectDevice-parameter

1. Om du vill lägga till en andra parameter för att representera namnet på de enheter som kan kontrol leras med hjälp av det här kommandot väljer du **Lägg till**. Använd följande konfiguration.


    | Inställning            | Föreslaget värde       |
    | ------------------ | --------------------- |
    | **Namn**               | `SubjectDevice`         |
    | **Är global**          | Omarkerade             |
    | **Obligatoriskt**           | Markerat               |
    | **Svar för obligatorisk parameter**     | **Enkelt redigerings program** > `Which device do you want to control?`    | 
    | **Typ**               | **Sträng**                |          |
    | **Konfiguration**      | **Acceptera fördefinierade indatavärden från en intern katalog** | 
    | **Fördefinierade indatavärden** | `tv`, `fan`               |
    | **Alias** ( `tv` )      | `television`, `telly`     |

1. Välj **Spara**.

#### <a name="modify-example-sentences"></a>Ändra exempel meningar

För kommandon som använder parametrar är det bra att lägga till exempel meningar som beskriver alla möjliga kombinationer. Exempel:

* Fullständig parameter information: `turn {OnOff} the {SubjectDevice}`
* Ofullständig parameter information: `turn it {OnOff}`
* Ingen parameter information: `turn something`

Exempel på meningar som använder varierande grad av information gör att anpassade kommandon kan matcha både ensidiga lösningar och lösningar med flera nivåer med hjälp av del information.

Med den informationen i åtanke redigerar du exempel meningarna så att de använder följande föreslagna parametrar:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Välj **Spara**.

> [!TIP]
> I redigeraren exempel – meningar använder du klammerparenteser för att referera till parametrarna. Till exempel `turn {OnOff} the {SubjectDevice}`.
> Använd en flik för automatisk komplettering som backas upp av parametrarna som skapats tidigare.

#### <a name="modify-completion-rules-to-include-parameters"></a>Ändra regler för slut för ande för att inkludera parametrar

Ändra den befintliga regeln för slut för ande `ConfirmationResponse` .

1. I avsnittet **villkor** väljer du **Lägg till ett villkor**.
1. I fönstret **nytt villkor** väljer du **nödvändiga parametrar** i listan **typ** . I listan som följer väljer du både **mikrofonen** och **SubjectDevice**.
1. Lämna **IsGlobal** omarkerat.
1. Välj **Skapa**.
1. I avsnittet **åtgärder** redigerar du **svars åtgärden skicka tal** genom att hovra över den och välja knappen Redigera. Den här gången använder du de nyligen skapade `OnOff` och `SubjectDevice` parametrarna:

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Välj **Spara**.

Prova ändringarna genom att välja **träna** -ikonen längst upp i fönstret till höger. 

När utbildningen är klar väljer du **test**. Ett **test av ditt program** -fönster visas. Prova följande interaktioner:

- Inmatade: *Stäng av TV*
- Utdata: OK, stänga av TV
- Inmatade: *Stäng av TV: n*
- Utdata: OK, stänga av TV
- Inmatade: *Stäng av*
- Utdata: vilken enhet vill du styra?
- Inmatade: *TV*
- Utdata: OK, stänga av TV

### <a name="configure-parameters-for-a-settemperature-command"></a>Konfigurera parametrar för ett SetTemperature-kommando

Ändra `SetTemperature` kommandot så att det kan ange temperatur när användaren dirigeras.

Lägg till en `Temperature` parameter. Använd följande konfiguration:

| Konfiguration      | Föreslaget värde     |
| ------------------ | ----------------|
| **Namn**               | `Temperature`           |
| **Obligatoriskt**           | Markerat         |
| **Svar för obligatorisk parameter**      | **Enkelt redigerings program** > `What temperature would you like?`
| **Typ**               | `Number`          |


Redigera yttranden-exemplet och Använd följande värden.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Redigera befintliga regler för slut för ande. Använd följande konfiguration.

| Konfiguration      | Föreslaget värde     |
| ------------------ | ----------------|
| **Villkor**         | **Obligatorisk parameter**  >  **Temperatur**           |
| **Åtgärder**           | **Skicka tal svar** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Konfigurera parametrar för ett SetAlarm-kommando

Lägg till en parameter med namnet `DateTime` . Använd följande konfiguration.

   | Inställning                           | Föreslaget värde                     | 
   | --------------------------------- | ----------------------------------------|
   | **Namn**                              | `DateTime`                               |
   | **Obligatoriskt**                          | Markerat                                 |
   | **Svar för obligatorisk parameter**   | **Enkelt redigerings program** > `For what time?`            | 
   | **Typ**                              | **DateTime**                                |
   | **Standardinställningar för datum**                     | Om datumet saknas använder du idag.            |
   | **Standardvärden**                     | Om tiden saknas kan du använda början på dagen.     |


> [!NOTE]
> Den här artikeln använder främst parameter typerna String, Number och DateTime. För en lista över alla parameter typer som stöds och deras egenskaper, se [begrepp och definitioner för anpassade kommandon](./custom-commands-references.md).


Redigera exemplet yttranden. Använd följande värden.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Redigera befintliga regler för slut för ande. Använd följande konfiguration.

   | Inställning    | Föreslaget värde                               |
   | ---------- | ------------------------------------------------------- |
   | **Åtgärder**    | **Skicka tal svar** > `Ok, alarm set for {DateTime}`  |

Testa de tre kommandona tillsammans med yttranden som är relaterade till olika kommandon. (Du kan växla mellan olika kommandon.)

- Inmatade: *Ställ in ett larm*
- Utdata: för vilken tid?
- Inmatade: *slå på TV*
- Utdata: OK, aktivera TV på
- Inmatade: *Ställ in ett larm*
- Utdata: för vilken tid?
- Inmatade: *17:00*
- Utdata: OK, alarm set för 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Lägg till konfigurationer till kommando parametrar

I det här avsnittet får du lära dig mer om konfiguration av avancerad parameter, inklusive:

 - Hur parameter värden kan tillhöra en uppsättning som definieras utanför programmet för anpassade kommandon.
 - Hur du lägger till verifierings satser i parameter värden.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Konfigurera en parameter som en extern katalog-entitet

Med funktionen anpassade kommandon kan du konfigurera sträng typ parametrar för att referera till externa kataloger som finns på en webb slut punkt. Det innebär att du kan uppdatera den externa katalogen oberoende utan att redigera programmet för anpassade kommandon. Den här metoden är användbar i fall där katalog posterna är flera.

Återanvänd `SubjectDevice` parametern från `TurnOnOff` kommandot. Den aktuella konfigurationen för den här parametern **accepterar fördefinierade indata från den interna katalogen**. Den här konfigurationen refererar till en statisk lista över enheter i parameter konfigurationen. Flytta ut det här innehållet till en extern data källa som kan uppdateras oberoende av varandra.

Börja med att lägga till en ny webb slut punkt för att flytta innehållet. Gå till avsnittet **webb slut punkter** i rutan till vänster. Där lägger du till en ny webb slut punkt. Använd följande konfiguration.

| Inställning | Föreslaget värde |
|----|----|
| **Namn** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Metod** | **TA** |


Om det föreslagna värdet för URL: en inte fungerar för dig kan du konfigurera och vara värd för en webb slut punkt som returnerar en JSON-fil som består av listan över enheter som kan kontrol leras. Webb slut punkten ska returnera en JSON-fil formaterad enligt följande:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Gå sedan till sidan med **SubjectDevice** parameter inställningar. Konfigurera följande egenskaper.

| Inställning | Föreslaget värde |
| ----| ---- |
| **Konfiguration** | **Acceptera fördefinierade indata från extern katalog** |                               
| **Katalog slut punkt** | `getDevices` |
| **Metod** | **TA** |

Välj sedan **Spara**.

> [!IMPORTANT]
> Du kan inte se ett alternativ för att konfigurera en parameter för att acceptera indata från en extern katalog om du inte har angett webb slut punkten i avsnittet **webb slut punkt** i fönstret till vänster.

Prova genom att välja **träna**. När utbildningen är klar väljer du **test** och prova några interaktioner.

* Inmatade: *Aktivera*
* Utdata: vilken enhet vill du styra?
* Inmatade: *lampor*
* Utdata: OK, aktivera indikeringar

> [!NOTE]
> Nu kan du kontrol lera alla enheter som finns på webb slut punkten. Men du behöver träna programmet för att testa de nya ändringarna och sedan publicera programmet på nytt.

### <a name="add-validation-to-parameters"></a>Lägg till validering i parametrar

*Valideringar* är konstruktioner som gäller för vissa parameter typer som gör att du kan konfigurera begränsningar för parameterns värde. Du uppmanas att ange ändringar om värdena inte faller inom begränsningarna. För en lista över parameter typer som utökar validerings konstruktionen, se [begrepp och definitioner för anpassade kommandon](./custom-commands-references.md).

Testa verifieringar med hjälp av `SetTemperature` kommandot. Använd följande steg för att lägga till en validering för- `Temperature` parametern.

1. I rutan till vänster väljer du kommandot **SetTemperature** .
1. I fönstret i mitten väljer du **temperatur**.
1. Välj **Lägg till en verifiering** i rutan till höger.
1. I fönstret **ny validering** konfigurerar du verifieringen enligt följande tabell. Välj sedan **Skapa**.


    | Parameter konfiguration | Föreslaget värde | Beskrivning |
    | ---- | ---- | ---- |
    | **Minsta värde** | `60` | För Number-parametrar kan det minsta värdet som denna parameter anta |
    | **Max värde** | `80` | För Number-parametrar kan det högsta värdet som den här parametern anta |
    | **Haveri svar** |  **Enkelt redigerings program**  >  **Första variationen** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | En uppmaning om att fråga efter ett nytt värde om valideringen Miss lyckas |

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du lägger till en intervall validering.](media/custom-commands/add-validations-temperature.png)

Prova genom att välja **träna** -ikonen längst upp i rutan till höger. När utbildningen är klar väljer du **test**. Prova några interaktioner:

- Inmatade: *Ange temperatur till 72 grader*
- Utdata: OK, ställa in temperatur på 72 grader
- Inmatade: *Ange temperatur till 45 grader*
- Utdata: det går bara att ange temperatur mellan 60 och 80 grader
- Inmatade: *gör det till 72 grader i stället*
- Utdata: OK, ställa in temperatur på 72 grader

## <a name="add-interaction-rules"></a>Lägga till interaktionsregler

Interaktions regler är *ytterligare* regler som hanterar vissa eller komplexa situationer. Även om du är kostnads fri att skapa egna interaktions regler, i det här exemplet använder du interaktions regler för följande scenarier:

* Bekräfta kommandon
* Lägga till en enstaka stegs korrigering i kommandon

Mer information om interaktions regler finns i [begrepp och definitioner för anpassade kommandon](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Lägg till bekräftelser i ett kommando

Om du vill lägga till en bekräftelse använder du `SetTemperature` kommandot. Du kan få bekräftelse genom att skapa interaktions regler med hjälp av följande steg:

1. I rutan till vänster väljer du kommandot **SetTemperature** .
1. I fönstret i mitten lägger du till interaktions regler genom att välja **Lägg till**. Välj sedan **interaktions regler**  >  **Bekräfta kommando**.

    Den här åtgärden lägger till tre interaktions regler. Reglerna ber användaren att bekräfta datum och tid för larmet. De förväntar sig en bekräftelse (Ja eller Nej) för nästa tur.

    1. Ändra den **bekräftade kommando** interaktions regeln med hjälp av följande konfiguration:
        1. Ändra namnet för att **Bekräfta temperatur**.
        1. Lägg till ett nytt villkor: temperatur för **obligatoriska parametrar**  >  .
        1. Lägg till en ny åtgärd: **Skriv**  >  **Skicka tal svar**  >  **är du säker på att du vill ställa in temperaturen som {temperatur} grader?**
        1. I avsnittet **förväntningar** lämnar du standardvärdet **förväntar dig bekräftelse från användaren**.
      
         > [!div class="mx-imgBorder"]
         > ![Skärm bild som visar hur du skapar nödvändiga parameter svar.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Ändra **bekräftelse regeln lyckades** för att hantera en lyckad bekräftelse (användaren säger ja).
      
          1. Ändra namnet till en **bekräftelse temperatur lyckades**.
          1. Lämna den befintliga **bekräftelsen lyckades** .
          1. Lägg till ett nytt villkor: **Ange**  >  **obligatorisk parameter**  >  **temperatur**.
          1. Låt standardvärdet för **efter körnings tillstånd** vara **slutfört**.

    1. Ändra den **bekräftelse regel som nekas** för att hantera scenarier när en bekräftelse nekas (användaren som anges Nej).

          1. Ändra namnet till **bekräftelse temperatur nekad**.
          1. Lämna den befintliga **bekräftelsen nekades** .
          1. Lägg till ett nytt villkor: **Ange**  >  **obligatorisk parameter**  >  **temperatur**.
          1. Lägg till en ny åtgärd: **Skriv**  >  **Skicka tal svar**  >  **inget problem. Vilken temperatur sedan?**.
          1. Ändra standard status värde för **efter körning** för att **vänta på användarens indata**.

> [!IMPORTANT]
> I den här artikeln använder du den inbyggda bekräftelse funktionen. Du kan också lägga till interaktions regler manuellt en i taget.
   
Prova ändringarna genom att välja **träna**. När utbildningen är klar väljer du **test**.

- **Inmatade**: *ange temperatur till 80 grader*
- **Utdata**: är du säker på att du vill ställa in temperaturen som 80 grader?
- **Inmatade**: *Nej*
- **Utdata**: inga problem. Vilken temperatur sedan?
- **Inmatade**: *72 grader*
- **Utdata**: är du säker på att du vill ställa in temperaturen som 72 grader?
- **Inmatade**: *Ja*
- **Utdata**: OK, ställa in temperatur på 72 grader

### <a name="implement-corrections-in-a-command"></a>Implementera korrigeringar i ett kommando

I det här avsnittet ska du konfigurera en korrigering i ett steg. Den här korrigeringen används när åtgärden som ska utföras har körts. Du ser också ett exempel på hur en korrigering aktive ras som standard om kommandot inte är uppfyllt än. Lägg till en korrigering när kommandot inte är klart genom att lägga till den nya parametern `AlarmTone` .

I den vänstra rutan väljer du kommandot **SetAlarm** . Lägg sedan till den nya parametern **AlarmTone**.
        
- **Namn** > `AlarmTone`
- **Typ**  >  **Sträng**
- **Standardvärde**  >  **CHIMES**
- **Konfiguration**  >  **Acceptera fördefinierade indatavärden från den interna katalogen**
- **Fördefinierade indatavärden**  >  **CHIMES**, **Jingle** och **ECHO** (dessa värden är enskilda fördefinierade indata.)


Uppdatera sedan svaret för **datetime** -parametern till **redo att ställa in alarm med tonen som {AlarmTone}. För vilken tid?**. Ändra sedan regeln för slut för Ande enligt följande:

1. Välj den befintliga regeln för slut för ande **ConfirmationResponse**.
1. Hovra över den befintliga åtgärden i rutan till höger och välj **Redigera**.
1. Uppdatera tal svaret till `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> Larm tonen kan ändras utan någon explicit konfiguration i ett pågående kommando. Till exempel kan den ändras när kommandot inte har slutförts än. En korrigering aktive ras *som standard* för alla kommando parametrar, oavsett vilket antal som finns, om kommandot ännu är uppfyllt.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Implementera en korrigering när ett kommando är klart

Med de anpassade kommandona kan du använda en-steg-korrigering även om kommandot har avslut ATS. Den här funktionen är inte aktive rad som standard. Det måste konfigureras explicit. 

Använd följande steg för att konfigurera en korrigering med en steg:

1. I kommandot **SetAlarm** lägger du till en interaktions regel av typen **Uppdatera föregående-kommando** för att uppdatera det tidigare angivna larmet. Byt namn på interaktions regeln som **Uppdatera föregående larm**.
1. Lämna standard villkoret: **föregående kommando måste uppdateras**.
1. Lägg till ett nytt villkor: **typ**  >  **obligatorisk parameter**  >  **datetime**.
1. Lägg till en ny åtgärd: **Skriv**  >  **skicka röst svar**  >  **enkel redigerare**  >  **uppdaterar föregående alarm tid till {datetime}**.
1. Låt standardvärdet för **efter körnings tillstånd** vara **slutfört**.

Prova ändringarna genom att välja **träna**. Vänta tills utbildningen är klar och välj sedan **testa**.

- **Inmatade**: *Ange ett larm.*
- **Utdata**: redo att ställa in alarm med tonen som CHIMES. För vilken tid?
- **Inmatade**: *Ställ in ett larm med tonen som Jingle för 9 am imorgon.*
- **Output**: OK, alarm anges för 2020-05-21 09:00:00. Alarm tonen är Jingle.
- **Inmatade**: *Nej, 8.*
- **Utdata**: uppdaterar föregående larm tid till 2020-05-29 08:00.

> [!NOTE]
> I ett verkligt program, i avsnittet **åtgärder** i den här korrigerings regeln, måste du också skicka tillbaka en aktivitet till klienten eller anropa en http-slutpunkt för att uppdatera larm tiden i systemet. Den här åtgärden bör enbart vara ansvarig för att uppdatera larm tiden. Den bör inte vara ansvarig för andra attribut för kommandot. I det här fallet är det attributet alarm-tonen.

## <a name="add-language-generation-templates-for-speech-responses"></a>Lägg till mallar för språk skapande för tal svar

Med LG-mallar (Language-generation) kan du anpassa svaren som skickas till klienten. De introducerar varians i svaren. Du kan skapa språk genom att använda:

* Mallar för att skapa språk.
* Anpassningsbara uttryck.

Mallar för anpassade kommandon baseras på bot- [mallarna](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)i bot Framework. Eftersom funktionen anpassade kommandon skapar en ny LG-mall vid behov (för tal svar i parametrar eller åtgärder) behöver du inte ange namnet på LG-mallen. 

Så du behöver inte definiera mallen så här:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

I stället kan du definiera bröd texten i mallen utan namnet, t. ex. följande:

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar ett exempel på en mall.](./media/custom-commands/template-editor-example.png)


Den här ändringen introducerar variation i de tal svar som skickas till klienten. För en uttryck plockas motsvarande tal svar slumpmässigt från de angivna alternativen.

Genom att dra nytta av LG-mallar kan du också definiera komplexa tal svar för kommandon med hjälp av anpassade uttryck. Mer information finns i formatet för [LG-mallar](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Som standard stöder funktionen anpassade kommandon alla funktioner, med följande mindre skillnader:

* I LG-mallarna visas entiteter som `${entityName}` . Funktionen anpassade kommandon använder inte entiteter. Men du kan använda parametrar som variabler med antingen `${parameterName}` representationen eller `{parameterName}` representationen.
* Funktionen anpassade kommandon stöder inte mallens sammansättning och expansion, eftersom du aldrig redigerar *. LG* -filen direkt. Du redigerar bara Svaren för automatiskt skapade mallar.
* Funktionen anpassade kommandon stöder inte anpassade funktioner som LG infogar. Fördefinierade funktioner stöds.
* Funktionen anpassade kommandon stöder inte alternativ, till exempel, `strict` , `replaceNull` och `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Lägg till svar på mallar i ett TurnOnOff-kommando

Ändra `TurnOnOff` kommandot för att lägga till en ny parameter. Använd följande konfiguration.

| Inställning            | Föreslaget värde       | 
| ------------------ | --------------------- | 
| **Namn**               | `SubjectContext`         | 
| **Är global**          | Omarkerade             | 
| **Obligatoriskt**           | Omarkerade               | 
| **Typ**               | **Sträng**                |
| **Standardvärde**      | `all` |
| **Konfiguration**      | **Acceptera fördefinierade indatavärden från intern katalog** | 
| **Fördefinierade indatavärden** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Ändra en regel för slut för ande

Redigera avsnittet **åtgärder** i den befintliga regeln för slut för ande av **ConfirmationResponse**. I fönstret **Redigera åtgärd** växlar du till redigeraren för **mallar**. Ersätt sedan texten med följande exempel.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Träna och testa ditt program genom att använda följande indata och utdata. Lägg märke till variationen av svar. Variationen skapas av flera alternativ för mallnamnet och även med hjälp av anpassningsbara uttryck.

* Inmatade: *slå på TV*
* Utdata: OK, aktivera TV på
* Inmatade: *slå på TV*
* Utdata: färdig, aktive rad på TV
* Inmatade: *Stäng av lamporna*
* Utdata: OK, vilket stänger av alla lampor
* Inmatade: *inaktivera rums lampor*
* Utdata: OK, inaktivera rums belysningen

## <a name="use-a-custom-voice"></a>Använd en anpassad röst

Ett annat sätt att anpassa anpassade kommando svar är att välja en röst för utdata. Använd följande steg för att växla standard rösten till en anpassad röst:

1. I det anpassade kommando programmet väljer du **Inställningar** i rutan till vänster.
1. I fönstret i mitten väljer du **anpassad röst**.
1. I tabellen väljer du en anpassad röst eller en offentlig röst.
1. Välj **Spara**.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar exempel meningar och parametrar.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> För offentliga röster är neurala-typer bara tillgängliga för vissa regioner. Mer information finns i [regioner som stöds av Speech service](./regions.md#standard-and-neural-voices).
>
> Du kan skapa anpassade röster på sidan **anpassat röst** projekt. Mer information finns i [Kom igång med anpassad röst](./how-to-custom-voice.md).

Programmet kommer nu att svara i den valda rösten i stället för standard rösten.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [integrerar dina anpassade kommando program](how-to-custom-commands-setup-speech-sdk.md) med en klient-app med hjälp av tal-SDK.
* [Konfigurera kontinuerlig distribution](how-to-custom-commands-deploy-cicd.md) för program med anpassade kommandon med hjälp av Azure-DevOps. 
                      
