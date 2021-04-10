---
title: Anpassa utvärderingar för Azure Migrate | Microsoft Docs
description: Beskriver hur du anpassar utvärderingar som skapats med Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 9bda4750f6b4340399bbbe070954dd23930b1ae1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785214"
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

I den här artikeln beskrivs hur du anpassar utvärderingar som skapats av Azure Migrate identifierings-och utvärderings verktyg.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata/offentliga virtuella moln datorer till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).

Du kan använda verktyget Azure Migrate identifiering och utvärdering för att skapa utvärderingar för lokala virtuella VMware-datorer och virtuella Hyper-V-datorer, som förberedelse för migrering till Azure. Verktyget för identifiering och bedömning bedömer lokala servrar för migrering till virtuella Azure IaaS-datorer och Azure VMware-lösning (AVS). 

## <a name="about-assessments"></a>Om utvärderingar

Utvärderingar som du skapar med identifierings-och utvärderings verktyget är en tidpunkts ögonblicks bild av data. Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: identifiering och bedömning.

**Utvärderingstyp** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md), [virtuella Hyper-V-datorer](how-to-set-up-appliance-hyper-v.md)och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till Azure med hjälp av den här utvärderings typen. (concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware Solution (AVS) med hjälp av den här utvärderingstypen.[Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

Storleks villkors alternativ i Azure Migrate utvärderingar:

**Storlekskriterier** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar som gör rekommendationer baserat på insamlade prestandadata | **Azure VM-utvärdering**: Rekommendationen för VM-storlek baseras på processor- och minnesanvändningsdata.<br/><br/> Rekommendation för disktyp (Standard HDD/SSD eller premiumhanterade diskar) baseras på IOPS och dataflödet för de lokala diskarna.<br/><br/>**Azure SQL-utvärdering**: Azure SQL-konfigurationen baseras på prestanda data för SQL-instanser och databaser, vilket innefattar: processor användning, minnes användning, IOPS (data-och loggfiler), data flöde och svars tid för IO-åtgärder<br/><br/>**Utvärdering med Azure VMware Solution (AVS)** : Rekommendationen för AVS-noder baseras på processor- och minnesanvändningsdata.
**Som lokalt** | Utvärderingar som inte använder prestandadata till att göra rekommendationer. | **Azure VM-utvärdering**: Rekommendationen för VM-storlek baseras på den lokala virtuella datorstorleken<br/><br> Rekommenderad disktyp baseras på vad du väljer i lagringstypens inställning vid utvärderingen.<br/><br/> **Utvärdering med Azure VMware Solution (AVS)** : Rekommendationen för AVS-noder baseras på den lokala virtuella datorstorleken.

## <a name="how-is-an-assessment-done"></a>Hur görs en utvärdering?

En utvärdering som utförs i Azure Migrate identifiering och utvärdering har tre steg. Utvärderingen börjar med en analys av lämplighet, följt av storlek och slutligen en uppskattning av månatlig kostnad. En dator flyttas bara till ett senare steg om den tidigare har passerat. Om en dator till exempel inte uppfyller Azures lämpligaste kontroll, är den markerad som olämplig för Azure, och storleks sortering och kostnads anpassning görs inte. [Läs mer.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Vad ingår i en Azure VM-utvärdering?

**Egenskap** | **Information**
--- | ---
**Målplats** | Azure-platsen du vill migrera till.<br/> Azure VM-utvärderingen stöder för närvarande dessa mål regioner: östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala Indien, centrala USA, Kina, östra, Kina, norra, Asien, östra, östra USA, östra 2; USA, Tyskland, centrala, Tyskland nordöstra, Östra Japan, västra Japan, centrala Korea, södra centrala USA, norra Europa, södra centrala USA, Sydostasien, södra Indien, Storbritannien, södra, Storbritannien, västra, US gov, Arizona , US Gov, Texas, US Gov, Virginia, västra centrala USA, Västeuropa, västra Indien, västra USA och västra 2; USA.
**Lagringstyp** | Du kan använda den här egenskapen för att ange vilken typ av diskar du vill flytta till i Azure.<br/><br/> För lokal storleks ändring kan du ange mål lagrings typ antingen som Premium-hanterade diskar, Standard SSD-hanterade diskar eller Standard HDD-hanterade diskar. För prestandabaserade storleks ändringar kan du ange mål disk typen antingen som automatiskt, Premium-hanterade diskar, Standard HDD-hanterade diskar eller Standard SSD-hanterade diskar.<br/><br/> När du anger lagrings typen som automatisk, utförs disk rekommendationer baserat på diskens prestanda data (IOPS och data flöde). Om du anger lagrings typ som Premium/standard, rekommenderar utvärderingen en disk-SKU i den valda lagrings typen. Om du vill uppnå ett service avtal för en enda instans av virtuell dator på 99,9%, kan du ange lagrings typ som Premium-hanterade diskar. Detta säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar. Azure
**Reserverade instanser (RI)** | Med den här egenskapen kan du ange om du har [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure och kostnads uppskattningar i utvärderingen sedan görs i RI-rabatter. Reserverade instanser stöds för närvarande bara för erbjudandet betala per användning i Azure Migrate.
**Ändra storlek på kriterium** | Det kriterium som ska användas för att ändra storlek på virtuella datorer i Azure. Du kan antingen göra *prestandabaserade* storleks ändringar eller ändra storlek på de virtuella datorerna *lokalt*, utan att behöva beakta prestanda historiken.
**Prestandahistorik** | Varaktigheten för att utvärdera prestanda data för datorer. Den här egenskapen gäller endast när storleks kriteriet är *prestanda-baserat*.
**Percentilutnyttjande** | Percentilvärdet för prestandaexempeluppsättningen som beaktas för rätt storleksändring. Den här egenskapen gäller endast när storlek är *prestanda beroende*.
**VM-serie** |     Du kan ange den VM-serie som du vill överväga vid storleksberäkningen. Om du till exempel har en produktions miljö som du inte planerar att migrera till virtuella datorer i A-serien i Azure, kan du undanta en-serien från listan eller serien och den högra storleks ändringen görs bara i den valda serien.
**Komfortfaktor** | Azure VM-utvärderingen anses vara en buffert (bekvämlighets faktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor.
**Erbjudande** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) du har registrerat dig för. Azure Migrate beräknar kostnaden enligt detta.
**Valuta** | Faktureringsvalutan.
**Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet.<br/> Standardinställningen är 0%.
**VM-drifttid** | Om dina virtuella datorer inte kommer att köras dygnet runt i Azure, kan du ange varaktigheten (antalet dagar per månad och antalet timmar per dag) som de ska köras och kostnads uppskattningarna skulle utföras i enlighet med detta.<br/> Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure Hybrid-förmån** | Ange om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. Standardvärdet är Ja.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Vad finns i en Azure VMware-lösning (AVS)-utvärdering?

Här är what's som ingår i en AVS-utvärdering:


| **Egenskap** | **Information** |
| - | - |
| **Målplats** | Anger den plats för molnets privata moln som du vill migrera till.<br/><br/> AVS-utvärderingen stöder för närvarande dessa mål regioner: östra USA, västra Europa, västra USA. |
| **Lagringstyp** | Anger den lagrings motor som ska användas i AVS.<br/><br/> Observera att AVS-utvärderingar endast stöder virtuellt San som standard lagrings typ. |
**Reserverade instanser (RIs)** | Med den här egenskapen kan du ange reserverade instanser i AVS. Fjärrinstallationstjänster stöds för närvarande inte för AVS-noder. |
**Nodtyp** | Anger den [typ av AVS-nodtyp](../azure-vmware/concepts-private-clouds-clusters.md) som används för att mappa lokala virtuella datorer. Observera att standardvärdet för nodtypen är AV36. <br/><br/> Azure Migrate rekommenderar att antalet noder som krävs för de virtuella datorerna migreras till AVS. |
**FTT-inställning, RAID-nivå** | Anger tillämpligt problem för att tolerera och RAID-kombinationer. Det valda FTT-alternativet kombinerat med kravet på lokal virtuell dator disk avgör den totala virtuellt SAN lagring som krävs i AVS. |
**Ändra storlek på kriterium** | Anger de kriterier som ska användas för att ändra storlek på virtuella datorer i AVS _-format_ . Du kan välja _prestandabaserade_ storleks ändringar eller _lokalt_ utan att behöva beakta prestanda historiken. |
**Prestandahistorik** | Ställer in varaktigheten för att utvärdera prestanda data för datorer. Den här egenskapen gäller bara när storleks kriteriet är _prestanda-baserat_. |
**Percentilutnyttjande** | Anger percentilvärdet för den prestanda exempel uppsättning som ska beaktas för höger storlek. Den här egenskapen gäller bara när storleken är prestanda-baserad.|
**Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. |
**Erbjudande** | Visar [Azure-erbjudandet](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. Azure Migrate beräknar kostnaden enligt detta.|
**Valuta** | Visar fakturerings valutan för ditt konto. |
**Rabatt (%)** | Visar en lista över den prenumerations information som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%. |
**Azure Hybrid-förmån** | Anger om du har Software Assurance och är berättigade till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Även om detta inte har någon inverkan på priserna för Azure VMware-lösningar på grund av det Node-baserade priset kan kunderna fortfarande använda sina lokala OS-licenser (Microsoft-baserade) i AVS med Azure Hybrid-förmåner. Andra program varu leverantörer måste ange sina egna licens villkor, till exempel RHEL. |
**vCPU överprenumeration** | Anger förhållandet mellan antalet virtuella kärnor som är knutna till 1 fysisk kärna i AVS-noden. Standardvärdet i beräkningarna är 4 vCPU: 1 fysisk kärna i AVS. <br/><br/> API-användare kan ange det här värdet som ett heltal. Observera att vCPU överprenumeration > 4:1 kan leda till försämrade prestanda men kan användas för arbets belastningar för webb server typ. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Vilka egenskaper används för att skapa och anpassa en Azure SQL-utvärdering?

Här är what's som ingår i utvärderings egenskaperna för Azure SQL:

**Egenskap** | **Information**
--- | ---
**Målplats** | Den Azure-region som du vill migrera till. Azure SQL-konfigurationen och kostnads rekommendationerna baseras på den plats som du anger.
**Mål distributions typ** | Den mål distributions typ som du vill köra utvärderingen på: <br/><br/> Välj **rekommenderas** om du vill Azure Migrate utvärdera beredskap för dina SQL-servrar för att migrera till Azure SQL mi och Azure SQL DB, och rekommendera det bästa anpassade distributions alternativet, mål nivån, Azure SQL-konfigurationen och månads uppskattningar.<br/><br/>Välj **Azure SQL DB** om du vill utvärdera dina SQL-servrar för att migrera till enbart Azure SQL-databaser och granska mål nivån, Azure SQL DB-konfigurationen och månads uppskattningar.<br/><br/>Välj **Azure SQL mi**, om du vill utvärdera dina SQL-servrar för att migrera till enbart Azure SQL-databaser och granska mål nivån, Azure SQL mi-konfiguration och månads uppskattningar.
**Reserverad kapacitet** | Anger reserverad kapacitet så att kostnads uppskattningar i utvärderingen tar dem i beaktande.<br/><br/> Om du väljer alternativet reserverad kapacitet kan du inte ange "rabatt (%)".
**Storlekskriterier** | Den här egenskapen används för att anpassa Azure SQL-konfigurationen till rätt storlek. <br/><br/> Standardvärdet är " **prestanda** ", vilket innebär att utvärderingen samlar in SQL Server instanser och databas prestanda mått för att rekommendera en optimal storlek för Azure SQL-hanterad instans och/eller Azure SQL Database nivå/konfigurations rekommendation.
**Prestandahistorik** | Prestanda historik anger den varaktighet som används när prestanda data utvärderas.
**Percentilutnyttjande** | Percentils användning anger percentilvärdet för det prestanda exempel som används för att ha behörighet.
**Komfortfaktor** | Den buffert som användes under utvärderingen. IT-konton för problem som säsongs användning, kort prestanda historik och sannolika ökningar i framtida användning.<br/><br/> Till exempel resulterar en 10-Core-instans med 20% belastning vanligt vis i en instans med två kärnor. Med en bekvämlighets faktor på 2,0 är resultatet en fyra kärnor-instans i stället.
**Erbjudande/licensierings program** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du har registrerat i. För närvarande kan du bara välja från "betala per användning" och "betala per användning"-utveckling/-test. Observera att du kan utnyttja ytterligare rabatt genom att använda reserverad kapacitet och Azure Hybrid-förmån ovanpå erbjudandet betala per användning.
**Tjänstenivå** | Det lämpligaste tjänst nivå alternativet för att tillgodose ditt företags behov av migrering till Azure SQL Database och/eller Azure SQL-hanterad instans:<br/><br/>**Rekommenderas** om du vill Azure Migrate rekommendera den bästa lämpliga tjänst nivån för dina servrar. Detta kan vara generell användning eller affärs kritisk. <br/><br/> **Generell användning** Om du vill ha en Azure SQL-konfiguration som är utformad för budgetorienterade arbets belastningar. [Läs mer](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **Affärskritisk** Om du vill ha en Azure SQL-konfiguration som är utformad för arbets belastningar med låg latens med hög återhämtnings förmåga till fel och snabba redundanser. [Läs mer](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Valuta** | Fakturerings valutan för ditt konto.
**Rabatt (%)** | Eventuella prenumerations rabatter som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.
**Azure Hybrid-förmån** | Anger om du redan har en SQL Server-licens. <br/><br/> Om du gör det, och de omfattas med aktiv Software Assurance för SQL Server prenumerationer, kan du ansöka om Azure Hybrid-förmån när du gör licenser till Azure.

## <a name="edit-assessment-properties"></a>Redigera bedömnings egenskaper

Gör följande om du vill redigera bedömnings egenskaper när du har skapat en utvärdering:

1. Klicka på **servrar** i Azure Migrate-projektet.
2. I **Azure Migrate: identifiering och bedömning** klickar du på beräknings antalet.
3. I **utvärderingen** klickar du på relevant utvärdering > **Redigera egenskaper**.
5. Anpassa bedömnings egenskaperna enligt tabellerna ovan.
6. Klicka på **Spara** för att uppdatera utvärderingen.


Du kan också redigera bedömnings egenskaper när du skapar en utvärdering.


## <a name="next-steps"></a>Nästa steg

- [Lär dig mer](concepts-assessment-calculation.md) om hur AZUREs VM-utvärderingar beräknas.
- [Lär dig mer](concepts-azure-sql-assessment-calculation.md) om hur Azure SQL-utvärderingar beräknas.
- [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md) om hur AVS-utvärderingar beräknas.

