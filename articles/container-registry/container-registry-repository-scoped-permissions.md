---
title: Behörigheter till databaser i Azure Container Registry
description: Skapa en token med behörigheter som är begränsade till vissa databaser i ett Premium-register för att hämta eller skicka avbildningar eller utföra andra åtgärder
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: ceec69d746f77ea7a23bc70d029c8b3736e7f292
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988266"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Skapa en token med databasens begränsade behörigheter

Den här artikeln beskriver hur du skapar token och områdes kartor för att hantera åtkomst till vissa databaser i behållar registret. Genom att skapa token kan en register ägare tillhandahålla användare eller tjänster med begränsad, tidsbegränsad åtkomst till databaser för att hämta eller skicka avbildningar eller utföra andra åtgärder. En token ger mer detaljerade behörigheter än andra [verifierings alternativ](container-registry-authentication.md)för registret, vilka scope-behörigheter till hela registret. 

Scenarier för att skapa en token är:

* Tillåt IoT-enheter med enskilda tokens för att hämta en avbildning från en lagrings plats
* Ange en extern organisation med behörigheter till en speciell lagrings plats 
* Begränsa databas åtkomsten till olika användar grupper i din organisation. Du kan till exempel ge Skriv-och Läs behörighet till utvecklare som skapar avbildningar som är riktade till specifika lagrings platser och Läs åtkomst till team som distribuerar från dessa databaser.

Den här funktionen är tillgänglig i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md).

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Du kan för närvarande inte tilldela lagrings plats behörigheter till en Azure Active Directory identitet, till exempel ett huvud namn för tjänsten eller en hanterad identitet.
* Det går inte att skapa en omfångs karta i ett register som är aktiverat för [Anonym pull-åtkomst](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Begrepp

Om du vill konfigurera behörigheter för databasens omfång skapar du en *token* med en associerad *omfångs karta*. 

* En **token** tillsammans med ett genererat lösen ord gör att användaren kan autentiseras med registret. Du kan ange ett förfallo datum för ett token-lösenord eller inaktivera en token när som helst.  

  Efter autentisering med en token kan användaren eller tjänsten utföra en eller flera *åtgärder* som är begränsade till en eller flera databaser.

  |Åtgärd  |Beskrivning  | Exempel |
  |---------|---------|--------|
  |`content/delete`    | Ta bort data från lagrings platsen  | Ta bort en lagrings plats eller ett manifest |
  |`content/read`     |  Läs data från lagrings platsen |  Hämta en artefakt |
  |`content/write`     |  Skriva data till lagrings platsen     | Använd med `content/read` för att skicka en artefakt |
  |`metadata/read`    | Läs metadata från lagrings platsen   | Visa taggar eller manifest |
  |`metadata/write`     |  Skriv metadata till lagrings platsen  | Aktivera eller inaktivera Läs-, Skriv-eller borttagnings åtgärder |

* En **omfångs karta** grupperar de lagrings behörigheter som du tillämpar på en token och kan tillämpas på andra tokens. Varje token är associerad med en enda omfångs karta. 

   Med en omfångs karta:

    * Konfigurera flera tokens med identiska behörigheter till en uppsättning databaser
    * Uppdatera token-behörigheter när du lägger till eller tar bort databas åtgärder i omfångs kartan eller använder en annan omfångs karta 

  Azure Container Registry innehåller också flera systemdefinierade omfattnings mappningar som du kan använda när du skapar token. Behörigheterna för systemdefinierade omfattnings mappningar gäller för alla lagrings platser i registret.

Följande bild visar relationen mellan tokens och omfångs kartor. 

![Register-token och omfångs kartor](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Förutsättningar

* Kommando exempel **för Azure CLI – Azure** CLI-kommandon i den här artikeln kräver Azure CLI version 2.17.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).
* **Docker** -för att autentisera med registret för att hämta eller push-avbildningar behöver du en lokal Docker-installation. I Docker finns installationsanvisningar för [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Container Registry** – om du inte har ett kan du skapa ett Premium container Registry i din Azure-prenumeration eller uppgradera ett befintligt register. Använd till exempel [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Skapa token – CLI

### <a name="create-token-and-specify-repositories"></a>Skapa token och ange databaser

Skapa en token med kommandot [AZ ACR token Create][az-acr-token-create] . När du skapar en token kan du ange en eller flera databaser och associerade åtgärder för varje lagrings plats. Databaserna behöver inte finnas i registret ännu. Om du vill skapa en token genom att ange en befintlig omfångs karta, se [Nästa avsnitt](#create-token-and-specify-scope-map).

I följande exempel skapas en token i Registry- *registret* med följande behörigheter på `samples/hello-world` lagrings platsen: `content/write` och `content/read` . Som standard anger kommandot standardvärdet för token till `enabled` , men du kan uppdatera statusen till `disabled` när som helst.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Utdata visar information om token. Som standard genereras två lösen ord som inte upphör att gälla, men du kan också ange ett förfallo datum. Vi rekommenderar att du sparar lösen orden på en säker plats för att använda senare för autentisering. Det går inte att hämta lösen orden igen, men nya kan genereras.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> Information om hur du återskapar token-lösenord och upphör ande perioder finns i [Återskapa token lösen ord](#regenerate-token-passwords) senare i den här artikeln.

Utdata innehåller information om omfångs kartan som skapas av kommandot. Du kan använda omfångs kartan, `MyToken-scope-map` som kallas för att tillämpa samma databas åtgärder på andra tokens. Eller uppdatera omfångs kartan senare för att ändra behörigheterna för de associerade tokens.

### <a name="create-token-and-specify-scope-map"></a>Skapa token och ange omfångs karta

Ett alternativt sätt att skapa en token är att ange en befintlig omfångs karta. Om du inte redan har en omfångs karta skapar du först en genom att ange databaser och associerade åtgärder. Ange sedan omfångs kartan när du skapar en token. 

Om du vill skapa en omfångs karta använder du kommandot [AZ ACR scope-Map Create][az-acr-scope-map-create] . Följande kommando skapar en omfångs karta med samma behörigheter för den `samples/hello-world` databas som användes tidigare. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Kör [AZ ACR token Create][az-acr-token-create] för att skapa en token genom att ange mappningen för *MyScopeMap* -omfånget. Som i föregående exempel ställer kommandot in standard-token-status till `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Utdata visar information om token. Som standard skapas två lösen ord. Vi rekommenderar att du sparar lösen orden på en säker plats för att använda senare för autentisering. Det går inte att hämta lösen orden igen, men nya kan genereras.

> [!NOTE]
> Information om hur du återskapar token-lösenord och upphör ande perioder finns i [Återskapa token lösen ord](#regenerate-token-passwords) senare i den här artikeln.

## <a name="create-token---portal"></a>Skapa token – Portal

Du kan använda Azure Portal för att skapa token och omfångs kartor. Precis som med `az acr token create` CLI-kommandot kan du använda en befintlig omfångs karta eller skapa en omfångs karta när du skapar en token genom att ange en eller flera databaser och associerade åtgärder. Databaserna behöver inte finnas i registret ännu. 

I följande exempel skapas en token och en omfångs karta skapas med följande behörigheter på `samples/hello-world` lagrings platsen: `content/write` och `content/read` .

1. I portalen navigerar du till behållar registret.
1. Under **lagrings behörigheter** väljer du **tokens (förhands granskning) > + Lägg till**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Skapa token i portalen":::
1. Ange ett token-namn.
1. Under **omfångs karta** väljer du **Skapa ny**.
1. Konfigurera omfångs kartan:
    1. Ange ett namn och en beskrivning för omfångs kartan. 
    1. Under **databaser**, ange och `samples/hello-world` under **behörigheter** väljer du  `content/read` och `content/write` . Välj sedan **+ Lägg till**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Skapa omfångs karta i portalen":::

    1. När du har lagt till databaser och behörigheter väljer du **Lägg** till för att lägga till omfångs kartan.
1. Godkänn standardvärdet **för token** **aktive rad** och välj sedan **skapa**.

När token har verifierats och skapats visas information om token på skärmen **tokens** .

### <a name="add-token-password"></a>Lägg till token-lösenord

Om du vill använda en token som skapats i portalen måste du generera ett lösen ord. Du kan generera ett eller två lösen ord och ange ett förfallo datum för var och en. 

1. I portalen navigerar du till behållar registret.
1. Under **lagrings behörigheter** väljer du **tokens (för hands version)** och väljer en token.
1. I information om token, väljer du **password1** eller **password2** och väljer ikonen generera.
1. I lösen ords skärmen anger du ett förfallo datum för lösen ordet och väljer **generera**. Vi rekommenderar att du anger ett förfallo datum.
1. När du har genererat ett lösen ord kopierar du och sparar det på en säker plats. Du kan inte hämta ett genererat lösen ord när du har stängt skärmen, men du kan skapa ett nytt lösen ord.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Skapa lösen ord för token i portalen":::

## <a name="authenticate-with-token"></a>Autentisera med token

När en användare eller tjänst använder en token för att autentisera med mål registret, innehåller det namnet på token som ett användar namn och ett av dess genererade lösen ord. 

Autentiseringsmetoden beror på den konfigurerade åtgärden eller de åtgärder som är associerade med token.

|Action  |Så här autentiserar du  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` i Azure CLI<br/><br/>Exempel: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` i Azure CLI<br/><br/>Exempel: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` i Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` i Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` i Azure CLI |

## <a name="examples-use-token"></a>Exempel: Använd token

I följande exempel används den token som skapades tidigare i den här artikeln för att utföra vanliga åtgärder på en lagrings plats: push-och pull-bilder, ta bort bilder och lista med lagrings plats taggar. Token konfigurerades ursprungligen med push-behörighet ( `content/write` och `content/read` åtgärder) på `samples/hello-world` lagrings platsen.

### <a name="pull-and-tag-test-images"></a>Hämta och tagga test bilder

I följande exempel kan du hämta offentliga `hello-world` och `nginx` bilder från Microsoft container Registry och tagga dem för ditt register och din lagrings plats.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Autentisera med token

Kör `docker login` eller `az acr login` för att autentisera med registret för att skicka eller ta emot avbildningar. Ange namnet på token som användar namn och ange ett lösen ord. Token måste ha `Enabled` status.

Följande exempel är formaterat för bash-gränssnittet och innehåller värdena med miljövariabler.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Utdata ska Visa lyckad autentisering:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Push-överför avbildningar till registret

Efter lyckad inloggning försöker du skicka de taggade bilderna till registret. Eftersom token har behörighet att skicka avbildningar till `samples/hello-world` lagrings platsen, lyckas följande push-meddelanden:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Token har inte behörighet till `samples/nginx` lagrings platsen, så följande push-försök Miss lyckas med ett fel som liknar `requested access to the resource is denied` :

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Uppdatera token-behörigheter

Uppdatera behörigheterna för en token genom att uppdatera behörigheterna i den associerade omfångs kartan. Den uppdaterade omfångs kartan tillämpas omedelbart på alla associerade tokens. 

Du kan till exempel uppdatera `MyToken-scope-map` med `content/write` och `content/read` åtgärder på `samples/ngnx` lagrings platsen och ta bort `content/write` åtgärden på `samples/hello-world` lagrings platsen.  

Om du vill använda Azure CLI kör du [AZ ACR scope-Map Update][az-acr-scope-map-update] för att uppdatera omfångs kartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

I Azure-portalen:

1. Navigera till behållar registret.
1. Under **lagrings behörigheter** väljer du **omfångs kartor (för hands version)** och väljer omfångs mappningen som ska uppdateras.
1. Under **databaser**, ange och `samples/nginx` under **behörigheter** väljer du `content/read` och `content/write` . Välj sedan **+ Lägg till**.
1. Under **databaser** väljer du `samples/hello-world` och under **behörigheter** avmarkerar du `content/write` . Välj sedan **Spara**.

När omfångs kartan har uppdaterats lyckas följande push:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Eftersom omfångs kartan bara har `content/read` behörighet till `samples/hello-world` lagrings platsen, Miss lyckas ett push-försök till `samples/hello-world` lagrings platsen:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Hämtning av bilder från båda databaser lyckas, eftersom omfångs kartan ger `content/read` behörigheter för båda databaserna:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Ta bort avbildningar

Uppdatera omfångs kartan genom att lägga till `content/delete` åtgärden i `nginx` lagrings platsen. Med den här åtgärden kan du ta bort avbildningar i lagrings platsen eller ta bort hela lagrings platsen.

För det kortfattat visar vi bara kommandot [AZ ACR scope-Map Update][az-acr-scope-map-update] för att uppdatera omfångs kartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

Information om hur du uppdaterar omfångs kartan med portalen finns i [föregående avsnitt](#update-token-permissions).

Använd följande [AZ ACR-databas ta][az-acr-repository-delete] bort-kommandot för att ta bort `samples/nginx` lagrings platsen. Om du vill ta bort avbildningar eller databaser skickar du tokens namn och lösen ord till kommandot. I följande exempel används miljövariablerna som skapades tidigare i artikeln:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Visa lagrings platsen-Taggar 

Uppdatera omfångs kartan genom att lägga till `metadata/read` åtgärden i `hello-world` lagrings platsen. Med den här åtgärden kan du läsa manifest-och tag-data i lagrings platsen.

För det kortfattat visar vi bara kommandot [AZ ACR scope-Map Update][az-acr-scope-map-update] för att uppdatera omfångs kartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

Information om hur du uppdaterar omfångs kartan med portalen finns i [föregående avsnitt](#update-token-permissions).

Om du vill läsa metadata i `samples/hello-world` databasen kör du kommandot [AZ ACR-lagringsplats show-manifestes][az-acr-repository-show-manifests] eller [AZ ACR-lagringsplatsen show-Tags][az-acr-repository-show-tags] . 

Om du vill läsa metadata skickar du tokens namn och lösen ord till något av kommandona. I följande exempel används miljövariablerna som skapades tidigare i artikeln:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Exempel på utdata:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>Hantera token och områdes kartor

### <a name="list-scope-maps"></a>Lista omfångs kartor

Använd kommandot [AZ ACR scope-Map List][az-acr-scope-map-list] eller sidan **omfångs kartor (för hands version)** i portalen för att visa en lista över alla omfångs mappningar som kon figurer ATS i ett register. Exempel:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Utdata består av de tre systemdefinierade omfångs Maps och andra definitions kartor som genereras av dig. Tokens kan konfigureras med någon av dessa omfångs kartor.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Visa information om token

Om du vill visa information om en token, till exempel dess status och lösen ordets förfallo datum, kör du kommandot [AZ ACR token show][az-acr-token-show] eller väljer token på skärmen för **token (förhands granskning)** i portalen. Exempel:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Använd kommandot [AZ ACR token List][az-acr-token-list] eller på skärmen för **token (förhands granskning)** i portalen för att visa alla token som kon figurer ATS i ett register. Exempel:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Återskapa lösen ord för token

Om du inte har skapat ett token-lösenord eller om du vill generera nya lösen ord, kör du kommandot [AZ ACR token Credential generate][az-acr-token-credential-generate] . 

I följande exempel genereras ett nytt värde för password1 *för token-token* , med en förfallo period på 30 dagar. Lösen ordet lagras i miljö variabeln `TOKEN_PWD` . Det här exemplet är formaterat för bash-gränssnittet.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Om du vill använda Azure Portal för att generera ett token-lösenord, se stegen i [create token-Portal](#create-token---portal) tidigare i den här artikeln.

### <a name="update-token-with-new-scope-map"></a>Uppdatera token med ny omfångs karta

Om du vill uppdatera en token med en annan omfångs karta kör du [AZ ACR token Update][az-acr-token-update] och anger den nya omfångs kartan. Exempel:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

I portalen väljer du token **(förhands granskning) på sidan token (förhands visning)** och väljer en annan omfångs karta under **omfång**.

> [!TIP]
> När du har uppdaterat en token med en ny omfångs karta kanske du vill generera nya token-lösenord. Använd kommandot [AZ ACR token Credential generate][az-acr-token-credential-generate] eller återskapa ett token-lösenord i Azure Portal.

## <a name="disable-or-delete-token"></a>Inaktivera eller ta bort token

Du kan behöva tillfälligt inaktivera användningen av autentiseringsuppgifter för en användare eller tjänst. 

Med hjälp av Azure CLI kör du kommandot [AZ ACR token Update][az-acr-token-update] för att ställa in på `status` `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Välj token på skärmen för **token (förhands visning)** i portalen och välj **inaktive rad** under **status**.

Om du vill ta bort en token för att göra det permanent att verifiera åtkomst av alla som använder sina autentiseringsuppgifter, kör du kommandot [AZ ACR token Delete][az-acr-token-delete] . 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

I portalen väljer du token på skärmen för **token (förhands granskning)** och väljer **Ignorera**.

## <a name="next-steps"></a>Nästa steg

* Om du vill hantera omfångs kartor och tokens använder du ytterligare kommandon i kommando grupperna [AZ ACR scope-Map][az-acr-scope-map] och [AZ ACR-token][az-acr-token] .
* Se [översikten över autentiseringen](container-registry-authentication.md) för andra alternativ för att autentisera med ett Azure Container Registry, inklusive att använda en Azure Active Directory identitet, ett huvud namn för tjänsten eller ett administratörs konto.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
