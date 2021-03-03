---
title: 'Snabb start: skapa en Synapse-arbetsyta'
description: Skapa en Synapse-arbetsyta genom att följa stegen i den här hand boken.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d2f2ed99af288b72a4c53f07d3cd7ba8b46974c0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693636"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Snabb start: skapa en Synapse-arbetsyta
I den här snabb starten beskrivs stegen för att skapa en Azure dataSynapses-arbetsyta med hjälp av Azure Portal.

## <a name="create-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

1. Öppna [Azure Portal](https://portal.azure.com)och högst upp i Sök efter **Synapse**.
1. I Sök resultaten under **tjänster** väljer du **Azure Synapse Analytics**.
1. Välj **Lägg till** för att skapa en arbets yta.
1. På fliken **grundläggande** ger du arbets ytan ett unikt namn. Vi använder **mysworkspace** i det här dokumentet
1. Du behöver ett ADLSGEN2-konto för att skapa en arbets yta. Det enklaste valet är att skapa ett nytt. Om du vill använda en befintlig konfiguration igen måste du utföra en del ytterligare konfiguration. 
1. ALTERNATIV 1 Skapa ett nytt ADLSGEN2-konto 
    1. Under **välj Data Lake Storage gen 2**, klickar du på **Skapa nytt** och ge den namnet **contosolake**.
    1. Under **välj Data Lake Storage gen 2**, klickar du på **fil system** och namnger IT- **användare**.
1. ALTERNATIV 2 Se anvisningarna för att **förbereda ett lagrings konto** längst ned i det här dokumentet.
1. Din Azure Synapse-arbetsyta kommer att använda det här lagrings kontot som det primära lagrings kontot och behållaren för att lagra data i arbets ytan. Arbets ytan lagrar data i Apache Spark tabeller. Programmet lagrar Spark-programloggarna under en mapp med namnet **/Synapse/workspacename**.
1. Välj **Granska + skapa** > **Skapa**. Din arbets yta är klar på några minuter.

> [!NOTE]
> När du har skapat din Azure Synapse-arbetsyta kan du inte flytta arbets ytan till en annan Azure Active Directory-klient. Om du gör det via migrering av prenumerationer eller andra åtgärder kan du förlora åtkomsten till artefakterna i arbetsytan.
> Dessutom kan du för närvarande inte skapa en Synapse Analytics-arbetsyta i en prenumeration på [moln lösnings leverantörer (CSP)](https://docs.microsoft.com/partner-center/csp-overview) .

## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta i [Azure Portal](https://portal.azure.com). Överst i **översikts** avsnittet väljer du **Starta Synapse Studio**.
* Gå till `https://web.azuresynapse.net` och logga in på din arbets yta.

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>Förbereda ett befintligt lagrings konto för användning med Azure Synapse Analytics

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Navigera till ett befintligt lagrings konto för ADLSGEN2
1. Välj **åtkomst kontroll (IAM)** i den vänstra rutan. Tilldela sedan följande roller eller kontrol lera att de redan har tilldelats:
    * Tilldela rollen som **ägare** .
    * Tilldela rollen som **ägare av Storage BLOB-data** .
1. I den vänstra rutan väljer du **behållare** och skapar en behållare.
1. Du kan ge behållaren ett valfritt namn. I det här dokumentet namnger vi behållar **användare**.
1. Godkänn standardinställningen **offentlig åtkomst nivå** och välj sedan **skapa**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Konfigurera åtkomst till lagrings kontot från din arbets yta

Hanterade identiteter för din Azure Synapse-arbetsyta kanske redan har åtkomst till lagrings kontot. Följ dessa steg för att se till att:

1. Öppna [Azure Portal](https://portal.azure.com) och det primära lagrings kontot som valts för din arbets yta.
1. Välj **åtkomst kontroll (IAM)** i den vänstra rutan.
1. Tilldela följande roller eller kontrol lera att de redan har tilldelats. Vi använder samma namn för arbets ytans identitet och arbets ytans namn.
    * För rollen **Storage BLOB data Contributor** på lagrings kontot tilldelar du min **arbets yta** som arbets ytans identitet.
    * Tilldela min **arbets yta** som arbets ytans namn.

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

* [Skapa en dedikerad SQL-pool](quickstart-create-sql-pool-studio.md) 
* [Skapa en server lös Apache Spark-pool](quickstart-create-apache-spark-pool-portal.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)
