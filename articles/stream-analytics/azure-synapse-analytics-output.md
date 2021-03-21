---
title: Azure Synapse Analytics-utdata från Azure Stream Analytics
description: I den här artikeln beskrivs Azure Synapse Analytics som utdata för Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 7e85df8ae67624a253a9fb617629d7355109c210
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019609"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Azure Synapse Analytics-utdata från Azure Stream Analytics

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) är en obegränsad analys tjänst som sammanför företags data lager hantering och stor data analys. 

Azure Stream Analytics jobb kan matas ut till en dedikerad SQL-adresspool i Azure Synapse Analytics och kan bearbeta data flödes hastigheter upp till 200 MB/s. Detta stöder de mest krävande real tids analys och frekventa data bearbetnings behov för arbets belastningar som rapportering och instrument paneler.  

Tabellen för den dedikerade SQL-poolen måste finnas innan du kan lägga till den som utdata till ditt Stream Analytics-jobb. Tabellens schema måste matcha fälten och deras typer i jobbets utdata. 

Om du vill använda Azure-Synapse som utdata måste du kontrol lera att lagrings kontot har kon figurer ATS. Gå till lagrings konto inställningarna för att konfigurera lagrings kontot. Endast de lagrings konto typer som stöder tabeller är tillåtna: General-Purpose v2 och General-Purpose v1. Välj endast standard nivå. Premium-nivån stöds inte.

## <a name="output-configuration"></a>Konfiguration av utdata

I följande tabell visas egenskaps namn och beskrivningar för att skapa am Azure Synapse Analytics-utdata.

|Egenskapsnamn|Beskrivning|
|-|-|
|Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet till den här databasen. |
|Databas |ett dedikerat SQL-poolnamn där du skickar dina utdata. |
|Servernamn |Namn på Azure Synapse-Server.  |
|Användarnamn |Det användar namn som har Skriv behörighet till databasen. Stream Analytics stöder endast SQL-autentisering. |
|Lösenord |Lösen ordet för att ansluta till databasen. |
|Tabell  | Tabell namnet där utdata skrivs. Tabell namnet är Skift läges känsligt. Schemat för den här tabellen ska exakt matcha det antal fält och deras typer som jobbets utdata genererar.|

## <a name="next-steps"></a>Nästa steg

* [Använda hanterade identiteter för att få åtkomst till Azure SQL Database eller Azure Synapse Analytics från ett Azure Stream Analytics jobb (förhands granskning)](sql-database-output-managed-identity.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)