┌──(root㉿jeffhack)-[~/projects]

└─$ more http_brute.py

    ╔═╗╔═╗╔╦╗╔═╗  ╦╔═╗  ╦  ╔═╗╦ ╦
    ║  ║ ║ ║║║╣   ║╚═╗  ║  ╠═╣║║║
    ╚═╝╚═╝═╩╝╚═╝  ╩╚═╝  ╩═╝╩ ╩╚╩╝

    ╔════════════════════════════════════════════════╗
    ║                                                ║
    ║  "Code is law in cyberspace"                   ║
    ║  - Lawrence Lessig                             ║
    ║                                                ║
    ║  >> The rules are written in code              ║
    ║  >> Code defines reality                       ║
    ║  >> Master the code, control the system        ║
    ║                                                ║
    ╚════════════════════════════════════════════════╝

---

# brute_http — README completo (documentação do projeto para fins educacionais)

> Uso exclusivo em ambientes autorizados (sua máquina, VMs, DVWA, Juice Shop).
> Qualquer uso não autorizado é ilegal. Leia a seção AVISO.

---

## 1) Título

**brute_http — Prova de Conceito Educacional (lab local)**

---

## 2) AVISO legal (obrigatório)

* Teste **apenas** em hosts que você possua ou com autorização escrita.
* Uso indevido é crime.
* Este repositório é didático: foca em entender ataques para criar defesas.

---

## 3) Resumo (visão geral)

* Projeto: PoC simples que demonstra técnica de tentativa de autenticação por força bruta em formulários HTTP via POST.
* Propósito educacional: ensinar detecção, mitigação e práticas seguras (rate-limit, logging e whitelist).
* Não contém instruções para uso malicioso; contém explicações técnicas, riscos e contramedidas.

---

## 4) Conteúdo do repositório (estrutura de arquivos recomendada)

* `README.md` — este arquivo.
* `docs/` — material didático expandido (slides, comparativos).
* `dicionario.txt` — wordlist de exemplo (apenas para lab).
* `brute_http_safe.py` — *opcional* script seguro (não incluído aqui).
* `requirements.txt` — bibliotecas necessárias para o lab.
* `lab/` — docker-compose com DVWA/Juice Shop (opcional).
* `LICENSE` — licença (ex.: MIT, deixar claro uso educacional).
* `CHANGELOG.md` — histórico de mudanças.

---

## 5) Requisitos (ambiente de laboratório)

* Python 3.x (opcional, se for executar PoC seguro).
* Ambiente isolado: VM ou container com DVWA/OWASP Juice Shop.
* Wordlist de teste (`dicionario.txt`).
* Ferramentas de inspeção: browser devtools, Burp/mitmproxy (para estudo).
* Acesso à rede local ou à VM no host permitido.

---

## 6) Como testar (lab seguro) — passos resumidos

1. Instale e execute DVWA ou Juice Shop em ambiente local/container.
2. Confirme que o alvo é `localhost` ou IP da VM.
3. Carregue `dicionario.txt` com poucas entradas (ex.: 10–50) para testes iniciais.
4. Execute o PoC apenas apontando para o host autorizado.
5. Observe logs do servidor da aplicação de teste e detecte padrões de acesso.
6. Aplique mecanismos de defesa no alvo e repita testes para avaliar eficácia.

---

## 7) Explicação didática do funcionamento (blocos lógicos — sem código)

### 7.1 Inicialização / ambiente

* Objetivo: declarar configurações básicas do experimento (target URL, arquivo de wordlist, taxas, timeouts, arquivo de log).
* Estratégia educativa: deixar valores claros e visíveis para mostrar que todo teste precisa de parâmetros controlados.

### 7.2 Validação do alvo (whitelist)

* Objetivo: garantir que o alvo é autorizado (ex.: `localhost`, IPs de laboratório).
* Por quê: evita execução acidental contra serviços externos.
* Método: comparar hostname/IP com lista permitida e abortar se não estiver na whitelist.

### 7.3 Abertura e leitura da wordlist

* Objetivo: carregar as credenciais candidatas (uma por linha).
* Boas práticas: validar existência do arquivo, tratar encoding, ignorar linhas vazias.
* Risco: wordlists grandes podem gerar tráfego massivo — sempre começar pequeno.

### 7.4 Reuso de conexão (session)

* Objetivo: utilizar a mesma conexão HTTP para eficiência (reduz overhead).
* Didática: demonstra diferença entre criar nova conexão a cada requisição vs. reuso.

### 7.5 Limpeza das entradas

* Objetivo: sanitizar cada linha (remover `\n`, espaços).
* Por quê: evitar payloads inválidos e falsos positivos.

### 7.6 Montagem do payload

* Objetivo: construir dados do formulário (nomes de campos típicos: `uname`, `pass`), entendendo que em alvos reais os nomes variam.
* Didática: mapear campos do form com devtools para saber o que enviar.

### 7.7 Envio da requisição (POST) com timeout

* Objetivo: executar a tentativa de autenticação; timeout previne bloqueios indefinidos.
* Boas práticas: tratar exceções de rede e aplicar backoff em erros.

### 7.8 Detecção de sucesso (heurística)

* Objetivo: identificar quando uma tentativa foi bem-sucedida.
* Exemplos de heurísticas educativas:

  * Presença de texto específico na resposta (ex.: "Logout").
  * Redirecionamento para dashboard (código 302).
  * Mudança no conteúdo do HTML (regex).
* Observação: heurística deve ser configurável e validada no lab.

### 7.9 Logging e auditoria

* Objetivo: registrar tentativas, falhas e erros em níveis (INFO, WARNING, DEBUG).
* Boas práticas: não logar credenciais em texto claro em ambientes de produção; no lab é aceitável, mas marcar como sensível.

### 7.10 Rate limiting / delays

* Objetivo: limitar taxa de requisições para reduzir impacto e simular comportamento humano.
* Estratégias avançadas: exponential backoff, jitter aleatório.

### 7.11 Encerramento e limpeza

* Objetivo: fechar conexões e finalizar logs corretamente.

---

## 8) Estratégias de defesa (educacional)

* Implementar **rate limiting** no servidor (por IP/usuário).
* Contagem de tentativas e bloqueio temporário (ex.: 5 tentativas -> bloqueio 15 min).
* CAPTCHA após número de tentativas.
* Monitoramento/alerta: detectar padrões de brute force e alertar.
* Uso de autenticação multifator (MFA).
* Auditoria de logs e correlação (SIEM).
* Implementar mecanismos de account lockout com aviso ao usuário.

---

## 9) Checklist de segurança antes de rodar (pré-execução)

* [ ] O alvo está na minha whitelist / sob minha posse?
* [ ] Wordlist pequena para testes iniciais?
* [ ] Ambiente isolado (VM/container)?
* [ ] Logs configurados e espaço em disco suficiente?
* [ ] Permissões e autorizações documentadas?

---

## 10) Exemplos de cenários de laboratório (sugestões)

* DVWA (WebApp Security VM) rodando em `http://127.0.0.1:80`.
* OWASP Juice Shop em container local.
* Aplicação Flask simples que registra tentativas e responde com "Logout" ao autenticar.

---

## 11) Apresentação visual no Git (sugestões rápidas)

* Use ASCII-art no topo (já incluído).
* Badges: licença, build (se houver lab CI), status do docker-compose.
* Seções bem curtas: Sobre → AVISO → Como testar → Explicação técnica → Defesa → Contribuir.
* Imagens: diagrama de fluxo (opcional) mostrando: Wordlist → Payload → POST → Heurística → Resultado/Log.

---

## 12) Contribuições e conduta

* Contribuições aceitas apenas se focarem em educação, defesa e segurança responsável.
* Ao contribuir, inclua testes no lab e explique impacto ético.
* Issues abusivas serão fechadas.

---

## 13) Licença

* Recomenda-se MIT com adição clara: “uso educacional e responsabilidade do usuário”.
* Exemplo de frase na LICENSE: “Uso apenas para fins educacionais em ambientes autorizados. O autor não se responsabiliza por uso indevido.”

---

## 14) Referências úteis (para estudo)

* OWASP Top 10 — ler sobre autenticação e brute force.
* DVWA / OWASP Juice Shop — plataformas de laboratório.
* Artigos sobre rate limiting e account lockout.

---

## 15) Notas finais (curtas)

* Este README cobre o fluxo completo: propósito, segurança, explicação técnica por blocos, checklist e defesas.
* Se quiser, transformo esta documentação em `README.md` pronto para commit com formatação final e badges.
