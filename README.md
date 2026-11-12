# Duelo 1v1

Jogo de duelo no velho oeste em HTML5, para dois jogadores em aparelhos
diferentes — sem cadastro e sem instalar nada. Arte, sons e código são
originais deste projeto (não usa nenhum arquivo do jogo Gunblood).

## Como jogar com um amigo

1. Os dois abrem o mesmo link deste jogo.
2. Um toca em **Criar sala** — aparece um código de **4 letras** na hora.
3. O outro toca em **Entrar numa sala**, digita as 4 letras e toca em
   **Entrar na sala**.
4. Quando aparecer **Conectado**, quem criou a sala toca em
   **Começar o duelo**.
5. Na contagem, os dois deixam o mouse sobre o tambor de balas (canto
   inferior). Depois do **FIRE!**, o mouse controla a altura do braço e
   o clique atira. São 6 balas por rodada; quem vencer 3 rodadas ganha
   o duelo. Também dá para jogar contra o computador.

Se a sala não registrar ou não encontrar, o jogo tenta de novo sozinho
e mostra o motivo na tela (VPN ligada, bloqueador de anúncios ou Wi-Fi
restrito costumam bloquear a conexão das salas — vale tentar a internet
do celular).

## Como o online funciona

- A partida em si roda em **WebRTC direto** entre os dois navegadores
  (canal de dados), com um servidor STUN público do Google só para
  descobrir os endereços — sem TURN e sem servidor de jogo no meio.
- **Vida com um dono só:** o anfitrião é a autoridade da partida — é
  ele quem aplica o dano e confirma a vida, os pontos e as balas dos
  dois (mensagem `hp` após cada tiro), para as duas telas nunca
  divergirem e a vida não trocar de jogador. Cada tiro leva a rodada
  e um id único, e tiros de rodada errada ou repetidos são ignorados.
- A sinalização (troca de oferta/resposta WebRTC) passa por **HTTPS**
  no serviço público **ntfy.sh**: o tópico é `duelo1v1-<CÓDIGO>` e o
  jogo publica e sonda mensagens com `fetch` comum — não usa WebSocket
  nem PeerJS.
- **Privacidade:** toda mensagem de sinalização é cifrada com
  **AES-GCM (WebCrypto)**, com a chave derivada do próprio código de
  4 letras da sala (SHA-256 de `duelo1v1-sala-<código>`). Quem assinar
  o tópico público sem saber o código só vê texto cifrado — sem SDP,
  sem endereços IP e sem dados da conexão. Mensagens velhas de outras
  salas com o mesmo código são ignoradas.
- Sem conta, sem cadastro e sem servidor próprio para manter: basta
  hospedar este `index.html` em qualquer hospedagem estática
  (GitHub Pages, por exemplo).

## Arquivos

- `index.html` — o jogo inteiro (arte, sons, mecânica e online).
