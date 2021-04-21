---
title: Felreferens för hälsokontroller i registret
description: Felkoder och möjliga lösningar på problem som hittas genom att köra kommandot az acr check-health diagnostic i Azure Container Registry
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: f9716c29093ae58518bc86ec06af40522d49047c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773449"
---
# <a name="health-check-error-reference"></a>Referens för hälsokontrollfel

Nedan visas information om felkoder som returneras av [kommandot az acr check-health.][az-acr-check-health] För varje fel visas möjliga lösningar.

Information om hur du `az acr check-healh` kör finns i Kontrollera [hälsotillståndet för ett Azure-containerregister.](container-registry-check-health.md)

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Det här felet innebär att Det gick inte att hitta Docker-klienten för CLI. Därför körs inte följande ytterligare kontroller: hitta Docker-version, utvärdera Docker-daemonstatus och köra ett Docker-pull-kommando.

*Potentiella lösningar:* Installera Docker-klienten; lägg till Docker-sökväg till systemvariablerna.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Det här felet innebär att Status för Docker-daemon inte är tillgänglig eller att det inte gick att nå den med hjälp av CLI. Därför är Docker-åtgärder (till exempel `docker login` och ) inte tillgängliga via `docker pull` CLI.

*Potentiella lösningar:* Starta om Docker-daemon eller verifiera att den är korrekt installerad.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Det här felet innebär att CLI inte kunde köra kommandot `docker --version` .

*Potentiella lösningar:* Prova att köra kommandot manuellt, kontrollera att du har den senaste CLI-versionen och undersök felmeddelandet.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Det här felet innebär att CLI inte kunde hämta en exempelavbildning till din miljö.

*Potentiella lösningar:* Verifiera att alla komponenter som behövs för att hämta en avbildning körs korrekt.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Det här felet innebär att Helm-klienten inte kunde hittas av CLI, vilket utesluter andra Helm-åtgärder.

*Potentiella lösningar:* Kontrollera att Helm-klienten är installerad och att dess sökväg har lagts till i systemmiljövariablerna.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Det här felet innebär att CLI inte kunde fastställa vilken Helm-version som är installerad. Detta kan inträffa om Den Azure CLI-version (eller om Helm-versionen) som används är föråldrad.

*Potentiella lösningar:* Uppdatera till den senaste Azure CLI-versionen eller till den rekommenderade Helm-versionen; kör kommandot manuellt och undersök felmeddelandet.

## <a name="cmk_error"></a>CMK_ERROR

Det här felet innebär att registret inte kan komma åt den användartilldeade eller sysem-tilldelade hanterade identiteten som används för att konfigurera registerkryptering med en kund hanterad nyckel. Den hanterade identiteten kan ha tagits bort.  

*Potentiell lösning:* Information om hur du löser problemet och roterar nyckeln med en annan hanterad identitet finns i stegen för att felsöka [den användar tilldelade identiteten](container-registry-customer-managed-keys.md#troubleshoot).

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Det här felet innebär att DNS för den angivna registerinloggningsservern pingades men inte svarade, vilket innebär att den inte är tillgänglig. Detta kan tyda på vissa anslutningsproblem. Alternativt kanske registret inte finns, användaren kanske inte har behörighet för registret (för att hämta sin inloggningsserver korrekt) eller så finns målregistret i ett annat moln än det som används i Azure CLI.

*Potentiella lösningar:* Verifiera anslutning; kontrollera stavningen i registret och att registret finns; kontrollera att användaren har rätt behörigheter för den och att registrets moln är samma som används i Azure CLI.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Det här felet innebär att utmaningsslutpunkten för det angivna registret svarade med statusen 403 Förbjuden HTTP. Det här felet innebär att användarna inte har åtkomst till registret, troligen på grund av en konfiguration för virtuellt nätverk eller på grund av att åtkomst till registrets offentliga slutpunkt inte tillåts. Om du vill se de konfigurerade brandväggsreglerna kör du `az acr show --query networkRuleSet --name <registry>` .

*Potentiella lösningar:* Ta bort regler för virtuellt nätverk eller lägg till den aktuella klientens IP-adress i listan över tillåtna.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Det här felet innebär att utmaningsslutpunkten för målregistret inte gav något problem.

*Potentiella lösningar:* Försök igen efter en stund. Om felet kvarstår öppnar du ett ärende på https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Det här felet innebär att utmaningsslutpunkten för målregistret utfärdade en utmaning, men registret stöder inte Azure Active Directory autentisering.

*Potentiella lösningar:* Prova ett annat sätt att autentisera, till exempel med administratörsautentiseringsuppgifter. Om användarna behöver autentisera med hjälp Azure Active Directory kan du öppna ett ärende på https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Det här felet innebär att inloggningsservern i registret inte svarade med en uppdateringstoken, så åtkomsten till målregistret nekades. Det här felet kan inträffa om användaren inte har rätt behörigheter i registret eller om autentiseringsuppgifterna för Azure CLI är inaktuella.

*Potentiella lösningar:* Kontrollera om användaren har rätt behörigheter för registret; kör `az login` för att uppdatera behörigheter, token och autentiseringsuppgifter.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Det här felet innebär att inloggningsservern i registret inte svarade med en åtkomsttoken, så att åtkomsten till målregistret nekades. Det här felet kan inträffa om användaren inte har rätt behörigheter i registret eller om autentiseringsuppgifterna för Azure CLI är inaktuella.

*Potentiella lösningar:* Kontrollera om användaren har rätt behörigheter för registret; kör `az login` för att uppdatera behörigheter, token och autentiseringsuppgifter.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Det här felet innebär att klienten inte kunde upprätta en säker anslutning till containerregistret. Det här felet inträffar vanligtvis om du kör eller använder en proxyserver.

*Potentiella lösningar:* Mer information om hur du arbetar bakom en [proxyserver finns här.](/cli/azure/use-cli-effectively)

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Det här felet innebär att CLI inte kunde hitta inloggningsservern för det angivna registret och inget standardsuffix hittades för det aktuella molnet. Det här felet kan inträffa om registret inte finns, om användaren inte har rätt behörigheter i registret, om registrets moln och det aktuella Azure CLI-molnet inte matchar, eller om Azure CLI-versionen är föråldrad.

*Potentiella lösningar:* Kontrollera att stavningen är korrekt och att registret finns; kontrollera att användaren har rätt behörigheter för registret och att molnen i registret och CLI-miljön matchar; uppdatera Azure CLI till den senaste versionen.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Det här felet innebär att CLI inte är kompatibelt med den installerade versionen av Docker/Notary. Prova att nedgradera din notary.exe version till en tidigare version än 0.6.0 genom att ersätta Docker-installationens Notary-klient manuellt för att lösa problemet.

## <a name="next-steps"></a>Nästa steg

Alternativ för att kontrollera hälsotillståndet för ett register finns i [Kontrollera hälsotillståndet för ett Azure-containerregister.](container-registry-check-health.md)

Se Vanliga [frågor](container-registry-faq.md) och svar för vanliga frågor och andra kända problem om Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
