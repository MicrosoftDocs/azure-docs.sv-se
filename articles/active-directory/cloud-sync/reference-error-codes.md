---
title: Azure AD Connect felkoder och beskrivningar för moln synkronisering
description: referens artikel för fel koder för moln synkronisering
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b1dbd9064e24327f129e8b8f957414d9162386
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650948"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect felkoder och beskrivningar för moln synkronisering
Följande är en lista över felkoder och deras beskrivning


## <a name="error-codes"></a>Felkoder

|Felkod|Information|Scenario|Lösning|
|-----|-----|-----|-----|
|Standardvärde|Fel meddelande: vi har upptäckt ett tids gräns fel för begäran när du kontaktade den lokala agenten och synkroniserat konfigurationen. Ytterligare problem som rör din Cloud Sync-agent finns i vår fel söknings vägledning.|Begäran till hans tids gräns. Aktuellt timeout-värde är 10 minuter.|Se vår [fel söknings vägledning](how-to-troubleshoot.md)|
|HybridSynchronizationActiveDirectoryInternalServerError|Fel meddelande: vi kunde inte bearbeta den här begäran just nu. Om problemet kvarstår kan du kontakta supporten och ange följande jobb-ID: AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Ytterligare information: bearbetning av HTTP-begäran resulterade i ett undantag. |Det gick inte att bearbeta parametrarna som togs emot i SCIM-begäran till en search-begäran.|Mer information finns i HTTP-svaret som returnerades av egenskapen respons i detta undantag.|
|HybridIdentityServiceNoAgentsAssigned|Fel meddelande: det går inte att hitta en aktiv agent för den domän som du försöker synkronisera. Kontrol lera om agenterna har tagits bort. Om så är fallet installerar du om agenten igen.|Inga agenter körs. Antagligen agenter har tagits bort. Registrera en ny agent.|"I det här fallet visas ingen agent som är tilldelad domänen i portalen.|
|HybridIdentityServiceNoActiveAgents|Fel meddelande: det går inte att hitta en aktiv agent för den domän som du försöker synkronisera. Kontrol lera om agenten körs genom att gå till servern, där agenten är installerad, och kontrol lera om det finns Microsoft Azure AD Cloud Sync-agenten under tjänster som körs.|"Agenter lyssnar inte på Service Bus-slutpunkten. [Agenten ligger bakom en brand vägg som inte tillåter anslutningar till Service Bus](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|Fel meddelande: vi kunde inte bearbeta den här begäran just nu. Om problemet kvarstår kan du kontakta supporten och ange följande jobb-ID: AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9. Ytterligare information: det verkar vara problem med konfigurationen av din moln synkronisering. Registrera om din Cloud Sync-agent på din lokal AD-domän och starta om konfigurationen från Azure Portal.|Resurs namnet måste anges så att denne vet vilken agent som ska kontaktas.|Registrera om din Cloud Sync-agent på din lokal AD-domän och starta om konfigurationen från Azure Portal.|
|HybridIdentityServiceAgentSignalingError|Fel meddelande: vi kunde inte bearbeta den här begäran just nu. Om problemet kvarstår kan du kontakta supporten och ange följande jobb-ID: AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-b495-18d5272eb3f9. Ytterligare information: vi kunde inte bearbeta den här begäran just nu. Om problemet kvarstår kan du kontakta supporten med jobb-ID: t (från status fönstret i konfigurationen).|Service Bus kan inte skicka ett meddelande till agenten. Kan vara ett avbrott i Service Bus, eller också svarar inte agenten.|Om problemet kvarstår kan du kontakta supporten med jobb-ID: t (från status fönstret i konfigurationen).|
|AzureDirectoryServiceServerBusy|Fel meddelande: ett fel uppstod. Felkod: 81. Fel Beskrivning: Azure Active Directory är upptagen. Ett nytt försök att utföra den här åtgärden görs automatiskt. Kontakta teknisk support om problemet kvarstår i mer än 24 timmar. Spårnings-ID: 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f-Server Namn:|Azure Active Directory är för tillfället upptagen.|Kontakta teknisk support om problemet kvarstår i mer än 24 timmar.|
|AzureActiveDirectoryInvalidCredential|Fel meddelande: Vi hittade ett problem med det tjänst konto som används för att köra Azure AD Connect Cloud Sync. Du kan reparera moln tjänst kontot genom att följa instruktionerna [här](./how-to-troubleshoot.md). Om felet kvarstår kan du kontakta supporten med jobb-ID: t (från status fönstret i konfigurationen). Ytterligare fel information: CredentialsInvalid AADSTS50034: användar kontot {EmailHidden} finns inte i skydrive365.onmicrosoft.com-katalogen. För att logga in på det här programmet måste kontot läggas till i katalogen. Spårnings-ID: 14b63033-3bc9-4bd4-b871-5eb4b3500200 korrelations-ID: 57d93ed1-be4d-483c-997c-a3b6f03deb00 tidsstämpel: 2021-01-12 21:08:29Z |Det här felet uppstår när ADToAADSyncServiceAccount för Sync-tjänsten inte finns i klient organisationen. Det kan bero på oavsiktlig borttagning av kontot.|Använd [Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) för att åtgärda tjänst kontot.|
|AzureActiveDirectoryExpiredCredentials|Fel meddelande: vi kunde inte bearbeta den här begäran just nu. Om problemet kvarstår kan du kontakta supporten med jobb-ID: t (från status fönstret i konfigurationen). Ytterligare fel information: CredentialsExpired AADSTS50055: lösen ordet har upphört att gälla. Spårnings-ID: 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 korrelations-ID: 1c69b196-1c3a-4381-9187-c84747807155 timestamp: 2021-01-12 20:59:31Z | Svars status koden indikerar inte lyckad: 401 (ej behörig).|Autentiseringsuppgifterna för AAD Sync tjänst kontot har upphört att gälla.|Du kan reparera moln tjänst kontot genom att följa anvisningarna på https://go.microsoft.com/fwlink/?linkid=2150988 . Om felet kvarstår kan du kontakta supporten med jobb-ID: t (från status fönstret i konfigurationen).  Ytterligare fel information: de administrativa Azure Active Directory autentiseringsuppgifterna för klient organisationen utbytdes för en OAuth-token som har upphört att gälla. "|
|AzureActiveDirectoryAuthenticationFailed|Fel meddelande: vi kunde inte bearbeta den här begäran just nu. Om problemet kvarstår kan du kontakta supporten och ange följande jobb-ID: AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405d-a3b9-de87761dc3ac. Ytterligare information: vi kunde inte bearbeta den här begäran just nu. Om problemet kvarstår kan du kontakta supporten med jobb-ID: t (från status fönstret i konfigurationen). Ytterligare fel information: UnexpectedError.|Okänt fel.|Om problemet kvarstår kan du kontakta supporten med jobb-ID: t (från status fönstret i konfigurationen).|

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)