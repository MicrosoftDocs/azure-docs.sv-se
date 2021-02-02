---
title: Resurser för att migrera appar till Azure Active Directory | Microsoft Docs
description: Resurser som hjälper dig att migrera program åtkomst och autentisering till Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ac7e273ebcc4cf541bd94bf7e7fad05802db8d0b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258311"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Resurser för att migrera program till Azure Active Directory

Resurser som hjälper dig att migrera program åtkomst och autentisering till Azure Active Directory (Azure AD).

| Resurs  | Beskrivning  |
|:-----------|:-------------|
|[Migrera dina appar till Azure AD](https://aka.ms/migrateapps/whitepaper) | Den här white paper visar fördelarna med migreringen och beskriver hur du planerar migreringen i fyra tydliga steg: identifiering, klassificering, migrering och kontinuerlig hantering. Du kommer att vägleda dig genom processen och dela upp ditt projekt i lätt att förbruka delar. I hela dokumentet finns länkar till viktiga resurser som hjälper dig längs vägen. |
|[Lösnings guide: Migrera appar från Active Directory Federation Services (AD FS) (AD FS) till Azure AD](./migrate-adfs-apps-to-azure.md) | Den här lösnings guiden vägleder dig genom samma fyra faser i planering och körning av ett programmigrerings projekt som beskrivs på en högre nivå i dokumentationen för migreringen. I den här guiden får du lära dig hur du tillämpar dessa faser på det specifika målet med att flytta ett program från Azure Directory federerade tjänster (AD FS) till Azure AD.|
|[Själv studie kurs om utvecklare: AD FS till Azure AD Application Migration Spelbok för utvecklare](https://aka.ms/adfsplaybook) | Den här uppsättningen ASP.NET kod exempel och medföljande självstudier hjälper dig att lära dig att säkert och säkert migrera dina program som är integrerade med Active Directory Federation Services (AD FS) (AD FS) till Azure Active Directory (Azure AD). Den här självstudien fokuserar på utvecklare som inte bara behöver lära sig att konfigurera appar på både AD FS och i Azure AD, utan också bli medvetna om och tryggande av ändringar i kodbasen kräver i den här processen.|
| [Verktyg: skript för Active Directory Federation Services (AD FS) migration readiness](https://aka.ms/migrateapps/adfstools) | Det här är ett skript som du kan köra på din lokala Active Directory Federation Services (AD FS)-Server (AD FS) för att fastställa om apparnas beredskap ska migreras till Azure AD.|
| [Distributions plan: Migrera från AD FS till hash-synkronisering av lösen ord](https://aka.ms/ADFSTOPHSDPDownload) | Med hash-synkronisering av lösen ord synkroniseras hashar av användar lösen ord från lokala Active Directory till Azure AD. Detta gör att Azure AD kan autentisera användare utan att interagera med den lokala Active Directory.| 
| [Distributions plan: Migrera från AD FS till direktautentisering](https://aka.ms/ADFSTOPTADPDownload)|Med Azure AD-direktautentisering kan användarna logga in i både lokala och molnbaserade program genom att använda samma lösen ord. Den här funktionen ger användarna en bättre upplevelse eftersom de har ett mindre lösen ord att komma ihåg. Det minskar också kostnaderna för IT-supportavdelningen eftersom användarna är mindre sannolika att glömma att logga in när de bara behöver komma ihåg ett lösen ord. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
| [Distributions plan: aktivera enkel inloggning till en SaaS-app med Azure AD](https://aka.ms/SSODPDownload) | Enkel inloggning (SSO) hjälper dig att komma åt alla appar och resurser du behöver för att göra affärer, samtidigt som du bara loggar in en gång, med ett enda användar konto. Efter att en användare har loggat in kan användaren till exempel flytta från Microsoft Office till SalesForce, till Box utan autentisering (till exempel skriva ett lösen ord) en andra gång. 
| [Distributions plan: utöka appar till Azure AD med Application Proxy](https://aka.ms/AppProxyDPDownload)| Att ge åtkomst från anställdas bärbara datorer och andra enheter till lokala program har traditionellt involverade virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZs). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Azure AD-programproxy gör det lättare att komma åt lokala program. |
| [Distributionsplaner](../fundamentals/active-directory-deployment-plans.md) | Hitta fler distributions planer för att distribuera funktioner som Multi-Factor Authentication, villkorlig åtkomst, användar etablering, sömlös SSO, självbetjäning för återställning av lösen ord och mycket mer! |