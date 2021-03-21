---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620185"
---
## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* En fördjupad läsar resurs som kon figurer ATS för Azure Active Directory autentisering. Följ [dessa instruktioner](../../how-to-create-immersive-reader.md) för att konfigurera.  Du behöver några av de värden som skapas här när du konfigurerar miljö egenskaperna. Spara utdata från sessionen i en textfil för framtida bruk.
* [Git](https://git-scm.com/).
* [SDK för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) och [pip](https://docs.python.org/3/installing/index.html). Från och med python 3,4 ingår pip som standard med de binära installations programmen för python.
* [Kolv](https://flask.palletsprojects.com/en/1.0.x/).
* [Jinja](http://jinja.pocoo.org/docs/2.10/).
* [virtuell miljö](https://virtualenv.pypa.io/en/latest/) och [virtualenvwrapper – Win för Windows](https://pypi.org/project/virtualenvwrapper-win/) eller [virtualenvwrapper för OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* [Modulen begär Anden](https://pypi.org/project/requests/2.7.0/).
* En IDE, till exempel [Visual Studio Code](https://code.visualstudio.com/).

## <a name="configure-authentication-credentials"></a>Konfigurera autentiseringsuppgifter för autentisering

Skapa en ny fil med namnet **. kuvert** och klistra in följande namn och värden i den. Ange de värden som anges när du skapade din fördjupade läsare-resurs.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Spara inte filen i käll kontrollen eftersom den innehåller hemligheter som inte ska publiceras.

Skydda **getimmersivereadertoken** API-slutpunkten bakom någon form av autentisering, till exempel [OAuth](https://oauth.net/2/). Autentisering förhindrar att obehöriga användare får till gång till token som ska användas mot tjänsten för avancerad läsare och fakturering. Detta arbete ligger utanför den här självstudiens omfattning.

## <a name="create-a-python-web-app-on-windows"></a>Skapa en python-webbapp i Windows

Skapa en python-webbapp med `flask` i Windows.

Installera [git](https://git-scm.com/).

När git har installerats öppnar du en kommando tolk och klonar den git-lagringsplatsen för avancerad Reader-SDK till en mapp på datorn.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installera [python](https://www.python.org/downloads/).

Markera kryss rutan **Lägg till python till sökväg** .

![Dialog rutan python Windows-installation steg 1](../../media/pythoninstallone.jpg)

Lägg till **valfria funktioner** genom att markera kryss rutor och välj sedan **Nästa**.

![Dialog rutan python Windows-installation steg 2](../../media/pythoninstalltwo.jpg)

Välj **anpassad installation** och ange installations Sök vägen som rotmapp, till exempel `C:\Python37-32\` . Välj sedan **Installera**.

![Dialog rutan python Windows-installation steg 3](../../media/pythoninstallthree.jpg)

När python-installationen är slutförd öppnar du en kommando tolk och använder `cd` för att gå till Python-skript-mappen.

```cmd
cd C:\Python37-32\Scripts
```

Installations flaska.

```cmd
pip install flask
```

Installera Jinja2. Det är en fullt aktuell mall för python.

```cmd
pip install jinja2
```

Installera virtuell miljö. Det här verktyget skapar isolerade python-miljöer.

```cmd
pip install virtualenv
```

Installera virtualenvwrapper – Win. Idén bakom virtualenvwrapper är att under lätta användningen av virtuell miljö.

```cmd
pip install virtualenvwrapper-win
```

Installera modulen begär Anden. Begär Anden är ett apache2 licensierat HTTP-bibliotek, skrivet i python.

```cmd
pip install requests
```

Installera python-dotenv-modulen. Den här modulen läser nyckel/värde-paret från. miljö filen och lägger till dem i miljö variabeln.

```cmd
pip install python-dotenv
```

Skapa en virtuell miljö.

```cmd
mkvirtualenv advanced-python
```

Använd `cd` för att gå till rotmappen för exempel projekt.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Anslut exempel projektet till-miljön. Den här åtgärden mappar den nyligen skapade virtuella miljön till rotmappen för exempel projekt.

```cmd
setprojectdir .
```

Aktivera den virtuella miljön.

```cmd
activate
```

Projektet bör nu vara aktivt och du ser något som liknar `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` i kommando tolken.

Inaktivera miljön.

```cmd
deactivate
```

`(advanced-python)`Prefixet måste vara borta eftersom miljön är inaktive rad.

Om du vill återaktivera miljön kör du `workon advanced-python` från exempel projektets rotmapp.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

När miljön är aktiv kan du köra exempelprojektet genom att ange `flask run` från rotmappen för exempel projekt.

```cmd
flask run
```

Öppna webbläsaren och gå till http://localhost:5000 .

## <a name="create-a-python-web-app-on-osx"></a>Skapa en python-webbapp på OSX

Skapa en python-webbapp genom att använda `flask` på OSX.

Installera [git](https://git-scm.com/).

När git har installerats öppnar du terminalen och klonar den git-lagringsplatsen för avancerad Reader-SDK till en mapp på datorn.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installera [python](https://www.python.org/downloads/).

Mappen python, till exempel, `Python37-32` bör nu finnas i mappen program.

När python-installationen är slutförd öppnar du Terminal och använder `cd` för att gå till Python-skript-mappen.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installera pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Kör följande kod för att installera pip för den aktuella inloggade användaren för att undvika problem med behörighet.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Ange ditt lösen ord när du uppmanas till det.
- Lägg till sökvägen till din pip-installation till din PATH-variabel.
- Gå till slutet av filen och ange den sökväg som du vill lägga till som sista objekt i listan, till exempel `PATH=$PATH:/usr/local/bin` .
- Välj **CTRL + X** om du vill avsluta.
- Skriv **Y** för att spara den ändrade bufferten.
- Klart! Testa det genom att ange i ett nytt terminalfönster `echo $PATH` .

Installations flaska.

```bash
pip install flask --user
```

Installera Jinja2. Det är en fullt aktuell mall för python.

```bash
pip install Jinja2 --user
```

Installera virtuell miljö. Det här verktyget skapar isolerade python-miljöer.

```bash
pip install virtualenv --user
```

Installera virtualenvwrapper. Idén bakom virtualenvwrapper är att under lätta användningen av virtuell miljö.

```bash
pip install virtualenvwrapper --user
```

Installera modulen begär Anden. Begär Anden är ett apache2 licensierat HTTP-bibliotek, skrivet i python.

```bash
pip install requests --user
```

Installera python-dotenv-modulen. Den här modulen läser nyckel/värde-paret från. miljö filen och lägger till dem i miljö variabeln.

```bash
pip install python-dotenv --user
```

Välj en mapp där du vill behålla de virtuella miljöerna och kör följande kommando:

```bash
mkdir ~/.virtualenvs
```

Använd `cd` för att gå till mappen avancerad Reader-SDK python-exempel.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Skapa en virtuell miljö.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Anslut exempel projektet till-miljön. Den här åtgärden mappar den nyligen skapade virtuella miljön till rotmappen för exempel projekt.

```bash
setprojectdir .
```

Aktivera den virtuella miljön.

```bash
activate
```

Projektet bör nu vara aktivt och du ser något som liknar `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` i kommando tolken.

Inaktivera miljön.

```bash
deactivate
```

`(advanced-python)`Prefixet måste vara borta eftersom miljön är inaktive rad.

Om du vill återaktivera miljön kör du `workon advanced-python` från exempel projektets rotmapp.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

När miljön är aktiv kan du köra exempelprojektet genom att ange `flask run` från rotmappen för exempel projekt.

```bash
flask run
```

Öppna webbläsaren och gå till http://localhost:5000 .

## <a name="next-steps"></a>Nästa steg

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](../../reference.md).
* Visa kod exempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
