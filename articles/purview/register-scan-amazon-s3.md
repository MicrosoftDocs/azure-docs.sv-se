---
title: Så här genomsöker du Amazon S3-bucketar
description: Den här guiden beskriver information om hur du genomsöker Amazon S3-bucketar.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/21/2021
ms.custom: references_regions
ms.openlocfilehash: 75a7cba1e47509e3186ab519d0d8ca82dd315373
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815530"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Azure Purview-anslutningsapp för Amazon S3

Den här guiden innehåller en förklaring av hur du använder Azure Purview för att genomsöka ostrukturerade data som för närvarande lagras i Amazon S3-standard buckets och identifiera vilka typer av känslig information som finns i dina data. Den här guiden beskriver också hur du identifierar Amazon S3-bucketar där data för närvarande lagras för att enkelt skydda information och dataefterlevnad.

För den här tjänsten använder du Purview för att ge en Microsoft-konto med säker åtkomst till AWS, där Purview-skannern körs. Purview-skannern använder den här åtkomsten till dina Amazon S3-bucketar för att läsa dina data och rapporterar sedan genomsökningsresultaten, inklusive endast metadata och klassificering, tillbaka till Azure. Använd Purview-klassificerings- och märkningsrapporter för att analysera och granska dina datagenomsökningsresultat.

I den här guiden får du lära dig hur du lägger till Amazon S3-bucketar som Purview-resurser och skapar en genomsökning efter dina Amazon S3-data.

## <a name="purview-scope-for-amazon-s3"></a>Vyomfång för Amazon S3

Följande omfång är specifikt för registrering och genomsökning av Amazon S3-bucketar som Purview-datakällor.

|Omfång  |Beskrivning  |
|---------|---------|
|**Databegränsningar**     |    Purview-skannertjänsten stöder för närvarande genomsökning av Amazon S3-bucketar för upp till 100 GB data per klientorganisation.     |
|**Filtyper**     | Purview-skannertjänsten stöder för närvarande följande filtyper: <br><br>.avro, .csv, .doc, .docm, .docx, .dot, .json, .odp, .ods, .odt, .orc, .parquet, .pdf, .pot, .pps, .ppsx, .ppt, .pptm, .pptx, .psv, .ssv, .tsv, .txt, .xlc, .xls, .xlsb, .xlsm, .xlsx, .xlsx, .xlt, .xml        |
|**Regioner**     | Purview-anslutningsappen för Amazon S3-tjänsten distribueras för närvarande endast i **regionerna AWS USA, östra (Ohio)** **och Europa (Ohio).** <br><br>Mer information finns i [Lagrings- och genomsökningsregioner.](#storage-and-scanning-regions)   |
|     |         |

Mer information finns i de dokumenterade Purview-gränserna på:

- [Hantera och öka kvoter för resurser med Azure Purview](how-to-manage-quotas.md)
- [Datakällor och filtyper som stöds i Azure Purview](sources-and-scans.md)
- [Använda privata slutpunkter för ditt Purview-konto](catalog-private-link.md)
### <a name="storage-and-scanning-regions"></a>Lagrings- och genomsökningsregioner

I följande tabell mappas de regioner där dina data lagras till den region där de skulle genomsökas av Azure Purview.

> [!IMPORTANT]
> Kunder debiteras för alla relaterade dataöverföringsavgifter enligt bucketens region.
>

| Lagringsregion | Genomsökningsregion |
| ------------------------------- | ------------------------------------- |
| USA, östra (Ohio)                  | USA, östra (Ohio)                        |
| USA, östra (N. Virginia)           | USA, östra (N. Virginia)                       |
| USA, västra (N. Kalifornien)         | USA, östra (Ohio)                        |
| USA, västra (Oregon)                | USA, östra (Ohio)                        |
| Afrika (Kapstad)              | Europa (Tyskland)                    |
| Asien och stillahavsområdet (Hongkong)        | Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Mumbai)           | Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Osaka-Local)      | Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Sydkorea)            | Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Singapore)        | Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Sydney)           | Asien och stillahavsområdet (Sydney)                  |
| Asien och stillahavsområdet (Tokyo)            | Asien och stillahavsområdet (Sydney)                 |
| Kanada (centrala)                | USA, östra (Ohio)                        |
| Kina (Beijing)                 | Stöds inte                    |
| Kina (Ningxia)                 | Stöds inte                   |
| Europa (Tyskland)              | Europa (Tyskland)                    |
| Europa (Irland)                | Europa (Irland)                   |
| Europa (London)                 | Europa (Irland)                   |
| Europa (Italien)                  | Europa (Tyskland)                    |
| Europa (Paris)                  | Europa (Tyskland)                    |
| Europa (Stockholm)              | Europa (Tyskland)                    |
| Mellanöstern (Saudiarabien)           | Europa (Tyskland)                    |
| Sydamerika (Süo Paulo)       | USA, östra (Ohio)                        |
| | |

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har utfört följande krav innan du lägger till dina Amazon S3-buckets som Purview-datakällor och genomsöker dina S3-data.

> [!div class="checklist"]
> * Du måste vara administratör för Datakälla i Azure Purview.
> * [Skapa ett Purview-konto](#create-a-purview-account) om du inte redan har ett
> * [Skapa purview-autentiseringsuppgifter för AWS-bucketgenomsökningen](#create-a-purview-credential-for-your-aws-bucket-scan)
> * [Skapa en ny AWS-roll för användning med Purview](#create-a-new-aws-role-for-purview)
> * [Konfigurera genomsökning efter krypterade Amazon S3-bucketar](#configure-scanning-for-encrypted-amazon-s3-buckets), om det är relevant
> * När du lägger till dina buckets som Purview-resurser behöver du värdena för [din AWS ARN,](#retrieve-your-new-role-arn) [bucketnamn](#retrieve-your-amazon-s3-bucket-name)och ibland [ditt AWS-konto-ID](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Skapa ett Purview-konto

- **Om du redan har ett Purview-konto kan** du fortsätta med de konfigurationer som krävs för AWS S3-stöd. Börja med [Skapa en Purview-autentiseringsbehörighet för AWS-bucketgenomsökningen.](#create-a-purview-credential-for-your-aws-bucket-scan)

- **Om du behöver skapa ett Purview-konto följer du** anvisningarna i [Skapa en Azure Purview-kontoinstans.](create-catalog-portal.md) När du har skapat ditt konto går du tillbaka hit för att slutföra konfigurationen och börja använda Purview-anslutningsappen för Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Skapa purview-autentiseringsuppgifter för AWS-bucketgenomsökningen

Den här proceduren beskriver hur du skapar en ny Purview-autentiseringsbehörighet som ska användas vid genomsökning av AWS-bucketar.

> [!TIP]
> Du kan också skapa nya autentiseringsuppgifter mitt i processen när du konfigurerar [genomsökningen.](#create-a-scan-for-one-or-more-amazon-s3-buckets) I så fall väljer du **Ny** i fältet **Autentiseringsuppgifter.**
>

1. I Purview navigerar du till **Hanteringscenter** och under **Säkerhet och åtkomst** väljer du **Autentiseringsuppgifter.**

1. Välj **Ny.** I fönstret **Nya autentiseringsuppgifter** som visas till höger använder du följande fält för att skapa dina Purview-autentiseringsuppgifter:

    |Fält |Beskrivning  |
    |---------|---------|
    |**Namn**     |Ange ett beskrivande namn för den här autentiseringsplatsen eller använd standardnamnet.        |
    |**Beskrivning**     |Ange en valfri beskrivning för den här autentiseringsbeskrivningen, till exempel `Used to scan the tutorial S3 buckets`         |
    |**Autentiseringsmetod**     |Välj **Roll-ARN** eftersom du använder en roll-ARN för att få åtkomst till din bucket.         |
    |**Microsoft-konto-ID**     |Klicka för att kopiera det här värdet till Urklipp. Använd det här värdet som **Microsoft-konto-ID** när [du skapar din roll-ARN i AWS](#create-a-new-aws-role-for-purview).           |
    |**Externt ID**     |Klicka för att kopiera det här värdet till Urklipp. Använd det här värdet som externt **ID när** du [skapar roll-ARN i AWS.](#create-a-new-aws-role-for-purview)        |
    |**Roll-ARN**     | När du har skapat [din Amazon IAM-roll](#create-a-new-aws-role-for-purview)navigerar du till din roll i IAM-området, kopierar värdet **för Roll-ARN** och anger det här. Exempel: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Mer information finns i Hämta [din nya roll-ARN.](#retrieve-your-new-role-arn) |
    | | |

    Välj **Skapa** när du är klar för att slutföra skapandet av autentiseringssuppgifter.

1. Om du inte har gjort det än kopierar du och klistrar in **värdena för Microsoft-konto-ID** och externt **ID** för användning när du skapar en ny [AWS-roll](#create-a-new-aws-role-for-purview)för Purview , vilket är nästa steg.

Mer information om Purview-autentiseringsuppgifter finns i [Autentiseringsuppgifter för källautentisering i Azure Purview.](manage-credentials.md)

### <a name="create-a-new-aws-role-for-purview"></a>Skapa en ny AWS-roll för Purview

Den här proceduren kräver att du anger värdena för ditt Azure-konto-ID och externa ID när du skapar din AWS-roll.

Om du inte har dessa värden letar du upp dem först i [Purview-autentiseringsbehörigheten](#create-a-purview-credential-for-your-aws-bucket-scan).

**Så här hittar du ditt Microsoft-konto-ID och externa ID:**

1. I Purview går du till Security and access Credentials (Säkerhets-  >  **och åtkomstautentiseringsuppgifter för** Management  >  Center).

1. Välj de autentiseringsuppgifter som du [skapade för AWS-bucketgenomsökningen](#create-a-purview-credential-for-your-aws-bucket-scan)och välj sedan Redigera i **verktygsfältet.**

1. I fönstret **Redigera autentiseringsuppgifter** som visas till höger kopierar du **värdena för Microsoft-konto-ID** och externt **ID** till en separat fil eller har dem till hands för att klistra in dem i relevant fält i AWS.

    Exempel:

    [![Leta upp värdena Microsoft-konto ID och externt ID. ](./media/register-scan-amazon-s3/locate-account-id-external-id.png)](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Så här skapar du din AWS-roll för Purview:**

1.  Öppna konsolen **Amazon Web Services** och välj **IAM** under **Säkerhet,** Identitet och Efterlevnad.

1. Välj **Roller** och sedan **Skapa roll**.

1. Välj **Ett annat AWS-konto** och ange sedan följande värden:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Konto-ID**     |    Ange ditt Microsoft-konto-ID. Exempelvis: `615019938638`     |
    |**Externt ID**     |   Under alternativ väljer du **Kräv externt ID...** och anger sedan ditt externa ID i det avsedda fältet. <br>Exempelvis: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Exempel:

    ![Lägg till Microsoft-konto-ID:t till ditt AWS-konto.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. I området **Skapa roll > Koppla behörighetsprinciper** filtrerar du de behörigheter som visas på **S3.** Välj **AmazonS3ReadOnlyAccess** och välj sedan **Nästa: Taggar**.

    ![Välj ReadOnlyAccess-principen för den nya Amazon S3-genomsökningsrollen.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > **AmazonS3ReadOnlyAccess-principen** ger minsta möjliga behörigheter som krävs för genomsökning av dina S3-buckets och kan även innehålla andra behörigheter.
    >
    >Om du bara vill använda de minsta behörigheter som krävs för att genomsöka dina bucketar skapar du en ny princip med de behörigheter som anges i Minsta behörigheter för [din AWS-princip,](#minimum-permissions-for-your-aws-policy)beroende på om du vill genomsöka en enskild bucket eller alla bucketar i ditt konto. 
    >
    >Tillämpa den nya principen på rollen i stället för **AmazonS3ReadOnlyAccess.**

1. I området **Lägg till taggar (valfritt)** kan du välja att skapa en meningsfull tagg för den nya rollen. Med användbara taggar kan du organisera, spåra och kontrollera åtkomsten för varje roll som du skapar.

    Ange en ny nyckel och ett värde för taggen efter behov. När du är klar, eller om du vill hoppa över det här steget, väljer du **Nästa: Granska** för att granska rollinformationen och slutföra rollskapandet.

    ![Lägg till en meningsfull tagg för att organisera, spåra eller styra åtkomsten för den nya rollen.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. I **området** Granska gör du följande:

    - I fältet **Rollnamn** anger du ett beskrivande namn för din roll
    - I rutan **Rollbeskrivning** anger du en valfri beskrivning för att identifiera syftet med rollen
    - I avsnittet **Principer** bekräftar du att rätt princip (**AmazonS3ReadOnlyAccess**) är kopplad till rollen.

    Välj sedan **Skapa roll** för att slutföra processen.

    Exempel:

    ![Granska informationen innan du skapar din roll.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Konfigurera genomsökning efter krypterade Amazon S3-bucketar

AWS-buckets stöder flera krypteringstyper. För bucketar som använder **AWS-KMS-kryptering** krävs särskild konfiguration för att aktivera genomsökning.

> [!NOTE]
> För buckets som inte använder någon kryptering, AES-256- eller AWS-KMS S3-kryptering, hoppar du över det här avsnittet och fortsätter till Hämta ditt [Amazon S3-bucketnamn](#retrieve-your-amazon-s3-bucket-name).
>

**Så här kontrollerar du vilken typ av kryptering som används i dina Amazon S3-bucketar:**

1. I AWS går du **till Storage**  >  **S3** > och **väljer Bucketar** på menyn till vänster.

    ![Välj fliken Amazon S3 Buckets (Amazon S3-bucketar).](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Välj den bucket som du vill kontrollera. På bucketens informationssida väljer du fliken **Egenskaper** och rullar ned till **området Standardkryptering.**

    - Om bucketen som du har valt är konfigurerad för allt annat än **AWS-KMS-kryptering,** inklusive om standardkryptering för din bucket är **Inaktiverad,** hoppar du över resten av den här proceduren och fortsätter med Hämta ditt Amazon [S3-bucketnamn](#retrieve-your-amazon-s3-bucket-name).

    - Om bucketen du valde är konfigurerad för **AWS-KMS-kryptering** fortsätter du enligt beskrivningen nedan för att lägga till en ny princip som tillåter genomsökning av en bucket med anpassad **AWS-KMS-kryptering.**

    Exempel:

    ![Visa en Amazon S3-bucket som konfigurerats med AWS-KMS-kryptering](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Så här lägger du till en ny princip som tillåter genomsökning av en bucket med anpassad AWS-KMS-kryptering:**

1. I AWS går du till  >   **Tjänster IAM-principer** och väljer Skapa  >    **princip.**

1. Definiera principen **med**  >  **följande värden** på fliken Skapa visuellt principredigeringsprogram:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Tjänst**     |  Ange och välj **KMS**.       |
    |**Åtgärder**     | Under **Åtkomstnivå** väljer du **Skriv** för att expandera **avsnittet** Skriva.<br>När den har expanderats väljer du bara **alternativet Dekryptera.**        |
    |**Resurser**     |Välj en specifik resurs eller **Alla resurser.**         |
    | | |

    När du är klar väljer du Granska **princip för** att fortsätta.

    ![Skapa en princip för att skanna en bucket med AWS-KMS-kryptering.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. På sidan **Granska princip** anger du ett beskrivande namn för principen och en valfri beskrivning och väljer sedan **Skapa princip.**

    Den nyligen skapade principen läggs till i listan över principer.

1. Koppla den nya principen till den roll som du har lagt till för genomsökning.

    1. Gå tillbaka till sidan **IAM-roller**  >   och välj den roll som du lade till [tidigare.](#create-a-new-aws-role-for-purview)

    1. På fliken **Behörigheter** väljer du **Koppla principer.**

        ![På fliken Behörigheter för din roll väljer du Koppla principer.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. På sidan **Bifoga behörigheter** söker du efter och väljer den nya princip som du skapade ovan. Välj **Koppla princip** för att koppla principen till rollen.

        Sidan **Sammanfattning** uppdateras med den nya principen kopplad till din roll.

        ![Visa en uppdaterad sammanfattningssida med den nya principen kopplad till din roll.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Hämta din nya roll-ARN

Du måste registrera din AWS-roll-ARN och kopiera den till Purview när du skapar en genomsökning [för Amazon S3-bucketen](#create-a-scan-for-one-or-more-amazon-s3-buckets).

**Så här hämtar du roll-ARN:**

1. I området AWS-roller för identitets- och **åtkomsthantering (IAM)** söker du efter och väljer den nya rollen  >   som du skapade [för Purview](#create-a-purview-credential-for-your-aws-bucket-scan).

1. På rollens **sammanfattningssida** väljer du knappen **Kopiera till Urklipp** till höger om värdet **för Roll-ARN.**

    ![Kopiera roll-ARN-värdet till Urklipp.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Klistra in det här värdet på en säker plats som är redo att användas när [du skapar en genomsökning för Amazon S3-bucketen](#create-a-scan-for-one-or-more-amazon-s3-buckets).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Hämta ditt Amazon S3-bucketnamn

Du behöver namnet på din Amazon S3-bucket för att kopiera den till Purview när du skapar en [genomsökning för din Amazon S3-bucket](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**Så här hämtar du bucketnamnet:**

1. I AWS går du **till Storage**  >  **S3** > och **väljer Bucketar** på menyn till vänster.

    ![Visa fliken Amazon S3-bucketar.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Sök efter och välj din bucket för att visa bucketinformationssidan och kopiera sedan bucketnamnet till Urklipp.

    Exempel:

    ![Hämta och kopiera S3-bucketens URL.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Klistra in bucketnamnet i en säker fil och lägg till ett prefix för att skapa det värde som du måste ange när du konfigurerar bucketen som `s3://` en Purview-resurs.

    Exempelvis: `s3://purview-tutorial-bucket`

> [!NOTE]
> Endast rotnivån för din bucket stöds som en Purview-datakälla. Följande URL, som innehåller en undermapp, *stöds till* exempel inte: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Leta upp ditt AWS-konto-ID

Du behöver ditt AWS-konto-ID för att registrera ditt AWS-konto som en Purview-datakälla, tillsammans med alla dess buckets.

Ditt AWS-konto-ID är det ID som du använder för att logga in på AWS-konsolen. Du kan också hitta den när du är inloggad på IAM-instrumentpanelen, till vänster under navigeringsalternativen och längst upp som den numeriska delen av inloggnings-URL:en:

Exempel:

![Hämta ditt AWS-konto-ID.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Lägga till en enskild Amazon S3-bucket som en Purview-resurs

Använd den här proceduren om du bara har en S3-bucket som du vill registrera till Purview som en datakälla, eller om du har flera buckets i ditt AWS-konto, men inte vill registrera alla till Purview.

**Så här lägger du till din bucket:** 

1. Starta Purview-portalen med den dedikerade Purview-anslutningsappen för Amazon S3-URL. Den här URL:en har angetts av produkthanteringsteamet för Amazon S3 Purview Connector.

    ![Starta Purview-portalen.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Gå till sidan Azure Purview Sources (Azure Purview **Sources)** och välj **registrera** ![ registrera-ikonen.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Fortsätt**.

    ![Lägg till en Amazon AWS-bucket som en Purview-datakälla.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Om du har [flera samlingar](manage-data-sources.md#manage-collections) och vill lägga till din  Amazon S3 i en specifik samling väljer du kartvyn längst upp till höger och väljer sedan **registrera** ![ registrera-ikonen.](./media/register-scan-amazon-s3/register-button.png) i samlingen.
    >

1. I fönstret **Registrera källor (Amazon S3)** som öppnas anger du följande information:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Namn**     |Ange ett beskrivande namn eller använd det angivna standardnamnet.         |
    |**Bucket-URL**     | Ange din AWS-bucket-URL med följande syntax:   `s3://<bucketName>`     <br><br>**Obs!** Se till att endast använda rotnivån för din bucket, utan några undermappar. Mer information finns i Hämta [ditt Amazon S3-bucketnamn.](#retrieve-your-amazon-s3-bucket-name) |
    |**Välj en samling** |Om du valde att registrera en datakälla från en samling, har den samlingen redan listats. <br><br>Välj en annan samling efter behov, **Ingen för** att inte tilldela någon samling eller **Ny för** att skapa en ny samling nu. <br><br>Mer information om Purview-samlingar finns i [Hantera datakällor i Azure Purview.](manage-data-sources.md#manage-collections)|
    | | |

    När du är klar väljer du **Slutför för** att slutföra registreringen.

Fortsätt med [Skapa en sökning efter en eller flera Amazon S3-bucketar.](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Lägga till ett Amazon-konto som en Purview-resurs

Använd den här proceduren om du har flera S3-buckets i ditt Amazon-konto och du vill registrera alla som Purview-datakällor.

När [du konfigurerar](#create-a-scan-for-one-or-more-amazon-s3-buckets)genomsökningen kan du välja de specifika bucketar som du vill genomsöka, om du inte vill genomsöka alla tillsammans.

**Så här lägger du till ditt Amazon-konto:**
1. Starta Purview-portalen med den dedikerade Purview-anslutningsappen för Amazon S3-URL. Den här URL:en har angetts av produkthanteringsteamet för Amazon S3 Purview Connector.

    ![Starta anslutningsappen för den dedikerade Purview-portalen för Amazon S3](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Gå till sidan Azure Purview Sources (Azure Purview **Sources)** och **välj registrera** ![ registrera-ikonen.](./media/register-scan-amazon-s3/register-button.png) > **Amazon-konton**  >  **Fortsätt**.

    ![Lägg till ett Amazon-konto som en Purview-datakälla.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Om du har [flera samlingar](manage-data-sources.md#manage-collections) och vill lägga till din  Amazon S3 i en specifik samling väljer du kartvyn längst upp till höger och väljer sedan **registrera** ![ registrera-ikonen.](./media/register-scan-amazon-s3/register-button.png) i samlingen.
    >

1. I fönstret **Registrera källor (Amazon S3)** som öppnas anger du följande information:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Namn**     |Ange ett beskrivande namn eller använd det angivna standardnamnet.         |
    |**AWS-konto-ID**     | Ange ditt AWS-konto-ID. Mer information finns i Hitta [ditt AWS-konto-ID](#locate-your-aws-account-id)|
    |**Välj en samling** |Om du valde att registrera en datakälla från en samling, har den samlingen redan listats. <br><br>Välj en annan samling efter behov, **Ingen för** att inte tilldela någon samling eller **Ny för** att skapa en ny samling nu. <br><br>Mer information om Purview-samlingar finns i [Hantera datakällor i Azure Purview.](manage-data-sources.md#manage-collections)|
    | | |

    När du är klar slutför du **registreringen genom** att välja Slutför.

Fortsätt med [Skapa en genomsökning efter en eller flera Amazon S3-bucketar.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Skapa en sökning efter en eller flera Amazon S3-bucketar

När du har lagt till dina bucketar som Purview-datakällor kan du konfigurera en genomsökning så att den körs med schemalagda intervall eller omedelbart.

1. Gå till området Azure Purview **Sources** och gör sedan något av följande:

    - I **kartvyn väljer du** Ny **genomsökning ikonen** Ny ![ genomsökning.](./media/register-scan-amazon-s3/new-scan-button.png) i rutan datakälla.
    - Hovra **över raden för** datakällan i listvyn och välj Ny **genomsökning Ikonen** Ny ![ genomsökning. ](./media/register-scan-amazon-s3/new-scan-button.png) .

1. I fönstret **Skanna...** som öppnas till höger definierar du följande fält och väljer sedan **Fortsätt:**

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Namn**     |  Ange ett beskrivande namn för genomsökningen eller använd standardnamnet.       |
    |**Typ** |Visas endast om du har lagt till ditt AWS-konto, med alla buckets inkluderade. <br><br>Aktuella alternativ omfattar endast **Alla**  >  **Amazon S3.** Håll ögonen öppna för fler alternativ att välja eftersom Purviews supportmatris utökas. |
    |**Autentiseringsuppgift**     |  Välj en Purview-autentiseringsbehörighet med din roll-ARN. <br><br>**Tips:** Om du vill skapa nya autentiseringsuppgifter just nu väljer du **Ny**. Mer information finns i Create [a Purview credential for your AWS bucket scan](#create-a-purview-credential-for-your-aws-bucket-scan).     |
    | **Amazon S3**    |   Visas bara om du har lagt till ditt AWS-konto, med alla buckets inkluderade. <br><br>Välj en eller flera buckets att genomsöka, eller **Markera alla** för att söka igenom alla buckets i ditt konto.      |
    | | |

    Purview kontrollerar automatiskt att roll-ARN är giltig och att bucketarna och objekten i bucketarna är tillgängliga och fortsätter sedan om anslutningen lyckas.

    > [!TIP]
    > Om du vill ange olika värden och testa anslutningen själv innan du fortsätter väljer du **Testa anslutning** längst ned till höger innan du väljer **Fortsätt.**
    >

1. I fönstret **Välj en uppsättning av skanningsregel** väljer du antingen standardregeluppsättningen **AmazonS3** eller väljer **Ny genomsökningsregeluppsättning** för att skapa en ny anpassad regeluppsättning. När du har valt din regeluppsättning väljer du **Fortsätt.**

    Om du väljer att skapa en ny regeluppsättning för anpassad genomsökning använder du guiden för att definiera följande inställningar:

    |Fönster  |Description  |
    |---------|---------|
    |**Ny genomsökningsregeluppsättning** /<br>**Beskrivning av genomsökningsregel**    |   Ange ett beskrivande namn och en valfri beskrivning för regeluppsättningen      |
    |**Välj filtyper**     | Markera alla filtyper som du vill inkludera i genomsökningen och välj sedan **Fortsätt.**<br><br>Om du vill lägga till en ny filtyp **väljer du Ny filtyp** och definierar följande: <br>– Filnamnstillägget som du vill lägga till <br>– En valfri beskrivning  <br>– Om filinnehållet har en anpassad avgränsare eller är en systemfiltyp. Ange sedan din anpassade avgränsare eller välj typ av systemfil. <br><br>Välj **Skapa** för att skapa din anpassade filtyp.     |
    |**Välj klassificeringsregler**     |   Gå till och välj de klassificeringsregler som du vill köra på datauppsättningen.      |
    |     |         |

    Välj **Skapa** när du är klar för att skapa regeluppsättningen.

1. I fönstret **Ange en sökningsutlösare** väljer du något av följande och väljer sedan **Fortsätt:**

    - **Återkommande** för att konfigurera ett schema för en återkommande genomsökning
    - **En** gång för att konfigurera en genomsökning som startar omedelbart

1. I fönstret Granska **genomsökningen** kontrollerar du genomsökningsinformationen för att  bekräfta  att de är korrekta. Välj sedan Spara eller Spara och kör om du har valt **En** gång i föregående fönster.

    > [!NOTE]
    > När du har startat genomsökningen kan det ta upp till 24 timmar att slutföra genomsökningen. Du kommer att kunna granska dina Insiktsrapporter **och söka** i katalogen 24 timmar efter att du har startat varje genomsökning.
    >

Mer information finns i Utforska [Purview-genomsökningsresultat.](#explore-purview-scanning-results)

## <a name="explore-purview-scanning-results"></a>Utforska Purview-genomsökningsresultat

När en Purview-genomsökning är klar på dina Amazon S3-bucketar går du nedåt i området Purview **Sources (Rensa** källor) för att visa genomsökningshistoriken.

Välj en datakälla för att visa information om den och välj sedan fliken **Genomsökningar** för att visa alla genomsökningar som körs eller har slutförts.
Om du har lagt till ett AWS-konto med flera bucketar visas genomsökningshistoriken för varje bucket under kontot.

Exempel:

![Visa AWS S3-bucketgenomsökningar under AWS-kontokällan.](./media/register-scan-amazon-s3/account-scan-history.png)

Använd de andra områdena i Purview för att få information om innehållet i din dataegenskap, inklusive dina Amazon S3-bucketar:

- **Sök i datakatalogen Purview och** filtrera efter en specifik bucket. Exempel:

    ![Sök i katalogen efter AWS S3-tillgångar.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Visa Insiktsrapporter** för att visa statistik för klassificering, känslighetsetiketter, filtyper och mer information om ditt innehåll.

    Alla Purview Insight-rapporter innehåller Amazon S3-genomsökningsresultaten, tillsammans med resten av resultaten från dina Azure-datakällor. När det var relevant lades ytterligare **en Amazon S3-tillgångstyp** till i rapportfiltreringsalternativen.

    Mer information finns i [Understand Insights i Azure Purview](concept-insights.md).

## <a name="minimum-permissions-for-your-aws-policy"></a>Lägsta behörighet för din AWS-princip

Standardproceduren för [att skapa en AWS-roll](#create-a-new-aws-role-for-purview) för Purview som ska användas vid genomsökning av dina S3-buckets använder **AmazonS3ReadOnlyAccess-principen.**

**AmazonS3ReadOnlyAccess-principen** ger minsta möjliga behörigheter som krävs för genomsökning av dina S3-buckets och kan även innehålla andra behörigheter.

Om du bara vill använda de minsta behörigheter som krävs för att söka igenom dina buckets skapar du en ny princip med de behörigheter som anges i följande avsnitt, beroende på om du vill genomsöka en enskild bucket eller alla bucketar i ditt konto.

Tillämpa den nya principen på rollen i stället för **AmazonS3ReadOnlyAccess.**

### <a name="individual-buckets"></a>Enskilda buckets

Vid genomsökning av enskilda S3-bucketar inkluderar minsta AWS-behörigheter:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Se till att definiera din resurs med det specifika bucketnamnet. Exempel:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>Alla buckets i ditt konto

När du genomsöker alla bucketar i ditt AWS-konto omfattar minsta AWS-behörigheter:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Se till att definiera resursen med ett jokertecken. Exempel:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Purview Insight-rapporter:

> [!div class="nextstepaction"]
> [Förstå insikter i Azure Purview](concept-insights.md)
