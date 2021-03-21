---
title: Viktiga skillnader för Machine Learning Services
description: I den här artikeln beskrivs viktiga skillnader mellan Machine Learning Services i Azure SQL-hanterad instans och SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599552"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Viktiga skillnader medllan Machine Learning Services i Azure SQL Managed Instance och SQL Server

I den här artikeln beskrivs några viktiga skillnader i funktionaliteten mellan [Machine Learning Services i Azure SQL-hanterad instans](machine-learning-services-overview.md) och [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning).

## <a name="language-support"></a>Stöd för språk

Machine Learning Services i både SQL-hanterad instans och SQL Server stödja [utöknings ramverket](/sql/machine-learning/concepts/extensibility-framework)python och R. Viktiga skillnader i SQL-hanterad instans är:

- Endast Python och R stöds. Det går inte att lägga till externa språk som Java.

- De ursprungliga versionerna av python och R är olika:

  | Plattform                   | Python runtime-version           | R runtime-versioner                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Azure SQL Managed Instance | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 och 3.7.2 (CU22 och senare) | 3.3.3 och 3.5.2 (CU22 och senare)     |
  | SQL Server 2016            | Inte tillgängligt                    | 3.2.2 och 3.5.2 (SP2 CU14 och senare) |

## <a name="python-and-r-packages"></a>Python-och R-paket

Det finns inget stöd för SQL-hanterade instanser för paket som är beroende av externa körningar (t. ex. Java) eller som behöver åtkomst till OS-API: er för installation eller användning.

Mer information om hur du hanterar python-och R-paket finns i:

- [Hämta information om Python-paket](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Hämta information om R-paket](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Resursstyrning

I SQL-hanterad instans går det inte att begränsa R-resurser via [Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true), och externa resurspooler stöds inte.

Som standard är R-resurser inställda på högst 20% av de tillgängliga SQL-hanterade instans resurserna när utöknings barhet är aktiverat. Om du vill ändra denna standard procents ATS skapar du ett support ärende för Azure på [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

Utöknings barhet är aktiverat med följande SQL-kommandon (SQL Managed instance startas om och är inte tillgängligt i några sekunder):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Om du vill inaktivera utöknings barhet och återställa 100% av minnes-och processor resurser till SQL Server använder du följande kommandon:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

De totala resurserna som är tillgängliga för SQL-hanterad instans beror på vilken tjänst nivå du väljer. Mer information finns i [Azure SQL Database inköps modeller](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Otillräckligt minnes fel

Minnesanvändningen beror på hur mycket som används i R-skripten och hur många frågor som körs parallellt. Om det inte finns tillräckligt med minne tillgängligt för R får du ett fel meddelande. Vanliga fel meddelanden är:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Om du får ett av dessa fel kan du lösa det genom att skala databasen till en högre tjänst nivå.

## <a name="sql-managed-instance-pools"></a>SQL-hanterade instans pooler

Machine Learning Services stöds för närvarande inte på [Azure SQL Managed instance Pools (för hands version)](instance-pools-overview.md).

## <a name="next-steps"></a>Nästa steg

- Se översikten [Machine Learning Services i Azure SQL-hanterad instans](machine-learning-services-overview.md).
- Information om hur du använder python i Machine Learning Services finns i [köra Python-skript](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Information om hur du använder R i Machine Learning Services finns i [Kör R-skript](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
