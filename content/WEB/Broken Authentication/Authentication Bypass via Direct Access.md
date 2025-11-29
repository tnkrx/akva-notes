Vulnerabilidades nesta seção permitem **bypassar completamente** mecanismos de autenticação.

## Acesso direto ao recurso protegido

O método mais simples é requisitar o **recurso protegido** sem estar autenticado. Se a aplicação **não verifica** corretamente a autenticação em cada request, um atacante pode obter dados protegidos.

Exemplo: após login bem-sucedido, usuários são redirecionados para **`/admin.php`**. Se a aplicação confia apenas na página de login, um atacante pode acessar diretamente `/admin.php`.

![1eb7dfc4c9a873c8f8ab4ae6d5b87467.png](../../../_resources/1eb7dfc4c9a873c8f8ab4ae6d5b87467.png)

### Variante comum (redireciona mas vaza conteúdo)

Código PHP para checar autenticação:

```
if(!$_SESSION['active']) {
    header("Location: index.php");
}

```

Ele redireciona para `index.php` quando a sessão não está ativa, **mas não interrompe a execução**. Resultado: a resposta tem **302 Found** com **o HTML completo da página admin no corpo**.

No navegador, o redirecionamento oculta o conteúdo. Porém, interceptando a **resposta** no Burp (Intercept ON → *Do intercept > Response to this request*), é possível **editar o status** de **302** para **200 OK**, e então **Forward**. O navegador renderiza o HTML protegido.

![614b316396724532195cd8152f511e3b.png](../../../_resources/614b316396724532195cd8152f511e3b.png)

![d8c45817138f2993a6719dc079b962ca.png](../../../_resources/d8c45817138f2993a6719dc079b962ca.png)