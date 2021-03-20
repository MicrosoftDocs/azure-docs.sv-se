---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9c734ff03b1cf277c7e0967d8b76b1941434f414
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050436"
---
I den här proceduren kommer du att:

1. [Förbered för att köra den körbara filen i underhållaren](#to-prepare-to-run-the-maintainer) .
2. [Förbered innehålls databasen och pappers korgen för omedelbar borttagning av överblivna blobbar](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Kör Maintainer.exe](#to-run-the-maintainer).
4. [Återställ inställningarna för innehålls databasen och pappers korgen](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Förbereda för att köra underhållaren
1. Öppna hanterings gränssnittet för SharePoint 2013 på front webb servern som administratör.
2. Navigera till mappen *Boot Drive*: \Program\microsoft SQL Remote Blob Storage 10.50 \ kvarhållare\.
3. Byt namn på **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** till **web.config**.
4. Använd `aspnet_regiis -pdf connectionStrings` för att dekryptera web.configs filen.
5. I den dekrypterade web.config-filen, under `connectionStrings` noden, lägger du till anslutnings strängen för din SQL Server-instans och innehålls databasens namn. Se följande exempel.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Använd `aspnet_regiis –pef connectionStrings` för att kryptera om web.configs filen. 
7. Byt namn på web.config till Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Så här förbereder du innehålls databasen och pappers korgen för att omedelbart ta bort överblivna BLOBBAR
1. Kör följande uppdaterings frågor för mål innehålls databasen i SQL Server i SQL Management Studio: 

    `use WSS_Content`

    `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

    `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. På front webb servern, under **Central administration**, redigerar du de **allmänna inställningarna för webb programmet** för den önskade innehålls databasen för att tillfälligt inaktivera pappers korgen. Den här åtgärden tömmer också pappers korgen för alla relaterade webbplats samlingar. Det gör du genom att klicka på hantering av **centrala administrations**  ->  **program** för  ->  **webb program (hantera webb program)**  ->  **SharePoint-80**  ->  **allmänna program inställningar**. Ange **statusen för pappers korgen** till **av**.
   
    ![Allmänna inställningar för webb program](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Köra underhållaren
* På front webb servern i hanterings gränssnittet för SharePoint 2013 kör du underhållaren enligt följande:
  
    `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Endast `GarbageCollection` åtgärden stöds för StorSimple för tillfället. Observera också att parametrarna som utfärdas för Microsoft.Data.SqlRemoteBlobs.Maintainer.exe är Skift läges känsliga. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Återställa inställningarna för innehålls databasen och pappers korgen
1. Kör följande uppdaterings frågor för mål innehålls databasen i SQL Server i SQL Management Studio:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. På front webb servern, i **Central administration**, redigerar du de **allmänna inställningarna för webb programmet** för den önskade innehålls databasen för att återaktivera pappers korgen. Det gör du genom att klicka på hantering av **centrala administrations**  ->  **program** för  ->  **webb program (hantera webb program)**  ->  **SharePoint-80**  ->  **allmänna program inställningar**. Ange statusen för pappers korgen till **på**.

