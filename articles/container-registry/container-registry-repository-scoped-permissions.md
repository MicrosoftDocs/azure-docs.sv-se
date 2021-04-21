---
title: Behörigheter till lagringsplatsen i Azure Container Registry
description: Skapa en token med behörigheter som är begränsade till specifika lagringsplatsen i ett Premium-register för att hämta eller push-skicka avbildningar eller utföra andra åtgärder
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: 0ac479b696a377509cee6459efd8bbb9de940d2a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781405"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Skapa en token med behörigheter som omfattar lagringsplatsen

Den här artikeln beskriver hur du skapar token och omfångskartor för att hantera åtkomst till specifika lagringsplatsen i containerregistret. Genom att skapa token kan en registerägare ge användare eller tjänster begränsad åtkomst till lagringsplatsen för att hämta eller skicka avbildningar eller utföra andra åtgärder. En token ger mer omfattande behörigheter än andra alternativ för [registerautentisering](container-registry-authentication.md), som omfångsbehörigheter till ett helt register. 

Scenarier för att skapa en token är:

* Tillåt att IoT-enheter med enskilda token hämtar en avbildning från en lagringsplats
* Ge en extern organisation behörighet till en specifik lagringsplats 
* Begränsa lagringsplatsens åtkomst till olika användargrupper i din organisation. Ge till exempel skriv- och läsåtkomst till utvecklare som skapar avbildningar som riktar sig mot specifika lagringsplatsen och läsåtkomst till team som distribuerar från dessa lagringsplatsen.

Den här funktionen är tillgänglig på **tjänstnivån** premiumcontainerregister. Information om registertjänstnivåer och begränsningar finns i [Azure Container Registry tjänstnivåer](container-registry-skus.md).

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Du kan för närvarande inte tilldela behörigheter som är begränsade till lagringsplatsen till en Azure Active Directory identitet, till exempel ett huvudnamn för tjänsten eller en hanterad identitet.
* Du kan inte skapa en omfångskarta i ett register som har aktiverats [för anonym pull-åtkomst.](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)

## <a name="concepts"></a>Begrepp

Om du vill konfigurera behörigheter för lagringsplatsen skapar du en *token med* en associerad *omfångskarta*. 

* Med **en token** tillsammans med ett genererat lösenord kan användaren autentisera med registret. Du kan ange ett förfallodatum för ett tokenlösenord eller inaktivera en token när som helst.  

  Efter autentisering med en token kan användaren eller tjänsten utföra en eller flera åtgärder som *är* begränsade till en eller flera lagringsplatsen.

  |Åtgärd  |Beskrivning  | Exempel |
  |---------|---------|--------|
  |`content/delete`    | Ta bort data från lagringsplatsen  | Ta bort en lagringsplats eller ett manifest |
  |`content/read`     |  Läsa data från lagringsplatsen |  Hämta en artefakt |
  |`content/write`     |  Skriva data till lagringsplatsen     | Använd med `content/read` för att push-skicka en artefakt |
  |`metadata/read`    | Läsa metadata från lagringsplatsen   | Visa en lista över taggar eller manifest |
  |`metadata/write`     |  Skriva metadata till lagringsplatsen  | Aktivera eller inaktivera läs-, skriv- eller borttagningsåtgärder |

* En **omfångskarta** grupperar de lagringsplatsbehörigheter som du tillämpar på en token och kan tillämpas på andra token igen. Varje token är associerad med en enda omfångskarta. 

   Med en omfångskarta:

    * Konfigurera flera token med identiska behörigheter till en uppsättning lagringsplatsen
    * Uppdatera tokenbehörigheter när du lägger till eller tar bort åtgärder för lagringsplatsen i omfångskartan eller tillämpar en annan omfångskarta 

  Azure Container Registry också flera systemdefinierade omfångskartor som du kan tillämpa när du skapar token. Behörigheterna för systemdefinierade omfångsmappningar gäller för alla lagringsplatsen i registret.

Följande bild visar relationen mellan token och omfångsmappningar. 

![Registertoken och omfångsmappningar](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Förutsättningar

* **Azure CLI –** Kommandoexempel för Azure CLI-kommandon i den här artikeln kräver Azure CLI version 2.17.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).
* **Docker** – För att autentisera med registret för att hämta eller push-hämta avbildningar behöver du en lokal Docker-installation. I Docker finns installationsanvisningar för [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Containerregister** – Om du inte har ett skapar du ett Premium-containerregister i din Azure-prenumeration eller uppgraderar ett befintligt register. Du kan till exempel använda [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Skapa token – CLI

### <a name="create-token-and-specify-repositories"></a>Skapa token och ange lagringsplatsen

Skapa en token med kommandot [az acr token create.][az-acr-token-create] När du skapar en token kan du ange en eller flera lagringsplatsen och tillhörande åtgärder på varje lagringsplats. Lagringsplatsen behöver inte vara i registret ännu. Information om hur du skapar en token genom att ange en befintlig omfångskarta finns [i nästa avsnitt.](#create-token-and-specify-scope-map)

I följande exempel skapas en token i registret *myregistry* med följande behörigheter på `samples/hello-world` lagringsplatsen: `content/write` och `content/read` . Som standard anger kommandot standardtokenstatusen `enabled` till , men du kan uppdatera statusen till när som `disabled` helst.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Utdata visar information om token. Som standard genereras två lösenord som inte upphör att gälla, men du kan också ange ett förfallodatum. Vi rekommenderar att du sparar lösenorden på en säker plats så att du kan använda dem senare för autentisering. Lösenorden kan inte hämtas igen, men nya kan genereras.

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
> Information om hur du återskapar tokenlösenord och giltighetstider finns [i Återskapa tokenlösenord](#regenerate-token-passwords) senare i den här artikeln.

Utdata innehåller information om omfångskartan som kommandot skapade. Du kan använda omfångskartan, här `MyToken-scope-map` med namnet , för att tillämpa samma lagringsplatsåtgärder på andra token. Du kan också uppdatera omfångskartan senare för att ändra behörigheterna för de associerade token.

### <a name="create-token-and-specify-scope-map"></a>Skapa token och ange omfångskarta

Ett annat sätt att skapa en token är att ange en befintlig omfångskarta. Om du inte redan har en omfångskarta skapar du först en genom att ange lagringsplatsen och tillhörande åtgärder. Ange sedan omfångskartan när du skapar en token. 

Om du vill skapa en omfångskarta använder [du kommandot az acr scope-map create.][az-acr-scope-map-create] Följande kommando skapar en omfångskarta med samma behörigheter på den `samples/hello-world` lagringsplats som användes tidigare. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Kör [az acr token create för][az-acr-token-create] att skapa en token och ange *omfångskartan MyScopeMap.* Som i föregående exempel anger kommandot standardtokenstatusen till `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Utdata visar information om token. Som standard genereras två lösenord. Vi rekommenderar att du sparar lösenorden på en säker plats för senare autentisering. Lösenorden kan inte hämtas igen, men nya kan genereras.

> [!NOTE]
> Information om hur du återskapar tokenlösenord och giltighetstider finns [i Återskapa tokenlösenord](#regenerate-token-passwords) senare i den här artikeln.

## <a name="create-token---portal"></a>Skapa token – portalen

Du kan använda Azure Portal för att skapa token och omfångsmappningar. Precis som med CLI-kommandot kan du använda en befintlig omfångskarta eller skapa en omfångskarta när du skapar en token genom att ange en eller flera `az acr token create` lagringsplatsen och associerade åtgärder. Lagringsplatsen behöver inte finnas i registret ännu. 

I följande exempel skapas en token och en omfångskarta med följande behörigheter på `samples/hello-world` lagringsplatsen: `content/write` och `content/read` .

1. Navigera till containerregistret i portalen.
1. Under **Lagringsplatsbehörigheter** väljer **du Token (förhandsversion) > +Lägg till**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Skapa token i portalen":::
1. Ange ett tokennamn.
1. Under **Omfångskarta** väljer **du Skapa ny**.
1. Konfigurera omfångskartan:
    1. Ange ett namn och en beskrivning för omfångskartan. 
    1. Under **Lagringsplatsen anger** du `samples/hello-world` och under **Behörigheter** väljer du  `content/read` och `content/write` . Välj sedan **+Lägg till**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Skapa omfångskarta i portalen":::

    1. När du har lagt till lagringsplatsen och behörigheterna väljer du **Lägg till för** att lägga till omfångskartan.
1. Acceptera **standardtokenstatusen** **Aktiverad** och välj sedan **Skapa.**

När token har verifierats och skapats visas tokeninformation på **skärmen Token.**

### <a name="add-token-password"></a>Lägga till tokenlösenord

Om du vill använda en token som skapats i portalen måste du generera ett lösenord. Du kan generera ett eller två lösenord och ange ett förfallodatum för vart och ett. 

1. Navigera till containerregistret i portalen.
1. Under **Lagringsplatsbehörigheter** **väljer du Token (förhandsversion)** och väljer en token.
1. I tokeninformationen väljer du **password1** **eller password2** och sedan ikonen Generera.
1. På lösenordsskärmen kan du välja att ange ett förfallodatum för lösenordet och välja **Generera**. Vi rekommenderar att du anger ett förfallodatum.
1. När du har genererat ett lösenord kopierar du och sparar det på en säker plats. Du kan inte hämta ett genererat lösenord när du har stängt skärmen, men du kan generera ett nytt.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Skapa tokenlösenord i portalen":::

## <a name="authenticate-with-token"></a>Autentisera med token

När en användare eller tjänst använder en token för att autentisera med målregistret anger den tokennamnet som ett användarnamn och ett av dess genererade lösenord. 

Autentiseringsmetoden beror på den konfigurerade åtgärden eller de åtgärder som är associerade med token.

|Action  |Så här autentiserar du  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` i Azure CLI<br/><br/>Exempel: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` i Azure CLI<br/><br/>Exempel: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` i Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` i Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` i Azure CLI |

## <a name="examples-use-token"></a>Exempel: Använda token

I följande exempel används den token som skapades tidigare i den här artikeln för att utföra vanliga åtgärder på en lagringsplats: skicka och hämta avbildningar, ta bort avbildningar och lista lagringsplatstaggar. Token konfigurerades inledningsvis med push-behörigheter ( `content/write` och `content/read` åtgärder) på `samples/hello-world` lagringsplatsen.

### <a name="pull-and-tag-test-images"></a>Hämta och tagga testbilder

I följande exempel hämtar du offentliga `hello-world` avbildningar och `nginx` avbildningar Microsoft Container Registry och taggar dem för ditt register och din lagringsplats.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Autentisera med hjälp av token

Kör `docker login` eller för att autentisera med registret för att skicka eller hämta `az acr login` avbildningar. Ange tokennamnet som användarnamn och ange ett av dess lösenord. Token måste ha `Enabled` statusen .

Följande exempel är formaterat för bash-gränssnittet och tillhandahåller värdena med hjälp av miljövariabler.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Utdata bör visa lyckad autentisering:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Push-överför avbildningar till registret

Efter lyckad inloggning försöker du skicka de taggade avbildningarna till registret. Eftersom token har behörighet att skicka avbildningar till `samples/hello-world` lagringsplatsen lyckas följande push-

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Token har inte behörighet till lagringsplatsen, `samples/nginx` så följande push-försök misslyckas med ett fel som liknar `requested access to the resource is denied` :

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Uppdatera tokenbehörigheter

Uppdatera behörigheterna för en token genom att uppdatera behörigheterna i den associerade omfångskartan. Den uppdaterade omfångskartan tillämpas omedelbart på alla associerade token. 

Du kan till exempel `MyToken-scope-map` uppdatera med åtgärderna och på `content/write` `content/read` `samples/ngnx` lagringsplatsen och ta bort `content/write` åtgärden på `samples/hello-world` lagringsplatsen.  

Om du vill använda Azure CLI kör du [az acr scope-map update för][az-acr-scope-map-update] att uppdatera omfångskartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

I Azure-portalen:

1. Gå till containerregistret.
1. Under **Behörigheter för lagringsplats** väljer **du Omfångsmappningar (förhandsversion)** och väljer den omfångskarta som ska uppdateras.
1. Under **Lagringsplatsen anger** du `samples/nginx` och under **Behörigheter** väljer du `content/read` och `content/write` . Välj sedan **+Lägg till**.
1. Under **Lagringsplatsen väljer** du `samples/hello-world` och **avmarkerar under** Behörigheter `content/write` . Välj sedan **Spara**.

När du har uppdaterat omfångskartan lyckas följande push-åtgärder:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Eftersom omfångskartan bara har `content/read` behörighet på `samples/hello-world` lagringsplatsen misslyckas nu ett `samples/hello-world` push-försök till lagringsplatsen:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Det går att hämta avbildningar från båda lagringsplatsen eftersom `content/read` omfångskartan ger behörighet till båda lagringsplatsen:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Ta bort avbildningar

Uppdatera omfångskartan genom att `content/delete` lägga till åtgärden på lagringsplatsen. `nginx` Den här åtgärden tillåter borttagning av bilder i lagringsplatsen eller borttagning av hela lagringsplatsen.

Av utrymmessk ände visar vi bara [kommandot az acr scope-map update][az-acr-scope-map-update] för att uppdatera omfångskartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

Information om hur du uppdaterar omfångskartan med hjälp av portalen finns [i föregående avsnitt.](#update-token-permissions)

Använd följande az [acr repository delete-kommando][az-acr-repository-delete] för att ta bort lagringsplatsen. `samples/nginx` Om du vill ta bort avbildningar eller lagringsplatsen skickar du tokens namn och lösenord till kommandot . I följande exempel används miljövariablerna som skapades tidigare i artikeln:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Visa lagringsplatsens taggar 

Uppdatera omfångskartan genom att `metadata/read` lägga till åtgärden till lagringsplatsen. `hello-world` Med den här åtgärden kan du läsa manifest- och taggdata i lagringsplatsen.

Av utrymmessk ände visar vi bara [kommandot az acr scope-map update][az-acr-scope-map-update] för att uppdatera omfångskartan:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

Information om hur du uppdaterar omfångskartan med hjälp av portalen finns [i föregående avsnitt.](#update-token-permissions)

Om du vill läsa metadata på `samples/hello-world` lagringsplatsen kör du [kommandot az acr repository show-manifests][az-acr-repository-show-manifests] eller [az acr repository show-tags.][az-acr-repository-show-tags] 

Om du vill läsa metadata skickar du tokens namn och lösenord till något av kommandona. I följande exempel används miljövariablerna som skapades tidigare i artikeln:

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

## <a name="manage-tokens-and-scope-maps"></a>Hantera token och omfångsmappningar

### <a name="list-scope-maps"></a>Lista omfångskartor

Använd kommandot [az acr scope-map list][az-acr-scope-map-list] eller skärmen Omfångskartor **(förhandsversion)** i portalen för att visa en lista över alla omfångskartor som konfigurerats i ett register. Exempel:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Utdata består av de tre systemdefinierade omfångsmappningar och andra omfångskartor som genereras av dig. Token kan konfigureras med någon av dessa omfångskartor.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Visa tokeninformation

Om du vill visa information om en token, till exempel dess status och förfallodatum för lösenord, kör du kommandot [az acr token show][az-acr-token-show] eller väljer token på skärmen Token **(förhandsversion)** i portalen. Exempel:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Använd kommandot [az acr token list][az-acr-token-list] eller skärmen Token **(förhandsversion)** i portalen för att visa en lista över alla token som konfigurerats i ett register. Exempel:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Återskapa tokenlösenord

Om du inte genererade ett tokenlösenord, eller om du vill generera nya lösenord, kör du [kommandot az acr token credential generate.][az-acr-token-credential-generate] 

I följande exempel genereras ett nytt värde för password1 för *MyToken-token* med en förfalloperiod på 30 dagar. Lösenordet lagras i miljövariabeln `TOKEN_PWD` . Det här exemplet är formaterat för bash-gränssnittet.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Om du vill använda Azure Portal för att generera ett tokenlösenord läser du stegen i [Skapa token – portalen](#create-token---portal) tidigare i den här artikeln.

### <a name="update-token-with-new-scope-map"></a>Uppdatera token med ny omfångskarta

Om du vill uppdatera en token med en annan omfångskarta kör du [az acr token update][az-acr-token-update] och anger den nya omfångskartan. Exempel:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

På portalen går du till skärmen Token (förhandsversion) och väljer en annan omfångskarta **under** **Omfångskarta.**

> [!TIP]
> När du har uppdaterat en token med en ny omfångskarta kanske du vill generera nya tokenlösenord. Använd kommandot [az acr token credential generate][az-acr-token-credential-generate] eller återskapa ett tokenlösenord i Azure Portal.

## <a name="disable-or-delete-token"></a>Inaktivera eller ta bort token

Du kan behöva inaktivera användningen av tokenautentiseringsuppgifter för en användare eller tjänst tillfälligt. 

Använd Azure CLI och kör kommandot [az acr token update][az-acr-token-update] för att ange till `status` `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

I portalen väljer du token på skärmen **Token (förhandsversion)** och väljer **Inaktiverad** under **Status**.

Om du vill ta bort en token för att permanent ogiltigförklara åtkomst av alla som använder dess autentiseringsuppgifter kör du [kommandot az acr token delete.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

I portalen väljer du token på skärmen **Token (förhandsversion)** och väljer **Ignorera**.

## <a name="next-steps"></a>Nästa steg

* Om du vill hantera omfångsmappningar och token använder du ytterligare kommandon i kommandogrupperna [az acr scope-map][az-acr-scope-map] [och az acr token.][az-acr-token]
* Se [autentiseringsöversikten](container-registry-authentication.md) för andra alternativ för att autentisera med ett Azure-containerregister, inklusive att använda en Azure Active Directory-identitet, ett huvudnamn för tjänsten eller ett administratörskonto.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az_acr_repository_show_manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az_acr_repository_delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az_acr_scope_map_create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az_acr_scope_map_update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az_acr_token_show
[az-acr-token-list]: /cli/azure/acr/token/#az_acr_token_list
[az-acr-token-delete]: /cli/azure/acr/token/#az_acr_token_delete
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-update]: /cli/azure/acr/token/#az_acr_token_update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az_acr_token_credential_generate
