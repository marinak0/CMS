
# **Kriptografija i mrežna sigurnost** <!-- omit in toc -->

- [Lab 2: Man-in-the-middle attacks (ARP spoofing)](#lab-2-man-in-the-middle-attacks-arp-spoofing)
  - [ARP spoofing](#arp-spoofing)
  - [Zadatak](#zadatak)
    - [Presretanje autentikacijskog tokena](#presretanje-autentikacijskog-tokena)
    - [Tajni `cookie`](#tajni-cookie)
    - [Dekripcijski ključ](#dekripcijski-ključ)
    - [Dekripcija izazova](#dekripcija-izazova)
  - [Pohrana rješenja u GitLab repo](#pohrana-rješenja-u-gitlab-repo)
  - [Pripremna pitanja](#pripremna-pitanja)

# Lab 2: Man-in-the-middle attacks (ARP spoofing)

U okviru vježbe upoznajemo se sa osnovnim sigurnosnim prijetnjama i ranjivostima u računalnim mrežama. Analizirat ćemo ranjivost _Address Resolution Protocol_-a (_ARP_) koja napadaču omogućava izvođenje _**Man in the Middle (MitM)**_ napada na računala koja dijele zajedničku lokalnu mrežu (LAN, WLAN).

## ARP spoofing

<p align="center">
<img src="../img/arp_spoofing.png" width="650px" height="auto"/>
</p>

## Zadatak

Dekriptirati _crypto_ izazov (_challenge_) enkriptiran _AES šifrom u CBC enkripcijskom modu rada_ (AES-CBC); korištene kratice i terminologija vam u ovom trenutku možda nisu bliske ni poznate, no s vremenom će sve "sjesti na svoje mjesto". Dekripcijski ključ potreban za dekripciju izazova otkriti ćete u interakciji s _crypto oracle_ serverom .

Ključ za enkripciju/dekripciju, u okviru ove vježbe, izveden je iz tajne vrijednosti koju zovemo **_cookie_**. Tajni _cookie_ možete dobiti slanjem sljedećeg REST API zahtjeva _crypto oracle_ serveru: `GET /arp/cookie`. Za uspješno slanje zahtjeva treba vam autentikacijski token kojeg možete zatražiti od _crypto oracle_ servera. Pri tome se trebate autenticirati slanjem korisničkog imena (ime vašeg Docker _container_-a) i odgovarajuće lozinke:

```txt
POST username=my_name&password=my_pass /arp/token
```

Pojednostvaljeni prikaz interakcije s _crypto oracle_ serverom:
> username & password ⇒ token ⇒ cookie ⇒ key ⇒ Chuck Norris fact

### Presretanje autentikacijskog tokena

> username & password ⇒ **token** ⇒ cookie ⇒ key ⇒ Chuck Norris fact

Iskoristiti ranjivost ARP protokola i izvršiti MitM napad na način da presretnete komunikaciju između _crypto oracle_ servera i računala `arp_client`. Računalo `arp_client` periodično se logira na _crypto oracle_ server i zatraži autentikacijski token. _Crypto oracle_ odgovara slanjem tokena u sljedećem formatu:

```json
{
    "access_token": "string",
    "token_type": "bearer"
}
```

Za izvođenje ovog napada, za student će koristiti specijalizirano napadačko računalo koje u nazivu ima sufiks `arpspoof`.

1. Otvorite dva (2) _remote_ terminala na svom napadačkom računalu (prethodno trebate saznati njegovu IP adresu, e.g., `10.0.15.x`):

    ```bash
    ssh <username>@10.0.15.x
    ```

2. Otkrijte MAC i IP adrese za _crypto oracle_, `arpspoof` i `arp_client` računala. Pomognite se alatima `ifconfig`, `ping` i `arp`.

3. Jedan terminal koristite za osluškivanje prometa na lokalnoj mrežnoj kartici. Za ovo korisite `tcpdump`.

    ```bash
    sudo tcpdump
    ```

4. Drugi terminal koristite za izvršavanje **_ARP spoofing_** napada. Koristite program `arpspoof`.

    ```bash
    # Usage: arpspoof [-i interface] [-c own|host|both] [-t target] [-r] host
    sudo arpspoof
    ```

    > VAŽNO: MitM napad izvršite na način da presretnete komunikaciju u smjeru od _crypto_oracle_ servera prema `arp_client` računalu; lažno se predstavite _crypto_oracle_ računalu kao `arp_client`.

5. Nakon što ste pokrenuli napad, u prvom terminalu filtrirajte ARP pakete kako slijedi:

    ```bash
    sudo tcpdump arp and host arp_client.macvlan
    ```

    Komentirajte rezultat.

6. Popunite sljedeću tablicu podacima iz zaglavlja mrežnih paketa koje žrtva (_crypto_oracle_) šalje `arp_client` računalu:

    |               | `MAC`<sub>src</sub> | `MAC`<sub>dst</sub> | `IP`<sub>src</sub> | `IP`<sub>dst</sub> |
    | :------------- | :----------------- | :----------------- | :---------------- | :---------------- |
    | before attack |                   |                   |                  |                  |
    | after attack  |                   |                   |                  |                  |

7. Traženi autentikacijski token možete doznati primjenom odgovarajućeg `tcpdump` filtera:

    ```bash
    sudo tcpdump -vvAls0 | grep "access_token"
    ```

    Kopirajte token i prekinite napad (`Ctrl + C`).

### Tajni `cookie`

> username & password ⇒ token ⇒ **cookie** ⇒ key ⇒ Chuck Norris fact

### Dekripcijski ključ

> username & password ⇒ token ⇒ cookie ⇒ **key** ⇒ Chuck Norris fact

**HINT:** Pogledati rezultate prošlih labova (kod za enkripciju/dekripciju _crypto_ izazova).

### Dekripcija izazova

> username & password ⇒ token ⇒ cookie ⇒ key ⇒ **Chuck Norris fact**

**HINT:** Pogledati rezultate prošlih labova (kod za enkripciju/dekripciju _crypto_ izazova).

Testirajte okriveni **_password_** za otključavanje sljedeće vježbe.

## Pohrana rješenja u GitLab repo

Potrebno pohraniti (koristiti zadan predložak `markdown` datoteke):

1. Popunjenu tablicu sa `MAC` i `IP` adresama.
2. Dekriptiran izazov i _password_.
3. Python skriptu za dekripciju izazova.
4. Odgovore na pripremna pitanja (u nastavku).

## Pripremna pitanja

1. What is the _Address Resolution Protocol (ARP)_, and what is its role in a network?
    - [Insert your answer here]
  
2. What is a _Man-in-the-Middle (MitM)_ attack, and how does ARP spoofing enable it?
   - [Insert your answer here]

3. How does an attacker use ARP spoofing to intercept network traffic?
   - [Insert your answer here]
  
4. How is the _cookie_ used to derive the encryption/decryption key?
   - [Insert your answer here]

5. What REST API request do you need to send to the _crypto oracle_ the secret cookie?
   - [Insert your answer here]
  
6. How do you obtain the authentication token?
   - [Insert your answer here]
  
7. How do you use the authentication token to obtain the cookie?
   - [Insert your answer here]

8. What encryption mode is used to encrypt the challenge in this lab?
   - [Insert your answer here]

9. What tool can you use to capture network traffic on a local network interface?
    - [Insert your answer here]


