---
title: Federazione con AD FS di un cliente
description: Come eseguire la federazione con il servizio AD FS del cliente in un'applicazione multi-tenant
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: token-cache
pnp.series.next: client-assertion
ms.openlocfilehash: 08bf567085a940287de310f61b9f447d0ce5d5ec
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="federate-with-a-customers-ad-fs"></a>Federazione con AD FS di un cliente

Questo articolo descrive come un'applicazione SaaS multi-tenant può supportare l'autenticazione tramite Active Directory Federation Services (AD FS) per la federazione con il servizio AD FS del cliente.

## <a name="overview"></a>Panoramica
Azure Active Directory (Azure AD) semplifica la procedura di accesso degli utenti dai tenant di Azure AD, ed è disponibile anche per i clienti di Office365 e Dynamics CRM Online. Per i clienti che usano Active Directory in una Intranet aziendale è tuttavia necessario valutare altre soluzioni.

Una opzione prevede che i clienti sincronizzino il servizio Active Directory locale con Azure AD tramite [Azure AD Connect]. Per alcuni clienti potrebbe risultare impossibile adottare questo approccio a causa di criteri IT aziendali o altri motivi. In questo caso, per la federazione è possibile usare Active Directory Federation Services (AD FS).

Per abilitare questo scenario:

* Il cliente deve avere una farm AD FS con connessione a Internet.
* Il provider SaaS distribuisce la propria farm AD FS.
* Il cliente e il provider SaaS devono configurare un [trust federativo]. Si tratta di un processo manuale.

Esistono tre ruoli principali nella relazione di trust:

* Il servizio AD FS del cliente rappresenta il [partner account], responsabile dell'autenticazione degli utenti dall'istanza di Active Directory del cliente e della creazione di token di sicurezza con attestazioni utente.
* Il servizio AD FS del provider SaaS è il [partner risorse]che considera attendibile il partner account e riceve le attestazioni utente.
* L'applicazione viene configurata come una relying party nell'istanza di AD FS del provider SaaS.
  
  ![trust federativo](./images/federation-trust.png)

> [!NOTE]
> In questo articolo si presuppone che l'applicazione usi OpenID Connect come protocollo di autenticazione. Un'altra opzione prevede l'uso di WS-Federation.
> 
> Per il protocollo OpenID Connect, il provider SaaS deve usare AD FS 2016 in esecuzione in Windows Server 2016. AD FS 3.0 non supporta il protocollo OpenID Connect.
> 
> ASP.NET Core non include il supporto predefinito per WS-Federation.
> 
> 

Per un esempio di utilizzo di WS-Federation con ASP.NET 4, vedere l'[esempio active-directory-dotnet-webapp-wsfederation][active-directory-dotnet-webapp-wsfederation].

## <a name="authentication-flow"></a>Flusso di autenticazione
1. Quando l'utente fa clic su "accedi", l'applicazione lo reindirizza a un endpoint OpenID Connect del servizio AD FS del provider SaaS.
2. L'utente immette il nome utente aziendale ("`alice@corp.contoso.com`"). AD FS usa l'individuazione dell'area di autenticazione principale per il reindirizzamento al servizio AD FS del cliente, in cui l'utente immette le proprie credenziali.
3. Il servizio AD FS del cliente invia le attestazioni utente al servizio AD FS del provider SaaS tramite WS-Federation o SAML.
4. Le attestazioni vengono inviate da AD FS all'app tramite OpenID Connect. Questo passaggio richiede una transizione di protocollo da WS-Federation.

## <a name="limitations"></a>Limitazioni
Per impostazione predefinita, l'applicazione relying party riceve solo un set fisso delle attestazioni disponibili nel parametro id_token, illustrato nella tabella seguente. Con AD FS 2016, è possibile personalizzare il parametro id_token negli scenari di OpenID Connect. Per altre informazioni, vedere [Custom ID Tokens in AD FS](/windows-server/identity/ad-fs/development/customize-id-token-ad-fs-2016) (Token ID personalizzati in AD FS).

| Attestazione | DESCRIZIONE |
| --- | --- |
| aud |Destinatari. Applicazione per la quale sono state rilasciate le attestazioni. |
| authenticationinstant |[Istante di autenticazione]. Ora in cui è stata eseguita l'autenticazione. |
| c_hash |Valore hash del codice. È un hash del contenuto del token. |
| exp |[Scadenza]. È l'ora dopo la quale il token non verrà più accettato. |
| iat |Ora di rilascio. È l'ora in cui è stato rilasciato il token. |
| iss |Autorità emittente. Il valore di questa attestazione è sempre quello dell'istanza AD FS del partner risorse. |
| name |Nome utente. Esempio: `john@corp.fabrikam.com` |
| nameidentifier |[Identificatore del nome]. È l'identificatore del nome dell'entità per la quale è stato rilasciato il token. |
| nonce |Nonce sessione. È un valore univoco generato da AD FS per impedire attacchi di tipo replay. |
| upn |Nome dell'entità utente. Esempio: `john@corp.fabrikam.com` |
| pwd_exp |Periodo di scadenza della password. Corrisponde al numero di secondi fino alla scadenza della password dell'utente o di un segreto di autenticazione simile, ad esempio un PIN. |

> [!NOTE]
> L'attestazione "iss" contiene l'istanza AD FS del partner. In genere, questa attestazione identifica il provider SaaS come autorità emittente. Non identifica l'istanza AD FS del cliente. Il dominio del cliente è incluso nel nome dell'entità utente.
> 
> 

La parte seguente di questo articolo descrive come configurare la relazione di trust tra la relying party (app) e il partner account (cliente).

## <a name="ad-fs-deployment"></a>Distribuzione di AD FS
Il provider SaaS può distribuire AD FS in locale o in macchine virtuali di Azure. Per garantire la sicurezza e la disponibilità, seguire queste linee guida:

* Distribuire almeno due server AD FS e due server proxy AD FS per ottenere la maggiore disponibilità del servizio AD FS.
* I controller di dominio e i server AD FS non devono mai essere esposti direttamente a Internet e devono risiedere in una rete virtuale con accesso diretto.
* Per pubblicare i server AD FS in Internet è necessario usare i proxy applicazione Web, precedentemente noti come proxy AD FS.

Per configurare una topologia simile in Azure, è necessario usare reti virtuali, gruppi di sicurezza di rete, macchine virtuali di Azure e set di disponibilità. Per altre informazioni, vedere [Linee guida per la distribuzione di Windows Server Active Directory nelle macchine virtuali di Azure][active-directory-on-azure].

## <a name="configure-openid-connect-authentication-with-ad-fs"></a>Configurare l'autenticazione OpenID Connect con AD FS
Il provider SaaS deve abilitare la connessione OpenID Connect tra l'applicazione e AD FS. A tale scopo, aggiungere un gruppo di applicazioni in AD FS.  Per istruzioni dettagliate su questa procedura, vedere l'argomento relativo alla configurazione di un'app Web per l'accesso ad AD FS tramite OpenID Connect in questo [post di blog]. 

Configurare quindi il middleware per OpenID Connect. L'endpoint dei metadati è `https://domain/adfs/.well-known/openid-configuration`, dove il dominio corrisponde al dominio AD FS del provider SaaS.

In genere questo endpoint verrà combinato con altri endpoint OpenID Connect, ad esempio AAD. Sarà necessario prevedere due pulsanti di accesso diversi per poterli distinguere, in modo che l'utente venga indirizzato all'endpoint di autenticazione corretto.

## <a name="configure-the-ad-fs-resource-partner"></a>Configurare il partner risorse AD FS
Il provider SaaS deve eseguire la procedura seguente per ogni cliente che si connette tramite AD FS:

1. Aggiungere un trust del provider di attestazioni.
2. Aggiungere le regole attestazioni.
3. Abilitare l'individuazione dell'area di autenticazione.

Ecco la procedura dettagliata.

### <a name="add-the-claims-provider-trust"></a>Aggiungere il trust del provider di attestazioni
1. In Server Manager selezionare **Strumenti** e quindi **Gestione AD FS**.
2. Nell'albero della console fare clic con il pulsante destro del mouse su **Attendibilità provider di attestazioni** in **AD FS**. Scegliere **Aggiungi attendibilità provider di attestazioni**.
3. Fare clic su **Avvia** per avviare la procedura guidata.
4. Selezionare l'opzione "Importa dati sul provider di attestazioni pubblicati online o in una rete locale". Immettere l'URI dell'endpoint dei metadati di federazione del cliente. Esempio: `https://contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`. Questo valore deve essere richiesto al cliente.
5. Completare la procedura guidata usando le opzioni predefinite.

### <a name="edit-claims-rules"></a>Modificare le regole attestazioni
1. Fare clic con il pulsante destro del mouse sul trust del provider di attestazioni e scegliere **Modifica regole attestazione**.
2. Fare clic su **Aggiungi regola**.
3. Selezionare "Applicare la funzione di pass-through o di filtro a un'attestazione in ingresso" e fare clic su **Avanti**.
   ![Aggiunta guidata regole attestazione di trasformazione](./images/edit-claims-rule.png)
4. Immettere un nome per la regola.
5. In "Tipo di attestazione in ingresso" selezionare **UPN**.
6. Selezionare "Pass-through di tutti i valori attestazione".
   ![Aggiunta guidata regole attestazione di trasformazione](./images/edit-claims-rule2.png)
7. Fare clic su **Fine**.
8. Ripetere i passaggi da 2 a 7 e specificare **Tipo di attestazione ancoraggio** per il tipo di attestazione in ingresso.
9. Fare clic su **OK** per completare la procedura guidata.

### <a name="enable-home-realm-discovery"></a>Abilitare l'individuazione dell'area di autenticazione
Eseguire lo script di PowerShell seguente:

```
Set-ADFSClaimsProviderTrust -TargetName "name" -OrganizationalAccountSuffix @("suffix")
```

dove "name" corrisponde al nome descrittivo del trust del provider di attestazioni e "suffix" rappresenta il suffisso del nome dell'entità utente per il dominio Active Directory del cliente, ad esempio "corp.fabrikam.com".

Con questa configurazione, gli utenti possono specificare il proprio account aziendale e AD FS seleziona automaticamente il provider di attestazioni corrispondente. Per altre informazioni, vedere la sezione "Configurare il provider di identità per l'uso di determinati suffissi di posta elettronica" in [Personalizzazione delle pagine di accesso ad AD FS].

## <a name="configure-the-ad-fs-account-partner"></a>Configurare il partner account di AD FS
Procedura per il cliente:

1. Aggiungere un trust della relying party.
2. Aggiungere le regole attestazioni.

### <a name="add-the-rp-trust"></a>Aggiungere il trust della relying party.
1. In Server Manager selezionare **Strumenti** e quindi **Gestione AD FS**.
2. Nell'albero della console fare clic con il pulsante destro del mouse su **Attendibilità componente** in **AD FS**. Scegliere **Aggiungi attendibilità componente**.
3. Selezionare **In grado di riconoscere attestazioni** e fare clic su **Avvia**.
4. Nella pagina **Seleziona origine dati** scegliere l'opzione "Importa dati sul provider di attestazioni pubblicati online o in una rete locale". Immettere l'URI dell'endpoint dei metadati di federazione del provider SaaS.
   ![Aggiunta guidata attendibilità componente](./images/add-rp-trust.png)
5. Nella pagina **Specifica nome visualizzato** immettere un nome.
6. Nella pagina **Scegliere i criteri di controllo di accesso** scegliere un criterio. È possibile autorizzare tutti gli utenti dell'organizzazione o scegliere un gruppo di sicurezza specifico.
   ![Aggiunta guidata attendibilità componente](./images/add-rp-trust2.png)
7. Immettere eventuali parametri richiesti nella casella **Criteri**.
8. Fare clic su **Avanti** per completare la procedura guidata.

### <a name="add-claims-rules"></a>Aggiungere le regole attestazioni
1. Fare clic con il pulsante destro del mouse sul trust della relying party appena aggiunto e quindi scegliere **Modifica criteri di rilascio delle attestazioni**.
2. Fare clic su **Aggiungi regola**.
3. Selezionare "Inviare attributi LDAP come attestazioni" e fare clic su **Avanti**.
4. Immettere un nome per la regola, ad esempio "UPN".
5. In **Archivio attributi** selezionare **Active Directory**.
   ![Aggiunta guidata regole attestazione di trasformazione](./images/add-claims-rules.png)
6. Nella sezione **Mapping degli attributi LDAP** :
   * In **Attributo LDAP** selezionare **User-Principal-Name**.
   * In **Tipo di attestazione in uscita** selezionare **UPN**.
     ![Aggiunta guidata regole attestazione di trasformazione](./images/add-claims-rules2.png)
7. Fare clic su **Fine**.
8. Fare nuovamente clic su **Add Rule** .
9. Selezionare "Inviare attestazioni mediante una regola personalizzata" e fare clic su **Avanti**.
10. Immettere un nome per la regola, ad esempio "Tipo di attestazione ancoraggio".
11. In **Regola personalizzata**immettere quanto segue:
    
    ```
    EXISTS([Type == "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype"])=>
    issue (Type = "http://schemas.microsoft.com/ws/2014/01/identity/claims/anchorclaimtype",
          Value = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn");
    ```
    
    Con questa regola viene rilasciata un'attestazione di tipo `anchorclaimtype`. L'attestazione indica alla relying party di usare il nome dell'entità utente come ID non modificabile dell'utente.
12. Fare clic su **Fine**.
13. Fare clic su **OK** per completare la procedura guidata.


<!-- Links -->
[Azure AD Connect]: /azure/active-directory/active-directory-aadconnect/
[trust federativo]: https://technet.microsoft.com/library/cc770993(v=ws.11).aspx
[partner account]: https://technet.microsoft.com/library/cc731141(v=ws.11).aspx
[partner risorse]: https://technet.microsoft.com/library/cc731141(v=ws.11).aspx
[Istante di autenticazione]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.authenticationinstant%28v=vs.110%29.aspx
[Scadenza]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-25#section-4.1.
[Identificatore del nome]: https://msdn.microsoft.com/library/system.security.claims.claimtypes.nameidentifier(v=vs.110).aspx
[active-directory-on-azure]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[post di blog]: http://www.cloudidentity.com/blog/2015/08/21/OPENID-CONNECT-WEB-SIGN-ON-WITH-ADFS-IN-WINDOWS-SERVER-2016-TP3/
[Personalizzazione delle pagine di accesso ad AD FS]: https://technet.microsoft.com/library/dn280950.aspx
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
[client assertion]: client-assertion.md
[active-directory-dotnet-webapp-wsfederation]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation
