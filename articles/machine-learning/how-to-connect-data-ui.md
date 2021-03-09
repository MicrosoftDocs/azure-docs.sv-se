---
title: Ansluta till data i Storage Services på Azure
titleSuffix: Azure Machine Learning
description: Skapa data lager och data uppsättningar för att på ett säkert sätt ansluta till data i lagrings tjänster i Azure med Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: 0e2230bdcf2b2f4358db4a0d600506b711f39c45
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508239"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Ansluta till data med Azure Machine Learning Studio

I den här artikeln får du lära dig hur du får åtkomst till dina data med [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). Anslut till dina data i lagrings tjänster på Azure med [Azure Machine Learning data lager](how-to-access-data.md)och paketera sedan data för aktiviteter i dina ml-arbetsflöden med [Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md).

I följande tabell definieras och sammanfattas fördelarna med data lager och data uppsättningar. 

|Objekt|Beskrivning| Fördelar|   
|---|---|---|
|Datalager| Anslut säkert till lagrings tjänsten på Azure genom att lagra din anslutnings information, t. ex. prenumerations-ID och token-auktorisering i [Key Vault](https://azure.microsoft.com/services/key-vault/) som är kopplade till arbets ytan | Eftersom informationen lagras på ett säkert sätt kan du <br><br> <li> Ange &nbsp; inte &nbsp; &nbsp; autentiseringsuppgifter &nbsp; för autentisering eller &nbsp; ursprungliga &nbsp; data källor i risk zonen. <li> Behöver du inte längre hårdkoda dem i dina skript.
|Datauppsättningar| Genom att skapa en datamängd skapar du en referens till datakällans plats, tillsammans med en kopia av dess metadata. Med data uppsättningar kan du <br><br><li> Få åtkomst till data under modell träning.<li> Dela data och samar beta med andra användare.<li> Utnyttja bibliotek med öppen källkod, som Pandas, för data utforskning. | Eftersom data uppsättningar är Lazy utvärderas och data finns kvar på sin befintliga plats, <br><br><li>Behåll en enda kopia av data i lagrings utrymmet.<li> Ingen extra lagrings kostnad <li> Riskerar inte att oavsiktligt ändra dina ursprungliga data källor.<li>Förbättra prestanda hastigheten i ML-arbetsflöde. 

Information om var data lager och data uppsättningar passar i Azure Machine Learning programmets övergripande data åtkomst arbets flöde finns i artikeln [säker åtkomst till data](concept-data.md#data-workflow) .

För en kod första gången kan du läsa följande artiklar för att använda [Azure Machine Learning python SDK](/python/api/overview/azure/ml/) för att:
* [Anslut till Azure Storage-tjänster med data lager](how-to-access-data.md). 
* [Skapa Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- Åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta. [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

    -  När du skapar en arbets yta registreras en Azure Blob-behållare och en Azure-filresurs automatiskt som data lager till arbets ytan. De heter `workspaceblobstore` `workspacefilestore` respektive. Om Blob Storage räcker för dina behov `workspaceblobstore` anges den som standard data lager och har redan kon figurer ATS för användning. Annars behöver du ett lagrings konto i Azure med en [lagrings typ som stöds](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Skapa data lager

Du kan skapa data lager från [dessa Azure Storage-lösningar](how-to-access-data.md#matrix). **För lagrings lösningar som inte stöds** och för att spara utgående kostnader under ml-experiment måste du [flytta dina data](how-to-access-data.md#move) till en Azure Storage-lösning som stöds. [Läs mer om data lager](how-to-access-data.md). 

Skapa ett nytt data lager med några steg med Azure Machine Learning Studio.

> [!IMPORTANT]
> Om ditt data lagrings konto finns i ett virtuellt nätverk krävs ytterligare konfigurations steg för att se till att Studio har åtkomst till dina data. Se [nätverks isolering & sekretess](how-to-enable-studio-virtual-network.md) för att se till att lämpliga konfigurations steg tillämpas.

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **data lager** i det vänstra fönstret under **Hantera**.
1. Välj **+ nytt data lager**.
1. Fyll i formuläret för att skapa och registrera ett nytt data lager. Formuläret uppdateras intelligent baserat på dina val för Azures lagrings typ och autentiseringstyp. Se [avsnittet lagrings åtkomst och behörigheter](#access-validation) för att förstå var du hittar autentiseringsuppgifterna för autentisering som du behöver fylla i det här formuläret.

Följande exempel visar hur formuläret ser ut när du skapar ett **Azure Blob-datalager**:

![Formulär för ett nytt data lager](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Skapa datauppsättningar

När du har skapat ett data lager skapar du en data uppsättning för att interagera med dina data. Data uppsättningar paketerar dina data i ett Lazy utvärderat förbruknings Bart objekt för Machine Learning-uppgifter, till exempel träning. [Läs mer om datamängder](how-to-create-register-datasets.md).

Det finns två typer av data uppsättningar, FileDataset och TabularDataset. 
[FileDatasets](how-to-create-register-datasets.md#filedataset) skapar referenser till en eller flera filer eller offentliga URL: er. [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) representerar dina data i tabell format. 

Följande steg och animering visar hur du skapar en data uppsättning i [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> Data uppsättningar som skapats med Azure Machine Learning Studio registreras automatiskt på arbets ytan.

![Skapa en data uppsättning med användar gränssnittet](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Så här skapar du en data uppsättning i Studio:
1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **data uppsättningar** i avsnittet **till gångar** i det vänstra fönstret.
1. Välj **skapa data uppsättning** för att välja källa för din data uppsättning. Den här källan kan vara lokala filer, ett data lager, offentliga URL: er eller [Azure Open-datauppsättningar](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).
1. Välj **tabell** eller **fil** för data uppsättnings typ.
1. Välj **Nästa** för att öppna formuläret **data lager och fil markering** . I det här formuläret väljer du var du vill behålla din data uppsättning när du har skapat den, samt hur du väljer vilka datafiler som ska användas för data uppsättningen.
    1. Aktivera hoppa över verifiering om dina data finns i ett virtuellt nätverk. Läs mer om [isolering av virtuella nätverk och sekretess](how-to-enable-studio-virtual-network.md).
    1. För tabell data uppsättningar kan du ange ett ' timeseries '-trait för att aktivera tidsrelaterade åtgärder för din data uppsättning. Lär dig hur du [lägger till timeseries-traiten i din data uppsättning](how-to-monitor-datasets.md#studio-dataset).
1. Välj **Nästa** för att fylla i **inställningarna och för hands versionen** och **schema** formulären. de fylls i intelligent utifrån filtypen och du kan konfigurera data uppsättningen ytterligare innan du skapar den här typen av formulär. 
1. Klicka på **Nästa** för att granska formuläret **Bekräfta Detaljer** . Kontrol lera dina val och skapa en valfri data profil för din data uppsättning. Läs mer om [data profilering](#profile).
1. Välj **skapa** för att slutföra skapandet av data uppsättningen.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Data profil och för hands version

När du har skapat din data uppsättning kontrollerar du att du kan visa profilen och för hands versionen i Studio med följande steg. 

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/)
1. Välj **data uppsättningar** i avsnittet **till gångar** i det vänstra fönstret.
1. Välj namnet på data uppsättningen som du vill visa. 
1. Välj fliken **utforska** . 
1. Välj fliken för **hands version** eller **profil** . 

![Visa data uppsättnings profil och för hands version](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

Du kan få en mängd olika sammanfattnings statistik över din data uppsättning för att kontrol lera om din data uppsättning är ML-redo. För icke-numeriska kolumner innehåller de bara grundläggande statistik som min, max och antal fel. För numeriska kolumner kan du också granska deras statistiska moment och uppskatta quantiles. 

Mer specifikt innehåller Azure Machine Learning data uppsättningens data profil:

>[!NOTE]
> Tomma poster visas för funktioner med irrelevanta typer.

|Statistik|Beskrivning
|------|------
|Funktion| Namn på den kolumn som sammanfattas.
|Profil| Infogad visualisering baserat på den härledda typen. Strängar, booleska värden och datum har till exempel värde antal, medan decimaler (numeriska värden) har ungefärligt histogram. På så sätt får du en snabb förståelse för data fördelningen.
|Typ distribution| Antal värden i en kolumn. Nullvärden är deras egna typ, så den här visualiseringen är användbar för att identifiera udda eller saknade värden.
|Typ|Den härledda typen för kolumnen. Möjliga värden är: strängar, booleska värden, datum och decimaler.
|Min| Minsta värde för kolumnen. Tomma poster visas för funktioner vars typ inte har en inbyggd ordning (som booleska värden).
|Max| Max värde för kolumnen. 
|Antal| Totalt antal saknade och icke-saknade poster i kolumnen.
|Antal saknas inte| Antal poster i kolumnen som inte saknas. Tomma strängar och fel behandlas som värden, så de kommer inte att bidra till det antal som saknas.
|Kvantiler| Ungefärligt värde vid varje quantile för att ge en uppfattning om data fördelningen.
|Medelvärde| Aritmetiskt medelvärde eller genomsnitt för kolumnen.
|Standardavvikelse| Mått på mängden spridning eller variation för den här kolumnens data.
|Varians| Mått på hur långt spridning av den här kolumnens data är från det genomsnittliga värdet. 
|Snedhet| Mått på hur olika data från den här kolumnen kommer från en normal distribution.
|Toppighet| Mått på hur mycket data som har staplats i den här kolumnens data jämförs med en normal distribution.

## <a name="storage-access-and-permissions"></a>Lagrings åtkomst och behörigheter

För att säkerställa att du ansluter till Azure Storage-tjänsten på ett säkert sätt, kräver Azure Machine Learning att du har behörighet att komma åt motsvarande data lagring. Den här åtkomsten beror på de autentiseringsreferenser som används för att registrera data lagret.

### <a name="virtual-network"></a>Virtuellt nätverk

Om ditt data lagrings konto finns i ett **virtuellt nätverk** krävs ytterligare konfigurations steg för att säkerställa att Azure Machine Learning har åtkomst till dina data. Se [nätverks isolering & sekretess](how-to-enable-studio-virtual-network.md) för att se till att lämpliga konfigurations steg tillämpas när du skapar och registrerar ditt data lager.  

### <a name="access-validation"></a>Åtkomst verifiering

**Som en del av den första processen för skapande och registrering av data lagret** validerar Azure Machine Learning automatiskt att den underliggande lagrings tjänsten finns och den användare som angavs huvud namn, tjänstens huvud namn eller SAS-token har åtkomst till den angivna lagrings platsen.

**När data lagret har skapats** utförs den här verifieringen bara för metoder som kräver åtkomst till den underliggande lagrings behållaren, **inte** varje gång som data lager objekt hämtas. Verifieringen sker till exempel om du vill hämta filer från ditt data lager. men om du bara vill ändra ditt standard data lager sker inte verifieringen.

Om du vill autentisera din åtkomst till den underliggande lagrings tjänsten kan du ange antingen din konto nyckel, token för signaturer för delad åtkomst (SAS) eller tjänstens huvud namn enligt den data lager typ som du vill skapa. I [matrisen lagrings typ](how-to-access-data.md#matrix) visas de autentiseringstyper som stöds och som motsvarar varje data lager typ.

Du hittar konto nyckel, SAS-token och information om tjänstens huvud namn på din [Azure Portal](https://portal.azure.com).

* Om du planerar att använda en konto nyckel eller SAS-token för autentisering väljer du **lagrings konton** i den vänstra rutan och väljer det lagrings konto som du vill registrera.
  * **Översikts** sidan innehåller information som konto namn, behållare och fil resurs namn.
      1. För konto nycklar går du till **åtkomst nycklar** i fönstret **Inställningar** .
      1. För SAS-token, gå till **signaturer för delad åtkomst** i fönstret **Inställningar** .

* Om du planerar att använda ett [huvud namn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md) för autentisering går du till din **Appregistreringar** och väljer vilken app du vill använda.
    * Dess motsvarande **översikts** sida innehåller nödvändig information, t. ex. klient-ID och klient-ID.

> [!IMPORTANT]
> * Om du behöver ändra åtkomst nycklarna för ett Azure Storage konto (konto nyckel eller SAS-token) måste du synkronisera de nya autentiseringsuppgifterna med din arbets yta och de data lager som är anslutna till den. Lär dig hur du [synkroniserar dina uppdaterade autentiseringsuppgifter](how-to-change-storage-access-key.md). <br> <br>
> * Om du avregistrerar och omregistrerar ett data lager med samma namn, och det Miss lyckas, kan det hända att Azure Key Vault för din arbets yta inte har mjuk borttagning aktive rad. Som standard aktive ras mjuk borttagning för Key Vault-instansen som skapats av din arbets yta, men den är kanske inte aktive rad om du använde ett befintligt nyckel valv eller har en arbets yta som skapats före oktober 2020. Information om hur du aktiverar mjuk borttagning finns i [Aktivera mjuk borttagning för ett befintligt nyckel valv]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault). "

### <a name="permissions"></a>Behörigheter

För Azure Blob-behållare och Azure Data Lake gen 2-lagring, se till att dina autentiseringsuppgifter har åtkomst till **lagrings-BLOB-dataläsaren** . Läs mer om [Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Ett konto SAS-token som standard saknar behörighet. 
* För **Läs behörighet** för data måste autentiseringsuppgifterna för autentisering ha minst list-och Läs behörighet för behållare och objekt. 

* För data **Skriv åtkomst** krävs även Skriv-och tilläggs behörigheter.

## <a name="train-with-datasets"></a>Träna med datauppsättningar

Använd dina data uppsättningar i Machine Learning-experiment för att träna ML-modeller. [Lär dig mer om hur du tränar med data uppsättningar](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Nästa steg

* [Ett steg-för-steg-exempel på utbildning med TabularDatasets och automatisk maskin inlärning](tutorial-first-experiment-automated-ml.md).

* [Träna en modell](how-to-set-up-training-targets.md).

* Fler exempel på data uppsättnings utbildning finns i [exempel antecknings böckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).