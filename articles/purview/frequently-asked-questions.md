---
title: Vanliga frågor och svar (FAQ)
description: I den här artikeln får du svar på vanliga frågor om Azure avdelningens kontroll.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667814"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Vanliga frågor och svar om Azure avdelningens kontroll

## <a name="overview"></a>Översikt

Många organisationer saknar en holistisk förståelse för deras data. Det är svårt att förstå vilka data som finns, var data finns och hur du hittar och kommer åt relevanta data. Data saknar kontext, till exempel härkomst, klassificering och omfattande metadata, vilket gör det svårt för företags användare att söka efter rätt data och använda dessa data på rätt sätt. Detta innebär att endast en liten del av insamlade data används för att informera affärs beslut. Slutligen är identifiering av data säkerhets problem och skydd av känsliga data inkonsekvent. Det kräver kontinuerlig tid och ansträngning, särskilt samtidigt som data försmidigas.

Azure avdelningens kontroll är en lösning för data styrning. Det hjälper kunderna att få djupgående kunskap om alla sina data samtidigt som de behåller kontrollen över användningen. Med Azure avdelningens kontroll kan organisationer upptäcka och granska data. De får insikter om sin datafastighet och styr centralt åtkomsten till data.

## <a name="purpose-of-this-faq"></a>Syftet med vanliga frågor och svar

I det här vanliga avsnittet besvaras vanliga frågor som kunder och fält grupper ofta ställer. Det är avsett att klargöra frågor om Azure avdelningens kontroll och relaterade lösningar, till exempel Azure Data Catalog (ADC) gen 2 (inaktuell) och Azure Information Protection.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Vilka är de käll typer som är tillgängliga för genomsökning och klassificering av metadata?

|Azure|Icke-Azure|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (tillgänglig i slutet av 2020)|
|Azure SQL Managed Instance|SAP ECC (tillgängligt i slutet av 2020)|
|Öppna Azure-datautforskaren|SAP S/4 HANA (tillgänglig i slutet av 2020)|
|Azure Data Lake Storage Gen1|Hive-Metaarkiv (tillgängligt i slutet av 2020)|
|Azure Data Lake Storage Gen2|Amazon S3|
|Azure Files|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Vilka data system/processorer kan vi ansluta till och få härkomst?

|Data system/processor 
|---------
|Azure Data Factory: kopierings aktivitet, data flödes aktivitet 
|Anpassad härkomst   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>Hur ADC gen 2, Azure Information Protection och Azure avdelningens kontroll är relaterade?

Azure-avdelningens kontroll började ursprungligen som ADC gen 2 men har sedan vidgats i omfånget. Det omfattar nu de avancerade katalog funktionerna i ADC gen 2 tillsammans med data klassificering, etikettering och efterlevnad av policyn för efterlevnadsprinciper i Azure Information Protection. Idag justerar den mer nära den bredare bransch definitionen för data styrning.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>Vad händer med kunder som använder ADC gen 1?

Azure avdelningens kontroll är fokus för all produkt innovation i katalog lösnings utrymmet för Microsoft. ADC gen 1 kommer även fortsättnings vis att stödjas.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Kan kunder ha flera Azure avdelningens kontroll-konton i samma prenumeration?

Ja, vi har stöd för många Azure avdelningens kontroll-konton per prenumeration och per klient.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>Kan jag köra ADC gen 1 och Azure avdelningens kontroll parallellt?

Ja. Båda är oberoende tjänster.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Hur gör jag för att migrera befintliga ADC gen 1-datatill gångar till Azure avdelningens kontroll?

Använd Azure avdelningens kontroll-API: er för att extrahera från ADC gen 1 och mata in i Azure avdelningens kontroll. För ord listan stöder vi Mass verktyg baserade på CSV.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Hur gör jag för att kryptera känsliga data för SQL-tabeller med Azure-avdelningens kontroll?

Data kryptering görs på data källans nivå. Azure avdelningens kontroll lagrar bara metadata. Det går inte att förhandsgranska data.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Kommer alla funktioner i ADC gen 2 finnas i Azure avdelningens kontroll?

Ja.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Vad är skillnaden mellan en ord lista och klassificering?

En ord lista använder en namn konvention följt av icke-tekniska/affärs användare av data, även kallade data konsumenter. Dessa typer av personer är affärsanalytiker eller data experter som använder Azure avdelningens kontroll för att söka efter vissa typer av data, baserat på företags användning. Till exempel kan tillhandahållandet av kedje analytiker behöva söka efter *SKU-typer* och *försändelse information*. De söker efter relevanta data i ord listan för dessa villkor.
Klassificering är en tagg som tillämpas på en data till gång på tabell-, kolumn-eller filnivå som identifierar vilka data som finns i till gången. Klassificering kan tillämpas automatiskt eller manuellt, baserat på vilken typ av data som hittas. Normalt använder du klassificerings taggar för att identifiera om en till gång innehåller känsliga data och vilken typ av känsliga data som kan vara.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Söker Azure avdelningens kontroll igenom och klassificerar e-postmeddelanden, PDF-filer osv. i min SharePoint och OneDrive?

Sökning efter lokala SharePoint-webbplatser och-bibliotek tillhandahålls via Azure Information Protection scanner. Skannern kan användas via en kunds Microsoft 365 prenumeration med följande SKU: er: AIP P1, EMS E3 och M365 E3. Om du har någon av dessa SKU: er bör du ha rätt rättigheter för att börja använda Azure Information Protection scannern.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Vad används för att skanna?
Det finns en Microsoft-hanterad skannings infrastruktur. För de flesta Azure/AWS-resurser som vi stöder behöver du inte distribuera en skannings infrastruktur.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Finns det något sätt att etablera Azure-avdelningens kontroll via Azure Resource Manager (ARM) Template/CLI/PowerShell?

Ja, ARM-mall är tillgänglig

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Jag använder redan Atlas, kan jag enkelt flytta till Azure-avdelningens kontroll?

Azure avdelningens kontroll är kompatibelt med Atlas-API. Om du migrerar från Atlas rekommenderar vi att du först söker igenom data källorna med Azure avdelningens kontroll. När till gångarna är tillgängliga i ditt konto kan du använda liknande Atlas-API: er för att integrera, till exempel uppdatera till gångar eller lägga till anpassade härkomst. Azure avdelningens kontroll ändrar Sök-API: et för att använda Azure Search så du bör kunna använda avancerad sökning.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Kan jag skapa flera kataloger i min klient organisation?

Ja, du kan skapa flera Azure avdelningens kontroll-konton per prenumeration och per klient. Du kan granska sidan gränser [Hantera och öka kvoter för resurser med Azure avdelningens kontroll](how-to-manage-quotas.md).

Ytterligare rekommendationer om när du bör eller inte bör ha flera konton dokumenteras i våra [bästa praxis för Azure avdelningens kontroll-distribution](deployment-best-practices.md).

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Kan jag registrera flera klienter i ett enda Azure avdelningens kontroll-konto?

Nej, för närvarande för att kunna söka igenom en annan klients data källa måste du skapa ett separat Azure avdelningens kontroll-konto i den klienten.

### <a name="does-azure-purview-support-column-level-lineage"></a>Stöder Azure avdelningens kontroll härkomst på kolumn nivå?

Ja, Azure avdelningens kontroll stöder härkomst på kolumn nivå.