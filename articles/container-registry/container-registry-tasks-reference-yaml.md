---
title: YAML-referens – ACR-uppgifter
description: Referens för att definiera uppgifter i YAML för ACR-uppgifter, inklusive aktivitetsegenskaper, stegtyper, stegegenskaper och inbyggda variabler.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 126fcbce0569b2be6d9302cbbb718fa11e3e8046
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780955"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-uppgifter referens: YAML

Uppgiftsdefinition i flera steg i ACR-uppgifter en containercentrerad beräkningsprimit som fokuserar på att skapa, testa och korrigera containrar. Den här artikeln beskriver kommandon, parametrar, egenskaper och syntax för YAML-filerna som definierar dina uppgifter i flera steg.

Den här artikeln innehåller referens för att skapa YAML-filer med flera steg för ACR-uppgifter. Om du vill ha en introduktion till ACR-uppgifter kan du gå ACR-uppgifter [översikt](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>filformatet acr-task.yaml

ACR-uppgifter har stöd för uppgiftsdeklaration i flera steg i yaml-standardsyntaxen. Du definierar en uppgifts steg i en YAML-fil. Du kan sedan köra uppgiften manuellt genom att skicka filen till [kommandot az acr run.][az-acr-run] Du kan också använda filen för att skapa en uppgift med [az acr task create][az-acr-task-create] som utlöses automatiskt vid en Git-genomförande, en basavbildningsuppdatering eller ett schema. Även om den här artikeln refererar till som den fil som innehåller stegen ACR-uppgifter stöd för alla `acr-task.yaml` giltiga filnamn med ett [filnamnstillägg som stöds.](#supported-task-filename-extensions)

De översta primitiverna `acr-task.yaml` är **aktivitetsegenskaper,** **stegtyper** och **stegegenskaper:**

* [Uppgiftsegenskaper](#task-properties) gäller för alla steg under uppgiftskörningen. Det finns flera globala uppgiftsegenskaper, bland annat:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Aktivitetsstegtyper](#task-step-types) representerar de typer av åtgärder som kan utföras i en uppgift. Det finns tre stegtyper:
  * `build`
  * `push`
  * `cmd`
* [Egenskaper för uppgiftssteg](#task-step-properties) är parametrar som gäller för ett enskilt steg. Det finns flera stegegenskaper, bland annat:
  * `startDelay`
  * `timeout`
  * `when`
  * ... och många fler.

Basformatet för en `acr-task.yaml` fil, inklusive några vanliga stegegenskaper, följer nedan. Även om det inte är en fullständig representation av alla tillgängliga stegegenskaper eller användning av stegtyper ger det en snabb översikt över det grundläggande filformatet.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Filnamnstillägg för aktiviteter som stöds

ACR-uppgifter har reserverat flera filnamnstillägg, `.yaml` inklusive , som kommer att bearbetas som en uppgiftsfil. Alla *tillägg* som inte finns i följande lista anses av ACR-uppgifter vara en Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML är det enda filformat som för närvarande stöds av ACR-uppgifter. De andra filnamnstilläggen är reserverade för eventuell framtida support.

## <a name="run-the-sample-tasks"></a>Köra exempeluppgifterna

Det finns flera exempel på aktivitetsfiler som refereras i följande avsnitt i den här artikeln. Exempeluppgifterna finns på en offentlig GitHub-lagringsplats, [Azure-Samples/acr-tasks.][acr-tasks] Du kan köra dem med Azure CLI-kommandot [az acr run][az-acr-run]. Exempelkommandona liknar följande:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formateringen av exempelkommandona förutsätter att du har konfigurerat ett standardregister i Azure CLI, så de utelämnar `--registry` parametern. Om du vill konfigurera ett standardregister använder [du kommandot az configure][az-configure] med `--defaults` parametern , som accepterar ett `acr=REGISTRY_NAME` värde.

Om du till exempel vill konfigurera Azure CLI med ett standardregister med namnet "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Uppgiftsegenskaper

Uppgiftsegenskaper visas vanligtvis överst i en fil och är globala egenskaper som gäller under hela `acr-task.yaml` körningen av uppgiftsstegen. Vissa av dessa globala egenskaper kan åsidosättas i ett enskilt steg.

| Egenskap | Typ | Valfritt | Description | Åsidosättning stöds | Standardvärde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sträng | Yes | Den version av `acr-task.yaml` filen som parsas av ACR-uppgifter tjänsten. Även ACR-uppgifter strävar efter att upprätthålla bakåtkompatibilitet gör det här värdet ACR-uppgifter att upprätthålla kompatibiliteten inom en definierad version. Om det inte anges används den senaste versionen som standard. | No | Inget |
| `stepTimeout` | int (sekunder) | Yes | Det maximala antalet sekunder som ett steg kan köras. Om egenskapen anges för en aktivitet anges `timeout` standardegenskapen för alla steg. Om egenskapen `timeout` anges i ett steg åsidosätter den egenskapen som anges av aktiviteten. | Yes | 600 (10 minuter) |
| `workingDirectory` | sträng | Yes | Arbetskatalogen för containern under körning. Om egenskapen anges för en aktivitet anges `workingDirectory` standardegenskapen för alla steg. Om det anges i ett steg åsidosätts egenskapen som tillhandahålls av aktiviteten. | Yes | `c:\workspace` i Windows eller `/workspace` i Linux |
| `env` | [sträng, sträng, ...] | Yes |  Matris med strängar i `key=value` format som definierar miljövariablerna för uppgiften. Om egenskapen anges för en aktivitet anges `env` standardegenskapen för alla steg. Om det anges i ett steg åsidosätts alla miljövariabler som ärvts från aktiviteten. | Yes | Inget |
| `secrets` | [hemlighet, hemlighet, ...] | Yes | Matris med [hemliga](#secret) objekt. | No | Inget |
| `networks` | [nätverk, nätverk, ...] | Yes | Matris med [nätverksobjekt.](#network) | No | Inget |
| `volumes` | [volym, volym, ...] | Yes | Matris med [volymobjekt.](#volume) Anger volymer med källinnehåll som ska monteras i ett steg. | No | Inget |

### <a name="secret"></a>hemlighet

Det hemliga objektet har följande egenskaper.

| Egenskap | Typ | Valfritt | Description | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sträng | No | Identifieraren för hemligheten. | Inget |
| `keyvault` | sträng | Yes | Webbadressen Azure Key Vault hemlighet. | Inget |
| `clientID` | sträng | Yes | Klient-ID för den användar [tilldelade hanterade identiteten för](container-registry-tasks-authentication-managed-identity.md) Azure-resurser. | Inget |

### <a name="network"></a>network

Nätverksobjektet har följande egenskaper.

| Egenskap | Typ | Valfritt | Description | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sträng | No | Namnet på nätverket. | Inget |
| `driver` | sträng | Yes | Drivrutinen för att hantera nätverket. | Inget |
| `ipv6` | boolesk | Yes | Om IPv6-nätverk är aktiverat. | `false` |
| `skipCreation` | boolesk | Yes | Om du vill hoppa över skapandet av nätverk. | `false` |
| `isDefault` | boolesk | Yes | Om nätverket är ett standardnätverk som medföljer Azure Container Registry. | `false` |

### <a name="volume"></a>volym

Volymobjektet har följande egenskaper.

| Egenskap | Typ | Valfritt | Description | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sträng | No | Namnet på volymen som ska monteras. Får endast innehålla alfanumeriska tecken, "-" och "_". | Inget |
| `secret` | map[string]string | No | Varje nyckel på kartan är namnet på en fil som skapats och fyllts i volymen. Varje värde är strängversionen av hemligheten. Hemliga värden måste vara Base64-kodade. | Inget |

## <a name="task-step-types"></a>Typer av uppgiftssteg

ACR-uppgifter har stöd för tre steg. Varje stegtyp har stöd för flera egenskaper, som beskrivs i avsnittet för varje stegtyp.

| Stegtyp | Description |
| --------- | ----------- |
| [`build`](#build) | Skapar en containeravbildning med hjälp av välbekant `docker build` syntax. |
| [`push`](#push) | Kör en av `docker push` nyligen skapade eller omtaggade avbildningar till ett containerregister. Azure Container Registry, andra privata register och offentliga Docker Hub stöds. |
| [`cmd`](#cmd) | Kör en container som ett kommando med parametrar som skickas till containerns `[ENTRYPOINT]` . Stegtypen `cmd` stöder parametrar som , och andra välbekanta `env` kommandoalternativ, vilket möjliggör `detach` `docker run` enhets- och funktionstestning med samtidig containerkörning. |

## <a name="build"></a>skapa

Skapa en containeravbildning. Stegtypen `build` representerar ett säkert sätt för flera innehavare att köra i molnet som en primitiv i första `docker build` klass.

### <a name="syntax-build"></a>Syntax: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Stegtypen `build` stöder parametrarna i följande tabell. Stegtypen `build` stöder också alla byggalternativ för kommandot [docker build,](https://docs.docker.com/engine/reference/commandline/build/) till exempel `--build-arg` för att ange byggtidsvariabler.

| Parameter | Beskrivning | Valfritt |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definierar den fullständigt kvalificerade `image:tag` avbildningen.<br /><br />Eftersom bilder kan användas för inre uppgiftsverifieringar, till exempel funktionella tester, behöver inte alla `push` avbildningar till ett register. Men för att instansen av en avbildning i en uppgiftskörning ska kunna visas behöver avbildningen ett namn att referera till.<br /><br />Till `az acr build` skillnad från ACR-uppgifter inte push-standardbeteendet när du kör ett program. Med ACR-uppgifter förutsätter standardscenariot att du kan skapa, validera och sedan push-skicka en avbildning. Se [push för](#push) att se hur du kan pusha skapade avbildningar om du vill. | Yes |
| `-f` &#124; `--file` | Anger den Dockerfile som skickas till `docker build` . Om inget anges antas standard-Dockerfile i kontextens rot. Om du vill ange en Dockerfile skickar du filnamnet relativt till kontextens rot. | Yes |
| `context` | Rotkatalogen som skickas till `docker build` . Rotkatalogen för varje uppgift är inställd på en delad [workingDirectory](#task-step-properties)och innehåller roten för den associerade Git-klonade katalogen. | No |

### <a name="properties-build"></a>Egenskaper: build

Stegtypen `build` stöder följande egenskaper. Mer information om dessa egenskaper finns i avsnittet [Egenskaper för uppgiftssteg](#task-step-properties) i den här artikeln.

| Egenskaper | Typ | Obligatorisk |
| -------- | ---- | -------- |
| `detach` | boolesk | Valfritt |
| `disableWorkingDirectoryOverride` | boolesk | Valfritt |
| `entryPoint` | sträng | Valfritt |
| `env` | [sträng, sträng, ...] | Valfritt |
| `expose` | [sträng, sträng, ...] | Valfritt |
| `id` | sträng | Valfritt |
| `ignoreErrors` | boolesk | Valfritt |
| `isolation` | sträng | Valfritt |
| `keep` | boolesk | Valfritt |
| `network` | objekt | Valfritt |
| `ports` | [sträng, sträng, ...] | Valfritt |
| `pull` | boolesk | Valfritt |
| `repeat` | int | Valfritt |
| `retries` | int | Valfritt |
| `retryDelay` | int (sekunder) | Valfritt |
| `secret` | objekt | Valfritt |
| `startDelay` | int (sekunder) | Valfritt |
| `timeout` | int (sekunder) | Valfritt |
| `volumeMount` | objekt | Valfritt |
| `when` | [sträng, sträng, ...] | Valfritt |
| `workingDirectory` | sträng | Valfritt |

### <a name="examples-build"></a>Exempel: build

#### <a name="build-image---context-in-root"></a>Skapa avbildning – kontext i roten

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Skapa avbildning – kontext i underkatalog

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Tryck

Push-skicka en eller flera byggda eller omtaggade avbildningar till ett containerregister. Stöder push-push till privata register som Azure Container Registry eller till offentliga Docker Hub.

### <a name="syntax-push"></a>Syntax: push

Stegtypen `push` stöder en samling avbildningar. YAML-samlingssyntax har stöd för infogade och kapslade format. Push-push av en enskild bild representeras vanligtvis med hjälp av infogade syntax:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Använd kapslad syntax när du push-överföra flera bilder för ökad läsbarhet:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Egenskaper: push

Stegtypen `push` stöder följande egenskaper. Information om dessa egenskaper finns i avsnittet [Egenskaper för uppgiftssteg](#task-step-properties) i den här artikeln.

| Egenskap | Typ | Obligatorisk |
| -------- | ---- | -------- |
| `env` | [sträng, sträng, ...] | Valfritt |
| `id` | sträng | Valfritt |
| `ignoreErrors` | boolesk | Valfritt |
| `startDelay` | int (sekunder) | Valfritt |
| `timeout` | int (sekunder) | Valfritt |
| `when` | [sträng, sträng, ...] | Valfritt |

### <a name="examples-push"></a>Exempel: push

#### <a name="push-multiple-images"></a>Push-pusha flera avbildningar

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Skapa, push-pusha och köra

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Stegtypen `cmd` kör en container.

### <a name="syntax-cmd"></a>Syntax: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Egenskaper: cmd

Stegtypen `cmd` stöder följande egenskaper:

| Egenskap | Typ | Obligatorisk |
| -------- | ---- | -------- |
| `detach` | boolesk | Valfritt |
| `disableWorkingDirectoryOverride` | boolesk | Valfritt |
| `entryPoint` | sträng | Valfritt |
| `env` | [sträng, sträng, ...] | Valfritt |
| `expose` | [sträng, sträng, ...] | Valfritt |
| `id` | sträng | Valfritt |
| `ignoreErrors` | boolesk | Valfritt |
| `isolation` | sträng | Valfritt |
| `keep` | boolesk | Valfritt |
| `network` | objekt | Valfritt |
| `ports` | [sträng, sträng, ...] | Valfritt |
| `pull` | boolesk | Valfritt |
| `repeat` | int | Valfritt |
| `retries` | int | Valfritt |
| `retryDelay` | int (sekunder) | Valfritt |
| `secret` | objekt | Valfritt |
| `startDelay` | int (sekunder) | Valfritt |
| `timeout` | int (sekunder) | Valfritt |
| `volumeMount` | objekt | Valfritt |
| `when` | [sträng, sträng, ...] | Valfritt |
| `workingDirectory` | sträng | Valfritt |

Du hittar information om dessa egenskaper i avsnittet Egenskaper [för uppgiftssteg](#task-step-properties) i den här artikeln.

### <a name="examples-cmd"></a>Exempel: cmd

#### <a name="run-hello-world-image"></a>Kör hello-world-avbildning

Det här kommandot kör `hello-world.yaml` uppgiftsfilen, som refererar till [hello-world-avbildningen](https://hub.docker.com/_/hello-world/) på Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Kör bash-avbildningen och ekot "hello world"

Det här kommandot kör `bash-echo.yaml` uppgiftsfilen, som refererar till [bash-avbildningen](https://hub.docker.com/_/bash/) på Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Köra specifik bash-avbildningstagg

Om du vill köra en specifik avbildningsversion anger du taggen i `cmd` .

Det här kommandot kör `bash-echo-3.yaml` uppgiftsfilen, som refererar till [bash:3.0-avbildningen](https://hub.docker.com/_/bash/) Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Köra anpassade avbildningar

Stegtypen `cmd` refererar till bilder med `docker run` standardformatet. Avbildningar som inte föregås av ett register antas komma från docker.io. Det föregående exemplet kan också representeras som:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Genom att använda `docker run` referenskonventionen för standardavbildningar `cmd` kan du köra avbildningar från alla privata register eller offentliga Docker Hub. Om du refererar till avbildningar i samma register där ACR Task körs behöver du inte ange några autentiseringsuppgifter för registret.

* Kör en avbildning som kommer från ett Azure-containerregister. I följande exempel förutsätts att du har ett register med namnet `myregistry` och en anpassad avbildning `myimage:mytag` .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalisera registerreferensen med en Körningsvariabel eller ett alias

    I stället för att hårdkoda ditt registernamn i en fil kan du göra det mer `acr-task.yaml` portabelt med hjälp av [variabeln Kör](#run-variables) eller [aliaset](#aliases). `Run.Registry`Variabeln `$Registry` eller aliaset expanderas vid körning till namnet på det register där aktiviteten körs.

    Om du till exempel vill generalisera föregående uppgift så att den fungerar i alla Azure-containerregister refererar du $Registry variabeln i avbildningens namn:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Få åtkomst till hemliga volymer

Egenskapen `volumes` tillåter att volymer och deras hemliga innehåll anges för stegen och i en `build` `cmd` uppgift. I varje steg visar en valfri `volumeMounts` egenskap volymerna och motsvarande containersökvägar som ska monteras i containern i det steget. Hemligheter tillhandahålls som filer på varje volyms monteringssökväg.

Kör en uppgift och montera två hemligheter i ett steg: en som lagras i ett nyckelvalv och en som anges på kommandoraden:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Egenskaper för uppgiftssteg

Varje stegtyp har stöd för flera egenskaper som är lämpliga för dess typ. I följande tabell definieras alla tillgängliga stegegenskaper. Alla stegtyper har inte stöd för alla egenskaper. Du kan se vilka av dessa egenskaper som är tillgängliga för varje stegtyp i referensavsnitten [cmd](#cmd), [build](#build)och [push](#push) step type.

| Egenskap | Typ | Valfritt | Description | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | boolesk | Yes | Om containern ska frånkopplad när den körs. | `false` |
| `disableWorkingDirectoryOverride` | boolesk | Yes | Om du vill inaktivera `workingDirectory` åsidosättningsfunktioner. Använd detta i kombination `workingDirectory` med för att få fullständig kontroll över containerns arbetskatalog. | `false` |
| `entryPoint` | sträng | Yes | Åsidosätter `[ENTRYPOINT]` för ett stegs container. | Inget |
| `env` | [sträng, sträng, ...] | Yes | Matris med strängar i `key=value` format som definierar miljövariablerna för steget. | Inget |
| `expose` | [sträng, sträng, ...] | Yes | Matris med portar som exponeras från containern. |  Inget |
| [`id`](#example-id) | sträng | Yes | Identifierar unikt steget i uppgiften. Andra steg i uppgiften kan referera till ett stegs , till `id` exempel för beroendekontroll med `when` .<br /><br />`id`är också namnet på containern som körs. Processer som körs i andra containrar i aktiviteten kan till exempel referera till som dess DNS-värdnamn eller för att komma åt den med `id` dockerloggar [id], till exempel. | `acb_step_%d`, `%d` där är det 0-baserade indexet för steget uppifrån och ned i YAML-filen |
| `ignoreErrors` | boolesk | Yes | Om steget ska markeras som lyckat oavsett om ett fel uppstod under containerkörningen eller inte. | `false` |
| `isolation` | sträng | Yes | Isoleringsnivån för containern. | `default` |
| `keep` | boolesk | Yes | Om stegets container ska behållas efter körningen. | `false` |
| `network` | objekt | Yes | Identifierar ett nätverk där containern körs. | Inget |
| `ports` | [sträng, sträng, ...] | Yes | Matris med portar som publiceras från containern till värden. |  Inget |
| `pull` | boolesk | Yes | Om du vill tvinga fram en låsning av containern innan den körs för att förhindra cachelagringsbeteende. | `false` |
| `privileged` | boolesk | Yes | Om du vill köra containern i privilegierat läge. | `false` |
| `repeat` | int | Yes | Antalet återförsök för att upprepa körningen av en container. | 0 |
| `retries` | int | Yes | Antalet återförsök att försöka om en container misslyckas med körningen. Ett nytt försök görs bara om slutkoden för en container inte är noll. | 0 |
| `retryDelay` | int (sekunder) | Yes | Fördröjningen i sekunder mellan återförsök för en containers körning. | 0 |
| `secret` | objekt | Yes | Identifierar en Azure Key Vault eller [hanterad identitet för Azure-resurser.](container-registry-tasks-authentication-managed-identity.md) | Inget |
| `startDelay` | int (sekunder) | Yes | Antal sekunder för att fördröja körningen av en container. | 0 |
| `timeout` | int (sekunder) | Yes | Maximalt antal sekunder som ett steg kan köras innan det avslutas. | 600 |
| [`when`](#example-when) | [sträng, sträng, ...] | Yes | Konfigurerar ett stegs beroende av ett eller flera andra steg i aktiviteten. | Inget |
| `user` | sträng | Yes | Användarnamnet eller UID:t för en container | Inget |
| `workingDirectory` | sträng | Yes | Anger arbetskatalogen för ett steg. Som standard ACR-uppgifter en rotkatalog som arbetskatalog. Men om bygget har flera steg kan tidigare steg dela artefakter med senare steg genom att ange samma arbetskatalog. | `c:\workspace` i Windows eller `/workspace` i Linux |

### <a name="volumemount"></a>volumeMount

VolumeMount-objektet har följande egenskaper.

| Egenskap | Typ | Valfritt | Description | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sträng | No | Namnet på volymen som ska monteras. Måste exakt matcha namnet från en `volumes` egenskap. | Inget |
| `mountPath`   | sträng | nej | Den absoluta sökvägen för att montera filer i containern.  | Inget |

### <a name="examples-task-step-properties"></a>Exempel: Egenskaper för uppgiftssteg

#### <a name="example-id"></a>Exempel: id

Skapa två avbildningar och ta en funktionstestbild. Varje steg identifieras av en unik som `id` andra steg i uppgiftsreferensen i sin `when` egenskap.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exempel: när

Egenskapen `when` anger ett stegs beroende av andra steg i aktiviteten. Den stöder två parametervärden:

* `when: ["-"]` – Anger inget beroende av andra steg. Ett steg som `when: ["-"]` anger startar körningen omedelbart och aktiverar samtidig stegkörning.
* `when: ["id1", "id2"]` – Anger att steget är beroende av steg med `id` "id1" och `id` "id2". Det här steget körs inte förrän både stegen "id1" och "id2" har slutförts.

Om `when` inte anges i ett steg beror det steget på slutförandet av föregående steg i `acr-task.yaml` filen.

Körning av sekventiella steg utan `when` :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sekventiell stegkörning med `when` :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallella avbildningar:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallell avbildningsbygge och beroende testning:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Köra variabler

ACR-uppgifter innehåller en standarduppsättning variabler som är tillgängliga för uppgiftssteg när de körs. Du kan komma åt dessa variabler med formatet `{{.Run.VariableName}}` , där är något av `VariableName` följande:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Variabelnamnen är vanligtvis självförklarande. Information följer för vanliga variabler. Från och med YAML-versionen `v1.1.0` kan du använda ett förkortat, fördefinierat [uppgiftsalias](#aliases) i stället för de flesta körningsvariabler. I stället för använder `{{.Run.Registry}}` du till exempel `$Registry` aliaset .

### <a name="runid"></a>Run.ID

Varje Körning, via `az acr run` eller utlösarbaserad körning av uppgifter som skapats via `az acr task create` har ett unikt ID. ID:t representerar den körning som körs för närvarande.

Används vanligtvis för en unikt taggning av en avbildning:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run.SharedVolume

Unik identifierare för en delad volym som kan nås av alla uppgiftssteg. Volymen monteras på i `c:\workspace` Windows eller `/workspace` i Linux. 

### <a name="runregistry"></a>Run.Registry

Det fullständigt kvalificerade servernamnet för registret. Används vanligtvis för att referera till registret där aktiviteten körs.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

Namnet på containerregistret. Används vanligtvis i uppgiftssteg som inte kräver ett fullständigt kvalificerat servernamn, till exempel steg som `cmd` kör Azure CLI-kommandon på register.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

Den aktuella UTC-tid då körningen påbörjades.

### <a name="runcommit"></a>Run.Commit

För en aktivitet som utlöses av en commit till en GitHub-lagringsplats, genomför du id:t.

### <a name="runbranch"></a>Run.Branch

För en aktivitet som utlöses av en commit till en GitHub-lagringsplats, grennamnet.

## <a name="aliases"></a>Alias

Från och `v1.1.0` med ACR-uppgifter alias som är tillgängliga för uppgiftssteg när de körs. Alias liknar alias (kommandogenvägar) som stöds i bash och vissa andra kommandogränssnitt. 

Med ett alias kan du starta ett kommando eller en grupp med kommandon (inklusive alternativ och filnamn) genom att ange ett enda ord.

ACR-uppgifter har stöd för flera fördefinierade alias och även anpassade alias som du skapar.

### <a name="predefined-aliases"></a>Fördefinierade alias

Följande aktivitetsalias är tillgängliga för användning i stället för [körningsvariabler:](#run-variables)

| Alias | Kör variabel |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

I uppgiftsstegen ska du föregå ett alias `$` med -direktivet, som i det här exemplet:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Avbildningsalias

Vart och ett av följande alias pekar på en stabil avbildning i Microsoft Container Registry (MCR). Du kan referera till var och en av dem i `cmd` avsnittet i en uppgiftsfil utan att använda ett direktiv.

| Alias | Bild |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

I följande exempelaktivitet används flera alias för att [rensa avbildningstaggar](container-registry-auto-purge.md) som är äldre än 7 dagar i `samples/hello-world` lagringsplatsen i körningsregistret:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Anpassat alias

Definiera ett anpassat alias i YAML-filen och använd det som du ser i följande exempel. Ett alias får bara innehålla alfanumeriska tecken. Standarddirektivet för att expandera ett alias är `$` tecknet .

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Du kan länka till en fjärransluten eller lokal YAML-fil för anpassade aliasdefinitioner. Följande exempel länkar till en YAML-fil i Azure Blob Storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Nästa steg

En översikt över uppgifter i flera steg finns i Köra uppgifter för att [skapa, testa](container-registry-tasks-multi-step.md)och korrigera flera steg i ACR-uppgifter .

Information om byggen med ett steg finns i [ACR-uppgifter översikt.](container-registry-tasks-overview.md)



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-configure]: /cli/azure/reference-index#az_configure
