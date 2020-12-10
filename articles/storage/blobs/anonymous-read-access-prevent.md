---
title: Förhindra anonym offentlig Läs behörighet till behållare och blobbar
titleSuffix: Azure Storage
description: Lär dig hur du analyserar anonyma begär Anden mot ett lagrings konto och hur du förhindrar anonym åtkomst för hela lagrings kontot eller för en enskild behållare.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 179e60a41a9cd6a2277959b3cd31159c796d845d
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937295"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Förhindra anonym offentlig Läs behörighet till behållare och blobbar

Anonym offentlig Läs behörighet till behållare och blobbar i Azure Storage är ett bekvämt sätt att dela data, men det kan också innebära en säkerhets risk. Det är viktigt att du hanterar anonym åtkomst sparsamt och förstår hur du utvärderar anonym åtkomst till dina data. Drifts komplexitet, mänskligt fel eller skadlig attack mot data som är allmänt tillgängliga kan leda till kostsamma data intrång. Microsoft rekommenderar att du bara aktiverar anonym åtkomst när det är nödvändigt för ditt program scenario.

Som standard är offentlig åtkomst till dina BLOB-data alltid förbjuden. Standard konfigurationen för ett lagrings konto tillåter dock en användare med rätt behörighet att konfigurera offentlig åtkomst till behållare och blobbar i ett lagrings konto. För ökad säkerhet kan du neka all offentlig åtkomst till lagrings kontot, oavsett inställningen för offentlig åtkomst för en enskild behållare. Att neka offentlig åtkomst till lagrings kontot förhindrar att en användare aktiverar offentlig åtkomst för en behållare i kontot. Microsoft rekommenderar att du inte tillåter offentlig åtkomst till ett lagrings konto om inte ditt scenario kräver det. Om du inte tillåter offentlig åtkomst kan du förhindra data intrång orsakade av oönskad anonym åtkomst.

Om du inte tillåter offentlig BLOB-åtkomst för lagrings kontot, Azure Storage avvisar alla anonyma begär anden till det kontot. När offentlig åtkomst inte tillåts för ett konto kan behållare i det kontot inte konfigureras senare för offentlig åtkomst. Alla behållare som redan har kon figurer ATS för offentlig åtkomst accepterar inte längre anonyma begär Anden. Mer information finns i [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](anonymous-read-access-configure.md).

Den här artikeln beskriver hur du använder ett ramverk för att dra (identifierings-och gransknings styrning) för att kontinuerligt hantera offentlig åtkomst för dina lagrings konton.

## <a name="detect-anonymous-requests-from-client-applications"></a>Identifiera anonyma begär Anden från klient program

Om du inte tillåter offentlig Läs behörighet för ett lagrings konto riskerar du att avvisa begär anden till behållare och blobbar som för närvarande är konfigurerade för offentlig åtkomst. Om den offentliga åtkomsten för ett lagrings konto nekas åsidosätts de offentliga åtkomst inställningarna för enskilda behållare i lagrings kontot. När offentlig åtkomst inte tillåts för lagrings kontot kommer alla framtida anonyma begär anden till det kontot att Miss förväntas.

För att förstå hur otillåten offentlig åtkomst kan påverka klient program, rekommenderar Microsoft att du aktiverar loggning och mått för kontot och analyserar mönster för anonyma begär Anden under ett tidsintervall. Använd mått för att fastställa antalet anonyma begär anden till lagrings kontot och Använd loggar för att avgöra vilka behållare som ska användas anonymt.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Övervaka anonyma begär Anden med Metrics Explorer

Om du vill spåra anonyma begär anden till ett lagrings konto använder du Azure Metrics Explorer i Azure Portal. Mer information om Metrics Explorer finns i [komma igång med Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Följ dessa steg om du vill skapa ett mått som spårar anonyma begär Anden:

1. Navigera till ditt lagringskonto i Azure-portalen. Under avsnittet **övervakning** väljer du **mått**.
1. Välj **Lägg till mått**. I dialog rutan **mått** anger du följande värden:
    1. Lämna fältet scope inställt på namnet på lagrings kontot.
    1. Ange **mått namn området** till *BLOB*. Det här måttet rapporterar endast begär Anden mot Blob Storage.
    1. Ange fältet **mått** till *transaktioner*.
    1. Ange **agg regerings** fältet som ska *summeras*.

    Det nya måttet visar summan av antalet transaktioner för Blob Storage under ett angivet tidsintervall. Det resulterande måttet visas som det visas i följande bild:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Skärm bild som visar hur du konfigurerar måttet till att summera BLOB-transaktioner":::

1. Välj sedan knappen **Lägg till filter** för att skapa ett filter på måttet för anonyma begär Anden.
1. I dialog rutan **filter** anger du följande värden:
    1. Ange **egenskap** svärdet till *autentisering*.
    1. Ställ in **operator** -fältet på likhets tecknet (=).
    1. Ange fältet **värden** som *Anonym*.
1. I det övre högra hörnet väljer du det tidsintervall som du vill visa måttet för. Du kan också ange hur detaljerad agg regeringen av förfrågningar ska vara genom att ange intervall var som helst från 1 minut till 1 månad.

När du har konfigurerat måttet kommer anonyma begär Anden att visas i grafen. Följande bild visar de anonyma begär Anden som aggregerats under de senaste 30 minuterna.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Skärm bild som visar sammanställda anonyma begär Anden mot Blob Storage":::

Du kan också konfigurera en varnings regel för att meddela dig när ett visst antal anonyma begär Anden görs mot ditt lagrings konto. Mer information finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analysera loggar för att identifiera behållare som tar emot anonyma begär Anden

Azure Storage loggar fångar information om begär Anden som gjorts mot lagrings kontot, inklusive hur en begäran har auktoriserats. Du kan analysera loggarna för att avgöra vilka behållare som tar emot anonyma begär Anden.

Om du vill logga begär anden till ditt Azure Storage konto för att utvärdera anonyma begär Anden, kan du använda Azure Storage inloggning Azure Monitor (för hands version). Mer information finns i [övervaka Azure Storage](./monitor-blob-storage.md).

Azure Storage loggning i Azure Monitor har stöd för att använda logg frågor för att analysera loggdata. Om du vill söka i loggar kan du använda en Azure Log Analytics-arbetsyta. Mer information om logg frågor finns i [Självstudier: komma igång med Log Analytics frågor](../../azure-monitor/log-query/log-analytics-tutorial.md).

> [!NOTE]
> Förhands granskningen av Azure Storage loggning i Azure Monitor stöds bara i det offentliga Azure-molnet. Offentliga moln stöder inte loggning för Azure Storage med Azure Monitor.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Skapa en diagnostisk inställning i Azure Portal

Om du vill logga Azure Storage data med Azure Monitor och analysera dem med Azure Log Analytics måste du först skapa en diagnostisk inställning som anger vilka typer av begär Anden och för vilka lagrings tjänster du vill logga data. Följ dessa steg om du vill skapa en diagnostisk inställning i Azure Portal:

1. Registrera dig för för [hands versionen av Azure Storage i Azure Monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Skapa en ny Log Analytics-arbetsyta i prenumerationen som innehåller ditt Azure Storage-konto. När du har konfigurerat loggning för ditt lagrings konto är loggarna tillgängliga i Log Analytics arbets ytan. Mer information finns i [skapa en Log Analytics arbets yta i Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
1. Navigera till ditt lagringskonto i Azure-portalen.
1. I avsnittet övervakning väljer du **diagnostikinställningar (för hands version)**.
1. Välj **BLOB** för att logga förfrågningar som görs mot Blob Storage.
1. Välj **Lägg till diagnostikinställning**.
1. Ange ett namn för den diagnostiska inställningen.
1. Under **kategori information** i avsnittet **logg** väljer du vilka typer av begär Anden som ska loggas. Alla anonyma begär Anden kommer att läsas, så välj **StorageRead** för att avbilda anonyma begär Anden.
1. Under **mål information** väljer **du skicka till Log Analytics**. Välj din prenumeration och Log Analytics arbets ytan som du skapade tidigare, som du ser i följande bild.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Skärm bild som visar hur du skapar en diagnostisk inställning för loggnings begär Anden":::

Efter att du har skapat den diagnostiska inställningen loggas begär anden till lagrings kontot sedan i enlighet med den inställningen. Mer information finns i [skapa diagnostisk inställning för insamling av resurs loggar och mått i Azure](../../azure-monitor/platform/diagnostic-settings.md).

En referens för fält som är tillgängliga i Azure Storage loggar i Azure Monitor finns i [resurs loggar (för hands version)](./monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Query-loggar för anonyma begär Anden

Azure Storage loggar i Azure Monitor innehåller den typ av auktorisering som användes för att göra en begäran till ett lagrings konto. Filtrera på egenskapen **AuthenticationType** i din logg fråga för att Visa anonyma begär Anden.

Om du vill hämta loggar för de senaste 7 dagarna för anonyma begär Anden mot Blob Storage öppnar du Log Analytics arbets ytan. Klistra sedan in följande fråga i en ny logg fråga och kör den:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Du kan också konfigurera en varnings regel baserat på den här frågan för att meddela dig om anonyma begär Anden. Mer information finns i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Reparera anonym offentlig åtkomst

När du har utvärderat anonyma begär anden till behållare och blobbar i ditt lagrings konto kan du vidta åtgärder för att begränsa eller förhindra offentlig åtkomst. Om några behållare i lagrings kontot kan behöva vara tillgängliga för offentlig åtkomst kan du konfigurera den offentliga åtkomst inställningen för varje behållare i ditt lagrings konto. Det här alternativet ger den mest detaljerade kontrollen över offentlig åtkomst. Mer information finns i [Ange offentlig åtkomst nivå för en behållare](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

För ökad säkerhet kan du neka offentlig åtkomst för hela lagrings kontot. Den offentliga åtkomst inställningen för ett lagrings konto åsidosätter de enskilda inställningarna för behållare i det kontot. Om du inte tillåter offentlig åtkomst för ett lagrings konto är alla behållare som är konfigurerade för att tillåta offentlig åtkomst inte längre tillgängliga anonymt. Mer information finns i [Tillåt eller neka offentlig Läs behörighet för ett lagrings konto](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Om ditt scenario kräver att vissa behållare måste vara tillgängliga för offentlig åtkomst kan det vara lämpligt att flytta dessa behållare och deras blobbar till lagrings konton som är reserverade för offentlig åtkomst. Du kan sedan neka offentlig åtkomst för andra lagrings konton.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Kontrol lera att offentlig åtkomst till en BLOB inte tillåts

För att kontrol lera att offentlig åtkomst till en viss BLOB inte tillåts kan du försöka hämta blobben via dess URL. Om hämtningen lyckas är blobben fortfarande offentligt tillgänglig. Om blobben inte är offentligt tillgänglig eftersom offentlig åtkomst har varit inaktive ras för lagrings kontot visas ett fel meddelande som anger att offentlig åtkomst inte tillåts på det här lagrings kontot.

I följande exempel visas hur du använder PowerShell för att försöka ladda ned en BLOB via URL: en. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Kontrol lera att det inte är tillåtet att ändra behållarens offentliga åtkomst inställning

För att verifiera att en behållares offentliga åtkomst inställning inte kan ändras efter att offentlig åtkomst inte tillåts för lagrings kontot kan du försöka ändra inställningen. Det går inte att ändra behållarens offentliga åtkomst inställning om ingen offentlig åtkomst tillåts för lagrings kontot.

I följande exempel visas hur du använder PowerShell för att försöka ändra en behållares offentliga åtkomst inställning. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Kontrol lera att det inte är tillåtet att skapa en behållare med aktive rad offentlig åtkomst

Om offentlig åtkomst inte tillåts för lagrings kontot kan du inte skapa en ny behållare med offentlig åtkomst aktive rad. För att verifiera kan du försöka skapa en behållare med offentlig åtkomst aktive rad.

I följande exempel visas hur du använder PowerShell för att försöka skapa en behållare med offentlig åtkomst aktive rad. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Kontrol lera inställningen för offentlig åtkomst för flera konton

Om du vill kontrol lera inställningen för offentlig åtkomst i en uppsättning lagrings konton med optimala prestanda kan du använda Azures resurs diagram Utforskaren i Azure Portal. Mer information om hur du använder resurs diagram Utforskaren finns i [snabb start: kör din första resurs diagram fråga med Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

Egenskapen **AllowBlobPublicAccess** har inte angetts för ett lagrings konto som standard och returnerar inget värde förrän du uttryckligen anger det. Lagrings kontot tillåter offentlig åtkomst när egenskap svärdet är antingen **Null** eller **Sant**.

När du kör följande fråga i resursens diagram Utforskaren returneras en lista över lagrings konton och den offentliga åtkomst inställningen för varje konto visas:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

Följande bild visar resultatet av en fråga i en prenumeration. Observera att för lagrings konton där **AllowBlobPublicAccess** -egenskapen uttryckligen har angetts visas den i resultatet som **Sant** eller **falskt**. Om egenskapen **AllowBlobPublicAccess** inte har angetts för ett lagrings konto visas det som tomt (eller null) i frågeresultatet.

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="Skärm bild som visar ett frågeresultat för offentlig åtkomst inställning över lagrings konton":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>Använda Azure Policy för att granska kompatibilitet

Om du har ett stort antal lagrings konton kanske du vill utföra en granskning för att se till att dessa konton är konfigurerade för att förhindra offentlig åtkomst. Om du vill granska en uppsättning lagrings konton för deras kompatibilitet använder du Azure Policy. Azure Policy är en tjänst som du kan använda för att skapa, tilldela och hantera principer som tillämpar regler på Azure-resurser. Azure Policy hjälper dig att hålla resurserna kompatibla med företagets standarder och service nivå avtal. Mer information finns i [Översikt över Azure policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Skapa en princip med en gransknings funktion

Azure Policy stöder effekter som avgör vad som händer när en princip regel utvärderas mot en resurs. Gransknings funktionen skapar en varning när en resurs inte är kompatibel, men den stoppar inte begäran. Mer information om effekter finns i [förstå Azure policys effekter](../../governance/policy/concepts/effects.md).

Följ dessa steg om du vill skapa en princip med en gransknings funktion för den offentliga åtkomst inställningen för ett lagrings konto med Azure Portal:

1. I Azure Portal navigerar du till tjänsten Azure Policy.
1. Under avsnittet **redigering** väljer du **definitioner**.
1. Välj **Lägg till princip definition** för att skapa en ny princip definition.
1. I fältet **definitions plats** väljer du knappen **mer** för att ange var gransknings princip resursen finns.
1. Ange ett namn för principen. Du kan också ange en beskrivning och kategori.
1. Under **princip regel** lägger du till följande princip definition i **policyRule** -avsnittet.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Spara principen.

### <a name="assign-the-policy"></a>Tilldela principen

Tilldela sedan principen till en resurs. Principens omfattning motsvarar resursen och eventuella resurser under den. Mer information om princip tilldelning finns i [Azure policy tilldelnings struktur](../../governance/policy/concepts/assignment-structure.md).

Följ dessa steg om du vill tilldela principen till Azure Portal:

1. I Azure Portal navigerar du till tjänsten Azure Policy.
1. Under avsnittet **redigering** väljer du **tilldelningar**.
1. Välj **tilldela princip** om du vill skapa en ny princip tilldelning.
1. I fältet **omfattning** väljer du omfånget för princip tilldelningen.
1. I fältet **princip definition** väljer du knappen **mer** och väljer sedan den princip som du definierade i föregående avsnitt i listan.
1. Ange ett namn för princip tilldelningen. Beskrivningen är valfri.
1. Aktivera **tvingande princip** uppsättning till *aktive rad*. Den här inställningen har ingen inverkan på gransknings principen.
1. Välj **Granska + skapa** för att skapa tilldelningen.

### <a name="view-compliance-report"></a>Visa Kompatibilitetsrapport

När du har tilldelat principen kan du Visa rapporten efterlevnad. Kompatibilitetsrapport för en gransknings princip innehåller information om vilka lagrings konton som inte är kompatibla med principen. Mer information finns i [Hämta efterlevnadsprincip](../../governance/policy/how-to/get-compliance-data.md)för information.

Det kan ta flera minuter för rapporten att bli tillgänglig när princip tilldelningen har skapats.

Följ dessa steg om du vill visa Kompatibilitetsrapport i Azure Portal:

1. I Azure Portal navigerar du till tjänsten Azure Policy.
1. Välj **efterlevnad**.
1. Filtrera resultaten för namnet på princip tilldelningen som du skapade i föregående steg. Rapporten visar hur många resurser som inte är kompatibla med principen.
1. Du kan öka detalj nivån i rapporten för ytterligare information, inklusive en lista över lagrings konton som inte är kompatibla.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Skärm bild som visar Kompatibilitetsrapport för gransknings principen för offentlig BLOB-åtkomst":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Använd Azure Policy för att genomdriva auktoriserad åtkomst

Azure Policy stöder moln styrning genom att se till att Azure-resurserna följer krav och standarder. För att säkerställa att lagrings konton i din organisation tillåter endast auktoriserade begär Anden, kan du skapa en princip som förhindrar att ett nytt lagrings konto skapas med en offentlig åtkomst inställning som tillåter anonyma begär Anden. Den här principen förhindrar även konfigurations ändringar till ett befintligt konto om den offentliga åtkomst inställningen för det kontot inte är kompatibel med principen.

Den tvingande principen använder funktionen neka för att förhindra en begäran som skapar eller ändrar ett lagrings konto för att tillåta offentlig åtkomst. Mer information om effekter finns i [förstå Azure policys effekter](../../governance/policy/concepts/effects.md).

Om du vill skapa en princip med en neka-påverkan för en offentlig åtkomst inställning som tillåter anonyma begär Anden följer du samma steg som beskrivs i [använda Azure policy för att granska kompatibilitet](#use-azure-policy-to-audit-for-compliance), men ange följande JSON i avsnittet **policyRule** i princip definitionen:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

När du har skapat principen med neka-inställningen och tilldelar den till ett omfång kan en användare inte skapa ett lagrings konto som tillåter offentlig åtkomst. Du kan inte heller göra några konfigurations ändringar i ett befintligt lagrings konto som för närvarande tillåter offentlig åtkomst. Om du försöker göra det uppstår ett fel. Den offentliga åtkomst inställningen för lagrings kontot måste anges till **false** för att det ska gå att skapa eller konfigurera konton.

Följande bild visar det fel som uppstår om du försöker skapa ett lagrings konto som tillåter offentlig åtkomst (standard för ett nytt konto) när en princip med en neka-påverkan kräver att offentlig åtkomst inte tillåts.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="Skärm bild som visar felet som inträffar när du skapar ett lagrings konto som strider mot principen":::

## <a name="permissions-for-allowing-or-disallowing-public-access"></a>Behörigheter för att tillåta eller neka offentlig åtkomst

Om du vill ange egenskapen **AllowBlobPublicAccess** för lagrings kontot måste en användare ha behörighet att skapa och hantera lagrings konton. Azure-rollbaserad åtkomst kontroll (Azure RBAC) roller som tillhandahåller dessa behörigheter innefattar åtgärden **Microsoft. Storage/storageAccounts/Write** eller **Microsoft. Storage/storageAccounts/ \** _. Inbyggda roller med den här åtgärden är:

- Rollen Azure Resource Manager [ägare](../../role-based-access-control/built-in-roles.md#owner)
- Rollen Azure Resource Manager [Contributor](../../role-based-access-control/built-in-roles.md#contributor)
- Rollen [lagrings konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Dessa roller ger inte åtkomst till data i ett lagrings konto via Azure Active Directory (Azure AD). De inkluderar dock * Microsoft. Storage/storageAccounts/listnycklar/Action * *, som ger åtkomst till kontots åtkomst nycklar. Med den här behörigheten kan en användare använda kontots åtkomst nycklar för att komma åt alla data i ett lagrings konto.

Roll tilldelningar måste begränsas till lagrings kontots nivå eller högre för att en användare ska kunna tillåta eller neka offentlig åtkomst för lagrings kontot. Mer information om roll omfattning finns i [förstå omfattning för Azure RBAC](../../role-based-access-control/scope-overview.md).

Var noga med att begränsa tilldelningen av rollerna till de som kräver möjlighet att skapa ett lagrings konto eller uppdatera dess egenskaper. Använd principen för minsta behörighet för att se till att användarna har minst de behörigheter som de behöver för att utföra sina uppgifter. Mer information om hur du hanterar åtkomst med Azure RBAC finns i [metod tips för Azure RBAC](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Administratören för rollen administratör för den klassiska prenumerationen och Co-Administrator innehåller motsvarigheten till Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) -rollen. **Ägar** rollen innehåller alla åtgärder, så en användare med någon av dessa administrativa roller kan också skapa och hantera lagrings konton. Mer information finns i [klassiska prenumerationer på administratörs roller, Azure-roller och Azure AD-administratörer](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](anonymous-read-access-configure.md)
- [Få åtkomst till offentliga behållare och blobbar anonymt med .NET](anonymous-read-access-client.md)