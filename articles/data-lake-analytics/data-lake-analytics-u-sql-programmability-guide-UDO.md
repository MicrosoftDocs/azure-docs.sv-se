---
title: Programmerings guide för U-SQL UDO för Azure Data Lake
description: Läs om U-SQL-UDO programmerings Azure Data Lake Analytics för att du ska kunna skapa ett lämpligt USQL-skript.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512656"
---
# <a name="u-sql-user-defined-objects-overview"></a>Översikt över U-SQL-användardefinierade objekt


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: användardefinierade objekt: UDO
Med U-SQL kan du definiera anpassade programmerings objekt, som kallas användardefinierade objekt eller UDO.

Följande är en lista över UDO i U-SQL:

* Användardefinierade Extraherare
    * Extrahera rad per rad
    * Används för att implementera data extrahering från anpassade strukturerade filer

* Användardefinierade utsparatillfil
    * Mata ut rad per rad
    * Används för att mata ut anpassade data typer eller anpassade fil format

* Användardefinierade processorer
    * Ta en rad och skapa en rad
    * Används för att minska antalet kolumner eller skapa nya kolumner med värden som härleds från en befintlig kolumn uppsättning

* Användardefinierade appliers
    * Ta en rad och producera 0 till n rader
    * Används med yttre/kors koppling

* Användardefinierade kombinations
    * Kombinerar rad uppsättningar – användardefinierade kopplingar

* Användardefinierade reducerare
    * Ta n rader och skapa en rad
    * Används för att minska antalet rader

UDO anropas vanligt vis explicit i U-SQL-skript som en del av följande U-SQL-uttryck:

* EXTRACT
* UTDATAPARAMETRAR
* UPPGRADERINGEN
* KOMBINERA
* MINSKA

> [!NOTE]  
> UDO är begränsade till att förbruka 0,5 GB minne.  Den här minnes begränsningen gäller inte för lokala körningar.

## <a name="next-steps"></a>Nästa steg
* [Guide för U-SQL-programmering – översikt](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programmerings guide – UDT och UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)