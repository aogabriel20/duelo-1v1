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

## Gráficos 3D em tempo real

Os bonecos e o cenário são 3D de verdade, renderizados em
tempo real no navegador com **Three.js** (biblioteca embutida no
repositório, `three.module.js` + `addons/` — o jogo não depende de
CDN). Os dois pistoleiros são **modelos 3D humanos de verdade**
(GLB, licença livre — ver Créditos abaixo): cada um é um modelo
articulado de corpo inteiro, com cabeça, rosto, braços, pernas e
mãos, vestido com as cores do personagem (Billy de camisa vermelha
e lenço azul, com o chapéu do modelo; Buster de terno branco, com
o chapéu branco do jogo e o violão nas costas), com revólver 3D na
mão, luz e sombra reais do sol do sertão — e o movimento usa as
mesmas 9 sequências quadro a quadro de antes (parado, andando, tiro
no tronco, tiro na cabeça, queda, no chão, morte em pé, morte no
chão, comemoração), mais agachar e pular. O cenário de Canudos
também é 3D: chão de terra vermelha com relevo, mandacaru,
xique-xique, caatinga, serra ao longe, igreja em ruínas e casinhas
de taipa, com poeira flutuando no ar. Se o navegador não tiver
WebGL, o jogo avisa na tela e continua na versão 2D.

## Créditos dos modelos 3D

Os dois pistoleiros são humanos realistas gerados com **MakeHuman /
MPFB 2** (makehumancommunity.org), em domínio público (**CC0 1.0**),
publicados no projeto **vsim** (github.com/kunalkushwaha/vsim,
`packages/assets/library/`), com pele e roupas do pacote CC0
`makehuman_system_assets`:

- **Billy Poeira** — "man" (MPFB 2, CC0), camisa pintada de vermelha
  na textura, calça escura.
- **Buster Scruggs** — "suited" (MPFB 2, CC0), terno de geometria de
  roupa de verdade, pintado de branco na textura.

Os modelos foram adaptados para o jogo (ossos renomeados para os nomes
canônicos, camisa vermelha e terno branco) e as animações do jogo
(parado, andar, tiro, queda, morte, agachar, pular e reação ao tiro
por região) dirigem os ossos deles em tempo real, com as animações
próprias dos modelos (idle, walk, wave) como base. Se os modelos
novos falharem, o jogo tenta os modelos anteriores (Quaternius,
"Farmer"/"Adventurer", CC0, via Poly Pizza — `billy-q.gltf` e
`buster-q.gltf`). Three.js é MIT (https://threejs.org).

## Arquivos

- `index.html` — o jogo inteiro (arte, sons, mecânica e online).
- `three.module.js` — biblioteca 3D (Three.js r160), embutida para o jogo não depender de CDN.
- `addons/` — GLTFLoader, BufferGeometryUtils e SkeletonUtils (Three.js r160).
- `billy.gltf` — modelo 3D humano do Billy Poeira ("man", MakeHuman/MPFB 2, CC0).
- `buster.gltf` — modelo 3D humano do Buster Scruggs ("suited", MakeHuman/MPFB 2, CC0).
- `billy-q.gltf` / `buster-q.gltf` — modelos anteriores (Quaternius, CC0), usados só se os novos falharem.
