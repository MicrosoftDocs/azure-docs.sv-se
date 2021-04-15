---
title: Distribuera Azure Monitor för SAP-lösningar med Azure Portal
description: Distribuera Azure Monitor för SAP-lösningar med Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 096f0425a6893d68341b97c821481fa0adf2f95c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375278"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Distribuera Azure Monitor för SAP-lösningar med Azure Portal

Azure Monitor för SAP-lösningar resurser kan skapas via [Azure Portal](https://azure.microsoft.com/features/azure-portal). Den här metoden tillhandahåller ett webbläsarbaserat användargränssnitt för att distribuera Azure Monitor för SAP-lösningar och konfigurera providers.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på Azure-portalen på https://portal.azure.com

## <a name="create-monitoring-resource"></a>Skapa övervakningsresurs

1. Välj **Azure Monitor för SAP-lösningar** från **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Bild som visar hur du väljer Azure Monitor för SAP-lösningar från Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. På **fliken Grundläggande anger** du de värden som krävs. Om det är tillämpligt kan du använda en befintlig Log Analytics-arbetsyta.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Visning av Azure Portal konfigurationsalternativ." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. När du väljer ett virtuellt nätverk ser du till att de system som du vill övervaka kan nås från det virtuella nätverket. 

   > [!IMPORTANT]
   > Genom **att** välja Dela för datadelning med Microsoft kan våra supportteam ge ytterligare support.

## <a name="configure-providers"></a>Konfigurera providers

### <a name="sap-hana-provider"></a>SAP HANA provider 

1. Välj fliken **Provider** för att lägga till de leverantörer som du vill konfigurera. Du kan lägga till flera providers en efter en eller lägga till dem när du har distribuerat övervakningsresursen. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Visar providerfliken för att lägga till ytterligare leverantörer i Azure Monitor för SAP-lösningar." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Välj **Lägg till** provider och **SAP HANA** i listrutan. 

   > [!IMPORTANT]
   > Se till SAP HANA providern är konfigurerad för SAP HANA "master"-nod.

3. Ange den privata IP-adressen för HANA-servern.

4. Ange namnet på den databasklient som du vill använda. Du kan välja vilken klientorganisation som helst, men vi rekommenderar att du använder **SYSTEMDB** eftersom det möjliggör ett bredare utbud av övervakningsområden. 

5. Ange det SQL-portnummer som är associerat med HANA-databasen. Portnumret ska ha formatet **[3]**  +  **[instance#]**  +  **[13]**. Till exempel 30013. 

6. Ange det databas-användarnamn som du vill använda. Se till att databasanvändaren har **tilldelats** **läsroller för** övervakning och katalog. 

7. När du är klar väljer du **Lägg till provider.** Fortsätt att lägga till fler leverantörer efter behov eller välj **Granska + skapa** för att slutföra distributionen.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Bild av konfigurationsalternativ när du lägger till providerinformation." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Leverantör av kluster med hög tillgänglighet (Pacemaker)

1. Välj **Kluster med hög tillgänglighet (Pacemaker)** i listrutan. 

   > [!IMPORTANT]
   > Om du vill konfigurera providern för kluster med hög tillgänglighet (Pacemaker) måste ha_cluster_provider installeras i varje nod. Mer information finns i [Exporter av HA-kluster](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Ange Prometheus-slutpunkten i form av http://IP:9664/metrics . 
 
3. Ange system-ID (SID), värdnamn och klusternamn.

4. När du är klar väljer du **Lägg till provider.** Fortsätt att lägga till fler leverantörer efter behov eller välj **Granska + skapa** för att slutföra distributionen.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Bild som visar alternativ relaterade till PROVIDERn pacemaker för HA-klustret." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>OS-provider (Linux) 

1. Välj OS (Linux) i listrutan 

>[!IMPORTANT]
> Om du vill konfigurera OS-providern (Linux) måste du se till att den senaste versionen av Node_Exporter är installerad på varje värd (BareMetal eller VM) som du vill övervaka. Använd den här [länk] ( https://prometheus.io/download/#node_exporter) för att hitta den senaste versionen. Mer information finns i [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Ange ett namn som ska vara identifieraren för BareMetal-instansen.
3. Ange Node Exporter-slutpunkten i form av http://IP:9100/metrics .

>[!IMPORTANT]
> Använd den privata IP-adressen för Linux-värden. Kontrollera att värden och AMS-resursen finns i samma virtuella nätverk. 

>[!Note]
> Brandväggsporten "9100" ska vara öppen på Linux-värden.
>Om du använder firewall-cmd: firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload Om du använder ufw: ufw tillåt 9100/tcp ufw reload

>[!Tip]
> Om Linux-värden är en virtuell Azure-dator ser du till att alla tillämpliga NSG:er tillåter inkommande trafik på port 9100 från "VirtualNetwork" som källa.
 
5. När du är klar väljer du **Lägg till provider.** Fortsätt att lägga till fler leverantörer efter behov eller välj **Granska + skapa** för att slutföra   distributionen. 


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server provider

1. Innan du lägger Microsoft SQL Server-providern bör du köra följande skript i SQL Server Management Studio för att skapa en användare med de behörigheter som krävs för att konfigurera providern.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Välj **Lägg till** provider och **Microsoft SQL Server** i listrutan. 

3. Fyll i fälten med hjälp av information som är associerad med Microsoft SQL Server. 

4. När du är klar väljer du **Lägg till provider.** Fortsätt att lägga till fler leverantörer efter behov eller välj **Granska + skapa** för att slutföra distributionen.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Bild som visar information om att lägga Microsoft SQL Server providern." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="sap-netweaver-provider"></a>SAP NetWeaver-provider

#### <a name="pre-requisites-for-adding-netweaver-provider"></a>Krav för att lägga till NetWeaver-providern

"SAP start service" tillhandahåller en mängd tjänster, inklusive övervakning av SAP-systemet. Vi använder "SAPControl", som är ett SOAP-webbtjänstgränssnitt som exponerar dessa funktioner. Det här SAPControl-webbtjänstgränssnittet skiljer [mellan skyddade och oskyddade](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) webbtjänstmetoder. För att kunna hämta specifika mått måste du ta bort skyddet från vissa metoder. Om du vill ta bort skyddet från de metoder som krävs för den aktuella versionen följer du stegen nedan för varje SAP-system:

1. Öppna en SAP GUI-anslutning till SAP-servern
2. Logga in med ett administratörskonto
3. Köra transaktions-RZ10
4. Välj lämplig profil (STANDARD. PFL)
5. Välj Utökat underhåll och klicka på Ändra 
6. Ändra värdet för den berörda parametern "service/protectedwebmethods" till "SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList" till den rekommenderade inställningen och klicka på Kopiera
7. Gå tillbaka och välj >Spara
8. Starta om systemet för att parametern ska börja gälla

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>Installera NetWeaver-providern på Azure Portal
1.  Kontrollera att de nödvändiga stegen har slutförts och att servern har startats om
2.  På Azure Portal väljer du Lägg till provider under AMS och väljer SAP NetWeaver i listrutan
3.  Ange värdnamnet för SAP-systemet och underdomänen (om tillämpligt)
4.  Ange instansnumret som motsvarar det angivna värdnamnet 
5.  Ange system-ID (SID)
6.  När du är klar väljer du Lägg till provider
7.  Fortsätt att lägga till ytterligare leverantörer efter behov eller välj Granska + skapa för att slutföra distributionen

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Monitor för SAP-lösningar](azure-monitor-overview.md)
