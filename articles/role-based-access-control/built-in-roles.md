---
title: Inbyggda Azure-roller – Azure RBAC
description: I den här artikeln beskrivs de inbyggda Azure-rollerna för rollbaserad åtkomst kontroll i Azure (Azure RBAC). Den listar åtgärder, NotActions, DataActions och NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 08/16/2020
ms.custom: generated
ms.openlocfilehash: 44b4134404d5af3a8dde7028ffa1b43258df7558
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271999"
---
# <a name="azure-built-in-roles"></a>Inbyggda roller i Azure

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](overview.md) har flera inbyggda Azure-roller som du kan tilldela till användare, grupper, tjänstens huvud namn och hanterade identiteter. Roll tilldelningar är hur du styr åtkomsten till Azure-resurser. Om de inbyggda rollerna inte uppfyller organisationens specifika behov kan du skapa egna [Azure-anpassade roller](custom-roles.md).

Den här artikeln innehåller de inbyggda Azure-rollerna, som alltid utvecklas. För att få de senaste rollerna använder du [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) eller [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list). Om du letar efter administratörs roller för Azure Active Directory (Azure AD) kan du läsa [behörigheter för administratörs roll i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Följande tabell innehåller en kort beskrivning och det unika ID: t för varje inbyggd roll. Klicka på roll namnet om du vill se en lista över `Actions` , `NotActions` , `DataActions` och `NotDataActions` för varje roll. Information om vad dessa åtgärder betyder och hur de tillämpas på hanterings-och data planen finns i [förstå definitioner av Azure-roller](role-definitions.md).

## <a name="all"></a>Alla

> [!div class="mx-tableFixed"]
> | Inbyggd roll | Beskrivning | ID |
> | --- | --- | --- |
> | **Allmänt** |  |  |
> | [Deltagare](#contributor) | Ger fullständig åtkomst för att hantera alla resurser, men tillåter inte att du tilldelar roller i Azure RBAC. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Ägare](#owner) | Ger fullständig åtkomst för att hantera alla resurser, inklusive möjligheten att tilldela roller i Azure RBAC. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Läsare](#reader) | Visa alla resurser, men du kan inte göra några ändringar. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administratör för användaråtkomst](#user-access-administrator) | Gör att du kan hantera användar åtkomst till Azure-resurser. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Beräkning** |  |  |
> | [Klassisk virtuell dator deltagare](#classic-virtual-machine-contributor) | Låter dig hantera klassiska virtuella datorer, men inte åtkomst till dem, inte det virtuella nätverk eller lagrings konto som de är anslutna till. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Administratörs inloggning för virtuell dator](#virtual-machine-administrator-login) | Visa Virtual Machines i portalen och logga in som administratör | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtuell datordeltagare](#virtual-machine-contributor) | Låter dig hantera virtuella datorer, men inte åtkomst till dem, inte det virtuella nätverk eller lagrings konto som de är anslutna till. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Användar inloggning för virtuell dator](#virtual-machine-user-login) | Visa Virtual Machines i portalen och logga in som en vanlig användare. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Nätverk** |  |  |
> | [CDN-slutpunkts deltagare](#cdn-endpoint-contributor) | Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-slutpunkt läsare](#cdn-endpoint-reader) | Kan visa CDN-slutpunkter, men kan inte göra ändringar. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN-profil deltagare](#cdn-profile-contributor) | Kan hantera CDN-profiler och deras slut punkter, men kan inte bevilja åtkomst till andra användare. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN profil läsare](#cdn-profile-reader) | Kan visa CDN-profiler och deras slut punkter, men kan inte göra ändringar. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Klassisk nätverksdeltagare](#classic-network-contributor) | Gör att du kan hantera klassiska nätverk, men inte till gång till dem. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS-zon deltagare](#dns-zone-contributor) | Gör att du kan hantera DNS-zoner och post uppsättningar i Azure DNS, men du kan inte styra vem som har åtkomst till dem. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Nätverksdeltagare](#network-contributor) | Gör att du kan hantera nätverk, men inte till gång till dem. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Privat DNS zon deltagare](#private-dns-zone-contributor) | Låter dig hantera resurser för privata DNS-zoner, men inte de virtuella nätverk som de är länkade till. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager deltagare](#traffic-manager-contributor) | Låter dig hantera Traffic Manager profiler, men låter dig inte kontrol lera vem som har åtkomst till dem. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Aver deltagare](#avere-contributor) | Kan skapa och hantera ett AVERT vFXT-kluster. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Aver operator](#avere-operator) | Används av det Avera vFXT-klustret för att hantera klustret | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Säkerhets kopierings deltagare](#backup-contributor) | Låter dig hantera säkerhets kopierings tjänsten, men kan inte skapa valv och ge åtkomst till andra | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Ansvarig för säkerhets kopiering](#backup-operator) | Låter dig hantera säkerhets kopierings tjänster, förutom att ta bort säkerhets kopiering, skapa valv och ge till gång till andra | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Säkerhets kopierings läsare](#backup-reader) | Kan visa säkerhets kopierings tjänster, men kan inte göra ändringar | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Klassisk lagrings konto deltagare](#classic-storage-account-contributor) | Gör att du kan hantera klassiska lagrings konton, men inte till gång till dem. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Klassisk lagrings kontots nyckel operatörs tjänst roll](#classic-storage-account-key-operator-service-role) | Klassiska lagrings konto nyckel operatörer får lista och återskapa nycklar på klassiska lagrings konton | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box-enhet deltagare](#data-box-contributor) | Låter dig hantera allt under Data Box-enhet tjänst, förutom att ge till gång till andra. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box-enhet läsare](#data-box-reader) | Låter dig hantera Data Box-enhet tjänst, förutom att skapa order-eller redigerings beställnings detaljer och ge åtkomst till andra. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics utvecklare](#data-lake-analytics-developer) | Låter dig skicka, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics konton. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Läsare och data åtkomst](#reader-and-data-access) | Gör att du kan visa allting men du kan inte ta bort eller skapa ett lagrings konto eller en resurs som saknas. Den kommer också att tillåta Läs-/skriv åtkomst till alla data som finns i ett lagrings konto via åtkomst till lagrings konto nycklar. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Lagringskontodeltagare](#storage-account-contributor) | Tillåter hantering av lagrings konton. Ger åtkomst till konto nyckeln, som kan användas för att få åtkomst till data via autentisering med delad nyckel. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Lagrings kontots nyckel operatörs tjänst roll](#storage-account-key-operator-service-role) | Tillåter att du visar och återskapar åtkomst nycklar för lagrings kontot. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage Blob Data-deltagare](#storage-blob-data-contributor) | Läsa, skriva och ta bort Azure Storage behållare och blobbar. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage BLOB data-ägare](#storage-blob-data-owner) | Ger fullständig åtkomst till Azure Storage BLOB-behållare och data, inklusive att tilldela POSIX-åtkomstkontroll. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage BLOB data Reader](#storage-blob-data-reader) | Läs och Visa Azure Storage behållare och blobbar. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage BLOB-delegerare](#storage-blob-delegator) | Hämta en användar Delegerings nyckel som sedan kan användas för att skapa en signatur för delad åtkomst för en behållare eller BLOB som är signerad med Azure AD-autentiseringsuppgifter. Mer information finns i [skapa en användar Delegerings-SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Storage-fildata för SMB-resursdeltagare](#storage-file-data-smb-share-contributor) | Tillåter Läs-, skriv-och borttagnings åtkomst på filer/kataloger i Azure-filresurser. Den här rollen har ingen inbyggd motsvarighet på Windows-filservrar. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage-fildata för upphöjd SMB-resursdeltagare](#storage-file-data-smb-share-elevated-contributor) | Tillåter Läs-, Skriv-, borttagnings-och ändrings-ACL: er på filer/kataloger i Azure-filresurser. Den här rollen motsvarar en fil resurs-ACL för ändring på Windows-filservrar. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage-fildata för SMB-resursläsare](#storage-file-data-smb-share-reader) | Tillåter Läs åtkomst till filer/kataloger i Azure-filresurser. Den här rollen motsvarar en fil resurs-ACL för läsning på Windows-filservrar. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Data deltagare i Storage Queue](#storage-queue-data-contributor) | Läsa, skriva och ta bort Azure Storage köer och köa meddelanden. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processor för data meddelande i lagrings kön](#storage-queue-data-message-processor) | Granska, hämta och ta bort ett meddelande från en Azure Storage kö. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Avsändare av data meddelande i lagrings köer](#storage-queue-data-message-sender) | Lägg till meddelanden i en Azure Storage-kö. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Data läsare för lagrings kön](#storage-queue-data-reader) | Läs och Visa Azure Storage köer och köa meddelanden. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Webb** |  |  |
> | [Azure Maps data läsare](#azure-maps-data-reader) | Beviljar åtkomst till läsa kartdata relaterade data från ett Azure Maps-konto. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Search Service deltagare](#search-service-contributor) | Låter dig hantera Sök tjänster, men inte till gång till dem. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Webb Plans deltagare](#web-plan-contributor) | Gör att du kan hantera webb planer för webbplatser, men inte till gång till dem. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Webbplats deltagare](#website-contributor) | Gör att du kan hantera webbplatser (inte webb planer), men inte till gång till dem. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containrar** |  |  |
> | [AcrDelete](#acrdelete) | ta bort ACR | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR-bildsignerare | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR-push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR Quarantine data Reader | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR karantän data skrivare | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Administratörs roll för Azure Kubernetes service Cluster](#azure-kubernetes-service-cluster-admin-role) | Visa lista med autentiseringsuppgifter för kluster administratör. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Användar roll för Azure Kubernetes service-kluster](#azure-kubernetes-service-cluster-user-role) | Visa lista över autentiseringsuppgifter för kluster användare. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Rollen Azure Kubernetes service Contributor](#azure-kubernetes-service-contributor-role) | Ger åtkomst till läsa och skriva Azure Kubernetes service-kluster | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [RBAC-administratör för Azure Kubernetes-tjänsten](#azure-kubernetes-service-rbac-admin) | Gör att du kan hantera alla resurser under kluster/namn område, förutom att uppdatera eller ta bort resurs kvoter och namn områden. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes service RBAC-kluster administratör](#azure-kubernetes-service-rbac-cluster-admin) | Gör att du kan hantera alla resurser i klustret. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [RBAC-läsare för Azure Kubernetes service](#azure-kubernetes-service-rbac-reader) | Gör att du kan visa alla resurser i kluster/namn område, förutom hemligheter. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [RBAC-skrivare för Azure Kubernetes service](#azure-kubernetes-service-rbac-writer) | Låter dig uppdatera allt i kluster/namn område, förutom resurs kvoter, namn områden, Pod säkerhets principer, certifikat signerings begär Anden, (kluster) roller och roll bindningar (kluster). | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Databaser** |  |  |
> | [Cosmos DB konto läsar roll](#cosmos-db-account-reader-role) | Kan läsa Azure Cosmos DB konto data. Se [DocumentDB Account Contributor](#documentdb-account-contributor) för att hantera Azure Cosmos DB-konton. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB operatör](#cosmos-db-operator) | Låter dig hantera Azure Cosmos DB konton, men inte komma åt data i dem. Förhindrar åtkomst till konto nycklar och anslutnings strängar. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kan skicka en Restore-begäran för en Cosmos DB databas eller en behållare för ett konto | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [DocumentDB-konto deltagare](#documentdb-account-contributor) | Kan hantera Azure Cosmos DB-konton. Azure Cosmos DB är tidigare känt som DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache deltagare](#redis-cache-contributor) | Låter dig hantera Redis-cacheer, men inte till gång till dem. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB-deltagare](#sql-db-contributor) | Gör att du kan hantera SQL-databaser, men inte åtkomst till dem. Du kan inte heller hantera säkerhets relaterade principer eller överordnade SQL-servrar. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL-hanterad instans deltagare](#sql-managed-instance-contributor) | Låter dig hantera SQL-hanterade instanser och nödvändig nätverks konfiguration, men kan inte ge åtkomst till andra. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL Security Manager](#sql-security-manager) | Gör att du kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server deltagare](#sql-server-contributor) | Gör att du kan hantera SQL-servrar och databaser, men inte åtkomst till dem och inte deras säkerhetsrelaterade principer. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analys** |  |  |
> | [Azure Event Hubs data ägare](#azure-event-hubs-data-owner) | Ger fullständig åtkomst till Azure Event Hubs-resurser. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs data mottagare](#azure-event-hubs-data-receiver) | Tillåter åtkomst till Azure Event Hubs-resurser. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs data avsändare](#azure-event-hubs-data-sender) | Tillåter skicka åtkomst till Azure Event Hubs-resurser. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory deltagare](#data-factory-contributor) | Skapa och hantera data fabriker, samt underordnade resurser i dem. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Data rensning](#data-purger) | Kan rensa analys data | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight-kluster operator](#hdinsight-cluster-operator) | Gör att du kan läsa och ändra HDInsight-klusterkonfigurationer. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight Domain Services-deltagare](#hdinsight-domain-services-contributor) | Kan läsa, skapa, ändra och ta bort åtgärder för domän tjänster som krävs för HDInsight-Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics deltagare kan läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna lägger du till VM-tillägget till virtuella datorer. läsning av lagrings konto nycklar för att kunna konfigurera samling av loggar från Azure Storage. Skapa och konfigurera Automation-konton. lägga till lösningar. och konfigurera Azure Diagnostics på alla Azure-resurser. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics Reader](#log-analytics-reader) | Log Analytics läsaren kan visa och söka i alla övervaknings data samt Visa övervaknings inställningar, inklusive Visa konfigurationen av Azure Diagnostics på alla Azure-resurser. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Blockkedja** |  |  |
> | [Blockchain för medlems Node (för hands version)](#blockchain-member-node-access-preview) | Tillåter åtkomst till blockchain-medlems noder | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + maskin inlärning** |  |  |
> | [Cognitive Services deltagare](#cognitive-services-contributor) | Gör att du kan skapa, läsa, uppdatera, ta bort och hantera nycklar för Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services data läsare (förhands granskning)](#cognitive-services-data-reader-preview) | Gör att du kan läsa Cognitive Services data. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services användare](#cognitive-services-user) | Gör att du kan läsa och Visa nycklar för Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Mixad verklighet** |  |  |
> | [Administratör för fjärrrendering](#remote-rendering-administrator) | Ger användaren funktioner för konvertering, hantering av sessioner, åter givning och diagnostik för Azure-fjärrrendering | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Fjärran sluten klient](#remote-rendering-client) | Ger användaren funktioner för att hantera sessioner, åter givning och diagnostik för Azure fjärrrendering. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Konto deltagare för spatiala ankare](#spatial-anchors-account-contributor) | Låter dig hantera spatiala ankare i ditt konto, men ta inte bort dem | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Konto ägare för spatiala ankare](#spatial-anchors-account-owner) | Låter dig hantera spatialdata i ditt konto, inklusive att ta bort dem | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Konto läsare för spatiala ankare](#spatial-anchors-account-reader) | Gör att du kan hitta och läsa egenskaper för spatiala ankare i ditt konto | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integrering** |  |  |
> | [API Management Service Contributor](#api-management-service-contributor) | Kan hantera tjänster och API: er | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Rollen API Management tjänst operatör](#api-management-service-operator-role) | Kan hantera tjänsten men inte API: erna | e022efe7-F5BA-4159-bbe4-b44f577e9b61 |
> | [Rollen API Management tjänst läsare](#api-management-service-reader-role) | Skrivskyddad åtkomst till tjänster och API: er | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Data ägare för app Configuration](#app-configuration-data-owner) | Ger fullständig åtkomst till konfigurations data för appar. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Konfigurations data läsare för app](#app-configuration-data-reader) | Tillåter Läs åtkomst till konfigurations data för appar. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus data ägare](#azure-service-bus-data-owner) | Ger fullständig åtkomst till Azure Service Bus resurser. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus data mottagare](#azure-service-bus-data-receiver) | Ger åtkomst till Azure Service Bus resurser. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus data avsändare](#azure-service-bus-data-sender) | Tillåter att åtkomst till Azure Service Bus-resurser skickas. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack registrerings ägare](#azure-stack-registration-owner) | Låter dig hantera Azure Stack-registreringar. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription-deltagare](#eventgrid-eventsubscription-contributor) | Låter dig hantera EventGrid händelse prenumerations åtgärder. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-läsare](#eventgrid-eventsubscription-reader) | Låter dig läsa EventGrid händelse prenumerationer. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR data deltagare](#fhir-data-contributor) | Rollen ger användaren eller principen fullständig åtkomst till FHIR-data | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR data export](#fhir-data-exporter) | Roll låter användaren eller huvud kontot läsa och exportera FHIR-data | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR data läsare](#fhir-data-reader) | Roll låter användaren eller huvud kontot läsa FHIR-data | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR data skrivare](#fhir-data-writer) | Roll låter användaren eller huvud kontot läsa och skriva FHIR-data | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Integration Service Environment deltagare](#integration-service-environment-contributor) | Låter dig hantera integrerings tjänst miljöer, men inte till gång till dem. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Integration Service Environment utvecklare](#integration-service-environment-developer) | Gör det möjligt för utvecklare att skapa och uppdatera arbets flöden, integrations konton och API-anslutningar i integrerings tjänst miljöer. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Konto deltagare i Intelligent Systems](#intelligent-systems-account-contributor) | Gör att du kan hantera intelligenta system konton, men inte åtkomst till dem. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logic app-deltagare](#logic-app-contributor) | Låter dig hantera Logi Kap par, men ändra inte åtkomsten till dem. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic app-operatör](#logic-app-operator) | Låter dig läsa, aktivera och inaktivera Logi Kap par, men inte redigera eller uppdatera dem. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identitet** |  |  |
> | [Hanterad identitets deltagare](#managed-identity-contributor) | Skapa, läsa, uppdatera och ta bort användare tilldelad identitet | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Hanterad identitets operator](#managed-identity-operator) | Läs och tilldela en tilldelad identitet | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Säkerhet** |  |  |
> | [Azure Sentinel-deltagare](#azure-sentinel-contributor) | Azure Sentinel-deltagare | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel-läsare](#azure-sentinel-reader) | Azure Sentinel-läsare | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel-svarare](#azure-sentinel-responder) | Azure Sentinel-svarare | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault deltagare](#key-vault-contributor) | Låter dig hantera nyckel valv, men inte åtkomst till dem. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Säkerhets administratör](#security-admin) | Visa och uppdatera behörigheter för Security Center. Samma behörigheter som säkerhets läsar rollen och kan också uppdatera säkerhets principen och ignorera aviseringar och rekommendationer. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Säkerhets utvärderings deltagare](#security-assessment-contributor) | Gör att du kan skicka utvärderingar till Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Säkerhets hanterare (bakåtkompatibelt)](#security-manager-legacy) | Detta är en äldre roll. Använd säkerhets administratör i stället. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Säkerhetsläsare](#security-reader) | Visa behörigheter för Security Center. Kan visa rekommendationer, aviseringar, säkerhets principer och säkerhets tillstånd, men kan inte göra ändringar. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs-användare](#devtest-labs-user) | Låter dig ansluta, starta, starta om och stänga av dina virtuella datorer i din Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Labb skapare](#lab-creator) | Gör att du kan skapa nya labb under dina Azure Lab-konton. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Övervaka** |  |  |
> | [Application Insights komponent deltagare](#application-insights-component-contributor) | Kan hantera Application Insights-komponenter | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Ger användaren behörighet att visa och hämta fel söknings ögonblicks bilder som samlats in med Application Insights Snapshot Debugger. Observera att dessa behörigheter inte ingår i [ägaren](#owner) eller [deltagar](#contributor) rollerna. När du ger användarna Application Insights Snapshot Debugger-rollen måste du ge användaren rollen direkt. Rollen identifieras inte när den läggs till i en anpassad roll. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Övervaknings deltagare](#monitoring-contributor) | Kan läsa alla övervaknings data och redigera övervaknings inställningar. Se även [komma igång med roller, behörigheter och säkerhet med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Övervaknings mått utgivare](#monitoring-metrics-publisher) | Möjliggör publicering av mått mot Azure-resurser | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Övervaknings läsare](#monitoring-reader) | Kan läsa alla övervaknings data (mått, loggar osv.). Se även [komma igång med roller, behörigheter och säkerhet med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Arbets boks deltagare](#workbook-contributor) | Kan spara delade arbets böcker. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Arbets boks läsare](#workbook-reader) | Kan läsa arbets böcker. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Hantering och styrning** |  |  |
> | [Automatiserings jobb operatör](#automation-job-operator) | Skapa och hantera jobb med hjälp av Automation-runbooks. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operatör](#automation-operator) | Automation-operatörer kan starta, stoppa, pausa och återuppta jobb | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook-operator](#automation-runbook-operator) | Läs Runbook-egenskaperna – för att kunna skapa jobb för runbooken. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure-ansluten dator onboarding](#azure-connected-machine-onboarding) | Kan publicera Azure-anslutna datorer. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Resurs administratör för Azure-ansluten dator](#azure-connected-machine-resource-administrator) | Kan läsa, skriva, ta bort och återställa Azure-anslutna datorer. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Fakturerings läsare](#billing-reader) | Tillåter Läs åtkomst till fakturerings data | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Skiss deltagare](#blueprint-contributor) | Kan hantera skiss definitioner, men tilldela dem inte. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Skiss operator](#blueprint-operator) | Kan tilldela befintliga publicerade ritningar, men kan inte skapa nya ritningar. Observera att detta endast fungerar om tilldelningen görs med en tilldelad hanterad identitet. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management deltagare](#cost-management-contributor) | Kan visa kostnader och hantera kostnads konfiguration (t. ex. budgetar, exporter) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management läsare](#cost-management-reader) | Kan visa kostnads data och konfiguration (t. ex. budgetar, exporter) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Administratör för hierarkiska inställningar](#hierarchy-settings-administrator) | Tillåter användare att redigera och ta bort inställningar för hierarki | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes-kluster – Azure Arc onboarding](#kubernetes-cluster---azure-arc-onboarding) | Roll definition som tillåter att en användare/tjänst skapar connectedClusters-resurs | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Rollen hanterad program deltagare](#managed-application-contributor-role) | Gör det möjligt att skapa hanterade program resurser. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rollen hanterad program operatör](#managed-application-operator-role) | Gör att du kan läsa och utföra åtgärder på hanterade program resurser | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Läsare för hanterade program](#managed-applications-reader) | Låter dig läsa resurser i en hanterad app och begära JIT-åtkomst. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Borttagnings roll för registrering av hanterade tjänster](#managed-services-registration-assignment-delete-role) | Ta bort roll för registrering av hanterade tjänster för att hantera klient organisations användare kan ta bort den registrerings tilldelning som tilldelats till klienten. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Deltagare i hanterings grupp](#management-group-contributor) | Rollen hanterings grupp deltagare | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Hanterings grupp läsare](#management-group-reader) | Rollen hanterings grupp läsare | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Ny Relic APM-konto deltagare](#new-relic-apm-account-contributor) | Låter dig hantera New Relic Application Performance Management konton och program, men inte till gång till dem. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Data skrivare för princip insikter (för hands version)](#policy-insights-data-writer-preview) | Tillåter Läs åtkomst till resurs principer och Skriv behörighet till resurs komponent princip händelser. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Deltagare för resursprincip](#resource-policy-contributor) | Användare med behörighet att skapa/ändra resurs principer, skapa support ärende och läsa resurser/hierarki. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery deltagare](#site-recovery-contributor) | Låter dig hantera Site Recovery tjänst förutom att skapa valv och roll tilldelning | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery operatör](#site-recovery-operator) | Låter dig redundansväxla och failback men inte utföra andra Site Recovery hanterings åtgärder | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery läsare](#site-recovery-reader) | Låter dig Visa Site Recovery status men inte utföra andra hanterings åtgärder | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Support förfrågan deltagare](#support-request-contributor) | Gör att du kan skapa och hantera support förfrågningar | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Tagga deltagare](#tag-contributor) | Låter dig hantera Taggar i entiteter utan att ge åtkomst till själva entiteterna. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Övrigt** |  |  |
> | [BizTalk-deltagare](#biztalk-contributor) | Gör att du kan hantera BizTalk Services, men inte till gång till dem. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Skriv bords Virtualization-användare](#desktop-virtualization-user) | Tillåter användare att använda programmen i en program grupp. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Jobb samlings deltagare i Scheduler](#scheduler-job-collections-contributor) | Gör att du kan hantera jobb samlingar i Scheduler, men inte till gång till dem. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Allmänt


### <a name="contributor"></a>Deltagare

Ger fullständig åtkomst för att hantera alla resurser, men tillåter inte att du tilldelar roller i Azure RBAC. [Läs mer](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | * | Skapa och hantera resurser av alla typer |
> | **NotActions** |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Ta bort roller, princip tilldelningar, princip definitioner och princip uppsättnings definitioner |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Skapa roller, roll tilldelningar, princip tilldelningar, princip definitioner och princip uppsättnings definitioner |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)-/elevateAccess/Action | Beviljar anroparen Användaråtkomst Administratörsåtkomst för klientomfånget |
> | [Microsoft. skiss](resource-provider-operations.md#microsoftblueprint)-/blueprintAssignments/Write | Skapa eller uppdatera alla skiss uppgifter |
> | [Microsoft. skiss](resource-provider-operations.md#microsoftblueprint)-/blueprintAssignments/Delete | Ta bort alla skiss uppgifter |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Ägare

Ger fullständig åtkomst för att hantera alla resurser, inklusive möjligheten att tilldela roller i Azure RBAC. [Läs mer](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | * | Skapa och hantera resurser av alla typer |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Läsare

Visa alla resurser, men du kan inte göra några ändringar. [Läs mer](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Administratör för användaråtkomst

Gör att du kan hantera användar åtkomst till Azure-resurser. [Läs mer](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/* | Hantera auktorisering |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Klassisk virtuell dator deltagare

Låter dig hantera klassiska virtuella datorer, men inte åtkomst till dem, inte det virtuella nätverk eller lagrings konto som de är anslutna till.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Skapa och hantera klassiska beräknings domän namn |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Skapa och hantera virtuella datorer |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/Join/Action |  |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Link/Action | Länka en reserverad IP |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Read | Hämtar de reserverade IP-adresserna |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Join/Action | Ansluter till det virtuella nätverket. |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Read | Hämta det virtuella nätverket. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/Read | Returnerar lagrings konto disken. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/Read | Returnerar lagrings konto avbildningen. Föråldrad. Använd Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Visar åtkomst nycklar för lagrings kontona. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Returnera lagrings kontot med det aktuella kontot. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Administratörs inloggning för virtuell dator

Visa Virtual Machines i portalen och logga in som administratör [Läs mer](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/publicIPAddresses/Read | Hämtar en offentlig IP-adress definition. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/Read | Hämtar en belastnings Utjämnings definition |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/Read | Hämtar en definition för nätverks gränssnitt.  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/login/Action | Logga in på en virtuell dator som en vanlig användare |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/loginAsAdmin/Action | Logga in på en virtuell dator med Windows-administratör eller Linux rot användar privilegier |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Virtuell datordeltagare

Låter dig hantera virtuella datorer, men inte åtkomst till dem, inte det virtuella nätverk eller lagrings konto som de är anslutna till. [Läs mer](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Skapa och hantera beräknings tillgänglighets uppsättningar |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Skapa och hantera beräknings platser |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Skapa och hantera virtuella datorer |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Skapa och hantera VM-skalningsuppsättningar |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/disks/Write | Skapar en ny disk eller uppdaterar en befintlig |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/disks/Read | Hämta egenskaperna för en disk |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/disks/Delete | Tar bort disken |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Schedules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/applicationGateways/backendAddressPools/Join/Action | Ansluter till en programgateways backend-adresspool. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/backendAddressPools/Join/Action | Ansluter till en backend-adresspool för belastnings utjämning. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/inboundNatPools/Join/Action | Ansluter en inkommande NAT-pool för belastningsutjämnaren. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/inboundNatRules/Join/Action | Ansluter en inkommande NAT-regel för belastningsutjämnare. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/PROBES/Join/Action | Tillåter att avsökningar av en belastningsutjämnare används. Till exempel kan den här behörighetens egenskap healthProbe-egenskap för VM Scale set referera till avsökningen. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/Read | Hämtar en belastnings Utjämnings definition |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/locations/* | Skapa och hantera nätverks platser |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/* | Skapa och hantera nätverks gränssnitt |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkSecurityGroups/Join/Action | Ansluter till en nätverks säkerhets grupp. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkSecurityGroups/Read | Hämtar en definition för nätverks säkerhets grupp |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/publicIPAddresses/Join/Action | Ansluter till en offentlig IP-adress. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/publicIPAddresses/Read | Hämtar en offentlig IP-adress definition. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Skapa en säkerhets kopia av skydds avsikt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Returnerar objekt information om det skyddade objektet |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Skapa ett säkerhetskopierat skyddat objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Returnerar alla skydds principer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Write | Skapar skydds princip |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Returnerar användnings information för ett Recovery Services-valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | Skapa valv-åtgärd skapar en Azure-resurs av typen valv |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/listKeys/Action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/Read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Användar inloggning för virtuell dator

Visa Virtual Machines i portalen och logga in som en vanlig användare. [Läs mer](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/publicIPAddresses/Read | Hämtar en offentlig IP-adress definition. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/Read | Hämtar en belastnings Utjämnings definition |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/Read | Hämtar en definition för nätverks gränssnitt.  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/login/Action | Logga in på en virtuell dator som en vanlig användare |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Nätverk


### <a name="cdn-endpoint-contributor"></a>CDN-slutpunkts deltagare

Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)-/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/endpoints/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN-slutpunkt läsare

Kan visa CDN-slutpunkter, men kan inte göra ändringar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)-/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/endpoints/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN-profil deltagare

Kan hantera CDN-profiler och deras slut punkter, men kan inte bevilja åtkomst till andra användare. [Läs mer](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)-/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN profil läsare

Kan visa CDN-profiler och deras slut punkter, men kan inte göra ändringar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)-/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Klassisk nätverksdeltagare

Gör att du kan hantera klassiska nätverk, men inte till gång till dem. [Läs mer](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Skapa och hantera klassiska nätverk |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS-zon deltagare

Gör att du kan hantera DNS-zoner och post uppsättningar i Azure DNS, men du kan inte styra vem som har åtkomst till dem. [Läs mer](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/dnsZones/* | Skapa och hantera DNS-zoner och-poster |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Nätverksdeltagare

Gör att du kan hantera nätverk, men inte till gång till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/* | Skapa och hantera nätverk |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>Privat DNS zon deltagare

Låter dig hantera resurser för privata DNS-zoner, men inte de virtuella nätverk som de är länkade till. [Läs mer](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/privateDnsOperationStatuses/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager deltagare

Låter dig hantera Traffic Manager profiler, men låter dig inte kontrol lera vem som har åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/trafficManagerProfiles/* |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Storage


### <a name="avere-contributor"></a>Aver deltagare

Kan skapa och hantera ett AVERT vFXT-kluster. [Läs mer](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/*/Read |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/*/Read |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/Read | Hämtar en under näts definition för virtuellt nätverk |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkSecurityGroups/Join/Action | Ansluter till en nätverks säkerhets grupp. Det går inte att avisera. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/*/Read |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/* | Skapa och hantera lagringskonton |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Resources/Read | Hämtar resurser för resurs gruppen. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Delete | Returnerar resultatet av att ta bort en BLOB |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Read | Returnerar en BLOB eller en lista över blobbar |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Write | Returnerar resultatet av att skriva en BLOB |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Aver operator

Används av det Avera vFXT-klustret för att hantera klustret [Läs mer](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/Read | Hämta egenskaperna för en virtuell dator |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/Read | Hämtar en definition för nätverks gränssnitt.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/Write | Skapar ett nätverks gränssnitt eller uppdaterar ett befintligt nätverks gränssnitt.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/Read | Hämtar en under näts definition för virtuellt nätverk |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkSecurityGroups/Join/Action | Ansluter till en nätverks säkerhets grupp. Det går inte att avisera. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/Delete | Returnerar resultatet av att ta bort en behållare |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/Read | Returnerar lista över behållare |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/Write | Returnerar resultatet av att skicka BLOB-behållare |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Delete | Returnerar resultatet av att ta bort en BLOB |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Read | Returnerar en BLOB eller en lista över blobbar |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Write | Returnerar resultatet av att skriva en BLOB |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Säkerhets kopierings deltagare

Låter dig hantera säkerhets kopierings tjänsten, men det går inte att skapa valv och ge till gång till andra [Mer information](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Hantera åtgärds resultat för säkerhets kopierings hantering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Skapa och hantera säkerhets kopierings behållare i säkerhets kopierings infrastrukturer i Recovery Services Vault |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Uppdaterar behållar listan |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Skapa och hantera säkerhets kopierings jobb |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportera jobb |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Skapa och hantera resultat av säkerhets kopierings hanterings åtgärder |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Skapa och hantera säkerhets kopierings principer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhets kopie ras |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Skapa och hantera säkerhetskopierade objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Skapa och hantera behållare som innehåller säkerhets kopierings objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Skapa och hantera certifikat som rör säkerhets kopiering i Recovery Services Vault |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valvet |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Hämtar aviseringarna för Recovery Services-valvet. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Skapa och hantera användning av Recovery Services valv |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/Read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Verifiera åtgärd på skyddat objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | Skapa valv-åtgärd skapar en Azure-resurs av typen valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Returnerar säkerhets kopierings åtgärdens status för Recovery Services valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Returnerar alla säkerhets kopierings hanterings servrar registrerade i valvet. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Hämta alla skydds bara behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | Kontrol lera säkerhets kopierings status för Recovery Services valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Validera funktioner |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Löser aviseringen. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Åtgärd returnerar listan över åtgärder för en resurs leverantör |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/Read | Hämtar åtgärds status för en specifik åtgärd |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Lista alla säkerhets avsikter för säkerhets kopiering |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Ansvarig för säkerhets kopiering

Låter dig hantera säkerhets kopierings tjänster, förutom att ta bort säkerhets kopiering, skapa valv och ge till gång till andra [Läs mer](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Returnerar status för åtgärden |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Hämtar resultat från utförd åtgärd på skydds behållare. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/Action | Utför säkerhets kopiering för skyddat objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Returnerar statusen för den åtgärd som utförts på skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Returnerar objekt information om det skyddade objektet |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Etablera snabb objekts återställning för skyddat objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Hämta återställnings punkter för skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Återställa återställnings punkter för skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Återkalla direkt objekt återställning för skyddat objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Skapa ett säkerhetskopierat skyddat objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Returnerar alla registrerade behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Uppdaterar behållar listan |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Skapa och hantera säkerhets kopierings jobb |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportera jobb |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Skapa och hantera resultat av säkerhets kopierings hanterings åtgärder |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Hämta resultat från princip åtgärd. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Returnerar alla skydds principer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhets kopie ras |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Returnerar listan över alla skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Returnerar alla behållare som hör till prenumerationen |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/Write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Write | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Hämtar aviseringarna för Recovery Services-valvet. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Write | Du kan använda åtgärden registrera tjänst behållare för att registrera en behållare med återställnings tjänsten. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Returnerar användnings information för ett Recovery Services-valv. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/Read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Verifiera åtgärd på skyddat objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Returnerar säkerhets kopierings åtgärdens status för Recovery Services valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Hämta status för princip åtgärd. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Write | Skapar en registrerad behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Inquire/Action | Gör förfrågan om arbets belastningar inom en behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Returnerar alla säkerhets kopierings hanterings servrar registrerade i valvet. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Skapa en säkerhets kopia av skydds avsikt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Få en säkerhets kopie rad skydds avsikt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Hämta alla skydds bara behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Hämta alla objekt i en behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | Kontrol lera säkerhets kopierings status för Recovery Services valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Validera funktioner |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Löser aviseringen. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Åtgärd returnerar listan över åtgärder för en resurs leverantör |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/Read | Hämtar åtgärds status för en specifik åtgärd |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Lista alla säkerhets avsikter för säkerhets kopiering |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Säkerhets kopierings läsare

Kan visa säkerhets kopierings tjänster, men kan inte göra ändringar [Läs mer](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Returnerar status för åtgärden |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Hämtar resultat från utförd åtgärd på skydds behållare. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Returnerar statusen för den åtgärd som utförts på skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Returnerar objekt information om det skyddade objektet |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Hämta återställnings punkter för skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Returnerar alla registrerade behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/Read | Returnerar resultatet av jobb åtgärden. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/Read | Returnerar alla jobb objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Exportera jobb |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/Read | Returnerar resultatet av säkerhets kopierings åtgärden för Recovery Services valvet. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Hämta resultat från princip åtgärd. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Returnerar alla skydds principer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Returnerar listan över alla skyddade objekt. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Returnerar alla behållare som hör till prenumerationen |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Hämtar aviseringarna för Recovery Services-valvet. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/Read | Returnerar lagrings konfiguration för Recovery Services valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/Read | Returnerar konfiguration för Recovery Services valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Returnerar säkerhets kopierings åtgärdens status för Recovery Services valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Hämta status för princip åtgärd. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Returnerar alla säkerhets kopierings hanterings servrar registrerade i valvet. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Få en säkerhets kopie rad skydds avsikt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Hämta alla objekt i en behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | Kontrol lera säkerhets kopierings status för Recovery Services valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Löser aviseringen. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Åtgärd returnerar listan över åtgärder för en resurs leverantör |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/Read | Hämtar åtgärds status för en specifik åtgärd |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Lista alla säkerhets avsikter för säkerhets kopiering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Returnerar användnings information för ett Recovery Services-valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Validera funktioner |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Klassisk lagrings konto deltagare

Gör att du kan hantera klassiska lagrings konton, men inte till gång till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Skapa och hantera lagringskonton |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Klassisk lagrings kontots nyckel operatörs tjänst roll

Klassiska lagrings konto nyckel operatörer får lista och återskapa nycklar på klassiska lagrings konton [Läs mer](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/Action | Visar åtkomst nycklar för lagrings kontona. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/Action | Återskapar befintliga åtkomst nycklar för lagrings kontot. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box-enhet deltagare

Låter dig hantera allt under Data Box-enhet tjänst, förutom att ge till gång till andra. [Läs mer](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. data-](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box-enhet läsare

Låter dig hantera Data Box-enhet tjänst, förutom att skapa order-eller redigerings beställnings detaljer och ge åtkomst till andra. [Läs mer](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. data](resource-provider-operations.md#microsoftdatabox)-och/Read |  |
> | [Microsoft. data](resource-provider-operations.md#microsoftdatabox)-/Jobs/listsecrets/Action |  |
> | [Microsoft. data](resource-provider-operations.md#microsoftdatabox)-/Jobs/listcredentials/Action | Visar en lista med okrypterade autentiseringsuppgifter relaterade till beställningen. |
> | [Microsoft. data](resource-provider-operations.md#microsoftdatabox)-/locations/availableSkus/Action | Den här metoden returnerar listan över tillgängliga SKU: er. |
> | [Microsoft. data](resource-provider-operations.md#microsoftdatabox)-/locations/validateInputs/Action | Den här metoden utför alla typer av valideringar. |
> | [Microsoft. data](resource-provider-operations.md#microsoftdatabox)-/locations/regionConfiguration/Action | Den här metoden returnerar konfigurationerna för regionen. |
> | [Microsoft. data](resource-provider-operations.md#microsoftdatabox)-/locations/validateAddress/Action | Verifierar leverans adressen och ger alternativa adresser om det finns några. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics utvecklare

Låter dig skicka, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics konton. [Läs mer](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | Microsoft. BigAnalytics/Accounts/* |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/Accounts/Delete |  |
> | Microsoft. BigAnalytics/Accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/konton/Skriv |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/Delete | Ta bort ett DataLakeAnalytics-konto. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/TakeOwnerShip/Action | Bevilja behörighet att avbryta jobb som skickats av andra användare. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/Write | Skapa eller uppdatera ett DataLakeAnalytics-konto. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/dataLakeStoreAccounts/Write | Skapa eller uppdatera ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/dataLakeStoreAccounts/Delete | Ta bort länken mellan ett DataLakeStore-konto och ett DataLakeAnalytics-konto. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/storageAccounts/Write | Skapa eller uppdatera ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/storageAccounts/Delete | Ta bort länken till ett lagrings konto från ett DataLakeAnalytics-konto. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/firewallRules/Write | Skapa eller uppdatera en brand Väggs regel. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/firewallRules/Delete | Ta bort en brand Väggs regel. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/computePolicies/Write | Skapa eller uppdatera en beräknings princip. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/Accounts/computePolicies/Delete | Ta bort en beräknings princip. |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Läsare och data åtkomst

Gör att du kan visa allting men du kan inte ta bort eller skapa ett lagrings konto eller en resurs som saknas. Den kommer också att tillåta Läs-/skriv åtkomst till alla data som finns i ett lagrings konto via åtkomst till lagrings konto nycklar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/listKeys/Action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/ListAccountSas/Action | Returnerar kontots SAS-token för det angivna lagrings kontot. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/Read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Lagringskontodeltagare

Tillåter hantering av lagrings konton. Ger åtkomst till konto nyckeln, som kan användas för att få åtkomst till data via autentisering med delad nyckel. [Läs mer](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Skapar, uppdaterar eller läser in diagnostikinställningar för Analysis Server |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/* | Skapa och hantera lagringskonton |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Lagrings kontots nyckel operatörs tjänst roll

Tillåter att du visar och återskapar åtkomst nycklar för lagrings kontot. [Läs mer](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/listkeys/Action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/regeneratekey/Action | Återskapar åtkomst nycklarna för det angivna lagrings kontot. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Storage Blob Data-deltagare

Läsa, skriva och ta bort Azure Storage behållare och blobbar. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/Delete | Ta bort en behållare. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/Read | Returnera en behållare eller en lista över behållare. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/Write | Ändra en behållares metadata eller egenskaper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/generateUserDelegationKey/Action | Returnerar en användar Delegerings nyckel för Blob Service. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Delete | Ta bort en blob. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Read | Returnera en BLOB eller en lista över blobbar. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Move/Action | Flyttar blobben från en sökväg till en annan |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Write | Skriv till en blob. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Storage BLOB data-ägare

Ger fullständig åtkomst till Azure Storage BLOB-behållare och data, inklusive att tilldela POSIX-åtkomstkontroll. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/* | Fullständiga behörigheter för behållare. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/generateUserDelegationKey/Action | Returnerar en användar Delegerings nyckel för Blob Service. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/* | Fullständiga behörigheter för blobbar. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Storage BLOB data Reader

Läs och Visa Azure Storage behållare och blobbar. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/Read | Returnera en behållare eller en lista över behållare. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/generateUserDelegationKey/Action | Returnerar en användar Delegerings nyckel för Blob Service. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/containers/blobs/Read | Returnera en BLOB eller en lista över blobbar. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage BLOB-delegerare

Hämta en användar Delegerings nyckel som sedan kan användas för att skapa en signatur för delad åtkomst för en behållare eller BLOB som är signerad med Azure AD-autentiseringsuppgifter. Mer information finns i [skapa en användar Delegerings-SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). [Läs mer](https://docs.microsoft.com/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/blobServices/generateUserDelegationKey/Action | Returnerar en användar Delegerings nyckel för Blob Service. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Storage-fildata för SMB-resursdeltagare

Tillåter Läs-, skriv-och borttagnings åtkomst på filer/kataloger i Azure-filresurser. Den här rollen har ingen inbyggd motsvarighet på Windows-filservrar. [Läs mer](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/fileServices/fileshares/Files/Read | Returnerar en fil/mapp eller en lista över filer/mappar. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/fileServices/fileshares/Files/Write | Returnerar resultatet av att skriva en fil eller skapa en mapp. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/fileServices/fileshares/Files/Delete | Returnerar resultatet av att ta bort en fil/mapp. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Storage-fildata för upphöjd SMB-resursdeltagare

Tillåter Läs-, Skriv-, borttagnings-och ändrings-ACL: er på filer/kataloger i Azure-filresurser. Den här rollen motsvarar en fil resurs-ACL för ändring på Windows-filservrar. [Läs mer](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/fileServices/fileshares/Files/Read | Returnerar en fil/mapp eller en lista över filer/mappar. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/fileServices/fileshares/Files/Write | Returnerar resultatet av att skriva en fil eller skapa en mapp. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/fileServices/fileshares/Files/Delete | Returnerar resultatet av att ta bort en fil/mapp. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/fileServices/fileshares/Files/modifypermissions/Action | Returnerar resultatet av att ändra behörighet för en fil/mapp. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Storage-fildata för SMB-resursläsare

Tillåter Läs åtkomst till filer/kataloger i Azure-filresurser. Den här rollen motsvarar en fil resurs-ACL för läsning på Windows-filservrar. [Läs mer](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/fileServices/fileshares/Files/Read | Returnerar en fil/mapp eller en lista över filer/mappar. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Data deltagare i Storage Queue

Läsa, skriva och ta bort Azure Storage köer och köa meddelanden. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Delete | Ta bort en kö. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Read | Returnera en kö eller en lista över köer. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Write | Ändra metadata eller egenskaper för kö. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Messages/Delete | Ta bort ett eller flera meddelanden från en kö. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Messages/Read | Granska eller hämta ett eller flera meddelanden från en kö. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Messages/Write | Lägg till ett meddelande i en kö. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Processor för data meddelande i lagrings kön

Granska, hämta och ta bort ett meddelande från en Azure Storage kö. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Messages/Read | Titta på ett meddelande. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Messages/process/Action | Hämta och ta bort ett meddelande. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Avsändare av data meddelande i lagrings köer

Lägg till meddelanden i en Azure Storage-kö. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Messages/Add/Action | Lägg till ett meddelande i en kö. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Data läsare för lagrings kön

Läs och Visa Azure Storage köer och köa meddelanden. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Read | Returnerar en kö eller en lista över köer. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/queueServices/Queues/Messages/Read | Granska eller hämta ett eller flera meddelanden från en kö. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Webb


### <a name="azure-maps-data-reader"></a>Azure Maps data läsare

Beviljar åtkomst till läsa kartdata relaterade data från ett Azure Maps-konto.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/Accounts/*/Read |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Search Service deltagare

Låter dig hantera Sök tjänster, men inte till gång till dem. [Läs mer](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Skapa och hantera Sök tjänster |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Webb Plans deltagare

Gör att du kan hantera webb planer för webbplatser, men inte till gång till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/serverFarms/* | Skapa och hantera Server grupper |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Ansluter till en App Service-miljön |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Webbplats deltagare

Gör att du kan hantera webbplatser (inte webb planer), men inte till gång till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Skapa och hantera Insights-komponenter |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/certificates/* | Skapa och hantera webbplats certifikat |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/Read | Hämta namn på platser tilldelade till värdnamn. |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/serverFarms/Join/Action | Kopplar en App Service plan |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Hämta egenskaperna för en App Service plan |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/Sites/* | Skapa och hantera webbplatser (webbplats skapande kräver även Skriv behörighet till den associerade App Service planen) |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Containrar


### <a name="acrdelete"></a>AcrDelete

ACR ta bort [Läs mer](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Artifacts/Delete | Ta bort artefakt i ett behållar register. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

ACR image-undertecknare [Läs mer](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Sign/Write | Push/pull-metadata för innehålls förtroende för ett behållar register. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

ACR pull [Läs mer](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/Read | Hämta eller hämta avbildningar från ett behållar register. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

ACR push [Läs mer](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/Read | Hämta eller hämta avbildningar från ett behållar register. |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/Write | Push-överför eller Skriv avbildningar till ett behållar register. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR Quarantine data Reader

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Read | Hämta eller hämta bilder i karantän från container Registry |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

ACR karantän data skrivare

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Read | Hämta eller hämta bilder i karantän från container Registry |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Write | Skriv/ändra karantän tillstånd för karantän avbildningar |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Administratörs roll för Azure Kubernetes service Cluster

Visa lista med autentiseringsuppgifter för kluster administratör. [Läs mer](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/Action | Visa en lista över clusterAdmin-autentiseringsuppgiften för ett hanterat kluster |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/Action | Hämta en hanterad kluster åtkomst profil efter rollnamn med hjälp av lista autentiseringsuppgifter |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Hämta ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Användar roll för Azure Kubernetes service-kluster

Visa lista över autentiseringsuppgifter för kluster användare. [Läs mer](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Visa en lista över clusterUser-autentiseringsuppgiften för ett hanterat kluster |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Hämta ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Rollen Azure Kubernetes service Contributor

Ger åtkomst till läsa och skriva Azure Kubernetes service-kluster [Läs mer](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Hämta ett hanterat kluster |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Write | Skapar ett nytt hanterat kluster eller uppdaterar ett befintligt |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>RBAC-administratör för Azure Kubernetes-tjänsten

Gör att du kan hantera alla resurser under kluster/namn område, förutom att uppdatera eller ta bort resurs kvoter och namn områden. [Läs mer](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Write | Skapar eller uppdaterar en distribution. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Visa en lista över clusterUser-autentiseringsuppgiften för ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Write | Skriver resourcequotas |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Delete | Tar bort resourcequotas |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Write | Skriver namn områden |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Delete | Tar bort namn områden |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes service RBAC-kluster administratör

Gör att du kan hantera alla resurser i klustret. [Läs mer](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Write | Skapar eller uppdaterar en distribution. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Visa en lista över clusterUser-autentiseringsuppgiften för ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>RBAC-läsare för Azure Kubernetes service

Gör att du kan visa alla resurser i kluster/namn område, förutom hemligheter. [Läs mer](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Write | Skapar eller uppdaterar en distribution. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Visa en lista över clusterUser-autentiseringsuppgiften för ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/Read |  |
> | **NotDataActions** |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/RBAC.Authorization.K8s.io/*/Read |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/RBAC.Authorization.K8s.io/*/Write |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Secrets/* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*/read"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/read",
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/write",
        "Microsoft.ContainerService/managedClusters/secrets/*"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>RBAC-skrivare för Azure Kubernetes service

Låter dig uppdatera allt i kluster/namn område, förutom resurs kvoter, namn områden, Pod säkerhets principer, certifikat signerings begär Anden, (kluster) roller och roll bindningar (kluster). [Läs mer](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Write | Skapar eller uppdaterar en distribution. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Visa en lista över clusterUser-autentiseringsuppgiften för ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/Read |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/Write |  |
> | **NotDataActions** |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/RBAC.Authorization.K8s.io/*/Read |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/RBAC.Authorization.K8s.io/*/Write |  |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Write | Skriver namn områden |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Write | Skriver resourcequotas |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/certificates.K8s.io/certificatesigningrequests/Write | Skriver certificatesigningrequests |
> | [Microsoft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/podsecuritypolicies/Write | Skriver podsecuritypolicies |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except resource quotas, namespaces, pod security policies, certificate signing requests, (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*/read",
        "Microsoft.ContainerService/managedClusters/*/write"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/read",
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/write",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/write",
        "Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/write"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Databaser


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB konto läsar roll

Kan läsa Azure Cosmos DB konto data. Se [DocumentDB Account Contributor](#documentdb-account-contributor) för att hantera Azure Cosmos DB-konton. [Läs mer](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/Read | Läs valfri samling |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/Action | Läser databas kontots ReadOnly-nycklar. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/MetricDefinitions/Read | Läs mått definitioner |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Metrics/Read | Läs mått |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB operatör

Låter dig hantera Azure Cosmos DB konton, men inte komma åt data i dem. Förhindrar åtkomst till konto nycklar och anslutnings strängar. [Läs mer](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Kan skicka en Restore-begäran för en Cosmos DB databas eller en behållare för ett konto [Läs mer](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/Action | Skicka en begäran om att konfigurera säkerhets kopiering |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Restore/Action | Skicka en begäran om återställning |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB-konto deltagare

Kan hantera Azure Cosmos DB-konton. Azure Cosmos DB är tidigare känt som DocumentDB. [Läs mer](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Skapa och hantera Azure Cosmos DB-konton |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache deltagare

Låter dig hantera Redis-cacheer, men inte till gång till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)-/register/Action | Registrerar resurs leverantören "Microsoft. cache" med en prenumeration |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)-/Redis/* | Skapa och hantera Redis-cache |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB-deltagare

Gör att du kan hantera SQL-databaser, men inte åtkomst till dem. Du kan inte heller hantera säkerhets relaterade principer eller överordnade SQL-servrar. [Läs mer](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/* | Skapa och hantera SQL-databaser |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/Read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Metrics/Read | Läs mått |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/metricDefinitions/Read | Läs mått definitioner |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditingPolicies/* | Redigera gransknings principer |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditingSettings/* | Redigera gransknings inställningar |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditRecords/Read | Hämta databasens BLOB audit-poster |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/connectionPolicies/* | Redigera anslutnings principer |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/dataMaskingPolicies/* | Redigera data masknings principer |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/securityAlertPolicies/* | Redigera säkerhets aviserings principer |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/securityMetrics/* | Redigera säkerhets mått |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL-hanterad instans deltagare

Låter dig hantera SQL-hanterade instanser och nödvändig nätverks konfiguration, men kan inte ge åtkomst till andra.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkSecurityGroups/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/routeTables/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/locations/instanceFailoverGroups/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Metrics/Read | Läs mått |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/metricDefinitions/Read | Läs mått definitioner |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL Security Manager

Gör att du kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. [Läs mer](../sql-database/sql-database-advanced-data-security.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/auditingPolicies/* | Skapa och hantera principer för SQL Server-granskning |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/auditingSettings/* | Skapa och hantera gransknings inställning för SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/extendedAuditingSettings/Read | Hämta information om den utökade gransknings principen för Server-blob som kon figurer ATS på en viss server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditingPolicies/* | Skapa och hantera gransknings principer för SQL Server-databaser |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditingSettings/* | Skapa och hantera gransknings inställningar för SQL Server-databasen |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditRecords/Read | Hämta databasens BLOB audit-poster |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/connectionPolicies/* | Skapa och hantera anslutnings principer för SQL Server-databaser |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/dataMaskingPolicies/* | Skapa och hantera data masknings principer för SQL Server-databas |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/extendedAuditingSettings/Read | Hämta information om den utökade blobb gransknings principen som kon figurer ATS för en viss databas |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/Read | Returnera listan över databaser eller hämta egenskaperna för den angivna databasen. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/schemas/Read | Hämta ett databas schema. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/schemas/tables/columns/Read | Hämta en databas kolumn. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/schemas/tables/Read | Hämta en databas tabell. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/securityAlertPolicies/* | Skapa och hantera säkerhets aviserings principer för SQL Server-databas |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/securityMetrics/* | Skapa och hantera säkerhets mått för SQL Server-databasen |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/firewallRules/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/Read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/securityAlertPolicies/* | Skapa och hantera SQL Server-principer för säkerhets avisering |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/vulnerabilityAssessments/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server deltagare

Gör att du kan hantera SQL-servrar och databaser, men inte åtkomst till dem och inte deras säkerhetsrelaterade principer. [Läs mer](../sql-database/sql-database-aad-authentication-configure.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/* | Skapa och hantera SQL-servrar |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Metrics/Read | Läs mått |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/metricDefinitions/Read | Läs mått definitioner |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/auditingPolicies/* | Redigera principer för SQL Server-granskning |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/auditingSettings/* | Redigera gransknings inställningar för SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditingPolicies/* | Redigera gransknings principer för SQL Server-databasen |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditingSettings/* | Redigera gransknings inställningar för SQL Server-databasen |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/auditRecords/Read | Hämta databasens BLOB audit-poster |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/connectionPolicies/* | Redigera anslutnings principer för SQL Server-databas |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/dataMaskingPolicies/* | Redigera data masknings principer för SQL Server-databas |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/securityAlertPolicies/* | Redigera säkerhets aviserings principer för SQL Server-databas |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/securityMetrics/* | Redigera säkerhets mått för SQL Server-databas |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/securityAlertPolicies/* | Redigera säkerhets aviserings principer för SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)-/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs data ägare

Ger fullständig åtkomst till Azure Event Hubs-resurser. [Läs mer](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs data mottagare

Tillåter åtkomst till Azure Event Hubs-resurser. [Läs mer](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs data avsändare

Tillåter skicka åtkomst till Azure Event Hubs-resurser. [Läs mer](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory deltagare

Skapa och hantera data fabriker, samt underordnade resurser i dem. [Läs mer](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Skapa och hantera data fabriker och underordnade resurser i dem. |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Skapa och hantera data fabriker och underordnade resurser i dem. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | Skapa eller uppdatera en eventSubscription |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Data rensning

Kan rensa analys data [Läs mer](../azure-monitor/platform/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Components/PURGE/Action | Rensar data från Application Insights |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/Read | Visa Log Analytics-data |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/PURGE/Action | Ta bort angivna data från arbets ytan |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight-kluster operator

Gör att du kan läsa och ändra HDInsight-klusterkonfigurationer. [Läs mer](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/*/Read |  |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)-/Clusters/getGatewaySettings/Action | Hämta Gateway-inställningar för HDInsight-kluster |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)-/Clusters/updateGatewaySettings/Action | Uppdatera Gateway-inställningar för HDInsight-kluster |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)-/Clusters/Configurations/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Operations/Read | Hämtar eller visar distributions åtgärder. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight Domain Services-deltagare

Kan läsa, skapa, ändra och ta bort åtgärder för domän tjänster som behövs för HDInsight Enterprise Security Package [Läs mer](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/*-/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)-/domainServices/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)-/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics Contributor

Log Analytics deltagare kan läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna lägger du till VM-tillägget till virtuella datorer. läsning av lagrings konto nycklar för att kunna konfigurera samling av loggar från Azure Storage. Skapa och konfigurera Automation-konton. lägga till lösningar. och konfigurera Azure Diagnostics på alla Azure-resurser. [Läs mer](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/* |  |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Visar åtkomst nycklar för lagrings kontona. |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Installerar eller uppdaterar ett Azure Arc-tillägg |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Skapar, uppdaterar eller läser in diagnostikinställningar för Analysis Server |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/listKeys/Action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics Reader

Log Analytics läsaren kan visa och söka i alla övervaknings data samt Visa övervaknings inställningar, inklusive Visa konfigurationen av Azure Diagnostics på alla Azure-resurser. [Läs mer](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Analytics/Query/Action | Sök med ny motor. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/Action | Kör en Sök fråga |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/Read | Hämtar de delade nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockkedja


### <a name="blockchain-member-node-access-preview"></a>Blockchain för medlems Node (för hands version)

Tillåter åtkomst till blockchain-medlems noder [Läs mer](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Read | Hämtar eller visar befintliga blockchain för medlems transaktioner. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Connect/Action | Ansluter till en blockchain. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + maskin inlärning


### <a name="cognitive-services-contributor"></a>Cognitive Services deltagare

Gör att du kan skapa, läsa, uppdatera, ta bort och hantera nycklar för Cognitive Services. [Läs mer](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft. features](resource-provider-operations.md#microsoftfeatures)/features/Read | Hämtar funktionerna i en prenumeration. |
> | [Microsoft. features](resource-provider-operations.md#microsoftfeatures)/providers/features/Read | Hämtar funktionen för en prenumeration i en specifik resurs leverantör. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Skapar, uppdaterar eller läser in diagnostikinställningar för Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/logDefinitions/Read | Läs logg definitioner |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/metricdefinitions/Read | Läs mått definitioner |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Metrics/Read | Läs mått |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Operations/Read | Hämtar eller visar distributions åtgärder. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services data läsare (förhands granskning)

Gör att du kan läsa Cognitive Services data.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services användare

Gör att du kan läsa och Visa nycklar för Cognitive Services. [Läs mer](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/Accounts/listkeys/Action | Lista nycklar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/alertRules/Read | Läs en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/diagnosticSettings/Read | Läs en inställning för resurs diagnostik |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/logDefinitions/Read | Läs logg definitioner |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/metricdefinitions/Read | Läs mått definitioner |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Metrics/Read | Läs mått |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Operations/Read | Hämtar eller visar distributions åtgärder. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Mixed Reality


### <a name="remote-rendering-administrator"></a>Administratör för fjärrrendering

Ger användare med konvertering, hantering av sessioner, åter givning och diagnostik för Azure fjärrrendering [Läs mer](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Action | Starta till gångs konvertering |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Read | Hämta egenskaper för till gångs konvertering |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Delete | Stoppa till gångs konvertering |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Hämta egenskaper för session |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Starta sessioner |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Stoppa sessioner |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Render/Read | Ansluta till en session |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Anslut till fjär åter givnings kontrollen |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Fjärran sluten klient

Ger användaren funktioner för att hantera sessioner, åter givning och diagnostik för Azure fjärrrendering. [Läs mer](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Hämta egenskaper för session |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Starta sessioner |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Stoppa sessioner |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Render/Read | Ansluta till en session |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Anslut till fjär åter givnings kontrollen |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Konto deltagare för spatiala ankare

Låter dig hantera spatiala ankare i ditt konto, men ta inte bort dem [Läs mer](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Skapa spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Identifiera närliggande spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Hämta egenskaper för spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Hitta spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Skicka diagnostikdata för att hjälpa till att förbättra kvaliteten på tjänsten Azure spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Uppdatera egenskaper för spatiala ankare |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Konto ägare för spatiala ankare

Låter dig hantera spatialdata i ditt konto, inklusive att ta bort dem [Läs mer](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Skapa spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Delete | Ta bort avstånds ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Identifiera närliggande spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Hämta egenskaper för spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Hitta spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Skicka diagnostikdata för att hjälpa till att förbättra kvaliteten på tjänsten Azure spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Uppdatera egenskaper för spatiala ankare |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Konto läsare för spatiala ankare

Gör att du kan hitta och läsa egenskaper för spatiala ankare i ditt konto. [Läs mer](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Identifiera närliggande spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Hämta egenskaper för spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Hitta spatiala ankare |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Skicka diagnostikdata för att hjälpa till att förbättra kvaliteten på tjänsten Azure spatiala ankare |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integrering


### <a name="api-management-service-contributor"></a>API Management Service Contributor

Kan hantera tjänsten och API: er [Läs mer](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/* | Skapa och hantera API Management-tjänst |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Rollen API Management tjänst operatör

Kan hantera tjänsten men inte API: er [Läs mer](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/*/Read | Läs API Management tjänst instanser |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/backup/Action | Säkerhetskopiera API Management tjänst till den angivna behållaren i ett användardefinierat lagrings konto |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/Delete | Ta bort API Management tjänst instans |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/Action | Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Managements tjänsten |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/Read | Läs metadata för en API Management tjänst instans |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/Restore/Action | Återställa API Management tjänst från den angivna behållaren i ett användardefinierat lagrings konto |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/Action | Överför TLS/SSL-certifikat för en API Management-tjänst |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/Action | Konfigurera, uppdatera eller ta bort anpassade domän namn för en API Management-tjänst |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/Write | Skapa eller uppdatera API Management tjänst instans |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/Users/Keys/Read | Hämta nycklar kopplade till användaren |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Rollen API Management tjänst läsare

Läs behörighet till tjänst och API: er [Läs mer](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/*/Read | Läs API Management tjänst instanser |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/Read | Läs metadata för en API Management tjänst instans |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | [Microsoft. API Management](resource-provider-operations.md#microsoftapimanagement)/service/Users/Keys/Read | Hämta nycklar kopplade till användaren |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Data ägare för app Configuration

Ger fullständig åtkomst till konfigurations data för appar. [Läs mer](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Write |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Delete |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Konfigurations data läsare för app

Tillåter Läs åtkomst till konfigurations data för appar. [Läs mer](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus data ägare

Ger fullständig åtkomst till Azure Service Bus resurser. [Läs mer](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus data mottagare

Ger åtkomst till Azure Service Bus resurser. [Läs mer](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus data avsändare

Tillåter att åtkomst till Azure Service Bus-resurser skickas. [Läs mer](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Service Bus](resource-provider-operations.md#microsoftservicebus)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack registrerings ägare

Låter dig hantera Azure Stack-registreringar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/Read | Hämta egenskaperna för en Azure Stack Edge-prenumeration |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/*/Action |  |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/Read | Hämtar egenskaperna för en Azure Stack Marketplace-produkt |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Read | Hämtar egenskaperna för en Azure Stack registrering |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription-deltagare

Låter dig hantera EventGrid händelse prenumerations åtgärder. [Läs mer](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Lista globala händelse prenumerationer efter typ av ämne |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/Read | Lista regionala händelse prenumerationer |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/Read | Lista regionala händelse prenumerationer efter TopicType |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-läsare

Låter dig läsa EventGrid händelse prenumerationer. [Läs mer](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Läs en eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Lista globala händelse prenumerationer efter typ av ämne |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/Read | Lista regionala händelse prenumerationer |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/Read | Lista regionala händelse prenumerationer efter TopicType |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>FHIR data deltagare

Rollen ger användaren eller principen fullständig åtkomst till FHIR-data [Läs mer](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/FHIR/Resources/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR data export

Roll låter användare eller huvud användare läsa och exportera FHIR-data [Läs mer](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/FHIR/Resources/Read | Läs FHIR-resurser (inklusive sökning och versions historik).  |
> | Microsoft. HealthcareApis/Services/FHIR/Resources/export/åtgärd | Export åtgärd ($export). |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHIR data läsare

Roll låter användare eller huvud användare läsa FHIR-data [Läs mer](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/FHIR/Resources/Read | Läs FHIR-resurser (inklusive sökning och versions historik).  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR data skrivare

Roll låter användare eller huvud användare läsa och skriva FHIR-data [Läs mer](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/FHIR/Resources/* |  |
> | **NotDataActions** |  |
> | Microsoft. HealthcareApis/service/FHIR/Resources/hardDelete/Action | Hård borttagning (inklusive versions historik). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Integration Service Environment deltagare

Låter dig hantera integrerings tjänst miljöer, men inte till gång till dem. [Läs mer](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Integration Service Environment utvecklare

Gör det möjligt för utvecklare att skapa och uppdatera arbets flöden, integrations konton och API-anslutningar i integrerings tjänst miljöer. [Läs mer](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)-/integrationServiceEnvironments/Read | Läser integrerings tjänst miljön. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)-/integrationServiceEnvironments/Join/Action | Kopplar Integration Service Environment. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Konto deltagare i Intelligent Systems

Gör att du kan hantera intelligenta system konton, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | Microsoft. IntelligentSystems/Accounts/* | Skapa och hantera intelligenta system konton |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Logic app-deltagare

Låter dig hantera Logi Kap par, men ändra inte åtkomsten till dem. [Läs mer](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Visar åtkomst nycklar för lagrings kontona. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Returnera lagrings kontot med det aktuella kontot. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Skapar, uppdaterar eller läser in diagnostikinställningar för Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Den här behörigheten krävs för användare som behöver åtkomst till aktivitets loggar via portalen. Lista logg kategorier i aktivitets loggen. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Läs mått definitioner (lista över tillgängliga mått typer för en resurs). |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/* | Hanterar Logic Apps resurser. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/listkeys/Action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/Read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Skapa och hantera en anslutnings-Gateway. |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/Connections/* | Skapa och hantera en anslutning. |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/customApis/* | Skapar och hanterar en anpassad API. |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/serverFarms/Join/Action | Kopplar en App Service plan |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Hämta egenskaperna för en App Service plan |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/Sites/Functions/listSecrets/Action | Visa en lista över funktions hemligheter. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logic app-operatör

Låter dig läsa, aktivera och inaktivera Logi Kap par, men inte redigera eller uppdatera dem. [Läs mer](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/Read | Läs Insights-aviserings regler |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/Read | Hämtar diagnostikinställningar för Logic Apps |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/Read | Hämtar tillgängliga mått för Logic Apps. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/*/Read | Läser Logic Apps-resurser. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)-/Workflows/Disable/Action | Inaktiverar arbets flödet. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)-/Workflows/Enable/Action | Aktiverar arbets flödet. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)-/Workflows/validate/Action | Verifierar arbets flödet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Operations/Read | Hämtar eller visar distributions åtgärder. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/Read | Läsa anslutnings-gatewayer. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/*/Read | Läsa anslutningar. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/Read | Läs anpassat API. |
> | [Microsoft. Web-](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Hämta egenskaperna för en App Service plan |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identitet


### <a name="managed-identity-contributor"></a>Hanterad identitets deltagare

Skapa, läsa, uppdatera och ta bort användare tilldelad identitet [Läs mer](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Read | Hämtar en befintlig användare tilldelad identitet |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Write | Skapar en ny tilldelad identitet eller uppdaterar de taggar som är associerade med en befintlig användare som tilldelats identiteten |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Delete | Tar bort en befintlig användare tilldelad identitet |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Hanterad identitets operator

Läs och tilldela en användardefinierad identitet [Läs mer](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Read |  |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Assign/Action |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Säkerhet


### <a name="azure-sentinel-contributor"></a>Azure Sentinel-deltagare

Azure Sentinel Contributor [Lär dig mer](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Analytics/Query/Action | Sök med ny motor. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/Read | Visa Log Analytics-data |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Ta slut på OMS-lösning |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/Read | Köra frågor över data i arbets ytan |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/Read | Hämta data källor under en arbets yta. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel-läsare

Azure Sentinel Reader [Läs mer](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Kontrol lera auktorisering och licens för användare |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Analytics/Query/Action | Sök med ny motor. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/Read | Visa Log Analytics-data |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/Read | Hämta länkade tjänster för den aktuella arbets ytan. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/Read | Hämtar en sparad Sök fråga |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Ta slut på OMS-lösning |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/Read | Köra frågor över data i arbets ytan |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/Read | Hämta data källor under en arbets yta. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Workbooks/Read | Läs en arbets bok |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel-svarare

Azure Sentinel-svarare [Lär dig mer](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Kontrol lera auktorisering och licens för användare |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Analytics/Query/Action | Sök med ny motor. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/Read | Visa Log Analytics-data |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/Read | Hämta data källor under en arbets yta. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/Read | Hämtar en sparad Sök fråga |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Ta slut på OMS-lösning |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/Read | Köra frågor över data i arbets ytan |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/Read | Hämta data källor under en arbets yta. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Workbooks/Read | Läs en arbets bok |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault deltagare

Låter dig hantera nyckel valv, men inte åtkomst till dem. [Läs mer](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. nyckel valv](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/PURGE/action för nyckel valv | Rensa ett ej permanent borttaget nyckel valv |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Säkerhetsadministratör

Visa och uppdatera behörigheter för Security Center. Samma behörigheter som säkerhets läsar rollen och kan också uppdatera säkerhets principen och ignorera aviseringar och rekommendationer. [Läs mer](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Skapa och hantera princip tilldelningar |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Skapa och hantera princip definitioner |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Skapa och hantera princip uppsättningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/Read | Visa Log Analytics-data |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Skapa och hantera säkerhets komponenter och principer |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Säkerhets utvärderings deltagare

Gör att du kan skicka utvärderingar till Security Center

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)-/assessments/Write | Skapa eller uppdatera säkerhets utvärderingar i din prenumeration |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Säkerhets hanterare (bakåtkompatibelt)

Detta är en äldre roll. Använd säkerhets administratör i stället.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/Read | Läs konfigurations information klassiska virtuella datorer |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/Write | Skriv konfiguration för klassiska virtuella datorer |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/Read | Läs konfigurations information om klassiskt nätverk |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Skapa och hantera säkerhets komponenter och principer |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Säkerhetsläsare

Visa behörigheter för Security Center. Kan visa rekommendationer, aviseringar, säkerhets principer och säkerhets tillstånd, men kan inte göra ändringar. [Läs mer](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/alertRules/Read | Läs en klassisk måtta avisering |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/Read | Visa Log Analytics-data |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/*/Read |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/*/Read | Läsa säkerhets komponenter och principer |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/*/Read |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs-användare

Låter dig ansluta, starta, starta om och stänga av dina virtuella datorer i din Azure DevTest Labs. [Läs mer](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/availabilitySets/Read | Hämta egenskaperna för en tillgänglighets uppsättning |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read | Läsa egenskaperna för en virtuell dator (VM-storlekar, körnings status, VM-tillägg osv.) |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/deallocate/Action | Stänger av den virtuella datorn och frigör beräknings resurserna |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/Read | Hämta egenskaperna för en virtuell dator |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/restart/Action | Startar om den virtuella datorn |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/start/Action | Startar den virtuella datorn |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/Read | Läsa egenskaperna för ett labb |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/claimAnyVm/Action | Anspråk på en slumpmässig virtuell dator i labbet. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/createEnvironment/Action | Skapa virtuella datorer i ett labb. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/ensureCurrentUserProfile/Action | Se till att den aktuella användaren har en giltig profil i labbet. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Delete | Ta bort formler. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Read | Läs formler. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Write | Lägg till eller ändra formler. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/policySets/evaluatePolicies/Action | Utvärderar labb princip. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/Claim/Action | Bli ägare till en befintlig virtuell dator |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualmachines/listApplicableSchedules/Action | Visar en lista över tillämpliga start-/stopp scheman, om det finns några. |
> | [Microsoft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/getRdpFileContents/Action | Hämtar en sträng som representerar innehållet i RDP-filen för den virtuella datorn |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/backendAddressPools/Join/Action | Ansluter till en backend-adresspool för belastnings utjämning. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/loadBalancers/inboundNatRules/Join/Action | Ansluter en inkommande NAT-regel för belastningsutjämnare. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/*/Read | Läs egenskaperna för ett nätverks gränssnitt (till exempel alla belastningsutjämnare som nätverks gränssnittet är en del av) |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/Join/Action | Ansluter en virtuell dator till ett nätverks gränssnitt. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/Read | Hämtar en definition för nätverks gränssnitt.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/networkInterfaces/Write | Skapar ett nätverks gränssnitt eller uppdaterar ett befintligt nätverks gränssnitt.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/publicIPAddresses/*/Read | Läsa egenskaperna för en offentlig IP-adress |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/publicIPAddresses/Join/Action | Ansluter till en offentlig IP-adress. Det går inte att avisera. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/publicIPAddresses/Read | Hämtar en offentlig IP-adress definition. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Operations/Read | Hämtar eller visar distributions åtgärder. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Read | Hämtar eller visar distributioner. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/listKeys/Action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | **NotActions** |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)-/virtualMachines/vmSizes/Read | Visar en lista över tillgängliga storlekar som den virtuella datorn kan uppdateras till |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Labb skapare

Gör att du kan skapa nya labb under dina Azure Lab-konton. [Läs mer](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/Read |  |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/Action | Skapa ett labb i ett labb konto. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/Action | Få pris och tillgänglighet för kombinationer av storlekar, geografiska områden och operativ system för labb kontot. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/Action | Hämta kärn begränsningar och användning för den här prenumerationen |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Övervaka


### <a name="application-insights-component-contributor"></a>Application Insights komponent deltagare

Kan hantera Application Insights-komponenter [Läs mer](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera klassiska aviserings regler |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Skapa och hantera nya varnings regler |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Skapa och hantera Insights-komponenter |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Skapa och hantera insikter-webbtester |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Ger användaren behörighet att visa och hämta fel söknings ögonblicks bilder som samlats in med Application Insights Snapshot Debugger. Observera att dessa behörigheter inte ingår i [ägaren](#owner) eller [deltagar](#contributor) rollerna. När du ger användarna Application Insights Snapshot Debugger-rollen måste du ge användaren rollen direkt. Rollen identifieras inte när den läggs till i en anpassad roll. [Läs mer](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Övervaknings deltagare

Kan läsa alla övervaknings data och redigera övervaknings inställningar. Se även [komma igång med roller, behörigheter och säkerhet med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). [Läs mer](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Alerts/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Skapa och hantera Insights-komponenter |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Skapar, uppdaterar eller läser in diagnostikinställningar för Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Visa lista över aktivitets logg händelser (hanterings händelser) i en prenumeration. Den här behörigheten gäller för både program mässig och Portal åtkomst till aktivitets loggen. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Den här behörigheten krävs för användare som behöver åtkomst till aktivitets loggar via portalen. Lista logg kategorier i aktivitets loggen. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Läs mått definitioner (lista över tillgängliga mått typer för en resurs). |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Läs mått för en resurs. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/register/Action | Registrera Microsoft Insights-providern |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Skapa och hantera insikter-webbtester |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Write | Skapar en ny arbets yta eller länkar till en befintlig arbets yta genom att ange kund-ID: t från den befintliga arbets ytan. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Läsa/skriva/ta bort lösnings paket för Log Analytics. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Läs/skriv/ta bort sparade Log Analytics-sökningar. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/Action | Kör en Sök fråga |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/Action | Hämtar de delade nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Läsa/skriva/ta bort insikter för Log Analytics-lagring. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* |  |
> | [Microsoft. WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/notificationSettings/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Övervaknings mått utgivare

Möjliggör publicering av mått mot Azure-resurser [Läs mer](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/register/Action | Registrera Microsoft Insights-providern |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Metrics/Write | Skriv mått |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Övervaknings läsare

Kan läsa alla övervaknings data (mått, loggar osv.). Se även [komma igång med roller, behörigheter och säkerhet med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). [Läs mer](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/Action | Kör en Sök fråga |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Arbets boks deltagare

Kan spara delade arbets böcker. [Läs mer](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Workbooks/Write | Skapa eller uppdatera en arbets bok |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Workbooks/Delete | Ta bort en arbetsbok |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Workbooks/Read | Läs en arbets bok |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Arbets boks läsare

Kan läsa arbets böcker. [Läs mer](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)-/Workbooks/Read | Läs en arbets bok |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Hantering och styrning


### <a name="automation-job-operator"></a>Automatiserings jobb operatör

Skapa och hantera jobb med hjälp av Automation-runbooks. [Läs mer](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/hybridRunbookWorkerGroups/Read | Läser Hybrid Runbook Worker resurser |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Read | Hämtar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Resume/Action | Återupptar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/stop/action | Stoppar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Streams/Read | Hämtar en Azure Automation jobb ström |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Suspend/Action | Pausar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Write | Skapar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/output/Read | Hämtar utdata för ett jobb |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation-operatör

Automation-operatörer kan starta, stoppa, pausa och återuppta jobb [Läs mer](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/hybridRunbookWorkerGroups/Read | Läser Hybrid Runbook Worker resurser |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Read | Hämtar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Resume/Action | Återupptar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/stop/action | Stoppar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Streams/Read | Hämtar en Azure Automation jobb ström |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Suspend/Action | Pausar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/Write | Skapar ett Azure Automation jobb |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/jobSchedules/Read | Hämtar ett Azure Automation jobb schema |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/jobSchedules/Write | Skapar ett Azure Automation jobb schema |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/linkedWorkspace/Read | Hämtar arbets ytan som är länkad till Automation-kontot |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Read | Hämtar ett Azure Automation konto |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Runbooks/Read | Hämtar en Azure Automation Runbook |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Schedules/Read | Hämtar en Azure Automation schema till gång |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Schedules/Write | Skapar eller uppdaterar en Azure Automation schema till gång |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Jobs/output/Read | Hämtar utdata för ett jobb |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation Runbook-operator

Läs Runbook-egenskaperna – för att kunna skapa jobb för runbooken. [Läs mer](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)-/automationAccounts/Runbooks/Read | Hämtar en Azure Automation Runbook |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure-ansluten dator onboarding

Kan publicera Azure-anslutna datorer. [Läs mer](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Read | Läs alla Azure Arc-datorer |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Skriver en Azure Arc-dator |
> | [Microsoft. GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/Read | Hämta gäst konfigurations tilldelning. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Resurs administratör för Azure-ansluten dator

Kan läsa, skriva, ta bort och återställa Azure-anslutna datorer.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Read | Läs alla Azure Arc-datorer |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Skriver en Azure Arc-dator |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Delete | Tar bort en Azure Arc-dator |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/reconnect/Action | Återansluter en Azure Arc-dator |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Installerar eller uppdaterar ett Azure Arc-tillägg |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/Read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Faktureringsläsare

Tillåter Läs åtkomst till fakturerings data [Läs mer](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. fakturering](resource-provider-operations.md#microsoftbilling)/*/Read | Läs fakturerings information |
> | [Microsoft. Commerce](resource-provider-operations.md#microsoftcommerce)/*/Read |  |
> | [Microsoft. förbrukning](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Skiss deltagare

Kan hantera skiss definitioner, men tilldela dem inte. [Läs mer](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. skiss](resource-provider-operations.md#microsoftblueprint)-/Blueprints/* | Skapa och hantera skiss definitioner eller skiss artefakter. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Skiss operator

Kan tilldela befintliga publicerade ritningar, men kan inte skapa nya ritningar. Observera att detta endast fungerar om tilldelningen görs med en tilldelad hanterad identitet. [Läs mer](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. skiss](resource-provider-operations.md#microsoftblueprint)-/blueprintAssignments/* | Skapa och hantera skiss tilldelningar. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management deltagare

Kan visa kostnader och hantera kostnads konfiguration (t. ex. budgetar, exporter) [Läs mer](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. förbrukning](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft. Billing](resource-provider-operations.md#microsoftbilling)-/billingPeriods/Read |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)-/Configurations/Read | Hämta konfigurationer |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)-/recommendations/Read | Läser rekommendationer |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management läsare

Kan visa kostnads data och konfiguration (t. ex. budgetar, export) [Läs mer](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. förbrukning](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. Billing](resource-provider-operations.md#microsoftbilling)-/billingPeriods/Read |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)-/Configurations/Read | Hämta konfigurationer |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)-/recommendations/Read | Läser rekommendationer |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Administratör för hierarkiska inställningar

Tillåter användare att redigera och ta bort inställningar för hierarki

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Settings/Write | Skapar eller uppdaterar inställningar för hanterings gruppens hierarki. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Settings/Delete | Tar bort inställningar för hanterings gruppens hierarki. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes-kluster – Azure Arc onboarding

Roll definition som tillåter att alla användare/tjänster skapar connectedClusters-resurs [Läs mer](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Deployments/Write | Skapar eller uppdaterar en distribution. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/operationresults/Read | Hämta prenumerations åtgärds resultatet. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Skriver connectedClusters |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Read | Läs connectedClusters |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Rollen hanterad program deltagare

Gör det möjligt att skapa hanterade program resurser.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/* |  |
> | /Register/action för [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions) | Registrera dig för lösningar. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Rollen hanterad program operatör

Gör att du kan läsa och utföra åtgärder på hanterade program resurser

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | /Applications/Read för [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions) | Hämtar en lista över program. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/*/Action |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Läsare för hanterade program

Låter dig läsa resurser i en hanterad app och begära JIT-åtkomst.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Borttagnings roll för registrering av hanterade tjänster

Ta bort roll för registrering av hanterade tjänster för att hantera klient organisations användare kan ta bort den registrerings tilldelning som tilldelats till klienten. [Läs mer](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Read | Hämtar en lista över uppgifter för registrering av hanterade tjänster. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Delete | Tar bort registrering av hanterade tjänster. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/Read | Läser åtgärds statusen för resursen. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Deltagare i hanterings grupp

Rollen deltagare i hanterings grupp [Läs mer](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Delete | Ta bort hanterings grupp. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Subscriptions/Delete | Ta bort prenumerationen från hanterings gruppen. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Subscriptions/Write | Kopplar en befintlig prenumeration till hanterings gruppen. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Write | Skapa eller uppdatera en hanterings grupp. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Subscriptions/Read | Visar en lista över prenumerationer under den aktuella hanterings gruppen. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Hanterings grupp läsare

Rollen hanterings grupp läsare

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)-/managementGroups/Subscriptions/Read | Visar en lista över prenumerationer under den aktuella hanterings gruppen. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Ny Relic APM-konto deltagare

Låter dig hantera New Relic Application Performance Management konton och program, men inte till gång till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | NewRelic. APM/Accounts/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Data skrivare för princip insikter (för hands version)

Tillåter Läs åtkomst till resurs principer och Skriv behörighet till resurs komponent princip händelser. [Läs mer](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)-/policyassignments/Read | Hämta information om en princip tilldelning. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)-/policydefinitions/Read | Hämta information om en princip definition. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)-/policysetdefinitions/Read | Hämta information om en princip uppsättnings definition. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/Action | Kontrol lera status för efterlevnad för en specifik komponent mot data policys. |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/Action | Logga händelser för resurs komponents princip. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Deltagare för resursprincip

Användare med behörighet att skapa/ändra resurs principer, skapa support ärende och läsa resurser/hierarki. [Läs mer](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Skapa och hantera princip tilldelningar |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Skapa och hantera princip definitioner |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Skapa och hantera princip uppsättningar |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery deltagare

Låter dig hantera Site Recovery tjänst förutom att skapa valv och roll tilldelning mer [information](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/Action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/Write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valvet |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/* | Skapa eller uppdatera aviserings inställningar för replikering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Läs eventuella händelser |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/* | Skapa och hantera infrastruktur resurser |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Skapa och hantera jobb för replikering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/* | Skapa och hantera principer för replikering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/* | Skapa och hantera återställnings planer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Skapa och hantera lagrings konfiguration för Recovery Services valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Returnerar användnings information för ett Recovery Services-valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Valv-token kan användas för att hämta valv-token för Server dels åtgärder på valvnivå. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Läs aviseringar för Recovery Services-valvet |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/Read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationOperationStatus/Read | Läs eventuell åtgärds status för valvets replikering |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery operatör

Låter dig redundansväxla och failback men inte utföra andra Site Recovery hanterings åtgärder [Läs mer](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)-/virtualNetworks/Read | Hämta definition av virtuellt nätverk |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/Action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Läs eventuella aviserings inställningar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Läs eventuella händelser |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/checkConsistency/Action | Kontrollerar konsekvensen för infrastrukturen |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Läs eventuella infrastruktur resurser |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/reassociateGateway/Action | Associera gatewayen igen |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/renewcertificate/Action | Förnya certifikat för infrastruktur resurser |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Läs alla nätverk |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Läs eventuella nätverks mappningar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Läs eventuella skydds behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Läs alla objekt som ska skyddas |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Tillämpa återställnings punkt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Genomför redundans |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Planerad redundans |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Läs alla skyddade objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Läs alla återställnings punkter för replikering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Reparera replikering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/Action | Återaktivera skydd för skyddat objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Växla skydds behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Testa redundans |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Rensning av redundanstest |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Redundans |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Uppdatera mobilitets tjänsten |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Läs alla skydds behållar mappningar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Läs eventuella Recovery Services-leverantörer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Uppdatera Provider |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Läs alla lagrings klassificeringar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Läs alla mappningar för lagrings klassificering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Läs eventuella vCenter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Skapa och hantera jobb för replikering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Läs eventuella principer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/failoverCommit/Action | Återställnings plan för redundans |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/plannedFailover/Action | Återställnings plan för planerad redundansväxling |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Läs eventuella återställnings planer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/reProtect/Action | Skydda återställnings plan |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailover/Action | Återställnings plan för redundanstest |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailoverCleanup/Action | Återställnings plan för rensning av redundanstest |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/unplannedFailover/Action | Återställnings plan för redundans |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Läs aviseringar för Recovery Services-valvet |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Returnerar användnings information för ett Recovery Services-valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Valv-token kan användas för att hämta valv-token för Server dels åtgärder på valvnivå. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)-/storageAccounts/Read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery läsare

Låter dig Visa Site Recovery status men inte utföra andra hanterings åtgärder [Läs mer](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Hämtar aviseringarna för Recovery Services-valvet. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Läs eventuella aviserings inställningar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Läs eventuella händelser |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Läs eventuella infrastruktur resurser |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Läs alla nätverk |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Läs eventuella nätverks mappningar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Läs eventuella skydds behållare |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Läs alla objekt som ska skyddas |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Läs alla skyddade objekt |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Läs alla återställnings punkter för replikering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Läs alla skydds behållar mappningar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Läs eventuella Recovery Services-leverantörer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Läs alla lagrings klassificeringar |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Läs alla mappningar för lagrings klassificering |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Läs eventuella vCenter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/Read | Läs alla jobb |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Läs eventuella principer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Läs eventuella återställnings planer |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/Read | Returnerar användnings information för ett Recovery Services-valv. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Valv-token kan användas för att hämta valv-token för Server dels åtgärder på valvnivå. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Support förfrågan deltagare

Gör att du kan skapa och hantera support förfrågningar mer [information](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Tagga deltagare

Låter dig hantera Taggar i entiteter utan att ge åtkomst till själva entiteterna. [Läs mer](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Resources/Read | Hämtar resurser för resurs gruppen. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/Resources/Read | Hämtar resurser för en prenumeration. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Tags/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Övrigt


### <a name="biztalk-contributor"></a>BizTalk-deltagare

Gör att du kan hantera BizTalk Services, men inte till gång till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | Microsoft. BizTalkServices/BizTalk/* | Skapa och hantera BizTalk Services |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Skriv bords Virtualization-användare

Tillåter användare att använda programmen i en program grupp. [Läs mer](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft. DesktopVirtualization/applicationGroups/useApplications/Action | Använd variabeln applicationgroup |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Jobb samlings deltagare i Scheduler

Gör att du kan hantera jobb samlingar i Scheduler, men inte till gång till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Läs roller och roll tilldelningar |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måtta avisering |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Skapa och hantera en distribution |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)-/Subscriptions/resourceGroups/Read | Hämtar eller listar resurs grupper. |
> | [Microsoft. Scheduler](resource-provider-operations.md#microsoftscheduler)-/jobcollections/* | Skapa och hantera jobb samlingar |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera ett support ärende |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Nästa steg

- [Matcha Resource Provider till tjänst](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Behörigheter i Azure Security Center](../security-center/security-center-permissions.md)
