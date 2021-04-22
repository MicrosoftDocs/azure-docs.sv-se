---
title: Vanliga frågor och svar
description: Svar på vanliga frågor om Azure Container Registry tjänsten
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: e5c855675990d6fd3ec97b839539acd843016a7d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864711"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Vanliga frågor och svar om Azure Container Registry

Den här artikeln tar upp vanliga frågor och kända problem med Azure Container Registry.

Information om felsökning av register finns i:
* [Felsöka registerinloggning](container-registry-troubleshoot-login.md)
* [Felsöka nätverksproblem med registret](container-registry-troubleshoot-access.md)
* [Felsöka registerprestanda](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Resurshantering

- [Kan jag skapa ett Azure-containerregister med en Resource Manager mall?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Finns det säkerhetsriskgenomsökning efter avbildningar i ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hur gör jag för att konfigurera Kubernetes med Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hur gör jag för att du administratörsautentiseringsuppgifter för ett containerregister?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hur gör jag för att du administratörsautentiseringsuppgifter i en Resource Manager mall?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Det går inte att ta bort replikeringen med statusen Förbjuden, även om replikeringen tas bort med hjälp av Azure CLI eller Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Brandväggsreglerna har uppdaterats men de har inte verkställts](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kan jag skapa en Azure Container Registry med en Resource Manager mall?

Ja. Här är [en mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) som du kan använda för att skapa ett register.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Finns det säkerhetsriskgenomsökning efter avbildningar i ACR?

Ja. Se dokumentationen från [Azure Security Center](../security-center/defender-for-container-registries-introduction.md) [,Lock och](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hur gör jag för att konfigurera Kubernetes med Azure Container Registry?

Se dokumentationen för [Kubernetes och](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) steg för [att Azure Kubernetes Service](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hur gör jag för att du administratörsautentiseringsuppgifter för ett containerregister?

> [!IMPORTANT]
> Administratörsanvändarkontot är utformat för att en enskild användare ska kunna komma åt registret, främst i testsyfte. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörskontot med flera användare. Enskilda identiteter rekommenderas för användare och tjänstens huvudnamn för scenarier utan huvudnamn. Se [Autentiseringsöversikt.](container-registry-authentication.md)

Kontrollera att registrets administratörsanvändare är aktiverad innan du hämtar administratörsautentiseringsuppgifter.

Så här hämtar du autentiseringsuppgifter med hjälp av Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Använda Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hur gör jag för att du administratörsautentiseringsuppgifter i en Resource Manager mall?

> [!IMPORTANT]
> Administratörsanvändarkontot är utformat för att en enskild användare ska kunna komma åt registret, främst i testsyfte. Vi rekommenderar inte att du delar autentiseringsuppgifterna för administratörskontot med flera användare. Enskilda identiteter rekommenderas för användare och tjänstens huvudnamn för scenarier utan huvudnamn. Se [Översikt över autentisering.](container-registry-authentication.md)

Kontrollera att registrets administratörsanvändare är aktiverad innan du hämtar administratörsautentiseringsuppgifter.

Så här hämtar du det första lösenordet:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Så här hämtar du det andra lösenordet:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Det går inte att ta bort replikeringen med statusen Förbjuden, men replikeringen tas bort med hjälp av Azure CLI eller Azure PowerShell

Felet visas när användaren har behörighet i ett register men inte har behörighet på läsarnivå för prenumerationen. Lös problemet genom att tilldela läsarbehörigheter för prenumerationen till användaren:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Brandväggsreglerna har uppdaterats men de har inte effekt

Det tar lite tid att sprida ändringar i brandväggsregeln. När du har ändrat brandväggsinställningarna väntar du några minuter innan du verifierar ändringen.


## <a name="registry-operations"></a>Registeråtgärder

- [Hur gör jag för att åtkomst till HTTP API V2 för Docker-registret?](#how-do-i-access-docker-registry-http-api-v2)
- [Hur gör jag för att bort alla manifest som inte refereras till av någon tagg på en lagringsplats?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Varför minskar inte kvoten för registret när avbildningar har tagits bort?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hur gör jag för att du ändringar i lagringskvoten?](#how-do-i-validate-storage-quota-changes)
- [Hur gör jag för att autentisera med mitt register när jag kör CLI i en container?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Hur aktiverar jag TLS 1.2?](#how-to-enable-tls-12)
- [Stöder Azure Container Registry Content Trust?](#does-azure-container-registry-support-content-trust)
- [Hur gör jag för att bevilja åtkomst för att hämta eller skicka avbildningar utan behörighet att hantera registerresursen?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hur gör jag för att aktivera automatisk avbildnings sätta i karantän för ett register?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Hur gör jag för att aktivera anonym hämtningsåtkomst?](#how-do-i-enable-anonymous-pull-access)
- [Hur gör jag för att skicka icke-distributablea lager till ett register?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hur gör jag för att åtkomst till HTTP API V2 för Docker-registret?

ACR stöder HTTP API V2 för Docker Registry. DU kan komma åt API:erna på `https://<your registry login server>/v2/` . Exempel: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hur gör jag för att bort alla manifest som inte refereras till av någon tagg på en lagringsplats?

Om du använder bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

För PowerShell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Obs! Du kan lägga till `-y` i borttagningskommandot för att hoppa över bekräftelsen.

Mer information finns i Ta [bort containeravbildningar i Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Varför minskar inte kvoten för registret när avbildningar har tagits bort?

Den här situationen kan inträffa om de underliggande lagren fortfarande refereras av andra containeravbildningar. Om du tar bort en avbildning utan referenser uppdateras användningen av registret inom några minuter.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hur gör jag för att att verifiera ändringar i lagringskvoten?

Skapa en avbildning med ett lager på 1 GB med hjälp av följande Docker-fil. Detta säkerställer att avbildningen har ett lager som inte delas av någon annan avbildning i registret.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Skapa och push-skicka avbildningen till registret med hjälp av Docker CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Du bör kunna se att lagringsanvändningen har ökat i Azure Portal, eller så kan du köra frågor mot användningen med hjälp av CLI.

```azurecli
az acr show-usage -n myregistry
```

Ta bort avbildningen med Hjälp av Azure CLI eller portalen och kontrollera den uppdaterade användningen på några minuter.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hur gör jag för att autentisera med mitt register när jag kör CLI i en container?

Du måste köra Azure CLI-containern genom att montera Docker-socketen:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Installera i `docker` containern:

```bash
apk --update add docker
```

Autentisera sedan med ditt register:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Hur aktiverar jag TLS 1.2?

Aktivera TLS 1.2 med valfri nyligen genomförd Docker-klient (version 18.03.0 och senare). 

> [!IMPORTANT]
> Från och med 13 januari 2020 Azure Container Registry alla säkra anslutningar från servrar och program att använda TLS 1.2. Stöd för TLS 1.0 och 1.1 kommer att dras tillbaka.

### <a name="does-azure-container-registry-support-content-trust"></a>Stöder Azure Container Registry Content Trust?

Ja, du kan använda betrodda avbildningar Azure Container Registry, eftersom [Docker Notary](https://docs.docker.com/notary/getting_started/) har integrerats och kan aktiveras. Mer information finns i [Innehållsförtroende i Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Var finns filen för tumavtrycket?

Under `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* Offentliga nycklar och certifikat för alla roller (utom delegeringsroller) lagras i `root.json` .
* Offentliga nycklar och certifikat för delegeringsrollen lagras i JSON-filen för dess överordnade roll (till `targets.json` exempel för `targets/releases` rollen).

Vi föreslår att du verifierar de offentliga nycklarna och certifikaten efter den övergripande TUF-verifieringen som gjorts av Docker- och Notary-klienten.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hur gör jag för att bevilja åtkomst för att hämta eller skicka avbildningar utan behörighet att hantera registerresursen?

ACR stöder [anpassade roller](container-registry-roles.md) som ger olika behörighetsnivåer. Mer specifikt `AcrPull` tillåter roller att användare hämtar `AcrPush` och/eller push-hämtar avbildningar utan behörighet att hantera registerresursen i Azure.

* Azure Portal: Ditt register -> Access Control (IAM) -> Lägg till `AcrPull` (Välj `AcrPush` eller för rollen).
* Azure CLI: Leta reda på registrets resurs-ID genom att köra följande kommando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Sedan kan du tilldela `AcrPull` rollen eller till en användare `AcrPush` (i följande exempel används `AcrPull` ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Eller tilldela rollen till ett huvudnamn för tjänsten som identifieras av dess program-ID:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Tilldelaren kan sedan autentisera och komma åt avbildningar i registret.

* Så här autentiserar du till ett register:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Så här listar du lagringsplatsen:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Så här hämtar du en avbildning:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Med enbart rollen eller har tilldelaren inte behörighet att `AcrPull` `AcrPush` hantera registerresursen i Azure. Till `az acr list` exempel, `az acr show -n myRegistry` eller visar inte registret.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hur gör jag för att aktivera automatisk avbildnings sätta i karantän för ett register?

Avbildningens karantän är för närvarande en förhandsgranskningsfunktion i ACR. Du kan aktivera karantänläget för ett register så att endast de avbildningar som har godkänt säkerhetsgenomsökningen visas för vanliga användare. Mer information finns i [ACR GitHub-lagringsplatsen](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Hur gör jag för att aktivera anonym hämtningsåtkomst?

Att konfigurera ett Azure-containerregister för anonym (oauthenticerad) pull-åtkomst är för närvarande en förhandsgranskningsfunktion som är tillgänglig på tjänstnivån Standard [och Premium.](container-registry-skus.md) 

Om du vill aktivera anonym pull-åtkomst uppdaterar du ett register med hjälp av Azure CLI (version 2.21.0 eller senare) och skickar parametern till `--anonymous-pull-enabled` [kommandot az acr update:](/cli/azure/acr#az_acr_update)

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

Du kan inaktivera anonym pull-åtkomst när som helst genom att ange `--anonymous-pull-enabled` till `false` .

> [!NOTE]
> * Innan du försöker utföra en anonym pull-åtgärd kör du `docker logout` för att se till att du rensar befintliga Docker-autentiseringsuppgifter.
> * Endast dataplansåtgärder är tillgängliga för oauthenticerade klienter.
> * Registret kan begränsa en hög frekvens av oauticerade begäranden.
> * För närvarande stöds inte anonym pull-åtkomst i [geo-replikerade](container-registry-geo-replication.md) registerregioner.

> [!WARNING]
> Anonym pull-åtkomst gäller för närvarande för alla lagringsplatsen i registret. Om du hanterar åtkomst till lagringsplatsen med token som är begränsade till [lagringsplatsen](container-registry-repository-scoped-permissions.md)bör du vara medveten om att alla användare kan hämta från dessa lagringsplatsen i ett register som är aktiverat för anonym pull. Vi rekommenderar att du tar bort token när anonym pull-åtkomst är aktiverat.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Hur gör jag för att skicka icke-distributable lager till ett register?

Ett icke-distributable lager i ett manifest innehåller en URL-parameter som innehåll kan hämtas från. Vissa möjliga användningsfall för att aktivera icke-distributable layer push är för nätverksbegränsade register, air-gapped register med begränsad åtkomst eller för register utan Internetanslutning.

Om du till exempel har NSG-regler konfigurerade så att en virtuell dator endast kan hämta avbildningar från ditt Azure-containerregister, hämtar Docker fel för externa/icke-distributable lager. En Windows Server Core-avbildning skulle till exempel innehålla referenser på främmande lager till Azure-containerregistret i manifestet och skulle inte kunna hämta i det här scenariot.

Så här aktiverar du push-av icke-distributable-lager:

1. Redigera filen `daemon.json` som finns i på Linux-värdar och på på Windows `/etc/docker/` `C:\ProgramData\docker\config\daemon.json` Server. Förutsatt att filen tidigare var tom lägger du till följande innehåll:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > Värdet är en matris med registeradresser avgränsade med kommatecken.

2. Spara och avsluta filen.

3. Starta om Docker.

När du push-pushar avbildningar till registren i listan skickas deras icke distributable lager till registret.

> [!WARNING]
> Icke-distributable artefakter har vanligtvis begränsningar för hur och var de kan distribueras och delas. Använd endast den här funktionen för att skicka artefakter till privata register. Se till att du efterlever alla villkor som täcker omfördelning av icke-distributable artefakter.

## <a name="diagnostics-and-health-checks"></a>Diagnostik- och hälsokontroller

- [Kontrollera hälsa med `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull misslyckas med felet: net/http: begäran avbröts i väntan på anslutning (Client.Timeout överskreds i väntan på huvuden)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push lyckas men docker pull misslyckas med fel: obehörig: autentisering krävs](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` lyckas, men Docker-kommandon misslyckas med felet: obehörig: autentisering krävs](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Aktivera och hämta felsökningsloggarna för Docker-daemon](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Nya användarbehörigheter kanske inte börjar gälla omedelbart efter uppdateringen](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Autentiseringsinformation anges inte i rätt format för direkta REST API anrop](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Varför visas Azure Portal inte alla mina lagringsplatsen eller taggarna?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Varför kan Azure Portal inte hämta lagringsplatsen eller taggar?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Varför misslyckas min pull- eller push-begäran med otillåten åtgärd?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Lagringsplatsens format är ogiltigt eller stöds inte](#repository-format-is-invalid-or-unsupported)
- [Hur gör jag för att samla in http-spårningar i Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Kontrollera hälsa med `az acr check-health`

Information om hur du felsöker vanliga miljö- och [registerproblem finns i Kontrollera hälsotillståndet för ett Azure-containerregister.](container-registry-check-health.md)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull misslyckas med felet: net/http: begäran avbröts i väntan på anslutning (Client.Timeout överskreds i väntan på huvuden)

 - Om det här felet är ett tillfälligt problem lyckas återförsöket.
 - Om `docker pull` misslyckas kontinuerligt kan det finnas ett problem med Docker-daemonen. Problemet kan vanligtvis åtgärdas genom att starta om Docker-daemonen. 
 - Om du fortsätter att se det här problemet när du har startat om Docker-daemonen kan problemet vara problem med nätverksanslutningen på datorn. Kontrollera om det allmänna nätverket på datorn är felfritt genom att köra följande kommando för att testa slutpunktsanslutningen. Den lägsta `az acr` version som innehåller det här anslutningskontrollkommandot är 2.2.9. Uppgradera Azure CLI om du använder en äldre version.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Du bör alltid ha en återförsöksmekanism för alla Docker-klientåtgärder.

### <a name="docker-pull-is-slow"></a>Docker-pull är långsam
Använd [det här](http://www.azurespeed.com/Azure/Download) verktyget för att testa datorns nedladdningshastighet för nätverket. Om datornätverket är långsamt bör du överväga att använda en virtuell Azure-dator i samma region som ditt register. Detta ger vanligtvis snabbare nätverkshastighet.

### <a name="docker-push-is-slow"></a>Docker-push är långsam
Använd [det här](http://www.azurespeed.com/Azure/Upload) verktyget för att testa datorns uppladdningshastighet i nätverket. Om datornätverket är långsamt bör du överväga att använda en virtuell Azure-dator i samma region som ditt register. Detta ger vanligtvis snabbare nätverkshastighet.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker-push lyckas men docker pull misslyckas med fel: obehörig: autentisering krävs

Det här felet kan inträffa med Red Hat-versionen av Docker-daemonen, `--signature-verification` där är aktiverat som standard. Du kan kontrollera Docker-daemonalternativen för Red Hat Enterprise Linux (RHEL) eller Fedora genom att köra följande kommando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Fedora 28 Server har till exempel följande docker-daemonalternativ:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Om `--signature-verification=false` det saknas misslyckas ett fel som liknar `docker pull` följande:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Så här löser du felet:
1. Lägg till alternativet `--signature-verification=false` i docker-daemonkonfigurationsfilen `/etc/sysconfig/docker` . Exempel:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Starta om Docker-daemontjänsten genom att köra följande kommando:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Information om `--signature-verification` finns genom att köra `man dockerd` .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login lyckas men docker misslyckas med felet: unauthorized: authentication required

Se till att du använder en server-URL med endast gemener, till exempel , även om namnet på registerresursen är `docker push myregistry.azurecr.io/myimage:latest` versaler eller blandat, till exempel `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Aktivera och hämta felsökningsloggarna för Docker-daemon    

Börja `dockerd` med `debug` alternativet . Skapa först konfigurationsfilen för Docker-daemon ( ) om `/etc/docker/daemon.json` den inte finns och lägg till `debug` alternativet:

```json
{    
    "debug": true    
}
```

Starta sedan om daemon. Till exempel med Ubuntu 14.04:

```bash
sudo service docker restart
```

Information finns i [Docker-dokumentationen.](https://docs.docker.com/engine/admin/#enable-debugging)    

 * Loggarna kan genereras på olika platser, beroende på systemet. För Ubuntu 14.04 är det till exempel `/var/log/upstart/docker.log` .    
Mer [information finns i Docker-dokumentationen.](https://docs.docker.com/engine/admin/#read-the-logs)    

 * För Docker för Windows genereras loggarna under %LOCALAPPDATA%/docker/. Den kanske dock inte innehåller all felsökningsinformation ännu.    

   För att få åtkomst till den fullständiga daemonloggen kan du behöva några extra steg:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Nu har du åtkomst till alla filer på den virtuella dator som kör `dockerd` . Loggen finns på `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nya användarbehörigheter kanske inte börjar gälla omedelbart efter uppdateringen

När du beviljar nya behörigheter (nya roller) till ett huvudnamn för tjänsten kanske ändringen inte börjar gälla omedelbart. Det finns två möjliga orsaker:

* Azure Active Directory fördröjd rolltilldelning. Normalt går det snabbt, men det kan ta några minuter på grund av spridningsfördröjningen.
* Behörighetsfördröjning på ACR-tokenserver. Det kan ta upp till 10 minuter. Du kan åtgärda detta genom `docker logout` att autentisera igen med samma användare efter 1 minut:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR stöder för närvarande inte borttagning av hemreplikering av användarna. Lösningen är att inkludera skapa hemreplikering i mallen men hoppa över skapandet genom att lägga till `"condition": false` det som visas nedan:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Autentiseringsinformation anges inte i rätt format vid direkt REST API anrop

Du kan stöta på `InvalidAuthenticationInfo` ett fel, särskilt när `curl` du använder verktyget med alternativet , `-L` `--location` (om du vill följa omdirigeringar).
Du kan till exempel hämta bloben med `curl` hjälp av med alternativ och grundläggande `-L` autentisering:

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

Rotorsaken är att vissa `curl` implementeringar följer omdirigeringar med huvuden från den ursprungliga begäran.

För att lösa problemet måste du följa omdirigeringar manuellt utan rubrikerna. Skriv ut svarshuvudena `-D -` med alternativet och extrahera `curl` sedan: `Location` sidhuvudet:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Varför visas Azure Portal inte alla mina lagringsplatsen eller taggarna? 

Om du använder webbläsaren Microsoft Edge/IE kan du se högst 100 databaser eller taggar. Om registret har fler än 100 lagringsplatsen eller taggar rekommenderar vi att du använder webbläsaren Firefox eller Chrome för att visa en lista över alla.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Varför kan Azure Portal inte hämta lagringsplatsen eller taggar?

Webbläsaren kanske inte kan skicka begäran om att hämta lagringsplatsen eller taggar till servern. Det kan finnas olika orsaker, till exempel:

* Brist på nätverksanslutning
* Brandvägg
* Annonsblockerare
* DNS-fel

Kontakta nätverksadministratören eller kontrollera nätverkskonfigurationen och anslutningen. Prova att `az acr check-health -n yourRegistry` köra med Hjälp av Azure CLI för att kontrollera om din miljö kan ansluta till Container Registry. Du kan också prova en inkognitosession eller privat session i webbläsaren för att undvika inaktuella webbläsarcacheminnen eller cookies.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Varför misslyckas min pull- eller push-begäran med otillåten åtgärd?

Här är några scenarier där åtgärder inte tillåts:
* Klassiska register stöds inte längre. Uppgradera till en tjänstnivå [som stöds](./container-registry-skus.md) med hjälp av [az acr update](/cli/azure/acr#az_acr_update) eller Azure Portal.
* Avbildningen eller lagringsplatsen kan låsas så att den inte kan tas bort eller uppdateras. Du kan använda kommandot [az acr show repository för](./container-registry-image-lock.md) att visa aktuella attribut.
* Vissa åtgärder tillåts inte om avbildningen är i karantän. Läs mer om [karantän.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)
* Registret kan ha nått [lagringsgränsen](container-registry-skus.md#service-tier-features-and-limits).

### <a name="repository-format-is-invalid-or-unsupported"></a>Lagringsplatsens format är ogiltigt eller stöds inte

Om du ser ett fel som "lagringsplatsens format som inte stöds", "ogiltigt format" eller "begärda data finns inte" när du anger ett lagringsplatsnamn i lagringsplatsåtgärder kontrollerar du stavningen och namnets fall. Giltiga lagringsplatsnamn får bara innehålla gemena alfanumeriska tecken, punkter, bindestreck, understreck och snedstreck. 

Fullständiga namngivningsregler för lagringsplatsen finns i [Open Container Initiative Distribution Specification](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Hur gör jag för att samla in http-spårningar i Windows?

#### <a name="prerequisites"></a>Förutsättningar

- Aktivera dekryptering av https i Fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Aktivera Docker för att använda en proxy via Docker-användargränssnittet: <https://docs.docker.com/docker-for-windows/#proxies>
- Se till att återställa när du är klar.  Docker fungerar inte med detta aktiverat och Fiddler körs inte.

#### <a name="windows-containers"></a>Windows-containrar

Konfigurera Docker-proxy till 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux-containrar

Hitta IP-adressen för den virtuella Docker vm-växeln:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Konfigurera Docker-proxyn till utdata från föregående kommando och port 8888 (till exempel 10.0.75.1:8888)

## <a name="tasks"></a>Uppgifter

- [Hur gör jag för att batch cancel-körningar?](#how-do-i-batch-cancel-runs)
- [Hur gör jag för att .git-mappen i kommandot az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Stöder tasks GitLab för källutlösare?](#does-tasks-support-gitlab-for-source-triggers)
- [Vilken git-lagringsplatshanteringstjänst stöder Tasks?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Hur gör jag för att batch cancel-körningar?

Följande kommandon avbryter alla aktiviteter som körs i det angivna registret.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hur gör jag för att .git-mappen i kommandot az acr build?

Om du skickar en lokal källmapp till `az acr build` kommandot `.git` undantas mappen från det uppladdade paketet som standard. Du kan skapa `.dockerignore` en fil med följande inställning. Den talar om för kommandot att återställa alla filer under `.git` i det uppladdade paketet. 

`!.git/**`

Den här inställningen gäller även för `az acr run` kommandot .

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Stöder tasks GitLab för källutlösare?

Vi stöder för närvarande inte GitLab för källutlösare.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Vilken git-lagringsplatshanteringstjänst har Tasks stöd för?

| Git-tjänst | Källkontext | Manuell version | Skapa automatiskt via commit-utlösare |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Ja | Ja |
| Azure-lagringsplatser | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Ja | Ja |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Ja | Inga |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Ja | Inga |

## <a name="run-error-message-troubleshooting"></a>Felsökning av körningsfelmeddelande

| Felmeddelande | Felsökningsguide |
|---|---|
|Ingen åtkomst har konfigurerats för den virtuella datorn, och därför hittades inga prenumerationer|Detta kan inträffa om du använder i `az login --identity` din ACR-uppgift. Det här är ett tillfälligt fel som inträffar när rolltilldelningen för din hanterade identitet inte har spridits. Väntar några sekunder innan återförsöket fungerar.|

## <a name="cicd-integration"></a>CI/CD-integrering

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Nästa steg

* [Läs mer](container-registry-intro.md) om Azure Container Registry.
