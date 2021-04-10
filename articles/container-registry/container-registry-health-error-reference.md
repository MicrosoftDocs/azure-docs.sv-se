---
title: Fel referens för register hälso kontroller
description: Felkoder och möjliga lösningar på problem som hittas genom att köra kommandot AZ ACR check-Health Diagnostic i Azure Container Registry
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: 05ae5a7ac19bb7748d5313ccb4974b639ab52d9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99061876"
---
# <a name="health-check-error-reference"></a>Fel referens för hälso kontroll

Nedan visas information om felkoder som returneras av kommandot [AZ ACR check-Health][az-acr-check-health] . För varje fel visas möjliga lösningar.

Information om hur `az acr check-healh` du kör finns i [kontrol lera hälso tillståndet för ett Azure Container Registry](container-registry-check-health.md).

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Det här felet innebär att Docker-klienten för CLI inte kunde hittas. Därför körs inte följande ytterligare kontroller: hitta Docker-version, utvärdera status för Docker daemon och köra ett Docker pull-kommando.

*Möjliga lösningar*: installera Docker-klienten; Lägg till Docker-sökvägen till systemvariablerna.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Det här felet innebär att status för Docker daemon inte är tillgänglig eller att den inte kan nås med hjälp av CLI. Därför är Docker-åtgärder (till exempel `docker login` och `docker pull` ) inte tillgängliga via cli.

*Möjliga lösningar*: starta om Docker daemon eller kontrol lera att det är korrekt installerat.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Det här felet innebär att CLI inte kunde köra kommandot `docker --version` .

*Möjliga lösningar*: Försök köra kommandot manuellt, kontrol lera att du har den senaste CLI-versionen och undersök fel meddelandet.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Det här felet innebär att CLI inte kunde hämta en exempel bild till din miljö.

*Möjliga lösningar*: kontrol lera att alla komponenter som krävs för att hämta en avbildning körs på rätt sätt.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Det här felet innebär att Helm-klienten inte kunde hittas av CLI, vilket förhindrar andra Helm-åtgärder.

*Möjliga lösningar*: kontrol lera att Helm-klienten är installerad och att dess sökväg läggs till i systemmiljövariabler.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Det här felet innebär att CLI inte kunde fastställa Helm-versionen som är installerad. Detta kan inträffa om Azure CLI-versionen (eller om Helm-versionen) används är föråldrad.

*Möjliga lösningar*: uppdatera till den senaste versionen av Azure CLI eller till den rekommenderade Helm-versionen; Kör kommandot manuellt och undersök fel meddelandet.

## <a name="cmk_error"></a>CMK_ERROR

Det här felet innebär att registret inte har åtkomst till den tilldelade användaren eller sysem hanterade identitet som används för att konfigurera register kryptering med en kundhanterad nyckel. Den hanterade identiteten kan ha tagits bort.  

*Potentiell lösning*: Lös problemet och rotera nyckeln med en annan hanterad identitet, se steg för att felsöka [den tilldelade identiteten](container-registry-customer-managed-keys.md#troubleshoot).

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Det här felet innebär att DNS för den registrerade inloggnings servern för registret skickades ping, men inte svarade, vilket innebär att den inte är tillgänglig. Detta kan tyda på problem med anslutningen. Det kan också hända att registret inte finns, användaren kanske inte har behörighet för registret (för att hämta sin inloggnings Server korrekt) eller också är mål registret i ett annat moln än det som används i Azure CLI.

*Möjliga lösningar*: verifiera anslutning; kontrol lera stavningen av registret och att registret finns. kontrol lera att användaren har rätt behörigheter och att registrets moln är detsamma som används i Azure CLI.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Det här felet innebär att utmanings slut punkten för det aktuella registret svarade med en 403-otillåten HTTP-status. Det här felet innebär att användarna inte har åtkomst till registret, förmodligen på grund av en virtuell nätverks konfiguration eller att åtkomst till registrets offentliga slut punkt inte är tillåten. Kör för att se de konfigurerade brand Väggs reglerna `az acr show --query networkRuleSet --name <registry>` .

*Möjliga lösningar*: ta bort regler för virtuella nätverk eller Lägg till den aktuella KLIENTens IP-adress i listan över tillåtna.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Det här felet innebär att anrops slut punkten för mål registret inte utfärdade någon utmaning.

*Möjliga lösningar*: försök igen om en stund. Om felet kvarstår öppnar du ett ärende på https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Det här felet innebär att anrops slut punkten för mål registret utfärdade en utmaning, men registret stöder inte Azure Active Directory autentisering.

*Möjliga lösningar*: försök med ett annat sätt att autentisera, till exempel med administratörs behörighet. Om användarna behöver autentisera med hjälp av Azure Active Directory öppnar du ett problem på https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Det här felet innebär att inloggnings servern för registret inte svarade med en uppdateringstoken, så åtkomst till mål registret nekades. Det här felet kan inträffa om användaren inte har rätt behörighet för registret eller om autentiseringsuppgifterna för Azure CLI är inaktuella.

*Möjliga lösningar*: kontrol lera om användaren har rätt behörighet för registret. kör `az login` för att uppdatera behörigheter, tokens och autentiseringsuppgifter.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Det här felet innebär att inloggnings servern för registret inte svarade med en åtkomsttoken, så att åtkomsten till mål registret nekades. Det här felet kan inträffa om användaren inte har rätt behörighet för registret eller om autentiseringsuppgifterna för Azure CLI är inaktuella.

*Möjliga lösningar*: kontrol lera om användaren har rätt behörighet för registret. kör `az login` för att uppdatera behörigheter, tokens och autentiseringsuppgifter.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Det här felet innebär att klienten inte kunde upprätta en säker anslutning till behållar registret. Det här felet uppstår vanligt vis om du kör eller använder en proxyserver.

*Möjliga lösningar*: Mer information om hur du arbetar bakom en proxy [finns här](/cli/azure/use-cli-effectively).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Det här felet innebär att CLI inte kunde hitta inloggnings servern för det aktuella registret, och inget standard-suffix hittades för det aktuella molnet. Det här felet kan uppstå om registret inte finns, om användaren inte har rätt behörighet för registret, om registrets moln och det aktuella Azure CLI-molnet inte matchar, eller om Azure CLI-versionen är föråldrad.

*Möjliga lösningar*: kontrol lera att stavningen är korrekt och att registret finns. kontrol lera att användaren har rätt behörigheter i registret och att molnen i registret och CLI-miljön matchar. Uppdatera Azure CLI till den senaste versionen.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Det här felet innebär att CLI inte är kompatibelt med den aktuella installerade versionen av Docker/Notary. Försök att nedgradera notary.exe-versionen till en tidigare version än 0.6.0 genom att ersätta Notary-klienten för Docker-installationen manuellt för att lösa problemet.

## <a name="next-steps"></a>Nästa steg

Information om alternativ för att kontrol lera hälso tillståndet för ett register finns i [kontrol lera hälso tillståndet för ett Azure Container Registry](container-registry-check-health.md).

Se vanliga frågor och [svar](container-registry-faq.md) om vanliga frågor och andra kända problem med Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
