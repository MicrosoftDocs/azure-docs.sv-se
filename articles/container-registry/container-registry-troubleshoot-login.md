---
title: Felsöka inloggning till registret
description: Symptom, orsaker och lösningar på vanliga problem vid inloggning i ett Azure-containerregister
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 47186cc8256836e5367ecee520787b67662eb42f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780739"
---
# <a name="troubleshoot-registry-login"></a>Felsöka registerinloggning

Den här artikeln hjälper dig att felsöka problem som kan uppstå när du loggar in på ett Azure-containerregister. 

## <a name="symptoms"></a>Symtom

Kan innehålla ett eller flera av följande:

* Det går inte att logga in på registret `docker login` med hjälp av , eller `az acr login` båda
* Det går inte att logga in på registret och du får `unauthorized: authentication required` felmeddelandet eller `unauthorized: Application not registered with AAD`
* Det går inte att logga in på registret och du får ett Azure CLI-fel `Could not connect to the registry login server`
* Det går inte att skicka eller hämta avbildningar och du får Docker-fel `unauthorized: authentication required`
* Det går inte att komma åt registret Azure Kubernetes Service, Azure DevOps eller någon annan Azure-tjänst
* Det går inte att komma åt registret och du får `Error response from daemon: login attempt failed with status: 403 Forbidden` felmeddelandet – [se Felsöka nätverksproblem med registret](container-registry-troubleshoot-access.md)
* Det går inte att komma åt eller visa registerinställningar i Azure Portal eller hantera registret med hjälp av Azure CLI

## <a name="causes"></a>Orsaker

* Docker har inte konfigurerats korrekt i din miljö – [lösningen](#check-docker-configuration)
* Registret finns inte eller så är namnet felaktigt – [lösning](#specify-correct-registry-name)
* Autentiseringsuppgifterna för registret är inte giltiga – [lösning](#confirm-credentials-to-access-registry)
* Autentiseringsuppgifterna har inte behörighet för push-, pull- eller Azure Resource Manager åtgärder – [lösning](#confirm-credentials-are-authorized-to-access-registry)
* Autentiseringsuppgifterna har upphört att gälla – [lösning](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Ytterligare diagnos 

Kör kommandot [az acr check-health](/cli/azure/acr#az_acr_check_health) för att få mer information om hälsotillståndet för registermiljön och eventuellt åtkomst till ett målregister. Du kan till exempel diagnostisera Docker-konfigurationsfel eller Azure Active Directory inloggningsproblem. 

Kommandoexempel [finns i Kontrollera hälsotillståndet för ett Azure-containerregister.](container-registry-check-health.md) Om fel rapporteras granskar du [felreferensen och](container-registry-health-error-reference.md) följande avsnitt för att hitta rekommenderade lösningar.

Om du har problem med att använda registret wih Azure Kubernetes Service kör du [kommandot az aks check-acr](/cli/azure/aks#az_aks_check_acr) för att verifiera att registret är tillgängligt från AKS-klustret.

> [!NOTE]
> Vissa autentiserings- eller auktoriseringsfel kan också inträffa om det finns brandväggs- eller nätverkskonfigurationer som förhindrar registeråtkomst. Se [Felsöka nätverksproblem med registret](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Möjliga lösningar

### <a name="check-docker-configuration"></a>Kontrollera Docker-konfigurationen

De Azure Container Registry autentiseringsflödena kräver en lokal Docker-installation så att du kan autentisera med registret för åtgärder som att push-pusha och hämta avbildningar. Kontrollera att Docker CLI-klienten och daemonen (Docker Engine) körs i din miljö. Du behöver Docker-klientversion 18.03 eller senare.

Relaterade länkar:

* [Översikt över autentisering](container-registry-authentication.md#authentication-options)
* [Vanliga frågor och svar om Container Registry](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Ange rätt registernamn

När du `docker login` använder anger du det fullständiga inloggningsservernamnet för registret, till exempel *myregistry.azurecr.io*. Se till att du bara använder gemener. Exempel:

```console
docker login myregistry.azurecr.io
```

När du [använder az acr login](/cli/azure/acr#az_acr_login) med en Azure Active Directory-identitet loggar du först in på Azure [CLI](/cli/azure/authenticate-azure-cli)och anger sedan Azure-resursnamnet för registret. Resursnamnet är det namn som angavs när registret skapades, till exempel *myregistry* (utan domänsuffix). Exempel:

```azurecli
az acr login --name myregistry
```

Relaterade länkar:

* [az acr login lyckas men docker misslyckas med felet: unauthorized: authentication required](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)

### <a name="confirm-credentials-to-access-registry"></a>Bekräfta autentiseringsuppgifter för att få åtkomst till registret

Kontrollera giltigheten för de autentiseringsuppgifter som du använder för ditt scenario eller som har angetts av en registerägare. Några möjliga problem:

* Om du använder tjänstens huvudnamn för Active Directory ska du kontrollera att du använder rätt autentiseringsuppgifter i Active Directory-klienten:
  * Användarnamn – program-ID för tjänstens huvudnamn (kallas även *klient-ID)*
  * Lösenord – lösenord för tjänstens huvudnamn (kallas *även klienthemlighet)*
* Om du använder en Azure-tjänst som Azure Kubernetes Service eller Azure DevOps för att komma åt registret, bekräftar du registerkonfigurationen för din tjänst. 
* Om du körde med alternativet , som aktiverar registerinloggning utan att använda `az acr login` `--expose-token` Docker-daemonen, ser du till att du autentiserar med användarnamnet `00000000-0000-0000-0000-000000000000` .
* Om registret har konfigurerats för anonym [pull-åtkomst](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)kan befintliga Docker-autentiseringsuppgifter som lagrats från en tidigare Docker-inloggning förhindra anonym åtkomst. Kör `docker logout` innan du försöker utföra en anonym pull-åtgärd i registret.

Relaterade länkar:

* [Översikt över autentisering](container-registry-authentication.md#authentication-options)
* [Individuell inloggning med Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Logga in med tjänstens huvudnamn](container-registry-auth-service-principal.md)
* [Logga in med hanterad identitet](container-registry-authentication-managed-identity.md)
* [Logga in med token som är begränsad till lagringsplatsen](container-registry-repository-scoped-permissions.md)
* [Logga in med administratörskonto](container-registry-authentication.md#admin-account)
* [Felkoder för Autentisering och auktorisering i Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az_acr_login) reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Bekräfta att autentiseringsuppgifterna har behörighet att komma åt registret

Bekräfta registerbehörigheterna som är associerade med autentiseringsuppgifterna, till exempel Azure-rollen för att hämta avbildningar från registret eller rollen `AcrPull` `AcrPush` för att push-skicka avbildningar. 

Åtkomst till ett register i portalen eller registerhantering med hjälp av Azure CLI kräver minst rollen eller motsvarande behörigheter för `Reader` att utföra Azure Resource Manager åtgärder.

Om dina behörigheter nyligen har ändrats för att tillåta registeråtkomst via portalen kan du behöva prova en inkognitosession eller privat session i webbläsaren för att undvika inaktuella webbläsarcacheminnen eller cookies.

Du eller en registerägare måste ha tillräcklig behörighet i prenumerationen för att lägga till eller ta bort rolltilldelningar.

Relaterade länkar:

* [Roller och behörigheter i Azure – Azure Container Registry](container-registry-roles.md)
* [Logga in med token som är begränsad till lagringsplatsen](container-registry-repository-scoped-permissions.md)
* [Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen](../role-based-access-control/role-assignments-portal.md)
* [Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md)
* [Skapa en ny programhemlighet](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Autentiserings- och auktoriseringskoder för Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Kontrollera att autentiseringsuppgifterna inte har upphört att gälla

Token och Active Directory-autentiseringsuppgifter kan upphöra att gälla efter definierade perioder, vilket förhindrar registeråtkomst. För att aktivera åtkomst kan autentiseringsuppgifter behöva återställas eller återskapas.

* Om du använder en enskild AD-identitet, en hanterad identitet eller tjänstens huvudnamn för registerinloggning upphör AD-token att gälla efter 3 timmar. Logga in igen i registret.  
* Om du använder ett AD-tjänsthuvudnamn med en klienthemlighet som har upphört att gälla måste en prenumerationsägare eller kontoadministratör återställa autentiseringsuppgifterna eller generera ett nytt huvudnamn för tjänsten.
* Om du använder [en token som är begränsad till lagringsplatsen](container-registry-repository-scoped-permissions.md)kan en registerägare behöva återställa ett lösenord eller generera en ny token.

Relaterade länkar:

* [Återställa autentiseringsuppgifter för tjänstens huvudnamn](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset)
* [Återskapa tokenlösenord](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Individuell inloggning med Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Avancerad felsökning

Om [insamling av resursloggar](container-registry-diagnostics-audit-logs.md) är aktiverad i registret granskar du loggen ContainterRegistryLoginEvents. Den här loggen lagrar autentiseringshändelser och status, inklusive inkommande identitet och IP-adress. Fråga loggen efter [fel vid registerautentisering.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Relaterade länkar:

* [Loggar för diagnostisk utvärdering och granskning](container-registry-diagnostics-audit-logs.md)
* [Vanliga frågor och svar om Container Registry](container-registry-faq.md)
* [Bästa metoder för Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nästa steg

Om du inte löser problemet här kan du se följande alternativ.

* Andra felsökningsämnen för registret är:
  * [Felsöka nätverksproblem med registret](container-registry-troubleshoot-access.md)
  * [Felsöka registerprestanda](container-registry-troubleshoot-performance.md)
* [Supportalternativ för](https://azure.microsoft.com/support/community/) communityn
* [Microsoft Q&A](/answers/products/)
* [Öppna en supportbegäran](https://azure.microsoft.com/support/create-ticket/) – baserat på den information du anger kan en snabbdiagnostik köras för autentiseringsfel i registret