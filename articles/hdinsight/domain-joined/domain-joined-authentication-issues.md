---
title: Autentiseringsfel i Azure HDInsight
description: Autentiseringsfel i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 218850feea8b0e22b8e11695a3aa3c69173f1ab7
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504933"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Autentiseringsfel i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

I säkra kluster som backas upp av Azure Data Lake (gen1 eller Gen2), och när domän användare loggar in på kluster tjänsterna via HDI Gateway (som att logga in på Apache Ambari-portalen), försöker HDI Gateway hämta en OAuth-token från Azure Active Directory (Azure AD) först och sedan hämta en Kerberos-biljett från Azure AD DS. Autentiseringen kan inte utföras i någon av dessa steg. Den här artikeln syftar till att felsöka vissa av de här problemen.

När autentiseringen Miss lyckas får du en uppmaning om autentiseringsuppgifter. Om du avbryter den här dialog rutan skrivs fel meddelandet. Här följer några vanliga fel meddelanden:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant eller unauthorized_client 50126

### <a name="issue"></a>Problem

Inloggningen misslyckades för federerade användare med felkod 50126 (inloggningen lyckades för moln användare). Fel meddelandet ser ut ungefär så här:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Orsak

Azure AD-felkod 50126 innebär att `AllowCloudPasswordValidation` principen inte har angetts av klienten.

### <a name="resolution"></a>Lösning

Företags administratören för Azure AD-klienten bör göra det möjligt för Azure AD att använda lösen ords-hashar för ADFS-baserade användare.  Använd det `AllowCloudPasswordValidationPolicy` som visas i artikeln [Använd Enterprise Security Package i HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant eller unauthorized_client 50034

### <a name="issue"></a>Problem

Inloggningen Miss lyckas med felkoden 50034. Fel meddelandet ser ut ungefär så här:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Orsak

Användar namnet är felaktigt (finns inte). Användaren använder inte samma användar namn som används i Azure Portal.

### <a name="resolution"></a>Lösning

Använd samma användar namn som fungerar i portalen.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant eller unauthorized_client 50053

### <a name="issue"></a>Problem

Användar kontot är utelåst, felkod 50053. Fel meddelandet ser ut ungefär så här:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Orsak

För många inloggnings försök med ett felaktigt lösen ord.

### <a name="resolution"></a>Lösning

Vänta i 30 minuter eller så, stoppa alla program som kan försöka autentisera sig.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant eller unauthorized_client 50053

### <a name="issue"></a>Problem

Lösen ordet har upphört att gälla, felkod 50053. Fel meddelandet ser ut ungefär så här:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Orsak

Lösen ordet har upphört att gälla.

### <a name="resolution"></a>Lösning

Ändra lösen ordet i Azure Portal (på ditt lokala system) och vänta i 30 minuter innan synkroniseringen har upprättats.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problem

Fel meddelande visas `interaction_required` .

### <a name="cause"></a>Orsak

Principen för villkorsstyrd åtkomst eller MFA används för användaren. Eftersom interaktiv autentisering inte stöds ännu, behöver användaren eller klustret undantas från MFA/villkorsstyrd åtkomst. Om du väljer att undanta klustret (IP-baserad undantags princip) kontrollerar du att AD `ServiceEndpoints` är aktiverat för det virtuella nätverket.

### <a name="resolution"></a>Lösning

Använd principen för villkorlig åtkomst och undanta HDInsight-kluster från MFA som visas i [Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Inloggning nekad

### <a name="issue"></a>Problem

Inloggningen nekas.

### <a name="cause"></a>Orsak

För att komma till det här steget är OAuth-autentiseringen inte ett problem, men Kerberos-autentisering är. Om det här klustret backas upp av ADLS har OAuth-inloggningen lyckats innan Kerberos-autentiseringen försökte utföras. I WASB-kluster görs inte OAuth-inloggning. Det kan finnas många orsaker till Kerberos-problem, t. ex. lösen ords-hashar är osynkroniserade, användar kontot är utelåst i Azure AD DS och så vidare. Password hash-värden synkroniseras bara när användaren ändrar lösen ordet. När du skapar Azure AD DS-instansen kommer den att börja synkronisera lösen ord som ändras efter att de har skapats. Det synkroniserar inte retroaktivt lösen ord som angavs innan det skrevs.

### <a name="resolution"></a>Lösning

Om du tror att lösen ord kanske inte är synkroniserade kan du försöka med att ändra lösen ordet och vänta några minuter innan du synkroniserar.

Försök att använda SSH i en du måste försöka autentisera (kinit) med samma användarautentiseringsuppgifter, från en dator som är ansluten till domänen. SSH till noden Head/Edge med en lokal användare och kör sedan kinit.

---

## <a name="kinit-fails"></a>kinit Miss lyckas

### <a name="issue"></a>Problem

Kinit Miss lyckas.

### <a name="cause"></a>Orsak

Sig.

### <a name="resolution"></a>Lösning

För att kinit ska lyckas måste du känna till ditt `sAMAccountName` (detta är det korta konto namnet utan sfären). `sAMAccountName` är vanligt vis konto prefixet (t. ex. Bob i `bob@contoso.com` ). Det kan vara olika för vissa användare. Du behöver kunna bläddra/söka i katalogen för att lära dig mer `sAMAccountName` .

Sätt att hitta `sAMAccountName` :

* Om du kan logga in på Ambari med hjälp av den lokala Ambari-administratören tittar du i listan över användare.

* Om du har en domänansluten [Windows-dator](../../active-directory-domain-services/manage-domain.md)kan du bläddra genom att använda standard verktygen i Windows AD. Detta kräver ett fungerande konto i domänen.

* Från Head-noden kan du söka genom att använda SAMBA-kommandon. Detta kräver en giltig Kerberos-session (lyckad kinit). NET Ads search (userPrincipalName = Bob *) "

    Sök-/Browse-resultaten ska visa `sAMAccountName` attributet. Du kan också titta på andra attribut som `pwdLastSet` , `badPasswordTime` `userPrincipalName` osv. för att se om dessa egenskaper matchar det du förväntar dig.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit Miss lyckas med ett Förautentiserings fel

### <a name="issue"></a>Problem

Kinit Miss lyckas med `Preauthentication` fel.

### <a name="cause"></a>Orsak

Felaktigt användar namn eller lösen ord.

### <a name="resolution"></a>Lösning

Kontrol lera ditt användar namn och lösen ord. Sök även efter andra egenskaper som beskrivs ovan. Om du vill aktivera utförlig fel sökning kan du köra `export KRB5_TRACE=/tmp/krb.log` från sessionen innan du försöker kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Kommandot Job/HDFS Miss lyckas på grund av TokenNotFoundException

### <a name="issue"></a>Problem

Kommandot Job/HDFS Miss lyckas på grund av `TokenNotFoundException` .

### <a name="cause"></a>Orsak

Det gick inte att hitta den OAuth-åtkomsttoken som krävs för att jobbet/kommandot ska lyckas. Driv rutinen ADLS/ABFS kommer att försöka hämta OAuth-åtkomsttoken från Credential-tjänsten innan du gör lagrings förfrågningar. Den här token registreras när du loggar in på Ambari-portalen med samma användare.

### <a name="resolution"></a>Lösning

Se till att du har loggat in på Ambari-portalen en gång via det användar namn vars identitet används för att köra jobbet.

---

## <a name="error-fetching-access-token"></a>Fel vid hämtning av åtkomsttoken

### <a name="issue"></a>Problem

Användaren får ett fel meddelande `Error fetching access token` .

### <a name="cause"></a>Orsak

Det här felet uppstår tillfälligt när användare försöker komma åt ADLS Gen2 med hjälp av ACL: er och Kerberos-token har upphört att gälla.

### <a name="resolution"></a>Lösning

* För Azure Data Lake Storage Gen1 rensar du webbläsarens cache och loggar in på Ambari igen.

* För Azure Data Lake Storage Gen2 kör du `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` för den användare som användaren försöker logga in som

---

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
