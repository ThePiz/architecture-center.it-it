---
title: 'CAF: Strumenti per la baseline di identità in Azure'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Strumenti per la baseline di identità in Azure
author: BrianBlanchard
ms.openlocfilehash: 81b0fa9cfee597da98d8b983fb155eac82d97bf8
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243672"
---
# <a name="identity-baseline-tools-in-azure"></a>Strumenti per la baseline di identità in Azure

[Identity Baseline (Baseline di identità)](overview.md) è una delle [Five Disciplines of Cloud Governance (Cinque discipline della Governance cloud)](../governance-disciplines.md). Questa disciplina è incentrata sulla modalità di definizione dei criteri che garantiscono la coerenza e la continuità aziendale delle identità utente, indipendentemente dal provider di servizi cloud che ospita l'applicazione o il carico di lavoro.

Gli strumenti seguenti sono inclusi nella guida all'individuazione sull'Identità ibrida.

**Active Directory Domain Services (locale):** Active Directory Domain Services è il provider di identità usato più frequentemente dall'azienda per archiviare e convalidare le credenziali dell'utente.

**Azure Active Directory:** Software come un servizio (SaaS) equivalente ad Active Directory, in grado di creare una federazione con Active Directory locale.

**Active Directory Domain Services (IaaS):** un'istanza dell'applicazione Active Directory Domain Services in esecuzione su una macchina virtuale in Azure.

L'identità è il piano di controllo della sicurezza IT. L'autenticazione protegge quindi l'accesso dell'organizzazione al cloud. Le organizzazioni hanno bisogno di un piano di controllo delle identità che ne rafforzi la sicurezza e tenga le app cloud al riparo dalle intrusioni.

## <a name="cloud-authentication"></a>Autenticazione cloud

La scelta del metodo di autenticazione corretto rappresenta la priorità assoluta per le organizzazioni che desiderano spostare le proprie applicazioni nel cloud.

Se si sceglie questo metodo, Azure AD gestisce il processo di accesso degli utenti. Affiancandolo a un accesso Single Sign-On trasparente, gli utenti possono accedere alle applicazioni cloud senza dover immettere nuovamente le proprie credenziali. Con l'autenticazione cloud è possibile scegliere tra due opzioni:

**Sincronizzazione dell'hash delle password di Azure AD**: È il modo più semplice per abilitare l'autenticazione per gli oggetti directory locali in Azure AD. Questo metodo si può anche usare con qualsiasi metodo, come metodo di autenticazione di un failover di backup, nel caso in cui il server locale sia inattivo.

**Autenticazione pass-through di Azure AD**: fornisce un processo di convalida permanente delle password per i servizi di autenticazione di Azure AD mediante un agente software eseguito in uno o più server locali.

> [!NOTE]
> Le società che per requisiti di sicurezza devono applicare immediatamente gli stati degli account utente locali, i criteri di gestione delle password e gli orari di accesso potrebbero usare questo metodo di autenticazione pass-through.

**Autenticazione federata:**

se si sceglie questo metodo, Azure AD trasferisce il processo di autenticazione a un sistema di autenticazione attendibile separato, ad esempio un sistema locale Active Directory Federation Services (AD FS) o un provider di federazione di terze parti, per convalidare la password dell'utente.

L'articolo [Scegliere il metodo di autenticazione appropriato per Azure AD](/azure/security/azure-ad-choose-authn) contiene un albero delle decisioni per scegliere la soluzione migliore per l'organizzazione.

La tabella seguente riporta un elenco di strumenti nativi che possono aiutare a sviluppare i criteri e i processi che supportano la disciplina di governance.

<!-- markdownlint-disable MD033 -->

|Considerazioni|Sincronizzazione dell'hash delle password + Seamless SSO|Autenticazione pass-through + Seamless SSO|Federazione con ADFS|
|:-----|:-----|:-----|:-----|
|Dove si verifica l'autenticazione?|Nel cloud|Nel cloud dopo la verifica della password di protezione con l'agente di autenticazione locale|Locale|
|Quali sono i requisiti del server locale oltre il sistema di provisioning Azure AD Connect?|Nessuna|Un server per ogni agente di autenticazione aggiuntivo|Due o più server AD FS<br><br>Due o più server WAP nella rete perimetrale|
|Quali sono i requisiti di rete e Internet locali oltre al sistema di provisioning?|Nessuna|[Accesso a Internet in uscita](/azure/active-directory/hybrid/how-to-connect-pta-quick-start) dai server in cui sono in esecuzione gli agenti di autenticazione|[Accesso Internet in ingresso](/windows-server/identity/ad-fs/overview/ad-fs-requirements) ai server WAP nelle reti perimetrali<br><br>Accesso di rete in ingresso ai server AD FS dai server WAP nelle reti perimetrali<br><br>Bilanciamento del carico di rete|
|Esiste un requisito per il certificato SSL?|No |No |Sì|
|Esiste una soluzione di monitoraggio dello stato?|Facoltativo|Stato agente fornito dall'[interfaccia di amministrazione di Azure Active Directory](/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)|[Azure AD Connect Health](/azure/active-directory/hybrid/how-to-connect-health-adfs)|
|Gli utenti ottengono l'accesso Single Sign-On alle risorse cloud dai dispositivi aggiunti al dominio all'interno della rete aziendale?|Sì, con l'[accesso Single Sign-On facile](/azure/active-directory/hybrid/how-to-connect-sso)|Sì, con l'[accesso Single Sign-On facile](/azure/active-directory/hybrid/how-to-connect-sso)|Sì|
|Quali tipi di accesso sono supportati?|UserPrincipalName + Password<br><br>Autenticazione integrata di Windows con [Seamless SSO](/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[ID di accesso alternativo](/azure/active-directory/hybrid/how-to-connect-install-custom)|UserPrincipalName + Password<br><br>Autenticazione integrata di Windows con [Seamless SSO](/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[ID di accesso alternativo](/azure/active-directory/hybrid/how-to-connect-pta-faq)|UserPrincipalName + Password<br><br>sAMAccountName + Password<br><br>Autenticazione integrata di Windows<br><br>[Autenticazione con certificato e smart card](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[ID di accesso alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Windows Hello for Business è supportato?|[Modello di attendibilità chiavi](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modello di attendibilità certificati con Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modello di attendibilità chiavi](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modello di attendibilità certificati con Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modello di attendibilità chiavi](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modello di attendibilità certificati](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quali sono le opzioni di autenticazione a più fattori?|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Controlli personalizzati con accesso condizionale*](/azure/active-directory/conditional-access/controls#custom-controls-1)|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Controlli personalizzati con accesso condizionale*](/azure/active-directory/conditional-access/controls#custom-controls-1)|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Server di Azure MFA](/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[MFA di terze parti](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Controlli personalizzati con accesso condizionale*](/azure/active-directory/conditional-access/controls#custom-controls-1)|
|Quali stati dell'account utente sono supportati?|Account disabilitati<br>(fino a 30 minuti di ritardo)|Account disabilitati<br><br>Account bloccato<br><br>Account scaduto<br><br>Password scaduta<br><br>Orari di accesso|Account disabilitati<br><br>Account bloccato<br><br>Account scaduto<br><br>Password scaduta<br><br>Orari di accesso|
|Quali sono le opzioni di accesso condizionale?|[Accesso condizionale di Azure AD](/azure/active-directory/active-directory-conditional-access-azure-portal)|[Accesso condizionale di Azure AD](/azure/active-directory/active-directory-conditional-access-azure-portal)|[Accesso condizionale di Azure AD](/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Regole di attestazione per AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Il blocco dei protocolli legacy è supportato?|[Sì](/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Sì](/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Sì](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|È possibile personalizzare il logo, l'immagine e la descrizione nelle pagine di accesso?|[Sì, con Azure AD Premium](/azure/active-directory/customize-branding)|[Sì, con Azure AD Premium](/azure/active-directory/customize-branding)|[Sì](/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Quali scenari avanzati sono supportati?|[Smart Password Lockout](/azure/active-directory/active-directory-secure-passwords)<br><br>[Report delle credenziali perse](/azure/active-directory/active-directory-reporting-risk-events)|[Smart Password Lockout](/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Sistema di autenticazione multisito a bassa latenza<br><br>[Blocco della Extranet di AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integrazione con i sistemi di gestione delle identità di terze parti](/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> I controlli personalizzati nell'accesso condizionale di Azure AD non supportano attualmente la registrazione dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Il [Framework della trasformazione digitale di identità ibrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?LCID=EN-US) delinea un numero di combinazioni e soluzioni per la scelta e l'integrazione di ognuno di questi componenti.

[Strumento di connessione di Azure AD](https://aka.ms/aadconnectwiz) aiuta a integrare le directory locali con Azure AD.
