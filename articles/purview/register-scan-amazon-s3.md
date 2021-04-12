---
title: Så här skannar du Amazon S3-buckets
description: Den här instruktions guiden beskriver hur du skannar Amazon S3-buckets.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: 542b6580994a2054526f0ddbb3ad93dc27c28fcc
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107660"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Azure avdelningens kontroll-anslutning för Amazon S3

Den här instruktions guiden innehåller en förklaring av hur du använder Azure avdelningens kontroll för att söka igenom ostrukturerade data som för närvarande lagras i Amazon S3 standard-buckets och identifierar vilka typer av känslig information som finns i dina data. Den här instruktions guiden beskriver också hur du identifierar Amazon S3-buckets där data för närvarande är lagrade för enkel informations skydd och data efterlevnad.

För den här tjänsten använder du avdelningens kontroll för att tillhandahålla en Microsoft-konto med säker åtkomst till AWS, där avdelningens kontroll-skannern kommer att köras. Avdelningens kontroll-skannern använder den här åtkomsten till dina Amazon S3-buckets för att läsa dina data och rapporterar sedan genomsöknings resultaten, inklusive endast metadata och klassificering, tillbaka till Azure. Använd avdelningens kontroll-klassificering och etikettera rapporter för att analysera och granska resultaten av data sökningen.

I den här instruktions guiden får du lära dig hur du lägger till Amazon S3-buckets som avdelningens kontroll-resurser och skapar en sökning efter dina Amazon S3-data.

## <a name="purview-scope-for-amazon-s3"></a>Avdelningens kontroll omfattning för Amazon S3

Följande omfång är särskilt för registrering och genomsökning av Amazon S3-buckets som avdelningens kontroll data källor.

|Omfång  |Beskrivning  |
|---------|---------|
|**Databegränsningar**     |    Tjänsten avdelningens kontroll Scanner stöder för närvarande genomsökning av Amazon S3-buckets för upp till 100 GB data per klient.     |
|**Filtyper**     | Tjänsten avdelningens kontroll Scanner stöder för närvarande följande filtyper: <br><br>. avro,. csv,. doc,. docm,. docx,. dot,. JSON,. ODP,. ods,. odt,. Orc,. Parquet,. pdf,. pot,. PPS,. ppsx,. ppt,. pptm,. pptx,. PSV,. SSV,. tsv,. txt,. xlc,. XML,. xlsb,. xlsm,. xlsx,. xls,. XML        |
|**Regioner**     | Avdelningens kontroll-anslutningen för Amazon S3-tjänsten är för närvarande endast distribuerad i **AWS-regionerna USA, östra (Ohio)** och **Europa (Frankfurt)** . <br><br>Mer information finns i [lagrings-och skannings regioner](#storage-and-scanning-regions).   |
|     |         |

Mer information finns i de dokumenterade avdelningens kontroll-gränserna på:

- [Hantera och öka kvoter för resurser med Azure avdelningens kontroll](how-to-manage-quotas.md)
- [Data källor och filtyper som stöds i Azure avdelningens kontroll](sources-and-scans.md)
- [Använd privata slut punkter för ditt avdelningens kontroll-konto](catalog-private-link.md)
### <a name="storage-and-scanning-regions"></a>Lagrings-och skannings regioner

I följande tabell mappas de regioner där du lagrar data i den region där det skulle genomsökas av Azure-avdelningens kontroll.

> [!IMPORTANT]
> Kunderna debiteras för alla relaterade avgifter för data överföring enligt regionen för deras Bucket.
>

| Lagrings region | Skannar region |
| ------------------------------- | ------------------------------------- |
| Östra USA (Ohio)                  | Östra USA (Ohio)                        |
| Östra USA (N. Virginia           | Östra USA (Ohio) eller östra USA (N. Virginia                       |
| Västra USA (N. Kalifornien         | Östra USA (Ohio)                        |
| USA, västra (Oregon)                | Östra USA (Ohio)                        |
| Afrika (kap stadens)              | Europa (Frankfurt)                    |
| Asien och stillahavsområdet (Hongkong)        | Europa (Frankfurt) eller Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Mumbai)           | Europa (Frankfurt) eller Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Osaka-Local)      | Europa (Frankfurt) eller Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Söul)            | Europa (Frankfurt) eller Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Singapore)        | Europa (Frankfurt) eller Asien och stillahavsområdet (Sydney)                   |
| Asien och stillahavsområdet (Sydney)           | Europa (Frankfurt) eller Asien och stillahavsområdet (Sydney)                  |
| Asien och stillahavsområdet (Tokyo)            | Europa (Frankfurt) eller Asien och stillahavsområdet (Sydney)                 |
| Kanada (Central)                | Östra USA (Ohio)                        |
| Kina (Beijing)                 | Stöds inte                    |
| Kina (NingXia)                 | Stöds inte                   |
| Europa (Frankfurt)              | Europa (Frankfurt)                    |
| Europa (Irland)                | Europa (Frankfurt) eller Europa (Irland)                   |
| Europa (London)                 | Europa (Frankfurt) eller Europa (Irland)                   |
| Europa (Milano)                  | Europa (Frankfurt)                    |
| Europa (Paris)                  | Europa (Frankfurt)                    |
| Europa (Stockholm)              | Europa (Frankfurt)                    |
| Mellanöstern (Bahrain)           | Europa (Frankfurt)                    |
| Sydamerika (São Paulo)       | Östra USA (Ohio)                        |
| | |

## <a name="prerequisites"></a>Förutsättningar

Se till att du har utfört följande förutsättningar innan du lägger till dina Amazon S3-buckets som avdelningens kontroll-datakällor och skannar dina S3-data.

> [!div class="checklist"]
> * Du måste vara en Azure avdelningens kontroll data source-administratör.
> * [Skapa ett avdelningens kontroll-konto](#create-a-purview-account) om du inte redan har ett
> * [Skapa en avdelningens kontroll-autentiseringsuppgift för din AWS-Bucket-genomsökning](#create-a-purview-credential-for-your-aws-bucket-scan)
> * [Skapa en ny AWS-roll som ska användas med avdelningens kontroll](#create-a-new-aws-role-for-purview)
> * [Konfigurera genomsökning för krypterade Amazon S3-buckets](#configure-scanning-for-encrypted-amazon-s3-buckets)om det är relevant
> * När du lägger till buckets som avdelningens kontroll-resurser behöver du värdena för ditt [AWS-ARN](#retrieve-your-new-role-arn), [Bucket-namn](#retrieve-your-amazon-s3-bucket-name)och ibland ditt [AWS-konto-ID](#locate-your-aws-account-id).

### <a name="create-a-purview-account"></a>Skapa ett avdelningens kontroll-konto

- **Om du redan har ett avdelningens kontroll-konto** kan du fortsätta med de konfigurationer som krävs för AWS S3-support. Börja med att [skapa en avdelningens kontroll-autentiseringsuppgift för din AWS-Bucket-genomsökning](#create-a-purview-credential-for-your-aws-bucket-scan).

- **Om du behöver skapa ett avdelningens kontroll-konto** följer du anvisningarna i [skapa en instans av Azure avdelningens kontroll-kontot](create-catalog-portal.md). När du har skapat ditt konto kan du gå tillbaka till Slutför konfigurationen och börja använda avdelningens kontroll Connector för Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Skapa en avdelningens kontroll-autentiseringsuppgift för din AWS-Bucket-genomsökning

Den här proceduren beskriver hur du skapar en ny avdelningens kontroll-autentiseringsuppgift som ska användas vid genomsökning av dina AWS-buckets.

> [!TIP]
> Du kan också skapa en ny autentiseringsuppgift i mitten av processen och [Konfigurera din genomsökning](#create-a-scan-for-one-or-more-amazon-s3-buckets). I så fall väljer du **ny** i fältet **autentiseringsuppgifter** .
>

1. I avdelningens kontroll navigerar du till **hanterings centret** och under **säkerhet och åtkomst** väljer du **autentiseringsuppgifter**.

1. Välj **ny** och i rutan **ny autentiseringsuppgift** som visas till höger, använder du följande fält för att skapa din avdelningens kontroll-autentiseringsuppgift:

    |Fält |Beskrivning  |
    |---------|---------|
    |**Namn**     |Ange ett beskrivande namn för den här autentiseringsuppgiften eller Använd standardvärdet.        |
    |**Beskrivning**     |Ange en valfri beskrivning för den här autentiseringsuppgiften, till exempel `Used to scan the tutorial S3 buckets`         |
    |**Autentiseringsmetod**     |Välj **roll ARN**, eftersom du använder en roll-ARN för att få åtkomst till din Bucket.         |
    |**Microsoft-konto-ID**     |Klicka om du vill kopiera värdet till Urklipp. Använd det här värdet som **Microsoft-konto-ID** när [du skapar din roll-ARN i AWS](#create-a-new-aws-role-for-purview).           |
    |**Externt ID**     |Klicka om du vill kopiera värdet till Urklipp. Använd det här värdet som det **externa ID: t** när [du skapar din roll ARN i AWS.](#create-a-new-aws-role-for-purview)        |
    |**Roll ARN**     | När du har [skapat Amazon IAM-rollen](#create-a-new-aws-role-for-purview)navigerar du till din roll i IAM-avsnittet, kopierar **rollens ARN** -värde och anger det här. Exempel: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Mer information finns i [Hämta din nya roll ARN](#retrieve-your-new-role-arn). |
    | | |

    Välj **skapa** när du är klar för att slutföra skapandet av autentiseringsuppgifterna.

Mer information om avdelningens kontroll-autentiseringsuppgifter finns i dokumentationen för den [offentliga för hands versionen av Azure avdelningens kontroll](manage-credentials.md).

### <a name="create-a-new-aws-role-for-purview"></a>Skapa en ny AWS-roll för avdelningens kontroll

1.  Öppna **Amazon Web Services** -konsolen och välj **IAM** under **säkerhet, identitet och efterlevnad**.

1. Välj **roller** och **skapa sedan roll**.

1. Välj **ett annat AWS-konto** och ange sedan följande värden:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Konto-ID**     |    Ange ditt Microsoft-konto-ID. Exempelvis: `615019938638`     |
    |**Externt ID**     |   Under alternativ väljer du **Kräv externt ID...** och anger sedan ditt externa ID i det angivna fältet. <br>Exempelvis: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>Du kan hitta det här externa ID: t när du.  |
    | | |

    > [!NOTE]
    > Du hittar värdena för både **ID för Microsoft-konto** och **externt ID** i avsnittet autentiseringsuppgifter för avdelningens kontroll **Management Center**  >   där du [skapade dina avdelningens kontroll-autentiseringsuppgifter](#create-a-purview-credential-for-your-aws-bucket-scan).
    >

    Exempel:

    ![Lägg till Microsoft-konto-ID: t till ditt AWS-konto.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. Filtrera behörigheterna som visas till **S3** i avsnittet **skapa roll > koppla behörighets principer** . Välj **AmazonS3ReadOnlyAccess** och välj sedan **Nästa: Taggar**.

    ![Välj ReadOnlyAccess-principen för den nya inläsnings rollen för Amazon S3.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > **AmazonS3ReadOnlyAccess** -principen ger de lägsta behörigheter som krävs för att genomsöka dina S3-buckets och kan även innehålla andra behörigheter.
    >
    >Om du bara vill använda de lägsta behörigheter som krävs för att genomsöka dina buckets, skapar du en ny princip med de behörigheter som anges i [lägsta behörigheter för din AWS-princip](#minimum-permissions-for-your-aws-policy), beroende på om du vill genomsöka en enskild Bucket eller alla Bucket i ditt konto. 
    >
    >Tillämpa den nya principen på rollen i stället för **AmazonS3ReadOnlyAccess.**

1. I avsnittet **Lägg till taggar (valfritt)** kan du välja att skapa en meningsfull tagg för den nya rollen. Användbara Taggar gör det möjligt att organisera, spåra och kontrol lera åtkomst för varje roll som du skapar.

    Ange en ny nyckel och ett värde för taggen efter behov. När du är klar, eller om du vill hoppa över det här steget, väljer du **Nästa: granska** för att granska roll informationen och slutföra skapandet av rollen.

    ![Lägg till en meningsfull tagg för att ordna, spåra eller kontrol lera åtkomsten för den nya rollen.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. Gör följande i **gransknings** avsnittet:

    - I fältet **rollnamn** anger du ett beskrivande namn för rollen
    - I rutan **roll Beskrivning** anger du en valfri beskrivning för att identifiera rollens syfte
    - I avsnittet **principer** bekräftar du att rätt princip (**AmazonS3ReadOnlyAccess**) är kopplad till rollen.

    Välj sedan **skapa roll** för att slutföra processen.

    Exempel:

    ![Granska informationen innan du skapar din roll.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Konfigurera genomsökning för krypterade Amazon S3-buckets

AWS-buckets stöder flera krypterings typer. För buckets som använder **AWS-KMS-** kryptering krävs särskild konfiguration för att aktivera genomsökning.

> [!NOTE]
> För buckets som inte använder någon kryptering, AES-256 eller AWS-KMS S3-kryptering, hoppar du över det här avsnittet och fortsätter att [Hämta ditt Amazon S3-Bucket-namn](#retrieve-your-amazon-s3-bucket-name).
>

**Kontrol lera vilken typ av kryptering som används i dina Amazon S3-buckets:**

1. I AWS går du till **lagring**  >  **S3** > och väljer **buckets** på menyn till vänster.

    ![Välj fliken Amazon S3-buckets.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Välj den Bucket som du vill kontrol lera. På sidan information om Bucket, Välj fliken **Egenskaper** och rulla ned till **standard krypterings** ytan.

    - Om den Bucket som du har valt har kon figurer ATS för något men **AWS** kryptering, inklusive om standard kryptering för Bucket är **inaktive rad**, hoppar du över resten av den här proceduren och fortsätter med [att hämta ditt Amazon S3-Bucket-namn](#retrieve-your-amazon-s3-bucket-name).

    - Om den Bucket som du har valt har kon figurer ATS för **AWS-KMS** , Fortsätt enligt beskrivningen nedan för att lägga till en ny princip som gör det möjligt att genomsöka en Bucket med anpassad **AWS-KMS-** kryptering.

    Exempel:

    ![Visa en Amazon S3-Bucket som kon figurer ATS med AWS-KMS-kryptering](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Så här lägger du till en ny princip för att söka igenom en Bucket med anpassad AWS-KMS-kryptering:**

1. I AWS navigerar du till **Services**  >   **IAM**  >   -**principer** och väljer **Skapa princip**.

1. På fliken **Skapa princip**  >  **visuellt** objekt definierar du principen med följande värden:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Tjänst**     |  Ange och välj **KMS**.       |
    |**Åtgärder**     | Under **åtkomst nivå** väljer du **Skriv** för att expandera avsnittet **Skriv** .<br>När du har expanderat väljer du alternativet **dekryptera** .        |
    |**Resurser**     |Välj en enskild resurs eller **alla resurser**.         |
    | | |

    När du är klar väljer du **Granska princip** för att fortsätta.

    ![Skapa en princip för att genomsöka en Bucket med AWS-KMS-kryptering.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. På sidan **Granska princip** anger du ett beskrivande namn för principen och en valfri beskrivning och väljer sedan **Skapa princip**.

    Den nyligen skapade principen läggs till i listan över principer.

1. Koppla den nya principen till rollen som du har lagt till för genomsökning.

    1. Gå tillbaka till **IAM**  >  -**roll** sidan och välj den roll som du lade till [tidigare](#create-a-new-aws-role-for-purview).

    1. På fliken **behörigheter** väljer du **bifoga principer**.

        ![På rollens fliken behörigheter väljer du bifoga principer.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. På sidan **bifoga behörigheter** söker du efter och väljer den nya princip som du skapade ovan. Välj **bifoga princip** för att koppla principen till rollen.

        Sidan **Sammanfattning** uppdateras med den nya principen som är kopplad till din roll.

        ![Visa en uppdaterad sammanfattnings sida med den nya principen som är kopplad till din roll.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Hämta din nya roll ARN

Du måste registrera AWS-ARN och kopiera den till avdelningens kontroll när du [skapar en genomsökning för din Amazon S3-Bucket](#create-a-scan-for-one-or-more-amazon-s3-buckets).

**Så här hämtar du din roll ARN:**

1. I roll områdena AWS **Identity and Access Management (IAM)**  >   söker du efter och väljer den nya rollen som du [skapade för avdelningens kontroll](#create-a-purview-credential-for-your-aws-bucket-scan).

1. På rollens **sammanfattnings** sida väljer du knappen **Kopiera till Urklipp** till höger om **rollens ARN** -värde.

    ![Kopiera rollens ARN-värde till Urklipp.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Klistra in det här värdet på en säker plats, redo att användas när [du skapar en sökning för din Amazon S3-Bucket](#create-a-scan-for-one-or-more-amazon-s3-buckets).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Hämta ditt Amazon S3-Bucket-namn

Du behöver namnet på din Amazon S3-Bucket för att kopiera det till avdelningens kontroll när du [skapar en genomsökning för din Amazon S3-Bucket](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**Hämta ditt Bucket-namn:**

1. I AWS går du till **lagring**  >  **S3** > och väljer **buckets** på menyn till vänster.

    ![Visa fliken Amazon S3-buckets.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Sök efter och välj Bucket för att visa sidan med Bucket-information och kopiera sedan Bucket-namnet till Urklipp.

    Exempel:

    ![Hämta och kopiera URL: en för S3-Bucket.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Klistra in Bucket-namnet i en säker fil och Lägg till ett `s3://` prefix till det för att skapa värdet som du måste ange när du konfigurerar Bucket som en avdelningens kontroll-resurs.

    Exempelvis: `s3://purview-tutorial-bucket`

> [!NOTE]
> Endast rot nivån i din Bucket stöds som en avdelningens kontroll-datakälla. Till exempel stöds *inte* följande URL som innehåller en undermapp: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Hitta ditt AWS-konto-ID

Du behöver ditt AWS-konto-ID för att registrera ditt AWS-konto som en avdelningens kontroll data källa, tillsammans med alla dess buckets.

Ditt AWS-konto-ID är det ID som du använder för att logga in på AWS-konsolen. Du kan också hitta den när du är inloggad på IAM-instrumentpanelen, till vänster under navigerings alternativen och överst, som den numeriska delen av din inloggnings-URL:

Exempel:

![Hämta ditt AWS-konto-ID.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Lägg till en enda Amazon S3-Bucket som en avdelningens kontroll-resurs

Använd den här proceduren om du bara har en enda S3-Bucket som du vill registrera till avdelningens kontroll som en data källa, eller om du har flera buckets i ditt AWS-konto, men inte vill registrera alla till avdelningens kontroll.

**Så här lägger du till din Bucket**: 

1. Starta avdelningens kontroll-portalen med hjälp av den dedikerade avdelningens kontroll-anslutningen för Amazon S3 URL. Den här URL: en tillhandahölls av Amazon S3 avdelningens kontroll Connector-produkt hanterings teamet.

    ![Starta avdelningens kontroll-portalen.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Gå till sidan med Azure avdelningens kontroll- **källor** och välj **Registrera** ![ register-ikon.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Fortsätt**.

    ![Lägg till en Amazon AWS-Bucket som en avdelningens kontroll-datakälla.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Om du har flera [samlingar](manage-data-sources.md#manage-collections) och vill lägga till din Amazon S3 i en speciell samling väljer du **vyn karta** längst upp till höger och väljer sedan **register** ![ ikonen registrera.](./media/register-scan-amazon-s3/register-button.png) i din samling.
    >

1. I fönstret **register källor (Amazon S3)** som öppnas anger du följande information:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Namn**     |Ange ett beskrivande namn eller Använd det angivna standardvärdet.         |
    |**Bucket-URL**     | Ange din AWS Bucket-URL med hjälp av följande syntax:   `s3://<bucketName>`     <br><br>**Obs**: se till att du bara använder rot nivån för din Bucket utan några undermappar. Mer information finns i [Hämta ditt Amazon S3-Bucket-namn](#retrieve-your-amazon-s3-bucket-name). |
    |**Välj en samling** |Om du har valt att registrera en data källa inifrån en samling visas redan den samlingen. <br><br>Välj en annan samling, ingen om det behövs, **ingen** samling eller **ny** för att skapa en ny samling nu. <br><br>Mer information om avdelningens kontroll-samlingar finns i [Hantera data källor i Azure avdelningens kontroll](manage-data-sources.md#manage-collections).|
    | | |

    När du är klar väljer du **Slutför** för att slutföra registreringen.

Fortsätt med [att skapa en sökning för en eller flera Amazon S3-buckets.](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Lägg till ett Amazon-konto som en avdelningens kontroll-resurs

Använd den här proceduren om du har flera S3-buckets i ditt Amazon-konto och du vill registrera alla som avdelningens kontroll data källor.

När du [konfigurerar din genomsökning](#create-a-scan-for-one-or-more-amazon-s3-buckets)kan du välja de angivna buckets som du vill genomsöka, om du inte vill genomsöka alla dem tillsammans.

**Så här lägger du till ditt Amazon-konto**:
1. Starta avdelningens kontroll-portalen med hjälp av den dedikerade avdelningens kontroll-anslutningen för Amazon S3 URL. Den här URL: en tillhandahölls av Amazon S3 avdelningens kontroll Connector-produkt hanterings teamet.

    ![Starta koppling för Amazon S3-dedikerad avdelningens kontroll-Portal](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Gå till sidan med Azure avdelningens kontroll- **källor** och välj **Registrera** ![ register-ikon.](./media/register-scan-amazon-s3/register-button.png) > **Amazon-konton**  >  **Fortsätt**.

    ![Lägg till ett Amazon-konto som en data källa för avdelningens kontroll.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Om du har flera [samlingar](manage-data-sources.md#manage-collections) och vill lägga till din Amazon S3 i en speciell samling väljer du **vyn karta** längst upp till höger och väljer sedan **register** ![ ikonen registrera.](./media/register-scan-amazon-s3/register-button.png) i din samling.
    >

1. I fönstret **register källor (Amazon S3)** som öppnas anger du följande information:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Namn**     |Ange ett beskrivande namn eller Använd det angivna standardvärdet.         |
    |**Konto-ID för AWS**     | Ange ditt AWS-konto-ID. Mer information finns i [hitta ditt AWS-konto-ID](#locate-your-aws-account-id)|
    |**Välj en samling** |Om du har valt att registrera en data källa inifrån en samling visas redan den samlingen. <br><br>Välj en annan samling, ingen om det behövs, **ingen** samling eller **ny** för att skapa en ny samling nu. <br><br>Mer information om avdelningens kontroll-samlingar finns i [Hantera data källor i Azure avdelningens kontroll](manage-data-sources.md#manage-collections).|
    | | |

    När du är klar väljer du **Slutför** för att slutföra registreringen.

Fortsätt med [att skapa en sökning för en eller flera Amazon S3-buckets](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Skapa en sökning för en eller flera Amazon S3-buckets

När du har lagt till Bucket som avdelningens kontroll-datakällor kan du konfigurera en sökning så att den körs med schemalagda intervall eller direkt.

1. Navigera till avsnittet Azure avdelningens kontroll **sources** och gör sedan något av följande:

    - I **vyn karta** väljer du **ny skannings** ![ ikon för ny skanning.](./media/register-scan-amazon-s3/new-scan-button.png) i rutan data källa.
    - Hovra över raden för din data källa i **listvyn** och välj **ny skannings** ![ ikon för ny skanning. ](./media/register-scan-amazon-s3/new-scan-button.png)

1. I fönstret **Skanna...** som öppnas till höger definierar du följande fält och väljer sedan **Fortsätt**:

    |Fält  |Beskrivning  |
    |---------|---------|
    |**Namn**     |  Ange ett beskrivande namn för genomsökningen eller Använd standardvärdet.       |
    |**Typ** |Visas bara om du har lagt till ditt AWS-konto, där alla buckets ingår. <br><br>De aktuella alternativen omfattar bara **alla**  >  **Amazon S3**. Håll koll på för fler alternativ för att välja avdelningens kontroll support mat ris expanderar. |
    |**Autentiseringsuppgift**     |  Välj en avdelningens kontroll-autentiseringsuppgift med din roll ARN. <br><br>**Tips**: Välj **nytt** om du vill skapa en ny autentiseringsuppgift för tillfället. Mer information finns i [skapa en avdelningens kontroll-autentiseringsuppgift för din AWS-Bucket-genomsökning](#create-a-purview-credential-for-your-aws-bucket-scan).     |
    | **Amazon S3**    |   Visas bara om du har lagt till ditt AWS-konto, där alla buckets ingår. <br><br>Välj en eller flera buckets som ska genomsökas eller **Välj alla** om du vill söka igenom alla buckets i ditt konto.      |
    | | |

    Avdelningens kontroll kontrollerar automatiskt att rollen ARN är giltig och att buckets och objekten i buckets är tillgängliga och fortsätter sedan om anslutningen lyckas.

    > [!TIP]
    > Om du vill ange olika värden och testa anslutningen själv innan du fortsätter väljer du **Testa anslutning** längst ned till höger innan du väljer **Fortsätt**.
    >

1. I fönstret **Välj en regel uppsättning för skanning** väljer du antingen **AmazonS3** standard regel uppsättning eller en **ny skannings regel uppsättning** för att skapa en ny anpassad regel uppsättning. När du har valt regel uppsättningen väljer du **Fortsätt**.

    Om du väljer att skapa en ny anpassad skannings regel uppsättning använder du guiden för att definiera följande inställningar:

    |Fönster  |Beskrivning  |
    |---------|---------|
    |**Ny skannings regel uppsättning** /<br>**Beskrivning av genomsöknings regel**    |   Ange ett meningsfullt namn och en valfri beskrivning av regel uppsättningen      |
    |**Välj filtyper**     | Välj alla filtyper som du vill ta med i genomsökningen och välj sedan **Fortsätt**.<br><br>Om du vill lägga till en ny filtyp väljer du **ny filtyp** och definierar följande: <br>– Det fil namns tillägg som du vill lägga till <br>– En valfri beskrivning  <br>– Om fil innehållet har en anpassad avgränsare eller är en system fil typ. Ange sedan din anpassade avgränsare eller Välj system fil typ. <br><br>Välj **skapa** för att skapa din anpassade filtyp.     |
    |**Välj klassificerings regler**     |   Navigera till och välj de klassificerings regler som du vill köra på din data uppsättning.      |
    |     |         |

    Välj **skapa** när du är klar för att skapa regel uppsättningen.

1. I fönstret **Ange en utlösare för sökning** väljer du något av följande och väljer sedan **Fortsätt**:

    - **Återkommande** för att konfigurera ett schema för en återkommande sökning
    - En **gång** för att konfigurera en genomsökning som startar omedelbart

1. Kontrol lera genomsöknings informationen i **gransknings** fönstret för att bekräfta att de är korrekta och välj sedan **Spara** eller **Spara och kör** om du valde **en gång** i föregående ruta.

    > [!NOTE]
    > Genomsökningen kan ta upp till 24 timmar innan den har startats. Du kommer att kunna granska dina **Insight-rapporter** och söka i katalogen 24 timmar efter att du startat varje genomsökning.
    >

Mer information finns i [utforska avdelningens kontroll scanning Results](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Utforska avdelningens kontroll genomsöknings resultat

När en avdelningens kontroll-genomsökning har slutförts på dina Amazon S3-buckets kan du gå nedåt i avsnittet avdelningens kontroll- **källor** för att Visa genomsöknings historiken.

Välj en data källa för att visa information om den och välj sedan fliken **genomsökningar** för att visa alla pågående eller slutförda genomsökningar.
Om du har lagt till ett AWS-konto med flera buckets visas genomsöknings historiken för varje Bucket under kontot.

Exempel:

![Visa AWS S3-buckets-genomsökningar under din AWS-konto källa.](./media/register-scan-amazon-s3/account-scan-history.png)

Använd de andra områdena i avdelningens kontroll för att ta reda på mer om innehållet i din datafastighet, inklusive Amazon S3-buckets:

- **Sök i avdelningens kontroll Data Catalog** och filtrera efter en bestämd Bucket. Exempel:

    ![Sök i katalogen efter AWS S3-till gångar.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Visa insikts rapporter** för att visa statistik för klassificering, känslighets etiketter, filtyper och mer information om ditt innehåll.

    Alla avdelningens kontroll Insight-rapporter inkluderar Amazon S3-genomsöknings resultat, tillsammans med resten av resultaten från dina Azure-datakällor. Vid behov har en ytterligare **Amazon S3** -till gångs typ lagts till i rapport filtrerings alternativen.

    Mer information finns i [förstå insikter i Azure avdelningens kontroll](concept-insights.md).

## <a name="minimum-permissions-for-your-aws-policy"></a>Lägsta behörighet för din AWS-princip

Standard proceduren för att [skapa en AWS-roll för avdelningens kontroll](#create-a-new-aws-role-for-purview) som ska användas vid genomsökning av S3-buckets använder **AmazonS3ReadOnlyAccess** -principen.

**AmazonS3ReadOnlyAccess** -principen ger de lägsta behörigheter som krävs för att genomsöka dina S3-buckets och kan även innehålla andra behörigheter.

Om du bara vill använda de lägsta behörigheter som krävs för att genomsöka Bucket, skapar du en ny princip med de behörigheter som anges i följande avsnitt, beroende på om du vill genomsöka en enskild Bucket eller alla Bucket i ditt konto.

Tillämpa den nya principen på rollen i stället för **AmazonS3ReadOnlyAccess.**

### <a name="individual-buckets"></a>Enskilda buckets

När du skannar enskilda S3-buckets, inkluderar lägsta AWS-behörigheter:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Se till att definiera din resurs med det angivna Bucket-namnet. Exempel:

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

När du skannar alla buckets i ditt AWS-konto inkluderar minsta AWS-behörighet:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Se till att definiera din resurs med ett jokertecken. Exempel:

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

Läs mer om Azure avdelningens kontroll Insight-rapporter:

> [!div class="nextstepaction"]
> [Förstå insikter i Azure Purview](concept-insights.md)
