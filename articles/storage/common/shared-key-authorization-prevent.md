---
title: Förhindra auktorisering med delad nyckel (förhands granskning)
titleSuffix: Azure Storage
description: Om du vill att klienter ska kunna använda Azure AD för att godkänna begär Anden kan du inte tillåta begär anden till lagrings kontot som har behörighet med delad nyckel (för hands version).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/11/2021
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: b7290abe102d22bb87c87c3c9d13ee99c127b942
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199920"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Förhindra auktorisering av delad nyckel för ett Azure Storage konto (förhands granskning)

Varje säker begäran till ett Azure Storage konto måste vara auktoriserad. Som standard kan begär Anden auktoriseras med antingen Azure Active Directory-autentiseringsuppgifter (Azure AD) eller med hjälp av konto åtkomst nyckeln för auktorisering av delad nyckel. Med de här två typerna av auktorisering ger Azure AD överlägsen säkerhet och enkel användning över delad nyckel och rekommenderas av Microsoft. Om du vill att klienter ska kunna använda Azure AD för att godkänna begär Anden kan du inte tillåta begär anden till lagrings kontot som har behörighet med delad nyckel (för hands version).

Om du inte tillåter autentisering med delad nyckel för ett lagrings konto, Azure Storage neka alla efterföljande begär anden till det kontot som har behörighet till kontots åtkomst nycklar. Endast säkra begär Anden som har auktoriserats med Azure AD kommer att lyckas. Mer information om hur du använder Azure AD finns i [bevilja åtkomst till blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

> [!IMPORTANT]
> Otillåten auktorisering av delad nyckel är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.

Den här artikeln beskriver hur du identifierar begär Anden som skickas med autentisering med delad nyckel och hur du kan åtgärda autentisering av delade nycklar för ditt lagrings konto. Information om hur du registrerar för för hands versionen finns i [om för hands versionen](#about-the-preview).

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Identifiera den typ av auktorisering som används av klient program

Om du inte tillåter autentisering med delad nyckel för ett lagrings konto kommer förfrågningar från klienter som använder konto åtkomst nycklar för auktorisering av delad nyckel att Miss förväntas. Om du vill förstå hur otillåten autentisering av delade nycklar kan påverka klient program innan du gör den här ändringen aktiverar du loggning och mått för lagrings kontot. Du kan sedan analysera mönster för förfrågningar till ditt konto under en viss tids period för att avgöra hur begär Anden auktoriseras.

Använd mått för att avgöra hur många begär Anden som ska tas emot av lagrings kontot som är auktoriserade med delad nyckel eller en signatur för delad åtkomst (SAS). Använd loggar för att avgöra vilka klienter som skickar dessa förfrågningar.

Mer information om hur du tolkar begär Anden som görs med en signatur för delad åtkomst under för hands versionen finns i [om förhands granskningen](#about-the-preview).

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Övervaka hur många begär Anden som är auktoriserade med delad nyckel

Om du vill spåra hur förfrågningar till ett lagrings konto auktoriseras använder du Azure Metrics Explorer i Azure Portal. Mer information om Metrics Explorer finns i [komma igång med Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md).

Följ dessa steg om du vill skapa ett mått som spårar begär Anden som görs med delad nyckel eller SAS:

1. Navigera till ditt lagringskonto i Azure-portalen. Under avsnittet **övervakning** väljer du **mått**.
1. Välj **Lägg till mått**. I dialog rutan **mått** anger du följande värden:
    1. Lämna fältet **scope** inställt på namnet på lagrings kontot.
    1. Ange **mått namn området** till *konto*. Detta mått kommer att rapportera om alla begär Anden mot lagrings kontot.
    1. Ange fältet **mått** till *transaktioner*.
    1. Ange **agg regerings** fältet som ska *summeras*.

    Det nya måttet visar summan av antalet transaktioner mot lagrings kontot under ett angivet tidsintervall. Det resulterande måttet visas som det visas i följande bild:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Skärm bild som visar hur du konfigurerar måttet till att summera transaktioner som görs med delad nyckel eller SAS":::

1. Välj sedan knappen **Lägg till filter** för att skapa ett filter för måttet för typen av auktorisering.
1. I dialog rutan **filter** anger du följande värden:
    1. Ange **egenskap** svärdet till *autentisering*.
    1. Ställ in **operator** -fältet på likhets tecknet (=).
    1. I fältet **värden** väljer du *konto nyckel* och *SAS*.
1. I det övre högra hörnet väljer du det tidsintervall som du vill visa måttet för. Du kan också ange hur detaljerad agg regeringen av förfrågningar ska vara genom att ange intervall var som helst från 1 minut till 1 månad. Ange till exempel **tidsintervallet** till 30 dagar och **tids kornig het** till 1 dag för att se begär Anden som sammanställs per dag under de senaste 30 dagarna.

När du har konfigurerat måttet kommer förfrågningar till ditt lagrings konto att visas i grafen. Följande bild visar begär Anden som har auktoriserats med delad nyckel eller gjorts med en SAS-token. Förfrågningarna sammanställs per dag under de senaste 30 dagarna.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Skärm bild som visar sammanställda begär Anden som har auktoriserats med delad nyckel":::

Du kan också konfigurera en varnings regel för att meddela dig när ett visst antal begär Anden som är auktoriserade med delad nyckel görs mot ditt lagrings konto. Mer information finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../../azure-monitor/alerts/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Analysera loggar för att identifiera klienter som auktoriserar begär Anden med delad nyckel eller SAS

Azure Storage loggar fångar information om begär Anden som gjorts mot lagrings kontot, inklusive hur en begäran har auktoriserats. Du kan analysera loggarna för att avgöra vilka klienter som auktoriserar begär Anden med delad nyckel eller SAS-token.

Om du vill logga förfrågningar till ditt Azure Storage-konto för att utvärdera hur de är auktoriserade kan du använda Azure Storage inloggning Azure Monitor (för hands version). Mer information finns i [övervaka Azure Storage](../blobs/monitor-blob-storage.md).

Azure Storage loggning i Azure Monitor har stöd för att använda logg frågor för att analysera loggdata. Om du vill söka i loggar kan du använda en Azure Log Analytics-arbetsyta. Mer information om logg frågor finns i [Självstudier: komma igång med Log Analytics frågor](../../azure-monitor/logs/log-analytics-tutorial.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Skapa en diagnostisk inställning i Azure Portal

Om du vill logga Azure Storage data med Azure Monitor och analysera dem med Azure Log Analytics måste du först skapa en diagnostisk inställning som anger vilka typer av begär Anden och för vilka lagrings tjänster du vill logga data. Följ dessa steg om du vill skapa en diagnostisk inställning i Azure Portal:

1. Registrera dig för för [hands versionen av Azure Storage i Azure Monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Skapa en ny Log Analytics-arbetsyta i prenumerationen som innehåller ditt Azure Storage-konto eller Använd en befintlig Log Analytics-arbetsyta. När du har konfigurerat loggning för ditt lagrings konto är loggarna tillgängliga i Log Analytics arbets ytan. Mer information finns i [skapa en Log Analytics arbets yta i Azure Portal](../../azure-monitor/logs/quick-create-workspace.md).
1. Navigera till ditt lagringskonto i Azure-portalen.
1. I avsnittet övervakning väljer du **diagnostikinställningar (för hands version)**.
1. Välj den Azure Storage tjänst som du vill logga förfrågningar för. Välj till exempel **BLOB** för att logga förfrågningar till Blob Storage.
1. Välj **Lägg till diagnostikinställning**.
1. Ange ett namn för den diagnostiska inställningen.
1. Under **kategori information** i avsnittet **logg** väljer du **StorageRead**, **StorageWrite** och **StorageDelete** för att logga alla data begär anden till den valda tjänsten.
1. Under **mål information** väljer **du skicka till Log Analytics**. Välj din prenumeration och Log Analytics arbets ytan som du skapade tidigare, som du ser i följande bild.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Skärm bild som visar hur du skapar en diagnostisk inställning för loggnings begär Anden":::

Du kan skapa en diagnostisk inställning för varje typ av Azure Storage resurs i ditt lagrings konto.

Efter att du har skapat den diagnostiska inställningen loggas begär anden till lagrings kontot sedan i enlighet med den inställningen. Mer information finns i [skapa diagnostisk inställning för insamling av resurs loggar och mått i Azure](../../azure-monitor/essentials/diagnostic-settings.md).

En referens för fält som är tillgängliga i Azure Storage loggar i Azure Monitor finns i [resurs loggar (för hands version)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Fråga efter begär Anden som görs med delad nyckel eller SAS

Azure Storage loggar i Azure Monitor innehåller den typ av auktorisering som användes för att göra en begäran till ett lagrings konto. Om du vill hämta loggar för begär Anden som gjorts under de senaste sju dagarna som har auktoriserats med delad nyckel eller SAS, öppnar du Log Analytics arbets ytan. Klistra sedan in följande fråga i en ny logg fråga och kör den. Den här frågan visar de tio IP-adresser som ofta skickas av begär Anden som har auktoriserats med delad nyckel eller SAS:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

Du kan också konfigurera en varnings regel baserat på den här frågan för att meddela dig om begär Anden som har behörighet med delad nyckel eller SAS. Mer information finns i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../../azure-monitor/alerts/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Åtgärda auktorisering via delad nyckel

När du har analyserat hur begär anden till ditt lagrings konto har auktoriserats kan du vidta åtgärder för att förhindra åtkomst via delad nyckel. Men först måste du uppdatera alla program som använder autentisering med delad nyckel för att använda Azure AD i stället. Du kan övervaka loggar och mått enligt beskrivningen i [identifiera vilken typ av auktorisering som används av klient program](#detect-the-type-of-authorization-used-by-client-applications) för att spåra över gången. Mer information om hur du använder Azure AD med blob-och Queue-data finns i [bevilja åtkomst till blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).

När du är säker på att du kan avvisa begär Anden som är auktoriserade med delad nyckel kan du ange egenskapen **AllowSharedKeyAccess** för lagrings kontot till **false**.

Egenskapen **AllowSharedKeyAccess** har inte angetts som standard och returnerar inte något värde förrän du uttryckligen anger det. Lagrings kontot tillåter begär Anden som är auktoriserade med delad nyckel när egenskap svärdet är **Null** eller när det är **Sant**.

> [!WARNING]
> Om alla klienter för närvarande har åtkomst till data i ditt lagrings konto med delad nyckel rekommenderar Microsoft att du migrerar dessa klienter till Azure AD innan du nekar åtkomst till den delade nyckeln till lagrings kontot.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ de här stegen för att förhindra autentisering av delad nyckel för ett lagrings konto i Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Leta upp **konfigurations** inställningen under **Inställningar**.
1. Ange **Tillåt delad nyckel åtkomst** till **inaktive rad**.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Skärm bild som visar hur du tillåter åtkomst till delade nycklar för kontot":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du inte vill tillåta autentisering med delad nyckel för ett lagrings konto med PowerShell installerar du [AZ. Storage PowerShell-modulen](https://www.powershellgallery.com/packages/Az.Storage), version 3.4.0 eller senare. Konfigurera sedan egenskapen **AllowSharedKeyAccess** för ett nytt eller befintligt lagrings konto.

I följande exempel visas hur du tillåter åtkomst med delad nyckel för ett befintligt lagrings konto med PowerShell. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -AllowSharedKeyAccess $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du inte vill tillåta autentisering med delad nyckel för ett lagrings konto med Azure CLI installerar du Azure CLI version 2.20.0 eller senare. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli). Konfigurera sedan egenskapen **allowSharedKeyAccess** för ett nytt eller befintligt lagrings konto.

I följande exempel visas hur du tillåter åtkomst med delad nyckel för ett befintligt lagrings konto med Azure CLI. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-shared-key-access false
```

---

När du har nekat autentisering för delad nyckel kommer en begäran till lagrings kontot med autentisering av delad nyckel att Miss Miss förklaras med felkoden 403 (tillåts inte). Azure Storage returnerar fel som indikerar att nyckelbaserad auktorisering inte tillåts på lagrings kontot.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Kontrol lera att åtkomsten till delad nyckel inte tillåts

För att verifiera att auktorisering av delad nyckel inte längre tillåts kan du försöka anropa en data åtgärd med kontots åtkomst nyckel. I följande exempel försöker skapa en behållare med hjälp av åtkomst nyckeln. Det här anropet fungerar inte när autentisering av delad nyckel inte tillåts för lagrings kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key> \
    --auth-mode key
```

> [!NOTE]
> Anonyma begär Anden är inte auktoriserade och fortsätter om du har konfigurerat lagrings kontot och behållaren för anonym offentlig Läs behörighet. Mer information finns i [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](../blobs/anonymous-read-access-configure.md).

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Kontrol lera åtkomst inställningen för delad nyckel för flera konton

Om du vill kontrol lera åtkomst inställningen för delad nyckel i en uppsättning lagrings konton med optimala prestanda kan du använda Azure Resource Graph Explorer i Azure Portal. Mer information om hur du använder resurs diagram Utforskaren finns i [snabb start: kör din första resurs diagram fråga med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

Om du kör följande fråga i resursens diagram Utforskaren returneras en lista över lagrings konton och inställningarna för delad nyckel åtkomst för varje konto visas:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="permissions-for-allowing-or-disallowing-shared-key-access"></a>Behörigheter för att tillåta eller neka åtkomst till delad nyckel

Om du vill ange egenskapen **AllowSharedKeyAccess** för lagrings kontot måste en användare ha behörighet att skapa och hantera lagrings konton. Azure-rollbaserad åtkomst kontroll (Azure RBAC) roller som tillhandahåller dessa behörigheter innefattar **Microsoft. Storage/storageAccounts/Write** eller **Microsoft. Storage/storageAccounts/ \*** action. Inbyggda roller med den här åtgärden är:

- Rollen Azure Resource Manager [ägare](../../role-based-access-control/built-in-roles.md#owner)
- Rollen Azure Resource Manager [Contributor](../../role-based-access-control/built-in-roles.md#contributor)
- Rollen [lagrings konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Dessa roller ger inte åtkomst till data i ett lagrings konto via Azure Active Directory (Azure AD). De innehåller dock **Microsoft. Storage/storageAccounts/listnycklar/Action**, som ger åtkomst till kontots åtkomst nycklar. Med den här behörigheten kan en användare använda kontots åtkomst nycklar för att komma åt alla data i ett lagrings konto.

Roll tilldelningar måste begränsas till lagrings kontots nivå eller högre för att en användare ska kunna tillåta eller neka åtkomst till delad nyckel för lagrings kontot. Mer information om roll omfattning finns i [förstå omfattning för Azure RBAC](../../role-based-access-control/scope-overview.md).

Var noga med att begränsa tilldelningen av rollerna till de som kräver möjlighet att skapa ett lagrings konto eller uppdatera dess egenskaper. Använd principen för minsta behörighet för att se till att användarna har minst de behörigheter som de behöver för att utföra sina uppgifter. Mer information om hur du hanterar åtkomst med Azure RBAC finns i [metod tips för Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Administratören för rollen administratör för den klassiska prenumerationen och Co-Administrator innehåller motsvarigheten till Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) -rollen. **Ägar** rollen innehåller alla åtgärder, så en användare med någon av dessa administrativa roller kan också skapa och hantera lagrings konton. Mer information finns i [klassiska prenumerationer på administratörs roller, Azure-roller och Azure AD-administratörer](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Förstå hur otillåten delad nyckel påverkar SAS-token

När åtkomsten till den delade nyckeln inte är tillåten för lagrings kontot, Azure Storage hanterar SAS-token baserat på typen av SAS och tjänsten som begäran riktas mot. Följande tabell visar hur varje typ av SAS är auktoriserad och hur Azure Storage kommer att hantera säkerhets associationen när **AllowSharedKeyAccess** -egenskapen för lagrings kontot är **false**.

| Typ av SAS | Typ av auktorisering | Beteende när AllowSharedKeyAccess är falskt |
|-|-|-|
| Användar delegering SAS (endast blob-lagring) | Azure AD | Begäran tillåts. Microsoft rekommenderar att du använder en användar Delegerings-SAS när det är möjligt för överlägsen säkerhet. |
| Tjänstens SAS | Delad nyckel | Begäran nekas för alla Azure Storage-tjänster. |
| Kontots SAS | Delad nyckel | Begäran nekas för alla Azure Storage-tjänster. |

Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Överväg kompatibilitet med andra Azure-verktyg och-tjänster

Ett antal Azure-tjänster använder autentisering med delad nyckel för att kommunicera med Azure Storage. Om du inte tillåter autentisering med delad nyckel för ett lagrings konto kommer dessa tjänster inte att kunna komma åt data i det kontot och dina program kan påverkas negativt.

Vissa Azure-verktyg ger dig möjlighet att använda Azure AD-auktorisering för att få åtkomst till Azure Storage. I följande tabell visas några populära Azure-verktyg och anteckningar om de kan använda Azure AD för att auktorisera begär anden till Azure Storage.

| Azure-verktyg | Azure AD-auktorisering till Azure Storage |
|-|-|
| Azure Portal | Stöds. Information om hur du auktoriserar med ditt Azure AD-konto från Azure Portal finns i [Välj hur du godkänner åtkomst till BLOB-data i Azure Portal](../blobs/authorize-data-operations-portal.md). |
| AzCopy | Stöds för Blob Storage. Information om hur du auktoriserar AzCopy-åtgärder finns i [Välj hur du ska ange autentiseringsuppgifter för auktorisering](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) i AzCopy-dokumentationen. |
| Azure Storage Explorer | Stöds endast för Blob Storage och Azure Data Lake Storage Gen2. Azure AD-åtkomst till Queue Storage stöds inte. Se till att välja rätt Azure AD-klient. Mer information finns i [Kom igång med Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | Stöds. Information om hur du auktoriserar PowerShell-kommandon för BLOB-eller Queue-åtgärder med Azure AD finns i [köra PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-data](../blobs/authorize-data-operations-powershell.md) eller [köra PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att komma åt köa data](../queues/authorize-data-operations-powershell.md) |
| Azure CLI | Stöds. Information om hur du auktoriserar Azure CLI-kommandon med Azure AD för åtkomst till blob-och Queue-data finns i [köra Azure CLI-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data](../blobs/authorize-data-operations-cli.md). |
| Azure IoT Hub | Stöds. Mer information finns i [IoT Hub stöd för virtuella nätverk](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | Azure Cloud Shell är ett integrerat gränssnitt i Azure Portal. Azure Cloud Shell Hosts-filer för persistence i en Azure-filresurs i ett lagrings konto. De här filerna blir otillgängliga om inte auktorisering av den delade nyckeln är tillåtet för det lagrings kontot. Mer information finns i [Anslut lagringen för Microsoft Azure filer](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage). <br /><br /> Om du vill köra kommandon i Azure Cloud Shell för att hantera lagrings konton för vilka åtkomst till den delade nyckeln är otillåten, kontrol lera först att du har beviljats de behörigheter som krävs för dessa konton via Azure RBAC. Mer information finns i [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)?](../../role-based-access-control/overview.md). |

## <a name="transition-azure-files-and-table-storage-workloads"></a>Över gång Azure Files-och tabell lagrings arbets belastningar

Azure Storage stöder endast Azure AD-auktorisering för förfrågningar till blob-och Queue-lagring. Om du inte tillåter auktorisering med delad nyckel för ett lagrings konto kommer förfrågningar till Azure Files eller tabell lagring som använder autentisering med delad nyckel att Miss Miss förväntas. Eftersom Azure Portal alltid använder autentisering med delad nyckel för att komma åt fil-och tabell data, och om du inte tillåter auktorisering med delad nyckel för lagrings kontot, kommer du inte att kunna komma åt fil-eller tabell data i Azure Portal.

Microsoft rekommenderar att du antingen migrerar Azure Files-eller tabell lagrings data till ett separat lagrings konto innan du tillåter åtkomst till kontot via den delade nyckeln, eller att du inte tillämpar den här inställningen på lagrings konton som stöder Azure Files-eller tabell lagrings arbets belastningar.

Att neka åtkomst till delade nycklar för ett lagrings konto påverkar inte SMB-anslutningar till Azure Files.

## <a name="about-the-preview"></a>Om för hands versionen

För hands versionen för att tillåta autentisering av delad nyckel är tillgänglig i det offentliga Azure-molnet. Det finns stöd för lagrings konton som endast använder Azure Resource Manager distributions modell. Information om vilka lagrings konton som använder Azure Resource Manager distributions modell finns i [typer av lagrings konton](storage-account-overview.md#types-of-storage-accounts).

Förhands granskningen innehåller de begränsningar som beskrivs i följande avsnitt.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Mått och loggning rapporterar alla begär Anden som görs med en SAS, oavsett hur de är auktoriserade

Azures mått och inloggnings Azure Monitor skiljer inte mellan olika typer av signaturer för delad åtkomst i förhands granskningen. **SAS** -filtret i Azure Metrics Explorer och fältet **SAS** i Azure Storage inloggning Azure Monitor båda rapport begär Anden som har behörighet till alla typer av SAS. Olika typer av signaturer för delad åtkomst tillåts dock annorlunda och beter sig annorlunda när åtkomsten till delad nyckel inte tillåts:

- En SAS-token för tjänsten eller en SAS-token för ett konto är auktoriserad med en delad nyckel och kommer inte att tillåtas på en begäran till Blob Storage när **AllowSharedKeyAccess** -egenskapen har angetts till **false**.
- En användar Delegerings-SAS är auktoriserad med Azure AD och kommer att tillåtas på en begäran till Blob Storage när **AllowSharedKeyAccess** -egenskapen har angetts till **false**.

När du utvärderar trafik till ditt lagrings konto bör du tänka på att mått och loggar enligt beskrivningen i [identifiera vilken typ av auktorisering som används av klient program](#detect-the-type-of-authorization-used-by-client-applications) kan innehålla begär Anden som görs med en användar Delegerings-SAS. Mer information om hur Azure Storage svarar på en SAS när egenskapen **AllowSharedKeyAccess** är inställd på **false** finns i [förstå hur otillåten delad nyckel påverkar SAS-token](#understand-how-disallowing-shared-key-affects-sas-tokens).

## <a name="next-steps"></a>Nästa steg

- [Auktorisera åtkomst till data i Azure Storage](storage-auth.md)
- [Ge åtkomst till blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md)
- [Auktorisera med delad nyckel](/rest/api/storageservices/authorize-with-shared-key)
