---
title: Skydda åtkomst och data
description: Säker åtkomst till indata, utdata, begär ande-baserade utlösare, körnings historik, hanterings uppgifter och åtkomst till andra resurser i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: a53ac9387664aafc218f13834e0499fde417d87d
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566088"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Säker åtkomst och data i Azure Logic Apps

Azure Logic Apps förlitar sig på [Azure Storage](../storage/index.yml) för att lagra och automatiskt [kryptera data i vila](../security/fundamentals/encryption-atrest.md). Den här krypteringen skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Som standard använder Azure Storage Microsoft-hanterade nycklar för att kryptera dina data. Mer information finns i [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md).

Om du vill kontrol lera åtkomsten och skydda känsliga data i Azure Logic Apps kan du konfigurera ytterligare säkerhet i följande områden:

* [Åtkomst till begär ande-baserade utlösare](#secure-triggers)
* [Åtkomst till Logic app-åtgärder](#secure-operations)
* [Åtkomst för att köra tidigare indata och utdata](#secure-run-history)
* [Åtkomst till parameter indata](#secure-action-parameters)
* [Åtkomst till tjänster och system som anropas från Logic Apps](#secure-outbound-requests)
* [Blockera skapande av anslutningar för vissa anslutningar](#block-connections)
* [Isolerings vägledning för Logic Apps](#isolation-logic-apps)
* [Azures säkerhets bas linje för Azure Logic Apps](../logic-apps/security-baseline.md)

Mer information om säkerhet i Azure finns i följande avsnitt:

* [Översikt över Azure-kryptering](../security/fundamentals/encryption-overview.md)
* [Azure Data Encryption – i vila](../security/fundamentals/encryption-atrest.md)
* [Benchmark för Azure-säkerhet](../security/benchmarks/overview.md)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Åtkomst till begär ande-baserade utlösare

Om din Logic app använder en begärd utlösare, som tar emot inkommande samtal eller begär Anden, till exempel [begäran](../connectors/connectors-native-reqres.md) eller [webhook](../connectors/connectors-native-webhook.md) -utlösare, kan du begränsa åtkomsten så att endast auktoriserade klienter kan anropa din Logic app. Alla begär Anden som tas emot av en Logic app krypteras och skyddas med Transport Layer Security-protokollet (TLS), tidigare kallat Secure Sockets Layer (SSL).

Här följer alternativ som kan hjälpa dig att skydda åtkomsten till den här utlösaren:

* [Generera signaturer för delad åtkomst](#sas)
* [Aktivera Azure Active Directory öppna autentisering (Azure AD OAuth)](#enable-oauth)
* [Begränsa inkommande IP-adresser](#restrict-inbound-ip-addresses)
* [Lägg till Azure Active Directory öppna autentisering (Azure AD OAuth) eller annan säkerhet](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Generera signaturer för delad åtkomst (SAS)

Varje begär ande slut punkt i en Logic app har en [signatur för delad åtkomst (SAS)](/rest/api/storageservices/constructing-a-service-sas) i slut punktens URL, som följer detta format:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Varje URL innehåller `sp` Frågeparametern, `sv` och, `sig` enligt beskrivningen i den här tabellen:

| Frågeparameter | Description |
|-----------------|-------------|
| `sp` | Anger behörigheter för de tillåtna HTTP-metoder som ska användas. |
| `sv` | Anger den SAS-version som ska användas för att skapa signaturen. |
| `sig` | Anger signaturen som ska användas för att autentisera åtkomsten till utlösaren. Den här signaturen genereras med hjälp av SHA256-algoritmen med en hemlig åtkomst nyckel på alla URL-sökvägar och egenskaper. Aldrig exponeras eller publiceras den här nyckeln krypteras och lagras med Logic app. Din Logic app godkänner bara de utlösare som innehåller en giltig signatur som skapats med den hemliga nyckeln. |
|||

Mer information om hur du skyddar åtkomst med SAS finns i följande avsnitt i det här avsnittet:

* [Återskapa åtkomstnycklar](#access-keys)
* [Skapa återanrops-URL: er som upphör](#expiring-urls)
* [Skapa URL: er med primär eller sekundär nyckel](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

Om du vill skapa en ny säkerhets åtkomst nyckel när som helst använder du Azure-REST API eller Azure Portal. Alla tidigare genererade URL: er som använder den gamla nyckeln är ogiltiga och har inte längre behörighet att utlösa Logic-appen. URL: erna som du hämtar efter återskapandet är signerad med den nya åtkomst nyckeln.

1. I [Azure Portal](https://portal.azure.com)öppnar du den Logic-app som har den nyckel som du vill återskapa.

1. Gå till menyn för Logic-appen, Välj **åtkomst nycklar**under **Inställningar**.

1. Välj den nyckel som du vill återskapa och slutföra processen.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Skapa återanrops-URL: er som upphör

Om du delar slut punkts-URL: en för en begärd utlösare med andra parter kan du generera återanrops-URL: er som använder vissa nycklar och som har förfallo datum. På så sätt kan du sömlöst återställa nycklar eller begränsa åtkomsten till att utlösa din Logi Kap par baserat på ett angivet tidsintervall. Om du vill ange ett förfallo datum för en URL använder du [Logic Apps REST API](/rest/api/logic/workflowtriggers), till exempel:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

I bröd texten inkluderar du `NotAfter` egenskapen med hjälp av en JSON-datum sträng. Den här egenskapen returnerar en callback-URL som endast är giltig fram till `NotAfter` datum och tid.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Skapa URL: er med primär eller sekundär hemlig nyckel

När du skapar eller listar återanrops-URL: er för en begärd utlösare kan du ange den nyckel som ska användas för att signera URL: en. Om du vill generera en URL som är signerad av en viss nyckel använder du [Logic Apps REST API](/rest/api/logic/workflowtriggers), till exempel:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

I bröd texten inkluderar du `KeyType` egenskapen som antingen `Primary` eller `Secondary` . Den här egenskapen returnerar en URL som är signerad av den angivna säkerhets nyckeln.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-oauth"></a>Aktivera Azure Active Directory OAuth

Om din Logic-app startar med en [begär ande utlösare](../connectors/connectors-native-reqres.md), kan du aktivera [Azure Active Directory öppna autentisering](../active-directory/develop/index.yml) (Azure AD OAuth) genom att definiera eller lägga till en auktoriseringsprincip för inkommande anrop till begär ande utlösaren. När din Logic app tar emot en inkommande begäran som innehåller en autentiseringstoken, jämför Azure Logic Apps token-anspråk mot anspråk i varje auktoriseringsprincip. Om det finns en matchning mellan token-anspråk och alla anspråk i minst en princip, lyckas auktoriseringen för den inkommande begäran. Token kan ha fler anspråk än det antal som anges av auktoriseringsprincipen.

Anta till exempel att din Logic app har en auktoriseringsprincip som kräver två anspråks typer, **utfärdare** och **mål grupp**. Den här exempel koden för avkodad [åtkomsttoken](../active-directory/develop/access-tokens.md) innehåller båda dessa anspråks typer:

```json
{
   "aud": "https://management.core.windows.net/",
   "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
   "iat": 1582056988,
   "nbf": 1582056988,
   "exp": 1582060888,
   "_claim_names": {
      "groups": "src1"
   },
   "_claim_sources": {
      "src1": {
         "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
    }
   },
   "acr": "1",
   "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
   "amr": [
      "rsa",
      "mfa"
   ],
   "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
   "appidacr": "2",
   "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
   "family_name": "Sophia Owen",
   "given_name": "Sophia Owen (Fabrikam)",
   "ipaddr": "167.220.2.46",
   "name": "sophiaowen",
   "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
   "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
   "puid": "1003000000098FE48CE",
   "scp": "user_impersonation",
   "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
   "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
   "unique_name": "SophiaOwen@fabrikam.com",
   "upn": "SophiaOwen@fabrikam.com",
   "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
   "ver": "1.0"
}
```

#### <a name="considerations-for-enabling-azure-oauth"></a>Att tänka på när du aktiverar Azure OAuth

Innan du aktiverar den här autentiseringen bör du gå igenom följande överväganden:

* Ett inkommande samtal till din Logi Kap par kan bara använda ett Authorization-schema, antingen Azure AD OAuth eller [signaturen för delad åtkomst (SAS)](#sas). Om du använder ett schema inaktive ras inte det andra, men om du använder båda samtidigt uppstår ett fel eftersom tjänsten inte vet vilket schema du ska välja. Endast [Bearer-typ](../active-directory/develop/active-directory-v2-protocols.md#tokens) stöds för OAuth-tokens som endast stöds för begär ande utlösare.

* Din Logic app är begränsad till ett maximalt antal Auktoriseringsprinciper. Varje auktoriseringsprincip har också ett maximalt antal [anspråk](../active-directory/develop/developer-glossary.md#claim). Mer information finns i [gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* En auktoriseringsprincip måste innehålla minst **utfärdarens** anspråk, som har ett värde som börjar med `https://sts.windows.net/` eller `https://login.microsoftonline.com/` (OAuth v2) som ID för Azure AD-utfärdaren. Mer information om åtkomsttoken finns i [Microsoft Identity Platform Access tokens](../active-directory/develop/access-tokens.md).

<a name="define-authorization-policy-portal"></a>

#### <a name="define-authorization-policy-in-azure-portal"></a>Definiera auktoriseringsprincipen i Azure Portal

Om du vill aktivera Azure AD OAuth för din Logic-app i Azure Portal följer du dessa steg för att lägga till en eller flera auktoriseringsprinciper i din Logic app:

1. I [Azure Portal](https://portal.microsoft.com)kan du söka efter och öppna din Logic app i Logic App Designer.

1. På menyn Logic app, under **Inställningar**, väljer du **auktorisering**. När fönstret behörighet öppnas väljer du **Lägg till princip**.

   ![Välj "auktorisering" > Lägg till princip "](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Ange information om auktoriseringsprincipen genom att ange de [anspråks typer](../active-directory/develop/developer-glossary.md#claim) och värden som din Logic app förväntar sig i autentiseringstoken som presenteras av varje inkommande anrop till begär ande utlösaren:

   ![Ange information för auktoriseringsprincipen](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Princip namn** | Yes | Det namn som du vill använda för auktoriseringsprincipen |
   | **Anspråk** | Yes | De anspråks typer och värden som din Logic app accepterar från inkommande samtal. Här följer tillgängliga anspråks typer: <p><p>- **Utfärdare** <br>- **Filmen** <br>- **Motiv** <br>- **JWT-ID** (JSON Web token-ID) <p><p>Som minimum måste **anspråks** listan innehålla **utfärdarens** anspråk, som har ett värde som börjar med `https://sts.windows.net/` eller `https://login.microsoftonline.com/` som ID för Azure AD-utfärdaren. Mer information om dessa anspråks typer finns i [anspråk i Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)-säkerhetstoken. Du kan också ange en egen typ och värde för anspråk. |
   |||

1. Om du vill lägga till ett annat anspråk väljer du bland följande alternativ:

   * Om du vill lägga till en annan anspråks typ väljer du **Lägg till standard anspråk**, väljer anspråks typ och anger anspråks värde.

   * Om du vill lägga till ett eget anspråk väljer du **Lägg till anpassat anspråk**och anger det anpassade anspråks värdet.

1. Om du vill lägga till en annan auktoriseringsprincip väljer du **Lägg till princip**. Upprepa föregående steg för att konfigurera principen.

1. När du är klar väljer du **Spara**.

<a name="define-authorization-policy-template"></a>

#### <a name="define-authorization-policy-in-azure-resource-manager-template"></a>Definiera auktoriseringsprincipen i Azure Resource Manager mall

Om du vill aktivera Azure AD OAuth i ARM-mallen för distribution av din Logi Kap par, `properties` lägger du till ett objekt, om det finns något, som innehåller ett objekt i avsnittet för din Logi Kap [Apps resurs definition](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition) `accessControl` `triggers` . I `triggers` objektet lägger du till ett `openAuthenticationPolicies` objekt där du definierar en eller flera Auktoriseringsprinciper genom att följa den här syntaxen:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "values": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

Mer information om `accessControl` avsnittet finns i [begränsa inkommande IP-intervall i Azure Resource Manager mall](#restrict-inbound-ip-template) och [referens för Microsoft. Logic-arbetsflöden](/azure/templates/microsoft.logic/2019-05-01/workflows).

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Begränsa inkommande IP-adresser

Tillsammans med signaturen för delad åtkomst (SAS) kanske du vill begränsa vilka klienter som kan anropa din Logic app. Om du till exempel hanterar din begärans slut punkt med hjälp av [Azure API Management](../api-management/api-management-key-concepts.md)kan du begränsa din Logi Kap par för att endast acceptera begär Anden från IP-adressen för den [API Management tjänst instans som du skapar](../api-management/get-started-create-service-instance.md).

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Begränsa inkommande IP-intervall i Azure Portal

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. På din Logic Apps-meny, under **Inställningar**, väljer du **arbets flödes inställningar**.

1. Under **åtkomst kontroll konfiguration**  >  **tillåtna inkommande IP-adresser**väljer du **vissa IP-intervall**.

1. Under **IP-intervall för utlösare**anger du de IP-adressintervall som utlösaren accepterar.

   Ett giltigt IP-intervall använder följande format: *x. x. x. x/x* eller *x. x. x* . x-x. x

Om du vill att din Logic app endast ska utlösa som en kapslad Logic-app väljer du **endast andra Logic Apps**i listan **tillåtna inkommande IP-adresser** . Med det här alternativet skrivs en tom matris till din Logic app-resurs. På så sätt kan endast anrop från Logic Appss tjänsten (överordnade Logic Apps) utlösa den kapslade Logic-appen.

> [!NOTE]
> Oavsett IP-adress kan du fortfarande köra en Logic-app som har en begäran-baserad utlösare med hjälp av [Logic Apps REST API: arbets flödes utlösare-kör](/rest/api/logic/workflowtriggers/run) begäran eller med hjälp av API Management. Det här scenariot kräver dock fortfarande [autentisering](../active-directory/develop/authentication-vs-authorization.md) mot Azure-REST API. Alla händelser visas i gransknings loggen i Azure. Se till att du anger principer för åtkomst kontroll i enlighet med detta.

<a name="restrict-inbound-ip-template"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Begränsa inkommande IP-intervall i Azure Resource Manager mall

Om du [automatiserar distributionen för logi Kap par med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du ange IP-intervallen i *x. x. x/x/x* eller x. x. x *-x. x* . x. x-format med hjälp av `accessControl` avsnittet och genom att inkludera `triggers` avsnitten och i din Logi kap Apps `actions` resurs definition, till exempel:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-open-authentication-or-other-security"></a>Lägg till Azure Active Directory öppna autentisering eller annan säkerhet

Överväg att använda [Azure API Management](../api-management/api-management-key-concepts.md) -tjänsten om du vill lägga till fler [autentiseringsprotokoll](../active-directory/develop/authentication-vs-authorization.md) i din Logic app. Den här tjänsten hjälper dig att exponera din Logic app som ett API och innehåller omfattande övervakning, säkerhet, principer och dokumentation för alla slut punkter. API Management kan exponera en offentlig eller privat slut punkt för din Logic app. Om du vill bevilja åtkomst till den här slut punkten kan du använda [Azure Active Directory öppna autentisering](#azure-active-directory-oauth-authentication) (Azure AD OAuth), [klient certifikat](#client-certificate-authentication)eller andra säkerhets standarder för att auktorisera åtkomst till den slut punkten. När API Management tar emot en begäran skickar tjänsten begäran till din Logic app, vilket även gör nödvändiga transformeringar eller begränsningar på vägen. Om du bara vill låta API Management utlösa din Logic-app kan du använda din Logic Apps inställningar för inkommande IP-intervall.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Åtkomst till Logic app-åtgärder

Du kan endast tillåta vissa användare eller grupper att köra vissa uppgifter, till exempel hantera, redigera och Visa Logic Apps. Om du vill kontrol lera deras behörigheter använder du [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) så att du kan tilldela de anpassade eller inbyggda rollerna till medlemmarna i din Azure-prenumeration:

* [Logic app-deltagare](../role-based-access-control/built-in-roles.md#logic-app-contributor): låter dig hantera Logi Kap par, men du kan inte ändra åtkomsten till dem.

* [Logic app-operator](../role-based-access-control/built-in-roles.md#logic-app-operator): låter dig läsa, aktivera och inaktivera Logic Apps, men du kan inte redigera eller uppdatera dem.

Om du vill hindra andra från att ändra eller ta bort din Logic app kan du använda [Azure Resource lock](../azure-resource-manager/management/lock-resources.md). Den här funktionen förhindrar andra från att ändra eller ta bort produktions resurser.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Åtkomst till körning av historik data

När en Logic App körs [krypteras alla data under överföringen](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) med hjälp av Transport Layer Security (TLS) och [rest](../security/fundamentals/encryption-atrest.md). När din Logic-app är klar kan du Visa historiken för den körningen, inklusive de steg som kördes tillsammans med status, varaktighet, indata och utdata för varje åtgärd. Den här utförliga informationen ger inblick i hur din Logi Kap par kördes och var du kan börja felsöka eventuella problem som uppstår.

När du visar den logiska appens körnings historik autentiserar Logic Apps åtkomsten och tillhandahåller länkar till indata och utdata för begär Anden och svar för varje körning. För åtgärder som hanterar lösen ord, hemligheter, nycklar eller annan känslig information vill du dock hindra andra från att visa och komma åt dessa data. Om din Logic app till exempel får en hemlighet från [Azure Key Vault](../key-vault/general/overview.md) att använda vid autentisering av en HTTP-åtgärd, vill du dölja hemligheten från vyn.

Om du vill kontrol lera åtkomsten till indata och utdata i din Logic Apps körnings historik har du följande alternativ:

* [Begränsa åtkomst efter IP-adressintervall](#restrict-ip).

  Det här alternativet hjälper dig att skydda åtkomsten till körnings historik baserat på begär Anden från ett speciellt IP-adressintervall.

* [Skydda data i körnings historiken med hjälp av döljande](#obfuscate).

  I många utlösare och åtgärder kan du skydda indata, utdata eller båda i en Logic Apps körnings historik.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Begränsa åtkomst efter IP-adressintervall

Du kan begränsa åtkomsten till indata och utdata i din Logic Apps körnings historik så att endast förfrågningar från vissa IP-adressintervall kan visa dessa data. Om du till exempel vill blockera alla från att komma åt indata och utdata anger du ett IP-adressintervall som `0.0.0.0-0.0.0.0` . Endast en person med administratörs behörighet kan ta bort den här begränsningen, vilket ger möjlighet till just-in-Time-åtkomst till din Logic Apps-data. Du kan ange vilka IP-intervall som ska begränsas genom att använda Azure Portal eller i en Azure Resource Manager mall som du använder för Logic app-distribution.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Begränsa IP-intervall i Azure Portal

1. I Azure Portal öppnar du din Logic app i Logic Apps designer.

1. På din Logic Apps-meny, under **Inställningar**, väljer du **arbets flödes inställningar**.

1. Under **åtkomst kontroll konfiguration**  >  **tillåtna inkommande IP-adresser**väljer du **vissa IP-intervall**.

1. Under **IP-intervall för innehåll**anger du de IP-adressintervall som har åtkomst till innehåll från indata och utdata.

   Ett giltigt IP-intervall använder följande format: *x. x. x. x/x* eller *x. x. x* . x-x. x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Begränsa IP-intervall i Azure Resource Manager mall

Om du [automatiserar distributionen för logi Kap par med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du ange IP-intervallen med hjälp av `accessControl` avsnittet med `contents` avsnittet i din Logic Apps resurs definition, till exempel:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Skydda data i körnings historiken med hjälp av döljande

Många utlösare och åtgärder har inställningar för att skydda indata, utdata eller både och från en Logic Apps körnings historik. Innan du använder de här inställningarna för att skydda dessa data bör du [gå igenom dessa överväganden](#obfuscation-considerations).

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Säkra indata och utdata i designern

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

   ![Öppna Logic-appen i Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. På utlösaren eller åtgärden där du vill skydda känsliga data väljer du knappen med tre punkter (**...**) och väljer sedan **Inställningar**.

   ![Öppna utlösare eller åtgärds inställningar](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Aktivera antingen **säkra indata**, **säkra utdata**eller både och. När du är klar väljer du **Klar**.

   ![Aktivera "säkra indata" eller "säkra utdata"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Åtgärden eller utlösaren visar nu en Lås ikon i namn listen.

   ![Åtgärds-eller Utlösarens namn List visar lås ikonen](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokens som representerar skyddade utdata från tidigare åtgärder visar också lås ikoner. Om du till exempel väljer utdata från listan med dynamiskt innehåll som ska användas i en åtgärd, visar denna token en Lås ikon.

   ![Välj token för skyddade utdata](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. När Logic-appen har körts kan du se historiken för den här körningen.

   1. I fönstret **Översikt** för Logic Apps väljer du den körning som du vill visa.

   1. I fönstret **körning av Logic-app** expanderar du de åtgärder som du vill granska.

      Om du väljer att dölja både indata och utdata visas dessa värden nu som dolda.

      ![Dolda indata och utdata i körnings historik](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Säkra indata och utdata i kodvyn

I den underliggande utlösaren eller åtgärds definitionen lägger du till eller uppdaterar `runtimeConfiguration.secureData.properties` matrisen med något av eller båda dessa värden:

* `"inputs"`: Skyddar indata i körnings historik.
* `"outputs"`: Skyddar utdata i körnings historiken.

Här är några [saker som du bör tänka på](#obfuscation-considerations) när du använder dessa inställningar för att skydda dessa data.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-securing-inputs-and-outputs"></a>Att tänka på när du skyddar indata och utdata

* När du skymmer indata eller utdata på en utlösare eller åtgärd skickar Logic Apps inga skyddade data till Azure Log Analytics. Du kan inte heller lägga till [spårade egenskaper](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) till den utlösaren eller åtgärden för övervakning.

* [Logic Apps-API för hantering av arbets flödes historik](/rest/api/logic/) returnerar inte säkra utdata.

* Om du vill skydda utdata från en åtgärd som skymmer indata eller tydligt skymmer utdata, aktiverar du **säkert utdata** manuellt i den åtgärden.

* Kontrol lera att du aktiverar **säkra indata** eller **säkra utdata** i efterföljande åtgärder där du förväntar dig att körnings historiken ska dölja dessa data.

  **Inställning av säkra utdata**

  När du aktiverar **säkra utdata** manuellt i en utlösare eller åtgärd kan Logic Apps dölja dessa utdata i körnings historiken. Om en underordnad åtgärd uttryckligen använder dessa skyddade utdata som indata, Logic Apps döljer den här åtgärdens indata i körnings historiken, men *aktiverar inte* åtgärdens inställning för **säker indata** .

  ![Skyddade utdata som indata och effekt påverkan på de flesta åtgärder](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Åtgärderna Skriv, parsa JSON och Response har bara inställningen **säkra indata** . När inställningen är aktive rad döljs även dessa åtgärders utdata. Om dessa åtgärder uttryckligen använder överordnade säkra utdata som indata, Logic Apps döljer dessa åtgärders indata och utdata, men *aktiverar inte* dessa åtgärders inställning för **säkra indata** . Om en underordnad åtgärd uttryckligen använder dolda utdata från Skriv-, parsa-JSON-eller Response-åtgärder som indata, kan Logic Apps *inte dölja den här underordnade åtgärdens indata eller utdata*.

  ![Skyddade utdata som indata med effekt på vissa åtgärder](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Inställningar för säker indata**

  När du aktiverar **säkra indata** manuellt i en utlösare eller åtgärd, Logic Apps döljer dessa indata i körnings historiken. Om en underordnad åtgärd uttryckligen använder synliga utdata från den utlösaren eller åtgärden som indata, kan Logic Apps dölja denna underordnade åtgärds indata i körnings historiken, men *aktiverar inte* **säkra** inmatningar i den här åtgärden och döljer inte den här åtgärden.

  ![Skyddade indata och effekt påverkan på de flesta åtgärder](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Om Skriv, parsa JSON och svars åtgärder uttryckligen använder synliga utdata från utlösaren eller åtgärden som har skyddade indata, Logic Apps döljer dessa åtgärders indata och utdata, men *aktiverar inte* den här åtgärdens inställning för **säkra indata** . Om en underordnad åtgärd uttryckligen använder dolda utdata från Skriv-, parsa-JSON-eller Response-åtgärder som indata, kan Logic Apps *inte dölja den här underordnade åtgärdens indata eller utdata*.

  ![Skyddade indata och effekter på vissa åtgärder](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Åtkomst till parameter indata

Om du distribuerar över olika miljöer bör du överväga att parametriserade värdena i arbets flödes definitionen som varierar beroende på dessa miljöer. På så sätt kan du undvika hårdkodade data med hjälp av en [Azure Resource Manager mall](../azure-resource-manager/templates/overview.md) för att distribuera din Logi Kap par, skydda känsliga data genom att definiera säkra parametrar och skicka dessa data som separata indata via [mallens parametrar](../azure-resource-manager/templates/template-parameters.md) genom att använda en [parameter fil](../azure-resource-manager/templates/parameter-files.md).

Om du till exempel autentiserar HTTP-åtgärder med [Azure Active Directory öppen autentisering](#azure-active-directory-oauth-authentication) (Azure AD OAuth) kan du definiera och dölja de parametrar som godkänner klient-ID och klient hemlighet som används för autentisering. Om du vill definiera dessa parametrar i din Logic app, använder du `parameters` avsnittet i din Logic Apps arbets flödes definition och Resource Manager-mall för distribution. För att skydda parameter värden som du inte vill ska visas när du redigerar din Logic app eller visar körnings historik definierar du parametrarna med hjälp av `securestring` `secureobject` typen eller och använder kodning vid behov. Parametrar som har den här typen returneras inte med resurs definitionen och är inte tillgängliga när du visar resursen efter distributionen. Om du vill komma åt dessa parameter värden under körningen använder du `@parameters('<parameter-name>')` uttrycket i arbets flödes definitionen. Det här uttrycket utvärderas bara vid körning och beskrivs av språket för [arbets flödes definitionen](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Om du använder en parameter i ett rubrik eller brödtext för begäran kan den parametern visas när du visar din Logic Apps körnings historik och utgående HTTP-begäran. Se till att du även anger dina principer för innehålls åtkomst.
> Du kan också använda [döljande](#obfuscate) för att dölja indata och utdata i körnings historiken. Authorization-rubriker visas aldrig i indata eller utdata. Så om en hemlighet används där, så kan det inte går att hämta hemligheten.

Mer information finns i följande avsnitt i det här avsnittet:

* [Säkra parametrar i arbets flödes definitioner](#secure-parameters-workflow)
* [Skydda data i körnings historiken med hjälp av döljande](#obfuscate)

Om du [automatiserar distributionen för logi Kap par med hjälp av Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kan du definiera säkra [mallparametrar](../azure-resource-manager/templates/template-parameters.md), som utvärderas vid distribution, med hjälp av- `securestring` och- `secureobject` typerna. Om du vill definiera mallparametrar använder du mallens översta nivå `parameters` -avsnitt, som är åtskilda och skiljer sig från arbets flödets definitions `parameters` avsnitt. Använd en separat [parameter fil](../azure-resource-manager/templates/parameter-files.md)för att ange värden för mallparametrar.

Om du till exempel använder hemligheter kan du definiera och använda säkra mallparametrar som hämtar dessa hemligheter från [Azure Key Vault](../key-vault/general/overview.md) vid distributionen. Sedan kan du referera till nyckel valvet och hemligheten i parameter filen. Mer information finns i de här ämnena:

* [Skicka känsliga värden vid distribution med hjälp av Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Säkra parametrar i Azure Resource Manager mallar](#secure-parameters-deployment-template) senare i det här avsnittet

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Säkra parametrar i arbets flödes definitioner

Om du vill skydda känslig information i din Logic Apps arbets flödes definition använder du skyddade parametrar så att informationen inte visas när du har sparat din Logic app. Anta till exempel att du har en HTTP-åtgärd som kräver grundläggande autentisering, som använder ett användar namn och lösen ord. I arbets flödes definitionen `parameters` definierar avsnittet `basicAuthPasswordParam` `basicAuthUsernameParam` parametrarna och genom att använda `securestring` typen. Åtgärds definitionen refererar sedan till dessa parametrar i `authentication` avsnittet.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Säkra parametrar i Azure Resource Manager mallar

En [Resource Manager-mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) för en Logic app har flera `parameters` avsnitt. Om du vill skydda lösen ord, nycklar, hemligheter och annan känslig information definierar du skyddade parametrar på mallnivå-och arbets flödes definitions nivå med hjälp av- `securestring` eller- `secureobject` typen. Du kan sedan lagra dessa värden i [Azure Key Vault](../key-vault/general/overview.md) och använda [parameter filen](../azure-resource-manager/templates/parameter-files.md) för att referera till nyckel valvet och hemligheten. Din mall hämtar sedan den informationen vid distributionen. Mer information finns i [Skicka känsliga värden vid distribution med hjälp av Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Här är mer information om de här `parameters` avsnitten:

* På mallens översta nivå `parameters` definierar ett avsnitt parametrarna för de värden som mallen använder vid *distributionen*. Dessa värden kan till exempel innehålla anslutnings strängar för en enskild distributions miljö. Du kan sedan lagra dessa värden i en separat [parameter fil](../azure-resource-manager/templates/parameter-files.md), vilket gör det enklare att ändra dessa värden.

* I din Logic Apps resurs definition, men utanför arbets flödes definitionen, `parameters` anger ett avsnitt värdena för arbets flödets definitions parametrar. I det här avsnittet kan du tilldela dessa värden med hjälp av mall-uttryck som refererar till mallens parametrar. Dessa uttryck utvärderas vid distribution.

* I arbets flödes definitionen `parameters` definierar ett avsnitt de parametrar som din Logic app använder vid körning. Du kan sedan referera till dessa parametrar i din Logic Apps-arbetsflöde genom att använda definitions uttryck för arbets flöde, som utvärderas vid körning.

Den här exempel mal len har flera skyddade parameter definitioner som använder `securestring` typen:

| Parameternamn | Description |
|----------------|-------------|
| `TemplatePasswordParam` | En mallparameter som accepterar ett lösen ord som sedan skickas till arbets flödets definitions `basicAuthPasswordParam` parameter |
| `TemplateUsernameParam` | En mallparameter som accepterar ett användar namn som sedan skickas till arbets flödets definitions `basicAuthUserNameParam` parameter |
| `basicAuthPasswordParam` | En parameter för arbets flödes definition som godkänner lösen ordet för grundläggande autentisering i en HTTP-åtgärd |
| `basicAuthUserNameParam` | En parameter för arbets flödes definition som godkänner användar namnet för grundläggande autentisering i en HTTP-åtgärd |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Åtkomst till tjänster och system som anropas från Logic Apps

Här följer några exempel på hur du kan skydda slut punkter som tar emot samtal eller förfrågningar från din Logic app:

* Lägg till autentisering i utgående begär Anden.

  När du arbetar med en HTTP-baserad utlösare eller åtgärd som gör utgående samtal, till exempel HTTP, HTTP + Swagger eller webhook, kan du lägga till autentisering till den begäran som skickas av din Logic app. Du kan till exempel välja följande typer av autentisering:

  * [Grundläggande autentisering](#basic-authentication)

  * [Autentisering av klient certifikat](#client-certificate-authentication)

  * [Active Directory OAuth-autentisering](#azure-active-directory-oauth-authentication)

  * [Autentisering av hanterad identitet](#managed-identity-authentication)

  Mer information finns i [lägga till autentisering till utgående samtal](#add-authentication-outbound) senare i det här avsnittet.

* Begränsa åtkomsten från Logic app-IP-adresser.

  Alla anrop till slut punkter från Logic Apps härstammar från angivna IP-adresser som baseras på dina Logic Apps-regioner. Du kan lägga till filtrering som bara accepterar begär Anden från dessa IP-adresser. För att hämta de här IP-adresserna, se [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Förbättra säkerheten för anslutningar till lokala system.

  Azure Logic Apps ger integrering med dessa tjänster för att hjälpa till att tillhandahålla säkrare och tillförlitlig kommunikation på plats.

  * Lokal datagateway

    Många hanterade anslutningar i Azure Logic Apps fören klar säkra anslutningar till lokala system, till exempel fil system, SQL, SharePoint och DB2. Gatewayen skickar data från lokala källor på krypterade kanaler via Azure Service Bus. All trafik kommer som säker utgående trafik från Gateway-agenten. Lär dig [hur den lokala datagatewayen fungerar](logic-apps-gateway-install.md#gateway-cloud-service).

  * Anslut via Azure API Management

    [Azure API Management](../api-management/api-management-key-concepts.md) tillhandahåller lokala anslutnings alternativ, till exempel plats-till-plats-virtuellt privat nätverk och [ExpressRoute](../expressroute/expressroute-introduction.md) -integrering för skyddad proxy och kommunikation till lokala system. Om du har ett API som ger åtkomst till ditt lokala system och du exponerar detta API genom att skapa en [API Management tjänst instans](../api-management/get-started-create-service-instance.md)kan du anropa detta API i din Logic app-arbetsflöde genom att välja den inbyggda API Management utlösaren eller åtgärden i Logic App Designer.

    > [!NOTE]
    > Anslutningen visar bara de API Management tjänster där du har behörighet att visa och ansluta, men visar inte förbruknings API Management tjänster.

    1. Ange i rutan Sök i Logic App Designer `api management` . Välj steget baserat på om du lägger till en utlösare eller en åtgärd:<p>

       * Om du lägger till en utlösare, som alltid är det första steget i arbets flödet, väljer du **Välj en Azure API Management-utlösare**.

       * Om du lägger till en åtgärd väljer du **Välj en Azure API Management-åtgärd**.

       Det här exemplet lägger till en utlösare:

       ![Lägg till Azure API Management-utlösare](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Välj din tidigare skapade API Management tjänst instans.

       ![Välj API Management tjänst instans](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Välj det API-anrop som ska användas.

       ![Välj befintligt API](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Lägg till autentisering i utgående samtal

HTTP-och HTTPS-slutpunkter stöder olika typer av autentisering. På vissa utlösare och åtgärder som du använder för att skicka utgående anrop eller begär anden till dessa slut punkter, kan du ange en autentiseringstyp. I Logic Apps designer har utlösare och åtgärder som stöder val av autentiseringstyp en egenskap för **autentisering** . Den här egenskapen kanske dock inte alltid visas som standard. I dessa fall öppnar du listan **Lägg till ny parameter** i utlösaren eller åtgärden och väljer **autentisering**.

> [!IMPORTANT]
> För att skydda känslig information som din Logic app hanterar, Använd skyddade parametrar och koda data vid behov.
> Mer information om hur du använder och skyddar parametrar finns i [åtkomst till parameter indata](#secure-action-parameters).

Den här tabellen identifierar de autentiseringstyper som är tillgängliga i utlösare och åtgärder där du kan välja en Autentiseringstyp:

| Autentiseringstyp | Utlösare och åtgärder som stöds |
|---------------------|--------------------------------|
| [Basic](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP webhook |
| [Klient certifikat](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
| [Rådata](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
| [Hanterade identiteter](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP-webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Grundläggande autentisering

Om alternativet [grundläggande](../active-directory-b2c/secure-rest-api.md) är tillgängligt anger du följande egenskaps värden:

| Egenskap (designer) | Egenskap (JSON) | Obligatorisk | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Yes | Basic | Autentiseringstypen som ska användas |
| **Användarnamn** | `username` | Yes | <*användar namn*>| Användar namnet för att autentisera åtkomsten till mål tjänstens slut punkt |
| **Lösenord** | `password` | Yes | <*ords*> | Lösen ordet för att autentisera åtkomsten till mål tjänstens slut punkt |
||||||

När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Detta exempel på en HTTP-åtgärds definition anger autentiseringen `type` som `Basic` och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Autentisering av klient certifikat

Om alternativet [klient certifikat](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) är tillgängligt anger du följande egenskaps värden:

| Egenskap (designer) | Egenskap (JSON) | Obligatorisk | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Yes | **Klient certifikat** <br>eller <br>`ClientCertificate` | Autentiseringstypen som ska användas. Du kan hantera certifikat med [Azure API Management](../api-management/api-management-howto-mutual-certificates.md). <p></p>**Obs**: anpassade anslutningar stöder inte certifikatbaserad autentisering för både inkommande och utgående samtal. |
| **-** | `pfx` | Yes | <*kodad-PFX-fil-innehåll*> | Det Base64-kodade innehållet från en PFX-fil (personal information Exchange) <p><p>Om du vill konvertera PFX-filen till Base64-kodat format kan du använda PowerShell genom att följa dessa steg: <p>1. Spara certifikat innehållet i en variabel: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. konvertera certifikat innehållet med hjälp av `ToBase64String()` funktionen och spara innehållet i en textfil: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Lösenord** | `password`| No | <*Password-för-PFX-fil*> | Lösen ordet för att komma åt PFX-filen |
|||||

När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Detta exempel på en HTTP-åtgärds definition anger autentiseringen `type` som `ClientCertificate` och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Mer information om hur du skyddar tjänster med hjälp av autentisering av klient certifikat finns i följande avsnitt:

* [Förbättra säkerheten för API: er med hjälp av autentisering av klient certifikat i Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Förbättra säkerheten för backend-tjänster med hjälp av autentisering av klient certifikat i Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Förbättra säkerheten för din RESTfuL-tjänst genom att använda klient certifikat](../active-directory-b2c/secure-rest-api.md)
* [Autentiseringsuppgifter för program-autentisering](../active-directory/develop/active-directory-certificate-credentials.md)
* [Använd ett TLS/SSL-certifikat i koden i Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory öppna autentisering

I begär ande utlösare kan du använda [Azure Active Directory öppna autentisering](../active-directory/develop/index.yml) (Azure AD OAuth) för att autentisera inkommande samtal när du [har konfigurerat Azure AD-Auktoriseringsprinciper](#enable-oauth) för din Logic app. Ange följande egenskaps värden för alla andra utlösare och åtgärder som tillhandahåller **Active Directory OAuth** -autentiseringstyp som du kan välja:

| Egenskap (designer) | Egenskap (JSON) | Obligatorisk | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Yes | **Active Directory OAuth** <br>eller <br>`ActiveDirectoryOAuth` | Autentiseringstypen som ska användas. Logic Apps följer för närvarande [OAuth 2,0-protokollet](../active-directory/develop/v2-overview.md). |
| **Myndighet** | `authority` | No | <*URL-för-Authority-token-Issuer*> | URL för den myndighet som tillhandahåller autentiseringstoken. Som standard är det här värdet `https://login.windows.net` . |
| **Klientorganisation** | `tenant` | Yes | <*klient organisations-ID*> | Klient-ID för Azure AD-klienten |
| **Målgrupp** | `audience` | Yes | <*resurs-till-auktorisera*> | Den resurs som du vill använda för auktorisering, till exempel `https://management.core.windows.net/` |
| **Klient-ID** | `clientId` | Yes | <*klient-ID*> | Klient-ID för appen som begär auktorisering |
| **Autentiseringstyp** | `credentialType` | Yes | Certifikat <br>eller <br>Hemlighet | Autentiseringstypen som klienten använder för att begära auktorisering. Den här egenskapen och värdet visas inte i din Logic Apps underliggande definition, men avgör vilka egenskaper som visas för den valda autentiseringstypen. |
| **Icke** | `secret` | Ja, men endast för autentiseringstypen "hemlig" | <*klient hemlighet*> | Klient hemligheten för att begära auktorisering |
| **-** | `pfx` | Ja, men endast för Credential-typen "certifikat" | <*kodad-PFX-fil-innehåll*> | Det Base64-kodade innehållet från en PFX-fil (personal information Exchange) |
| **Lösenord** | `password` | Ja, men endast för Credential-typen "certifikat" | <*Password-för-PFX-fil*> | Lösen ordet för att komma åt PFX-filen |
|||||

När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Det här exemplet på en HTTP-åtgärd anger autentiseringen `type` som `ActiveDirectoryOAuth` , autentiseringstypen typ som `Secret` och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>RAW-autentisering

Om alternativet **RAW** är tillgängligt kan du använda den här autentiseringstypen när du måste använda [autentiseringsscheman](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) som inte följer [OAuth 2,0-protokollet](https://oauth.net/2/). Med den här typen skapar du ett Authorization-huvud-värde som du skickar med den utgående begäran, och anger detta huvud värde i utlösaren eller åtgärden.

Här är till exempel ett exempel på en rubrik för en HTTPS-begäran som följer [OAuth 1,0-protokollet](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

I utlösaren eller åtgärden som stöder RAW-autentisering anger du följande egenskaps värden:

| Egenskap (designer) | Egenskap (JSON) | Obligatorisk | Värde | Beskrivning |
|---------------------|-----------------|----------|-------|-------------|
| **Autentisering** | `type` | Yes | Rådata | Autentiseringstypen som ska användas |
| **Värde** | `value` | Yes | <*auktorisering – huvud värde*> | Det Authorization-huvud värde som ska användas för autentisering |
||||||

När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Det här exemplet på en HTTP-åtgärd anger autentiseringen `type` som `Raw` och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>Autentisering av hanterad identitet

Om alternativet för [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) är tillgängligt på en [viss utlösare eller åtgärd](#add-authentication-outbound)kan din Logi Kap par använda den systemtilldelade identiteten eller en *enskild* manuellt skapad användardefinierad identitet för att autentisera åtkomsten till andra resurser som skyddas av Azure Active Directory (Azure AD) utan att logga in. Azure hanterar den här identiteten för dig och hjälper dig att skydda dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Läs mer om [Azure-tjänster som har stöd för hanterade identiteter för Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Innan din Logic-app kan använda en hanterad identitet följer du stegen i [autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps](../logic-apps/create-managed-service-identity.md). De här stegen aktiverar den hanterade identiteten på din Logic app och konfigurerar identitetens åtkomst till Azure-resursen.

1. Innan en Azure-funktion kan använda en hanterad identitet måste du först [aktivera autentisering för Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. I utlösaren eller åtgärden där du vill använda den hanterade identiteten anger du följande egenskaps värden:

   | Egenskap (designer) | Egenskap (JSON) | Obligatorisk | Värde | Beskrivning |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autentisering** | `type` | Yes | **Hanterad identitet** <br>eller <br>`ManagedServiceIdentity` | Autentiseringstypen som ska användas |
   | **Hanterad identitet** | `identity` | Yes | * **Systemtilldelad hanterad identitet** <br>eller <br>`SystemAssigned` <p><p>* <*användare-tilldelad identitet-namn*> | Den hanterade identitet som ska användas |
   | **Målgrupp** | `audience` | Yes | <*mål resurs-ID*> | Resurs-ID för den mål resurs som du vill komma åt. <p>Till exempel `https://storage.azure.com/` blir [åtkomsttoken](../active-directory/develop/access-tokens.md) för autentisering giltig för alla lagrings konton. Du kan dock också ange en rot tjänst-URL, till exempel `https://fabrikamstorageaccount.blob.core.windows.net` för ett angivet lagrings konto. <p>**Obs!** egenskapen **Audience** kan vara dold i vissa utlösare eller åtgärder. Om du vill att den här egenskapen ska vara synlig i utlösaren eller åtgärden öppnar du listan **Lägg till ny parameter** och väljer **mål grupp**. <p><p>**Viktigt**: se till att det här mål resurs-ID: t *exakt matchar* det värde som Azure AD förväntar sig, inklusive eventuella avslutande snedstreck. `https://storage.azure.com/`Resurs-ID för alla Azure Blob Storage-konton kräver därför ett avslutande snedstreck. Resurs-ID för ett angivet lagrings konto kräver dock inte något avslutande snedstreck. Du hittar dessa resurs-ID: n i [Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   När du använder [skyddade parametrar](#secure-action-parameters) för att hantera och skydda känslig information, till exempel i en [Azure Resource Manager mall för automatisk distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), kan du använda uttryck för att få åtkomst till dessa parameter värden vid körning. Detta exempel på en HTTP-åtgärds definition anger autentiseringen `type` som `ManagedServiceIdentity` och använder [funktionen parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) för att hämta parameter värden:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Blockera skapande av anslutningar

Om din organisation inte tillåter att du ansluter till vissa resurser genom att använda deras anslutningar i Azure Logic Apps kan du [blockera möjligheten att skapa anslutningar](../logic-apps/block-connections-connectors.md) för vissa anslutningar i Logic app-arbetsflöden genom att använda [Azure policy](../governance/policy/overview.md). Mer information finns i [blockera anslutningar som skapats av vissa anslutningar i Azure Logic Apps](../logic-apps/block-connections-connectors.md).

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Isolerings vägledning för Logic Apps

Du kan använda Azure Logic Apps i [Azure Government](../azure-government/documentation-government-welcome.md) stöd för alla effekt nivåer i de regioner som beskrivs i den [Azure Government inverkan på nivå 5](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) och den [amerikanska avdelningen för försvar av säkerhets krav för moln hantering (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html). För att uppfylla dessa krav har Logic Apps stöd för möjligheten att skapa och köra arbets flöden i en miljö med dedikerade resurser så att du kan minska prestanda påverkan från andra Azure-klienter i Logi Kap par och undvika att dela dator resurser med andra klienter.

* Om du vill köra en egen kod eller utföra XML-omvandling [skapar du och anropar en Azure-funktion](../logic-apps/logic-apps-azure-functions.md)i stället för att använda den [infogade kod funktionen](../logic-apps/logic-apps-add-run-inline-code.md) eller tillhandahåller [sammansättningar som ska användas som Maps](../logic-apps/logic-apps-enterprise-integration-maps.md). Konfigurera också värd miljön för din Function-app så att den överensstämmer med dina isolerings krav.

  Om du till exempel vill uppfylla kraven på nivå 5, skapar du en Function-app med [App Service plan](../azure-functions/functions-scale.md#app-service-plan) med hjälp av den [ **isolerade** pris nivån](../app-service/overview-hosting-plans.md) tillsammans med en [App Service-miljön (ASE)](../app-service/environment/intro.md) som också använder den **isolerade** pris nivån. I den här miljön körs funktions appar på dedikerade virtuella Azure-datorer och dedikerade virtuella Azure-nätverk, vilket ger nätverks isolering ovanpå beräknings isolering för dina appar och maximal skalbar kapacitet. Mer information finns i avsnittet om [hur du Azure Government påverkan på nivå 5 – Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions).

  Mer information finns i de här ämnena:<p>

  * [Azure App Services planer](../app-service/overview-hosting-plans.md)
  * [Nätverksalternativ för Azure Functions](../azure-functions/functions-networking-options.md)
  * [Azure-dedikerade värdar för virtuella datorer](../virtual-machines/windows/dedicated-hosts.md)
  * [Isolering av virtuella datorer i Azure](../virtual-machines/isolation.md)
  * [Distribuera dedikerade Azure-tjänster i virtuella nätverk](../virtual-network/virtual-network-for-azure-services.md)

* Om du vill skapa Logi Kap par som körs på dedikerade resurser och har åtkomst till resurser som skyddas av ett virtuellt Azure-nätverk kan du skapa en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Vissa virtuella Azure-nätverk använder privata slut punkter ([Azures privata länk](../private-link/private-link-overview.md)) för att ge åtkomst till Azure PaaS-tjänster, till exempel Azure Storage, Azure Cosmos DB eller Azure SQL Database, partner tjänster eller kund tjänster som finns i Azure. Om dina Logi Kap par behöver åtkomst till virtuella nätverk som använder privata slut punkter, måste du skapa, distribuera och köra dessa Logic Apps i en ISE.

  * Om du vill ha mer kontroll över de krypterings nycklar som används av Azure Storage kan du konfigurera, använda och hantera din egen nyckel med hjälp av [Azure Key Vault](../key-vault/general/overview.md). Den här funktionen kallas även "Bring Your Own Key" (BYOK) och din nyckel kallas för "kundhanterad nyckel". Mer information finns i [Konfigurera Kundhanterade nycklar för att kryptera data i vila för integrerings tjänst miljöer (ISEs) i Azure Logic Apps](../logic-apps/customer-managed-keys-integration-service-environment.md).

Mer information finns i de här ämnena:

* [Isolering i det offentliga Azure-molnet](../security/fundamentals/isolation-choices.md)
* [Säkerhet för mycket känsliga IaaS-appar i Azure](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Nästa steg

* [Azures säkerhets bas linje för Azure Logic Apps](../logic-apps/security-baseline.md)
* [Automatisera distribution för Azure Logic Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Övervaka logikappar](../logic-apps/monitor-logic-apps-log-analytics.md)
