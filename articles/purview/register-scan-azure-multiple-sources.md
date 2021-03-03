---
title: Så här genomsöker du Azure flera källor
description: Lär dig hur du skannar en hel Azure-prenumeration eller resurs grupp i din Azure avdelningens kontroll Data Catalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: c57ac9ddbebcf02cb0118705b63f97fd1880b0f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696022"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Registrera och skanna Azure flera källor

Den här artikeln beskriver hur du registrerar en Azure Multiple-källa (Azure-prenumerationer eller resurs grupper) i avdelningens kontroll och konfigurerar en genomsökning på den.

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure flera källor stöder genomsökningar för att avbilda metadata och schema på de flesta Azure-avdelningens kontroll som stöds av. Den klassificerar också data automatiskt baserat på system-och anpassade klassificerings regler.

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett Azure avdelningens kontroll-konto innan du registrerar data källor. Mer information om hur du skapar ett avdelningens kontroll-konto finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
- Du måste vara en Azure avdelningens kontroll data source-administratör
- Konfigurera autentisering enligt beskrivningen i avsnitten nedan

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Konfigurera autentisering för att räkna upp resurser under en prenumeration eller resurs grupp

1. Gå till prenumerationen eller resurs gruppen i Azure Portal.  
1. Välj **Access Control (IAM)**   i den vänstra navigerings menyn 
1. Du måste vara ägare eller administratör för användar åtkomst för att lägga till en roll i prenumerationen eller resurs gruppen. Välj *+ Lägg till* . 
1. Ange rollen **läsare** och ange namnet på ditt Azure avdelningens kontroll-konto (som representerar dess MSI) under Välj indatamängd. Klicka på *Spara* för att slutföra roll tilldelningen.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Konfigurera autentisering för genomsökning av resurser under en prenumeration eller resurs grupp

Det finns två sätt att konfigurera autentisering för Azure flera källor:

- Hanterad identitet
- Tjänstens huvudnamn

> [!NOTE]
> Du måste konfigurera autentisering för varje resurs i din prenumeration eller resurs grupp, som du tänker registrera och skanna. Resurs typerna Azure Storage (Azure Blob Storage och Azure Data Lake Storage Gen2) gör det enkelt att lägga till MSI-eller tjänstens huvud namn på prenumerations-eller resurs grupps nivå som Storage BLOB data Reader. Behörigheterna trickle ned till varje lagrings konto i den prenumerationen eller resurs gruppen. För alla andra resurs typer måste du använda MSI-eller tjänstens huvud namn på varje resurs, eller enhet ett skript för att göra det. Så här lägger du till behörigheter för varje resurs typ i en prenumeration eller resurs grupp.
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Hanterad instans](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Registrera en Azure Multiple-källa

Gör så här för att registrera en ny Azure-datakälla i din data katalog:

1. Navigera till ditt avdelningens kontroll-konto
1. Välj **källor** i det vänstra navigerings fältet
1. Välj **register**
1. Välj **Azure (flera)** på **register källor**
1. Välj **Fortsätt**

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Registrera Azure flera källor":::

På skärmen **Registrera källor (Azure flera)** gör du följande:

1. Ange ett **namn** som data källan ska visas i listan med i katalogen 
1. Du kan också välja en **hanterings grupp** som du vill filtrera ned till
1. **Välj en prenumeration eller en specifik resurs grupp** under en angiven prenumeration i list rutan. Registrerings omfånget kommer att anges till den valda prenumerationen eller resurs gruppen  
1. Välj en **samling** eller skapa en ny (valfritt)
1. **Slutför** registreringen av data källan

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Konfigurera flera Azure-källor":::

## <a name="creating-and-running-a-scan"></a>Skapa och köra en sökning

Om du vill skapa och köra en ny genomsökning gör du följande:

1. Navigera till avsnittet **källor**

1. Välj den data källa som du har registrerat

1. Klicka på Visa information och välj **+ Ny skanning** eller Använd ikonen Skanna snabb åtgärd på käll panelen

1. Fyll i *namnet* och välj alla typer av resurser som du vill genomsöka i den här källan

    1. Du kan lämna det som *alla* (Detta inkluderar framtida resurs typer som kanske inte finns i den prenumerationen eller resurs gruppen)
    1. Du kan **specifikt välja resurs typer** som du vill genomsöka. Om du väljer det här alternativet kommer framtida resurs typer som kan skapas i den här prenumerationen eller resurs gruppen inte att inkluderas för genomsökningar, om inte genomsökningen redige ras i framtiden
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Azure-skanning på flera källor":::

1. Välj autentiseringsuppgifter för att ansluta till resurserna i din data källa. 
    1. Du kan välja en **autentiseringsuppgift på den överordnade nivån** som MSI eller en viss typ av autentiseringsuppgifter för tjänstens huvud namn, som du kan välja att använda för alla resurs typer under prenumerationen eller resurs gruppen
    1. Du kan också **välja resurs typ och använda olika autentiseringsuppgifter** för den resurs typen
    1. Varje autentiseringsuppgift betraktas som autentiseringsmetod för alla resurser under en viss typ
    1. Du måste ange de valda autentiseringsuppgifterna för resurserna för att kunna söka igenom dem enligt beskrivningen i det här [avsnittet](#Setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) ovan
1. Inom varje typ kan du välja att antingen söka igenom alla resurser eller en delmängd av dem efter namn.
    1. Om du lämnar alternativet som **alla** kommer framtida resurser av den typen också att genomsökas i framtida skannings körningar
    1. Om du väljer ett specifikt lagrings konto eller SQL-databaser inkluderas inte framtida resurser som skapats av den typen i den här prenumerationen eller resurs gruppen för genomsökningar, om inte genomsökningen redige ras i framtiden
 
1.  Fortsätt genom att klicka på **Fortsätt** . Vi kommer att testa åtkomsten för att kontrol lera om du har tillämpat avdelningens kontroll MSI som läsare för prenumerationen eller resurs gruppen. Om ett fel meddelande genereras följer du anvisningarna [här](#Setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Välj **skannings regel uppsättningar** för varje resurs typ som valts i föregående steg. Du kan också skapa skannings regel uppsättningar infogade.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Val av regel uppsättning för Azure Multiple Scan":::

1. Välj din genomsöknings utlösare. Du kan schemalägga att den ska köras **varje vecka/månad** eller **en gång**

1. Granska din genomsökning och välj Spara för att slutföra installationen   

## <a name="viewing-your-scans-and-scan-runs"></a>Visa genomsökningar och skannings körningar

1. Visa information om källan genom att klicka på **Visa detaljer** på panelen under avsnittet källor. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Information om flera källor i Azure"::: 

1. Visa information om genomsöknings körningar genom att gå till sidan med **genomsöknings information** .
    1. *Statusfältet* är en kort sammanfattning av de underordnade resursernas körnings status. Den kommer att visas på prenumerations-eller resurs grupps nivå
    1. Grönt betyder att det lyckades, medan rött innebär fel. Grått innebär att genomsökningen fortfarande pågår
    1. Du kan klicka i varje skanning för att visa mer detaljerade Detaljer

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Information om flera Azure-sökningar":::

1. Visa en sammanfattning av de senaste misslyckade genomsöknings körningarna längst ned på sidan med käll information. Du kan också klicka i om du vill visa mer detaljerad information som rör dessa körningar.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Hantera dina genomsökningar – redigera, ta bort eller Avbryt
Gör följande för att hantera eller ta bort en genomsökning:

- Gå till hanterings centret. Välj data källor under avsnittet källor och sökning och välj sedan på önskad data Källa

- Välj den genomsökning som du vill hantera. Du kan redigera sökningen genom att välja Redigera

- Du kan ta bort din genomsökning genom att välja ta bort
- Om en genomsökning körs kan du även avbryta den

## <a name="next-steps"></a>Nästa steg

- [Bläddra i Azure avdelningens kontroll Data Catalog](how-to-browse-catalog.md)
- [Sök i Azure avdelningens kontroll-Data Catalog](how-to-search-catalog.md)    
