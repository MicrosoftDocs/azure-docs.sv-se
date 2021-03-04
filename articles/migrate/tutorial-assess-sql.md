---
title: Självstudie för att utvärdera SQL-instanser för migrering till Azure SQL-hanterad instans och Azure SQL Database
description: Lär dig hur du skapar en utvärdering för Azure SQL i Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 9b33890d53f67eee870b42462a65b4a0b7ba9981
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055637"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Självstudie: utvärdera SQL-instanser för migrering till Azure SQL

Som en del av migreringen till Azure bedömer du dina lokala arbets belastningar för att mäta moln beredskap, identifiera risker och beräkna kostnader och komplexitet.
Den här artikeln visar hur du bedömer identifierade SQL Server instans databaser som förberedelse för migrering till Azure SQL med hjälp av verktyget Azure Migrate: identifiering och bedömning.

> [!Note]
> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i för hands version. Om du vill testa den här funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) för att skapa ett projekt i regionen **östra Australien** . Om du redan har ett projekt i östra Australien och vill testa den här funktionen, måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kör en utvärdering baserat på Server konfiguration och prestanda data.
> * Granska en Azure SQL-utvärdering 

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ där det är möjligt. 


## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

- Innan du följer den här självstudien för att utvärdera SQL Server instanser för migrering till Azure SQL kontrollerar du att du har identifierat de SQL-instanser som du vill utvärdera med hjälp av Azure Migrate-installationen, [följer den här kursen](tutorial-discover-vmware.md)

## <a name="run-an-assessment"></a>Köra en utvärdering
Kör en utvärdering på följande sätt:
1. På sidan **översikt** > **Windows, Linux och SQL Server** klickar du på **utvärdera och migrera servrar**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Översikts sida för Azure Migrate":::
2. På **Azure Migrate: identifiering och utvärdering**, klicka på **utvärdera** och välj utvärderings typ som **Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Listruta för att välja utvärderings typ som Azure SQL":::
3. I **utvärderings servrar** > kan du se utvärderings typen i förväg valt som **Azure SQL** och identifierings källan som standard till servrar som **identifierats från Azure Migrate-enheten**.

4. Klicka på **Redigera** för att granska utvärderings egenskaperna.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Knappen Redigera där du kan anpassa utvärderings egenskaper":::
5. I bedömnings egenskaper > **mål egenskaper**:
    - Ange den Azure-region som du vill migrera till på **mål platsen**. 
        - Azure SQL-konfigurationen och kostnads rekommendationerna baseras på den plats som du anger. 
    - I **mål distributions typ**,
        - Välj **rekommenderas** om du vill Azure Migrate utvärdera beredskap för dina SQL-instanser för att migrera till Azure SQL mi och Azure SQL DB, och rekommendera det bästa anpassade distributions alternativet, mål nivån, Azure SQL-konfigurationen och månads uppskattningar. [Läs mer](concepts-azure-sql-assessment-calculation.md)
        - Välj **Azure SQL DB** om du vill utvärdera beredskap för dina SQL-instanser för migrering till enbart Azure SQL-databaser och granska mål nivån, Azure SQL-konfigurationen och månads uppskattningar.
        - Välj **Azure SQL mi** om du vill utvärdera beredskap för dina SQL-instanser för migrering till endast Azure SQL Managed instance och granska mål nivån, Azure SQL-konfigurationen och månads uppskattningar.
    - I **reserverad kapacitet** anger du om du vill använda reserverad kapacitet för SQL-servern efter migreringen.
        - Om du väljer alternativet reserverad kapacitet kan du inte ange "rabatt (%)".

6. I bedömnings egenskaper > **bedömnings kriterier**:
    - Storleks kriterierna är standardiserade för **prestanda,** vilket innebär att Azure Migrate samlar in prestanda mått som rör SQL-instanser och de databaser som hanteras av IT för att rekommendera en optimal storlek Azure SQL Database-och/eller Azure SQL-hanterad instans konfiguration. Du kan ange:
        - **Prestanda historik** som visar data varaktigheten som du vill basera utvärderingen på. (Standardvärdet är en dag)
        - **Percentil**, för att ange percentilvärdet som du vill använda för prestanda exemplet. (Standardvärdet är 95 percentil)
    - I **komfort faktor** anger du den buffert som du vill använda under utvärderingen. Dessa konton för problem som säsongs användning, kort prestanda historik och sannolika ökningar i framtida användning. Om du till exempel använder en bekvämlighets faktor på två: 
        
        **Komponent** | **Effektiv användning** | **Lägg till bekvämlighets faktor (2,0)**
        --- | --- | ---
        Kärnor | 2  | 4
        Minne | 8 GB | 16 GB
   
7. I **prissättning**:
    - I **erbjudande/licensierings program** anger du Azure-erbjudandet om du är registrerad. För närvarande kan du bara välja från "betala per användning" och "betala per användning"-utveckling/-test. 
        - Du kan utnyttja ytterligare rabatt genom att använda reserverad kapacitet och Azure Hybrid-förmån ovanpå erbjudandet betala per användning. 
        - Du kan använda Azure Hybrid-förmån ovanpå dev/test med betala per användning. Utvärderingen stöder för närvarande inte användning av reserverad kapacitet utöver erbjudande för utveckling/testning av betala per användning.
    - I **tjänst nivå** väljer du det mest lämpliga tjänst nivå alternativet för att tillgodose dina verksamhets behov för migrering till Azure SQL Database och/eller Azure SQL-hanterad instans: 
        - Välj **rekommenderas** om du vill Azure Migrate rekommendera den bästa lämpliga tjänst nivån för dina servrar. Detta kan vara generell användning eller affärs kritisk. Läs mer
        - Välj **generell användning** om du vill att en Azure SQL-konfiguration ska vara utformad för budgetorienterade arbets belastningar.
        - Välj **affärskritisk** om du vill att en Azure SQL-konfiguration är utformad för arbets belastningar med låg latens med hög återhämtnings kapacitet till fel och snabba redundanser.
    - I **rabatt (%)**, Lägg till eventuella prenumerations rabatter som du får ovanpå Azure-erbjudandet. Standardinställningen är 0%.
    - I **valuta** väljer du fakturerings valutan för ditt konto.
    - I **Azure Hybrid-förmån** anger du om du redan har en SQL Server licens. Om du gör det, och de omfattas med aktiv Software Assurance för SQL Server prenumerationer, kan du ansöka om Azure Hybrid-förmån när du gör licenser till Azure.
    - Klicka på Spara om du gör ändringar.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Knappen Spara på bedömnings egenskaper":::
8. I **utvärdera servrar** > klickar du på Nästa.
9.  I **Välj servrar för att utvärdera**  >  **bedömnings namnet** > anger du ett namn för utvärderingen.
10. I **Välj eller skapa en grupp** > väljer du **Skapa ny** och anger ett grupp namn.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Knappen placering av ny grupp":::
11. Välj enheten och välj de servrar som du vill lägga till i gruppen. Klicka sedan på Nästa.
12. Granska utvärderings informationen i **Granska och skapa utvärdering** och klicka på Skapa utvärdering för att skapa gruppen och köra utvärderingen.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Plats för granska och skapa utvärderings knapp.":::
13. När utvärderingen har skapats går du till **Windows, Linux och SQL Server**  >  **Azure Migrate: identifierings-och utvärderings** panelen > klickar på siffran bredvid Azure SQL-utvärderingen.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Navigering till skapad utvärdering":::
15. Klicka på det bedömnings namn som du vill visa.

> [!NOTE]
> Eftersom Azure SQL-utvärderingar är prestandabaserade utvärderingar rekommenderar vi att du väntar minst en dag efter att du har startat identifieringen innan du skapar en utvärdering. Detta ger dig tid att samla in prestanda data med högre tillförlitlighet. Om identifieringen fortfarande pågår, markeras beredskapen för dina SQL-instanser som **okänd**. Vi rekommenderar att du när du har startat identifieringen och **väntar på varaktigheten för prestanda som du anger (dag/vecka/månad)** för att skapa eller beräkna om utvärderingen för en hög exakthet. 

## <a name="review-an-assessment"></a>Granska en utvärdering

**Så här visar du en utvärdering**:

1. **Windows, Linux och SQL Server**  >  **Azure Migrate: identifiering och bedömning** > Klicka på numret bredvid Azure SQL-utvärderingen.
2. Klicka på det bedömnings namn som du vill visa. Som exempel (uppskattningar och kostnader endast för exempel): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="Översikt över SQL-utvärdering":::
3. Granska utvärderings sammanfattningen. Du kan också redigera bedömnings egenskaperna eller beräkna om utvärderingen.

#### <a name="discovered-items"></a>Identifierade objekt

Detta anger antalet SQL-servrar, instanser och databaser som utvärderades i utvärderingen.
    
#### <a name="azure-readiness"></a>Azure-beredskap

Detta visar distributionen av utvärderade SQL-instanser: 
    
**Mål distributions typ (i bedömnings egenskaper)** | **Beredskap**   
--- | --- |
**Rekommenderas** |  Redo för Azure SQL Database, redo för Azure SQL-hanterad instans, eventuellt redo för virtuell Azure-dator, beredskap okänd (om identifieringen pågår eller om det finns några identifierings problem som ska åtgärdas)
**Azure SQL DB** eller **Azure SQL mi** | Redo för Azure SQL Database eller Azure SQL-hanterad instans, inte redo för Azure SQL Database eller Azure SQL-hanterad instans, beredskap okänd (om identifieringen pågår eller om det finns några identifierings problem som ska åtgärdas)
     
Du kan öka detalj nivån om du vill veta mer om migrerings problem/varningar som du kan åtgärda innan du migrerar till Azure SQL. [Läs mer](concepts-azure-sql-assessment-calculation.md) Du kan också granska de rekommenderade Azure SQL-konfigurationerna för att migrera till Azure SQL-databaser och/eller hanterade instanser.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Information om Azure SQL Database och hanterad instans kostnad

Uppskattningen av månads kostnaden omfattar beräknings-och lagrings kostnader för Azure SQL-konfigurationer som motsvarar den rekommenderade distributions typen Azure SQL Database och/eller Azure SQL-hanterad instans. [Läs mer](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Granska beredskap

1. Klicka på **Azure SQL-beredskap**.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Information om Azure SQL-beredskap":::
1. I Azure SQL-beredskap granskar du **Azure SQL DB** -beredskap och **Azure SQL mi-beredskap** för de utvärderade SQL-instanserna:
    - **Klar**: instansen är redo att migreras till Azure SQL DB/mi utan några problem med migreringen eller varningar. 
        - Klar (länkad och blå informations ikon): instansen är redo att migreras till Azure SQL DB/MI utan några migreringsåtgärder, men det finns vissa migrerings varningar som du måste granska. Du kan klicka på hyperlänken för att granska migrerings varningar och den rekommenderade reparations vägledningen: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="utvärdering med klar status":::       
    - **Inte klar**: instansen har ett eller flera migreringsåtgärder för migrering till Azure SQL DB/mi. Du kan klicka på hyperlänken och granska problemen med migreringen och den rekommenderade reparations vägledningen.
    - **Okänd**: Azure Migrate kan inte utvärdera beredskap, eftersom identifieringen pågår eller eftersom det finns problem under identifieringen som måste åtgärdas från meddelande bladet. Kontakta Microsoft-supporten om problemet kvarstår. 
1. Granska den rekommenderade distributions typen för den SQL-instans som bestäms enligt matrisen nedan:

    - **Mål distributions typ** (enligt vald i bedömnings egenskaper): **rekommenderas**

        **Beredskap för Azure SQL DB** | **Azure SQL MI-beredskap** | **Rekommenderad distributions typ** | **Beräknas Azure SQL-konfiguration och kostnads uppskattningar?**
         --- | --- | --- | --- |
        Redo | Redo | Azure SQL DB eller Azure SQL MI [Läs mer](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | Ja
        Redo | Inte klar eller okänd | Azure SQL-databas | Ja
        Inte klar eller okänd | Redo | Azure SQL MI | Ja
        Inte klar | Inte klar | Potentiellt redo för Azure VM [Läs mer](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | Inga
        Inte klar eller okänd | Inte klar eller okänd | Okänt | Inga
    
    - **Mål distributions typ** (som valts i bedömnings egenskaper): **Azure SQL DB**
    
        **Beredskap för Azure SQL DB** | **Beräknas Azure SQL-konfiguration och kostnads uppskattningar?**
        --- | --- |
        Redo | Ja
        Inte klar | Inga
        Okänt | Inga
    
    - **Mål distributions typ** (som valts i bedömnings egenskaper): **Azure SQL mi**
    
        **Azure SQL MI-beredskap** | **Beräknas Azure SQL-konfiguration och kostnads uppskattningar?**
         --- | --- |
        Redo | Ja
        Inte klar | Inga
        Okänt | Inga

4. Klicka på instansen för att öka detalj nivån om du vill se antalet användar databaser, instans information inklusive instans egenskaper, beräkna (begränsat till instans) och information om käll databas lagring.
5. Klicka på antalet användar databaser för att granska listan över databaser och deras information. Som exempel (uppskattningar och kostnader endast för exempel): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="information om SQL-instans":::
5. Klicka på granska information i kolumnen migrations problem om du vill granska problem med migrering och varningar för en viss mål distributions typ. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Problem med databas migrering och varningar":::

### <a name="review-cost-estimates"></a>Granska kostnadsuppskattningar
Utvärderings sammanfattningen visar den beräknade kostnaden för månatlig beräkning och lagring för Azure SQL-konfigurationer som motsvarar de rekommenderade distributions typerna Azure SQL-databaser och/eller hanterade instanser.

1. Granska de totala månads kostnaderna. Kostnaderna sammanställs för alla SQL-instanser i den utvärderade gruppen.
    - Kostnads uppskattningar baseras på den rekommenderade Azure SQL-konfigurationen för en instans. 
    - Uppskattade månatliga kostnader för beräkning och lagring visas. Som exempel (uppskattningar och kostnader endast för exempel):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Kostnads information":::

1. Du kan öka detalj nivån på en instans nivå för att se Azure SQL-konfiguration och kostnads uppskattningar på en instans nivå.  
1. Du kan också öka detalj nivån för databas listan för att granska Azure SQL-konfigurationen och kostnads uppskattningarna per databas när en Azure SQL Database konfiguration rekommenderas.

### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme
Azure Migrate tilldelar en konfidensnivå till alla Azure SQL-utvärderingar baserat på tillgängligheten för de data punkter för prestanda/användning som behövs för att beräkna utvärderingen av alla utvärderade SQL-instanser och databaser. Omdömet är från en stjärna (lägst) till fem stjärnor (högst).
Förtroende omdömet hjälper dig att beräkna tillförlitligheten för storleks rekommendationer i utvärderingen. Tillförlitlighets klassificeringen är följande:

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Läs mer](concepts-azure-sql-assessment-calculation.md#confidence-ratings) om Tillförlitlighets klassificeringar.


## <a name="next-steps"></a>Nästa steg

- [Lär dig mer](concepts-azure-sql-assessment-calculation.md) om hur Azure SQL-utvärderingar beräknas.
- Börja migrera SQL-instanser och databaser med hjälp av [Azure Database migration service](https://docs.microsoft.com/azure/dms/dms-overview).
