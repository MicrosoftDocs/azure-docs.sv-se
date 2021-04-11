---
title: Vanliga frågor och svar
description: Svar på vanliga frågor som rör Azure Container Registry tjänsten
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: 5550c53289228f154fab485b4b7bbff17555aad7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045747"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Vanliga frågor och svar om Azure Container Registry

Den här artikeln handlar om vanliga frågor och kända problem med Azure Container Registry.

Fel söknings vägledning för registret finns i:
* [Felsöka inloggning av registret](container-registry-troubleshoot-login.md)
* [Felsöka nätverks problem med registret](container-registry-troubleshoot-access.md)
* [Felsöka register prestanda](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Resurshantering

- [Kan jag skapa ett Azure Container Registry med hjälp av en Resource Manager-mall?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Finns det någon säkerhets risk som söker efter avbildningar i ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hur gör jag för att konfigurera Kubernetes med Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hur gör jag för att få administratörsautentiseringsuppgifter för ett behållar register?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hur gör jag för att hämta autentiseringsuppgifter för administratör i en Resource Manager-mall?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Det går inte att ta bort replikeringen med statusen tillåts även om replikeringen tas bort med hjälp av Azure CLI eller Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Brand Väggs reglerna har uppdaterats, men de börjar inte gälla](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kan jag skapa en Azure Container Registry med hjälp av en Resource Manager-mall?

Ja. Här är [en mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) som du kan använda för att skapa ett register.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Finns det någon säkerhets risk som söker efter avbildningar i ACR?

Ja. Se dokumentationen från [Azure Security Center](../security-center/defender-for-container-registries-introduction.md), [twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) och [turkos](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hur gör jag för att konfigurera Kubernetes med Azure Container Registry?

Se dokumentationen för [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) och steg för [Azure Kubernetes-tjänsten](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hur gör jag för att få administratörsautentiseringsuppgifter för ett behållar register?

> [!IMPORTANT]
> Administratörs användar kontot har utformats för att en enskild användare ska kunna komma åt registret, främst i testnings syfte. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörs kontot med flera användare. Individuell identitet rekommenderas för användare och tjänst huvud namn för konsol lösta scenarier. Se [Översikt över autentisering](container-registry-authentication.md).

Innan du hämtar autentiseringsuppgifter för administratören kontrollerar du att registrets administratörs användare är aktive rad.

Så här hämtar du autentiseringsuppgifter med Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Använda Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hur gör jag för att hämta autentiseringsuppgifter för administratör i en Resource Manager-mall?

> [!IMPORTANT]
> Administratörs användar kontot har utformats för att en enskild användare ska kunna komma åt registret, främst i testnings syfte. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörs kontot med flera användare. Individuell identitet rekommenderas för användare och tjänst huvud namn för konsol lösta scenarier. Se [Översikt över autentisering](container-registry-authentication.md).

Innan du hämtar autentiseringsuppgifter för administratören kontrollerar du att registrets administratörs användare är aktive rad.

Så här hämtar du det första lösen ordet:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Så här hämtar du det andra lösen ordet:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Det går inte att ta bort replikeringen med statusen tillåts även om replikeringen tas bort med hjälp av Azure CLI eller Azure PowerShell

Felet visas när användaren har behörighet för ett register men inte har behörighet på läsar nivå för prenumerationen. Lös problemet genom att tilldela läsar behörighet för-prenumerationen till användaren:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Brand Väggs reglerna har uppdaterats, men de börjar inte gälla

Det tar lite tid att sprida ändringar av brand Väggs regeln. När du har ändrat brand Väggs inställningarna väntar du några minuter innan du verifierar ändringen.


## <a name="registry-operations"></a>Registeråtgärder

- [Hur gör jag för att Access Docker Registry HTTP API v2?](#how-do-i-access-docker-registry-http-api-v2)
- [Hur gör jag för att ta bort alla manifest som inte refereras till av någon tagg i en lagrings plats?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Varför minskar register kvot användningen inte när avbildningar tas bort?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hur gör jag för att verifiera lagrings kvot ändringar?](#how-do-i-validate-storage-quota-changes)
- [Hur gör jag för att autentisera med mitt register när du kör CLI i en behållare?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Hur aktiverar jag TLS 1,2?](#how-to-enable-tls-12)
- [Har Azure Container Registry stöd för innehålls förtroende?](#does-azure-container-registry-support-content-trust)
- [Hur gör jag för att bevilja du åtkomst till pull-eller push-avbildningar utan behörighet att hantera register resursen?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hur gör jag för att aktivera automatisk avbildnings karantän för ett register?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Hur gör jag för att aktivera anonym pull-åtkomst?](#how-do-i-enable-anonymous-pull-access)
- [Hur gör jag för att push-överförda ej distribuerbara lager till ett register?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hur gör jag för att Access Docker Registry HTTP API v2?

ACR stöder Docker Registry HTTP API v2. API: er kan nås på `https://<your registry login server>/v2/` . Exempel: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hur gör jag för att ta bort alla manifest som inte refereras till av någon tagg i en lagrings plats?

Om du använder Bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

För PowerShell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Obs: du kan lägga till `-y` i kommandot Ta bort för att hoppa över bekräftelsen.

Mer information finns i [ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Varför minskar register kvot användningen inte när avbildningar tas bort?

Den här situationen kan inträffa om underliggande lager fortfarande refereras till av andra behållar avbildningar. Om du tar bort en avbildning utan referenser uppdateras register användningen om några minuter.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hur gör jag för att verifiera lagrings kvot ändringar?

Skapa en avbildning med ett 1 GB lager med hjälp av följande Docker-fil. Detta säkerställer att avbildningen har ett lager som inte delas av någon annan bild i registret.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Bygg och skicka avbildningen till ditt register med hjälp av Docker CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Du bör kunna se att lagrings användningen har ökat i Azure Portal, eller så kan du fråga användningen med hjälp av CLI.

```azurecli
az acr show-usage -n myregistry
```

Ta bort avbildningen med hjälp av Azure CLI eller portalen och kontrol lera den uppdaterade användningen på några få minuter.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hur gör jag för att autentisera med mitt register när du kör CLI i en behållare?

Du måste köra Azure CLI-behållaren genom att montera Docker-socketen:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

I behållaren installerar du `docker` :

```bash
apk --update add docker
```

Autentisera sedan med ditt register:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Hur aktiverar jag TLS 1,2?

Aktivera TLS 1,2 med hjälp av en senaste docker-klient (version 18.03.0 och senare). 

> [!IMPORTANT]
> Från och med 13 januari 2020 kräver Azure Container Registry alla säkra anslutningar från servrar och program för att använda TLS 1,2. Stöd för TLS 1,0 och 1,1 kommer att dras tillbaka.

### <a name="does-azure-container-registry-support-content-trust"></a>Har Azure Container Registry stöd för innehålls förtroende?

Ja, du kan använda betrodda avbildningar i Azure Container Registry, eftersom [Docker-Notary](https://docs.docker.com/notary/getting_started/) har integrerats och kan aktive ras. Mer information finns [i innehålls förtroende i Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Var finns filen för tumavtrycket?

Under `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* Offentliga nycklar och certifikat för alla roller (förutom Delegerings roller) lagras i `root.json` .
* Offentliga nycklar och certifikat för Delegerings rollen lagras i JSON-filen för den överordnade rollen (till exempel `targets.json` för `targets/releases` rollen).

Vi rekommenderar att du verifierar dessa offentliga nycklar och certifikat efter den övergripande TUF-verifieringen som gjorts av Docker-och Notary-klienten.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hur gör jag för att bevilja du åtkomst till pull-eller push-avbildningar utan behörighet att hantera register resursen?

ACR stöder [anpassade roller](container-registry-roles.md) som ger olika behörighets nivåer. Specifikt, `AcrPull` och `AcrPush` roller gör det möjligt för användare att ta emot och/eller push-avbildningar utan behörighet att hantera register resursen i Azure.

* Azure Portal: ditt register-> Access Control (IAM)-> Lägg till (Välj `AcrPull` eller `AcrPush` för rollen).
* Azure CLI: hitta resurs-ID för registret genom att köra följande kommando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Sedan kan du tilldela `AcrPull` rollen eller `AcrPush` till en användare (följande exempel använder `AcrPull` ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Eller tilldela rollen till ett huvud namn för tjänsten som identifieras av dess program-ID:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Den tilldelas sedan kan autentisera och komma åt avbildningar i registret.

* För att autentisera till ett register:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Så här listar du databaser:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Hämta en avbildning:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Om du bara använder `AcrPull` `AcrPush` rollen eller, har den tilldelade tilldelas inte behörighet att hantera register resursen i Azure. Till exempel `az acr list` eller `az acr show -n myRegistry` visar inte registret.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hur gör jag för att aktivera automatisk avbildnings karantän för ett register?

Bild karantänen är för närvarande en förhands gransknings funktion i ACR. Du kan aktivera karantän läget i ett register så att endast de avbildningar som har klarat säkerhets genomsökningen visas för normala användare. Mer information finns i [ACR GitHub lagrings platsen](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Hur gör jag för att aktivera anonym pull-åtkomst?

Att konfigurera ett Azure Container Registry för anonym (oautentiserad) pull-åtkomst är för närvarande en för hands versions funktion som är tillgänglig på [tjänst nivåerna](container-registry-skus.md)standard och Premium. 

Om du vill aktivera anonym åtkomst, uppdatera ett register med Azure CLI (version 2.21.0 eller senare) och skicka `--anonymous-pull-enabled` parametern till kommandot [AZ ACR Update](/cli/azure/acr#az_acr_update) :

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

Du kan inaktivera anonym pull-åtkomst när som helst genom `--anonymous-pull-enabled` att ställa in på `false` .

> [!NOTE]
> * Innan du försöker utföra en anonym pull-åtgärd `docker logout` ska du köra för att se till att du rensar eventuella befintliga Docker-autentiseringsuppgifter.
> * Endast data Plans åtgärder är tillgängliga för oautentiserade klienter.
> * Registret kan begränsa en hög överföringshastighet av oautentiserade begär Anden.

> [!WARNING]
> Anonym pull-åtkomst gäller för alla databaser i registret. Om du hanterar lagrings plats åtkomst med hjälp av [lagrings](container-registry-repository-scoped-permissions.md)platser för databaser bör du vara medveten om att alla användare kan hämta från dessa databaser i ett register som är aktiverat för anonym hämtning. Vi rekommenderar att du tar bort tokens när anonym åtkomst har Aktiver ATS.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Hur gör jag för att push-överförda ej distribuerbara lager till ett register?

Ett icke-Distribuerbart lager i ett manifest innehåller en URL-parameter som innehållet kan hämtas från. En del möjliga användnings fall för att aktivera icke-distribuerbara lager push-meddelanden är för nätverks begränsade register, gapped register med begränsad åtkomst eller för register utan Internet anslutning.

Om du till exempel har NSG regler som har kon figurer ATS så att en virtuell dator bara kan hämta avbildningar från ditt Azure Container Registry, kommer Docker att ta emot problem för externa/icke-distribuerbara lager. En Windows Server Core-avbildning skulle till exempel innehålla referenser till externa lager till Azure Container Registry i manifestet och kan inte hämta i det här scenariot.

Så här aktiverar du push-överföring av icke-distribuerbara lager:

1. Redigera `daemon.json` filen, som finns i `/etc/docker/` på Linux-värdar och på `C:\ProgramData\docker\config\daemon.json` Windows Server. Anta att filen var tom tidigare, Lägg till följande innehåll:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > Värdet är en matris med register adresser, avgränsade med kommatecken.

2. Spara och avsluta filen.

3. Starta om Docker.

När du push-överför avbildningar till registren i listan skickas deras icke-distribuerbara lager till registret.

> [!WARNING]
> Icke-distribuerbara artefakter har vanligt vis begränsningar för hur och var de kan distribueras och delas. Använd endast den här funktionen för att skicka artefakter till privata register. Se till att du följer alla villkor som behandlar distribution av icke-distribuerbara artefakter.

## <a name="diagnostics-and-health-checks"></a>Diagnostik-och hälso kontroller

- [Kontrol lera hälsa med `az acr check-health`](#check-health-with-az-acr-check-health)
- [Docker-pull misslyckades med felet: net/http: begäran avbröts under väntan på anslutning (klienten. tids gränsen överskreds vid väntan på huvuden)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [Docker-push lyckades men Docker pull Miss lyckas med felet: obehörig: autentisering krävs](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` fungerar, men Docker-kommandon Miss lyckas med fel: obehörig: autentisering krävs](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Aktivera och hämta fel söknings loggarna i Docker daemon](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Nya användar behörigheter kanske inte träder i kraft omedelbart efter uppdateringen](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Autentiseringsinformation anges inte i rätt format för direkta REST API-anrop](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Varför visar Azure Portal inte alla mina databaser eller Taggar?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Varför kan Azure Portal inte hämta databaser eller Taggar?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Varför Miss tar pull-eller push-begäran med otillåten åtgärd?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Databas formatet är ogiltigt eller stöds inte](#repository-format-is-invalid-or-unsupported)
- [Hur gör jag för att samla in http-spårningar i Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Kontrol lera hälsa med `az acr check-health`

Information om fel sökning av vanliga problem med miljö och register finns i [kontrol lera hälso tillståndet för ett Azure Container Registry](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>Docker-pull misslyckades med felet: net/http: begäran avbröts under väntan på anslutning (klienten. tids gränsen överskreds vid väntan på huvuden)

 - Om det här felet är ett tillfälligt problem lyckas försök igen.
 - Om `docker pull` det Miss lyckas kontinuerligt kan det uppstå problem med Docker daemon. Problemet kan vanligt vis minimeras genom att du startar om Docker daemon. 
 - Om du fortsätter att se det här problemet efter att du har startat om Docker daemon, kan problemet vara problem med nätverks anslutningen på datorn. Kontrol lera om det allmänna nätverket på datorn är felfritt genom att köra följande kommando för att testa slut punkts anslutningen. Den lägsta `az acr` version som innehåller den här anslutnings kontroll kommandot är 2.2.9. Uppgradera Azure CLI om du använder en äldre version.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Du bör alltid ha en mekanism för återförsök på alla Docker-klient åtgärder.

### <a name="docker-pull-is-slow"></a>Docker pull är långsamt
Använd [det här](http://www.azurespeed.com/Azure/Download) verktyget för att testa nedladdnings hastigheten för dator nätverk. Om dator nätverket är långsamt bör du överväga att använda Azure VM i samma region som ditt register. Detta ger vanligt vis bättre nätverks hastighet.

### <a name="docker-push-is-slow"></a>Docker-push är långsam
Använd [det här](http://www.azurespeed.com/Azure/Upload) verktyget för att testa uppladdnings hastigheten för dator nätverk. Om dator nätverket är långsamt bör du överväga att använda Azure VM i samma region som ditt register. Detta ger vanligt vis bättre nätverks hastighet.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker-push lyckades men Docker pull Miss lyckas med felet: obehörig: autentisering krävs

Det här felet kan inträffa med Red Hat-versionen av Docker daemon, där `--signature-verification` är aktiverat som standard. Du kan kontrol lera Docker daemon-alternativen för Red Hat Enterprise Linux (RHEL) eller Fedora genom att köra följande kommando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Till exempel har Fedora 28-servern följande Docker daemon-alternativ:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Som `--signature-verification=false` saknar, `docker pull` Miss lyckas med ett fel som liknar:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Så här löser du felet:
1. Lägg till alternativet `--signature-verification=false` i konfigurations filen för Docker daemon `/etc/sysconfig/docker` . Exempel:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Starta om tjänsten Docker daemon genom att köra följande kommando:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Information om `--signature-verification` kan hittas genom att köra `man dockerd` .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>AZ ACR-inloggningen lyckades men Docker Miss lyckas med fel: obehörig: autentisering krävs

Se till att du använder en URL för gemener, till exempel, `docker push myregistry.azurecr.io/myimage:latest` även om register resurs namnet är versaler eller blandade fall, t `myRegistry` . ex..

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Aktivera och hämta fel söknings loggarna i Docker daemon    

Börja `dockerd` med `debug` alternativet. Först skapar du konfigurations filen för Docker daemon ( `/etc/docker/daemon.json` ) om den inte finns och lägger till `debug` alternativet:

```json
{    
    "debug": true    
}
```

Starta sedan om daemon. Till exempel med Ubuntu 14,04:

```bash
sudo service docker restart
```

Information finns i [Docker-dokumentationen](https://docs.docker.com/engine/admin/#enable-debugging).    

 * Loggarna kan genereras på olika platser, beroende på ditt system. För Ubuntu 14,04 är det till exempel `/var/log/upstart/docker.log` .    
Mer information finns i [Docker-dokumentationen](https://docs.docker.com/engine/admin/#read-the-logs) .    

 * För Docker för Windows genereras loggarna under% LOCALAPPDATA%/Docker/. Det kan dock innehålla all fel söknings information än.    

   För att få åtkomst till full daemon-loggen kan du behöva några extra steg:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Nu har du åtkomst till alla filer på den virtuella datorn som kör `dockerd` . Loggen är på `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nya användar behörigheter kanske inte träder i kraft omedelbart efter uppdateringen

När du tilldelar nya behörigheter (nya roller) till ett huvud namn för tjänsten kanske ändringen inte träder i kraft omedelbart. Det finns två möjliga orsaker:

* Azure Active Directory roll tilldelnings fördröjning. Normalt är det snabbt, men det kan ta minuter på grund av spridnings fördröjning.
* Behörighets fördröjning på ACR-token-Server. Detta kan ta upp till 10 minuter. Du kan åtgärda problemet `docker logout` och sedan autentisera igen med samma användare efter 1 minut:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR har inte stöd för att ta bort hemreplikering av användarna. Lösningen är att ta med hemreplikeringen skapa i mallen men hoppa över den genom att lägga till `"condition": false` enligt nedan:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Autentiseringsinformation anges inte i rätt format för direkta REST API-anrop

Du kan stöta `InvalidAuthenticationInfo` på ett fel, särskilt med `curl` verktyget med alternativet `-L` `--location` (för att följa omdirigeringar).
Till exempel hämtar bloben med `curl` `-L` alternativet with Basic Authentication:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

kan resultera i följande svar:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Rotor saken är att vissa `curl` implementeringar följer omdirigeringar med huvuden från den ursprungliga begäran.

För att lösa problemet måste du följa omdirigeringar manuellt utan sidhuvuden. Skriv ut svarshuvuden med `-D -` alternativet `curl` och extrahera sedan:- `Location` rubriken:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Varför visar Azure Portal inte alla mina databaser eller Taggar? 

Om du använder Microsoft Edge/IE-webbläsaren kan du se högst 100 databaser eller taggar. Om registret har fler än 100 databaser eller Taggar rekommenderar vi att du använder antingen Firefox-eller Chrome-webbläsaren för att lista dem alla.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Varför kan Azure Portal inte hämta databaser eller Taggar?

Webbläsaren kanske inte kan skicka begäran om att hämta databaser eller taggar till servern. Det kan finnas olika anledningar till att:

* Brist på nätverks anslutning
* Brandvägg
* Ad-blockerare
* DNS-fel

Kontakta nätverks administratören eller kontrol lera nätverks konfigurationen och din anslutning. Prova `az acr check-health -n yourRegistry` att köra med Azure CLI för att kontrol lera om din miljö kan ansluta till container Registry. Dessutom kan du också försöka med en Incognito eller privat session i webbläsaren för att undvika inaktuella webbläsares cacheminnen eller cookies.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Varför Miss tar pull-eller push-begäran med otillåten åtgärd?

Här följer några scenarier där åtgärder kan vara otillåtna:
* Klassiska register stöds inte längre. Uppgradera till en [tjänst nivå](./container-registry-skus.md) som stöds med hjälp av [AZ acr Update](/cli/azure/acr#az-acr-update) eller Azure Portal.
* Avbildningen eller databasen kanske är låst så att den inte kan tas bort eller uppdateras. Du kan använda kommandot [AZ ACR show databas](./container-registry-image-lock.md) för att visa aktuella attribut.
* Vissa åtgärder är inte tillåtna om bilden är i karantän. Lär dig mer om [karantän](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).
* Registret kan ha nått sin [lagrings gräns](container-registry-skus.md#service-tier-features-and-limits).

### <a name="repository-format-is-invalid-or-unsupported"></a>Databas formatet är ogiltigt eller stöds inte

Om du ser ett fel som "inte stödda databas format", "ogiltigt format" eller "de begärda data finns inte" när du anger ett databas namn i databas åtgärder, kontrollerar du stavningen och fallet för namnet. Giltiga lagrings namn får bara innehålla gemena alfanumeriska tecken, punkter, bindestreck, under streck och snedstreck. 

För fullständiga namngivnings regler för databaser, se [distributions specifikationen Open container Initiative](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Hur gör jag för att samla in http-spårningar i Windows?

#### <a name="prerequisites"></a>Förutsättningar

- Aktivera dekryptering av https i Fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Aktivera Docker att använda en proxy via Docker-gränssnittet: <https://docs.docker.com/docker-for-windows/#proxies>
- Var noga med att återställa när du är klar.  Docker fungerar inte med den här funktionen och Fiddler körs inte.

#### <a name="windows-containers"></a>Windows-containrar

Konfigurera Docker-proxy till 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux-containrar

Hitta IP-adressen för den virtuella växeln Docker VM:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurera Docker-proxyn till utdata från föregående kommando och port 8888 (till exempel 10.0.75.1:8888)

## <a name="tasks"></a>Uppgifter

- [Vill du avbryta körningen av batch Hur gör jag för att batch?](#how-do-i-batch-cancel-runs)
- [Hur gör jag för att inkludera mappen. git i AZ ACR build-kommandot?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Stöder aktiviteter GitLab för käll utlösare?](#does-tasks-support-gitlab-for-source-triggers)
- [Vilken hanterings tjänst för git-lagringsplats stöder aktiviteter?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Vill du avbryta körningen av batch Hur gör jag för att batch?

Följande kommandon avbryter alla aktiviteter som körs i det angivna registret.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hur gör jag för att inkludera mappen. git i AZ ACR build-kommandot?

Om du skickar en lokal källmapp till `az acr build` kommandot `.git` undantas mappen från det överförda paketet som standard. Du kan skapa en `.dockerignore` fil med följande inställning. Den meddelar kommandot att återställa alla filer under `.git` i det överförda paketet. 

`!.git/**`

Den här inställningen gäller även för `az acr run` kommandot.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Stöder aktiviteter GitLab för käll utlösare?

Vi stöder för närvarande inte GitLab för käll utlösare.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Vilken hanterings tjänst för git-lagringsplats stöder aktiviteter?

| Git-tjänst | Käll kontext | Manuell version | Automatisk generering via commit trigger |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Ja | Ja |
| Azure-lagringsplatser | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Ja | Ja |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Ja | Inga |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Ja | Inga |

## <a name="run-error-message-troubleshooting"></a>Köra fel meddelande fel sökning

| Felmeddelande | Felsökningsguide |
|---|---|
|Ingen åtkomst har kon figurer ATS för den virtuella datorn, därför hittades inga prenumerationer|Detta kan inträffa om du använder `az login --identity` i din ACR-uppgift. Detta är ett tillfälligt fel och inträffar när roll tilldelningen för din hanterade identitet inte har spridits. Vänta några sekunder innan du försöker igen.|

## <a name="cicd-integration"></a>CI/CD-integrering

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Nästa steg

* [Läs mer](container-registry-intro.md) om Azure Container Registry.
