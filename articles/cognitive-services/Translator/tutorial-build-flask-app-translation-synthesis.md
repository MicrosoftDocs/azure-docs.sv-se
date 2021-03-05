---
title: 'Självstudie: Bygg en kolv-app för att översätta, syntetisera och analysera text översättare'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en mätkolv-baserad webbapp för att översätta text, analysera sentiment och syntetisera översatt text till tal.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: lajanuar
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: c04bac76453d565abb99a971386b9ce0461b88ae
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172087"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Självstudie: Bygg en kolv-app med Azure Cognitive Services

I den här självstudien skapar du en mätkolv som använder Azure-Cognitive Services för att översätta text, analysera sentiment och syntetisera översatt text till tal. Vi fokuserar på de python-vägar och Flask vägar som möjliggör vårt program, men vi kommer att hjälpa dig med HTML och Java Script som hämtar appen tillsammans. Om du stöter på problem kan du använda knappen feedback nedan.

Den här själv studie kursen beskriver följande:

> [!div class="checklist"]
> * Hämta prenumerations nycklar för Azure
> * Konfigurera utvecklings miljön och installera beroenden
> * Skapa en kolv-app
> * Använd Translator för att översätta text
> * Använd Textanalys för att analysera positiva/negativa sentiment för indatamängds text och översättningar
> * Använda tal tjänster för att konvertera översatt text till syntetiskt tal
> * Kör din flaska app lokalt

> [!TIP]
> Om du vill hoppa framåt och se all kod samtidigt, är hela exemplet tillsammans med build-instruktioner tillgängliga på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Vad är Flask?

Kolv är ett mikroramverk för att skapa webb program. Det innebär att kolven ger dig verktyg, bibliotek och tekniker som gör det möjligt att bygga ett webb program. Det här webb programmet kan vara en del webb sidor, en blogg, en wiki eller en webbplats som är ett webbaserat kalender program eller en kommersiell webbplats.

För dem som vill ha djupet efter den här kursen är några användbara länkar:

* [Dokumentation om flaska](http://flask.pocoo.org/)
* [Kolv för Dummies – en nybörjar guide till kolv](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Förutsättningar

Vi går igenom de program-och prenumerations nycklar som du behöver i den här kursen.

* [Python-3.5.2 eller senare](https://www.python.org/downloads/)
* [Git-verktyg](https://git-scm.com/downloads)
* En IDE-eller text redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/) eller [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) eller [Firefox](https://www.mozilla.org/firefox)
* En prenumerations nyckel för **översättare** (du kan förmodligen använda den **globala** platsen.)
* En **textanalys** prenumerations nyckel i regionen **USA, västra** .
* En prenumerations nyckel för **tal tjänster** i regionen **USA, västra** .

## <a name="create-an-account-and-subscribe-to-resources"></a>Skapa ett konto och prenumerera på resurser

Som tidigare nämnts kommer du att behöva tre prenumerations nycklar för den här självstudien. Det innebär att du måste skapa en resurs i ditt Azure-konto för:
* Översättare
* Textanalys
* Speech Services

Använd [skapa ett Cognitive Services konto i Azure Portal](../cognitive-services-apis-create-account.md) för stegvisa instruktioner för att skapa resurser.

> [!IMPORTANT]
> I den här självstudien skapar du dina resurser i regionen USA, västra. Om du använder en annan region måste du justera bas-URL: en i var och en av dina python-filer.

## <a name="set-up-your-dev-environment"></a>Konfigurera din utvecklingsmiljö

Innan du skapar en-webbapp måste du skapa en arbets katalog för projektet och installera några python-paket.

### <a name="create-a-working-directory"></a>Skapa en arbets katalog

1. Öppna kommando raden (Windows) eller Terminal (macOS/Linux). Skapa sedan en arbets katalog och under kataloger för ditt projekt:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Ändra till projektets arbets katalog:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Skapa och aktivera din virtuella miljö med `virtualenv`

Nu ska vi skapa en virtuell miljö för vår mätkolv-app med hjälp av `virtualenv` . Med hjälp av en virtuell miljö kan du se till att du har en ren miljö att arbeta med.

1. I din arbets katalog kör du det här kommandot för att skapa en virtuell miljö: **MacOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Vi har uttryckligen deklarerat att den virtuella miljön bör använda python 3. Detta säkerställer att användare med flera python-installationer använder rätt version.

   **Windows CMD/Windows-bash:**
   ```
   virtualenv venv
   ```
   Vi håller på att vara enkla att namnge din virtuella miljö venv.

2. De kommandon som används för att aktivera den virtuella miljön varierar beroende på din plattform/gränssnitt:   

   | Plattform | Gränssnitt | Kommando |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Kommandorad | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   När du har kört det här kommandot bör du använda en kommando rad eller Terminal-session `venv` .

3. Du kan inaktivera sessionen när du vill genom att skriva in den i kommando raden eller terminalen: `deactivate` .

> [!NOTE]
> Python innehåller omfattande dokumentation för att skapa och hantera virtuella miljöer, se [virtuell miljö](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Installera förfrågningar

Begär Anden är en populär modul som används för att skicka HTTP 1,1-begäranden. Det finns inget behov av att manuellt lägga till frågesträngar i dina URL: er, eller för att forma-koda dina POST-data.

1. Kör följande om du vill installera begär Anden:

   ```
   pip install requests
   ```

> [!NOTE]
> Om du vill veta mer om begär Anden, se [förfrågningar: http för människor](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Installera och konfigurera en kolv

Nu måste vi installera kolv. Kolv hanterar routningen för vår webbapp och gör det möjligt för oss att ringa server-till-Server-anrop som döljer våra prenumerations nycklar från slutanvändaren.

1. Om du vill installera en mätkolv kör du:
   ```
   pip install Flask
   ```
   Nu ska vi se till att kolven har installerats. Kör följande:
   ```
   flask --version
   ```
   Versionen ska skrivas ut till terminalen. Något annat innebär att något har gått fel.

2. Om du vill köra kolv-appen kan du antingen använda flaska-kommandot eller python: s-m-switch med kolv. Innan du kan göra det måste du tala om för terminalen vilken app du ska arbeta med genom att exportera `FLASK_APP` miljövariabeln:

   **MacOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Skapa din kolv-app

I det här avsnittet ska du skapa en barebones kolv-app som returnerar en HTML-fil när användare träffar roten för din app. Spendera inte för mycket tid på att försöka hämta koden. vi kommer att gå tillbaka till att uppdatera filen senare.

### <a name="what-is-a-flask-route"></a>Vad är ett kolv flöde?

Låt oss ta en stund och prata om "[vägar](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Routning används för att binda en URL till en viss funktion. Kolv använder Route-dekoratörer för att registrera funktioner till vissa URL: er. Till exempel, när en användare navigerar till roten ( `/` ) i vår webbapp, `index.html` återges.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Låt oss ta en titt på ett annat exempel för att hamma den här bostaden.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Den här koden säkerställer att när en användare navigerar till `http://your-web-app.com/about` `about.html` filen återges.

De här exemplen illustrerar hur du återger HTML-sidor för en användare. vägar kan också användas för att anropa API: er när en knapp trycks ned, eller så kan du vidta ett antal åtgärder utan att behöva gå från start sidan. Du ser detta i åtgärd när du skapar vägar för översättning, sentiment och tal syntes.

### <a name="get-started"></a>Kom igång

1. Öppna projektet i IDE och skapa sedan en fil med namnet `app.py` i roten i din arbets katalog. Kopiera sedan koden till `app.py` och spara:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Det här kod blocket visar att appen visas `index.html` när en användare navigerar till roten för din webbapp ( `/` ).

2. Nu ska vi skapa klient delen för vår webbapp. Skapa en fil med namnet `index.html` i `templates` katalogen. Kopiera sedan koden till `templates/index.html` .

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Nu ska vi testa kolv-appen. Från terminalen kör du:

   ```
   flask run
   ```

4. Öppna en webbläsare och gå till den angivna URL: en. Du bör se appen med en sida. Tryck på **CTRL + C** för att avsluta appen.

## <a name="translate-text"></a>Översätt text

Nu när du har en uppfattning om hur en enkel kolv-app fungerar, kan du:

* Skriv lite python för att anropa Translator och returnera ett svar
* Skapa en kolv för att anropa din python-kod
* Uppdatera HTML-koden med ett ytdiagram för text ingångs-och översättning, en språk väljare och knappen Översätt
* Skriv java script som gör det möjligt för användare att interagera med din kolv-app från HTML

### <a name="call-the-translator"></a>Anropa Translator

Det första du behöver göra är att skriva en funktion för att anropa Translator. Den här funktionen tar två argument: `text_input` och `language_output` . Den här funktionen anropas när en användare trycker på knappen Översätt i din app. Text området i HTML-koden skickas som `text_input` och språk markering svärdet i HTML-koden skickas som `language_output` .

1. Vi börjar med att skapa en fil som kallas `translate.py` i roten i din arbets katalog.
2. Lägg sedan till den här koden i `translate.py` . Den här funktionen tar två argument: `text_input` och `language_output` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   location = 'YOUR_TRANSLATOR_RESOURCE_LOCATION'
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': location,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Lägg till prenumerations nyckeln för Translator och spara.

### <a name="add-a-route-to-apppy"></a>Lägg till en väg i `app.py`

Därefter måste du skapa en väg i din mätkolv-app som anropar `translate.py` . Den här vägen kommer att anropas varje gången en användare trycker på knappen Översätt i appen.

För den här appen kommer din väg att acceptera `POST` begär Anden. Detta beror på att funktionen förväntar sig att texten ska översättas och utmatnings språket för översättningen.

Kolv ger hjälp funktioner som hjälper dig att parsa och hantera varje begäran. I den angivna koden `get_json()` returnerar data från `POST` begäran som JSON. Sedan `data['text']` `data['to']` skickas värdena text-och utdata-språk till `get_translation()` funktionen som är tillgänglig från `translate.py` . Det sista steget är att returnera svaret som JSON, eftersom du måste visa dessa data i din webbapp.

I följande avsnitt upprepas processen när du skapar vägar för sentiment-analys och tal syntes.

1. Öppna `app.py` och leta upp import-instruktionen längst upp i `app.py` och Lägg till följande rad:

   ```python
   import translate
   ```
   Nu kan vår mätkolv-app använda metoden som är tillgänglig via `translate.py` .

2. Kopiera den här koden till slutet av `app.py` och spara:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Uppdatera `index.html`

Nu när du har en funktion för att översätta text, och en väg i din kolv-app för att anropa den, är nästa steg att börja skapa HTML för din app. I HTML-koden nedan finns några saker:

* Innehåller ett text utrymme där användare kan skriva text för översättning.
* Innehåller en språk väljare.
* Innehåller HTML-element för att återge identifierat språk och förtroende poäng som returneras under översättning.
* Innehåller ett skrivskyddat text utrymme där översättnings resultatet visas.
* Innehåller plats hållare för sentiment analys och tal syntes kod som du kommer att lägga till i den här filen senare i självstudien.

Uppdatera nu `index.html` .

1. Öppna `index.html` och leta upp följande kod kommentarer:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Ersätt kod kommentarerna med följande HTML-block:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

Nästa steg är att skriva vissa JavaScript-skript. Detta är bryggan mellan din HTML-och kolv-väg.

### <a name="create-mainjs"></a>Fram `main.js`  

`main.js`Filen är bryggan mellan din HTML-och kolv-väg. Din app kommer att använda en kombination av jQuery, Ajax och XMLHttpRequest för att återge innehåll och göra `POST` förfrågningar till dina Flask vägar.

I koden nedan används innehåll från HTML för att skapa en begäran till din kolv. Mer specifikt är innehållet i text-och språk väljarna kopplade till variabler och skickas sedan vidare i begäran till `translate-text` .

Koden itererar sedan igenom svaret och uppdaterar HTML med översättning, identifierat språk och förtroende poäng.

1. Skapa en fil med namnet `main.js` i katalogen från din IDE `static/scripts` .
2. Kopiera den här koden till `static/scripts/main.js` :
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Test Översättning

Låt oss testa översättningen i appen.

```
flask run
```

Navigera till den angivna Server adressen. Skriv text i Indatatyp, Välj ett språk och tryck på Översätt. Du bör få en översättning. Om det inte fungerar kontrollerar du att du har lagt till din prenumerations nyckel.

> [!TIP]
> Om ändringarna som du har gjort inte visas, eller om appen inte fungerar som förväntat, kan du försöka med att rensa cacheminnet eller öppna ett privat/Incognito-fönster.

Tryck på **CTRL + c** för att avsluta appen och gå sedan till nästa avsnitt.

## <a name="analyze-sentiment"></a>Analysera sentiment

[API för textanalys](../text-analytics/overview.md) kan användas för att utföra analys av sentiment, extrahera nyckel fraser från text eller identifiera käll språket. I den här appen ska vi använda sentiment analys för att avgöra om den tillhandahållna texten är positiv, neutral eller negativ. API:n returnerar en numerisk poäng mellan 0 och 1. Poängen nära 1 visar positiv sentiment och poängen nära 0 visar negativa sentiment.

I det här avsnittet ska du göra några saker:

* Skriv några python för att anropa API för textanalys för att utföra sentiment-analys och returnera ett svar
* Skapa en kolv för att anropa din python-kod
* Uppdatera HTML-koden med ett utrymme för sentiment resultat och en knapp för att utföra analyser
* Skriv java script som gör det möjligt för användare att interagera med din kolv-app från HTML

### <a name="call-the-text-analytics-api"></a>Anropa API:t för textanalys

Nu ska vi skriva en funktion för att anropa API för textanalys. Funktionen tar fyra argument: `input_text` , `input_language` , `output_text` och `output_language` . Den här funktionen anropas när en användare trycker på analys knappen Kör sentiment i din app. Data som tillhandahålls av användaren från text området och språk väljaren, samt det identifierade språket och översättnings utdata finns i varje begäran. Objektet Response innehåller sentiment resultat för källan och översättningen. I följande avsnitt kommer du att skriva vissa JavaScript-skript för att parsa svaret och använda det i din app. Nu ska vi fokusera på att anropa API för textanalys.

1. Nu ska vi skapa en fil `sentiment.py` som kallas i roten i din arbets katalog.
2. Lägg sedan till den här koden i `sentiment.py` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'
   endpoint = "YOUR_TEXT_ANALYTICS_ENDPOINT" 
   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language):
       path = '/text/analytics/v3.0/sentiment'
       constructed_url = endpoint + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Lägg till din Textanalys prenumerations nyckel och spara.

### <a name="add-a-route-to-apppy"></a>Lägg till en väg i `app.py`

Nu ska vi skapa en väg i din mätkolv-app som anropar `sentiment.py` . Den här vägen kommer att anropas varje gången en användare trycker på analys knappen Kör sentiment i din app. Precis som vägen för översättning kommer den här vägen att acceptera `POST` begär Anden eftersom funktionen förväntar sig argument.

1. Öppna `app.py` och leta upp import-instruktionen överst i `app.py` och uppdatera den:

   ```python
   import translate, sentiment
   ```
   Nu kan vår mätkolv-app använda metoden som är tillgänglig via `sentiment.py` .

2. Kopiera den här koden till slutet av `app.py` och spara:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Uppdatera `index.html`

Nu när du har en funktion för att köra sentiment-analys och en väg i din kolv-app för att anropa den, är nästa steg att börja skriva HTML-koden för din app. I HTML-koden nedan finns några saker:

* Lägger till en knapp i appen för att köra sentiment-analys
* Lägger till ett element som förklarar sentiment-Poäng
* Lägger till ett-element för att Visa sentiment-poängen

1. Öppna `index.html` och leta upp följande kod kommentarer:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Ersätt kod kommentarerna med följande HTML-block:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment can be labeled as "positive", "negative", "neutral", or "mixed". </p>
      <strong>Sentiment label for input:</strong> <span id="input-sentiment"></span><br />
   </div>
   ```

### <a name="update-mainjs"></a>Uppdatera `main.js`

I koden nedan används innehåll från HTML för att skapa en begäran till din kolv. Mer specifikt är innehållet i text-och språk väljarna kopplade till variabler och skickas sedan vidare i begäran till `sentiment-analysis` vägen.

Koden itererar sedan igenom svaret och uppdaterar HTML med sentiment-poängen.

1. Skapa en fil med namnet `main.js` i katalogen från din IDE `static` .

2. Kopiera den här koden till `static/scripts/main.js` :
   ```javascript
   //Run sentiment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage};

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].sentiment;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== ''){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Testa sentiment-analys

Nu ska vi testa sentiment analys i appen.

```
flask run
```

Navigera till den angivna Server adressen. Skriv text i Indatatyp, Välj ett språk och tryck på Översätt. Du bör få en översättning. Tryck sedan på knappen Kör sentiment analys. Du bör se två resultat. Om det inte fungerar kontrollerar du att du har lagt till din prenumerations nyckel.

> [!TIP]
> Om ändringarna som du har gjort inte visas, eller om appen inte fungerar som förväntat, kan du försöka med att rensa cacheminnet eller öppna ett privat/Incognito-fönster.

Tryck på **CTRL + c** för att avsluta appen och gå sedan till nästa avsnitt.

## <a name="convert-text-to-speech"></a>Konvertera text-till-tal

[Text till tal-API: et](../speech-service/text-to-speech.md) gör det möjligt för din app att konvertera text till naturligt mänskligt syntetiskt tal. Tjänsten stöder standard-, neurala-och anpassade röster. Vår exempel app använder en fåtal av tillgängliga röster, för en fullständig lista, se [språk som stöds](../speech-service/language-support.md#text-to-speech).

I det här avsnittet ska du göra några saker:

* Skriv en del python för att konvertera text till tal med text till tal-API: et
* Skapa en kolv för att anropa din python-kod
* Uppdatera HTML med en knapp för att konvertera text till tal och ett element för ljud uppspelning
* Skriv java script som gör det möjligt för användare att interagera med din kolv-app

### <a name="call-the-text-to-speech-api"></a>Anropa text-till-Speech API

Nu ska vi skriva en funktion för att konvertera text till tal. Den här funktionen tar två argument: `input_text` och `voice_font` . Den här funktionen anropas när en användare trycker på knappen omvandla text till tal i din app. `input_text` är översättnings resultatet som returneras av anropet till Översätt text, `voice_font` värdet från röst teckensnitts väljaren i HTML-koden.

1. Nu ska vi skapa en fil `synthesize.py` som kallas i roten i din arbets katalog.

2. Lägg sedan till den här koden i `synthesize.py` .
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Lägg till din prenumerations nyckel för tal tjänster och spara.

### <a name="add-a-route-to-apppy"></a>Lägg till en väg i `app.py`

Nu ska vi skapa en väg i din mätkolv-app som anropar `synthesize.py` . Den här vägen kommer att anropas varje gången en användare trycker på knappen omvandla text till tal i din app. Precis som vägarna för översättning och sentiment analys kommer den här vägen att acceptera `POST` begär Anden eftersom funktionen förväntar sig två argument: texten att syntetisera och röst teckensnittet för uppspelning.

1. Öppna `app.py` och leta upp import-instruktionen överst i `app.py` och uppdatera den:

   ```python
   import translate, sentiment, synthesize
   ```
   Nu kan vår mätkolv-app använda metoden som är tillgänglig via `synthesize.py` .

2. Kopiera den här koden till slutet av `app.py` och spara:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Uppdatera `index.html`

Nu när du har en funktion för att konvertera text till tal och en väg i din kolv-app för att anropa den, är nästa steg att börja skriva HTML-koden för din app. I HTML-koden nedan finns några saker:

* Visar en listruta för röst val
* Lägger till en knapp för att konvertera text till tal
* Lägger till ett ljud element som används för att spela upp det syntetiska talet

1. Öppna `index.html` och leta upp följande kod kommentarer:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Ersätt kod kommentarerna med följande HTML-block:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yating, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Heami</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Leta sedan upp följande kod kommentarer:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Ersätt kod kommentarerna med följande HTML-block:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Se till att spara ditt arbete.

### <a name="update-mainjs"></a>Uppdatera `main.js`

I koden nedan används innehåll från HTML för att skapa en begäran till din kolv. Mer specifikt är översättningen och röst teckensnittet tilldelade till variabler och skickas sedan vidare i begäran till `text-to-speech` vägen.

Koden itererar sedan igenom svaret och uppdaterar HTML med sentiment-poängen.

1. Skapa en fil med namnet `main.js` i katalogen från din IDE `static` .
2. Kopiera den här koden till `static/scripts/main.js` :
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. Nästan klart. Det sista du ska göra är att lägga till en kod för `main.js` att automatiskt välja ett röst teckensnitt baserat på det språk som valts för översättning. Lägg till det här kod blocket i `main.js` :
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Testa appen

Vi testar tal syntesen i appen.

```
flask run
```

Navigera till den angivna Server adressen. Skriv text i Indatatyp, Välj ett språk och tryck på Översätt. Du bör få en översättning. Välj sedan en röst och tryck sedan på knappen konvertera text till tal. översättningen ska spelas upp som syntetiskt tal. Om det inte fungerar kontrollerar du att du har lagt till din prenumerations nyckel.

> [!TIP]
> Om ändringarna som du har gjort inte visas, eller om appen inte fungerar som förväntat, kan du försöka med att rensa cacheminnet eller öppna ett privat/Incognito-fönster.

Det innebär att du har en fungerande app som utför översättningar, analyserar sentiment och syntetiskt tal. Tryck på **CTRL + c** för att avsluta appen. Se till att ta en titt på de andra [Azure-Cognitive Services](../index.yml).

## <a name="get-the-source-code"></a>Hämta källkoden

Käll koden för det här projektet är tillgänglig på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Nästa steg

* [Translator-referens](./reference/v3-0-reference.md)
* [API för textanalys – referens](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Referens för text till tal-API](../speech-service/rest-text-to-speech.md)