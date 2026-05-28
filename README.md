<h1 align="center">🗡️ Sombra e Eco (Tower of Echoes)</h1>

<p align="center">
  <img src="banner.png" alt="Banner Sombra e Eco" width="350">
</p>

> Um jogo de plataforma 2D focado em combates intensos contra chefes (*Boss Rush*), exigindo reflexos rápidos para *parry* e gestão de estamina. Desenvolvido durante uma Game Jam.

<p align="center">
  <img src="https://img.shields.io/badge/JavaScript-ES6+-yellow" alt="JavaScript">
  <img src="https://img.shields.io/badge/Phaser-3.0-blue" alt="Phaser">
  <img src="https://img.shields.io/badge/PET_Game_Jam_UFES-2%C2%BA_Lugar-gold" alt="Trophy">
</p>

🎮 **[Jogue no navegador via itch.io](https://yorguts.itch.io/tower-of-echoes)** 🎬 **[Assista ao Trailer de Gameplay](https://www.youtube.com/shorts/zkL-cjqfOf8)**

## 📋 Sobre o Projeto

Este projeto foi a nossa submissão para a **PET Game Jam da UFES**, conquistando o 2º lugar. O objetivo principal foi criar uma experiência punitiva e justa, onde o jogador precisa decorar padrões e agir no tempo exato.

Como o tempo de desenvolvimento era restrito, a equipe optou por focar 100% no polimento das hitboxes, colisões e na inteligência dos chefes. Devido a isso, o jogo acabou sendo deixado sem efeitos sonoros, a recomendação é jogar escutando a sua própria *playlist*.

### 👥 Equipe de Desenvolvimento
Projeto construído pelo trio:
* **[Gustavo](https://github.com/gustsoprani)**
* **[Tárcio](https://github.com/tarciofernandes14)**
* **[Marcos](https://github.com/MarcosBrunetti)**

---

## ⚙️ Destaques Técnicos e Lógica

O código-fonte foi estruturado utilizando módulos ES6 e empacotado com Vite para otimização web. O projeto brilha na sua arquitetura de combate, baseada fortemente em Máquinas de Estados Finito (FSM) e reatividade de Inteligência Artificial.

### 🤺 1. Máquina de Estados do Jogador (Player FSM)
O jogador possui um controlador de estado rígido que previne o cancelamento indevido de animações e *race conditions*. A mobilidade fluida é garantida por transições matemáticas de velocidade e detecção de solo (Grounded).

```mermaid
flowchart TD
    %% Nós de Movimento
    IDLE((IDLE)) <-->|Input Direcional / Parar| RUN((RUN))
    IDLE -->|Input Pulo| JUMP((JUMP))
    RUN -->|Input Pulo| JUMP
    JUMP -->|Ápice da gravidade| FALL((FALL))
    FALL -->|Toca no chão| IDLE

    %% Ações de Combate
    IDLE -.->|J, K, L, E| Acoes{Ações de Combate}
    RUN -.->|J, K, L, E| Acoes
    
    Acoes --> ATK[Ataque / Combo Especial]
    Acoes --> DASH[Dash: Esquiva Invulnerável]
    Acoes --> PARRY[Parry: Bloqueio Perfeito]
    Acoes --> HEAL[Cura: Consome Cargas]

    %% Funil de Retorno (Simplificando as setas)
    ATK & DASH & PARRY & HEAL --> FimAnim([Fim da Animação])
    FimAnim --> IDLE

    %% Sistema de Dano
    IDLE & RUN & ATK & HEAL ===>|Recebe Golpe| HURT((HURT))
    HURT -->|Fim do Stun| IDLE
    HURT -->|HP = 0| DEAD((MORTE))

    %% Estilos
    style IDLE fill:#2d3436,stroke:#74b9ff,stroke-width:2px,color:#fff
    style HURT fill:#d63031,stroke:#ff7675,stroke-width:2px,color:#fff
    style DEAD fill:#000,stroke:#d63031,stroke-width:4px,color:#fff
    style FimAnim fill:#636e72,stroke:#b2bec3,stroke-width:2px,color:#fff
```

### 🧠 2. Inteligência Artificial dos Chefes (Boss Mechanics)
Os chefes (como Akane e Mestre Jubei) compartilham um núcleo de IA agressivo, desenhado para punir erros de posicionamento e evitar que o jogador vença apenas "esmagando botões" (*Anti-Stunlock*).

```mermaid
flowchart TD
    Inicio((Decisão de IA)) --> Dist{Qual a distância?}
    
    %% Árvore de Distância
    Dist -->|Longe| CHASE[Persegue o Jogador]
    Dist -->|Perto| ATK{Sorteio de Ataque}
    CHASE -->|Chegou perto| ATK
    
    ATK -->|RNG / Cooldown| COMBO[Combo Básico / Dash]
    ATK -->|RNG / Cooldown| SKILL[Habilidade Especial Unblockable]
    
    %% Reatividade (Estus Punish)
    Inicio -.->|Detecta Jogador Curando| PUNISH[Ataque Punitivo Imediato]
    PUNISH --> ATK

    %% Árvore de Dano e Defesa
    Dano((Chefe Recebe Golpe)) --> Check{Estado Atual?}
    
    Check -->|Atacando| ARMOR[Super Armor: Ignora Stun e segue ataque]
    Check -->|Idle / Chase| STUN[Sofre Stun e interrompe ação]
    Check -->|Já no Stun| BREAKER[Combo Breaker: Fuga imediata]
    
    %% Progressão
    STUN --> HP{HP chegou a 0?}
    HP -->|Sim, tem vidas restando| ENRAGE[Restaura HP -> Modo Berserk]
    HP -->|Sim, última vida| MORTE((Execução / Morte))

    %% Estilos
    style Inicio fill:#2d3436,stroke:#00b894,stroke-width:2px,color:#fff
    style Dano fill:#d63031,stroke:#ff7675,stroke-width:2px,color:#fff
    style MORTE fill:#000,stroke:#d63031,stroke-width:4px,color:#fff
```

---

## 🚀 Repositório e Execução Local

**⚠️ AVISO DE LICENCIAMENTO:** *Os assets visuais (spritesheets, tilesets e backgrounds) utilizados na versão final possuem licenças pagas de uso restrito. Por questões de direitos autorais, a pasta `public/assets` **não está inclusa** neste repositório público. O código-fonte está preservado aqui exclusivamente para fins de estudo de arquitetura, lógica e portfólio.*

Se o projeto for clonado e executado localmente, o terminal funcionará perfeitamente, **mas o jogo renderizará uma tela preta** devido à ausência das imagens originais. 

Para inspecionar a estrutura do bundler e a lógica:

1. Clone o repositório:
   ```bash
   git clone [https://github.com/gustsoprani/towerofechoes.git](https://github.com/gustsoprani/towerofechoes.git)
   ```
2. Instale as dependências (Phaser e Vite):
   ```bash
   npm install
   ```
3. Inicie o servidor local:
   ```bash
   npm run dev
   ```

*Para ter a experiência visual completa, utilize o link do [itch.io](https://yorguts.itch.io/tower-of-echoes).*
