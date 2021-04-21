---
title: Hanterade anslutningsappar för Azure Logic Apps
description: Använd Microsoft-hanterade utlösare och åtgärder för att skapa automatiserade arbetsflöden som integrerar andra appar, data, tjänster och system med hjälp av Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: d50cf1009fb674f2d3d5914715dcbd9699565489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797181"
---
# <a name="managed-connectors-for-logic-apps"></a>Hanterade anslutningsappar för Logic Apps

[Hanterade anslutningsappar](apis-list.md) ger dig sätt att komma åt andra tjänster och system [där inbyggda utlösare och åtgärder](built-in.md) inte är tillgängliga. Du kan använda dessa utlösare och åtgärder för att skapa arbetsflöden som integrerar data, appar, molnbaserade tjänster och lokala system. Jämfört med inbyggda utlösare och åtgärder är dessa anslutningsappar vanligtvis knutna till en specifik tjänst eller ett specifikt system, till exempel Azure Blob Storage-, Office 365-, SQL-, Salesforce- eller SFTP-servrar. Hanterade anslutningsappar hanteras av Microsoft och hanteras i Azure och kräver vanligtvis att du först skapar en anslutning från arbetsflödet och autentiserar din identitet. Både upprepningsbaserade och webhook-baserade utlösare är tillgängliga, så om du använder en upprepningsbaserad utlösare kan du läsa Översikt över [upprepningsbeteende.](apis-list.md#recurrence-behavior)

För några tjänster, system och protokoll, till exempel Azure Service Bus, Azure Functions, SQL, AS2 och så vidare, tillhandahåller Logic Apps även inbyggda versioner. Antalet och intervallet varierar beroende på om du skapar en logikapp för flera innehavare eller en logikapp för en enskild klientorganisation. I några få fall är både en inbyggd version och en version av en hanterad anslutningsapp tillgänglig. I de flesta fall ger den inbyggda versionen bättre prestanda, funktioner, priser och så vidare. Om du till exempel vill utbyta [B2B-meddelanden](../logic-apps/logic-apps-enterprise-integration-as2.md)med as2-protokollet väljer du den inbyggda versionen om du inte behöver spårningsfunktioner, som endast är tillgängliga i den (inaktuella) versionen av den hanterade anslutningsappen.

Vissa hanterade anslutningsappar Logic Apps tillhör flera underkategorier. SAP-anslutningsappen är till exempel både en [företagsanslutning](#enterprise-connectors) och [en lokal anslutningsapp.](#on-premises-connectors)

* [Standardanslutningsappar](#standard-connectors) ger åtkomst till tjänster som Azure Blob Storage, Office 365, SharePoint, Salesforce, Power BI, OneDrive och många fler.
* [Lokala anslutningsappar ger](#on-premises-connectors) åtkomst till lokala system, till exempel SQL Server, SharePoint Server, SAP, Oracle DB, filresurser med mera.
* [Anslutningsappar för integrationskonto](#integration-account-connectors) hjälper dig att omvandla och verifiera XML, koda och avkoda flata filer och bearbeta B2B-meddelanden (business-to-business) med hjälp av PROTOKOLLEN AS2, EDIFACT och X12. 

## <a name="standard-connectors"></a>Anslutningsappar av standardtyp

Azure Logic Apps tillhandahåller dessa populära Standard-anslutningsappar för att skapa automatiserade arbetsflöden med hjälp av dessa tjänster och system. Vissa standardanslutningsappar stöder [även lokala system eller](#on-premises-connectors) [integrationskonton.](#integration-account-connectors)

Vissa Logic Apps Standard-anslutningsappar [stöder lokala system eller](#on-premises-connectors) [integrationskonton.](#integration-account-connectors)

:::row:::
    :::column:::
        [![Azure Service Bus för hanterad anslutningsapp i Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        Hantera asynkrona meddelanden, sessioner och ämnesprenumerationer med det anslutningsprogram som oftast används i Logic Apps.
    :::column-end:::
    :::column:::
        [![SQL Server för hanterad anslutningsapp i Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        Anslut till SQL Server lokalt eller en Azure SQL Database i molnet så att du kan hantera poster, köra lagrade procedurer eller köra frågor.
    :::column-end:::
    :::column:::
        [![Ikon för Azure Blog Storage-hanterad anslutningsapp i Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blog Storage**][azure-blob-storage-doc]
        \
        \
        Anslut till ditt Azure Storage konto så att du kan skapa och hantera blobinnehåll.
    :::column-end:::
    :::column:::
        [![Ikon för Office 365 Outlook-hanterad anslutningsapp i Logic Apps][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        Anslut till ditt e-postkonto för arbetet eller skolan så att du kan skapa och hantera e-post, uppgifter, kalenderhändelser och möten, kontakter, begäranden med mera.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikon för STFP-SSH-hanterad anslutningsapp i Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        Anslut till SFTP-servrar som du kan komma åt från Internet med SSH så att du kan arbeta med dina filer och mappar.
    :::column-end:::
    :::column:::
        [![Ikon för SharePoint Online-hanterad anslutningsapp i Logic Apps][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Anslut till SharePoint Online så att du kan hantera filer, bilagor, mappar med mera.
    :::column-end:::
    :::column:::
        [![Ikon för hanterad anslutningsapp i Azure Queues Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure-köer**][azure-queues-doc]
        \
        \
        Anslut till ditt Azure Storage-konto så att du kan skapa och hantera köer och meddelanden.
    :::column-end:::
    :::column:::
        [![Ikon för FTP-hanterad anslutningsapp i Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**Ftp**][ftp-doc]
        \
        \
        Anslut till FTP-servrar som du kan komma åt från Internet så att du kan arbeta med dina filer och mappar.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikon för filsystem för hanterad anslutningsapp i Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Filsystem**][file-system-doc]
        \
        \
        Anslut till din lokala filresurs så att du kan skapa och hantera filer.
    :::column-end:::
    :::column:::
        [![Azure Event Hubs för hanterad anslutningsapp i Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs**][azure-event-hubs-doc]
        \
        \
        Använda och publicera händelser i en Event Hub. Du kan till exempel hämta utdata från din logikapp med Event Hubs och sedan skicka dem till en leverantör av realtidsanalys.
    :::column-end:::
    :::column:::
        [![Azure Event Grid för hanterad anslutningsapp i Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Övervaka händelser som publiceras av Event Grid, till exempel när Azure-resurser eller resurser från tredje part ändras.
    :::column-end:::
    :::column:::
        [![Ikon för salesforce-hanterad anslutningsapp i Logic Apps][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Anslut till ditt Salesforce-konto så att du kan skapa och hantera objekt som poster, jobb, objekt med mera.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>Lokala anslutningsappar

Innan du kan skapa en anslutning till ett lokalt system måste du först ladda ned, installera och konfigurera [en lokal datagateway][gateway-doc]. Den här gatewayen tillhandahåller en säker kommunikationskanal utan att behöva konfigurera den nödvändiga nätverksinfrastrukturen. 

Följande anslutningsappar är några vanliga [Standard-anslutningsappar](#standard-connectors) som Logic Apps tillhandahåller för åtkomst till data och resurser i lokala system. Listan över lokala anslutningsappar finns i [Datakällor som stöds.](../logic-apps/logic-apps-gateway-connection.md#supported-connections)

:::row:::
    :::column:::
        [![Ikonen för lokal Biztalk Server-anslutning i Logic Apps][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Ikon för lokal filsystemanslutning i Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Filsystem**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Ikon för lokal IBM Db2-anslutning i Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM Db2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Ikon för lokal IBM Informix-anslutningsapp i Logic Apps][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikon för lokal MySQL-anslutningsapp i Logic Apps][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB lokal anslutningsikon i Logic Apps][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Ikonen för den lokala PostgreSQL-anslutningsappen i Logic Apps][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**Postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Ikon för lokal SharePoint Server-anslutning i Logic Apps][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server lokal anslutningsikon i Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Ikonen för teradata lokalt anslutning i Logic Apps][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>Anslutningar för integrationskonton

Anslutningsappar för integrationskonto har specifikt [stöd för B2B-kommunikationsscenarier (business-to-business)](../logic-apps/logic-apps-enterprise-integration-overview.md) i Azure Logic Apps. När du har skapat ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och definierat dina B2B-artefakter, till exempel handelspartner, avtal, kartor och scheman, kan du använda integrationskontoanslutningsappar för att koda och avkoda meddelanden, transformera innehåll med mera.

Om du till exempel använder Microsoft BizTalk Server kan du skapa en anslutning från arbetsflödet med hjälp [BizTalk Server lokala anslutningsappen](#on-premises-connectors). Du kan sedan utöka eller utföra BizTalk-liknande åtgärder i arbetsflödet med hjälp av dessa anslutningsappar för integrationskonto.

> [!NOTE]
> Innan du kan använda anslutningsappar för integrationskonto måste du [länka logikappen till ett integrationskonto.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)


:::row:::
    :::column:::
        [![As2-avkodningsåtgärdsikon i Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2-avkodning**][as2-doc]
    :::column-end:::
    :::column:::
        [![Ikon för AS2-kodningsåtgärd i Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2-kodning**][as2-doc]
    :::column-end:::
    :::column:::
        [![Ikon för EDIFACT-avkodning i Logic Apps][edifact-icon]][edifact-decode-doc]
        \
        \
        [**EDIFACT-avkodning**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Ikon för EDIFACT-kodningsåtgärd i Logic Apps][edifact-icon]][edifact-encode-doc]
        \
        \
        [**EDIFACT-kodning**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Åtgärdsikon för flat filavkodning i Logic Apps][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Avkodning av flat fil**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Åtgärdsikon för flat filkodning i Logic Apps][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Flat filkodning**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Åtgärdsikon för integrationskonto i Logic Apps][integration-account-icon]][integration-account-doc]
        \
        \
        [**Integrationskonto**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Åtgärdsikonen Liquid transforms i Logic Apps][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Liquid-transformeringar**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikon för X12-avkodningsåtgärd i Logic Apps][x12-icon]][x12-decode-doc]
        \
        \
        [**X12-avkodning**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Ikon för X12-kodningsåtgärd i Logic Apps][x12-icon]][x12-encode-doc]
        \
        \
        [**X12-kodning**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Åtgärdsikonen XML-transformering i Logic Apps][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**XML-transformeringar**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Ikon för XML-verifieringsåtgärd i Logic Apps][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML-verifiering**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Enterprise-anslutningsappar

Följande anslutningsappar ger åtkomst till företagssystem till en extra kostnad:

:::row:::
    :::column:::
        [![Ikon för IBM 3270 Enterprise-anslutning i Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** Enterprise-anslutningsapp][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Ikon för IBM MQ Enterprise-anslutning i Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** Enterprise-anslutningsapp][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Ikon för SAP Enterprise-anslutningsapp i Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Enterprise-anslutningsapp][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>ISE-anslutningsappar

I en integrationstjänstmiljö (ISE) har dessa hanterade anslutningsappar också [ISE-versioner](apis-list.md#ise-and-connectors), som har andra funktioner än deras versioner för flera innehavare:

> [!NOTE]
> Logikappar som körs i en ISE och deras anslutningsappar, oavsett var anslutningsapparna körs, följer en fast prisplan jämfört med den förbrukningsbaserade prisplanen. Mer information finns i [Logic Apps prismodell och](../logic-apps/logic-apps-pricing.md) [Logic Apps prisinformation.](https://azure.microsoft.com/pricing/details/logic-apps/)

:::row:::
    :::column:::
        [![Ikon för AS2 ISE-anslutning i Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2** Ise][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation ise-anslutningsikonen i Logic Apps][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** Ise][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob Storage ise-anslutningsikonen i Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob Storage** Ise][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB ise-anslutningsikonen i Logic Apps][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** Ise][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs ise-anslutningsikonen i Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs** Ise][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ise-anslutningsikonen i Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** Ise][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Ikon för Azure File Storage ISE-anslutning i Logic Apps][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure File Storage** Ise][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault ISE-anslutningsikon i Logic Apps][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** Ise][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor loggikonen för ISE-anslutning i Logic Apps][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Azure Monitor loggar** Ise][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure Service Bus ise-anslutningsikon i Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** Ise][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE-anslutningsikon i Logic Apps][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** Ise][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Ikon för Azure Table Storage ISE-anslutning i Logic Apps][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Azure Table Storage** Ise][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikonen för ISE-anslutningsappen i Azure Queues Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure-köer** Ise][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Ikon för EDIFACT ISE-anslutning i Logic Apps][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** Ise][edifact-doc]
    :::column-end:::
    :::column:::
        [![Ikon för FILSYSTEM ISE-anslutning i Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Filsystem** Ise][file-system-doc]
    :::column-end:::
    :::column:::
        [![Ikon för FTP ISE-anslutning i Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** Ise][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![Ikon för IBM 3270 ISE-anslutning i Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** Ise][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Ikon för IBM DB2 ISE-anslutning i Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** Ise][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Ikon för IBM MQ ISE-anslutning i Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** Ise][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Ikon för SAP ISE-anslutning i Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Ise][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikon för SFTP-SSH ISE-anslutning i Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** Ise][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Ikon för SMTP ISE-anslutning i Logic Apps][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** Ise][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ise-anslutningsikonen i Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** Ise][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Ikon för X12 ISE-anslutning i Logic Apps][x12-icon]][x12-doc]
        \
        \
        [**X12** Ise][x12-doc]
    :::column-end:::
:::row-end:::

Mer information finns i de här ämnena:

* [Åtkomst till virtuella Azure-nätverksresurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Prissättningsmodell för Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa anpassade API:er som du kan anropa från Logic Apps](/logic-apps/logic-apps-create-api-app)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png


<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Skapa och hantera automatiseringsjobb för din molnbaserade och lokala infrastruktur"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Hantera filer i blobcontainern med Azure Blob Storage Connector"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Anslut till Azure Cosmos DB så att du kan komma åt dokument och lagrade procedurer"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Övervaka händelser som publiceras av en Event Grid, till exempel när Azure-resurser eller resurser från tredje part ändras"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Anslut till Azure Event Hubs så att du kan ta emot och skicka händelser mellan logikappar och Event Hubs"
[azure-file-storage-doc]: /connectors/azurefile/ "Anslut till ditt Azure Storage-konto så att du kan skapa, uppdatera, hämta och ta bort filer"
[azure-key-vault-doc]: /connectors/keyvault/ "Anslut till Azure Key Vault så att du kan hantera dina hemligheter och nycklar"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Köra frågor mot Azure Monitor loggar över Log Analytics-arbetsytor och Application Insights komponenter"
[azure-queues-doc]: /connectors/azurequeues/ "Anslut till ditt Azure Storage konto så att du kan skapa och hantera köer och meddelanden"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Skicka meddelanden från Service Bus-köer och Service Bus-ämnen och ta emot meddelanden från Service Bus-köer och Service Bus-prenumerationer"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Anslut till Azure Synapse Analytics så att du kan visa dina data"
[azure-table-storage-doc]: /connectors/azuretables/ "Anslut till ditt Azure Storage-konto så att du kan skapa, uppdatera och fråga tabeller med mera"
[biztalk-server-doc]: /connectors/biztalk/ "Anslut till BizTalk Server så att du kan köra BizTalk-baserade program sida vid sida med Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Ansluta till ett lokalt filsystem"
[ftp-doc]: ./connectors-create-api-ftp.md "Ansluta till en FTP-/FTPS-server för FTP-aktiviteter, till exempel överföring, hämtning och borttagning av filer och mycket mer"
[github-doc]: ./connectors-create-api-github.md "Ansluta till GitHub och spåra problem"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Ansluter till Google Calendar och kan hantera kalendern"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Anslut till Google Sheets så att du kan ändra dina blad"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Ansluter till Google Tasks så att du kan hantera dina uppgifter"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Ansluta till 3270-appar på IBM-stordatorer"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Anslut till IBM DB2 i molnet eller lokalt. Uppdatera en rad, hämta en tabell med mera"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Anslut till Informix i molnet eller lokalt. Läsa en rad, lista tabellerna med mera"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Ansluta till IBM MQ lokalt eller i Azure för att skicka och ta emot meddelanden"
[instagram-doc]: ./connectors-create-api-instagram.md "Anslut till Instagram. Utlösa eller agera på händelser"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Ansluta till Mandrill för kommunikation"
[mysql-doc]: /connectors/mysql/ "Anslut till din lokala MySQL-databas så att du kan läsa och skriva data"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Anslut till ditt arbets- eller skolkonto så att du kan skicka och ta emot e-postmeddelanden, hantera din kalender och dina kontakter med mera"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Anslut till din personliga Microsoft OneDrive så att du kan ladda upp, ta bort, lista filer med mera"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Anslut till microsoft OneDrive för företag så att du kan ladda upp, ta bort, lista filer med mera"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Anslut till en Oracle-databas så att du kan lägga till, infoga, ta bort rader med mera"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Anslut till din Outlook-postlåda så att du kan hantera e-post, kalendrar, kontakter med mera"
[postgre-sql-doc]: /connectors/postgresql/ "Anslut till PostgreSQL-databasen så att du kan läsa data från tabeller"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Anslut till ditt Salesforce-konto. Hantera konton, leads, affärsmöjligheter med mera"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Ansluta till ett lokalt SAP-system"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Anslut till SendGrid. Skicka e-post och hantera mottagarlistor"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Anslut till ditt SFTP-konto med hjälp av SSH. Ladda upp, hämta, ta bort filer med mera"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Anslut till den lokala SharePoint-servern. Hantera dokument, listobjekt med mera"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Anslut till SharePoint Online. Hantera dokument, listobjekt med mera"
[slack-doc]: ./connectors-create-api-slack.md "Ansluta till Slack och skicka meddelanden till Slack-kanaler"
[smtp-doc]: ./connectors-create-api-smtp.md "Ansluta till en SMTP-server och skicka e-post med bifogade filer"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ansluter till SparkPost för kommunikation"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Anslut till Azure SQL Database eller SQL Server. Skapa, uppdatera, hämta och ta bort poster i en SQL-databastabell"
[teradata-doc]: /connectors/teradata/ "Ansluta till Teradata-databasen för att läsa data från tabeller"
[twilio-doc]: ./connectors-create-api-twilio.md "Anslut till Twilio. Skicka och hämta meddelanden, hämta tillgängliga nummer, hantera inkommande telefonnummer med mera"
[youtube-doc]: ./connectors-create-api-youtube.md "Anslut till YouTube. Hantera videor och kanaler"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Koda och avkoda meddelanden som använder AS2-protokollet"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Koda och avkoda meddelanden som använder EDIFACT-protokollet"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Avkoda meddelanden som använder EDIFACT-protokollet"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Koda meddelanden som använder EDIFACT-protokollet"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Lär dig mer om flat fil för Enterprise-integration"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Lär dig mer om flat fil för Enterprise-integration"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Hantera metadata för integrationskontoartefakter"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformera JSON med Liquid-mallar"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Koda och avkoda meddelanden som använder X12-protokollet"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Avkoda meddelanden som använder X12-protokollet"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Koda meddelanden som använder X12-protokollet"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformera XML-meddelanden"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Verifiera XML-meddelanden"


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Anslut till datakällor lokalt från logikappar med lokala datagatewayer"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Koda och avkoda meddelanden som använder AS2-protokollet"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Koda och avkoda meddelanden som använder EDIFACT-protokollet"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Avkoda meddelanden som använder EDIFACT-protokollet"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Koda meddelanden som använder EDIFACT-protokollet"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Lär dig mer om flat fil för Enterprise-integration"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Lär dig mer om flat fil för Enterprise-integration"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Hantera metadata för integrationskontoartefakter"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformera JSON med Liquid-mallar"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Koda och avkoda meddelanden som använder X12-protokollet"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Avkoda meddelanden som använder X12-protokollet"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Koda meddelanden som använder X12-protokollet"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformera XML-meddelanden"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Verifiera XML-meddelanden"
