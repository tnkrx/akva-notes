## 1\. Knowledge-Based Authentication (Conhecimento)

Baseia-se em **algo que o usuário sabe** para provar sua identidade.  
Exemplos:

- **Senhas**
    
- **Frases secretas**
    
- **PINs**
    
- **Respostas a perguntas de segurança**
    

## 2\. Ownership-Based Authentication (Propriedade)

Baseia-se em **algo que o usuário possui**.  
O usuário comprova identidade mostrando posse de um objeto/dispositivo físico.  
Exemplos:

- **Cartões de identificação (ID cards)**
    
- **Tokens de segurança**
    
- **Smartphones com apps autenticadores**
    

## 3\. Inherence-Based Authentication (Inerência)

Baseia-se em **algo que o usuário é ou faz**, como características biométricas.  
Exemplos:

- **Impressões digitais**
    
- **Reconhecimento facial**
    
- **Reconhecimento de voz**
    
- **Assinatura biométrica**
    

| Knowledge | Ownership | Inherence |
| --- | --- | --- |
| Password | ID Card | Fingerprint |
| PIN | Security Token | Facial Pattern |
| Security Question | Authenticator App | Voice Recognition |

* * *

## Single-Factor vs Multi-Factor Authentication

- **Single-Factor Authentication (SFA):** usa apenas **um método** de autenticação.  
    Exemplo: login com senha (baseado apenas em *knowledge*).
    
- **Multi-Factor Authentication (MFA):** combina **métodos de categorias diferentes**, elevando a segurança.  
    Exemplo: senha (**knowledge**) + código TOTP no app (**ownership**).
    

* * *

# Attacks on Authentication

Os ataques a sistemas de autenticação podem ser classificados conforme o tipo de método utilizado: **knowledge**, **ownership** e **inherence**.

* * *

## 1\. Attacking Knowledge-Based Authentication

A autenticação baseada em **conhecimento** (como senhas, PINs e perguntas de segurança) é a mais comum — e também a mais vulnerável.  
Ela depende de informações **estáticas e previsíveis**, que podem ser:

- **Obtidas** em vazamentos de dados,
    
- **Adivinhadas** por força bruta ou engenharia social,
    
- **Exploradas** por ataques de *phishing*.
    

Conforme as ameaças evoluem, os invasores se tornam mais habilidosos em explorar essas fraquezas por meio de **engenharia social** e **data breaches**.

* * *

## 2\. Attacking Ownership-Based Authentication

A autenticação baseada em **propriedade** (tokens físicos, smart cards, apps autenticadores) é **mais resistente** a ataques comuns como *phishing* ou adivinhação de senhas, pois depende de algo que o usuário **possui fisicamente**.

Entretanto, apresenta desafios como:

- **Custo e logística** de distribuição/gerenciamento de dispositivos;
    
- **Ataques físicos**, como **roubo ou clonagem** do token;
    
- **Ataques criptográficos** contra o algoritmo de autenticação.
    

> Exemplo: clonagem de crachás NFC em locais públicos (transporte, cafés) é um vetor de ataque viável.

* * *

## 3\. Attacking Inherence-Based Authentication

A autenticação baseada em **inerência** (biometria: impressão digital, face, voz) é **conveniente e prática**, pois elimina a necessidade de memorizar senhas ou carregar tokens.  
Melhora a **experiência do usuário** e reduz falhas por senhas fracas ou perdidas.

Mas também traz riscos importantes:

- **Privacidade e segurança dos dados biométricos**;
    
- **Vieses** nos algoritmos de reconhecimento;
    
- E, principalmente, **comprometimento irreversível** em caso de vazamento.
    

> Exemplo: em 2019, uma empresa de **fechaduras inteligentes biométricas** foi invadida, expondo **impressões digitais, padrões faciais, usuários e endereços**.  
> Diferente de senhas (que podem ser trocadas), os usuários **não podem alterar** seus dados biométricos — tornando o impacto permanente.