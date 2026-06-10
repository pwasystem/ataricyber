# Regras do Workspace: Especialista em Criação de Jogos 3D Locais

Esta regra orienta o agente de IA do Google Antigravity sobre como criar, modificar ou planejar novos jogos no workspace. Ela deve ser seguida rigidamente para garantir consistência técnica e estética com os jogos existentes do projeto (`index.html`, `missel.html`, `enduro.html`).

## 1. Ativação da Regra
Esta regra deve ser ativada e aplicada sempre que o usuário solicitar:
*   "Criar um jogo" ou "Desenvolver um jogo".
*   "Criar um novo projeto de jogo".
*   "Fazer um remake tridimensional de [Jogo Clássico]".
*   Modificar ou estender jogos locais existentes.

---

## 2. Tecnologias Obrigatórias e Restrições
O jogo gerado deve rodar de forma totalmente autônoma e offline em ambiente local. Não utilize dependências de backend ou integrações na nuvem.

*   **HTML único (SPA):** Todo o código (HTML, CSS e JavaScript) deve residir em um **único arquivo HTML** localizado na raiz do projeto (ex: `caca_espacial.html`).
*   **CSS3 Vanilla:** Estilização pura para a interface do jogo.
    *   **Proibido:** TailwindCSS, Bootstrap ou frameworks CSS semelhantes, a menos que o usuário solicite explicitamente.
    *   **Aparência:** Estética cyberpunk neon de alta qualidade. Use variáveis customizadas para paleta de cores (Cyan, Pink, Green, Orange, Yellow).
    *   **Interface:** Aplique glassmorphism moderno (`backdrop-filter: blur(12px)`) para HUD e overlays de menus, botões e telas de Game Over.
    *   **Controles Virtuais:** Sempre implemente botões de toque virtuais integrados na tela para dispositivos móveis (D-pad e botões de ação), controlados via propriedades de CSS responsivo e `@media` queries. Eles devem ficar ocultos em computadores e aparecer somente em telas móveis/touch.
*   **Three.js (r128):** Importe a biblioteca Three.js via CDN:
    ```html
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    ```
    *   **Modelagem 3D Procedural:** Todos os modelos tridimensionais (jogador, inimigos, projéteis, cenários) devem ser construídos programaticamente usando geometrias primitivas do Three.js (`BoxGeometry`, `ConeGeometry`, `CylinderGeometry`, `SphereGeometry`, etc.) e materiais avançados (como `MeshPhysicalMaterial` com `clearcoat` e `transmission`, ou `MeshStandardMaterial` com emissivos para luzes neon).
    *   **Sem Assets Externos:** É estritamente proibido carregar texturas de arquivos locais ou modelos externos (`.gltf`, `.obj`). O jogo deve ser auto-suficiente.
*   **Web Audio API Nativa:** Toda a sonorização do jogo deve ser gerada e sintetizada proceduralmente em tempo real. Não utilize arquivos de som externos (`.mp3`, `.wav`).
    *   Implemente uma classe gerenciadora de áudio (ex: `SomJogo`) com inicialização segura após o primeiro clique do usuário (`AudioContext`).
    *   **Som do Motor (se aplicável):** Oscilador contínuo do tipo `sawtooth` ou `triangle` em baixa frequência, variando dinamicamente a frequência e o filtro de passa-baixas com base na velocidade do jogador.
    *   **Efeitos de Tiro/Ação:** Varredura rápida de frequência descendente ou ascendente usando osciladores.
    *   **Explosões:** Ruído branco dinâmico criado em um `AudioBuffer` e passado por um filtro passa-baixa decrescente para simular o estrondo de explosão.
    *   **Recargas/Colecionáveis:** Pequenos bips ou cliques senoidais agudos.
    *   **Música de Fundo (Sequenciador):** Loop sequenciador baseado em tempo de áudio real (`ctx.currentTime`) para tocar uma batida eletrônica simples (Bumbo de queda de frequência, Chimbal de ruído metálico filtrado, e Linha de Baixo sintetizada com oscilador dente de serra).

---

## 3. Padrão de Organização do Código
O arquivo HTML do jogo deve seguir a seguinte estrutura de blocos e comentários:

1.  **Cabeçalho HTML:** Metadados responsivos, fontes do Google (ex: `Orbitron` para títulos e `Outfit` para textos), e a folha de estilos interna (`<style>`).
2.  **Layout da Interface (HUD e Overlays):**
    *   HUD Superior/Inferior: Painéis de pontuação, vidas, energia/combustível estilizados em glassmorphism.
    *   Controles Mobile: Divs contendo os botões virtuais ocultos por padrão.
    *   Telas Overlay: Tela de Menu Inicial, Tela de Vitória/Transição de Fase e Tela de Game Over.
3.  **Scripts do Jogo (`<script>`):**
    *   **Classe de Áudio:** Sintetizador e sequenciador Web Audio API.
    *   **Configurações Globais:** Objeto literal `CONFIG` centralizando constantes de jogo (Velocidades, Consumos, Dimensões, Câmera offsets).
    *   **Gerador Determinístico:** Algoritmo LCG baseado em semente (`SeededRNG`) para geração consistente do mapa infinito ou posicionamento de obstáculos.
    *   **Geradores de Modelos 3D:** Funções ou classes de utilidades que criam e agrupam geometrias primitivas tridimensionais do Three.js para compor o visual dos objetos (caças, navios, estradas, carros, etc.).
    *   **Motor/Lógica de Loop:** A classe principal do jogo que gerencia o loop de animação (`requestAnimationFrame`), atualiza posições dos objetos, processa a câmera, gerencia colisões (AABB ou distância radial) e atualiza o estado da partida (vidas, pontuação, combustível).
    *   **Eventos de Entrada:** Listeners de teclado (WASD, Setas, Espaço) e binds de eventos de clique/touch para os botões virtuais do celular.

---

## 4. Estética Visual de Alto Nível
O jogo deve passar uma sensação premium de design.
*   **Cores Neon Cyberpunk:** Combine cores de emissão forte contra o fundo escuro (`#020205` ou `#03030c`). Use sombras (`box-shadow` e `text-shadow`) para simular o brilho do neon na interface gráfica.
*   **Materiais 3D Brilhantes:** Para as malhas tridimensionais, use `MeshStandardMaterial` ou `MeshPhysicalMaterial` com alta refletividade metálica e luzes direcionais que façam as superfícies brilharem. Adicione uma luz ambiente neon fraca e uma ou duas luzes direcionais coloridas para gerar reflexos dinâmicos nas carenagens.
*   **Micro-animações:** Aplique transições suaves de CSS (`transition: all 0.3s ease`) nos botões e painéis, e efeitos de oscilação suave 3D (ex: os barcos, helicópteros ou balões devem balançar suavemente no eixo Y de forma senoidal).
