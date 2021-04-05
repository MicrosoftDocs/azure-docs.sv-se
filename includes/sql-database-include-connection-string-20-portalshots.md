---
title: Hämta anslutnings strängen från Azure Portal
description: Hämta anslutnings strängen från Azure Portal
keywords: SQL-anslutning, anslutnings sträng
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: b06004143d4a1ec5a2808e21b1f78833e410c195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557410"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Hämta anslutnings strängen från Azure Portal
Använd [Azure Portal](https://portal.azure.com/) för att hämta anslutnings strängen som krävs för att ditt klient program ska interagera med Azure SQL Database.

1. Välj **alla tjänsters**  >  **SQL-databaser**.

2. Ange namnet på databasen i text rutan filter längst upp till vänster i bladet SQL- **databaser** .

3. Välj raden för din databas.

4. När bladet har visats för din databas väljer du alternativet **minimera** för att dölja de blad som du använde för bläddring och databas filtrering för visuell bekvämlighet.

5. På bladet för din databas väljer du **Visa databas anslutnings strängar**.

6. Kopiera lämplig anslutnings sträng. dvs. om du tänker använda anslutnings biblioteket för ADO.NET kopierar du lämplig sträng från fliken **ADO.net** .

    ![Kopiera ADO-anslutningssträngen för databasen][20-CopyAdoConnectionString]

7. Redigera anslutnings strängen efter behov. t. ex. sätta ditt lösen ord i anslutnings strängen eller ta bort @ &lt; servername &gt; från användar namnet om användar namnet eller Server namnet är för långt.

8. I ett format eller ett annat klistrar du in anslutnings Strängs informationen i klient program koden.

Mer information finns i [anslutnings strängar och konfigurationsfiler](/dotnet/framework/data/adonet/connection-strings-and-configuration-files).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->