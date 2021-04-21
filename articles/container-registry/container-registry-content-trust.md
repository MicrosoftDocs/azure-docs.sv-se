---
title: Hantera signerade avbildningar
description: Lär dig hur du aktiverar innehållsförtroende för ditt Azure-containerregister och push-överför och hämtar signerade avbildningar. Innehållsförtroende implementerar Docker-innehållsförtroende och är en funktion på Premium-tjänstnivån.
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 238908c0075ffa5d2193eda642175a0cfe75b839
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784127"
---
# <a name="content-trust-in-azure-container-registry"></a>Innehållsförtroende i Azure Container Registry

Azure Container Registry implementerar Dockers modell för [innehållsförtroende,][docker-content-trust] vilket möjliggör push-teknik och dragning av signerade avbildningar. Den här artikeln beskriver hur du aktiverar innehållsförtroende i dina containerregister.

> [!NOTE]
> Innehållsförtroende är en funktion [i Premium-tjänstnivån](container-registry-skus.md) för Azure Container Registry.

## <a name="how-content-trust-works"></a>Så fungerar innehållsförtroende

Det är viktigt för alla distribuerade system som har utformats för säkerhet att både *källan* och *integriteten* för de data som skickas till systemet verifieras. Konsumenter av data måste både kunna verifiera utgivaren (källan) av data och se till att den inte har ändrats efter publiceringen (integritet). 

Som avbildningsutgivare kan du använda innehållsförtroende för att **signera** de avbildningar som du överför till registret. Konsumenter av dina avbildningar (personer eller system som hämtar avbildningar från ditt register) kan konfigurera sina klienter att hämta *endast* signerade avbildningar. När en avbildningskonsument hämtar en signerad avbildning verifierar deras Docker-klient avbildningens integritet. I den här modellen försäkras konsumenterna om att de signerade avbildningarna i registret verkligen publicerades av dig och att de inte har ändrats efter publiceringen.

### <a name="trusted-images"></a>Betrodda avbildningar

Innehållsförtroende fungerar med **taggarna** i en lagringsplats. Lagringsplatser för avbildningar kan innehålla avbildningar med både signerade och osignerade taggar. Till exempel signerar du kanske endast avbildningarna `myimage:stable` och `myimage:latest` men inte `myimage:dev`.

### <a name="signing-keys"></a>Signeringsnycklar

Innehållsförtroende hanteras genom användning av en uppsättning kryptografiska signeringsnycklar. De här nycklarna är associerade med en specifik lagringsplats i ett register. Det finns flera typer av signeringsnycklar som Docker-klienter och ditt register använder vid hantering av förtroende för taggarna i en databas. När du aktiverar innehållsförtroende och integrerar det i din containerpublicerings- och förbrukningspipeline måste du hantera de här nycklarna noggrant. Mer information finns i [Nyckelhantering](#key-management) senare i den här artikeln och [Hantera nycklar för innehållsförtroende][docker-manage-keys] i Docker-dokumentationen.

> [!TIP]
> Detta var en mycket allmän översikt över Dockers modell för innehållsförtroende. En detaljerad beskrivning av innehållsförtroende finns i [Innehållsförtroende i Docker][docker-content-trust].

## <a name="enable-registry-content-trust"></a>Aktivera innehållsförtroende för register

Första steget är att aktivera innehållsförtroende på registernivån. När du har aktiverat innehållsförtroende kan klienter (användare eller tjänster) överföra signerade avbildningar till ditt register. Aktivering av innehållsförtroende i ditt register begränsar inte registeranvändning till endast konsumenter med innehållsförtroende aktiverat. Konsumenter utan innehållsförtroende aktiverat kan fortsätta att använda ditt register som vanligt. Konsumenter som har aktiverat innehållsförtroende i sina klienter kan dock *endast* se signerade avbildningar i ditt register.

Om du vill aktivera innehållsförtroende för ditt register navigerar du först till registret i Azure-portalen. Under **Principer** väljer du **Innehållsförtroende**  >  **aktiverat**  >  **Spara.** Du kan också använda [kommandot az acr config content-trust update][az-acr-config-content-trust-update] i Azure CLI.

![Skärmbild som visar aktivering av innehållsförtroende för ett register i Azure Portal.][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>Aktivera innehållsförtroende för klient

För att arbeta med betrodda avbildningar måste både bildutgivare och konsumenter aktivera innehållsförtroende för sina Docker-klienter. Som utgivare kan du signera de avbildningar som du överför till ett register med innehållsförtroende aktiverat. Som konsument får du en vy för ett register som är begränsad till endast signerade avbildningar om innehållsförtroende aktiveras. Innehållsförtroende är inaktiverat som standard i Docker-klienter, men du kan aktivera det per shell-session eller per kommando.

Om du vill aktivera innehållsförtroende för en shell-session ställer du in `DOCKER_CONTENT_TRUST`-miljövariabeln till **1**. Till exempel i Bash-gränssnittet:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Om du i stället vill aktivera eller inaktivera innehållsförtroende för ett enda kommando stöder flera Docker-kommandon argumentet `--disable-content-trust`. Så här aktiverar du innehållsförtroende för ett enda kommando:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

Om du har aktiverat innehållsförtroende för shell-sessionen och vill inaktivera det för ett enda kommando:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Bevilja behörigheter för signering av avbildningar

Endast de användare eller system som du har beviljat behörighet kan skicka betrodda avbildningar till registret. Om du vill ge användare behörighet att skicka betrodda avbildningar (eller ett system som använder ett tjänsthuvudnamn) ger du deras Azure Active Directory-identiteter rollen `AcrImageSigner`. Detta är utöver rollen `AcrPush` (eller motsvarande) som krävs för att push-pusha avbildningar till registret. Mer information finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

> [!IMPORTANT]
> Du kan inte bevilja push-behörighet för betrodda avbildningar till följande administrativa konton: 
> * [administratörskontot](container-registry-authentication.md#admin-account) för ett Azure-containerregister
> * ett användarkonto i Azure Active Directory med den [klassiska systemadministratörsrollen](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

Information om att bevilja rollen `AcrImageSigner` i Azure-portalen och Azure CLI följer nedan.

### <a name="azure-portal"></a>Azure Portal

Gå till registret i Azure Portal och välj sedan **Åtkomstkontroll (IAM) Lägg**  >  **till rolltilldelning**. Under **Lägg till rolltilldelning** väljer du `AcrImageSigner` under **Roll**. **Välj** sedan en eller flera användare eller tjänsthuvudnamn och **Spara** därefter.

I det här exemplet har två entiteter tilldelats rollen: ett tjänsthuvudnamn med namnet `AcrImageSigner` "service-principal" och en användare med namnet "Azure-användare".

![Bevilja signeringsbehörigheter för ACR-avbildningar i Azure Portal][content-trust-02-portal]

### <a name="azure-cli"></a>Azure CLI

Om du vill tilldela en användare signeringsbehörigheter med hjälp av Azure CLI tilldelar du rollen `AcrImageSigner` till användaren, med omfång för ditt register. Formatet för kommandot är:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Om du till exempel vill ge en icke-administrativ användare rollen kan du köra följande kommandon i en autentiserad Azure CLI-session. Ändra värdet `REGISTRY` så att det speglar namnet på ditt Azure-containerregister.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)
```

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee azureuser@contoso.com
```

Du kan även ge ett [tjänsthuvudnamn](container-registry-auth-service-principal.md) behörighet att skicka betrodda avbildningar till registret. Att använda ett tjänsthuvudnamn är användbart för byggsystem och andra obevakade system som behöver skicka betrodda avbildningar till registret. Formatet liknar åtgärden för att bevilja en användarbehörighet, men du anger ett tjänsthuvudnamn-ID för värdet `--assignee`.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

`<service principal ID>` kan vara tjänsthuvudnamnets **appId**, **objectId**, eller något av dess **servicePrincipalNames**. Mer information om hur du arbetar med tjänsthuvudnamn och Azure Container Registry finns i avsnittet om [Azure Container Registry-autentisering med tjänsthuvudnamn](container-registry-auth-service-principal.md).

> [!IMPORTANT]
> När rolländringarna har gjorts kör du för att uppdatera den lokala identitetstoken `az acr login` för Azure CLI så att de nya rollerna kan gälla. Information om hur du verifierar roller för en identitet finns i Lägga till eller ta bort [Azure-rolltilldelningar](../role-based-access-control/role-assignments-cli.md) med Azure CLI [och Felsöka Azure RBAC.](../role-based-access-control/troubleshooting.md)

## <a name="push-a-trusted-image"></a>Överföra en betrodd avbildning

För att skicka en tagg för en betrodd avbildning till ditt containerregister aktiverar du innehållsförtroende och överför avbildningen med `docker push`. När push med en signerad tagg har slutförts första gången uppmanas du att skapa en lösenfras för både en rotsigneringsnyckel och en signeringsnyckel för lagringsplatsen. Både rotnyckeln och lagringsplatsnyckeln genereras och lagras lokalt på din dator.

```console
$ docker push myregistry.azurecr.io/myimage:v1
[...]
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

Efter din första `docker push` med innehållsförtroende aktiverat använder Docker-klienten samma rotnyckel för efterföljande överföringar. För varje efterföljande överföring till samma databas uppmanas du endast att ange lagringsplatsnyckeln. Varje gång du överför en betrodd avbildning till en ny lagringsplats uppmanas du att ange en lösenfras för en ny lagringsplatsnyckel.

## <a name="pull-a-trusted-image"></a>Hämta en betrodd avbildning

För att hämta en betrodd avbildning aktiverar du innehållsförtroende och kör kommandot `docker pull` som vanligt. För att hämta betrodda `AcrPull` avbildningar räcker rollen för normala användare. Inga ytterligare roller som `AcrImageSigner` en roll krävs. Konsumenter med innehållsförtroende aktiverat kan endast hämta avbildningar med signerade taggar. Här är ett exempel på att hämta en signerad tagg:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

Om en klient med innehållsförtroende aktiverat försöker hämta en osignerad tagg misslyckas åtgärden med ett fel som liknar följande:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
Error: remote trust data does not exist
```

### <a name="behind-the-scenes"></a>I bakgrunden

När du kör `docker pull` använder Docker-klienten samma bibliotek som i [Notary CLI][docker-notary-cli] för att begära tagg-till-SHA-256-hashmappningen för den tagg du hämtar. Efter verifiering av signaturerna på förtroendedata instruerar klienten Docker-motorn att utföra en ”hämtning via hash”. Under hämtningen använder motorn SHA-256-kontrollsumman som en innehållsadress för att begära och verifiera avbildningsmanifestet från Azure-containerregistret.

> [!NOTE]
> Azure Container Registry stöder inte officiellt Notary CLI men är kompatibelt med Notary Server-API:et, som ingår i Docker Desktop. För närvarande rekommenderas Notary **version 0.6.0.**

## <a name="key-management"></a>Nyckelhantering

Enligt `docker push`-utdata är rotnyckeln som mest känslig när du överför din första betrodda avbildning. Glöm inte att säkerhetskopiera din rotnyckel och lagra den på en säker plats. Som standard lagrar Docker-klienten signeringsnycklar i följande katalog:

```sh
~/.docker/trust/private
```

Kopiera rot- och lagringsplatsnycklarna genom att komprimera dem i ett arkiv och lagra dem på en säker plats. Till exempel i Bash:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

Utöver de lokalt genererade rot- och lagringsplatsnycklarna genereras och lagras flera andra av Azure Container Registry när du överför en betrodd avbildning. En detaljerad beskrivning av olika nycklar i Dockers implementering av innehållsförtroende, inklusive ytterligare vägledning för hantering, finns i [Hantera nycklar för innehållsförtroende][docker-manage-keys] i Docker-dokumentationen.

### <a name="lost-root-key"></a>Förlorad rotnyckel

Om du förlorar åtkomst till din rotnyckel förlorar du åtkomsten till de signerade taggarna i alla lagringsplats vars taggar signerades med den nyckeln. Azure Container Registry kan inte återställa åtkomst till avbildningstaggar som signerats med en förlorad rotnyckel. Om du vill ta bort alla förtroendedata (signaturer) för ditt register inaktiverar du först och återaktiverar sedan innehållsförtroende för registret.

> [!WARNING]
> Om du inaktiverar och återaktiverar innehållsförtroende i registret **tas alla förtroendedata bort för alla signerade taggar i alla lagringsplats i registret**. Den här åtgärden går inte att ångra – Azure Container Registry kan inte återställa borttagna förtroendedata. Inaktivering av innehållsförtroende tar inte bort själva avbildningarna.

Om du vill inaktivera innehållsförtroende för ditt register navigerar du till registret i Azure-portalen. Under **Principer väljer** du **Innehållsförtroende**  >  **inaktiverat**  >  **Spara.** Du får en varning om förlust av alla signaturer i registret. Välj **OK** om du vill ta bort alla signaturer i registret permanent.

![Inaktivera innehållsförtroende för ett register i Azure-portalen][content-trust-03-portal]

## <a name="next-steps"></a>Nästa steg

* Se [Innehållsförtroende i Docker för][docker-content-trust] ytterligare information om innehållsförtroende, inklusive [kommandon för docker-förtroende](https://docs.docker.com/engine/reference/commandline/trust/) och [förtroendedelegeringar.](https://docs.docker.com/engine/security/trust/trust_delegation/) Den här artikeln berör flera viktiga punkter, men innehållsförtroende är ett omfattande ämne och beskrivs mer ingående i Docker-dokumentationen.

* I [dokumentationen om Azure Pipelines](/azure/devops/pipelines/build/content-trust) finns ett exempel på hur du använder innehållsförtroende när du skapar och push-överför en Docker-avbildning.

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-content-trust-update]: /cli/azure/acr/config/content-trust#az_acr_config_content_trust_update
