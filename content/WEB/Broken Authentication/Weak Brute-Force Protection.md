Depois de entender diferentes ataques de brute-force contra autenticação, esta seção aborda **mecanismos de proteção** (rate limit e CAPTCHA) e como podem ser **burlados** quando mal implementados.

## Rate Limits

**Rate limiting** controla a taxa de requisições para evitar sobrecarga, indisponibilidade e **brute-force**. Ao atingir o limite, o servidor pode **degradar** (aumentar o tempo de resposta) ou **bloquear** temporariamente o cliente, tornando o ataque inviável.

- Deve **atingir apenas o atacante**, não usuários legítimos (evitar DoS involuntário).
    
- Implementações comuns usam o **IP de origem**; porém, com *middleboxes* (proxy reverso, load balancer, cache), o IP visto é o do **intermediário**.
    
- Para contornar isso, alguns sistemas confiam em cabeçalhos como **`X-Forwarded-For`** — o que abre brecha: o atacante pode **forjar** esses cabeçalhos e **driblar** o rate limit variando o valor a cada requisição.
    
- Esse tipo de falha aparece no mundo real (ex.: **CVE-2020-35590**).
    

## CAPTCHAs

**CAPTCHA** força interação humana para bloquear *bots* (letras distorcidas, seleção de objetos, puzzles), reduzindo **automação** de ataques (spam, criação de contas falsas, brute-force em logins).  
Desafios:

- **Usabilidade/acessibilidade** (ex.: usuários com deficiência visual/cognitiva).
    
- **Implementações falhas** que **exponham a solução** na resposta (ex.: HTML/inspector revelando a resposta).
    
- Crescente disponibilidade de **resolvedores automáticos** (inclusive **ferramentas open-source** e soluções **baseadas em IA** com reconhecimento de imagem/voz), que diminuem a eficácia de CAPTCHAs mal configurados.