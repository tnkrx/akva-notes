Implementações de autenticação podem falhar quando **dependem da presença/valor de um parâmetro HTTP**, abrindo brechas de **bypass de autenticação/autorização** e **elevação de privilégio**. Esse cenário se relaciona a problemas como **IDOR**.

![333c83ed3db355840cbbc433e8633bc0.png](../../../_resources/333c83ed3db355840cbbc433e8633bc0.png)

## Parameter Modification

Com credenciais de `htb-stdnt`, após o login somos redirecionados para **`/admin.php?user_id=183`**. No navegador, faltam privilégios (parte dos dados não carrega).

Ao **remover o parâmetro `user_id`** e requisitar `/admin.php`, somos redirecionados de volta ao **/index.php**, mesmo com `PHPSESSID` válido.  
Conclusão: o **`user_id` está atrelado à autenticação**.

![ce1bcb9b56dfe2214e89cf7c90a52487.png](../../../_resources/ce1bcb9b56dfe2214e89cf7c90a52487.png)

Acessando **diretamente** `/admin.php?user_id=183`, obtemos **200 OK** (conteúdo carregado), o que indica **bypass de autenticação** via parâmetro.

![19dc49c81f355b9e763abde8977b8567.png](../../../_resources/19dc49c81f355b9e763abde8977b8567.png)

Pelo nome do parâmetro, **`user_id`** define **qual usuário** acessa a página. Se for possível **adivinhar/bruteforcear** o `user_id` de um **administrador**, podemos **especificá-lo** no parâmetro e obter **privilégios administrativos** (revelando informações de admin).