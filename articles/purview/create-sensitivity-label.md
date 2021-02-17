---
title: Använd automatiskt känslighets etiketter för dina data
description: Lär dig hur du skapar känslighets etiketter och använder dem automatiskt för dina data under en genomsökning.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 70aaa528fc86c9e543267b68b5b4cf157ec2dc65
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535271"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Etikettera data automatiskt i Azure avdelningens kontroll

Den här artikeln beskriver hur du skapar känsliga MIP-etiketter (Microsoft Information Protection) och tillämpar dem automatiskt på dina Azure-tillgångar i Azure avdelningens kontroll.

## <a name="what-are-sensitivity-labels"></a>Vad är känslighets etiketter? 

Personer i din organisation samarbetar med andra både i och utanför organisationen för att få jobbet gjort. Data behålls inte alltid i molnet och ofta roamas överallt, mellan enheter, appar och tjänster. 

När dina data är roamade, vill du att de ska göra det på ett säkert sätt som uppfyller organisationens principer för affärs-och efterlevnad.

Genom att använda känslighets etiketter kan du ange hur känslig viss information som finns i din organisation. Ett enskilt projekt namn kan till exempel vara mycket konfidentiellt i din organisation, medan samma term inte är konfidentiell till andra organisationer. 

### <a name="sensitivity-labels-in-azure-purview"></a>Känslighets etiketter i Azure avdelningens kontroll

I avdelningens kontroll liknar klassificeringarna ämnes koder och används för att markera och identifiera data för en speciell typ som finns i din datafastighet under genomsökningen.

Avdelningens kontroll använder samma klassificeringar, även kallade känsliga informations typer, som Microsoft 365.  MIP känslighets etiketter skapas i Microsoft 365 Security and Compliance Center (SCC). På så sätt kan du utöka dina befintliga känslighets etiketter i dina Azure avdelningens kontroll-tillgångar.

**Klassificeringar** matchas direkt, till exempel en social Security Number, som har en klassificering av **person nummer**. 

**Känslighets etiketter** tillämpas däremot när en eller flera klassificeringar och villkor hittas tillsammans. I det här sammanhanget refererar [villkoren](/microsoft-365/compliance/apply-sensitivity-label-automatically) till alla parametrar som du kan definiera för ostrukturerade data, till exempel *närhet till en annan klassificering* och *% konfidensgrad*. 

Känslighets etiketter i Azure avdelningens kontroll kan användas för att automatiskt tillämpa etiketter på filer och databas kolumner.

Mer information finns i:

- [Lär dig mer om känslighets etiketter](/microsoft-365/compliance/sensitivity-labels) i Microsoft 365-dokumentationen
- [Vad är etiketterade regler automatiskt?](#what-are-auto-labeling-rules)
- [Data typer som stöds för känslighets etiketter i Azure avdelningens kontroll](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [Etiketter för SQL Database-kolumner](#labeling-for-sql-database-columns)

#### <a name="what-are-auto-labeling-rules"></a>Vad är etiketterade regler automatiskt?

Dina data växer ständigt och förändras. Att spåra data som för närvarande inte är märkta och som vidtar åtgärder för att manuellt tillämpa etiketter är inte bara besvärliga, men det är också onödigt att gå vidare. 

Automatiskt märkta regler är villkor som du anger, och anger när en viss etikett ska användas. När dessa villkor är uppfyllda, tilldelas etiketten automatiskt data, vilket behåller konsekventa känslighets etiketter för dina data, i skala.

När du skapar etiketter ska du se till att definiera regler för automatisk etikett för både [filer](#define-auto-labeling-rules-for-files) och [databas kolumner](#define-auto-labeling-rules-for-database-columns) för att automatiskt tillämpa dina etiketter med varje data genomsökning. 

När du har skannat dina data i avdelningens kontroll kan du visa etiketterna som används automatiskt i avdelningens kontroll-katalogen och Insight-rapporter.
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Data typer som stöds för känslighets etiketter i Azure avdelningens kontroll

Känslighets etiketter stöds i Azure dataavdelningens kontrolls för följande data typer:

|Datatyp  |Källor  |
|---------|---------|
|Automatisk etikettering för filer     |     – Azure Blob Storage  </br>-Azure Data Lake Storage gen 1 och gen 2  |
|Automatisk etikettering för databas kolumner     |  – SQL Server </br>– Azure SQL Database </br>-Azure SQL Database Hanterad instans   <br> – Azure-Synapse  <br>-Azure Cosmos DB <br><br>Mer information finns i [kolumn etiketter för SQL Database](#labeling-for-sql-database-columns) nedan.  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>Etiketter för SQL Database-kolumner

Förutom avdelningens kontroll-etiketter för databas kolumner, stöder Microsoft också etiketter för SQL Database-kolumner med SQL data-klassificeringen i [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). När avdelningens kontroll använder globala [MIP](/microsoft-365/compliance/sensitivity-labels), använder SSMS endast etiketter som definierats lokalt.

Märkning i avdelningens kontroll och märkning i SSMS är separata processer som inte interagerar med varandra. Därför visas inte etiketter som används i SSMS i avdelningens kontroll och vice versa. Vi rekommenderar Azure-avdelningens kontroll för att märka SQL-databaser, eftersom den använder globala MIP-etiketter som kan tillämpas på flera plattformar.

Mer information finns i dokumentationen för [SQL data Discovery och klassificering](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Så här skapar du känslighets etiketter i Microsoft 365

Om du inte redan har känslighets etiketter måste du skapa dem och göra dem tillgängliga för Azure avdelningens kontroll. Befintliga känslighets etiketter kan också ändras så att de blir tillgängliga för Azure avdelningens kontroll.

Mer information finns i:

- [Licenskrav](#licensing-requirements)
- [Utöka känslighets etiketter till Azure-avdelningens kontroll](#extending-sensitivity-labels-to-azure-purview)
- [Skapa nya känslighets etiketter eller ändra befintliga etiketter](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>Licenskrav

MIP känslighets etiketter skapas och hanteras i Microsoft 365 Security and Compliance Center. Du måste ha en aktiv Microsoft 365 E5-licens för att kunna skapa känslighets etiketter för användning i Azure-avdelningens kontroll.

Om du inte redan har den licens som krävs kan du registrera dig för en utvärderings version av [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Utöka känslighets etiketter till Azure-avdelningens kontroll

Som standard är MIP känslighets etiketter bara tillgängliga för till gångar i Microsoft 365, där du kan tillämpa dem på filer och e-postmeddelanden.

Om du vill använda MIP-känslighets etiketter på Azure-resurser i Azure avdelningens kontroll måste du uttryckligen godkänna att utöka etiketterna och välja de etiketter som du vill ska vara tillgängliga i avdelningens kontroll.

Genom att utöka MIPs känslighets etiketter med Azure avdelningens kontroll kan organisationer nu upptäcka, klassificera och få inblick i känsligheten i ett bredare utbud av data källor, vilket minimerar kompatibiliteten.

> [!NOTE]
> Eftersom Microsoft 365 och Azure avdelningens kontroll är separata tjänster, finns det en risk att de distribueras i olika regioner. Etikett namn och namn på anpassade känsliga informations typer anses vara kund information och bevaras inom samma geografiska plats som standard för att skydda känsligheten hos dina data och för att undvika GDPR lagar.
>
> Därför delas etiketter och anpassade känsliga informations typer inte till Azure-avdelningens kontroll som standard och kräver att ditt medgivande används i Azure avdelningens kontroll.

**Utöka känslighets etiketter till avdelningens kontroll:**

I Microsoft 365 går du till **Information Protection** sidan. I rutan **utöka etiketter till till gångar i Azure avdelningens kontroll** väljer du knappen **slå på** och väljer sedan **Ja** i den bekräftelse dialog ruta som visas.

Exempel:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="Välj * * Aktivera * * för att utöka känslighets etiketter till avdelningens kontroll" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
När du har utökat etiketter till till gångar i Azure avdelningens kontroll kan du välja de etiketter som du vill göra tillgängliga i avdelningens kontroll. Mer information finns i [skapa nya känslighets etiketter eller ändra befintliga etiketter](#creating-new-sensitivity-labels-or-modifying-existing-labels).
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>Skapa nya känslighets etiketter eller ändra befintliga etiketter

1. Öppna [Microsoft 365 Security och Compliance Center](https://protection.office.com/homepage). 

1. Under **lösningar** väljer du **informations skydd** och väljer sedan **skapa en etikett**. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Skapa känslighets etiketter i Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. Namnge etiketten. Under **definiera omfånget för den här etiketten** väljer du **filer och e-postmeddelanden** och **Azure avdelningens kontroll-tillgångar**.
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Skapa din etikett i Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. Följ resten av anvisningarna i guiden för dina etikett inställningar. 

    Definiera särskilt regler för automatisk etikett för filer och databas kolumner:

    - [Definiera regler för automatisk etikett för filer](#define-auto-labeling-rules-for-files)
    - [Definiera regler för automatisk etikett för databas kolumner](#define-auto-labeling-rules-for-database-columns)

    Mer information om alternativ i guiden finns i [vilka känslighets etiketter som kan](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) användas i Microsoft 365-dokumentationen.

1. Upprepa stegen ovan för att skapa fler etiketter. 

    Om du vill skapa en underordnad etikett väljer du den överordnade etiketten > **...**  >  **Fler åtgärder**  >  **Lägg till under etikett**.

1. Om du vill ändra befintliga etiketter bläddrar du till **Information Protection**  >  **Etiketter** och väljer din etikett. 

    Välj sedan **Redigera etikett** för att öppna guiden **Redigera känslighets etikett** igen, med alla de inställningar som du definierade när du skapade etiketten.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="Redigera en befintlig känslighets etikett" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. När du är klar med att skapa alla etiketter ser du till att du ser din etikett ordning och ordnar om dem efter behov. 

    Om du vill ändra ordning på en etikett väljer du **...** **> fler åtgärder**  >  **Flytta upp** eller **Flytta ned.** 

    Mer information finns i [etikett prioritet (sorterings frågor)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) i Microsoft 365-dokumentationen.

> [!IMPORTANT]
> Ta inte bort en etikett om du inte förstår konsekvenserna för dina användare. 
>
> Mer information finns i [ta bort och ta bort etiketter](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) i Microsoft 365-dokumentationen.

Fortsätt genom [att genomsöka dina data för att tillämpa etiketter automatiskt](#scan-your-data-to-apply-labels-automatically)och sedan:

- [Visa etiketter på till gångar](#view-labels-on-assets)
- [Visa Insight-rapporter för klassificeringar och känslighets etiketter](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-auto-labeling-rules-for-files"></a>Definiera regler för automatisk etikett för filer

Definiera regler för automatisk etikettering för filer i guiden när du skapar eller redigerar din etikett. 

På sidan **Auto-Labeling för Office-appar** aktiverar du **Automatisk etikettering för Office-appar** och definierar sedan de villkor där du vill att etiketten automatiskt ska tillämpas på dina data.

Exempel:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Definiera regler för automatisk etikett för filer i Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
Mer information finns i [använda en känslighets etikett för data automatiskt](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) i Microsoft 365-dokumentationen. 

#### <a name="define-auto-labeling-rules-for-database-columns"></a>Definiera regler för automatisk etikett för databas kolumner

Definiera regler för automatisk etikettering för databas kolumner i guiden när du skapar eller redigerar din etikett. 

Under alternativet **Azure avdelningens kontroll-tillgångar (för hands version)** :

1. Välj skjutreglaget för att markera **automatiskt för databas kolumner** .

1. Välj **kontrol lera känsliga informations typer** för att välja de känsliga informations typer som du vill använda för din etikett.

Exempel:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Definiera regler för automatisk etikett för SQL-kolumner i Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>Sök igenom dina data för att tillämpa etiketter automatiskt

Sök igenom dina data i Azure avdelningens kontroll för att automatiskt tillämpa de etiketter som du har skapat, baserat på de regler för automatisk etikett som du har definierat. 

Mer information om hur du konfigurerar genomsökningar på olika till gångar i Azure avdelningens kontroll finns i:

|Källa  |Referens  |
|---------|---------|
|**Azure Blob Storage**     |[Registrera och skanna Azure-Blob Storage](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Registrera och skanna Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Registrera och skanna Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Azure SQL Databases**|[Registrera och skanna en Azure SQL Database](register-scan-azure-sql-database.md) </br>[Registrera och skanna en Azure SQL Database Hanterad instans](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>Visa etiketter på till gångar

När du har definierat regler för automatisk etikett för dina etiketter i Microsoft 365 och genomsöker dina data i Azure avdelningens kontroll, används etiketter automatiskt för dina till gångar. 

**Så här visar du etiketter som används för dina till gångar i Azure avdelningens kontroll-katalogen:**

I Azure avdelningens kontroll-katalogen använder du **etikett** filtrerings alternativ för att endast visa filer med vissa etiketter. Exempel: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="Sök efter till gångar efter etikett" lightbox="media/create-sensitivity-label/filter-search-results.png":::

Exempel:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Visa en känslighets etikett för en fil i din Azure-Blob Storage" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Visa Insight-rapporter för klassificeringar och känslighets etiketter

Hitta insikter om dina klassificerade och märkta data i Azure avdelningens kontroll med hjälp av rapporten **klassificering** och **känslighets etiketter** .

> [!div class="nextstepaction"]
> [Klassificerings insikter](./classification-insights.md)

> [!div class="nextstepaction"]
> [Etikett insikter för känslighet](sensitivity-insights.md)


