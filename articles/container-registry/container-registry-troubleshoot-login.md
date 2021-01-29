---
title: Felsöka inloggning till registret
description: Symptom, orsaker och lösningar på vanliga problem vid inloggning i ett Azure Container Registry
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 5deb1717cf3886d8ea9c021d92afa358946b16dc
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052086"
---
# <a name="troubleshoot-registry-login"></a>Felsöka inloggning av registret

Den här artikeln hjälper dig att felsöka problem som kan uppstå när du loggar in på ett Azure Container Registry. 

## <a name="symptoms"></a>Symtom

Kan innehålla ett eller flera av följande:

* Det går inte att logga in på registret med `docker login` , `az acr login` eller både och
* Det går inte att logga in på registret och du får ett fel meddelande `unauthorized: authentication required` eller `unauthorized: Application not registered with AAD`
* Det går inte att logga in på registret och du får Azure CLI-fel `Could not connect to the registry login server`
* Det går inte att push-överföra eller hämta bilder och du får Docker-fel `unauthorized: authentication required`
* Det går inte att komma åt registret från Azure Kubernetes service, Azure DevOps eller någon annan Azure-tjänst
* Det går inte att komma åt registret och du får ett fel meddelande `Error response from daemon: login attempt failed with status: 403 Forbidden` [om fel sökning av nätverks problem med registret](container-registry-troubleshoot-access.md)
* Det går inte att öppna eller Visa register inställningar i Azure Portal eller hantera registret med hjälp av Azure CLI

## <a name="causes"></a>Orsaker

* Docker har inte kon figurer ATS korrekt i din miljö [lösning](#check-docker-configuration)
* Registret finns inte eller också är namnet fel- [lösning](#specify-correct-registry-name)
* Autentiseringsuppgifterna för registret är inte giltiga – [lösning](#confirm-credentials-to-access-registry)
* Autentiseringsuppgifterna är inte auktoriserade för push-, pull-eller Azure Resource Manager- [lösningar](#confirm-credentials-are-authorized-to-access-registry)
* Autentiseringsuppgifterna har upphört att gälla- [lösning](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Ytterligare diagnos 

Kör kommandot [AZ ACR check-Health](/cli/azure/acr#az-acr-check-health) för att få mer information om hälso tillståndet för register miljön och eventuellt åtkomst till ett mål register. Du kan till exempel diagnostisera Docker-konfigurations fel eller Azure Active Directory inloggnings problem. 

Se [kontrol lera hälso tillståndet för ett Azure Container Registry](container-registry-check-health.md) för kommando exempel. Om fel rapporteras läser du [fel referensen](container-registry-health-error-reference.md) och följande avsnitt för rekommenderade lösningar.

Om du har problem med att använda registret wih Azure Kubernetes-tjänsten kör du kommandot [AZ AKS check-ACR](/cli/azure/aks#az_aks_check_acr) för att kontrol lera att registret är tillgängligt från AKS-klustret.

> [!NOTE]
> Vissa autentiserings-eller auktoriseringsfel kan också uppstå om det finns brand Väggs-eller nätverkskonfigurationer som förhindrar åtkomst till registret. Se [Felsöka nätverks problem med registret](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Möjliga lösningar

### <a name="check-docker-configuration"></a>Kontrol lera Docker-konfigurationen

De flesta Azure Container Registry autentiserings flöden kräver en lokal Docker-installation så att du kan autentisera med ditt register för åtgärder som att skicka och ta emot bilder. Bekräfta att Docker CLI-klienten och daemon (Docker Engine) körs i din miljö. Du behöver Docker-klient version 18,03 eller senare.

Relaterade länkar:

* [Översikt över autentisering](container-registry-authentication.md#authentication-options)
* [Vanliga frågor om container Registry](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Ange rätt register namn

När `docker login` du använder, anger du det fullständiga inloggnings Server namnet för registret, till exempel *myregistry.azurecr.io*. Se till att du bara använder små bokstäver. Exempel:

```console
docker login myregistry.azurecr.io
```

När du använder [AZ ACR-inloggning](/cli/azure/acr#az-acr-login) med en Azure Active Directory identitet måste du först [Logga in på Azure CLI](/cli/azure/authenticate-azure-cli)och sedan ange Azure-resurs namnet för registret. Resurs namnet är det namn som angavs när registret skapades, till exempel *registret* (utan domänsuffix). Exempel:

```azurecli
az acr login --name myregistry
```

Relaterade länkar:

* [AZ ACR-inloggningen lyckades men Docker Miss lyckas med fel: obehörig: autentisering krävs](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Bekräfta autentiseringsuppgifter för att få åtkomst till registret

Kontrol lera giltigheten för de autentiseringsuppgifter som du använder för ditt scenario, eller tillhandahölls av en register ägare. Möjliga problem:

* Om du använder ett Active Directory tjänstens huvud namn kontrollerar du att du använder rätt autentiseringsuppgifter i Active Directory klient organisationen:
  * Användar namn – program-ID för tjänstens huvud namn (kallas även *klient-ID*)
  * Lösen ord för tjänstens huvud namn (kallas även *klient hemlighet*)
* Om du använder en Azure-tjänst som Azure Kubernetes service eller Azure DevOps för att komma åt registret, bekräftar du register konfigurationen för din tjänst. 
* Om du körde `az acr login` med `--expose-token` alternativet, som aktiverar register inloggning utan att använda Docker daemon, måste du se till att du autentiserar med användar namnet `00000000-0000-0000-0000-000000000000` .
* Om registret är konfigurerat för [Anonym](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)åtkomst kan befintliga Docker-autentiseringsuppgifter som lagras från en tidigare Docker-inloggning förhindra anonym åtkomst. Kör `docker logout` innan du försöker utföra en anonym pull-åtgärd i registret.

Relaterade länkar:

* [Översikt över autentisering](container-registry-authentication.md#authentication-options)
* [Individuell inloggning med Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Logga in med tjänstens huvud namn](container-registry-auth-service-principal.md)
* [Logga in med hanterad identitet](container-registry-authentication-managed-identity.md)
* [Logga in med lagringsplats-token](container-registry-repository-scoped-permissions.md)
* [Logga in med administratörs konto](container-registry-authentication.md#admin-account)
* [Fel koder för Azure AD-autentisering och-auktorisering](../active-directory/develop/reference-aadsts-error-codes.md)
* [AZ ACR-inloggnings](/cli/azure/acr#az-acr-login) referens

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Bekräfta autentiseringsuppgifter har behörighet att komma åt registret

Bekräfta de register behörigheter som är associerade med autentiseringsuppgifterna, till exempel Azure- `AcrPull` rollen för att hämta avbildningar från registret eller `AcrPush` rollen för att push-överföra avbildningar. 

Åtkomst till ett register i portalen eller register hanteringen med hjälp av Azure CLI kräver minst `Reader` rollen eller motsvarande behörigheter för att utföra Azure Resource Manager åtgärder.

Om dina behörigheter nyligen har ändrats för att tillåta åtkomst till registret via portalen kan du behöva försöka med en Incognito eller privat session i webbläsaren för att undvika inaktuella webbläsares cacheminnen eller cookies.

Du eller en register ägare måste ha tillräcklig behörighet i prenumerationen för att lägga till eller ta bort roll tilldelningar.

Relaterade länkar:

* [Azure-roller och-behörigheter – Azure Container Registry](container-registry-roles.md)
* [Logga in med lagringsplats-token](container-registry-repository-scoped-permissions.md)
* [Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen](../role-based-access-control/role-assignments-portal.md)
* [Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md)
* [Skapa en ny programhemlighet](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD-autentisering och auktoriseringsregler](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Kontrol lera att autentiseringsuppgifterna inte har upphört att gälla

Tokens och Active Directory autentiseringsuppgifter kan upphöra att gälla efter definierade perioder, vilket förhindrar register åtkomst. Om du vill aktivera åtkomst kan autentiseringsuppgifter behöva återställas eller återskapas.

* Om du använder en individuell AD-identitet, en hanterad identitet eller tjänstens huvud namn för register inloggning, förfaller AD-token efter 3 timmar. Logga in igen till registret.  
* Om du använder ett AD-tjänstens huvud namn med en utgången klient hemlighet måste en prenumerations ägare eller konto administratör återställa autentiseringsuppgifterna eller generera ett nytt huvud namn för tjänsten.
* Om du använder en [webbprogramsomfattande token](container-registry-repository-scoped-permissions.md)kan en register ägare behöva återställa ett lösen ord eller generera en ny token.

Relaterade länkar:

* [Återställ autentiseringsuppgifter för tjänstens huvud namn](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Återskapa lösen ord för token](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Individuell inloggning med Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Avancerad felsökning

Om [samlingen av resurs loggar](container-registry-diagnostics-audit-logs.md) är aktive rad i registret, granskar du ContainterRegistryLoginEvents-loggen. Den här loggen lagrar autentiserings händelser och status, inklusive inkommande identitet och IP-adress. Fråga [efter autentiseringsfel i loggen.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Relaterade länkar:

* [Loggar för diagnostisk utvärdering och granskning](container-registry-diagnostics-audit-logs.md)
* [Vanliga frågor om container Registry](container-registry-faq.md)
* [Bästa metoder för Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nästa steg

Om du inte löser problemet här kan du läsa följande alternativ.

* Andra fel söknings avsnitt för registret innehåller:
  * [Felsöka nätverks problem med registret](container-registry-troubleshoot-access.md)
  * [Felsöka register prestanda](container-registry-troubleshoot-performance.md)
* Alternativ för [Community-support](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A](/answers/products/)
* [Öppna en support ärende](https://azure.microsoft.com/support/create-ticket/) baserad på information som du anger, en snabb diagnostik kan köras för autentiseringsfel i registret