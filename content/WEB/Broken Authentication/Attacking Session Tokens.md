Vulnerabilidades de autenticação não surgem apenas do *login* em si, mas também do **manuseio de tokens de sessão**. O token identifica a sessão do usuário; se um atacante obtiver um token válido de outra pessoa, pode **se passar por ela** e assumir a sessão.

## Brute-Force Attack (baixa entropia)

Tokens com **pouca aleatoriedade** (curtos, com partes estáticas) podem ser **brute-forceados**, como em tokens de reset de senha.

**Exemplo — 4 caracteres:**

**![b5340390632f600d50f2aba03d2cda77.png](../../../_resources/b5340390632f600d50f2aba03d2cda77.png)**

`Set-Cookie: session=a5fd`

Enumerável por completo.

**Exemplo — 32 chars com partes fixas:**

![69416ae9de15e710f3f9b2e2abb25556.png](../../../_resources/69416ae9de15e710f3f9b2e2abb25556.png)  
Tokens observados:

```
2c0c58b27c71a2ec5bf2b4b6e892b9f9
2c0c58b27c71a2ec5bf2b4546092b9f9
2c0c58b27c71a2ec5bf2b497f592b9f9
2c0c58b27c71a2ec5bf2b48bcf92b9f9
2c0c58b27c71a2ec5bf2b4735e92b9f9

```

Padrão: `2c0c58b27c71a2ec5bf2b4` + **4 chars aleatórios** + `92b9f9`.  
Só 4 posições variam ⇒ brute-force dos **4 caracteres** para sequestrar sessões ativas.

**Exemplo — identificador incremental:**

```
141233
141234
141237
141238
141240

```

Sequência previsível ⇒ trivial enumerar sessões passadas/futuras.

> Conclusão: **capture múltiplos tokens** e analise sua entropia/padrões para avaliar risco de brute-force.

* * *

## Attacking Predictable Session Tokens (previsibilidade/tampering)

Mesmo com tamanho “ok”, o processo de geração pode ser **previsível**. Casos simples incluem **dados apenas codificados** no cookie.

**Base64:**

**![52379dc3f23c263ddff3e74794544e72.png](../../../_resources/52379dc3f23c263ddff3e74794544e72.png)**

`Set-Cookie: session=dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy`

Decodificando:

```
tnkr98@htb[/htb]$ echo -n dXNlcj1odGItc3RkbnQ7cm9sZT11c2Vy | base64 -d

user=htb-stdnt;role=user
```

Forjando como admin:

```
echo -n 'user=htb-stdnt;role=admin' | base64
# dXNlcj1odGItc3RkbnQ7cm9sZT1hZG1pbg==

```

Enviar esse cookie permite **acesso administrativo** (se não houver validação/assinatura).

![3d1e265bd121c1445914a6713b060d73.png](../../../_resources/3d1e265bd121c1445914a6713b060d73.png)

**Hex encoding:**

`Set-Cookie: session=757365723d6874622d73746d6e743b726f6c653d75736572`

Forja equivalente:

```
echo -n 'user=htb-stdnt;role=admin' | xxd -p
# 757365723d6874622d7374646e743b726f6c653d61646d696e

```

**Criptografia fraca/mal utilizada:**  
Tokens podem ser **cifrados** em vez de apenas codificados; algoritmos fracos ou **injeção de dados do usuário** no material cifrado podem levar a **elevação de privilégio/bypass**, embora sejam mais difíceis de atacar em *black box* sem o código-fonte da geração do token.