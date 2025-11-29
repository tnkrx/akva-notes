## Enumerating via Differing Error Messages

Para montar uma lista de usuários válidos, normalmente precisamos de uma **wordlist** de usernames. Usuários normalmente são simples (poucos caracteres especiais), então wordlists funcionam bem (ex.: SecLists).

Abordagem:

1.  Testar cada username da wordlist no endpoint de login (POST).
    
2.  Filtrar respostas que indicam "Unknown user" para isolar usuários válidos.
    

Exemplo com `ffuf` usando a wordlist `xato-net-10-million-usernames.txt`:

`ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt \ -u http://172.17.0.2/index.php -X POST \ -H "Content-Type: application/x-www-form-urlencoded" \ -d "username=FUZZ&password=invalid" -fr "Unknown user"`

Saída exemplo indica um usuário válido:

`* FUZZ: consuelo`

Usuários identificados podem ser alvos de brute-force/password spraying.

* * *

## User Enumeration via Side-Channel Attacks

Além de mensagens de erro distintas, a enumeração pode ocorrer por **canais laterais** (side-channels) — informações indiretas inferidas da resposta:

- **Timing:** diferença de tempo de resposta pode revelar se a aplicação fez uma lookup no banco (mais tempo) só para usernames válidos. Mesmo com respostas idênticas, medir latência pode permitir distinguir usuários válidos.