
#GNU PRIVACY GUARD


Marta Feriani

`marta.celeste.feriani@gmail.com`

Corsi GNU/Linux Avanzati 2016

//logo


---

#COSA FACCIAMO OGGI

--

* introduzione alla cifratura:
	* simmetrica
	* asimmetrica
* Web of Trust
* GPG the hard way
* daily GPG

---

##PERCHÈ CIFRARE?

* tenere al sicuro dati sensibili
	* miei
	* degli altri (aziendali etc.)
* privacy nelle conversazioni

--

##GPG COME PROTOCOLLO

* Gnu Privacy Guard
* implementazione di _OpenPGP_
	* __P__retty __G__ood __P__rivacy

* cosa fa? tutto

---

##LA STORIA DI ALICE & BOB

Alice e Bob vogliono scambiarsi messaggi

senza che nessun altro (all'infuori di loro)

possa leggerli

--

##CIFRATURA

--

##1° TENTATIVO PER ALICE & BOB
###_CIFRATURA SIMMETRICA_

* A sceglie una chiave ___k___
* A cifra il messaggio ___m___ per B con ___k___
* A invia ___m___ a B
* B riceve ___m___ cifrato
* B decifra ___m___ con ___k___
* B legge ___m___

--

###_PROBLEMI CIFRATURA SIMMETRICA_

* Sia Alice che Bob devono conoscere ___k___ a priori
	* ___k___ va scambiata di persona
* __k__ ha duplice funzione: cifra/decifra
	* per sua natura __non deve essere divulgata__

--

##ALICE & BOB CI RIPROVANO
###_CIFRATURA ASIMMETRICA_

* ogni utente (Alice e Bob) dispone di due chiavi
	* chiave pubblica: cifra
	* chiave privata: decifra

_Le due chiavi sono indipendenti tra loro,_ 

_dall'una non è possibile ricavare l'altra_

--

##ALICE & BOB... 
###LO SCAMBIO DELLE CHIAVI

* A e B si scambiano le rispettive chiavi pubbliche

_ognuno custodisce (gelosamente) la propria chiave privata_

--

###MESSAGGIO SOLO PER BOB

* Alice cifra ___m___ con chiave pubblica di Bob $k_{B,pub}$


_Solo Bob con la sua privata_ $k_{B,pri}$

_può decifrare il messaggio_ 

* A invia ___m___ cifrato a B
* B riceve ___m___
* B decifra ___m___ con $k_{B,pri}$
* B legge

--

###THERE'S STILL WORK TO DO

Quando Bob riceve ___m___ ha la _certezza_ che il messaggio sia per lui

Non ha invece garanzie _su chi_ abbia mandato effettivamente ___m___

($k_{B,pub}$ è disponibile per tutti coloro vogliano contattare Bob)

--

###MESSAGGIO SOLO DA ALICE

Oltre a cifrare , Alice _firma_ ___m___ con $k_{A,priv}$

* Bob riceve ___m___
* B "decifra" la firma di A con $k_{A,pub}$

_Certezza che __m__ sia scritto da A_

--

###CIFRATURE A CONFRONTO
_Simmetrica:_

* leggera
* veloce
* _scambio delle chiavi di persona_
* _tante chiavi da memorizzare_

_Asimmetrica:_

* lenta
* 2 chiavi per 2 diversi scopi
* scambio comodo
* _facile fingersi qualcun altro_

---

##WEB OF TRUST

--

###TRUST MODEL

gli utenti si comportano da _notai_

certificano il legame _utente, chiave_

--

###HOW TO

* ciascuno si autocertifica

	$SIG_{priv,A}(Pub_A, ID_A)$
* si possono avere altre certificazioni, da altri utenti

	$SIG_{priv,B}(Pub_A, ID_A)$

--

###GESTIONE CHIAVI

* livello pubblico: servers
* livello privato: keyrings, trust_db

--

###LIVELLO PUBBLICO: SERVERS

* chiavi memorizzate su servers
* servers connessi tra loro (HKP, HTTPS)
* sincronizzazione e propagazione delle chiavi

--

###LIVELLO LOCALE: KEYRINGS

* archivi di chiavi locali:
	* chiavi pubbliche (mie, di altri)
	* chiavi private

_Pub_ring_ | | _Sec_ring_ 
--- | --- | ---
$Pub\_A$, $ID\_A$, $SIG\_{ME}$ | | $Pri\_{ME}$
$Pub\_{ME}$, $ID\_{ME}$| | ...

--

###LIVELLO LOCALE: TRUST_DB (1)

associa ad ogni chiave di _Pub_ring_ un livello di fiducia

_Keys_ | | _Levels_ 
--- | --- | ---
$Pub_{someone}$ | | $trust level$
$Pub_{someoneelse}$ | | ...
...| | ...

i valori vengono settati dall'utente, a mano 

--

###LIVELLO LOCALE: TRUST_DB (2)

1. unknown
2. undefined
3. untrusted
4. marginal
5. full
6. ultimate

--

###LIVELLO LOCALE: VALIDITY (1)

quanto il possessore del _keyring_ possa ritenere valida la chiave

1. unknown
2. untrusted
3. marginal
4. full

--

###LIVELLO LOCALE: VALIDITY (2)

###RULES

_Sig Trust Level_| | _Key Validity_
--- | --- | ---
ultimate | | full
full | | full
marginal | | marginal
3x marginal | | full
untrusted | | ...

--

##FORMATO CHIAVI (1)

* generazione chiavi: due coppie
	* Primary (pub & priv)
	* Subkey (pub & priv)

--

##FORMATO CHIAVI (2)

_Convenzione_

* $PRIM\_{Priv,A}(m)$ → A firma m

* $PRIM\_{Pub,A}(m)$ → B verifica firma di A

* $SUB_{Pub,A}(m)$ → B cifra per A

* $SUB_{Priv,A}(c)$ → A decifra c

--

##CERTIFICATE FORM (RFC 4880)

|$Master\_{Pub}^A$|
|--- |
|$ID\_A$ | 
|$SIG\_{M\_{priv}^A}(M\_{Pub}^A, ID\_A)$ |
|$Sub\_{pub}^A$S |
|$SIG\_{M\_{priv}^A}(M\_{Pub}^A, S\_{pub}^A)$ |
|$SIG\_{S\_{priv}^A}(M\_{Pub}^A, S\_{pub}^A)$ |

--


##FIRME DIVERSE

_certificazione identità_

* autocertificazione $SIG\_{M\_{priv}^A}(M\_{Pub}^A, ID\_A)$
* da altri utenti $SIG\_{M\_{priv}^B}(M\_{Pub}^A, ID\_A)$

--

##FIRME DIVERSE

_legame con sottochiavi_

* $SIG\_{M\_{priv}^A}(M\_{Pub}^A, S\_{pub}^A)$
* $SIG\_{S\_{priv}^A}(M\_{Pub}^A, S\_{pub}^A)$

--

##MODIFICA CERTIFICATO

* scarico dal server
* appendo in coda la modifica
* rimetto sul server

__NB: i server non controllano l'integrità dei certificati__

--

##IN CASO DI CATASTROFI
###REVOCATION SIGNATURE

certificati differenti per:

* revoca chiave primaria
* revoca sottochiave
* revoca firma

--

##STATO DELLA WEB OF TRUST

rappresentata come un grafo (orientato)

//disegno?

--

##ALCUNI DATI

* 3'867'397 chiavi totali
* 300k nodi isolati
* ~117 chiavi di media per SCC
* STRONGSET: 59'466 chiavi

---

#LET'S DO STUFF

--

##installazione

	$ sudo apt-get install gnupg2
	$ gpg2 --gen-key

--

##DEMO

--

##MANUTENZIONE CHIAVI (1)

_operazioni da e verso keyservers_

	$ gpg2 --send-keys # esporto chiavi
	$ gpg2 --search-keys # cerco chiavi
	$ gpg2 --recv-keys # importo chiavi
	$ gpg2 --refresh-keys` # controllo cambiamenti

--

##DEMO

--

##MANUTENZIONE CHIAVI (2)

	$ gpg2 --export # esporto chiavi (backup)
	$ gpg2 --gen-revoke # certificato di revoca

--

##DEMO

---

##CIFRIAMO COSE!

* simmetrica


	$ gpg2 -c nomefile
	$ gpg2 -d nomefile

--

##CIFRIAMO COSE!

* asimmetrica


	$ gpg2 -r "nome destinatario" -e nomefile
	$ gpg2 -o outputfile -d nomefile

--

##CIFRIAMO COSE!

* firma


	$ gpg2 --output doc.sig --sign doc
	$ gpg2 --output doc --decrypt doc.sig
	$ gpg2 --output doc.sig --detach-sig doc
	$ gpg2 --verify doc.sig doc

--

##DEMO

--

##GPG E PACCHETTI

* con GPG vengono firmati pacchetti  metadata dei repository
* il sistema controlla le firme sui repo in automatico
* si può verificare (a mano) la firma sul singolo .deb

--

##GPG E PACCHETTI


	$ gpg2 --verify nomesorgente.dsc # verifica il source
	$ debsig-verify nomepck.deb # verifica il pacchetto

--

##GPG & POSTA
###FORMATO E-MAIL

* header


	To: dest@mail.cose
	From: mitt@mail.cose
	Subject: gatti


* body: body della mail (ASCII)

--

##E-MAIL THE HARD WAY
// demo, mail cifrata da terminale

--


##E-MAIL THE EASY WAY

* Thunderbird + Enigmail
* K_9 + OpenKeyChain
* GPG Tools + Apple Mail
* iPGMail

--

#THAT'S ALL FOLKS!

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Licenza Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />Quest'opera è distribuita con Licenza <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribuzione - Non commerciale - Condividi allo stesso modo 4.0 Internazionale</a>.


<style>
.reveal table {
  color: white;
  margin: auto;
  border-collapse: collapse;
  border-spacing: 0;
}

.reveal table th {
  font-weight: bold;
}

.reveal table th,
.reveal table td {
  text-align: left;
  padding: 0.2em 0.5em 0.2em 0.5em;
  border-bottom: 1px solid;
}
</style>