---
title: Haveri beredskap för FarmBeats
description: Den här artikeln beskriver hur data återställning skyddas från att förlora dina data.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-ummehabiba
ms.openlocfilehash: 9ece624546cab1b8b6fab8c19f4401bd050f6267
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179892"
---
# <a name="disaster-recovery-for-farmbeats"></a>Haveri beredskap för FarmBeats

Data återställning skyddar dig från att förlora dina data i en händelse som att dölja Azure-regionen. I sådana fall kan du starta redundans och återställa data som lagras i din FarmBeats-distribution.

Data återställning är inte en standard funktion i Azure FarmBeats. Du kan konfigurera den här funktionen manuellt genom att konfigurera nödvändiga Azure-resurser som används av FarmBeats för att lagra data i en Azure-kopplad region. Använd aktiv – passiv metod för att aktivera återställning.

Följande avsnitt innehåller information om hur du kan konfigurera Data återställning i Azure FarmBeats:

- [Aktivera dataredundans](#enable-data-redundancy)
- [Återställa tjänsten från säkerhets kopiering online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Aktivera dataredundans

FarmBeats lagrar data i tre Azure-tjänster från första part, som är **Azure Storage**, **Cosmos DB** och **Time Series Insights**. Använd följande steg för att aktivera dataredundans för dessa tjänster till en länkad Azure-region:

1.  **Azure Storage** – Följ den här rikt linjen för att aktivera dataredundans för varje lagrings konto i din FarmBeats-distribution.
2.  **Azure Cosmos DB** – Följ den här rikt linjen för att aktivera dataredundans för Cosmos DB konto din FarmBeats-distribution.
3.  **Azure Time Series Insights (TSD)** -TSD för närvarande inte erbjuder dataredundans. Om du vill återställa Time Series Insights data går du till din sensor/väder partner och skickar data igen till FarmBeats-distributionen.

## <a name="restore-service-from-online-backup"></a>Återställa tjänsten från säkerhets kopiering online

Du kan initiera redundans och återställa data som lagras för vilka, var och en av ovanstående data lager för din FarmBeats-distribution. När du har återställt data för Azure Storage och Cosmos DB skapar du en annan FarmBeats-distribution i Azure-kopplade regionen och konfigurerar sedan den nya distributionen så att den använder data från återställda data lager (t. ex. Azure Storage och Cosmos DB) genom att följa stegen nedan:

1. [Konfigurera Cosmos DB](#configure-cosmos-db)
2. [Konfigurera lagrings konto](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Konfigurera Cosmos DB

Kopiera åtkomst nyckeln för den återställda Cosmos DB och uppdatera den nya FarmBeats Datahub-Key Vault.


  ![Skärm bild som visar var du kan hämta en kopia av åtkomst nyckeln.](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Kopiera URL: en till den återställda Cosmos DB och uppdatera den i den nya FarmBeats Datahub App Service-konfigurationen. Nu kan du ta bort Cosmos DB-kontot i den nya distributionen av FarmBeats.

  ![Skärm bild som visar var du kopierar URL: en för återställda Cosmos DB.](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Konfigurera lagrings konto

Kopiera åtkomst nyckeln för det återställda lagrings kontot och uppdatera det i den nya FarmBeats Datahub-Key Vault.

![Skärm bild som visar var du kopierar åtkomst nyckeln för det återställda lagrings kontot.](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Se till att uppdatera lagrings konto namnet i den nya FarmBeats batch VM config-filen.

![Haveriberedskap](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Om du har aktiverat Data återställning för ditt Accelerator Storage-konto följer du stegen i steg 2 för att uppdatera åtkomst nyckeln och namnet för Accelerator Storage-kontot i den nya FarmBeats-instansen.
