---
title: Använda SQL Database DACPAC och BACPAC-paket – Azure SQL Edge
description: Lär dig mer om att använda DACPACs och BacPacs i Azure SQL Edge
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392256"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Database DACPAC-och BACPAC-paket i SQL Edge

Azure SQL Edge är en optimerad relationsdatabasmotor som är avsedd för IoT- och Edge-distributioner. Den bygger på de senaste versionerna av Microsoft SQL Database-motorn, som tillhandahåller branschledande prestanda-, säkerhets-och fråge bearbetnings funktioner. Tillsammans med de branschledande Relations databas hanterings funktionerna i SQL Server ger Azure SQL Edge inbyggd strömnings kapacitet för analys i real tid och komplex händelse bearbetning.

Azure SQL Edge tillhandahåller inbyggd mekanism som gör att du kan distribuera ett [SQL Database DACPAC-och BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) -paket under eller efter distributionen av SQL Edge.

SQL Database DACPAC-och BACPAC-paket kan distribueras till SQL Edge med `MSSQL_PACKAGE` miljövariabeln. Miljö variabeln kan konfigureras med något av följande.  
- En lokal mapplats i SQL-behållaren som innehåller DACPAC-och BACPAC-filerna. Den här mappen kan mappas till en värd volym med hjälp av antingen monterings punkter eller data volym behållare. 
- En lokal fil Sök väg inom SQL container-mappningen till DACPAC eller BACPAC-filen. Den här fil Sök vägen kan mappas till en värd volym med hjälp av antingen monterings punkter eller data volym behållare. 
- En lokal fil Sök väg inom SQL container-mappningen till en zip-fil som innehåller DACPAC-eller BACPAC-filerna. Den här fil Sök vägen kan mappas till en värd volym med hjälp av antingen monterings punkter eller data volym behållare. 
- En Azure Blob SAS-URL till en zip-fil som innehåller filerna DACPAC och BACPAC.
- En Azure Blob SAS-URL till en DACPAC-eller BACPAC-fil. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Använda ett SQL Database DAC-paket med SQL Edge

Om du vill distribuera (eller importera) ett SQL Database DAC-paket `(*.dacpac)` eller en BACPAC `(*.bacpac)` -fil med Azure Blob Storage och en zip-fil följer du stegen nedan. 

1. Skapa/extrahera ett DAC-paket eller exportera en BACPAC-fil med hjälp av mekanismen nedan. 
    - Skapa eller extrahera ett SQL Database DAC-paket. Mer information om hur du skapar ett DAC-paket för en befintlig SQL Server databas finns i [extrahera en DAC från en databas](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) .
    - Exportera ett distribuerat DAC-paket eller en databas. Se [Exportera ett data skikts program](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) för information om hur du skapar en BACPAC-fil för en befintlig SQL Server databas.

2. Zip- `*.dacpac` eller `*.bacpac` File-filen och ladda upp den till ett Azure Blob Storage-konto. Mer information om hur du laddar upp filer till Azure Blob Storage finns i [Ladda upp, ladda ned och lista blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generera en signatur för delad åtkomst för zip-filen med hjälp av Azure Portal. Mer information finns i [Delegera åtkomst med signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).

4. Uppdatera konfigurationen av SQL Edge-modulen för att inkludera URI för delad åtkomst för DAC-paketet. Gör så här för att uppdatera SQL Edge-modulen:

    1. I Azure Portal går du till din IoT Hub-distribution.

    2. I den vänstra rutan väljer du **IoT Edge**.

    3. På sidan **IoT Edge** söker du efter och väljer den IoT Edge där SQL Edge-modulen har distribuerats.

    4. På sidan **IoT Edge enhets** enhet väljer du **ange modul**.

    5. På sidan **Ange moduler** och klicka på Azure SQL Edge-modulen.

    6. I fönstret **uppdatera IoT Edge-modulen** väljer du **miljövariabler**. Lägg till `MSSQL_PACKAGE` miljövariabeln och ange SAS-URL: en som genererades i steg 3 ovan som värde för miljövariabeln. 

    7. Välj **Uppdatera**.

    8. På sidan **Ange moduler** väljer du **Granska + skapa**.

    9. På sidan **Ange moduler** väljer du **skapa**.

5. Efter uppdateringen laddas paketfilerna ned, zippas upp och distribueras mot SQL Edge-instansen.

Vid varje omstart av Azure SQL Edge-behållaren försöker SQL Edge hämta det zippade fil paketet och utvärdera för ändringar. Om en ny version av DACPAC-filen påträffas distribueras ändringarna till databasen i SQL Edge.

## <a name="known-issue"></a>Känt problem

Under vissa DACPAC-eller BACPAC-distributioner kan användare stöta på ett kommando-timeout, vilket leder till att DACPAC distributions åtgärden Miss lyckas. Om det här problemet uppstår använder du SQLPackage.exe (eller SQL-klient verktyg) för att tillämpa DACPAC-eller BACPAC-som. 

## <a name="next-steps"></a>Nästa steg

- [Distribuera SQL Edge via Azure Portal](deploy-portal.md).
- [Strömma data](stream-data.md)
- [Machine Learning och AI med ONNX i SQL Edge](onnx-overview.md)