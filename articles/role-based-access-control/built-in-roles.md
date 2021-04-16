---
title: Inbyggda Azure-roller – Azure RBAC
description: I den här artikeln beskrivs de inbyggda Azure-rollerna för rollbaserad åtkomstkontroll i Azure (Azure RBAC). Den visar åtgärder, NotActions, DataActions och NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 04/09/2021
ms.custom: generated
ms.openlocfilehash: c89e6ed98e0a71f530cefda4cc1f42a27996d805
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518515"
---
# <a name="azure-built-in-roles"></a>Inbyggda roller i Azure

[Rollbaserad åtkomstkontroll i Azure (Azure RBAC)](overview.md) har flera inbyggda Azure-roller som du kan tilldela till användare, grupper, tjänsthuvudnamn och hanterade identiteter. Rolltilldelningar är det sätt som du styr åtkomsten till Azure-resurser. Om de inbyggda rollerna inte uppfyller organisationens specifika behov kan du skapa egna anpassade [Azure-roller.](custom-roles.md) Information om hur du tilldelar roller finns i [Steg för att tilldela en Azure-roll.](role-assignments-steps.md)

I den här artikeln visas de inbyggda Rollerna i Azure. Om du letar efter administratörsroller för Azure Active Directory (Azure AD) kan du se [Inbyggda roller i Azure AD.](../active-directory/roles/permissions-reference.md)

Följande tabell innehåller en kort beskrivning av varje inbyggd roll. Klicka på rollnamnet för att visa listan `Actions` över , , och för varje `NotActions` `DataActions` `NotDataActions` roll. Information om vad de här åtgärderna innebär och hur de tillämpas på hanterings- och dataplan finns i [Förstå rolldefinitioner för Azure.](role-definitions.md)

## <a name="all"></a>Alla

> [!div class="mx-tableFixed"]
> | Inbyggd roll | Description | ID |
> | --- | --- | --- |
> | **Allmänt** |  |  |
> | [Deltagare](#contributor) | Ger fullständig åtkomst för att hantera alla resurser, men tillåter inte att du tilldelar roller i Azure RBAC, hanterar tilldelningar i Azure Blueprints eller delar bildgallerier. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Ägare](#owner) | Ger fullständig åtkomst för att hantera alla resurser, inklusive möjligheten att tilldela roller i Azure RBAC. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Läsare](#reader) | Visa alla resurser, men tillåter inte att du gör några ändringar. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administratör för användaråtkomst](#user-access-administrator) | Låter dig hantera användaråtkomst till Azure-resurser. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Beräkning** |  |  |
> | [Klassisk virtuell datordeltagare](#classic-virtual-machine-contributor) | Låter dig hantera klassiska virtuella datorer, men inte åtkomst till dem, och inte det virtuella nätverket eller lagringskontot som de är anslutna till. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Administratörsinloggning för virtuell dator](#virtual-machine-administrator-login) | Visa Virtual Machines i portalen och logga in som administratör | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtuell datordeltagare](#virtual-machine-contributor) | Låter dig hantera virtuella datorer, men inte åtkomst till dem, och inte det virtuella nätverket eller lagringskontot som de är anslutna till. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Användarinloggning för virtuell dator](#virtual-machine-user-login) | Visa Virtual Machines i portalen och logga in som en vanlig användare. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Nätverk** |  |  |
> | [CDN-slutpunktsdeltagare](#cdn-endpoint-contributor) | Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-slutpunktsläsare](#cdn-endpoint-reader) | Kan visa CDN-slutpunkter, men kan inte göra ändringar. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN-profildeltagare](#cdn-profile-contributor) | Kan hantera CDN-profiler och deras slutpunkter, men kan inte bevilja åtkomst till andra användare. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profilläsare](#cdn-profile-reader) | Kan visa CDN-profiler och deras slutpunkter, men kan inte göra ändringar. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Klassisk nätverksdeltagare](#classic-network-contributor) | Låter dig hantera klassiska nätverk, men inte åtkomst till dem. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS-zondeltagare](#dns-zone-contributor) | Låter dig hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte styra vem som har åtkomst till dem. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Nätverksdeltagare](#network-contributor) | Låter dig hantera nätverk, men inte åtkomst till dem. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Privat DNS zondeltagare](#private-dns-zone-contributor) | Låter dig hantera privata DNS-zonresurser, men inte de virtuella nätverk som de är länkade till. | b12aaa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager deltagare](#traffic-manager-contributor) | Låter dig hantera Traffic Manager profiler, men låter dig inte styra vem som har åtkomst till dem. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Avere-deltagare](#avere-contributor) | Kan skapa och hantera ett Avere vFXT kluster. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere-operator](#avere-operator) | Används av Avere vFXT för att hantera klustret | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Säkerhetskopieringsdeltagare](#backup-contributor) | Låter dig hantera säkerhetskopieringstjänsten, men kan inte skapa valv och ge åtkomst till andra | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operator för säkerhetskopiering](#backup-operator) | Låter dig hantera säkerhetskopieringstjänster, förutom borttagning av säkerhetskopiering, valvskapande och att ge åtkomst till andra | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Backup Reader](#backup-reader) | Kan visa säkerhetskopieringstjänster, men kan inte göra ändringar | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Klassisk lagringskontodeltagare](#classic-storage-account-contributor) | Låter dig hantera klassiska lagringskonton, men inte åtkomst till dem. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Tjänstroll för nyckeloperatör för klassisk lagringskonto](#classic-storage-account-key-operator-service-role) | Operatorer för klassiska lagringskontonycklar kan visa och återskapa nycklar på klassiska lagringskonton | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box-enhet deltagare](#data-box-contributor) | Låter dig hantera allt under Data Box-enhet service förutom att ge åtkomst till andra. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box-enhet Läsare](#data-box-reader) | Låter dig hantera Data Box-enhet-tjänsten förutom att skapa beställnings- eller redigeringsinformation och ge åtkomst till andra. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics Utvecklare](#data-lake-analytics-developer) | Låter dig skicka, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics konton. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Läsar- och dataåtkomst](#reader-and-data-access) | Låter dig visa allt men låter dig inte ta bort eller skapa ett lagringskonto eller en innesluten resurs. Det ger också läs-/skrivåtkomst till alla data som finns i ett lagringskonto via åtkomst till lagringskontonycklar. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Lagringskontodeltagare](#storage-account-contributor) | Tillåter hantering av lagringskonton. Ger åtkomst till kontonyckeln, som kan användas för att komma åt data via auktorisering med delad nyckel. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Tjänstroll för nyckeloperatör för lagringskonto](#storage-account-key-operator-service-role) | Tillåter att åtkomstnycklar för lagringskontot listas och återskapas. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage Blob Data-deltagare](#storage-blob-data-contributor) | Läsa, skriva och ta bort Azure Storage containrar och blobar. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage Blob Data-ägare](#storage-blob-data-owner) | Ger fullständig åtkomst till Azure Storage blobcontainrar och data, inklusive tilldelning av POSIX-åtkomstkontroll. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage Blob Data-läsare](#storage-blob-data-reader) | Läs och lista Azure Storage containrar och blobar. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i Behörigheter för [att anropa blob- och ködataåtgärder.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage Blob Dele blob](#storage-blob-delegator) | Hämta en nyckel för användardelegering som sedan kan användas för att skapa en signatur för delad åtkomst för en container eller blob som är signerad med Azure AD-autentiseringsuppgifter. Mer information finns i Skapa [en SAS för användardelegering.](/rest/api/storageservices/create-user-delegation-sas) | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Storage-fildata för SMB-resursdeltagare](#storage-file-data-smb-share-contributor) | Tillåter läs-, skriv- och borttagningsåtkomst för filer/kataloger i Azure-filresurser. Den här rollen har ingen inbyggd motsvarighet på Windows-filservrar. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage-fildata för upphöjd SMB-resursdeltagare](#storage-file-data-smb-share-elevated-contributor) | Tillåter läsning, skrivning, borttagning och ändring av ACL:er för filer/kataloger i Azure-filresurser. Den här rollen motsvarar en filresurs-ACL för ändring på Windows-filservrar. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage-fildata för SMB-resursläsare](#storage-file-data-smb-share-reader) | Tillåter läsåtkomst för filer/kataloger i Azure-filresurser. Den här rollen motsvarar en filresurs-ACL för läsning på Windows-filservrar. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Storage Queue Data-deltagare](#storage-queue-data-contributor) | Läsa, skriva och ta bort Azure Storage köer och kömeddelanden. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i Behörigheter för [att anropa blob- och ködataåtgärder.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Storage Queue Data Message Processor](#storage-queue-data-message-processor) | Granska, hämta och ta bort ett meddelande från en Azure Storage kö. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i Behörigheter för [att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Avsändare av storage queue-datameddelande](#storage-queue-data-message-sender) | Lägga till meddelanden i en Azure Storage kö. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i Behörigheter för [att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Storage Queue Data Reader](#storage-queue-data-reader) | Läs och lista Azure Storage köer och kömeddelanden. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i Behörigheter för [att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Webb** |  |  |
> | [Azure Maps datadeltagare](#azure-maps-data-contributor) | Ger åtkomst till att läsa, skriva och ta bort åtkomst till kartrelaterade data från ett Azure Maps-konto. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Azure Maps Data Reader](#azure-maps-data-reader) | Ger åtkomst till läsmappningsrelaterade data från ett Azure Maps-konto. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Spring Cloud dataläsare](#azure-spring-cloud-data-reader) | Tillåt läsåtkomst till Azure Spring Cloud data | b5537268-8956-4941-a8f0-646150406f0c |
> | [Search Service Contributor](#search-service-contributor) | Låter dig hantera Söktjänster, men inte åtkomst till dem. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [SignalR AccessKey Reader](#signalr-accesskey-reader) | Läs SignalR Service åtkomstnycklar | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR App Server (förhandsversion)](#signalr-app-server-preview) | Låter din appserver komma åt SignalR Service med AAD-autentiseringsalternativ. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [SignalR-deltagare](#signalr-contributor) | Skapa, läsa, uppdatera och ta bort SignalR Service-resurser | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [SignalR-serverlös deltagare (förhandsversion)](#signalr-serverless-contributor-preview) | Låter din app komma åt tjänsten i serverlöst läge med AAD-autentiseringsalternativ. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR Service ägare (förhandsversion)](#signalr-service-owner-preview) | Fullständig åtkomst till Azure SignalR Service REST-API:er | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR Service Reader (förhandsversion)](#signalr-service-reader-preview) | Skrivskyddade åtkomst till rest Azure SignalR Service-API:er | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Webbplansdeltagare](#web-plan-contributor) | Låter dig hantera webbplaner för webbplatser, men inte åtkomst till dem. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Webbplatsdeltagare](#website-contributor) | Låter dig hantera webbplatser (inte webbplaner), men inte åtkomst till dem. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containrar** |  |  |
> | [AcrDelete](#acrdelete) | acr delete | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr image signer | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | acr quarantine data reader | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr quarantine data writer | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service administratörsroll för kluster](#azure-kubernetes-service-cluster-admin-role) | Visa en lista över åtgärden för klusteradministratörsbehörighet. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service användarroll för kluster](#azure-kubernetes-service-cluster-user-role) | Visa en lista över åtgärden för klusteranvändares autentiseringsuppgifter. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes Service deltagarroll](#azure-kubernetes-service-contributor-role) | Ger åtkomst till Azure Kubernetes Service skrivskyddade kluster | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Service RBAC-administratör](#azure-kubernetes-service-rbac-admin) | Låter dig hantera alla resurser under kluster/namnområde, förutom att uppdatera eller ta bort resurskvoter och namnområden. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service RBAC-klusteradministratör](#azure-kubernetes-service-rbac-cluster-admin) | Låter dig hantera alla resurser i klustret. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC-läsare](#azure-kubernetes-service-rbac-reader) | Tillåter skrivskyddade åtkomst för att se de flesta objekt i ett namnområde. Det tillåter inte visning av roller eller rollbindningar. Den här rollen tillåter inte visning av hemligheter, eftersom läsning av innehållet i hemligheter ger åtkomst till ServiceAccount-autentiseringsuppgifter i namnområdet, vilket skulle tillåta API-åtkomst som alla ServiceAccount i namnområdet (en form av behörighetseskalering). Om du tillämpar den här rollen i klusteromfång får du åtkomst till alla namnområden. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC Writer](#azure-kubernetes-service-rbac-writer) | Tillåter läs-/skrivåtkomst till de flesta objekt i ett namnområde. Den här rollen tillåter inte visning eller ändring av roller eller rollbindningar. Den här rollen tillåter dock åtkomst till hemligheter och poddar som alla ServiceAccount i namnområdet, så den kan användas för att få API-åtkomstnivåer för alla ServiceAccount i namnområdet. Om du tillämpar den här rollen i klusteromfång får du åtkomst till alla namnområden. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Databaser** |  |  |
> | [Cosmos DB rollen Kontoläsare](#cosmos-db-account-reader-role) | Kan läsa Azure Cosmos DB-kontodata. Se [DocumentDB-kontodeltagare för](#documentdb-account-contributor) att hantera Azure Cosmos DB konton. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB operator](#cosmos-db-operator) | Låter dig hantera Azure Cosmos DB-konton, men inte komma åt data i dem. Förhindrar åtkomst till kontonycklar och anslutningssträngar. | 230815da-be43-4aae-9cb4-875f7bd000aaa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kan skicka återställningsbegäran för en Cosmos DB databas eller en container för ett konto | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Kan utföra återställningsåtgärd för Cosmos DB databaskonto med kontinuerligt säkerhetskopieringsläge | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [DocumentDB-kontodeltagare](#documentdb-account-contributor) | Kan hantera Azure Cosmos DB konton. Azure Cosmos DB kallades tidigare DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache deltagare](#redis-cache-contributor) | Låter dig hantera Redis-cacher, men inte åtkomst till dem. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB-deltagare](#sql-db-contributor) | Låter dig hantera SQL-databaser, men inte åtkomst till dem. Du kan inte heller hantera deras säkerhetsrelaterade principer eller deras överordnade SQL-servrar. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL Managed Instance deltagare](#sql-managed-instance-contributor) | Låter dig hantera SQL Managed Instances och nödvändig nätverkskonfiguration, men kan inte ge åtkomst till andra. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-säkerhetshanteraren](#sql-security-manager) | Låter dig hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server deltagare](#sql-server-contributor) | Låter dig hantera SQL-servrar och databaser, men inte åtkomst till dem, och inte deras säkerhetsrelaterade principer. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analys** |  |  |
> | [Azure Event Hubs dataägare](#azure-event-hubs-data-owner) | Ger fullständig åtkomst till Azure Event Hubs resurser. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs datamottagare](#azure-event-hubs-data-receiver) | Tillåter åtkomst till Azure Event Hubs resurser. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs Data Sender](#azure-event-hubs-data-sender) | Tillåter att åtkomst skickas till Azure Event Hubs resurser. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory deltagare](#data-factory-contributor) | Skapa och hantera datafabriker samt underordnade resurser i dem. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Datarensning](#data-purger) | Ta bort privata data från en Log Analytics-arbetsyta. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight-klusteroperator](#hdinsight-cluster-operator) | Gör att du kan läsa och ändra HDInsight-klusterkonfigurationer. | 61ed4efc-fel3-44fd-b111-e24485cc132a |
> | [HDInsight Domain Services-deltagare](#hdinsight-domain-services-contributor) | Kan läsa, skapa, ändra och ta bort Domain Services-relaterade åtgärder som krävs för HDInsight-Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics-deltagare kan läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningar omfattar att lägga till VM-tillägget till virtuella datorer. läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure Storage; skapa och konfigurera Automation-konton; lägga till lösningar; och konfigurera Azure Diagnostics på alla Azure-resurser. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics Reader](#log-analytics-reader) | Log Analytics-läsare kan visa och söka i alla övervakningsdata samt visa övervakningsinställningar, inklusive att visa konfigurationen av Azure Diagnostics på alla Azure-resurser. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Vy över dataöversiktsman](#purview-data-curator) | Dataöversikten Microsoft.Purview kan skapa, läsa, ändra och ta bort katalogdataobjekt och upprätta relationer mellan objekt. Den här rollen är i förhandsversion och kan komma att ändras. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Purview Data Reader](#purview-data-reader) | Dataläsaren Microsoft.Purview kan läsa katalogdataobjekt. Den här rollen är i förhandsversion och kan komma att ändras. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Purview Data Source Administrator](#purview-data-source-administrator) | Datakällsadministratören Microsoft.Purview kan hantera datakällor och genomsökningar. Den här rollen är i förhandsversion och kan komma att ändras. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Schema Registry-deltagare (förhandsversion)](#schema-registry-contributor-preview) | Läsa, skriva och ta bort schemaregistergrupper och scheman. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Schema Registry Reader (förhandsversion)](#schema-registry-reader-preview) | Läs och lista schemaregistergrupper och scheman. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockkedja** |  |  |
> | [Åtkomst till blockkedjemedlemsnod (förhandsversion)](#blockchain-member-node-access-preview) | Tillåter åtkomst till blockkedjemedlemsnoder | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + maskininlärning** |  |  |
> | [Cognitive Services deltagare](#cognitive-services-contributor) | Låter dig skapa, läsa, uppdatera, ta bort och hantera nycklar för Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Custom Vision deltagare](#cognitive-services-custom-vision-contributor) | Fullständig åtkomst till projektet, inklusive möjligheten att visa, skapa, redigera eller ta bort projekt. | c1ff6cc2-c111-46fe-8896-e0ef812ad9f3 |
> | [Cognitive Services Custom Vision distribution](#cognitive-services-custom-vision-deployment) | Publicera, avpublicera eller exportera modeller. Distributionen kan visa projektet men kan inte uppdatera. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Cognitive Services Custom Vision Labeler](#cognitive-services-custom-vision-labeler) | Visa, redigera träningsbilder och skapa, lägga till, ta bort eller ta bort bildtaggar. Etiketterare kan visa projektet men kan inte uppdatera något annat än träningsbilder och taggar. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Cognitive Services Custom Vision Läsare](#cognitive-services-custom-vision-reader) | Skrivskyddade åtgärder i projektet. Läsare kan inte skapa eller uppdatera projektet. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Custom Vision utbildare](#cognitive-services-custom-vision-trainer) | Visa, redigera projekt och träna modellerna, inklusive möjligheten att publicera, avpublicera och exportera modellerna. Utbildare kan inte skapa eller ta bort projektet. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Cognitive Services dataläsare (förhandsversion)](#cognitive-services-data-reader-preview) | Låter dig läsa Cognitive Services data. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services Metrics Advisor administratör](#cognitive-services-metrics-advisor-administrator) | Fullständig åtkomst till projektet, inklusive konfiguration på systemnivå. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Cognitive Services QnA Maker Editor](#cognitive-services-qna-maker-editor) | Nu ska vi skapa, redigera, importera och exportera en KUNSKAPSB. Du kan inte publicera eller ta bort en kunskapsbasen. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Cognitive Services QnA Maker Läsare](#cognitive-services-qna-maker-reader) | Nu ska vi läsa och testa en kunskapsbasen. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Cognitive Services användare](#cognitive-services-user) | Låter dig läsa och lista nycklar för Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Sakernas Internet** |  |  |
> | [Administratör för enhetsuppdatering](#device-update-administrator) | Ger dig fullständig åtkomst till hanterings- och innehållsåtgärder | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [Innehållsadministratör för enhetsuppdatering](#device-update-content-administrator) | Ger dig fullständig åtkomst till innehållsåtgärder | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [Innehållsläsare för enhetsuppdatering](#device-update-content-reader) | Ger dig läsåtkomst till innehållsåtgärder, men tillåter inte ändringar | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [Administratör för enhetsuppdateringsdistributioner](#device-update-deployments-administrator) | Ger dig fullständig åtkomst till hanteringsåtgärder | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [Läsare för enhetsuppdateringsdistributioner](#device-update-deployments-reader) | Ger läsåtkomst till hanteringsåtgärder, men tillåter inte ändringar | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [Läsare för enhetsuppdatering](#device-update-reader) | Ger läsåtkomst till hanterings- och innehållsåtgärder, men tillåter inte ändringar | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | **Mixad verklighet** |  |  |
> | [Remote Rendering administratör](#remote-rendering-administrator) | Ger användaren funktioner för konvertering, hantering av session, rendering och diagnostik för Azure Remote Rendering | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Remote Rendering Klient](#remote-rendering-client) | Ger användaren hanterings-, renderings- och diagnostikfunktioner för Azure Remote Rendering. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Spatial Anchors kontodeltagare](#spatial-anchors-account-contributor) | Låter dig hantera spatiala fästpunkter i ditt konto, men inte ta bort dem | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors kontoägare](#spatial-anchors-account-owner) | Låter dig hantera spatiala fästpunkter i ditt konto, inklusive att ta bort dem | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors-kontoläsare](#spatial-anchors-account-reader) | Gör att du kan hitta och läsa egenskaper för spatiala fästpunkter i ditt konto | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integrering** |  |  |
> | [API Management tjänstdeltagare](#api-management-service-contributor) | Kan hantera tjänsten och API:erna | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management tjänstoperatörsroll](#api-management-service-operator-role) | Kan hantera tjänsten men inte API:erna | e022efe7-f5ba-4159-bbe4-b44f57e9b61 |
> | [API Management-tjänstläsarroll](#api-management-service-reader-role) | Skrivskyddade åtkomst till tjänsten och API:er | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration dataägare](#app-configuration-data-owner) | Ger fullständig åtkomst till App Configuration data. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration Data Reader](#app-configuration-data-reader) | Tillåter läsåtkomst till App Configuration data. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus dataägare](#azure-service-bus-data-owner) | Ger fullständig åtkomst till Azure Service Bus resurser. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus datamottagare](#azure-service-bus-data-receiver) | Ger åtkomst till Azure Service Bus resurser. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus Data Sender](#azure-service-bus-data-sender) | Tillåter att skicka åtkomst till Azure Service Bus resurser. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack registreringsägare](#azure-stack-registration-owner) | Låter dig hantera Azure Stack registreringar. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid-deltagare](#eventgrid-contributor) | Låter dig hantera EventGrid-åtgärder. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [EventGrid EventSubscription-deltagare](#eventgrid-eventsubscription-contributor) | Låter dig hantera eventGrid-händelseprenumerationsåtgärder. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-läsare](#eventgrid-eventsubscription-reader) | Låter dig läsa EventGrid-händelseprenumerationer. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR-datadeltagare](#fhir-data-contributor) | Rollen tillåter fullständig åtkomst för användare eller huvudnamn till FHIR-data | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR Data Exporter](#fhir-data-exporter) | Rollen tillåter användare eller huvudnamn att läsa och exportera FHIR-data | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR-dataläsare](#fhir-data-reader) | Rollen tillåter användare eller huvudnamn att läsa FHIR-data | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR-dataskrivare](#fhir-data-writer) | Rollen tillåter användare eller huvudnamn att läsa och skriva FHIR-data | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Integration Service Environment deltagare](#integration-service-environment-contributor) | Låter dig hantera integreringstjänstmiljöer, men inte åtkomst till dem. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Integration Service Environment Utvecklare](#integration-service-environment-developer) | Gör att utvecklare kan skapa och uppdatera arbetsflöden, integrationskonton och API-anslutningar i integrationstjänstmiljöer. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Intelligent Systems Account Contributor](#intelligent-systems-account-contributor) | Låter dig hantera konton för intelligenta system, men inte åtkomst till dem. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logic App-deltagare](#logic-app-contributor) | Låter dig hantera logikappar, men inte ändra åtkomsten till dem. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic App-operatör](#logic-app-operator) | Låter dig läsa, aktivera och inaktivera logikappar, men inte redigera eller uppdatera dem. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identitet** |  |  |
> | [Hanterad identitetsdeltagare](#managed-identity-contributor) | Skapa, läsa, uppdatera och ta bort användar tilldelad identitet | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operatör för hanterad identitet](#managed-identity-operator) | Läsa och tilldela användar tilldelad identitet | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Säkerhet** |  |  |
> | [Attestation-deltagare](#attestation-contributor) | Kan läsa skriv- eller borttagning av attestationsproviderinstansen | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Attestation Reader](#attestation-reader) | Kan läsa egenskaperna för attestationsprovidern | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel Automation-deltagare](#azure-sentinel-automation-contributor) | Azure Sentinel Automation-deltagare | f4c81013-99ee-4d62-a7ee-b3f1f648599a |
> | [Azure Sentinel-deltagare](#azure-sentinel-contributor) | Azure Sentinel-deltagare | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel-läsare](#azure-sentinel-reader) | Azure Sentinel-läsare | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel-svarare](#azure-sentinel-responder) | Azure Sentinel-svarare | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault administratör](#key-vault-administrator) | Utför alla dataplansåtgärder på ett nyckelvalv och alla objekt i det, inklusive certifikat, nycklar och hemligheter. Det går inte att hantera nyckelvalvsresurser eller rolltilldelningar. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault certifikatansvarig](#key-vault-certificates-officer) | Utför en åtgärd på certifikaten för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Key Vault deltagare](#key-vault-contributor) | Hantera nyckelvalv, men tillåter inte att du tilldelar roller i Azure RBAC och ger dig inte åtkomst till hemligheter, nycklar eller certifikat. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault Crypto Officer](#key-vault-crypto-officer) | Utför en åtgärd på nycklarna för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault kryptografitjänstkrypteringsanvändare](#key-vault-crypto-service-encryption-user) | Läs metadata för nycklar och utför radbrytningsåtgärder. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault Crypto-användare](#key-vault-crypto-user) | Utföra kryptografiska åtgärder med hjälp av nycklar. Fungerar bara för nyckelvalv som använder behörighetsmodellen rollbaserad åtkomstkontroll i Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault Läsare](#key-vault-reader) | Läs metadata för nyckelvalv och dess certifikat, nycklar och hemligheter. Det går inte att läsa känsliga värden, till exempel hemligt innehåll eller nyckelmaterial. Fungerar bara för nyckelvalv som använder behörighetsmodellen rollbaserad åtkomstkontroll i Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault Secrets Officer](#key-vault-secrets-officer) | Utför en åtgärd på hemligheterna för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault hemlighetsanvändare](#key-vault-secrets-user) | Läsa hemligt innehåll. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure". | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Hanterad HSM-deltagare](#managed-hsm-contributor) | Låter dig hantera hanterade HSM-pooler, men inte åtkomst till dem. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Säkerhetsadministratör](#security-admin) | Visa och uppdatera behörigheter för Security Center. Samma behörigheter som rollen Säkerhetsläsare och kan även uppdatera säkerhetsprincipen och ignorera aviseringar och rekommendationer. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Deltagare i säkerhetsutvärdering](#security-assessment-contributor) | Låter dig skicka utvärderingar till Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Security Manager (äldre)](#security-manager-legacy) | Det här är en äldre roll. Använd säkerhetsadministratören i stället. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Säkerhetsläsare](#security-reader) | Visa behörigheter för Security Center. Kan visa rekommendationer, aviseringar, en säkerhetsprincip och säkerhets tillstånd, men kan inte göra ändringar. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs-användare](#devtest-labs-user) | Låter dig ansluta, starta, starta om och stänga av dina virtuella datorer i Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Labbskapare](#lab-creator) | Gör att du kan skapa nya labb under dina Azure Lab-konton. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Övervaka** |  |  |
> | [Application Insights komponentdeltagare](#application-insights-component-contributor) | Kan hantera Application Insights komponenter | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Ger användaren behörighet att visa och ladda ned ögonblicksbilder av felsökning som samlats in med Application Insights Snapshot Debugger. Observera att dessa behörigheter inte ingår i [rollerna Ägare](#owner) [eller](#contributor) Deltagare. När du ger Application Insights Snapshot Debugger rollen måste du bevilja rollen direkt till användaren. Rollen känns inte igen när den läggs till i en anpassad roll. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Övervakningsdeltagare](#monitoring-contributor) | Kan läsa alla övervakningsdata och redigera övervakningsinställningar. Se även [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Övervakningsmåttutgivare](#monitoring-metrics-publisher) | Aktiverar publiceringsmått mot Azure-resurser | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Övervakningsläsare](#monitoring-reader) | Kan läsa alla övervakningsdata (mått, loggar osv.). Se även [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Arbetsboksdeltagare](#workbook-contributor) | Kan spara delade arbetsböcker. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Arbetsboksläsare](#workbook-reader) | Kan läsa arbetsböcker. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Hantering och styrning** |  |  |
> | [Automation-jobboperator](#automation-job-operator) | Skapa och hantera jobb med Automation Runbooks. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operatör](#automation-operator) | Automation-operatörer kan starta, stoppa, pausa och återuppta jobb | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook-operatör](#automation-runbook-operator) | Läsa Runbook-egenskaper – för att kunna skapa jobb för runbooken. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Arc aktiverat kubernetes-klusteranvändarroll](#azure-arc-enabled-kubernetes-cluster-user-role) | Visa en lista över åtgärden för autentiseringsuppgifter för klusteranvändare. | 00493d72-78f6-4148-b6c5-d3ce8e4799ddd |
> | [Azure Arc Kubernetes-administratör](#azure-arc-kubernetes-admin) | Låter dig hantera alla resurser under kluster/namnområde, förutom att uppdatera eller ta bort resurskvoter och namnområden. | dffb1e0c-446f-4dde-a09f-99eb5cc68b96 |
> | [Azure Arc Kubernetes-klusteradministratör](#azure-arc-kubernetes-cluster-admin) | Låter dig hantera alla resurser i klustret. | 8393591c-06b9-48a2-a542-1bd6b377f6a2 |
> | [Azure Arc Kubernetes Viewer](#azure-arc-kubernetes-viewer) | Låter dig visa alla resurser i kluster/namnrymd, förutom hemligheter. | 63f0a09d-1495-4db4-a681-037d84835eb4 |
> | [Azure Arc Kubernetes Writer](#azure-arc-kubernetes-writer) | Låter dig uppdatera allt i kluster/namnrymd, förutom (kluster)roller och (kluster)rollbindningar. | 5b999177-9696-4545-85c7-50de3797e5a1 |
> | [Azure Connected Machine Onboarding](#azure-connected-machine-onboarding) | Kan publicera Azure Connected Machines. | b64e21ea-ac4e-4cdf-9dc9-5b892992 tb7 |
> | [Azure Connected Machine resursadministratör](#azure-connected-machine-resource-administrator) | Kan läsa, skriva, ta bort och publicera Azure Connected Machines på nya sätt. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Faktureringsläsare](#billing-reader) | Tillåter läsåtkomst till faktureringsdata | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Skissdeltagare](#blueprint-contributor) | Kan hantera skissdefinitioner, men inte tilldela dem. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Skissoperator](#blueprint-operator) | Kan tilldela befintliga publicerade skisser, men kan inte skapa nya skisser. Observera att detta endast fungerar om tilldelningen görs med en användar tilldelad hanterad identitet. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management-deltagare](#cost-management-contributor) | Kan visa kostnader och hantera kostnadskonfiguration (t.ex. budgetar, exporter) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management Läsare](#cost-management-reader) | Kan visa kostnadsdata och konfiguration (t.ex. budgetar, exporter) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Administratör för hierarkiinställningar](#hierarchy-settings-administrator) | Tillåter användare att redigera och ta bort hierarkiinställningar | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes-kluster – Azure Arc onboarding](#kubernetes-cluster---azure-arc-onboarding) | Rolldefinition för att auktorisera alla användare/tjänster att skapa connectedClusters-resurs | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Deltagarroll för hanterat program](#managed-application-contributor-role) | Gör det möjligt att skapa hanterade programresurser. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Operatörsroll för hanterat program](#managed-application-operator-role) | Låter dig läsa och utföra åtgärder på hanterade programresurser | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Läsare för hanterade program](#managed-applications-reader) | Låter dig läsa resurser i en hanterad app och begära JIT-åtkomst. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Tilldelning av registrering av hanterade tjänster Ta bort roll](#managed-services-registration-assignment-delete-role) | Med borttagningsrollen för tilldelning av hanterade tjänster kan användare av hanterade tjänster ta bort den registreringstilldelning som tilldelats deras klientorganisation. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Deltagare i hanteringsgrupp](#management-group-contributor) | Deltagarroll för hanteringsgrupp | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Läsare för hanteringsgrupp](#management-group-reader) | Läsarroll för hanteringsgrupp | ac63b705-f282-497d-ac71-919bf39d939d |
> | [New Relic APM-kontodeltagare](#new-relic-apm-account-contributor) | Låter dig hantera New Relic Application Performance Management och program, men inte åtkomst till dem. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Dataskrivare för Policy Insights (förhandsversion)](#policy-insights-data-writer-preview) | Ger läsåtkomst till resursprinciper och skrivåtkomst till resurskomponentprinciphändelser. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Operator för kvotbegäran](#quota-request-operator) | Läs och skapa kvotbegäranden, få status för kvotbegäran och skapa supportärenden. | 0e5f05e5-9ab9-446b-b98d-1e2157c94125 |
> | [Reservationsköpare](#reservation-purchaser) | Låter dig köpa reservationer | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Deltagare för resursprincip](#resource-policy-contributor) | Användare med behörighet att skapa/ändra resursprincip, skapa supportbiljett och läsa resurser/hierarki. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery-deltagare](#site-recovery-contributor) | Låter dig hantera Site Recovery förutom valvskapande och rolltilldelning | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery-operatör](#site-recovery-operator) | Låter dig redundans- och återställning efter fel men inte utföra Site Recovery av hanteringsåtgärder | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery-läsare](#site-recovery-reader) | Låter dig visa Site Recovery status men inte utföra andra hanteringsåtgärder | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Supportbegärandedeltagare](#support-request-contributor) | Låter dig skapa och hantera supportbegäranden | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Taggdeltagare](#tag-contributor) | Låter dig hantera taggar på entiteter utan att ge åtkomst till själva entiteterna. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Övrigt** |  |  |
> | [Azure Digital Twins dataägare](#azure-digital-twins-data-owner) | Fullständig åtkomstroll Digital Twins dataplanet | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure Digital Twins dataläsare](#azure-digital-twins-data-reader) | Skrivskyddade roller för Digital Twins egenskaper för dataplanet | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk-deltagare](#biztalk-contributor) | Låter dig hantera BizTalk-tjänster, men inte åtkomst till dem. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Programgruppdeltagare för skrivbordsvirtualisering](#desktop-virtualization-application-group-contributor) | Deltagare i programgruppen för skrivbordsvirtualisering. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Gruppläsare för program för skrivbordsvirtualisering](#desktop-virtualization-application-group-reader) | Läsare av programgruppen för skrivbordsvirtualisering. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Desktop Virtualization Contributor](#desktop-virtualization-contributor) | Deltagare i Desktop Virtualization. | 082f0a83-3be5-4ba1-904c-961lob79b387 |
> | [Deltagare i värdpool för skrivbordsvirtualisering](#desktop-virtualization-host-pool-contributor) | Deltagare i värdpoolen för skrivbordsvirtualisering. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Läsare för värdpool för skrivbordsvirtualisering](#desktop-virtualization-host-pool-reader) | Läsare av värdpoolen för skrivbordsvirtualisering. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Läsare för skrivbordsvirtualisering](#desktop-virtualization-reader) | Läsare av skrivbordsvirtualisering. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Värdoperator för skrivbordsvirtualiseringssession](#desktop-virtualization-session-host-operator) | Operatör av sessionsvärden för skrivbordsvirtualisering. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Användare av skrivbordsvirtualisering](#desktop-virtualization-user) | Tillåter att användaren använder programmen i en programgrupp. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Sessionsoperator för skrivbordsvirtualisering](#desktop-virtualization-user-session-operator) | Operatör av Uesr-sessionen för skrivbordsvirtualisering. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Deltagare i skrivbordsvirtualiseringsarbetsyta](#desktop-virtualization-workspace-contributor) | Deltagare i arbetsytan för skrivbordsvirtualisering. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Läsare för arbetsyta för skrivbordsvirtualisering](#desktop-virtualization-workspace-reader) | Läsare av arbetsytan för skrivbordsvirtualisering. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [DiskSäkerhetskopieringsläsare](#disk-backup-reader) | Ger behörighet att säkerhetskopiera valv för att utföra disksäkerhetskopiering. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Diskåterställningsoperator](#disk-restore-operator) | Ger behörighet till säkerhetskopieringsvalvet för att utföra diskåterställning. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Deltagare för ögonblicksbild av disk](#disk-snapshot-contributor) | Ger behörighet att säkerhetskopiera valv för att hantera ögonblicksbilder av diskar. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Deltagare i Scheduler-jobbsamlingar](#scheduler-job-collections-contributor) | Låter dig hantera Scheduler-jobbsamlingar, men inte åtkomst till dem. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Services Hub-operatör](#services-hub-operator) | Med Services Hub Operator kan du utföra alla läs-, skriv- och borttagningsåtgärder relaterade till Services Hub Connectors. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Allmänt


### <a name="contributor"></a>Deltagare

Ger fullständig åtkomst för att hantera alla resurser, men tillåter inte att du tilldelar roller i Azure RBAC, hanterar tilldelningar i Azure Blueprints eller delar bildgallerier. [Läs mer](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | * | Skapa och hantera resurser av alla typer |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Ta bort | Ta bort roller, principtilldelningar, principdefinitioner och principuppsättningsdefinitioner |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Skapa roller, rolltilldelningar, principtilldelningar, principdefinitioner och principuppsättningsdefinitioner |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Beviljar anroparen Användaråtkomst Administratörsåtkomst för klientomfånget |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Skapa eller uppdatera skisstilldelningar |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Ta bort skisstilldelningar |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/galleries/share/action | Delar ett galleri till olika omfång |
> | **DataActions** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
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
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
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

Visa alla resurser, men tillåter inte att du gör några ändringar. [Läs mer](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
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

Låter dig hantera användaråtkomst till Azure-resurser. [Läs mer](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | Hantera auktorisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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


### <a name="classic-virtual-machine-contributor"></a>Klassisk virtuell datordeltagare

Låter dig hantera klassiska virtuella datorer, men inte åtkomst till dem, och inte det virtuella nätverket eller lagringskontot som de är anslutna till.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Skapa och hantera klassiska beräkningsdomännamn |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Skapa och hantera virtuella datorer |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | Länka en reserverad IP-adress |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | Hämtar reserverade IP-adresser |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | Ansluter till det virtuella nätverket. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | Hämta det virtuella nätverket. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | Returnerar lagringskontodisken. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | Returnerar lagringskontoavbildningen. (Inaktuell. Använd Microsoft.ClassicStorage/storageAccounts/vmImages) |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Visar åtkomstnycklarna för lagringskontona. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Returnera lagringskontot med det angivna kontot. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="virtual-machine-administrator-login"></a>Administratörsinloggning för virtuell dator

Visa Virtual Machines portalen och logga in som administratör [Läs mer](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Hämtar en definition för offentlig IP-adress. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Hämtar en lastbalanseringsdefinition |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Hämtar en definition för nätverksgränssnittet.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Logga in på en virtuell dator som en vanlig användare |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Logga in på en virtuell dator med Windows-administratörs- eller Linux-rotanvändarbehörighet |
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

Låter dig hantera virtuella datorer, men inte åtkomst till dem, och inte det virtuella nätverket eller lagringskontot som de är anslutna till. [Läs mer](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Skapa och hantera tillgänglighetsuppsättningar för beräkning |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Skapa och hantera beräkningsplatser |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Utför alla åtgärder för virtuella datorer, inklusive att skapa, uppdatera, ta bort, starta, starta om och stänga av virtuella datorer. Kör fördefinierade skript på virtuella datorer. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Skapa och hantera VM-skalningsuppsättningar |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Skapar en ny disk eller uppdaterar en befintlig |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Hämta egenskaperna för en disk |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | Tar bort disken |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | Ansluter till en programgateway-adresspool. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Ansluter till en lastbalanseringsadresspool på en backend-adress. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | Ansluter till en inkommande NAT-pool för lastbalanserare. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Ansluter till en inkommande NAT-regel för lastbalanserare. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | Tillåter att avsökningar av en lastbalanserare används. Med den här behörigheten kan healthProbe-egenskapen för VM-skalningsuppsättningen till exempel referera till avsökningen. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Hämtar en lastbalanseringsdefinition |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Skapa och hantera nätverksplatser |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Skapa och hantera nätverksgränssnitt |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Ansluter till en nätverkssäkerhetsgrupp. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | Hämtar en definition för en nätverkssäkerhetsgrupp |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Ansluter till en offentlig IP-adress. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Hämtar en definition för offentlig IP-adress. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Inte aviseringsbar. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en avsikt för säkerhetskopieringsskydd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar objektinformation för det skyddade objektet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett skyddat säkerhetskopieringsobjekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Returnerar alla skyddsprinciper |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | Skapar skyddsprincip |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Åtgärden Get Vault hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Returnerar användningsinformation för ett Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Åtgärden Skapa valv skapar en Azure-resurs av typen "valv" |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="virtual-machine-user-login"></a>Användarinloggning för virtuell dator

Visa Virtual Machines i portalen och logga in som en vanlig användare. [Läs mer](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Hämtar en definition för offentlig IP-adress. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Hämtar en lastbalanseringsdefinition |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Hämtar en definition för nätverksgränssnittet.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Logga in på en virtuell dator som en vanlig användare |
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


### <a name="cdn-endpoint-contributor"></a>CDN-slutpunktsdeltagare

Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="cdn-endpoint-reader"></a>CDN-slutpunktsläsare

Kan visa CDN-slutpunkter, men kan inte göra ändringar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="cdn-profile-contributor"></a>CDN-profildeltagare

Kan hantera CDN-profiler och deras slutpunkter, men kan inte bevilja åtkomst till andra användare. [Läs mer](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="cdn-profile-reader"></a>CDN-profilläsare

Kan visa CDN-profiler och deras slutpunkter, men kan inte göra ändringar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

Låter dig hantera klassiska nätverk, men inte åtkomst till dem. [Läs mer](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Skapa och hantera klassiska nätverk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="dns-zone-contributor"></a>DNS-zondeltagare

Låter dig hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte styra vem som har åtkomst till dem. [Läs mer](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Skapa och hantera DNS-zoner och -poster |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

Låter dig hantera nätverk, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | Skapa och hantera nätverk |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="private-dns-zone-contributor"></a>Privat DNS zondeltagare

Låter dig hantera privata DNS-zonresurser, men inte de virtuella nätverk som de är länkade till. [Läs mer](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för det virtuella nätverket |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | Ansluter till ett virtuellt nätverk. Inte aviseringsbar. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
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

Låter dig hantera Traffic Manager profiler, men låter dig inte styra vem som har åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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


### <a name="avere-contributor"></a>Avere-deltagare

Kan skapa och hantera ett Avere vFXT kluster. [Läs mer](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för det virtuella nätverket |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Hämtar en definition för undernät för virtuellt nätverk |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter resurser som lagringskonto eller SQL-databas till ett undernät. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Ansluter till en nätverkssäkerhetsgrupp. Inte aviseringsbar. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Skapa och hantera lagringskonton |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Hämtar resurserna för resursgruppen. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Returnerar en blob eller en lista med blobar |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en blob |
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

### <a name="avere-operator"></a>Avere-operatör

Används av Avere vFXT för att hantera klustret Läs [mer](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Hämtar en definition för nätverksgränssnittet.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Skapar ett nätverksgränssnitt eller uppdaterar ett befintligt nätverksgränssnitt.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för det virtuella nätverket |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Hämtar en definition för undernät för virtuellt nätverk |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Ansluter till en nätverkssäkerhetsgrupp. Inte aviseringsbar. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en container |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Returnerar en lista över containrar |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Returnerar resultatet av put-blobcontainern |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Returnerar en blob eller en lista med blobar |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en blob |
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

### <a name="backup-contributor"></a>Säkerhetskopieringsdeltagare

Låter dig hantera säkerhetskopieringstjänsten, men kan inte skapa valv och ge åtkomst till andra [Läs mer](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för det virtuella nätverket |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Hantera åtgärdsresultat för säkerhetskopieringshantering |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Skapa och hantera säkerhetskopieringscontainrar i säkerhetskopieringstjänsterna i Recovery Services-valvet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Uppdaterar containerlistan |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Skapa och hantera säkerhetskopieringsjobb |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Exportera jobb |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Skapa och hantera resultat av åtgärder för säkerhetskopieringshantering |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Skapa och hantera säkerhetskopieringsprinciper |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhetskopieras |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Skapa och hantera säkerhetskopierade objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Skapa och hantera containrar som innehåller säkerhetskopieringsobjekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services . |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Skapa och hantera certifikat som rör säkerhetskopiering i Recovery Services-valv |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Skapa och hantera utökad information relaterad till valvet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Åtgärden Get Vault hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Skapa och hantera användningen av Recovery Services-valv |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Validera åtgärden för skyddat objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Åtgärden Skapa valv skapar en Azure-resurs av typen "valv" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Returnerar status för säkerhetskopieringsåtgärd för Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngine/read | Returnerar alla servrar för säkerhetskopieringshantering som är registrerade med valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Hämta alla containrar som kan skyddas |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Kontrollera säkerhetskopieringsstatus för Recovery Services-valv |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Verifiera funktioner |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Löser aviseringen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Åtgärden returnerar listan över åtgärder för en resursprovider |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Hämtar åtgärdsstatus för en viss åtgärd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Lista alla avsikter för säkerhetskopieringsskydd |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="backup-operator"></a>Operator för säkerhetskopiering

Låter dig hantera säkerhetskopieringstjänster, förutom borttagning av säkerhetskopiering, valvskapande och att ge åtkomst till andra [Läs mer](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för det virtuella nätverket |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultatet av åtgärden som utfördes på skyddscontainern. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Utför säkerhetskopiering för skyddat objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultatet av åtgärden som utförts på skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för åtgärden som utförts på skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar objektinformation för det skyddade objektet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Etablera omedelbar objektåterställning för skyddat objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | Hämta AccessToken för återställning mellan regioner. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställningspunkter för skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återställa återställningspunkter för skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla omedelbar objektåterställning för skyddat objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett skyddat säkerhetskopieringsobjekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade containrar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Uppdaterar containerlistan |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Skapa och hantera säkerhetskopieringsjobb |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Exportera jobb |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Skapa och hantera resultat av åtgärder för säkerhetskopieringshantering |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Hämta resultatet av principåtgärden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Returnerar alla skyddsprinciper |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhetskopieras |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Returnerar alla containrar som tillhör prenumerationen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services . |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Åtgärden Uppdatera resurscertifikat uppdaterar certifikatet för resurs-/valvaudential. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Åtgärden Get Vault hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Åtgärden Hämta åtgärdsresultat kan användas för att hämta åtgärdsstatus och resultat för den asynkront skickade åtgärden |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Åtgärden Hämta containrar kan användas för att registrera containrarna för en resurs. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | Åtgärden Registrera tjänstcontainer kan användas för att registrera en container med Recovery Service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Returnerar användningsinformation för ett Recovery Services-valv. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Validera åtgärd för skyddat objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Returnerar status för säkerhetskopieringsåtgärd för Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Hämta status för principåtgärden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | Skapar en registrerad container |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | Göra en förfrågan om arbetsbelastningar i en container |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngine/read | Returnerar alla servrar för säkerhetskopieringshantering som har registrerats med valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en avsikt för säkerhetskopieringsskydd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Hämta en avsikt för säkerhetskopieringsskydd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Hämta alla skyddbara containrar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en container |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Kontrollera säkerhetskopieringsstatus för Recovery Services-valv |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Verifiera funktioner |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/read | Hämta AAD-egenskaper för autentisering i den tredje regionen för återställning mellan regioner. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Lista återställningsjobb mellan regioner i den sekundära regionen för Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Hämta information om återställningsjobb mellan regioner i den sekundära regionen för Recovery Services-valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/action | Utlösa återställning mellan regioner. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Returnerar CRR-åtgärdsresultat för Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Returnerar CRR-åtgärdsstatus för Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Löser aviseringen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Åtgärden returnerar listan över åtgärder för en resursprovider |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Hämtar åtgärdsstatus för en viss åtgärd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Visa en lista över alla avsikter för säkerhetskopieringsskydd |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
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
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
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

### <a name="backup-reader"></a>Backup Reader

Kan visa säkerhetskopieringstjänster, men kan inte göra ändringar [Läs mer](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultatet av åtgärden som utfördes på skyddscontainern. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultatet av åtgärden som utförts på skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för åtgärden som utförts på skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar objektinformation för det skyddade objektet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställningspunkter för skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade containrar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | Returnerar resultatet av jobbåtgärden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | Returnerar alla jobbobjekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Exportera jobb |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Returnerar resultatet av säkerhetskopieringsåtgärden för Recovery Services-valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Hämta resultatet av principåtgärden. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Returnerar alla skyddsprinciper |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Returnerar alla containrar som tillhör prenumerationen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services . |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Åtgärden Get Vault hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Åtgärden Hämta åtgärdsresultat kan användas för att hämta åtgärdsstatus och resultat för den asynkront skickade åtgärden |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Åtgärden Hämta containrar kan användas för att registrera containrarna för en resurs. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Returnerar Lagringskonfiguration för Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Returnerar konfiguration för Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Returnerar status för säkerhetskopieringsåtgärd för Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Hämta status för principåtgärd. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngine/read | Returnerar alla servrar för säkerhetskopieringshantering som är registrerade med valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Hämta en avsikt för säkerhetskopieringsskydd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en container |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Kontrollera säkerhetskopieringsstatus för Recovery Services-valv |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Löser aviseringen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Åtgärden returnerar listan över åtgärder för en resursprovider |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Hämtar åtgärdsstatus för en viss åtgärd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Lista alla avsikter för säkerhetskopieringsskydd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Returnerar användningsinformation för ett Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Verifiera funktioner |
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

### <a name="classic-storage-account-contributor"></a>Klassisk lagringskontodeltagare

Låter dig hantera klassiska lagringskonton, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Skapa och hantera lagringskonton |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="classic-storage-account-key-operator-service-role"></a>Tjänstroll för nyckeloperatör för klassiskt lagringskonto

Nyckelansvariga för klassiska lagringskonton kan visa och återskapa nycklar på klassiska lagringskonton Läs [mer](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | Visar åtkomstnycklarna för lagringskontona. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | Återskapar de befintliga åtkomstnycklarna för lagringskontot. |
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

Låter dig hantera allt under Data Box-enhet Service förutom att ge åtkomst till andra. [Läs mer](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
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

### <a name="data-box-reader"></a>Data Box-enhet Läsare

Låter dig hantera Data Box-enhet-tjänsten, förutom att skapa beställnings- eller redigeringsinformation och ge åtkomst till andra. [Läs mer](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | Visar en lista över okrypterade autentiseringsuppgifter relaterade till ordern. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | Den här metoden returnerar listan över tillgängliga SKU:er. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | Den här metoden gör alla typer av verifieringar. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | Den här metoden returnerar konfigurationerna för regionen. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | Verifierar leveransadressen och tillhandahåller alternativa adresser om det finns några. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics Utvecklare

Låter dig skicka, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics konton. [Läs mer](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Ta bort ett DataLakeAnalytics-konto. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | Bevilja behörighet att avbryta jobb som skickats av andra användare. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Skapa eller uppdatera ett DataLakeAnalytics-konto. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Skapa eller uppdatera ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Ta bort länken till ett DataLakeStore-konto från ett DataLakeAnalytics-konto. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Skapa eller uppdatera ett länkat Storage-konto för ett DataLakeAnalytics-konto. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Ta bort länken till ett lagringskonto från ett DataLakeAnalytics-konto. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Skapa eller uppdatera en brandväggsregel. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Ta bort en brandväggsregel. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Skapa eller uppdatera en beräkningsprincip. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Ta bort en beräkningsprincip. |
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

### <a name="reader-and-data-access"></a>Läsar- och dataåtkomst

Låter dig visa allt, men du kan inte ta bort eller skapa ett lagringskonto eller en innesluten resurs. Det ger också läs-/skrivåtkomst till alla data som finns i ett lagringskonto via åtkomst till lagringskontonycklar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | Returnerar kontots SAS-token för det angivna lagringskontot. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
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

Tillåter hantering av lagringskonton. Ger åtkomst till kontonyckeln, som kan användas för att komma åt data via auktorisering med delad nyckel. [Läs mer](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter resurser som lagringskonto eller SQL-databas till ett undernät. Inte aviseringsbar. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Skapa och hantera lagringskonton |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="storage-account-key-operator-service-role"></a>Tjänstroll för nyckeloperatör för lagringskonto

Tillåter att åtkomstnycklar för lagringskontot listas och återskapas. [Läs mer](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | Återskapar åtkomstnycklarna för det angivna lagringskontot. |
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

Läsa, skriva och ta bort Azure Storage containrar och blobar. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i Behörigheter för [att anropa blob- och ködataåtgärder.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Ta bort en container. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Returnera en container eller en lista över containrar. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Ändra metadata eller egenskaper för en container. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en nyckel för användardelegering för Blob Service. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Ta bort en blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Returnera en blob eller en lista med blobar. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Skriva till en blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | Flyttar bloben från en sökväg till en annan |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/add/action | Returnerar resultatet av att lägga till blobinnehåll |
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
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Storage Blob Data-ägare

Ger fullständig åtkomst till Azure Storage blobcontainrar och data, inklusive tilldelning av POSIX-åtkomstkontroll. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | Fullständig behörighet för containrar. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en nyckel för användardelegering för Blob Service. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Fullständig behörighet för blobar. |
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

### <a name="storage-blob-data-reader"></a>Storage Blob Data-läsare

Läs och lista Azure Storage containrar och blobar. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Returnera en container eller en lista över containrar. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en nyckel för användardelegering för Blob Service. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Returnera en blob eller en lista med blobar. |
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

### <a name="storage-blob-delegator"></a>Storage Blob Dele blob

Hämta en nyckel för användardelegering som sedan kan användas för att skapa en signatur för delad åtkomst för en container eller blob som är signerad med Azure AD-autentiseringsuppgifter. Mer information finns i Skapa [en SAS för användardelegering.](/rest/api/storageservices/create-user-delegation-sas) [Läs mer](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en nyckel för användardelegering för Blob Service. |
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

Tillåter läs-, skriv- och borttagningsåtkomst för filer/kataloger i Azure-filresurser. Den här rollen har ingen inbyggd motsvarighet på Windows-filservrar. [Läs mer](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Returnerar en fil/mapp eller en lista med filer/mappar. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Returnerar resultatet av att skriva en fil eller skapa en mapp. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Returnerar resultatet av borttagningen av en fil/mapp. |
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

Tillåter läsning, skrivning, borttagning och ändring av ACL:er för filer/kataloger i Azure-filresurser. Den här rollen motsvarar en filresurs-ACL för ändring på Windows-filservrar. [Läs mer](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Returnerar en fil/mapp eller en lista med filer/mappar. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Returnerar resultatet av att skriva en fil eller skapa en mapp. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Returnerar resultatet av borttagningen av en fil/mapp. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Returnerar resultatet av att ändra behörigheten för en fil/mapp. |
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

Tillåter läsåtkomst för filer/kataloger i Azure-filresurser. Den här rollen motsvarar en filresurs-ACL med läsning på Windows-filservrar. [Läs mer](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Returnerar en fil/mapp eller en lista med filer/mappar. |
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

### <a name="storage-queue-data-contributor"></a>Storage Queue Data-deltagare

Läsa, skriva och ta bort Azure Storage köer och kömeddelanden. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | Ta bort en kö. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Returnera en kö eller en lista med köer. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | Ändra kömetadata eller egenskaper. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | Ta bort ett eller flera meddelanden från en kö. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Granska eller hämta ett eller flera meddelanden från en kö. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | Lägg till ett meddelande i en kö. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Returnerar resultatet av bearbetningen av ett meddelande |
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
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Storage Queue Data Message Processor

Granska, hämta och ta bort ett meddelande från en Azure Storage kö. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i Behörigheter för [att anropa blob- och ködataåtgärder.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Granska ett meddelande. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Hämta och ta bort ett meddelande. |
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

### <a name="storage-queue-data-message-sender"></a>Avsändare av storage queue-datameddelande

Lägga till meddelanden i en Azure Storage kö. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i Behörigheter för [att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | Lägg till ett meddelande i en kö. |
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

### <a name="storage-queue-data-reader"></a>Storage Queue Data Reader

Läs och lista Azure Storage köer och kömeddelanden. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Läs mer](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Returnerar en kö eller en lista med köer. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Granska eller hämta ett eller flera meddelanden från en kö. |
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


### <a name="azure-maps-data-contributor"></a>Azure Maps datadeltagare

Ger åtkomst till att läsa, skriva och ta bort åtkomst till mappningsrelaterade data från ett Azure Maps-konto. [Läs mer](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/delete |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Azure Maps dataläsare

Ger åtkomst till läsmappningsrelaterade data från ett Azure Maps-konto. [Läs mer](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
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

### <a name="azure-spring-cloud-data-reader"></a>Azure Spring Cloud dataläsare

Tillåt läsåtkomst till Azure Spring Cloud Data [Läs mer](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/*/read |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b5537268-8956-4941-a8f0-646150406f0c",
  "name": "b5537268-8956-4941-a8f0-646150406f0c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Search Service Contributor

Låter dig hantera Söktjänster, men inte åtkomst till dem. [Läs mer](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Skapa och hantera söktjänster |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="signalr-accesskey-reader"></a>SignalR AccessKey Reader

Läs SignalR Service åtkomstnycklar

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | Visa värdet för SignalR-åtkomstnycklar i hanteringsportalen eller via API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>SignalR App Server (förhandsversion)

Låter din appserver komma åt SignalR Service med AAD-autentiseringsalternativ.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Generera en tillfällig AccessKey för signering av ClientTokens. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Starta en serveranslutning. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>SignalR-deltagare

Skapa, läsa, uppdatera och ta bort SignalR Service-resurser

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
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
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>SignalR-serverlös deltagare (förhandsversion)

Låter din app komma åt tjänsten i serverlöst läge med AAD-autentiseringsalternativ.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Generera en ClientToken för att starta en klientanslutning. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>SignalR Service ägare (förhandsversion)

Fullständig åtkomst till rest Azure SignalR Service-API:er

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Generera en tillfällig AccessKey för signering av ClientTokens. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Generera en ClientToken för att starta en klientanslutning. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | Skicka meddelanden till alla klientanslutningar i hubben. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | Skicka ett meddelande till gruppen. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Kontrollera om gruppen finns eller om användaren finns i gruppen. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | Gå med i/lämna gruppen. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | Skicka meddelanden direkt till en klientanslutning. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Kontrollera att klientanslutningen finns. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Stäng klientanslutningen. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | Skicka meddelanden till användaren, som kan bestå av flera klientanslutningar. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Kontrollera om användaren finns. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/användare/skrivning | Ändra en användare. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>SignalR Service Läsare (förhandsversion)

Skrivskyddade åtkomst till rest Azure SignalR Service-API:er

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Kontrollera om det finns en grupp eller om användaren finns i en grupp. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Kontrollera att klientanslutningen finns. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Kontrollera om användaren finns. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Webbplansdeltagare

Låter dig hantera webbplaner för webbplatser, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Skapa och hantera servergrupper |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Ansluter till en App Service-miljön |
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

### <a name="website-contributor"></a>Webbplatsdeltagare

Låter dig hantera webbplatser (inte webbplaner), men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Skapa och hantera Insights-komponenter |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | Skapa och hantera webbplatscertifikat |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | Hämta namn på platser som tilldelats värdnamnet. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Ansluter till en App Service plan |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Hämta egenskaperna för en App Service plan |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Skapa och hantera webbplatser (webbplatsskapande kräver även skrivbehörighet till den associerade App Service plan) |
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

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

acr delete [Läs mer](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | Ta bort artefakt i ett containerregister. |
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

acr image signer [Läs mer](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | Push-överför/hämta metadata för innehållsförtroende för ett containerregister. |
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

acr pull [Läs mer](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Hämta eller hämta avbildningar från ett containerregister. |
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

acr push [Läs mer](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Hämta eller hämta avbildningar från ett containerregister. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Skicka eller skriva avbildningar till ett containerregister. |
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

acr quarantine data reader

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Hämta eller hämta avbildningar i karantän från containerregistret |
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

acr quarantine data writer

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Hämta eller hämta avbildningar i karantän från containerregistret |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | Skriva/ändra karantäntillståndet för avbildningar i karantän |
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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service administratörsroll för kluster

Visa en lista över åtgärden för klusteradministratörsbehörighet. [Läs mer](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | Visa en lista över autentiseringsuppgifter för clusterAdmin för ett hanterat kluster |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | Hämta en hanterad klusteråtkomstprofil efter rollnamn med hjälp av lista över autentiseringsuppgifter |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Hämta ett hanterat kluster |
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

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service användarroll för kluster

Visa en lista över åtgärden för klusteranvändares autentiseringsuppgifter. [Läs mer](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Lista klustretAnvändar-autentiseringsuppgifter för ett hanterat kluster |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Hämta ett hanterat kluster |
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

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes Service rollen Deltagare

Ger åtkomst till läs- och Azure Kubernetes Service kluster [Läs mer](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Hämta ett hanterat kluster |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Skapar ett nytt hanterat kluster eller uppdaterar ett befintligt |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
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

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Service RBAC-administratör

Låter dig hantera alla resurser under kluster/namnområde, förutom att uppdatera eller ta bort resurskvoter och namnområden. [Läs mer](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Lista klustretAnvändar-autentiseringsuppgifter för ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Skriver resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Tar bort resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Skriver namnområden |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | Tar bort namnområden |

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

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Service RBAC-klusteradministratör

Låter dig hantera alla resurser i klustret. [Läs mer](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Lista klustretAnvändares autentiseringsuppgifter för ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
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

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC-läsare

Tillåter skrivskyddade åtkomst för att se de flesta objekt i ett namnområde. Det tillåter inte visning av roller eller rollbindningar. Den här rollen tillåter inte visning av hemligheter, eftersom läsning av innehållet i hemligheter ger åtkomst till ServiceAccount-autentiseringsuppgifter i namnområdet, vilket skulle tillåta API-åtkomst som alla ServiceAccount i namnområdet (en form av behörighetseskalering). Om du tillämpar den här rollen i klusteromfång får du åtkomst till alla namnområden. [Läs mer](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Läser controllerrevisions |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | Läser daemonsets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | Läser distributioner |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | Läser replikuppsättningar |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | Läser tillståndsfulsets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | Läser horizontalpodautoscalers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | Läser cronjobs |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | Läser jobb |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | Läser konfigurationskartor |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | Läsningar av slutpunkter |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Läsningar av händelser |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Läsningar av händelser |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | Läser daemonsets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | Läser distributioner |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | Läser ingresser |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | Läser nätverkspolicies |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | Skrivskyddade replikuppsättningar |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Läsgränsintervall |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Läser namnområden |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | Läser ingresser |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | Läser nätverkspolicies |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | Läser persistentvolumeclaims |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | Läser poddar |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | Läser poddisruptionbudgetar |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Läser replikeringskontrollerna |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Läser replikeringskontrollerna |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Läser resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | Läser serviceaccounts |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | Läser tjänster |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
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
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC Writer

Tillåter läs-/skrivåtkomst till de flesta objekt i ett namnområde. Den här rollen tillåter inte visning eller ändring av roller eller rollbindningar. Den här rollen tillåter dock åtkomst till hemligheter och poddar som körs som alla ServiceAccount i namnområdet, så den kan användas för att få API-åtkomstnivåer för alla ServiceAccount i namnområdet. Om du tillämpar den här rollen i klusteromfång får du åtkomst till alla namnområden. [Läs mer](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Läser controllerrevisions |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Läser händelser |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Läser händelser |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Läsgränsintervall |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Läser namnområden |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Läser resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
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
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Databaser


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB rollen Kontoläsare

Kan läsa Azure Cosmos DB-kontodata. Se [DocumentDB-kontodeltagare för](#documentdb-account-contributor) att hantera Azure Cosmos DB konton. [Läs mer](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | Läsa valfri samling |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | Läser databaskontots skrivskyddade nycklar. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | Läsa måttdefinitioner |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | Läsmått |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="cosmos-db-operator"></a>Cosmos DB operator

Låter dig hantera Azure Cosmos DB-konton, men inte komma åt data i dem. Förhindrar åtkomst till kontonycklar och anslutningssträngar. [Läs mer](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter resurser som lagringskonto eller SQL-databas till ett undernät. Inte aviseringsbar. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/write | Skapa eller uppdatera en SQL-rolldefinition |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/delete | Ta bort en SQL-rolldefinition |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/write | Skapa eller uppdatera en SQL-rolltilldelning |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/delete | Ta bort en SQL-rolltilldelning |
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
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
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

Kan skicka återställningsbegäran för en Cosmos DB-databas eller en container för ett konto [Läs mer](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | Skicka en begäran om att konfigurera säkerhetskopiering |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | Skicka en återställningsbegäran |
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

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

Kan utföra återställningsåtgärd för Cosmos DB databaskonto med kontinuerligt säkerhetskopieringsläge

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/restore/action | Skicka en återställningsbegäran |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/read | Läs ett återställningsbart databaskonto eller visa en lista över alla återställningsbara databaskonton |
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
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB-kontodeltagare

Kan hantera Azure Cosmos DB konton. Azure Cosmos DB kallades tidigare DocumentDB. [Läs mer](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Skapa och hantera Azure Cosmos DB konton |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter resurser som lagringskonto eller SQL-databas till ett undernät. Inte aviseringsbar. |
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

Låter dig hantera Redis-cacher, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | Registrerar resursprovidern Microsoft.Cache med en prenumeration |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Skapa och hantera Redis-cacher |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

Låter dig hantera SQL-databaser, men inte åtkomst till dem. Du kan inte heller hantera deras säkerhetsrelaterade principer eller deras överordnade SQL-servrar. [Läs mer](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | Skapa och hantera SQL-databaser |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Läsmått |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Läsa måttdefinitioner |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Redigera granskningsinställningar |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Hämta databasens blobgranskningsposter |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Redigera principer för datamaskering |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Redigera principer för säkerhetsaviseringar |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Redigera säkerhetsmått |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
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
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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

### <a name="sql-managed-instance-contributor"></a>SQL Managed Instance deltagare

Låter dig hantera SQL Managed Instances och nödvändig nätverkskonfiguration, men kan inte ge åtkomst till andra.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Läs mått |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Läsa måttdefinitioner |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | Tar bort en specifik hanterad server Azure Active Directory autentiseringsobjekt |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | Lägger till eller uppdaterar en specifik hanterad server Azure Active Directory autentiseringsobjekt |
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
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL-säkerhetshanteraren

Låter dig hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. [Läs mer](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter resurser som lagringskonto eller SQL-databas till ett undernät. Inte aviseringsbar. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | Hämtar resultatet för den hanterade instansen azure async-administratörsåtgärder. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Skapa och hantera SQL Server-granskningsinställning |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | Hämta information om den utökade serverblobgranskningsprincipen som konfigurerats på en viss server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Skapa och hantera granskningsinställningar för SQL Server-databasen |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Hämta databasens blobgranskningsposter |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Skapa och hantera principer för datamaskering för SQL Server-databaser |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | Hämta information om den utökade blobgranskningsprincip som konfigurerats på en viss databas |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | Returnera listan över databaser eller hämtar egenskaperna för den angivna databasen. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | Hämta ett databasschema. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | Hämta en databaskolumn. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | Hämta en databastabell. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Skapa och hantera principer för SQL Server-databassäkerhetsaviseringar |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Skapa och hantera säkerhetsmått för SQL Server-databasen |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Skapa och hantera principer för SQL Server-säkerhetsaviseringar |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | Returnera listan över hanterade instanser eller hämtar egenskaperna för den angivna hanterade instansen. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/administrators/read | Hämtar en lista över administratörer för hanterade instanser. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/administrators/read | Hämtar ett specifikt Azure Active Directory-administratörsobjekt |
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
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
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

Låter dig hantera SQL-servrar och databaser, men inte åtkomst till dem, och inte deras säkerhetsrelaterade principer. [Läs mer](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | Skapa och hantera SQL-servrar |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Läsmått |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Läsa måttdefinitioner |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Redigera granskningsinställningar för SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Redigera granskningsinställningar för SQL Server-databasen |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Hämta databasens blobgranskningsposter |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Redigera principer för datamaskning för SQL Server-databaser |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Redigera principer för SQL Server-databassäkerhetsaviseringar |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Redigera säkerhetsmått för SQL Server-databas |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Redigera principer för SQL Server-säkerhetsaviseringar |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | Tar bort en specifik server Azure Active Directory autentiseringsobjekt |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | Lägger till eller uppdaterar en specifik server Azure Active Directory autentiseringsobjekt |
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
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
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

## <a name="analytics"></a>Analys


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs dataägare

Ger fullständig åtkomst till Azure Event Hubs resurser. [Läs mer](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs datamottagare

Tillåter åtkomst till Azure Event Hubs resurser. [Läs mer](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
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

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs Data Sender

Tillåter att åtkomst skickas till Azure Event Hubs resurser. [Läs mer](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
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

Skapa och hantera datafabriker samt underordnade resurser i dem. [Läs mer](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Skapa och hantera datafabriker och underordnade resurser i dem. |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Skapa och hantera datafabriker och underordnade resurser i dem. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Skapa eller uppdatera en eventSubscription |
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

### <a name="data-purger"></a>Datarensning

Ta bort privata data från en Log Analytics-arbetsyta. [Läs mer](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Rensa data från Application Insights |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visa logganalysdata |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | Ta bort angivna data från arbetsytan |
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

### <a name="hdinsight-cluster-operator"></a>HDInsight-klusteroperator

Låter dig läsa och ändra HDInsight-klusterkonfigurationer. [Läs mer](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | Hämta gatewayinställningar för HDInsight-kluster |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | Uppdatera gatewayinställningar för HDInsight-kluster |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Hämtar eller visar en lista över distributionsåtgärder. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

Kan läsa, skapa, ändra och ta bort Domain Services-relaterade åtgärder som behövs för HDInsight Enterprise Security Package [Läs mer](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
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

Log Analytics-deltagare kan läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningar omfattar att lägga till VM-tillägget till virtuella datorer. läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure Storage; skapa och konfigurera Automation-konton; lägga till lösningar; och konfigurera Azure Diagnostics på alla Azure-resurser. [Läs mer](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Visar åtkomstnycklarna för lagringskontona. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installerar eller uppdaterar Azure Arc tillägg |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

Log Analytics-läsare kan visa och söka efter alla övervakningsdata samt visa övervakningsinställningar, inklusive att visa konfigurationen av Azure Diagnostics på alla Azure-resurser. [Läs mer](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Sök med den nya motorn. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Kör en sökfråga |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
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

### <a name="purview-data-curator"></a>Purview Data Intendent

Datacuratorn Microsoft.Purview kan skapa, läsa, ändra och ta bort katalogdataobjekt och upprätta relationer mellan objekt. Den här rollen är i förhandsversion och kan komma att ändras.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Läs kontoresursen för Microsoft Purview-providern. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Läsa dataobjekt. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/write | Skapa, uppdatera och ta bort dataobjekt. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator can create, read, modify and delete catalog data objects and establish relationships between objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader"></a>Purview Data Reader

Dataläsaren Microsoft.Purview kan läsa katalogdataobjekt. Den här rollen är i förhandsversion och kan komma att ändras.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Läs kontoresursen för Microsoft Purview-providern. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Läsa dataobjekt. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader can read catalog data objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator"></a>Purview Data Source Administrator

Datakällsadministratören Microsoft.Purview kan hantera datakällor och genomsökningar. Den här rollen är i förhandsversion och kan komma att ändras.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Läs kontoresursen för Microsoft Purview-providern. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/read | Läsa datakällor och genomsökningar. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | Skapa, uppdatera och ta bort datakällor och hantera genomsökningar. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator can manage data sources and data scans. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Schemaregisterdeltagare (förhandsversion)

Läsa, skriva och ta bort schemaregistergrupper och scheman.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Schema Registry Reader (förhandsversion)

Läs och lista schemaregistergrupper och scheman.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | Hämta lista över resursbeskrivningar för SchemaGroup |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | Hämta scheman |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockkedja


### <a name="blockchain-member-node-access-preview"></a>Åtkomst till blockkedjemedlemsnod (förhandsversion)

Tillåter åtkomst till blockkedjemedlemsnoder [Läs mer](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | Hämtar eller listar befintliga blockkedjemedlemstransaktionsnoder. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | Ansluter till en blockkedjemedlemstransaktionsnod. |
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

## <a name="ai--machine-learning"></a>AI + maskininlärning


### <a name="cognitive-services-contributor"></a>Cognitive Services deltagare

Låter dig skapa, läsa, uppdatera, ta bort och hantera nycklar för Cognitive Services. [Läs mer](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | Hämtar funktionerna i en prenumeration. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | Hämtar funktionen för en prenumeration i en viss resursprovider. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Läsa loggdefinitioner |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Läsa måttdefinitioner |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Läs mått |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="cognitive-services-custom-vision-contributor"></a>Cognitive Services Custom Vision deltagare

Fullständig åtkomst till projektet, inklusive möjligheten att visa, skapa, redigera eller ta bort projekt. [Läs mer](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Cognitive Services Custom Vision distribution

Publicera, avpublicera eller exportera modeller. Distributionen kan visa projektet men kan inte uppdatera. [Läs mer](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/publish/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/quicktest/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Exporterar ett projekt. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Cognitive Services Custom Vision Labeler

Visa, redigera träningsbilder och skapa, lägga till, ta bort eller ta bort bildtaggar. Etiketterare kan visa projektet men kan inte uppdatera något annat än träningsbilder och taggar. [Läs mer](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Hämta bilder som har skickats till förutsägelseslutpunkten. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tags/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/suggestions/action | Det här API:et hämtar föreslagna taggar och regioner för en matris/batch med otaggade bilder tillsammans med förtroenden för taggarna. Den returnerar en tom matris om inga taggar hittas. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Exporterar ett projekt. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Cognitive Services Custom Vision Läsare

Skrivskyddade åtgärder i projektet. Läsare kan inte skapa eller uppdatera projektet. [Läs mer](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Hämta bilder som har skickats till förutsägelseslutpunkten. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Exporterar ett projekt. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Custom Vision utbildare

Visa, redigera projekt och träna modellerna, inklusive möjligheten att publicera, avpublicera och exportera modellerna. Utbildare kan inte skapa eller ta bort projektet. [Läs mer](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/action | Skapa ett projekt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/delete | Ta bort ett specifikt projekt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/action | Importerar ett projekt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Exporterar ett projekt. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services dataläsare (förhandsversion)

Låter dig läsa Cognitive Services data.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
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

### <a name="cognitive-services-metrics-advisor-administrator"></a>Cognitive Services Metrics Advisor administratör

Fullständig åtkomst till projektet, inklusive konfiguration på systemnivå. [Läs mer](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsAdvisor/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Cognitive Services QnA Maker Editor

Nu ska vi skapa, redigera, importera och exportera en KUNSKAPSB. Du kan inte publicera eller ta bort en kunskapsbasen. [Läs mer](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Hämta information om en rolltilldelning. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Hämta information om en rolldefinition. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Hämtar lista över kunskapsbaser eller information om en specifik kunskapsbaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Ladda ned kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/create/write | Asynkron åtgärd för att skapa en ny kunskapsbas. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | Asynkron åtgärd för att ändra en kunskapsbas eller ersätta kunskapsbasinnehåll. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer-anrop för att fråga kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/train/action | Träna anropet för att lägga till förslag i kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Ladda ned ändringar från körningen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/write | Ersätt ändringsdata. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Hämtar slutpunktsnycklar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/action | Genererar om en slutpunktsnyckel. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Hämtar slutpunktsinställningar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | Uppdatera slutpunktsseningar för en slutpunkt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/operations/read | Hämtar information om en specifik långvarig åtgärd. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Hämtar lista över kunskapsbaser eller information om en specifik kunskapsbaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Ladda ned kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/create/write | Asynkron åtgärd för att skapa en ny kunskapsbas. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | Asynkron åtgärd för att ändra en kunskapsbas eller Ersätta kunskapsbasinnehåll. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | GenerateAnswer-anrop för att fråga kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/train/action | Träna anropet för att lägga till förslag i kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Ladda ned ändringar från runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/write | Ersätt ändringsdata. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Hämtar slutpunktsnycklar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | Genererar om en slutpunktsnyckel. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Hämtar slutpunktsinställningar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | Uppdatera slutpunktspunkter för en slutpunkt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/operations/read | Hämtar information om en specifik långvarig åtgärd. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Hämtar lista över kunskapsbaser eller information om en specifik kunskapsbaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Ladda ned kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write | Asynkron åtgärd för att skapa en ny kunskapsbas. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/write | Asynkron åtgärd för att ändra en kunskapsbas eller ersätta kunskapsbasinnehåll. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer-anrop för att fråga kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action | Träna anropet för att lägga till förslag i kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Ladda ned ändringar från runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/write | Ersätt ändringsdata. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Hämtar slutpunktsnycklar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action | Genererar om en slutpunktsnyckel. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Hämtar slutpunktsinställningar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/write | Uppdatera slutpunktsseningar för en slutpunkt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/operations/read | Hämtar information om en specifik långvarig åtgärd. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Cognitive Services QnA Maker Läsare

Nu ska vi läsa och testa en kunskapsbasen. [Läs mer](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Hämta information om en rolltilldelning. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Hämta information om en rolldefinition. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Hämtar lista över kunskapsbaser eller information om en specifik kunskapsbaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Ladda ned kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer-anrop för att fråga kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Ladda ned ändringar från körningen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Hämtar slutpunktsnycklar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Hämtar slutpunktsinställningar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Hämtar lista över kunskapsbaser eller information om en specifik kunskapsbaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Ladda ned kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | GenerateAnswer-anrop för att fråga kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Ladda ned ändringar från runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Hämtar slutpunktsnycklar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Hämtar slutpunktsinställningar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Hämtar lista över kunskapsbaser eller information om en specifik kunskapsbaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Ladda ned kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer-anrop för att fråga kunskapsbasen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Ladda ned ändringar från runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Hämtar slutpunktsnycklar för en slutpunkt |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Hämtar slutpunktsinställningar för en slutpunkt |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services användare

Låter dig läsa och lista nycklar för Cognitive Services. [Läs mer](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | Visa nycklar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Läsa en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | Läsa en resursdiagnostikinställning |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Läsa loggdefinitioner |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Läsa måttdefinitioner |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Läs mått |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
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

## <a name="internet-of-things"></a>Sakernas Internet


### <a name="device-update-administrator"></a>Administratör för enhetsuppdatering

Ger dig fullständig åtkomst till hanterings- och innehållsåtgärder [Läs mer](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Utför en läsåtgärd som rör uppdateringar |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Utför en skrivåtgärd som rör uppdateringar |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Utför en borttagningsåtgärd som rör uppdateringar |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Utför en läsåtgärd som rör hantering |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Utför en skrivåtgärd som rör hantering |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Utför en borttagningsåtgärd som rör hantering |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management and content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "name": "02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete",
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-administrator"></a>Innehållsadministratör för enhetsuppdatering

Ger dig fullständig åtkomst till innehållsåtgärder Läs [mer](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Utför en läsåtgärd som rör uppdateringar |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Utför en skrivåtgärd som rör uppdateringar |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Utför en borttagningsåtgärd som rör uppdateringar |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "name": "0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-reader"></a>Innehållsläsare för enhetsuppdatering

Ger läsåtkomst till innehållsåtgärder, men tillåter inte ändringar Läs [mer](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Utför en läsåtgärd som rör uppdateringar |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "name": "d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-administrator"></a>Administratör för enhetsuppdateringsdistributioner

Ger dig fullständig åtkomst till hanteringsåtgärder [Läs mer](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Utför en läsåtgärd som rör hantering |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Utför en skrivåtgärd som rör hantering |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Utför en borttagningsåtgärd som rör hantering |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4237640-0e3d-4a46-8fda-70bc94856432",
  "name": "e4237640-0e3d-4a46-8fda-70bc94856432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-reader"></a>Läsare för enhetsuppdateringsdistributioner

Ger dig läsåtkomst till hanteringsåtgärder, men tillåter inte ändringar Läs [mer](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Utför en läsåtgärd som rör hantering |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "name": "49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-reader"></a>Läsare för enhetsuppdatering

Ger läsåtkomst till hanterings- och innehållsåtgärder, men tillåter inte ändringar [Läs mer](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Utför en läsåtgärd som rör uppdateringar |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Utför en läsåtgärd som rör hantering |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management and content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "name": "e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Mixed Reality


### <a name="remote-rendering-administrator"></a>Remote Rendering administratör

Ger användare funktioner för konvertering, hantering av session, återgivning och diagnostik för Azure Remote Rendering [Läs mer](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | Starta tillgångskonvertering |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | Hämta egenskaper för tillgångskonvertering |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | Stoppa tillgångskonvertering |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Hämta sessionsegenskaper |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Starta sessioner |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Stoppa sessioner |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/rendera/läsa | Ansluta till en session |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Ansluta till Remote Rendering kontroll |
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

### <a name="remote-rendering-client"></a>Remote Rendering Klient

Ger användaren hanterings-, renderings- och diagnostikfunktioner för Azure Remote Rendering. [Läs mer](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Hämta sessionsegenskaper |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Starta sessioner |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Stoppa sessioner |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/rendera/läsa | Ansluta till en session |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Ansluta till Remote Rendering kontroll |
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

### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors-kontodeltagare

Låter dig hantera spatiala fästpunkter i ditt konto, men inte ta bort dem [Läs mer](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Skapa spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Upptäck närliggande spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Hämta egenskaper för spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Leta upp spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att förbättra kvaliteten på Azure Spatial Anchors tjänsten |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Uppdatera egenskaper för spatiala fästpunkter |
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

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors kontoägare

Låter dig hantera spatiala fästpunkter i ditt konto, inklusive att ta bort dem [Läs mer](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Skapa spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Ta bort spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Upptäck närliggande rumsliga fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Hämta egenskaper för spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Hitta spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att förbättra kvaliteten på Azure Spatial Anchors tjänsten |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Uppdatera egenskaper för spatiala fästpunkter |
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

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors-kontoläsare

Låter dig hitta och läsa egenskaper för spatiala fästpunkter i ditt konto [Läs mer](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Upptäck närliggande spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Hämta egenskaper för spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Hitta spatiala fästpunkter |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att förbättra kvaliteten på Azure Spatial Anchors tjänsten |
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


### <a name="api-management-service-contributor"></a>API Management tjänstdeltagare

Kan hantera tjänsten och API:erna [Läs mer](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | Skapa och hantera API Management tjänst |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="api-management-service-operator-role"></a>API Management rollen tjänstoperatör

Kan hantera tjänsten men inte API:erna [Läs mer](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Läsa API Management Service-instanser |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | Säkerhetskopiera API Management service till den angivna containern i ett lagringskonto som tillhandahålls av användaren |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | Ta API Management Service-instans |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | Ändra SKU/enheter, lägga till/ta bort regionala distributioner av API Management Service |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Läsa metadata för en API Management Service-instans |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | Återställa API Management service från den angivna containern i ett lagringskonto som användaren har angett |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | Ladda upp TLS/SSL-certifikat för en API Management tjänst |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management tjänst |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | Skapa eller uppdatera API Management serviceinstans |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Hämta nycklar som är associerade med användaren |
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

### <a name="api-management-service-reader-role"></a>API Management tjänstläsarroll

Skrivskyddade åtkomst till tjänsten och API:er [Läs mer](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Läsa API Management Service-instanser |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Läsa metadata för en API Management Service-instans |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Hämta nycklar som är associerade med användaren |
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

### <a name="app-configuration-data-owner"></a>App Configuration dataägare

Ger fullständig åtkomst till App Configuration data. [Läs mer](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
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

### <a name="app-configuration-data-reader"></a>App Configuration Data Reader

Tillåter läsåtkomst till App Configuration data. [Läs mer](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
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

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus dataägare

Ger fullständig åtkomst till Azure Service Bus resurser. [Läs mer](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
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

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus datamottagare

Ger åtkomst till Azure Service Bus resurser. [Läs mer](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
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

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus Data Sender

Tillåter att skicka åtkomst till Azure Service Bus resurser. [Läs mer](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
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

### <a name="azure-stack-registration-owner"></a>Azure Stack registreringsägare

Låter dig hantera Azure Stack registreringar.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | Hämta egenskaperna för en Azure Stack Edge prenumeration |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Hämtar egenskaperna för en Azure Stack Marketplace-produkt |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Hämtar egenskaperna för en Azure Stack registrering |
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

### <a name="eventgrid-contributor"></a>EventGrid-deltagare

Låter dig hantera EventGrid-åtgärder.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Skapa och hantera Event Grid resurser |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Lets you manage EventGrid operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1e241071-0855-49ea-94dc-649edcd759de",
  "name": "1e241071-0855-49ea-94dc-649edcd759de",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/*",
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
  "roleName": "EventGrid Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription-deltagare

Låter dig hantera eventGrid-händelseprenumerationsåtgärder. [Läs mer](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* | Skapa och hantera prenumerationer på regionala händelser |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Lista globala händelseprenumerationer efter ämnestyp |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Lista prenumerationer på regionala händelser |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Visa en lista över regionala händelseprenumerationer efter topictype |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

Låter dig läsa EventGrid-händelseprenumerationer. [Läs mer](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Läsa en eventSubscription |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Lista globala händelseprenumerationer efter ämnestyp |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Lista prenumerationer på regionala händelser |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Lista regionala händelseprenumerationer efter ämnestyp |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
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

### <a name="fhir-data-contributor"></a>FHIR-datadeltagare

Roll ger användare eller huvudnamn fullständig åtkomst till FHIR-data [Läs mer](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
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

### <a name="fhir-data-exporter"></a>FHIR Data Exporter

Roll tillåter användare eller huvudnamn att läsa och exportera FHIR-data [Läs mer](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Läsa FHIR-resurser (inklusive sökning och versionshistorik).  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | Exportåtgärd ($export). |
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

### <a name="fhir-data-reader"></a>FHIR-dataläsare

Roll låter användare eller huvudnamn läsa FHIR-data [Läs mer](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Läsa FHIR-resurser (inklusive sökning och versionshistorik).  |
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

### <a name="fhir-data-writer"></a>FHIR-dataskrivare

Roll låter användare eller huvudnamn läsa och skriva FHIR-data [Läs mer](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | Hård borttagning (inklusive versionshistorik). |

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

Låter dig hantera integreringstjänstmiljöer, men inte åtkomst till dem. [Läs mer](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
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

### <a name="integration-service-environment-developer"></a>Integration Service Environment Utvecklare

Gör att utvecklare kan skapa och uppdatera arbetsflöden, integrationskonton och API-anslutningar i integreringstjänstmiljöer. [Läs mer](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | Läser integrationstjänstmiljön. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/action |  |
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
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
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

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems-kontodeltagare

Låter dig hantera konton för intelligenta system, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | Microsoft.IntelligentSystems/accounts/* | Skapa och hantera konton för intelligenta system |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="logic-app-contributor"></a>Logic App-deltagare

Låter dig hantera logikappar, men inte ändra åtkomsten till dem. [Läs mer](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Visar åtkomstnycklarna för lagringskontona. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Returnera lagringskontot med det angivna kontot. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Den här behörigheten är nödvändig för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i aktivitetsloggen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Läsa måttdefinitioner (lista över tillgängliga måtttyper för en resurs). |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Hanterar Logic Apps resurser. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Skapa och hantera en anslutningsgateway. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | Skapa och hantera en anslutning. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | Skapar och hanterar ett anpassat API. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Ansluter till en App Service plan |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Hämta egenskaperna för en App Service plan |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | Lista funktionshemligheter. |
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

### <a name="logic-app-operator"></a>Logic App-operatör

Låter dig läsa, aktivera och inaktivera logikappar, men inte redigera eller uppdatera dem. [Läs mer](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Läsa aviseringsregler för Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Hämtar diagnostikinställningar för Logic Apps |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Hämtar tillgängliga mått för Logic Apps. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Läser Logic Apps resurser. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | Inaktiverar arbetsflödet. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | Aktiverar arbetsflödet. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | Verifierar arbetsflödet. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Hämtar eller visar en lista över distributionsåtgärder. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | Läs Anslutningsgatewayer. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | Läsanslutningar. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | Läsa anpassat API. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Hämta egenskaperna för en App Service plan |
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


### <a name="managed-identity-contributor"></a>Hanterad identitetsdeltagare

Skapa, läsa, uppdatera och ta bort användar tilldelad identitet [Läs mer](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | Hämtar en befintlig användar tilldelad identitet |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Skapar en ny användar tilldelad identitet eller uppdaterar taggarna som är associerade med en befintlig användar tilldelad identitet |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Tar bort en befintlig användar tilldelad identitet |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="managed-identity-operator"></a>Operatör för hanterad identitet

Läs och tilldela användar tilldelad identitet [Läs mer](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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


### <a name="attestation-contributor"></a>Attestation-deltagare

Kan läsa skriva eller ta bort instansen av attestationsprovidern [Läs mer](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
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
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Attestation Reader

Kan läsa egenskaperna för attestationsprovidern [Läs mer](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
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
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-automation-contributor"></a>Azure Sentinel Automation-deltagare

Azure Sentinel Automation-deltagare [Läs mer](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/read | Läser utlösaren. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/listCallbackUrl/action | Hämtar motringning-URL:en för utlösaren. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/runs/read | Läser arbetsflödeskörningen. |
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
  "description": "Azure Sentinel Automation Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "name": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Logic/workflows/triggers/read",
        "Microsoft.Logic/workflows/triggers/listCallbackUrl/action",
        "Microsoft.Logic/workflows/runs/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Azure Sentinel-deltagare

Azure Sentinel Deltagare [Läs mer](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Sök med den nya motorn. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visa logganalysdata |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Hämta en avslutande OMS-lösning |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Köra frågor över data i arbetsytan |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Hämta datakällor under en arbetsyta. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Läsa en privat arbetsbok |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
        "Microsoft.Insights/myworkbooks/read",
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

Azure Sentinel Läsa [mer](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Kontrollera användarauktorisering och licens |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Query Threat Intelligence-indikatorer |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Query Threat Intelligence-indikatorer |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Sök med den nya motorn. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visa logganalysdata |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | Hämta länkade tjänster under den angivna arbetsytan. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Hämtar en sparad sökfråga |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Få en avslutande OMS-lösning |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Köra frågor över data i arbetsytan |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Hämta datakällor under en arbetsyta. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Läsa en arbetsbok |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Läsa en privat arbetsbok |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
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

Azure Sentinel svarare [Läs mer](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Kontrollera användarauktorisering och licens |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Lägga till taggar till threat intelligence-indikatorn |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Query Threat Intelligence-indikatorer |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | Hotinformation för masstaggar |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Lägga till taggar till threat intelligence-indikatorn |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | Ersätta taggar för hotinformationsindikator |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Query Threat Intelligence-indikatorer |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Sök med den nya motorn. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visa logganalysdata |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Hämta datakällor under en arbetsyta. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Hämtar en sparad sökfråga |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Komma igång med att avsluta OMS-lösningen |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Köra frågor över data i arbetsytan |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Hämta datakällor under en arbetsyta. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Läsa en arbetsbok |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Läsa en privat arbetsbok |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Ta bort |  |
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
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Key Vault administratör

Utför alla dataplansåtgärder på ett nyckelvalv och alla objekt i det, inklusive certifikat, nycklar och hemligheter. Det går inte att hantera nyckelvalvsresurser eller rolltilldelningar. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure".

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Kontrollerar att ett nyckelvalvsnamn är giltigt och inte används |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visa egenskaperna för mjukt borttagna nyckelvalv |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Visar en lista över åtgärder som är tillgängliga i resursprovidern Microsoft.KeyVault |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Key Vault certifikatansvarig

Utför en åtgärd på certifikaten för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure".

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Kontrollerar att ett nyckelvalvsnamn är giltigt och inte används |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visa egenskaperna för mjukt borttagna nyckelvalv |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Visar en lista över åtgärder som är tillgängliga i resursprovidern Microsoft.KeyVault |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault deltagare

Hantera nyckelvalv, men tillåter inte att du tilldelar roller i Azure RBAC och ger dig inte åtkomst till hemligheter, nycklar eller certifikat. [Läs mer](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | Rensa ett mjukt borttagna nyckelvalv |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
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
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
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

### <a name="key-vault-crypto-officer"></a>Key Vault Crypto Officer

Utför en åtgärd på nycklarna för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen rollbaserad åtkomstkontroll i Azure.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Kontrollerar att ett nyckelvalvsnamn är giltigt och inte används |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visa egenskaperna för mjukt borttagna nyckelvalv |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Visar en lista över åtgärder som är tillgängliga i resursprovidern Microsoft.KeyVault |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault kryptografitjänstkrypteringsanvändare

Läs metadata för nycklar och utför radbrytningsåtgärder. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure".

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Skapa eller uppdatera en eventSubscription |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Läsa en eventSubscription |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | Ta bort en eventSubscription |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Visa en lista över nycklar i det angivna valvet, eller läsegenskaper och offentligt material för en nyckel. För asymmetriska nycklar exponerar den här åtgärden offentlig nyckel och innehåller möjlighet att utföra algoritmer för offentliga nycklar, till exempel kryptera och verifiera signatur. Privata nycklar och symmetriska nycklar exponeras aldrig. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Omsluter en symmetrisk nyckel med Key Vault nyckel. Observera att om Key Vault nyckel är asymmetrisk kan den här åtgärden utföras av huvudnamn med läsbehörighet. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Packa upp en symmetrisk nyckel med en Key Vault nyckel. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault Crypto-användare

Utföra kryptografiska åtgärder med hjälp av nycklar. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure".

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Visa en lista med nycklar i det angivna valvet eller läsegenskaper och offentligt material för en nyckel. För asymmetriska nycklar exponerar den här åtgärden offentlig nyckel och innehåller möjligheten att utföra algoritmer för offentliga nycklar, till exempel kryptera och verifiera signatur. Privata nycklar och symmetriska nycklar exponeras aldrig. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | Uppdaterar de angivna attributen som är associerade med den angivna nyckeln. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | Skapar säkerhetskopian av en nyckel. Filen kan användas för att återställa nyckeln i en Key Vault i samma prenumeration. Begränsningar kan gälla. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | Krypterar klartext med en nyckel. Observera att om nyckeln är asymmetrisk kan den här åtgärden utföras av huvudnamn med läsbehörighet. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | Dekrypterar chiffertext med en nyckel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Omsluter en symmetrisk nyckel med Key Vault nyckel. Observera att om Key Vault är asymmetrisk kan den här åtgärden utföras av huvudnamn med läsbehörighet. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Omsluter en symmetrisk nyckel med en Key Vault nyckel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | Signerar en sammanfattning av meddelanden (hash) med en nyckel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | Verifierar signaturen för en sammanfattning av meddelanden (hash) med en nyckel. Observera att om nyckeln är asymmetrisk kan den här åtgärden utföras av huvudnamn med läsbehörighet. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Key Vault Läsare

Läs metadata för nyckelvalv och dess certifikat, nycklar och hemligheter. Det går inte att läsa känsliga värden, till exempel hemligt innehåll eller nyckelmaterial. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure".

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Kontrollerar att ett nyckelvalvsnamn är giltigt och inte används |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visa egenskaperna för mjukt borttagna nyckelvalv |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Visar en lista över åtgärder som är tillgängliga på Resursprovidern Microsoft.KeyVault |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Visa eller visa egenskaperna för en hemlighet, men inte dess värde. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Key Vault Secrets Officer

Utför en åtgärd på hemligheterna för ett nyckelvalv, förutom att hantera behörigheter. Fungerar bara för nyckelvalv som använder behörighetsmodellen "rollbaserad åtkomstkontroll i Azure".

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Kontrollerar att ett nyckelvalvsnamn är giltigt och inte används |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Visa egenskaperna för mjukt borttagna nyckelvalv |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Visar en lista över åtgärder som är tillgängliga i resursprovidern Microsoft.KeyVault |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Key Vault hemlighetsanvändare

Läsa hemligt innehåll. Fungerar bara för nyckelvalv som använder behörighetsmodellen rollbaserad åtkomstkontroll i Azure.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | Hämtar värdet för en hemlighet. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Visa eller visa egenskaperna för en hemlighet, men inte dess värde. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Managed HSM-deltagare

Låter dig hantera hanterade HSM-pooler, men inte åtkomst till dem. [Läs mer](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
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
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Säkerhetsadministratör

Visa och uppdatera behörigheter för Security Center. Samma behörigheter som säkerhetsläsarrollen och kan även uppdatera säkerhetsprincipen och avvisa aviseringar och rekommendationer. [Läs mer](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Skapa och hantera principtilldelningar |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Skapa och hantera principdefinitioner |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Skapa och hantera principundantag |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Skapa och hantera principuppsättningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visa logganalysdata |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Skapa och hantera säkerhetskomponenter och principer |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
        "Microsoft.Authorization/policyExemptions/*",
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

### <a name="security-assessment-contributor"></a>Deltagare i säkerhetsutvärdering

Låter dig skicka utvärderingar till Security Center

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Skapa eller uppdatera säkerhetsutvärderingar för din prenumeration |
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

### <a name="security-manager-legacy"></a>Security Manager (äldre)

Det här är en äldre roll. Använd säkerhetsadministratören i stället.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | Läsa konfigurationsinformation för klassiska virtuella datorer |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Skrivkonfiguration för klassiska virtuella datorer |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | Läs konfigurationsinformation om det klassiska nätverket |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Skapa och hantera säkerhetskomponenter och principer |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

Visa behörigheter för Security Center. Kan visa rekommendationer, aviseringar, en säkerhetsprincip och säkerhets tillstånd, men kan inte göra ändringar. [Läs mer](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Läsa en klassisk måttavisering |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Visa logganalysdata |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | Läsa säkerhetskomponenter och principer |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/action | Hämtar information om nedladdningsbara IoT Defender-paket |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/action | Ladda ned aktiveringsfilen för hanteraren med prenumerationskvotdata |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/action | Laddar ned återställning av lösenordsfil för IoT-sensorer |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
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
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
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

Låter dig ansluta, starta, starta om och stänga av dina virtuella datorer i Azure DevTest Labs. [Läs mer](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | Hämta egenskaperna för en tillgänglighetsuppsättning |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | Läs egenskaperna för en virtuell dator (VM-storlekar, körningsstatus, VM-tillägg osv.) |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräkningsresurserna |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | Startar om den virtuella datorn |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | Startar den virtuella datorn |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | Läsa egenskaperna för ett labb |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | Göra anspråk på en slumpmässig, anspråksbar virtuell dator i labbet. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | Skapa virtuella datorer i ett labb. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | Se till att den aktuella användaren har en giltig profil i labbet. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | Ta bort formler. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | Läsa formler. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | Lägga till eller ändra formler. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | Utvärderar labbprincipen. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | Bli ägare till en befintlig virtuell dator |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | Visar en lista över tillämpliga scheman för start/stopp, om några. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | Hämtar en sträng som representerar innehållet i RDP-filen för den virtuella datorn |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Ansluter till en lastbalanseringsadresspool för backend. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Ansluter till en inkommande nat-regel för lastbalanserare. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | Läs egenskaperna för ett nätverksgränssnitt (till exempel alla lastbalanserare som nätverksgränssnittet ingår i) |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Ansluter en virtuell dator till ett nätverksgränssnitt. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Hämtar en definition för nätverksgränssnittet.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Skapar ett nätverksgränssnitt eller uppdaterar ett befintligt nätverksgränssnitt.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | Läsa egenskaperna för en offentlig IP-adress |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Ansluter till en offentlig IP-adress. Inte aviseringsbar. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Hämtar en definition för offentlig IP-adress. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Inte aviseringsbar. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Hämtar eller visar en lista över distributionsåtgärder. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Hämtar eller visar en lista över distributioner. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | Visar tillgängliga storlekar som den virtuella datorn kan uppdateras till |
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

### <a name="lab-creator"></a>Labbskapare

Gör att du kan skapa nya labb under dina Azure Lab-konton. [Läs mer](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | Skapa ett labb i ett labbkonto. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | Hämta priser och tillgänglighet för kombinationer av storlekar, geografiska områden och operativsystem för labbkontot. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | Hämta grundläggande begränsningar och användning för den här prenumerationen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

## <a name="monitor"></a>Monitor


### <a name="application-insights-component-contributor"></a>Application Insights komponentdeltagare

Kan hantera Application Insights komponenter Läs [mer](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera klassiska aviseringsregler |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/read | Hämta token för livemått |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Skapa och hantera nya aviseringsregler |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Skapa och hantera Insights-komponenter |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/topologi/read | Lästopologi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/transactions/read | Lästransaktioner |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Skapa och hantera Insights-webbtester |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
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

Ger användaren behörighet att visa och ladda ned ögonblicksbilder av felsökning som samlats in med Application Insights Snapshot Debugger. Observera att dessa behörigheter inte ingår i rollerna [Ägare](#owner) [eller](#contributor) Deltagare. När du ger Application Insights Snapshot Debugger rollen måste du bevilja rollen direkt till användaren. Rollen känns inte igen när den läggs till i en anpassad roll. [Läs mer](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="monitoring-contributor"></a>Övervakningsdeltagare

Kan läsa alla övervakningsdata och redigera övervakningsinställningar. Se även [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Läs mer](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Skapa och hantera Insights-komponenter |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Lista aktivitetslogghändelser (hanteringshändelser) i en prenumeration. Den här behörigheten gäller för både program- och portalåtkomst till aktivitetsloggen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Den här behörigheten är nödvändig för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i aktivitetsloggen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Läsa måttdefinitioner (lista över tillgängliga måtttyper för en resurs). |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Läsa mått för en resurs. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registrera Microsoft Insights-providern |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Skapa och hantera Insights-webbtester |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | Skapar en ny arbetsyta eller länkar till en befintlig arbetsyta genom att ange kund-ID från den befintliga arbetsytan. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Läs-/skriv-/ta bort logganalyslösningspaket. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Sparade sökningar för läsning/skrivning/borttagning av logganalys. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Kör en sökfråga |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Läsa/skriva/ta bort log analytics-konfigurationer för lagringsinformation. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Hämta information om hälsoövervakare för virtuella gästdatorn. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
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

### <a name="monitoring-metrics-publisher"></a>Övervakningsmåttutgivare

Aktiverar publiceringsmått mot Azure-resurser [Läs mer](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Registrera Microsoft Insights-providern |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Skriva mått |
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

### <a name="monitoring-reader"></a>Övervakningsläsare

Kan läsa alla övervakningsdata (mått, loggar osv.). Se även [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Läs mer](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Kör en sökfråga |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="workbook-contributor"></a>Arbetsboksdeltagare

Kan spara delade arbetsböcker. [Läs mer](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | Skapa eller uppdatera en arbetsbok |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Ta bort en arbetsbok |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Läsa en arbetsbok |
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

### <a name="workbook-reader"></a>Arbetsboksläsare

Kan läsa arbetsböcker. [Läs mer](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Läsa en arbetsbok |
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


### <a name="automation-job-operator"></a>Automation-jobboperator

Skapa och hantera jobb med Automation Runbooks. [Läs mer](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker resurser |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Hämtar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Hämtar en Azure Automation jobbström |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Skapar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Hämtar utdata från ett jobb |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker resurser |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Hämtar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Hämtar en Azure Automation jobbström |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Skapar ett Azure Automation jobb |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Hämtar ett Azure Automation jobbschema |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Skapar ett Azure Automation jobbschema |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | Hämtar arbetsytan som är länkad till Automation-kontot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Hämtar ett Azure Automation konto |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Hämtar en Azure Automation runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Hämtar en Azure Automation schematillgång |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Skapar eller uppdaterar en Azure Automation schematillgång |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Hämtar utdata från ett jobb |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="automation-runbook-operator"></a>Automation Runbook-operatör

Läsa Runbook-egenskaper – för att kunna skapa jobb för runbooken. [Läs mer](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Hämtar en Azure Automation runbook |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="azure-arc-enabled-kubernetes-cluster-user-role"></a>Azure Arc aktiverat kubernetes-klusteranvändarroll

Visa en lista över åtgärden för autentiseringsuppgifter för klusteranvändare.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/listClusterUserCredentials/action | Lista klusterAnvändar-autentiseringsuppgifter |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "List cluster user credentials action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "name": "00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Enabled Kubernetes Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-admin"></a>Azure Arc Kubernetes-administratör

Låter dig hantera alla resurser under kluster/namnområde, förutom att uppdatera eller ta bort resurskvoter och namnområden. [Läs mer](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Läser controllerrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write | Skriver localsubjectaccessreviews |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Läser händelser |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Läsningar av händelser |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Läsgränsintervall |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Läser namnområden |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/rolebindings/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/roles/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Läser resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "name": "dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-cluster-admin"></a>Azure Arc Kubernetes-klusteradministratör

Låter dig hantera alla resurser i klustret. [Läs mer](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "name": "8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-viewer"></a>Azure Arc Kubernetes Viewer

Låter dig visa alla resurser i kluster/namnrymd, förutom hemligheter. [Läs mer](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Läser controllerrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/read | Läser daemonsets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/read | Läser distributioner |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/read | Läser replikuppsättningar |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/read | Läser tillståndsfulsets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/read | Läser horizontalpodautoscalers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/read | Läser cronjobs |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/read | Läser jobb |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/read | Läser konfigurationskartor |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/read | Läsningar av slutpunkter |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Läsningar av händelser |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Läsningar av händelser |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/read | Läser daemonsets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/read | Läser distributioner |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/read | Läser ingresser |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/read | Läser nätverkspolicies |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/read | Skrivskyddade replikuppsättningar |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Läsgränsintervall |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Läser namnområden |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/read | Läser ingresser |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/read | Läser nätverkspolicies |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/read | Läser persistentvolumeclaims |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/read | Läser poddar |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/read | Läser poddisruptionbudgetar |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Läser replikeringskontrollerna |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Läser replikeringskontrollerna |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Läser resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/read | Läser serviceaccounts |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/read | Läser tjänster |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/63f0a09d-1495-4db4-a681-037d84835eb4",
  "name": "63f0a09d-1495-4db4-a681-037d84835eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/read",
        "Microsoft.Kubernetes/connectedClusters/configmaps/read",
        "Microsoft.Kubernetes/connectedClusters/endpoints/read",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read",
        "Microsoft.Kubernetes/connectedClusters/pods/read",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/read",
        "Microsoft.Kubernetes/connectedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Viewer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-writer"></a>Azure Arc Kubernetes Writer

Låter dig uppdatera allt i kluster/namnrymd, förutom (kluster)roller och (kluster)rollbindningar. [Läs mer](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Läser controllerrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Läser händelser |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Läser händelser |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Läsgränsintervall |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Läser namnområden |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Läser resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5b999177-9696-4545-85c7-50de3797e5a1",
  "name": "5b999177-9696-4545-85c7-50de3797e5a1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine Onboarding

Kan publicera Azure Connected Machines. [Läs mer](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Läsa alla Azure Arc datorer |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Skriver en Azure Arc datorer |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/read | Läsa alla Azure Arc privateLinkScopes |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | Hämta tilldelning av gästkonfiguration. |
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
        "Microsoft.HybridCompute/privateLinkScopes/read",
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

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine resursadministratör

Kan läsa, skriva, ta bort och registrera Azure Connected Machines på nya sätt.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Läsa alla Azure Arc datorer |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Skriver en Azure Arc datorer |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Tar bort en Azure Arc datorer |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installerar eller uppdaterar Azure Arc tillägg |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
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
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/privateLinkScopes/*",
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

Ger läsåtkomst till faktureringsdata [Läs mer](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | Läsa faktureringsinformation |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="blueprint-contributor"></a>Skissdeltagare

Kan hantera skissdefinitioner, men inte tilldela dem. [Läs mer](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | Skapa och hantera skissdefinitioner eller skissartefakter. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="blueprint-operator"></a>Skissoperator

Kan tilldela befintliga publicerade skisser, men kan inte skapa nya skisser. Observera att detta endast fungerar om tilldelningen görs med en användar tilldelad hanterad identitet. [Läs mer](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Skapa och hantera skisstilldelningar. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="cost-management-contributor"></a>Cost Management-deltagare

Kan visa kostnader och hantera kostnadskonfiguration (t.ex. budgetar, exporter) [Läs mer](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Hämta konfigurationer |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Läser rekommendationer |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
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
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
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

### <a name="cost-management-reader"></a>Cost Management Läsare

Kan visa kostnadsdata och konfiguration (t.ex. budgetar, exporter) [Läs mer](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Hämta konfigurationer |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Läser rekommendationer |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
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
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
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

### <a name="hierarchy-settings-administrator"></a>Administratör för hierarkiinställningar

Tillåter användare att redigera och ta bort hierarkiinställningar

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | Skapar eller uppdaterar hierarkiinställningar för hanteringsgrupp. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | Tar bort hierarkiinställningar för hanteringsgrupp. |
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

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes-kluster – Azure Arc registrering

Rolldefinition för att auktorisera en användare/tjänst för att skapa anslutenClusters-resurs [Läs mer](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Skapar eller uppdaterar en distribution. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Hämta resultatet av prenumerationsåtgärden. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Skriver connectedClusters |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | Läsa anslutnakluslar |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="managed-application-contributor-role"></a>Deltagarroll för hanterade program

Gör det möjligt att skapa hanterade programresurser.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | Registrera dig för lösningar. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
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

### <a name="managed-application-operator-role"></a>Operatörsroll för hanterat program

Låter dig läsa och utföra åtgärder på hanterade programresurser

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | Hämtar en lista över program. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
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
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
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

### <a name="managed-services-registration-assignment-delete-role"></a>Tilldelning av registrering av hanterade tjänster Ta bort roll

Med borttagningsrollen för tilldelning av hanterade tjänster kan användare av hanterade tjänster ta bort den registreringstilldelning som tilldelats deras klientorganisation. [Läs mer](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | Hämtar en lista över tilldelningar för managed services-registrering. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Tar bort tilldelning av managed services-registrering. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | Läser åtgärdsstatus för resursen. |
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

### <a name="management-group-contributor"></a>Deltagare i hanteringsgrupp

Deltagarroll för hanteringsgrupp [Läs mer](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Ta bort hanteringsgrupp. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | Av associerar prenumerationen från hanteringsgruppen. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | Associerar befintlig prenumeration med hanteringsgruppen. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Skapa eller uppdatera en hanteringsgrupp. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Listar prenumerationen under den angivna hanteringsgruppen. |
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

### <a name="management-group-reader"></a>Läsare för hanteringsgrupp

Läsarroll för hanteringsgrupp

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Listar prenumerationen under den angivna hanteringsgruppen. |
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

### <a name="new-relic-apm-account-contributor"></a>New Relic APM-kontodeltagare

Låter dig hantera New Relic Application Performance Management och program, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | NewRelic.APM/accounts/* |  |
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

### <a name="policy-insights-data-writer-preview"></a>Dataskrivare för Policy Insights (förhandsversion)

Ger läsåtkomst till resursprinciper och skrivåtkomst till resurskomponentprinciphändelser. [Läs mer](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | Hämta information om en principtilldelning. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | Hämta information om en principdefinition. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | Hämta information om ett principundantag. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | Hämta information om en principuppsättningsdefinition. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | Kontrollera kompatibilitetsstatusen för en viss komponent mot dataprinciper. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | Logga resurskomponentens principhändelser. |
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
        "Microsoft.Authorization/policyexemptions/read",
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

### <a name="quota-request-operator"></a>Operator för kvotbegäran

Läsa och skapa kvotbegäranden, få status för kvotbegäran och skapa supportbegäranden. [Läs mer](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/read | Hämta den aktuella tjänstgränsen eller kvoten för den angivna resursen och platsen |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/write | Skapa tjänstgräns eller kvot för den angivna resursen och platsen |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimitsRequests/read | Hämta en tjänstgränsbegäran för den angivna resursen och platsen |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Registrerar kapacitetsresursprovidern och gör det möjligt att skapa kapacitetsresurser. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Read and create quota requests, get quota request status, and create support tickets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "name": "0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "permissions": [
    {
      "actions": [
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/read",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/write",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read",
        "Microsoft.Capacity/register/action",
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
  "roleName": "Quota Request Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Reservationsköpare

Låter dig köpa reservationer [Läs mer](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Hämtar listan över prenumerationer. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Registrerar kapacitetsresursprovidern och gör det möjligt att skapa kapacitetsresurser. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/register/action | Registrerar prenumeration med resursprovidern Microsoft.Compute |
> | [Microsoft.SQL](resource-provider-operations.md#microsoftsql)/register/action | Registrerar prenumerationen för Microsoft SQL Database och gör det möjligt att skapa Microsoft SQL-databaser. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/register/action | Registrera till förbruknings-RP |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/catalogs/read | Läsa katalogen för reservation |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Hämta information om en rolltilldelning. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/read | Lista enskilda eller delade rekommendationer för reserverade instanser för en prenumeration. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/supporttickets/write | Gör att du kan skapa och uppdatera en supportbiljett |
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
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Deltagare för resursprincip

Användare med behörighet att skapa/ändra resursprincip, skapa supportbiljett och läsa resurser/hierarki. [Läs mer](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | */läsa | Läs resurser av alla typer, utom hemligheter. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Skapa och hantera principtilldelningar |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Skapa och hantera principdefinitioner |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Skapa och hantera principundantag |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Skapa och hantera principuppsättningar |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
        "Microsoft.Authorization/policyexemptions/*",
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

### <a name="site-recovery-contributor"></a>Site Recovery-deltagare

Låter dig hantera Site Recovery förutom valvskapande och rolltilldelning [Läs mer](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för det virtuella nätverket |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Åtgärden Uppdatera resurscertifikat uppdaterar autentiseringscertifikatet för resurs/valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Skapa och hantera utökad information relaterad till valvet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Åtgärden Get Vault hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Skapa eller uppdatera inställningar för replikeringsaviseringar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Läsa alla händelser |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Skapa och hantera replikeringsrepliker |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Skapa och hantera replikeringsjobb |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Skapa och hantera replikeringsprinciper |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Skapa och hantera återställningsplaner |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Skapa och hantera lagringskonfiguration för Recovery Services-valv |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Returnerar användningsinformation för ett Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Åtgärden Valvtoken kan användas för att hämta valvtoken för backend-åtgärder på valvnivå. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Läsa aviseringar för Recovery Services-valvet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | Läsa status för val av valvreplikeringsåtgärd |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="site-recovery-operator"></a>Site Recovery-operatör

Låter dig redundans och återställning efter fel men inte utföra andra Site Recovery-hanteringsåtgärder [Läs mer](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Hämta definitionen för det virtuella nätverket |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Åtgärden Get Vault hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Åtgärden Hämta åtgärdsresultat kan användas för att hämta åtgärdsstatus och resultat för den asynkront skickade åtgärden |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Åtgärden Hämta containrar kan användas för att registrera containrarna för en resurs. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Läsa eventuella aviseringsinställningar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Läsa alla händelser |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | Kontrollerar infrastruktur fabricens konsekvens |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Läsa alla material |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | Associera gatewayen på ett annat sätt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för infrastruktur |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Läsa alla nätverk |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läsa alla nätverksmappningar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Läsa alla skyddscontainrar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läsa alla objekt som kan skyddas |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Tillämpa återställningspunkt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Genomför redundans |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läsa eventuella återställningspunkter för replikering |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Återaktivera skydd av skyddat objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Växelskyddscontainer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testa redundans |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Rensa redundanstest |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Redundans |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Uppdatera mobilitetstjänsten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läsa alla mappningar av skyddscontainrar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läs alla Recovery Services-leverantörer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Uppdatera provider |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Läsa alla lagringsklassificeringar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läsa alla lagringsklassificeringsmappningar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Läs alla vCenters |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Skapa och hantera replikeringsjobb |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Läsa eventuella principer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | Återställningsplan för genomförd redundans |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | Plan för planerad redundansåterställning |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Läs eventuella återställningsplaner |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | Återställningsplan för återaktivering av skydd |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | Återställningsplan för redundanstest |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Återställningsplan för redundansrensning |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | Återställningsplan för redundans |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Läsa aviseringar för Recovery Services-valvet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Returnerar användningsinformation för ett Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Åtgärden Valvtoken kan användas för att hämta valvtoken för backend-åtgärder på valvnivå. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="site-recovery-reader"></a>Site Recovery-läsare

Låter dig visa Site Recovery status men inte utföra andra hanteringsåtgärder [Läs mer](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Åtgärden Get Vault hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Åtgärden Hämta åtgärdsresultat kan användas för att hämta åtgärdsstatus och resultat för den asynkront skickade åtgärden |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Åtgärden Hämta containrar kan användas för att registrera containrarna för en resurs. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Läsa eventuella aviseringsinställningar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Läsa alla händelser |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Läsa alla material |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Läsa alla nätverk |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läsa alla nätverksmappningar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Läsa eventuella skyddscontainrar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läsa alla skyddbara objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läsa eventuella återställningspunkter för replikering |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läsa alla mappningar av skyddscontainer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läs alla Recovery Services-leverantörer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Läsa alla lagringsklassificeringar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läsa alla lagringsklassificeringsmappningar |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Läs alla vCenters |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | Läsa alla jobb |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Läsa eventuella principer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Läs eventuella återställningsplaner |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Returnerar användningsinformation för ett Recovery Services-valv. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Åtgärden Valvtoken kan användas för att hämta valvtoken för backend-åtgärder på valvnivå. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="support-request-contributor"></a>Supportbegärandedeltagare

Låter dig skapa och hantera supportförfrågningar [Läs mer](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="tag-contributor"></a>Taggdeltagare

Låter dig hantera taggar på entiteter utan att ge åtkomst till själva entiteterna. [Läs mer](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Hämtar resurserna för resursgruppen. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | Hämtar resurser för en prenumeration. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
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


### <a name="azure-digital-twins-data-owner"></a>Azure Digital Twins dataägare

Fullständig åtkomstroll för Digital Twins dataplan [Läs mer](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Läsa, ta bort, skapa eller uppdatera en händelseväg |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Läsa, skapa, uppdatera eller ta bort digital tvilling |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Anropa alla kommandon på en Digital Twin |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Läsa, skapa, uppdatera eller ta bort digital tvillingrelationer |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Läsa, skapa, uppdatera eller ta bort en modell |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Köra frågor mot Digital Twins Graph |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Azure Digital Twins dataläsare

Skrivskyddade roller för Digital Twins egenskaper för dataplanet [Läs mer](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | Läsa valfri Digital Twin |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | Läsa alla Digital Twin-relationer |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | Läsa en händelseväg |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | Läsa vilken modell som helst |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | Köra frågor mot Digital Twins Graph |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>BizTalk-deltagare

Låter dig hantera BizTalk-tjänster, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | Microsoft.BizTalkServices/BizTalk/* | Skapa och hantera BizTalk-tjänster |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="desktop-virtualization-application-group-contributor"></a>Programgruppdeltagare för skrivbordsvirtualisering

Deltagare i programgruppen för skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Läsa värdpooler |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Läsa värdpooler/sessionsvärdar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Läsare för programgrupp för skrivbordsvirtualisering

Läsare av programgruppen för skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Läsa programgrupper |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Läsa värdpooler |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Läsa värdpooler/sessionsvärdar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Hämtar eller visar en lista över distributioner. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Läsa en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>Desktop Virtualization Contributor

Deltagare i Desktop Virtualization. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Deltagare i värdpoolen för skrivbordsvirtualisering

Deltagare i värdpoolen för skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Läsare för värdpool för skrivbordsvirtualisering

Läsare av värdpoolen för skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Läsa värdpooler |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Hämtar eller visar en lista över distributioner. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Läsa en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>Läsare för skrivbordsvirtualisering

Läsare av skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Hämtar eller visar en lista över distributioner. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Läsa en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Värdoperatör för skrivbordsvirtualiseringssession

Operatör av sessionsvärden för skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Läsa värdpooler |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Användare av skrivbordsvirtualisering

Tillåter användare att använda programmen i en programgrupp. [Läs mer](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataActions** |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/action | Använda ApplicationGroup |
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

### <a name="desktop-virtualization-user-session-operator"></a>Sessionsoperatör för skrivbordsvirtualisering

Operatör av Uesr-sessionen för skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Läsa värdpooler |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Läsa värdpooler/sessionsvärdar |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Deltagare i arbetsytan för skrivbordsvirtualisering

Deltagare i arbetsytan för skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Läsa programgrupper |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Läsare för arbetsyta för skrivbordsvirtualisering

Läsare av arbetsytan För skrivbordsvirtualisering. [Läs mer](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.DesktopVirtualisering](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/read | Läs arbetsytor |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Läsa programgrupper |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Hämtar eller visar en lista över distributioner. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Läsa en klassisk måttavisering |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>DiskSäkerhetskopieringsläsare

Ger behörighet till säkerhetskopieringsvalvet för att utföra disksäkerhetskopiering. [Läs mer](../backup/disk-backup-faq.yml)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Hämta egenskaperna för en disk |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Hämta SAS-URI för disken för blobåtkomst |
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
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Diskåterställningsoperator

Ger behörighet till säkerhetskopieringsvalvet för att utföra diskåterställning. [Läs mer](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Skapar en ny disk eller uppdaterar en befintlig |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Hämta egenskaperna för en disk |
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
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Diskögonblicksbilddeltagare

Ger behörighet till säkerhetskopieringsvalvet för att hantera ögonblicksbilder av diskar. [Läs mer](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/delete | Ta bort en ögonblicksbild |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | Skapa en ny ögonblicksbild eller uppdatera en befintlig |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/read | Hämta egenskaperna för en ögonblicksbild |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/action | Hämta SAS-URI för ögonblicksbilden för blobåtkomst |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/action | Återkalla SAS-URI för ögonblicksbilden |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Hämta SAS-URI för disken för blobåtkomst |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | Skapar ett lagringskonto med de angivna parametrarna, uppdaterar egenskaperna eller taggarna eller lägger till en anpassad domän för det angivna lagringskontot. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/delete | Tar bort ett befintligt lagringskonto. |
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
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Deltagare i Scheduler-jobbsamlingar

Låter dig hantera Scheduler-jobbsamlingar, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Skapa och hantera en klassisk måttavisering |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna omfånget |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Skapa och hantera jobbsamlingar |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Skapa och uppdatera en supportbiljett |
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

### <a name="services-hub-operator"></a>Tjänsthubboperator

Med Services Hub Operator kan du utföra alla läs-, skriv- och borttagningsåtgärder relaterade till Services Hub Connectors. [Läs mer](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Åtgärder | Beskrivning |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Läsa roller och rolltilldelningar |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hämtar eller visar en lista över resursgrupper. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Skapa och hantera en distribution |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | Skapa eller uppdatera en Tjänsthubbanslutning |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/read | Visa eller visa en lista över hubbanslutningsappar för tjänster |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/delete | Ta bort services hub-anslutningsappar |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/action | Visar en lista över utvärderingsrättigheter för en viss Services Hub-arbetsyta |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/read | Visa berättiganden för supporterbjudanden för en viss Tjänsthubbarbetsyta |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/workspaces/read | Visa en lista över arbetsytor i tjänsthubben för en viss användare |
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
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Behörigheter i Azure Security Center](../security-center/security-center-permissions.md)
