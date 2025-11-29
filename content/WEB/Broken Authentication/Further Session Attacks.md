# Session Fixation

**Session Fixation** ocorre quando a aplicação **não gera um novo token de sessão após o login**. Se o atacante fizer a vítima usar um token escolhido por ele, consegue **assumir a sessão** após a autenticação da vítima.

## Exemplo (cookie `session` + parâmetro `sid`)

1.  O atacante autentica e obtém um token válido (ex.: `a1b2c3d4e5f6`) e depois faz **logout**.
    
2.  Ele induz a vítima a acessar:  
    `http://vulnerable.htb/?sid=a1b2c3d4e5f6`  
    A aplicação responde setando o cookie:
    
    `Set-Cookie: session=a1b2c3d4e5f6`
    
3.  A vítima faz **login**; o browser já tem o cookie com o token do atacante.
    
4.  Como a aplicação **não renova** o token após autenticar, a sessão permanece com `a1b2c3d4e5f6`.
    
5.  O atacante, que conhece esse token, **sequestra a sessão** da vítima.
    

**Mitigação:** sempre **gerar um novo token aleatório após autenticação** (session regeneration).

* * *

# Improper Session Timeout

A aplicação deve definir um **tempo de expiração** (session timeout) para o token. Sem timeout, um token comprometido pode ser reutilizado **indefinidamente**.

- O valor adequado depende do **risco e do negócio**:
    
    - Dados sensíveis (ex.: saúde): **minutos**.
        
    - Redes sociais/uso comum: **horas**.
        

**Essência:** tokens precisam **expirar** em prazo compatível com a sensibilidade do sistema, reduzindo o impacto de sessões sequestradas.