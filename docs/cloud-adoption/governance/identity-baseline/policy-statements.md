---
title: 'CAF: Istruzioni dei criteri di esempio della Baseline di identità'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Istruzioni dei criteri di esempio della Baseline di identità
author: BrianBlanchard
ms.openlocfilehash: 5fad9265b9c048ee502c7e084ddd03faa0ad3e23
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902035"
---
# <a name="identity-baseline-sample-policy-statements"></a>Istruzioni dei criteri di esempio della Baseline di identità

Le singole definizioni dei criteri del cloud sono linee guida per affrontare i rischi specifici identificati durante il processo di valutazione dei rischi. Queste definizioni devono fornire un riassunto conciso dei rischi e dei piani per gestirli. Ogni definizione di istruzione deve includere i tipi di informazioni seguenti:

- Rischio tecnico - Riepilogo del rischio a cui sono applicabili questi criteri.
- Definizione dei criteri - Una spiegazione chiara e riepilogativa dei requisiti dei criteri.
- Opzioni di progettazione - Suggerimenti pratici, specifiche o altre linee guida che i team IT e gli sviluppatori possono usare per l'implementazione dei criteri.

Gli esempi seguenti di istruzioni dei criteri riguardano alcuni rischi aziendali comuni relativi all'identità e vengono forniti come esempi a cui riferirsi durante la creazione di istruzioni dei criteri per soddisfare le esigenze dell'organizzazione. Questi esempi non devono essere considerati prescrittivi ed esistono potenzialmente diverse opzioni relative ai criteri per affrontare qualsiasi tipo di rischio. Lavorare a stretto contatto con i team responsabili delle attività aziendali e IT per identificare le migliori soluzioni di criteri per il proprio set di rischi specifico.

## <a name="lack-of-access-controls"></a>Mancanza di controlli di accesso

**Rischio tecnico**: le impostazioni di controllo di accesso insufficienti o ad hoc possono introdurre rischi di accesso non autorizzato alle risorse sensibili o di importanza cruciale.

**Definizione dei criteri**: tutti gli asset distribuiti nel cloud devono essere controllati tramite identità e ruoli approvati da criteri di governance correnti.

**Opzioni di progettazione potenziali**: [Accesso condizionale di Azure Active Directory](/azure/active-directory/conditional-access/overview) è il meccanismo di controllo di accesso predefinito di Azure.

## <a name="overprovisioned-access"></a>Accesso con provisioning eccessivo

**Rischio tecnico**: utenti e gruppi con il controllo delle risorse oltre l'area di responsabilità possono comportare modifiche non autorizzate, vulnerabilità della sicurezza o interruzioni.

**Definizione dei criteri**: verranno implementati i criteri seguenti:

- Un modello di accesso con privilegi minimi verrà applicato a tutte le risorse coinvolte in applicazioni di importanza cruciale o dati protetti.
- Autorizzazioni elevate dovrebbero essere un'eccezione e ogni eccezione di questo tipo devono essere registrate con il team di Governance Cloud. Le eccezioni verranno controllate regolarmente.

**Opzioni di progettazione potenziali**: Consultare le [Procedure consigliate per la gestione delle identità di Azure](/azure/security/azure-security-identity-management-best-practices) per implementare una strategia di controllo degli accessi in base al ruolo (RBAC) che limita l'accesso a seconda dei principi [necessario conoscere](https://wikipedia.org/wiki/Need_to_know) e [privilegi minimi di sicurezza](https://wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="lack-of-shared-management-accounts-between-on-premises-and-the-cloud"></a>Mancanza di account di gestione condivisi tra locali e cloud

**Rischio tecnico**: la gestione IT o il personale amministrativo con gli account in Active Directory Domain Services locale potrebbero non disporre dei diritti di accesso sufficienti alle risorse del cloud che potrebbero non essere in grado di risolvere efficacemente i problemi operativi e di sicurezza.

**Definizione dei criteri**: tutti i gruppi con privilegi elevati nell'infrastruttura di Active Directory Domain Services locale dovrebbero essere associati a un ruolo di controllo approvato degli accessi in base al ruolo.

**Opzioni di progettazione potenziali**: implementare una soluzione con identità ibrida tra Azure Active Directory basata su cloud e Active Directory in locale, quindi aggiungere i gruppi necessari in locale per i ruoli RBAC necessari per svolgere il proprio lavoro.

## <a name="weak-authentication-mechanisms"></a>Meccanismi di autenticazione debole

**Rischio tecnico**: sistemi di gestione delle identità con i metodi di autenticazione utente non sufficientemente sicura, ad esempio le combinazioni di base utente/password, possono portare a password compromesse o violate, costituendo un grave rischio di accesso non autorizzato ai sistemi cloud protetti.

**Definizione dei criteri**: Tutti gli account sono necessari per eseguire l'accesso alle risorse protette tramite un metodo di autenticazione a più fattori (MFA).

**Opzioni di progettazione potenziali**: Per Azure Active Directory, implementare [autenticazione a più fattori di Azure](/azure/active-directory/authentication/concept-mfa-howitworks) come parte del processo di autorizzazione utente.

## <a name="isolated-identity-providers"></a>Provider di identità isolata

**Rischio tecnico**: provider di identità incompatibili possono causare l'impossibilità di condividere risorse o servizi con i clienti e altri partner aziendali.

**Definizione dei criteri**: per la distribuzione di applicazioni che richiedono l'autenticazione del cliente è necessario usare un provider di identità approvato compatibile con il provider di identità primario per gli utenti interni.

**Opzioni di progettazione potenziali**: Implementare [Federazione con Azure Active Directory](/azure/active-directory/hybrid/whatis-fed) tra i provider di identità interni e dei clienti.

## <a name="identity-reviews"></a>Revisione di identità

**Rischio tecnico**: le modifiche aziendali, l'aggiunta di nuove distribuzioni cloud o altri problemi di sicurezza possono aumentare nel tempo i rischi di accesso non autorizzato per proteggere le risorse.

**Definizione dei criteri**: i processi di Governance cloud devono includere un esame trimestrale condotto con i team che si occupano della gestione dell'identità, al fine di identificare gli attori dannosi o i modelli di uso da evitare nella configurazione degli asset del cloud.

**Opzioni di progettazione potenziali**: stabilire una riunione di revisione trimestrale per la sicurezza che includa sia i membri del team di governance e sia il personale IT responsabile dei servizi di gestione delle identità. Verificare i dati e le metriche di sicurezza esistenti per identificare le lacune negli strumenti e nei criteri della gestione delle identità correnti, oltre ad aggiornare i criteri per attenuare nuovi possibili rischi.

## <a name="next-steps"></a>Passaggi successivi

Usare gli esempi citati in questo articolo come punto di partenza per elaborare criteri applicabili a rischi aziendali specifici, in linea con i piani di adozione del cloud.

Per iniziare a sviluppare le proprie istruzioni di criteri personalizzate correlate alla Baseline di identità, scaricare il [modello Baseline di identità](template.md).

Per accelerare l'adozione di questa disciplina, scegliere il [percorso di governance di utilità pratica](../journeys/overview.md) più adatto al proprio ambiente. Modificare quindi la progettazione per incorporare le decisioni relative a criteri aziendali specifici.

> [!div class="nextstepaction"]
> [Actionable Governance Journeys (Percorsi di governance di utilità pratica)](../journeys/overview.md)