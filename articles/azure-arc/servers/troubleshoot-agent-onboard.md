---
title: Felsöka Azure Arc-aktiverade serveragentanslutningsproblem
description: Den här artikeln beskriver hur du felsöker och löser problem med connected machine-agenten som uppstår med Azure Arc-aktiverade servrar när du försöker ansluta till tjänsten.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498007"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Felsöka Azure Arc-aktiverade serveragentanslutningsproblem

Den här artikeln innehåller information om felsökning och lösning av problem som kan uppstå när du försöker konfigurera Azure Arc-aktiverade servrar Connected Machine-agenten för Windows eller Linux. Både de interaktiva installationsmetoderna och installationsmetoderna i stor skala när du konfigurerar anslutningen till tjänsten ingår. Allmän information finns i Översikt [över Arc-aktiverade servrar.](./overview.md)

## <a name="agent-error-codes"></a>Agentfelkoder

Om du får ett felmeddelande när du konfigurerar Azure Arc-aktiverade serveragenten kan följande tabell hjälpa dig att identifiera den sannolika orsaken och de föreslagna stegen för att lösa problemet. Du behöver ("0000" kan vara valfri 4-siffrig nummer) felkod som skrivs ut till konsolen eller `AZCM0000` skriptutdata för att fortsätta.

| Felkod | Trolig orsak | Föreslagen reparation |
|------------|----------------|-----------------------|
| AZCM0000 | Åtgärden lyckades | Ej tillämpligt |
| AZCM0001 | Ett okänt fel inträffade | Kontakta Microsoft Support om du vill ha mer hjälp |
| AZCM0011 | Användaren avbröt åtgärden (CTRL+ C) | Försök igen med föregående kommando |
| AZCM0012 | Den angivna åtkomsttoken är ogiltig | Hämta en ny åtkomsttoken och försök igen |
| AZCM0013 | De angivna taggarna är ogiltiga | Kontrollera att taggarna omges av dubbla citattecken, avgränsade med kommatecken, och att alla namn eller värden med blanksteg omges av enkla citattecken: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | Molnet är ogiltigt | Ange ett moln som stöds: `AzureCloud` eller `AzureUSGovernment` |
| AZCM0015 | Det angivna korrelations-ID:t är inte ett giltigt GUID | Ange ett giltigt GUID för `--correlation-id` |
| AZCM0016 | En obligatorisk parameter saknas | Granska utdata för att identifiera vilka parametrar som saknas |
| AZCM0017 | Resursnamnet är ogiltigt | Ange ett namn som endast använder alfanumeriska tecken, bindestreck och/eller understreck. Namnet får inte sluta med ett bindestreck eller understreck. |
| AZCM0018 | Kommandot kördes utan administratörsbehörighet | Försök igen med administratörs- eller rotbehörigheter i en upphöjd kommandotolk eller konsolsession. |
| AZCM0041 | De angivna autentiseringsuppgifterna är ogiltiga | För enhetsinloggningar kontrollerar du att det angivna användarkontot har åtkomst till klientorganisationen och prenumerationen där serverresursen kommer att skapas. För inloggningar med tjänstens huvudnamn kontrollerar du klient-ID och hemlighet för korrekthet, utgångsdatum för hemligheten och att tjänstens huvudnamn kommer från samma klientorganisation där serverresursen skapas. |
| AZCM0042 | Det gick inte att skapa den Arc-aktiverade serverresursen | Kontrollera att det angivna användar-/tjänsthuvudnamnet har åtkomst till att skapa Arc-aktiverade serverresurser i den angivna resursgruppen. |
| AZCM0043 | Det gick inte att ta bort den Arc-aktiverade serverresursen | Kontrollera att användaren/tjänstens huvudnamn har åtkomst till att ta bort Arc-aktiverade serverresurser i den angivna resursgruppen. Om resursen inte längre finns i Azure använder du flaggan `--force-local-only` för att fortsätta. |
| AZCM0044 | Det finns redan en resurs med samma namn | Ange ett annat namn för `--resource-name` parametern eller ta bort den befintliga Arc-aktiverade servern i Azure och försök igen. |
| AZCM0061 | Det går inte att nå agenttjänsten | Kontrollera att du kör kommandot i en upphöjd användarkontext (administratör/rot) och att HIMDS-tjänsten körs på servern. |
| AZCM0062 | Ett fel uppstod vid anslutning till servern | Granska andra felkoder i utdata för mer specifik information. Om felet uppstod efter att Azure-resursen skapades måste du ta bort Arc-servern från resursgruppen innan du försöker igen. |
| AZCM0063 | Ett fel uppstod vid frånkoppling av servern | Granska andra felkoder i utdata för mer specifik information. Om du fortsätter att stöta på det här felet kan du ta bort resursen i Azure och sedan köra på `azcmagent disconnect --force-local-only` servern för att koppla från agenten. |
| AZCM0064 | Agenttjänsten svarar inte | Kontrollera statusen för `himds` tjänsten för att se till att den körs. Starta tjänsten om den inte körs. Om den körs väntar du en minut och försöker sedan igen. |
| AZCM0065 | Ett internt agentkommunikationsfel inträffade | Kontakta Microsoft Support om du behöver hjälp |
| AZCM0066 | Agentwebbtjänsten svarar inte eller är inte tillgänglig | Kontakta Microsoft Support om du behöver hjälp |
| AZCM0067 | Agenten är redan ansluten till Azure | Följ stegen i Koppla [från agenten](manage-agent.md#unregister-machine) först och försök sedan igen. |
| AZCM0068 | Ett internt fel inträffade vid frånkoppling av servern från Azure | Kontakta Microsoft Support om du behöver hjälp |
| AZCM0081 | Ett fel uppstod vid nedladdning av Azure Active Directory certifikat för hanterad identitet | Om det här meddelandet påträffas vid försök att ansluta servern till Azure kan agenten inte kommunicera med Azure Arc tjänsten. Ta bort resursen i Azure och försök att ansluta igen. |
| AZCM0101 | Kommandot har inte parsats | Kör `azcmagent <command> --help` för att granska rätt kommandosyntax |
| AZCM0102 | Det går inte att hämta datorns värdnamn | Kör `hostname` för att söka efter felmeddelanden på systemnivå och kontakta Microsoft Support. |
| AZCM0103 | Ett fel uppstod vid generering av RSA-nycklar | Kontakta Microsoft Support om du behöver hjälp |
| AZCM0104 | Det gick inte att läsa systeminformation | Kontrollera att identiteten som används `azcmagent` för att köra har administratörs-/rotbehörighet på systemet och försök igen. |

## <a name="agent-verbose-log"></a>Utförlig agentlogg

Innan du följer felsökningsstegen som beskrivs senare i den här artikeln är den minsta information du behöver den utförliga loggen. Den innehåller utdata från **azcmagent tool-kommandon** när det utförliga argumentet (-v) används. Loggfilerna skrivs till `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` för Windows och Linux till `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Följande är ett exempel på kommandot för att aktivera utförlig loggning med Connected Machine-agenten för Windows när du utför en interaktiv installation.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Följande är ett exempel på kommandot för att aktivera utförlig loggning med Connected Machine-agenten för Windows när du utför en skalenlig installation med hjälp av ett huvudnamn för tjänsten.

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

Följande är ett exempel på kommandot för att aktivera utförlig loggning med Connected Machine-agenten för Linux när du utför en interaktiv installation.

>[!NOTE]
>Du måste ha *rotåtkomstbehörigheter* på Linux-datorer för att köra **azcmagent**.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Följande är ett exempel på kommandot för att aktivera utförlig loggning med Connected Machine-agenten för Linux när du utför en skalenlig installation med hjälp av ett huvudnamn för tjänsten.

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

## <a name="agent-connection-issues-to-service"></a>Problem med agentanslutning till tjänsten

I följande tabell visas några av de kända felen och förslag på hur du felsöker och löser dem.

|Meddelande |Fel |Trolig orsak |Lösning |
|--------|------|---------------|---------|
|Det gick inte att hämta enhetsflödet för auktoriseringstoken |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Det går inte att nå `login.windows.net` slutpunkten | Verifiera anslutningen till slutpunkten. |
|Det gick inte att hämta enhetsflödet för auktoriseringstoken |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxyn eller brandväggen blockerar åtkomsten till `login.windows.net` slutpunkten. | Kontrollera anslutningen till slutpunkten och den blockeras inte av en brandvägg eller proxyserver. |
|Det gick inte att hämta enhetsflödet för auktoriseringstoken  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | grupprincip Object *Computer Configuration\ Administrativa mallar\ System\ User Profiles\* Delete user profiles older than a specified number of days on system restart is enabled. | Kontrollera att grupprincipobjektet är aktiverat och riktar in sig på den berörda datorn. Mer information finns i fotnot <sup>[1.](#footnote1)</sup> |
|Det gick inte att hämta auktoriseringstoken från SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy eller brandvägg blockerar åtkomsten till `login.windows.net` slutpunkten. |Verifiera anslutningen till slutpunkten och den blockeras inte av en brandvägg eller proxyserver. |
|Det gick inte att hämta auktoriseringstoken från SPN |`Invalid client secret is provided` |Fel eller ogiltig hemlighet för tjänstens huvudnamn. |Verifiera hemligheten för tjänstens huvudnamn. |
| Det gick inte att hämta auktoriseringstoken från SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Felaktigt huvudnamn för tjänsten och/eller klientorganisations-ID. |Kontrollera tjänstens huvudnamn och/eller klientorganisations-ID.|
|Hämta ARM-resurssvar |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Fel autentiseringsuppgifter och/eller behörigheter |Kontrollera att du eller tjänstens huvudnamn är medlem i **Azure Connected Machine Onboarding-rollen.** |
|Det gick inte att använda AzcmagentConnect ARM-resursen |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure-resursproviders är inte registrerade. |Registrera [resursproviders.](./agent-overview.md#register-azure-resource-providers) |
|Det gick inte att använda AzcmagentConnect ARM-resursen |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Proxyserver eller brandvägg blockerar åtkomsten till `management.azure.com` slutpunkten. |Verifiera anslutningen till slutpunkten och den blockeras inte av en brandvägg eller proxyserver. |

<a name="footnote1"></a><sup>1</sup> Om grupprincipobjektet är aktiverat och gäller för datorer med connected machine-agenten tas användarprofilen som är associerad med det inbyggda kontot som angetts för *tjänsten himds* bort. Därför tas även autentiseringscertifikatet som används för att kommunicera med tjänsten som cachelagras i det lokala certifikatarkivet i 30 dagar bort. Innan gränsen på 30 dagar görs ett försök att förnya certifikatet. Lös problemet genom att följa stegen för att [avregistrera datorn](manage-agent.md#unregister-machine) och sedan registrera den igen med tjänsten som kör `azcmagent connect` .

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem här eller om du inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Microsoft Q&A](/answers/topics/azure-arc.html).

* Anslut med [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure för att förbättra kundupplevelsen. Azure Support ansluter Azure-communityn till svar, support och experter.

* Skapa en azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/)och välj **Få support.**
