---
title: Säkerhets kopiering och återställning av Azure Analysis Services databasen | Microsoft Docs
description: Den här artikeln beskriver hur du säkerhetskopierar och återställer modell-metadata och data från en Azure Analysis Services databas.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 6c2ea063e37660767d266c15dcfc471ee5d73a02
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732116"
---
# <a name="analysis-services-database-backup-and-restore"></a>Säkerhets kopiering och återställning av Analysis Services databasen

Att säkerhetskopiera tabell modell databaser i Azure Analysis Services är ungefär samma som för lokala Analysis Services. Den främsta skillnaden är den plats där du lagrar dina säkerhets kopior. Säkerhetskopierade filer måste sparas i en behållare i ett [Azure Storage-konto](../storage/common/storage-account-create.md). Du kan använda ett lagrings konto och en behållare som du redan har, eller så kan de skapas när du konfigurerar lagrings inställningar för servern.

> [!NOTE]
> Att skapa ett lagrings konto kan resultera i en ny fakturerbar tjänst. Mer information finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

> [!NOTE]
> Om lagrings kontot finns i en annan region konfigurerar du inställningarna för lagrings konto brand väggen för att tillåta åtkomst från **valda nätverk**. I brand Väggs **adress intervall** anger du IP-adressintervallet för den region som Analysis Services-servern finns i. Det finns stöd för att konfigurera brand Väggs inställningar för lagrings konto för att tillåta åtkomst från alla nätverk, men att välja valda nätverk och ange ett IP-adressintervall rekommenderas. Mer information finns i [vanliga frågor och svar om nätverks anslutning](analysis-services-network-faq.md#backup-and-restore).

Säkerhets kopior sparas med tillägget. ABF. För i-minnes tabell modeller lagras både modell data och metadata. För DirectQuery-tabell modeller lagras bara modellens metadata. Säkerhets kopior kan komprimeras och krypteras beroende på vilka alternativ du väljer.


## <a name="configure-storage-settings"></a>Konfigurera lagrings inställningar
Innan du säkerhetskopierar måste du konfigurera lagrings inställningarna för servern.


### <a name="to-configure-storage-settings"></a>Konfigurera lagrings inställningar
1.  Klicka på **säkerhetskopiera** i Azure Portal > **Inställningar**.

    ![Säkerhets kopieringar i inställningar](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klicka på **aktive rad** och sedan på **lagrings inställningar**.

    ![Aktivera](./media/analysis-services-backup/aas-backup-enable.png)

3. Välj ditt lagrings konto eller skapa ett nytt.

4. Välj en behållare eller skapa en ny.

    ![Välja container](./media/analysis-services-backup/aas-backup-container.png)

5. Spara inställningarna för säkerhets kopiering.

    ![Spara inställningar för säkerhets kopiering](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Säkerhetskopiera med hjälp av SSMS

1. I SSMS högerklickar du på en databas > **säkerhets kopiering**.

2. I säkerhets kopierings **databasens**  >  **säkerhets kopia** klickar du på **Bläddra**.

3. I dialog rutan **Spara filen som** kontrollerar du mappsökvägen och skriver sedan ett namn för säkerhets kopian. 

4. I dialog rutan **Säkerhetskopiera databas** väljer du alternativ.

    **Tillåt att filen skrivs över** – Välj det här alternativet om du vill skriva över säkerhetskopierade filer med samma namn. Om det här alternativet inte är markerat kan filen du sparar inte ha samma namn som en fil som redan finns på samma plats.

    **Tillämpa komprimering** – Välj det här alternativet om du vill komprimera säkerhets kopierings filen. Komprimerade säkerhets kopior sparar disk utrymme, men kräver lite högre processor belastning. 

    **Kryptera säkerhets kopierings fil** – Välj det här alternativet om du vill kryptera säkerhets kopian. För det här alternativet krävs ett användar lösen ord för att skydda säkerhets kopierings filen. Lösen ordet förhindrar läsning av säkerhets kopierings data på annat sätt än en återställnings åtgärd. Om du väljer att kryptera säkerhets kopior lagrar du lösen ordet på en säker plats.

5. Klicka på **OK** för att skapa och spara säkerhets kopian.


### <a name="powershell"></a>PowerShell
Använd [backup-databas-](/powershell/module/sqlserver/backup-asdatabase) cmdlet.

## <a name="restore"></a>Återställ
När du återställer måste säkerhets kopian finnas i det lagrings konto som du har konfigurerat för servern. Om du behöver flytta en säkerhets kopia från en lokal plats till ditt lagrings konto använder du [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) eller kommando rads verktyget [AzCopy](../storage/common/storage-use-azcopy-v10.md) . 



> [!NOTE]
> Om du återställer från en lokal server måste du ta bort alla domän användare från modellens roller och lägga tillbaka dem till rollerna som Azure Active Directory användare.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Återställa med hjälp av SSMS

1. I SSMS högerklickar du på en databas > **återställning**.

2. I dialog rutan **Säkerhetskopiera databas** i **säkerhets kopia** klickar du på **Bläddra**.

3. I dialog rutan **Leta upp databasfiler** väljer du den fil som du vill återställa.

4. I **restore Database** väljer du databasen.

5. Ange alternativ. Säkerhets alternativen måste matcha de säkerhets kopierings alternativ som du använde när du säkerhetskopierade.


### <a name="powershell"></a>PowerShell

Använd [restore-Database-](/powershell/module/sqlserver/restore-asdatabase) cmdlet.


## <a name="related-information"></a>Relaterad information

[Azure-lagringskonton](../storage/common/storage-account-create.md)  
[Hög tillgänglighet](analysis-services-bcdr.md)      
[Vanliga frågor och svar om nätverks anslutning Analysis Services](analysis-services-network-faq.md)