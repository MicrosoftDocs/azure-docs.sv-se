---
title: Konfigurera och hantera Azure Notebooks för hands version
description: Lär dig hur du hanterar projekt-metadata, projektfiler, projektets miljö och installations steg via både Azure Notebooks användar gränssnitt och direkt åtkomst till terminalen.
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: devx-track-python
ms.openlocfilehash: fb106132cf0dfb14dc41c1d4b480fd3a1d7fd7fe
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093544"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a>Hantera och konfigurera projekt i Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ett projekt i Azure Notebooks för hands versionen är i grunden en konfiguration av den underliggande virtuella Linux-datorn där Jupyter-anteckningsböcker körs, tillsammans med en filmapp och beskrivande metadata. 

Med instrument panelen för projektet i Azure Notebooks kan du hantera filer och på annat sätt konfigurera projektets egenskaper:

- Beräknings nivån som projektet körs på, vilket kan vara den kostnads fria nivån eller en virtuell Azure-dator.
- Project-metadata, som innehåller ett namn, en beskrivning, en identifierare som används vid delning av projektet och om projektet är offentligt eller privat.
- Projektets bärbara dator, data och andra filer som du hanterar precis som andra fil system.
- Ett projekts miljö, som du hanterar antingen via Start skript eller direkt via terminalen.
- Loggar, som du kommer åt via terminalen.

> [!Note]
> De hanterings-och konfigurations funktioner som beskrivs här är endast tillgängliga för den projekt ägare som skapade projektet från början. Du kan dock klona projektet till ditt eget konto, i vilket fall du blir ägare och kan konfigurera projektet efter behov.

Azure Notebooks startar den underliggande virtuella datorn när du kör en antecknings bok eller annan fil. Servern sparar automatiskt filer och stängs ned efter 60 minuter av inaktivitet. Du kan också stoppa servern när som helst med kommandot **shutdown** (kortkommando: h).

## <a name="compute-tier"></a>Beräkningsnivå

Som standard körs projekt på den **kostnads fria beräknings** nivån, som är begränsad till 4 GB minne och 1 GB data för att förhindra missbruk. Du kan kringgå de här begränsningarna och öka beräknings kraften genom att använda en annan virtuell dator som du har etablerad i en Azure-prenumeration. Mer information finns i [så här använder du data vetenskap Virtual Machines](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Redigera metadata för projektet

På instrument panelen för projektet väljer du **projekt inställningar** och väljer sedan fliken **information** som innehåller projektets metadata enligt beskrivningen i följande tabell. Du kan ändra projektets metadata när som helst.

| Inställning | Beskrivning |
| --- | --- |
| Projektnamn | Ett eget namn för ditt projekt som Azure Notebooks används för visning. Till exempel "Hello World i python". |
| Projekt-ID | En anpassad identifierare som blir en del av den URL som du använder för att dela ett projekt. Detta ID får endast använda bokstäver, siffror och bindestreck, får innehålla högst 30 tecken och får inte vara ett [reserverat projekt-ID](create-clone-jupyter-notebooks.md#reserved-project-ids). Om du är osäker på vad du ska använda, är en gemensam konvention att använda en gemen version av ditt projekt namn där blank steg är i bindestreck, till exempel "Min-anteckningsbok-Project" (trunkeras om det behövs för att få plats för längd begränsningen). |
| Offentligt projekt | Om den här inställningen är aktive rad kan vem som helst med länken komma åt projektet. Ta bort det här alternativet när du skapar ett privat projekt. |
| Dölj kloner | Om det är inställt kan andra användare inte se en lista över kloner som har gjorts för det här projektet. Att dölja kloner är användbart för projekt som delas med många personer som inte är en del av samma organisation, till exempel när en bärbar dator används för att under lätta för undervisning av en klass. |

> [!Important]
>
> Om du ändrar projekt-ID: t avverifieras länkar till projektet som du kanske har delat tidigare.

## <a name="manage-project-files"></a>Hantera projektfiler

Instrument panelen för projektet visar innehållet i projektets mapp system. Du kan använda olika kommandon för att hantera dessa filer.

### <a name="create-new-files-and-folders"></a>Skapa nya filer och mappar

Kommandot **+ New** (kortkommando: n) skapar nya filer eller mappar. När du använder kommandot väljer du först den typ av objekt som ska skapas:

| Objekt typ | Description | Kommando beteende |
| --- | --- | --- |
| **Notebook-fil** | En Jupyter Notebook | Visar ett popup-fönster där du kan ange antecknings bokens fil namn och språk. |
| **Mapp** | En undermapp | Skapar ett redigerings fält i projektets fillista där du anger mappnamnet. |
| **Tom fil** | En fil där du kan lagra innehåll, till exempel text, data osv. | Skapar ett redigerings fält i projektets fillista där du anger fil namnet. |
| **Markdown** | En MARKDOWN-fil. | Skapar ett redigerings fält i projektets fillista där du anger fil namnet. |

### <a name="upload-files"></a>Ladda upp filer

**Uppladdnings** kommandot innehåller två alternativ för att importera data från andra platser: **från URL** och **från dator**. Mer information finns i [arbeta med datafiler i Azure Notebook-projekt](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Välj filvissa kommandon

Varje objekt i projektets fil lista innehåller kommandon via en snabb meny för snabb klickning:

![Kommandon på en fil snabb meny](media/project-file-commands.png)

| Kommando | Kortkommando | Action |
| --- | --- | --- |
| Kör | r (eller klicka) | Kör en Notebook-fil. Andra filtyper öppnas för visning.  |
| Kopiera länk | y | Kopierar en länk till filen till Urklipp. |
| Kör i Jupyter Lab | j | Kör en antecknings bok i JupyterLab, som är ett mer utvecklare-orienterat gränssnitt än Jupyter vanligt vis tillhandahåller. |
| Förhandsgranskning | P | Öppnar en HTML-förhandsgranskning av filen. för antecknings böcker är förhands granskningen en skrivskyddad åter givning av antecknings boken. Mer information finns i avsnittet för [hands version](#preview) . |
| Redigera fil | i | Öppnar filen för redigering. |
| Ladda ned | d | Laddar ned en zip-fil som innehåller filen eller innehållet i en mapp. |
| Byt namn | a | Efterfrågar ett nytt namn för filen eller mappen. |
| Ta bort | x | Du uppmanas att bekräfta och sedan ta bort filen från projektet permanent. Det går inte att återställa borttagningar. |
| Flytta | m | Flyttar en fil till en annan mapp i samma projekt. |

#### <a name="preview"></a>Förhandsgranskning

En för hands version av en fil eller antecknings bok är en skrivskyddad vy av innehållet. att köra antecknings böcker är inaktiverat. En för hands version visas för alla som har en länk till filen eller antecknings boken, men som inte har loggat in på Azure Notebooks. När du har loggat in kan en användare klona antecknings boken till sitt eget konto, eller så kan de Ladda ned antecknings boken till den lokala datorn.

Förhands gransknings sidan stöder flera verktygsfälts kommandon med kortkommandon:

| Kommando | Kortkommando | Action |
| --- | --- | --- |
| Dela | s | Visar popup-fönstret för delning som du kan använda för att få en länk, dela till sociala medier, Hämta HTML för inbäddning och skicka ett e-postmeddelande. |
| Klona | c  | Klona antecknings boken till ditt konto. |
| Kör | r | Kör antecknings boken om du har behörighet att göra det. |
| Ladda ned | d | Laddar ned en kopia av antecknings boken. |

## <a name="configure-the-project-environment"></a>Konfigurera projekt miljön

Det finns tre sätt att konfigurera miljön för den underliggande virtuella dator där dina antecknings böcker körs:

- Ta med ett initierings skript vid ett tillfälle
- Använd projektets miljö inställningar för att ange installations steg
- Få åtkomst till den virtuella datorn via en Terminal.

Alla former av projekt konfiguration tillämpas varje gång den virtuella datorn startas och påverkar därmed alla antecknings böcker i projektet.

### <a name="one-time-initialization-script"></a>Initierings skript vid ett tillfälle

Första gången Azure Notebooks skapar en server för projektet söker den efter en fil i projektet som heter *aznbsetup.sh*. Om den här filen finns körs Azure Notebooks. Utdata från skriptet lagras i projektmappen som *. aznbsetup. log*.

### <a name="environment-setup-steps"></a>Installations steg för miljön

Du kan använda projektets miljö inställningar för att skapa enskilda steg som konfigurerar miljön.

På instrument panelen för projektet väljer du **projekt inställningar** och väljer sedan fliken **miljö** där du lägger till, tar bort och ändrar installations stegen för projektet:

![Popup-fliken för projekt inställningar med fliken miljö vald](media/project-settings-environment-steps.png)

Om du vill lägga till ett steg väljer du **+ Lägg till** och väljer sedan en stegtyp i list rutan **åtgärd** :

![Åtgärds väljare för det nya installations steget för miljön](media/project-settings-environment-details.png)

Den information som du sedan projektet beror på vilken typ av åtgärd du har valt:

- **Requirements.txt**: välj en *requirements.txt* -fil som redan finns i projektet i den andra List rutan. Välj sedan en python-version från den tredje List rutan som visas. Genom att använda en *requirements.txt* -fil körs Azure Notebooks `pip install -r` med *requirements.txt* -filen när du startar en Notebook-Server. Du behöver inte uttryckligen installera paket inifrån själva antecknings boken.

- **Gränssnitts skript**: i den andra List rutan väljer du ett bash-gränssnitts skript i projektet (vanligt vis en fil med fil namns tillägget *. sh* ) som innehåller alla kommandon som du vill köra för att initiera miljön.

- **Miljö. yml**: i den andra List rutan väljer du en *miljö. yml* -fil för python-projekt med hjälp av en Conda-miljö.

   > [!WARNING]
   > Eftersom det här är en för hands versions tjänst under utveckling finns det ett känt problem där `Environment.yml` inställningen inte tillämpas på projektet som förväntat. Projektet och Jupyter-anteckningsbokarna i läser inte in den angivna miljö filen för närvarande.

När du är klar med att lägga till steg väljer du **Spara**.

### <a name="use-the-terminal"></a>Använd terminalen

På instrument panelen för projektet öppnar **Terminal** -kommandot en Linux-terminal som ger direkt åtkomst till servern. I terminalen kan du hämta data, redigera eller hantera filer, inspektera processer och även använda verktyg som vi och nano.

> [!Note]
> Om du har start skript i projekt miljön kan det hända att när du öppnar terminalen visas ett meddelande som anger att installationen fortfarande pågår.

Du kan utfärda alla vanliga Linux-kommandon i terminalen. Du kan också använda `ls` i arbetsmappen för att se de olika miljöer som finns på den virtuella datorn, till exempel *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp* och *R*, tillsammans med en *projektmapp* som innehåller projektet:

![Projektets Terminal i Azure Notebooks](media/project-terminal.png)

Om du vill påverka en speciell miljö måste du först ändra katalogerna till denna miljö.

För python-miljöer kan du hitta `pip` och `conda` i mappen *bin* i varje miljö. Du kan också använda inbyggda alias för miljöer:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Ändringar som görs på servern gäller endast för den aktuella sessionen, förutom filer och mappar som du skapar i själva *projektmappen* . Till exempel är redigering av en fil i projektmappen beständig mellan sessioner, men paket med `pip install` är inte.

> [!Note]
> Om du använder `python` eller `python3` , anropar du de systeminstallerade versionerna av python, som inte används för antecknings böcker. Du har inte behörighet för åtgärder som `pip install` antingen, så se till att använda de versions bara aliasen.

## <a name="access-notebook-logs"></a>Komma åt Notebook-loggar

Om du stöter på problem när du kör en bärbar dator lagras utdata från Jupyter i en mapp med namnet *. Obs. log*. Du kan komma åt dessa loggar via **Terminal** -kommandot eller instrument panelen för projektet.

Vanligt vis när du kör Jupyter lokalt kan du ha startat den från ett terminalfönster. Terminalfönstret visar utdata, till exempel kernel-status.

Om du vill visa loggar använder du följande kommando i terminalen:

```bash
cat .nb.log
```

Du kan också använda kommandot från en kod cell i en python-anteckningsbok:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Nästa steg

- [Gör så här: arbeta med projektfiler](work-with-project-data-files.md)
- [Komma åt moln data i en bärbar dator](access-data-resources-jupyter-notebooks.md)
