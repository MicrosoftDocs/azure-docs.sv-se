---
title: Felsök problem med Azure Arc-aktiverade servrars agent anslutning
description: Den här artikeln beskriver hur du felsöker och löser problem med den anslutna dator agent som uppstår med Azure Arc-aktiverade servrar när du försöker ansluta till tjänsten.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 36feb6a65ec52d99dfd664ae54cb099ea6a7e239
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900679"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Felsök problem med anslutning till anslutna dator agenter

Den här artikeln innehåller information om hur du felsöker och löser problem som kan uppstå vid försök att konfigurera Azure Arc-aktiverade servrar som är anslutna till dator agent för Windows eller Linux. Både interaktiva och storskaliga installations metoder när du konfigurerar anslutning till tjänsten ingår. Allmän information finns i [Översikt över Arc-aktiverade servrar](./overview.md).

## <a name="agent-verbose-log"></a>Utförlig logg för agent

Innan du följer fel söknings stegen som beskrivs längre fram i den här artikeln, är den minsta information som du behöver vara utförlig logg. Det innehåller utdata från kommandona för **azcmagent** , när argumentet verbose (-v) används. Loggfilerna skrivs till `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` för Windows och Linux till `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Följande är ett exempel på kommandot för att aktivera utförlig loggning med den anslutna dator agenten för Windows när du utför en interaktiv installation.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Följande är ett exempel på kommandot för att aktivera utförlig loggning med den anslutna dator agenten för Windows när du utför en storskalig installation med ett huvud namn för tjänsten.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Följande är ett exempel på kommandot för att aktivera utförlig loggning med den anslutna dator agenten för Linux när du utför en interaktiv installation.

>[!NOTE]
>Du måste ha *rot* åtkomst behörighet på Linux-datorer för att kunna köra **azcmagent**.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Följande är ett exempel på kommandot för att aktivera utförlig loggning med den anslutna dator agenten för Linux när du utför en storskalig installation med ett huvud namn för tjänsten.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problem med agent anslutning till tjänsten

I följande tabell visas några kända fel och förslag på hur du felsöker och löser dem.

|Meddelande |Fel |Trolig orsak |Lösning |
|--------|------|---------------|---------|
|Det gick inte att erhålla enhets flödet för autentiseringstoken |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Det går inte att komma åt `login.windows.net` slut punkten | Verifiera anslutningen till slut punkten. |
|Det gick inte att erhålla enhets flödet för autentiseringstoken |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxy eller brand vägg blockerar åtkomst till `login.windows.net` slut punkten. | Kontrol lera anslutningen till slut punkten och den blockeras inte av en brand vägg eller proxyserver. |
|Det gick inte att erhålla enhets flödet för autentiseringstoken  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Grupprincip objekt *dator konfiguration \ administrativa mallar \ system \ användar profiler \ ta bort användar profiler som är äldre än ett visst antal dagar efter omstart av systemet* har Aktiver ATS. | Kontrol lera att GRUPPRINCIPOBJEKTet är aktiverat och riktas mot den berörda datorn. Se fotnot <sup>[1](#footnote1)</sup> om du vill ha mer information. |
|Det gick inte att hämta autentiseringstoken från SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy eller brand vägg blockerar åtkomst till `login.windows.net` slut punkten. |Kontrol lera anslutningen till slut punkten och den blockeras inte av en brand vägg eller proxyserver. |
|Det gick inte att hämta autentiseringstoken från SPN |`Invalid client secret is provided` |Felaktig eller ogiltig hemlighet för tjänstens huvud namn. |Verifiera hemligheten för tjänstens huvud namn. |
| Det gick inte att hämta autentiseringstoken från SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Felaktigt tjänst huvud namn och/eller klient-ID. |Verifiera tjänstens huvud namn och/eller klient-ID: t.|
|Hämta resurs svar för ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Fel autentiseringsuppgifter och/eller behörigheter |Verifiera att du eller tjänstens huvud namn är medlem i rollen **Azure Connected Machine onboarding** . |
|Det gick inte att AzcmagentConnect ARM-resurs |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure Resource providers har inte registrerats. |Registrera [resurs leverantörer](./agent-overview.md#register-azure-resource-providers). |
|Det gick inte att AzcmagentConnect ARM-resurs |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Proxyservern eller brand väggen blockerar åtkomst till `management.azure.com` slut punkten. |Kontrol lera anslutningen till slut punkten och den blockeras inte av en brand vägg eller proxyserver. |

<a name="footnote1"></a><sup>1</sup> Om det här GRUPPRINCIPOBJEKTet är aktiverat och tillämpas på datorer med den anslutna dator agenten, tas användar profilen som är kopplad till det inbyggda kontot som angetts för *himds* -tjänsten bort. Därför tar det också bort autentiseringscertifikatet som används för att kommunicera med tjänsten som cachelagras i det lokala certifikat arkivet i 30 dagar. Före gränsen på 30 dagar görs ett försök att förnya certifikatet. Lös problemet genom att följa stegen för att [avregistrera datorn](manage-agent.md#unregister-machine) och sedan registrera den igen med tjänsten som körs `azcmagent connect` .

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Microsoft Q&A](/answers/topics/azure-arc.html).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Azure-support ansluter Azure-communityn till svar, support och experter.

* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.
