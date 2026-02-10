# Sonic the Hedgehog - Master System
## Documentação Completa para Implementação em Phaser

---

## Índice

1. [Visão Geral do Jogo](#visão-geral-do-jogo)
2. [Mecânicas de Jogo](#mecânicas-de-jogo)
3. [Física e Movimento](#física-e-movimento)
4. [Interface e HUD](#interface-e-hud)
5. [Controles](#controles)
6. [Zonas e Níveis](#zonas-e-níveis)
7. [Inimigos e Obstáculos](#inimigos-e-obstáculos)
8. [Power-ups e Itens](#power-ups-e-itens)
9. [Sistema de Pontuação e Regras](#sistema-de-pontuação-e-regras)
10. [Chaos Emeralds e Fases Especiais](#chaos-emeralds-e-fases-especiais)
11. [Áudio e Música](#áudio-e-música)
12. [Implementação em Phaser](#implementação-em-phaser)
13. [Exportação para Android com Capacitor](#exportação-para-android-com-capacitor)

---

## Visão Geral do Jogo

**Sonic the Hedgehog** para Master System (1991) é uma versão 8-bit exclusiva do clássico jogo de plataforma da SEGA. Diferente da versão do Mega Drive/Genesis, esta versão apresenta:

- **6 Zonas Principais** (cada uma com 3 atos: 2 de plataforma + 1 boss)
- **Gameplay focado em plataforma** ao invés de velocidade pura
- **Física mais simples** adaptada às limitações do hardware 8-bit
- **Chaos Emeralds escondidos nos níveis** (não em fases especiais)
- **Design de níveis mais linear** com foco em precisão

### Diferenças Principais vs Versão 16-bit
- Velocidade máxima mais baixa
- Física de rampa simplificada
- Sem loops completos de 360 graus
- Sem sistema de pontuação (score)
- Emeralds escondidos nos atos ao invés de special stages

---

## Mecânicas de Jogo

### Movimento Básico
- **Andar/Correr**: Aceleração gradual até velocidade máxima
- **Parar**: Desaceleração progressiva com inércia
- **Virar**: Animação de derrapagem ao mudar de direção em alta velocidade
- **Agachar**: Sonic se abaixa (tecla para baixo)
- **Olhar para cima**: Tecla para cima

### Ações Especiais
- **Pulo**: Altura variável baseada no tempo de pressão do botão
- **Spin Dash/Rolagem**: Enrolar em bola durante movimento aumenta velocidade
- **Impulso por molas**: Springs vermelhas e amarelas com diferentes forças
- **Transporte**: Plataformas móveis, teleportes

### Colisão e Dano
- **Com Anéis**: Sonic perde todos os anéis ao ser atingido
- **Sem Anéis**: Sonic perde uma vida
- **Espinhos**: Dano instantâneo
- **Quedas**: Cair em abismos = perda de vida
- **Afogamento**: Sonic morre após ~20 segundos submerso sem ar
- **Tempo Limite**: 9:59 - ultrapassar causa perda de vida

---

## Física e Movimento

### Sistema de Posicionamento
- **Subpixel Precision**: Movimento calculado com precisão subpixel para transições suaves
- **Posição atualizada a cada frame** baseada em: posição atual + velocidade + aceleração

### Velocidade e Aceleração
```
Velocidade Máxima (Horizontal): ~6 pixels/frame
Aceleração ao Correr: Gradual
Desaceleração ao Parar: Progressiva
Gravidade: ~0.5 pixels/frame²
Velocidade de Pulo: -6.5 pixels/frame (inicial)
```

### Física de Rampa
- **Subida**: Sonic perde velocidade gradualmente
- **Descida**: Ganha velocidade (momentum)
- **Rolagem em rampa**: Mantém momentum melhor que corrida normal
- **Nota**: Física mais simples que versão Genesis - sem rotação 360°

### Controle no Ar
- **Ajuste Horizontal**: Limitado durante o pulo
- **Influência do Momentum**: Velocidade inicial afeta trajetória
- **Mais restritivo**: Comparado à versão 16-bit para aumentar desafio

### Colisão em Alta Velocidade
- Sistema verifica colisão a cada frame
- Velocidade moderada previne "passar através" de objetos sólidos
- Menos problemático que na versão Genesis devido a velocidades menores

---

## Interface e HUD

### Elementos do HUD (Topo da Tela)

#### Anéis (Top Left)
- Mostra número de anéis coletados
- Formato: Número simples (max: 999)
- Função: Proteção contra um hit

#### Vidas (Top Center)
- Ícone do Sonic + número
- Começa com 3 vidas
- Game Over ao perder todas

#### Timer (Top Right)
- Formato: M:SS (ex: 1:23)
- Conta progressivamente de 0:00
- Time Over aos 9:59 = perda de vida

#### Score/Pontuação
- **NÃO PRESENTE** na versão Master System!
- Esta é uma diferença única desta versão

### Estilo Visual do HUD
- Design minimalista e funcional
- Fontes pequenas e limpas
- Apenas informações vitais
- Permanece fixo no topo da tela
- Sem ornamentação excessiva

---

## Controles

### Master System Original
```
D-Pad:
  ← → : Mover esquerda/direita
  ↑   : Olhar para cima
  ↓   : Agachar / Spin (se em movimento)

Botão 1: Pular
Botão 2: Não utilizado (em Sonic 1)
```

### Adaptação para Touch/Mobile
```
Virtual D-Pad: Movimento
Botão A (direita): Pular
Duplo toque: Possível ação especial
Swipe: Movimento rápido alternativo
```

### Implementação Recomendada em Phaser
- Suporte simultâneo: Teclado, Gamepad, Touch
- Cursores para movimento
- Espaço/Z/Botão para pulo
- Touch controls responsivos para mobile

---

## Zonas e Níveis

### Estrutura de Níveis
Cada zona tem **3 Atos**:
- **Ato 1**: Nível de plataforma (Chaos Emerald pode estar escondido)
- **Ato 2**: Nível de plataforma (Chaos Emerald pode estar escondido)  
- **Ato 3**: Boss fight contra Dr. Robotnik

### Lista de Zonas

#### 1. Green Hill Zone
**Tema**: Pradaria verdejante com flores e cachoeiras
- **Inimigos**: Motobug, Buzz Bomber, Crabmeat
- **Obstáculos**: Espinhos, molas, plataformas móveis
- **Características**: Zona introdutória, relativamente fácil
- **Emerald**: Escondido no Ato 2

#### 2. Bridge Zone
**Tema**: Pontes sobre água
- **Inimigos**: Chopper, Buzz Bomber, Crabmeat
- **Obstáculos**: Água, auto-scroll, pontes que desmoronam
- **Características**: **Ato 2 tem auto-scroll** (primeiro em Sonic!)
- **Emerald**: Escondido no Ato 1

#### 3. Jungle Zone
**Tema**: Selva tropical com cachoeiras
- **Inimigos**: Monkey Dude, Buzz Bomber
- **Obstáculos**: Cipós, cachoeiras, troncos
- **Características**: Muitas plataformas verticais
- **Emerald**: Escondido no Ato 2

#### 4. Labyrinth Zone
**Tema**: Ruínas subaquáticas
- **Inimigos**: Jaws, Burrobot
- **Obstáculos**: Água (afogamento!), espinhos, bolhas de ar
- **Características**: Zona mais difícil, mecânica de afogamento
- **Emerald**: Escondido no Ato 2

#### 5. Scrap Brain Zone
**Tema**: Fábrica industrial
- **Inimigos**: Orbinaut, Burrobot
- **Obstáculos**: Eletricidade, fogo, prensas
- **Características**: Armadilhas complexas, timing preciso
- **Emerald**: Escondido no Ato 2

#### 6. Sky Base Zone
**Tema**: Base aérea de Robotnik
- **Inimigos**: Orbinaut, flying robots
- **Obstáculos**: Canhões, raios, plataformas com hélices
- **Características**: Zona final, desafio máximo
- **Emerald**: Escondido no Ato 2

### Mecânicas de Nível

#### Checkpoints
- Postes com placa giratória
- Salvam progresso no ato
- Retorno aqui ao perder vida (se ativado)

#### Fases Especiais (Bonus Stages)
- Acessadas com 50+ anéis ao completar ato
- Estilo pinball com bumpers e springs
- Objetivo: Coletar anéis e continues
- **NÃO contêm Chaos Emeralds** (diferente do Genesis)

---

## Inimigos e Obstáculos

### Badniks (Robôs Inimigos)

#### Motobug
- **Zona**: Green Hill
- **Comportamento**: Rola horizontalmente
- **Padrão**: Movimento linear em uma direção
- **Derrota**: Pulo em cima

#### Buzz Bomber
- **Zona**: Várias zonas
- **Comportamento**: Voa e atira projéteis
- **Padrão**: Patrulha aérea, atira ao avistar Sonic
- **Derrota**: Pulo em cima (cuidado com tiros)

#### Crabmeat
- **Zona**: Green Hill, Bridge
- **Comportamento**: Caminha e atira projéteis
- **Padrão**: Patrulha horizontal, atira periodicamente
- **Derrota**: Pulo em cima

#### Chopper
- **Zona**: Bridge
- **Comportamento**: Peixe robótico que pula da água
- **Padrão**: Emerge e submerge em arco
- **Derrota**: Pulo enquanto no ar

#### Jaws
- **Zona**: Labyrinth
- **Comportamento**: Patrulha subaquática
- **Padrão**: Movimento horizontal na água
- **Derrota**: Spin attack

#### Burrobot
- **Zona**: Labyrinth, Scrap Brain
- **Comportamento**: Escava no chão
- **Padrão**: Emerge e submerge
- **Derrota**: Timing preciso ao emergir

#### Orbinaut
- **Zona**: Scrap Brain, Sky Base
- **Comportamento**: Flutua com bolas orbitando
- **Padrão**: Movimento errático
- **Derrota**: Difícil - atacar o centro

### Obstáculos Ambientais

#### Espinhos (Spikes)
- Dano instantâneo ao toque
- Fixos ou retráteis
- Padrões: Solo, teto, paredes

#### Água
- Sonic pode nadar mas se move lentamente
- **Afogamento**: Após ~20 segundos submerso
- **Bolhas de Ar**: Restauram oxigênio
- **Contador de Afogamento**: Música muda quando crítico

#### Plataformas Móveis
- Movimento horizontal ou vertical
- Algumas caem ao pisar
- Outras com padrões circulares

#### Molas (Springs)
- **Vermelhas**: Impulso forte
- **Amarelas**: Impulso moderado
- Direções: Para cima, diagonal, horizontal

#### Eletricidade e Fogo
- Presente em Scrap Brain e Sky Base
- Dano ao toque
- Padrões intermitentes

#### Prensas (Crushers)
- Scrap Brain Zone
- Esmagam com timing
- Instakill se pego

---

## Power-ups e Itens

### Monitors (Item Boxes)

Caixas coloridas que concedem power-ups quando quebradas (pulo em cima ou spin).

#### Monitor de Anéis (Ring Monitor)
- **Ícone**: Anel dourado
- **Efeito**: +10 anéis
- **Comum**: Sim

#### Monitor de Escudo (Shield)
- **Ícone**: Bolha azul
- **Efeito**: Protege de 1 hit (não protege de quedas ou afogamento)
- **Duração**: Até receber dano
- **Visual**: Bolha brilhante ao redor de Sonic

#### Monitor de Invencibilidade (Invincibility)
- **Ícone**: Estrelas
- **Efeito**: Imune a inimigos e espinhos temporariamente
- **Duração**: ~10-15 segundos
- **Visual**: Estrelas girando ao redor de Sonic
- **Nota**: NÃO protege de quedas ou afogamento

#### Monitor de Vida Extra (1-Up)
- **Ícone**: Cabeça do Sonic
- **Efeito**: +1 vida
- **Raro**: Sim

#### Monitor de Speed Sneakers (Power Sneakers)
- **Ícone**: Tênis vermelho
- **Efeito**: Aumenta velocidade de corrida temporariamente
- **Duração**: ~10-15 segundos
- **Som**: Música acelera

#### Monitor de Continue
- **Localização**: Apenas em Special Stages
- **Efeito**: Permite continuar após Game Over
- **Importante**: Permite recomeçar ao invés de voltar ao menu

### Checkpoint (Poste com Seta)
- **Não é monitor**, mas importante
- **Efeito**: Define ponto de respawn
- **Visual**: Poste gira mostrando rosto do Sonic
- **Som**: "Ding" característico

### Colecionáveis

#### Anéis (Rings)
- **Função Primária**: Proteção contra dano
- **Coleta**: Ao toque
- **Perda**: Todos ao ser atingido (dispersam e desaparecem rápido)
- **Vida Extra**: 100 anéis = +1 vida
- **Special Stage**: 50+ anéis ao fim do ato = acesso

#### Chaos Emeralds
- 6 no total
- Escondidos nos atos principais (geralmente Ato 2)
- **Localização**: Áreas secretas, caminhos alternativos
- **Finalidade**: Final verdadeiro do jogo
- **Não está em Special Stages!** (diferente do Genesis)

---

## Sistema de Pontuação e Regras

### Pontuação (Score)
**⚠️ IMPORTANTE**: A versão Master System de Sonic 1 **NÃO TEM sistema de pontuação**!
- Sem score display no HUD
- Sem bônus de pontos
- Foco total no gameplay e exploração

### Sistema de Vidas

#### Ganhar Vidas
1. **Coletar 100 anéis**: +1 vida (contador reseta)
2. **1-Up Monitors**: Encontrados escondidos nos níveis
3. **Special Stages**: Podem conter 1-Ups

#### Perder Vidas
1. Ser atingido sem anéis
2. Cair em abismo
3. Afogamento (água)
4. Time Over (9:59)

#### Game Over
- Ocorre ao perder todas as vidas
- **Sem Continue**: Retorna ao menu principal
- **Com Continue**: Recomeça no início do ato atual

### Sistema de Continues

#### Obter Continues
1. **Special Stages**: Quebrar monitors de Continue
2. **Colecionar 50+ anéis**: Ao terminar ato (não boss) acessa Special Stage

#### Uso de Continue
- Ativado após Game Over
- Permite recomeçar do início do ato onde morreu
- **Score reseta para zero** (mas não há score nesta versão)
- Não perde Emeralds já coletados

### Regras de Jogo

#### Objetivo Principal
1. Completar todas as 6 zonas
2. Derrotar Dr. Robotnik em cada Boss Act (Ato 3)
3. Coletar todos os 6 Chaos Emeralds (final verdadeiro)

#### Progressão
- Deve completar atos em ordem
- Não pode pular zonas
- Checkpoints salvam progresso dentro do ato
- Morte retorna ao último checkpoint (ou início do ato)

#### Condições de Vitória
- **Final Normal**: Completar Sky Base Zone Ato 3
- **Final Verdadeiro**: Completar com todos os 6 Chaos Emeralds

#### Condições de Derrota
- Game Over sem continues disponíveis

---

## Chaos Emeralds e Fases Especiais

### Chaos Emeralds (Master System)

#### Localização
**⚠️ DIFERENTE DO GENESIS!** Emeralds estão **escondidos nos atos principais**, não em Special Stages.

| Zona         | Ato | Dica de Localização                    |
|--------------|-----|----------------------------------------|
| Green Hill   | 2   | Área secreta atrás de parede quebrável |
| Bridge       | 1   | Caminho alternativo superior           |
| Jungle       | 2   | Área escondida perto de cachoeira      |
| Labyrinth    | 2   | Seção subaquática secreta              |
| Scrap Brain  | 2   | Caminho superior difícil de alcançar   |
| Sky Base     | 2   | Área secreta antes do final            |

#### Como Encontrar
1. Explore caminhos alternativos
2. Procure áreas suspeitas
3. Use molas e plataformas especiais
4. Algumas requerem timing perfeito
5. **Sem indicação visual clara** - pura exploração!

#### Importância
- **6/6 Emeralds**: Desbloqueia final verdadeiro
- **Menos que 6**: Final normal
- Não afetam poderes do Sonic durante o jogo
- Não são perdidos ao morrer

### Special Stages (Fases Especiais)

#### Como Acessar
- Terminar qualquer Ato 1 ou 2 (não Boss) com **50+ anéis**
- Automaticamente transportado após passar placa de fim

#### Características
- **Estilo**: Pinball/Flipper com física especial
- **Limite de Tempo**: Nenhum
- **Objetivo**: Coletar itens e continues
- **Total**: 8 Special Stages diferentes

#### Conteúdo das Special Stages
- **Anéis**: Pequenos (1) e grandes (10)
- **Continues**: Monitors especiais
- **1-Ups**: Monitors de vida extra
- **Bumpers**: Impulsionam Sonic
- **Springs**: Para navegar

#### Mecânica
- Física similar ao pinball
- Controle limitado do Sonic
- Use momentum e timing
- Coletar 100 anéis = vida extra

#### Recompensas
1. Continue (permite Game Over recovery)
2. Vidas extras
3. Muitos anéis
4. **Não contém Chaos Emeralds!**

#### Estratégia
- Priorize continues se tiver poucas vidas
- Explore toda a área
- Use bumpers para alcançar áreas altas
- Não há penalidade por tempo

---

## Áudio e Música

### Compositor
**Yuzo Koshiro** - Renomado compositor de trilhas clássicas SEGA

### Hardware de Áudio
**SN76489 PSG (Programmable Sound Generator)**
- 3 canais de onda quadrada
- 1 canal de ruído
- Limitações criativas resultam em melodias memoráveis

### Técnicas de Composição
- **Arpeggiation**: Simula acordes tocando notas rapidamente
- **Melodias Intrincadas**: Compensam limitação de canais
- **Mudanças Rítmicas**: Dinâmica para manter interesse
- **Pitch Bending**: Efeitos de glissando
- **Eco Simulado**: Notas repetidas com volume decrescente

### Trilha Sonora por Zona

#### 1. Green Hill Zone
- **Tom**: Alegre, energético
- **Tempo**: Moderado
- **Característica**: Melodia icônica de abertura

#### 2. Bridge Zone
- **Tom**: Aventureiro
- **Tempo**: Moderado a rápido
- **Característica**: Ritmo pulsante

#### 3. Jungle Zone
- **Tom**: Misterioso, tribal
- **Tempo**: Moderado
- **Característica**: Percussão forte

#### 4. Labyrinth Zone
- **Tom**: Tenso, sombrio
- **Tempo**: Lento
- **Característica**: Atmosfera subaquática opressiva

#### 5. Scrap Brain Zone
- **Tom**: Industrial, mecânico
- **Tempo**: Rápido
- **Característica**: Urgência crescente

#### 6. Sky Base Zone
- **Tom**: Épico, final
- **Tempo**: Rápido
- **Característica**: Intensidade máxima

#### Músicas Especiais
- **Title Screen**: Apresentação icônica
- **Boss Theme**: Intensa, repetitiva
- **Invincibility**: Acelerada, poderosa
- **Speed Shoes**: Versão acelerada da música do nível
- **Extra Life**: Jingle curto celebratório
- **Game Over**: Melancólico
- **Special Stage**: Alegre, pinball-like
- **Drowning**: **Música de alerta** - muda quando oxigênio está acabando!

### Efeitos Sonoros Principais

#### Gameplay
- **Jump**: Som característico "boing"
- **Spin Dash**: Carregamento rotativo
- **Ring Collect**: "Ring!" metálico
- **Ring Loss**: Dispersão de anéis
- **Spring**: "Boing" de mola

#### Itens
- **Monitor Break**: Quebra de caixa
- **Shield**: Ativação de escudo
- **1-Up**: Jingle de vida extra
- **Checkpoint**: "Ding" de confirmação

#### Dano e Morte
- **Hit**: Som de dano
- **Death**: Melodia descendente
- **Drowning**: Som de afogamento

#### Inimigos
- **Enemy Destroy**: Explosão de robot
- **Boss Hit**: Dano no boss

#### Ambiente
- **Spikes**: Som de espinho
- **Moving Platform**: Som mecânico

### Recursos de Áudio para Implementação

#### Downloads Disponíveis
- **VGM Format**: Zophar's Domain
- **MP3**: Archive.org, YouTube
- **WAV Effects**: The Sounds Resource
- **MIDI**: Versões fan-made

#### Considerações para Phaser
- Usar Web Audio API
- Formatos: OGG, MP3, WAV
- Pré-carregar todos os sons
- Sistema de música adaptativa
- Fade in/out para transições
- Controle de volume individual

---

## Implementação em Phaser

### 1. Configuração Inicial do Projeto

#### Estrutura de Pastas Recomendada
```
sonic-phaser/
├── src/
│   ├── scenes/
│   │   ├── PreloadScene.js
│   │   ├── MenuScene.js
│   │   ├── GreenHillScene.js
│   │   ├── BridgeScene.js
│   │   └── ...
│   ├── entities/
│   │   ├── Player.js
│   │   ├── Enemy.js
│   │   └── Boss.js
│   ├── objects/
│   │   ├── Ring.js
│   │   ├── Monitor.js
│   │   └── Spring.js
│   ├── config.js
│   └── main.js
├── assets/
│   ├── sprites/
│   ├── tilemaps/
│   ├── audio/
│   │   ├── music/
│   │   └── sfx/
│   └── fonts/
├── dist/
├── index.html
└── package.json
```

#### Configuração Básica (config.js)
```javascript
const config = {
    type: Phaser.AUTO,
    width: 256,  // Resolução original Master System
    height: 192,
    scale: {
        mode: Phaser.Scale.FIT,
        autoCenter: Phaser.Scale.CENTER_BOTH,
        width: 256,
        height: 192,
        zoom: 2  // Escala 2x para melhor visualização
    },
    physics: {
        default: 'arcade',
        arcade: {
            gravity: { y: 500 },  // Ajustar conforme necessário
            debug: false
        }
    },
    scene: [
        PreloadScene,
        MenuScene,
        GreenHillScene,
        // ... outras cenas
    ],
    backgroundColor: '#000000',
    pixelArt: true  // Importante para gráficos retro
};

const game = new Phaser.Game(config);
```

### 2. Sistema de Física do Sonic

#### Classe Player (Player.js)
```javascript
class Player extends Phaser.Physics.Arcade.Sprite {
    constructor(scene, x, y) {
        super(scene, x, y, 'sonic');
        
        // Adicionar à cena
        scene.add.existing(this);
        scene.physics.add.existing(this);
        
        // Propriedades de física
        this.setCollideWorldBounds(true);
        this.setBounce(0);
        this.setGravityY(0);  // Gravidade global já definida
        
        // Propriedades customizadas do Sonic
        this.maxSpeed = 6;  // pixels/frame
        this.acceleration = 0.5;
        this.deceleration = 0.3;
        this.airAcceleration = 0.3;
        this.jumpVelocity = -350;
        
        // Estados
        this.isRolling = false;
        this.hasShield = false;
        this.isInvincible = false;
        this.rings = 0;
        this.lives = 3;
        
        // Timers
        this.invincibilityTimer = 0;
        this.speedShoesTimer = 0;
    }
    
    update(cursors) {
        // Movimento horizontal
        if (cursors.left.isDown) {
            this.moveLeft();
        } else if (cursors.right.isDown) {
            this.moveRight();
        } else {
            this.decelerate();
        }
        
        // Pulo
        if (cursors.up.isDown && this.body.touching.down) {
            this.jump();
        }
        
        // Rolagem
        if (cursors.down.isDown && Math.abs(this.body.velocity.x) > 100) {
            this.roll();
        }
        
        // Atualizar animações
        this.updateAnimation();
        
        // Atualizar timers
        this.updateTimers();
    }
    
    moveLeft() {
        const acc = this.body.touching.down ? this.acceleration : this.airAcceleration;
        this.setVelocityX(Math.max(this.body.velocity.x - acc * 60, -this.maxSpeed * 60));
        this.setFlipX(true);
    }
    
    moveRight() {
        const acc = this.body.touching.down ? this.acceleration : this.airAcceleration;
        this.setVelocityX(Math.min(this.body.velocity.x + acc * 60, this.maxSpeed * 60));
        this.setFlipX(false);
    }
    
    decelerate() {
        if (this.body.velocity.x > 0) {
            this.setVelocityX(Math.max(this.body.velocity.x - this.deceleration * 60, 0));
        } else if (this.body.velocity.x < 0) {
            this.setVelocityX(Math.min(this.body.velocity.x + this.deceleration * 60, 0));
        }
    }
    
    jump() {
        this.setVelocityY(this.jumpVelocity);
        this.scene.sound.play('sfx_jump');
    }
    
    roll() {
        this.isRolling = true;
        // Aumentar velocidade levemente ao rolar (mantém direção)
        this.setVelocityX(this.body.velocity.x * 1.1);
    }
    
    takeDamage() {
        if (this.isInvincible) return;
        
        if (this.hasShield) {
            this.hasShield = false;
            this.scene.sound.play('sfx_shield_loss');
            return;
        }
        
        if (this.rings > 0) {
            this.loseRings();
        } else {
            this.die();
        }
    }
    
    loseRings() {
        const ringsToLose = Math.min(this.rings, 32);  // Máximo de anéis perdidos
        this.rings = 0;
        
        // Criar anéis dispersos
        for (let i = 0; i < ringsToLose; i++) {
            this.scene.createLostRing(this.x, this.y, i);
        }
        
        this.scene.sound.play('sfx_ring_loss');
        this.makeInvincible(2000);  // 2 segundos de invencibilidade
    }
    
    die() {
        this.lives--;
        this.scene.sound.play('sfx_death');
        
        if (this.lives > 0) {
            this.scene.respawn();
        } else {
            this.scene.gameOver();
        }
    }
    
    collectRing() {
        this.rings++;
        this.scene.sound.play('sfx_ring');
        this.scene.updateHUD();
        
        if (this.rings >= 100) {
            this.lives++;
            this.rings -= 100;
            this.scene.sound.play('sfx_1up');
        }
    }
    
    makeInvincible(duration) {
        this.isInvincible = true;
        this.invincibilityTimer = duration;
    }
    
    updateTimers() {
        if (this.invincibilityTimer > 0) {
            this.invincibilityTimer -= 16;  // ~60fps
            if (this.invincibilityTimer <= 0) {
                this.isInvincible = false;
            }
        }
        
        if (this.speedShoesTimer > 0) {
            this.speedShoesTimer -= 16;
            if (this.speedShoesTimer <= 0) {
                this.maxSpeed = 6;  // Retornar velocidade normal
            }
        }
    }
    
    updateAnimation() {
        if (!this.body.touching.down) {
            this.anims.play('sonic_jump', true);
        } else if (this.isRolling) {
            this.anims.play('sonic_roll', true);
        } else if (Math.abs(this.body.velocity.x) > 10) {
            this.anims.play('sonic_run', true);
        } else {
            this.anims.play('sonic_idle', true);
        }
    }
}
```

### 3. Sistema de HUD

#### HUD Scene (HUDScene.js)
```javascript
class HUDScene extends Phaser.Scene {
    constructor() {
        super({ key: 'HUDScene', active: true });
    }
    
    create() {
        const width = this.cameras.main.width;
        
        // Anéis (top left)
        this.ringsText = this.add.text(10, 5, 'RINGS 0', {
            font: '8px Arial',
            fill: '#FFFF00'
        }).setScrollFactor(0);
        
        // Vidas (top center)
        this.livesIcon = this.add.image(width / 2 - 10, 10, 'sonic_icon')
            .setScrollFactor(0)
            .setScale(0.5);
        this.livesText = this.add.text(width / 2 + 5, 5, 'x 3', {
            font: '8px Arial',
            fill: '#FFFFFF'
        }).setScrollFactor(0);
        
        // Timer (top right)
        this.timerText = this.add.text(width - 50, 5, '0:00', {
            font: '8px Arial',
            fill: '#FFFFFF'
        }).setScrollFactor(0);
        
        // Timer de jogo
        this.gameTime = 0;
        this.timeEvent = this.time.addEvent({
            delay: 1000,
            callback: this.updateTimer,
            callbackScope: this,
            loop: true
        });
    }
    
    updateRings(rings) {
        this.ringsText.setText(`RINGS ${rings}`);
    }
    
    updateLives(lives) {
        this.livesText.setText(`x ${lives}`);
    }
    
    updateTimer() {
        this.gameTime++;
        const minutes = Math.floor(this.gameTime / 60);
        const seconds = this.gameTime % 60;
        this.timerText.setText(`${minutes}:${seconds.toString().padStart(2, '0')}`);
        
        // Time Over aos 9:59
        if (minutes >= 9 && seconds >= 59) {
            this.scene.get('GameScene').timeOver();
        }
    }
    
    resetTimer() {
        this.gameTime = 0;
        this.timerText.setText('0:00');
    }
}
```

### 4. Sistema de Níveis com Tilemaps

#### Criando Tilemap no Tiled
1. Use Tiled Map Editor (gratuito)
2. Tamanho do tile: 8x8 ou 16x16 pixels
3. Layers:
   - Background (fundo)
   - Platforms (plataformas sólidas)
   - Objects (inimigos, itens, etc.)
   - Foreground (elementos frontais)

#### Carregando Tilemap em Phaser
```javascript
class GreenHillScene extends Phaser.Scene {
    preload() {
        this.load.image('tiles', 'assets/tilemaps/green_hill_tiles.png');
        this.load.tilemapTiledJSON('map', 'assets/tilemaps/green_hill.json');
    }
    
    create() {
        // Criar tilemap
        const map = this.make.tilemap({ key: 'map' });
        const tileset = map.addTilesetImage('green_hill_tiles', 'tiles');
        
        // Layers
        this.backgroundLayer = map.createLayer('Background', tileset, 0, 0);
        this.platformLayer = map.createLayer('Platforms', tileset, 0, 0);
        this.foregroundLayer = map.createLayer('Foreground', tileset, 0, 0);
        
        // Definir colisões
        this.platformLayer.setCollisionByProperty({ collides: true });
        
        // Criar player
        this.player = new Player(this, 50, 100);
        
        // Colisão player com plataformas
        this.physics.add.collider(this.player, this.platformLayer);
        
        // Câmera segue player
        this.cameras.main.startFollow(this.player);
        this.cameras.main.setBounds(0, 0, map.widthInPixels, map.heightInPixels);
        
        // Criar objetos do mapa
        this.createObjectsFromTilemap(map);
    }
    
    createObjectsFromTilemap(map) {
        const objectsLayer = map.getObjectLayer('Objects');
        
        objectsLayer.objects.forEach(obj => {
            switch(obj.name) {
                case 'ring':
                    this.createRing(obj.x, obj.y);
                    break;
                case 'enemy_motobug':
                    this.createMotobug(obj.x, obj.y);
                    break;
                case 'monitor_shield':
                    this.createMonitor(obj.x, obj.y, 'shield');
                    break;
                // ... outros objetos
            }
        });
    }
}
```

### 5. Sistema de Inimigos

#### Classe Base Enemy
```javascript
class Enemy extends Phaser.Physics.Arcade.Sprite {
    constructor(scene, x, y, texture) {
        super(scene, x, y, texture);
        
        scene.add.existing(this);
        scene.physics.add.existing(this);
        
        this.setCollideWorldBounds(false);
        this.health = 1;
    }
    
    takeDamage() {
        this.health--;
        if (this.health <= 0) {
            this.destroy();
            this.scene.sound.play('sfx_enemy_destroy');
            this.scene.createExplosion(this.x, this.y);
        }
    }
}

class Motobug extends Enemy {
    constructor(scene, x, y) {
        super(scene, x, y, 'motobug');
        
        this.speed = 60;
        this.direction = 1;
        this.setVelocityX(this.speed * this.direction);
    }
    
    update() {
        // Virar ao encontrar borda ou parede
        if (this.body.blocked.right || this.body.blocked.left) {
            this.direction *= -1;
            this.setVelocityX(this.speed * this.direction);
            this.setFlipX(this.direction < 0);
        }
    }
}
```

### 6. Sistema de Áudio

#### Gerenciamento de Música e SFX
```javascript
class AudioManager {
    constructor(scene) {
        this.scene = scene;
        this.currentMusic = null;
        this.musicVolume = 0.7;
        this.sfxVolume = 1.0;
    }
    
    playMusic(key, loop = true) {
        if (this.currentMusic) {
            this.currentMusic.stop();
        }
        
        this.currentMusic = this.scene.sound.add(key, {
            volume: this.musicVolume,
            loop: loop
        });
        
        this.currentMusic.play();
    }
    
    stopMusic() {
        if (this.currentMusic) {
            this.currentMusic.stop();
        }
    }
    
    playSFX(key) {
        this.scene.sound.play(key, { volume: this.sfxVolume });
    }
    
    speedUpMusic() {
        if (this.currentMusic) {
            this.currentMusic.setRate(1.3);  // Speed Shoes effect
        }
    }
    
    normalizeMusic() {
        if (this.currentMusic) {
            this.currentMusic.setRate(1.0);
        }
    }
}
```

### 7. Otimizações para Performance

#### Object Pooling para Anéis
```javascript
class RingPool {
    constructor(scene, size = 100) {
        this.scene = scene;
        this.pool = scene.physics.add.group({
            classType: Ring,
            maxSize: size,
            runChildUpdate: true
        });
    }
    
    spawn(x, y) {
        const ring = this.pool.get(x, y);
        if (ring) {
            ring.activate();
        }
        return ring;
    }
    
    despawn(ring) {
        this.pool.killAndHide(ring);
    }
}
```

### 8. Sistema de Salvamento

#### LocalStorage para Save States
```javascript
class SaveManager {
    static saveProgress(data) {
        localStorage.setItem('sonic_save', JSON.stringify(data));
    }
    
    static loadProgress() {
        const saved = localStorage.getItem('sonic_save');
        return saved ? JSON.parse(saved) : null;
    }
    
    static deleteSave() {
        localStorage.removeItem('sonic_save');
    }
}

// Uso
const saveData = {
    currentZone: 'GreenHill',
    lives: player.lives,
    rings: player.rings,
    emeralds: [true, false, false, false, false, false],
    continues: 2
};

SaveManager.saveProgress(saveData);
```

### 9. Controles Touch para Mobile

```javascript
class TouchControls {
    constructor(scene) {
        this.scene = scene;
        this.createVirtualControls();
    }
    
    createVirtualControls() {
        // D-Pad Virtual
        this.dpad = this.scene.add.container(60, this.scene.cameras.main.height - 60);
        
        // Botão esquerda
        this.leftBtn = this.createButton(-30, 0, '←');
        this.dpad.add(this.leftBtn);
        
        // Botão direita
        this.rightBtn = this.createButton(30, 0, '→');
        this.dpad.add(this.rightBtn);
        
        // Botão pular
        this.jumpBtn = this.createButton(
            this.scene.cameras.main.width - 60,
            this.scene.cameras.main.height - 60,
            'A'
        );
        
        this.dpad.setScrollFactor(0);
        this.jumpBtn.setScrollFactor(0);
    }
    
    createButton(x, y, label) {
        const button = this.scene.add.circle(x, y, 25, 0x0000ff, 0.5);
        const text = this.scene.add.text(x, y, label, {
            font: '20px Arial',
            fill: '#ffffff'
        }).setOrigin(0.5);
        
        button.setInteractive();
        
        return this.scene.add.container(0, 0, [button, text]);
    }
    
    update() {
        // Processar input dos botões virtuais
        // Retornar objeto similar ao cursors do teclado
    }
}
```

---

## Exportação para Android com Capacitor

### 1. Preparação do Projeto

#### Instalar Dependências
```bash
# Navegue até a pasta do projeto
cd sonic-phaser

# Instalar Capacitor
npm install @capacitor/core @capacitor/cli --save-dev
npm install @capacitor/android

# Inicializar Capacitor
npx cap init
```

Durante a inicialização, forneça:
- **App name**: Sonic Phaser
- **App ID**: com.seunome.sonicphaser (formato reverse-domain)
- **Web Dir**: dist (ou diretório de build)

#### Configurar capacitor.config.json
```json
{
  "appId": "com.seunome.sonicphaser",
  "appName": "Sonic Phaser",
  "webDir": "dist",
  "bundledWebRuntime": false,
  "android": {
    "buildOptions": {
      "keystorePath": "path/to/keystore",
      "keystoreAlias": "alias_name"
    }
  }
}
```

### 2. Configuração do Build

#### Webpack/Vite Configuration
Configure seu bundler para otimizar assets:

```javascript
// vite.config.js (exemplo)
export default {
  base: './',  // Importante para paths relativos
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
    minify: 'terser',
    rollupOptions: {
      output: {
        manualChunks: {
          phaser: ['phaser']
        }
      }
    }
  }
}
```

#### Script de Build no package.json
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "cap:sync": "npm run build && npx cap sync",
    "cap:android": "npm run cap:sync && npx cap open android"
  }
}
```

### 3. Adicionar Plataforma Android

```bash
# Adicionar plataforma Android
npx cap add android

# Build e sync (sempre que atualizar código)
npm run build
npx cap sync

# Abrir no Android Studio
npx cap open android
```

### 4. Configurações Específicas para Android

#### AndroidManifest.xml
Localização: `android/app/src/main/AndroidManifest.xml`

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Permissões -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    
    <!-- Evitar sleep durante jogo -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme"
        android:hardwareAccelerated="true"
        android:usesCleartextTraffic="true">
        
        <activity
            android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|smallestScreenSize|screenLayout|uiMode"
            android:name=".MainActivity"
            android:label="@string/title_activity_main"
            android:theme="@style/AppTheme.NoActionBarLaunch"
            android:launchMode="singleTask"
            android:screenOrientation="landscape"
            android:exported="true">
            
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

#### Orientação de Tela (Landscape)
O jogo Sonic é horizontal, então force landscape no manifesto:
```xml
android:screenOrientation="landscape"
```

### 5. Ícone e Splash Screen

#### Gerar Ícones
Use ferramentas como:
- Android Asset Studio
- Capacitor Assets Generator

Coloque em:
```
android/app/src/main/res/
├── mipmap-hdpi/
├── mipmap-mdpi/
├── mipmap-xhdpi/
├── mipmap-xxhdpi/
└── mipmap-xxxhdpi/
```

#### Splash Screen
Localização: `android/app/src/main/res/values/styles.xml`

```xml
<resources>
    <style name="AppTheme.NoActionBarLaunch" parent="AppTheme.NoActionBar">
        <item name="android:background">@drawable/splash</item>
    </style>
</resources>
```

Adicione imagem splash em `android/app/src/main/res/drawable/splash.png`

### 6. Otimizações para Mobile

#### Performance
```javascript
// No config do Phaser
const config = {
    type: Phaser.WEBGL,  // Use WebGL para melhor performance
    powerPreference: 'high-performance',
    transparent: false,
    clearBeforeRender: false,
    batchSize: 2048,
    maxLights: 0,  // Desabilitar luzes se não usar
    
    // ... resto da config
};
```

#### Responsividade
```javascript
// Adaptar para diferentes tamanhos de tela
const config = {
    scale: {
        mode: Phaser.Scale.FIT,
        autoCenter: Phaser.Scale.CENTER_BOTH,
        width: 256,
        height: 192,
        min: {
            width: 256,
            height: 192
        },
        max: {
            width: 1024,
            height: 768
        }
    }
};
```

#### Touch Controls
```javascript
// Detectar se é mobile
const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent);

if (isMobile) {
    // Ativar controles touch
    this.touchControls = new TouchControls(this);
}
```

### 7. Build para Produção

#### Debug Build (Teste)
```bash
# No Android Studio
Build > Build Bundle(s) / APK(s) > Build APK(s)
```

#### Release Build (Publicação)

1. **Criar Keystore**:
```bash
keytool -genkey -v -keystore sonic-release.keystore -alias sonic -keyalg RSA -keysize 2048 -validity 10000
```

2. **Configurar Gradle**:
Edite `android/app/build.gradle`:

```gradle
android {
    ...
    signingConfigs {
        release {
            storeFile file('path/to/sonic-release.keystore')
            storePassword 'sua-senha'
            keyAlias 'sonic'
            keyPassword 'sua-senha'
        }
    }
    
    buildTypes {
        release {
            signingConfig signingConfigs.release
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```

3. **Build Release**:
```bash
# Via Android Studio
Build > Generate Signed Bundle / APK > Android App Bundle

# Ou via linha de comando
cd android
./gradlew bundleRelease
```

Output estará em: `android/app/build/outputs/bundle/release/app-release.aab`

### 8. Testes no Dispositivo

#### Via Android Studio
1. Conecte dispositivo Android via USB
2. Ative "Depuração USB" no dispositivo
3. Em Android Studio: Run > Run 'app'

#### Via APK Direto
1. Build APK debug
2. Transfira para dispositivo
3. Instale (habilite "Fontes Desconhecidas" se necessário)

### 9. Publicação na Google Play Store

#### Preparação
1. **Criar conta**: Google Play Console
2. **Taxa única**: ~$25 USD
3. **Assets necessários**:
   - Ícone: 512x512px
   - Feature Graphic: 1024x500px
   - Screenshots: mínimo 2, várias resoluções
   - Descrição curta e longa
   - Classificação de conteúdo

#### Upload
1. Crie novo aplicativo no Play Console
2. Preencha informações do app
3. Faça upload do AAB (Android App Bundle)
4. Configure preço (gratuito/pago)
5. Envie para revisão

#### Dicas
- Prepare descrição em português
- Use screenshots atraentes
- Adicione vídeo demonstrativo (opcional mas recomendado)
- Configure política de privacidade
- Teste em múltiplos dispositivos antes de publicar

### 10. Plugins Capacitor Úteis

#### Manter Tela Ligada
```bash
npm install @capacitor-community/keep-awake
```

```javascript
import { KeepAwake } from '@capacitor-community/keep-awake';

// Prevenir screen sleep durante jogo
await KeepAwake.keepAwake();

// Permitir sleep novamente
await KeepAwake.allowSleep();
```

#### Vibração
```bash
npm install @capacitor/haptics
```

```javascript
import { Haptics, ImpactStyle } from '@capacitor/haptics';

// Vibrar ao colidir com inimigo
await Haptics.impact({ style: ImpactStyle.Heavy });
```

#### Status Bar
```bash
npm install @capacitor/status-bar
```

```javascript
import { StatusBar, Style } from '@capacitor/status-bar';

// Esconder status bar para fullscreen
await StatusBar.hide();

// Estilo escuro
await StatusBar.setStyle({ style: Style.Dark });
```

### 11. Troubleshooting Comum

#### Problema: Jogo não carrega no Android
**Solução**: 
- Verifique `webDir` no capacitor.config.json
- Execute `npm run build` antes de `npx cap sync`
- Check console no Chrome DevTools via `chrome://inspect`

#### Problema: Sons não tocam
**Solução**:
- Use formatos OGG e M4A para compatibilidade
- Adicione user interaction antes de tocar áudio
```javascript
this.input.once('pointerdown', () => {
    this.sound.unlock();
});
```

#### Problema: Performance ruim
**Solução**:
- Use object pooling
- Reduza número de partículas
- Otimize spritesheets
- Use WebGL renderer
- Desabilite debug mode

#### Problema: Controles touch não responsivos
**Solução**:
- Aumente tamanho dos botões touch
- Adicione feedback visual (highlight)
- Use `setInteractive()` corretamente
- Teste hitboxes dos botões

---

## Recursos Adicionais

### Assets e Sprites
- **The Spriters Resource**: Sprites originais do jogo
- **OpenGameArt**: Sprites alternativos gratuitos
- **Sonic Retro**: Documentação técnica detalhada

### Ferramentas
- **Tiled Map Editor**: Criar tilemaps
- **Aseprite**: Editar sprites pixel art
- **Audacity**: Editar sons
- **Android Studio**: IDE para Android

### Tutoriais Phaser
- Documentação oficial Phaser 3
- Phaser Examples (labs.phaser.io)
- Game Dev Academy
- Ourcade (YouTube)

### Comunidades
- Phaser Discord
- Sonic Retro Forums
- GameDev.net
- Reddit: r/gamedev, r/phaser

---

## Checklist de Implementação

### Fase 1: Setup Básico
- [ ] Configurar projeto Phaser
- [ ] Criar estrutura de pastas
- [ ] Implementar PreloadScene
- [ ] Implementar MenuScene
- [ ] Sistema de configuração

### Fase 2: Player e Física
- [ ] Classe Player
- [ ] Sistema de movimento
- [ ] Sistema de pulo
- [ ] Animações do Sonic
- [ ] Colisão básica

### Fase 3: Níveis
- [ ] Green Hill Zone Ato 1
- [ ] Sistema de tilemap
- [ ] Checkpoints
- [ ] End-of-act signpost

### Fase 4: Objetos e Itens
- [ ] Anéis coletáveis
- [ ] Monitors (Shield, Rings, 1-Up)
- [ ] Molas (Springs)
- [ ] Chaos Emeralds escondidos

### Fase 5: Inimigos
- [ ] Sistema base de inimigos
- [ ] Motobug
- [ ] Buzz Bomber
- [ ] Crabmeat
- [ ] Outros badniks

### Fase 6: HUD e Interface
- [ ] Sistema de HUD
- [ ] Contador de anéis
- [ ] Contador de vidas
- [ ] Timer
- [ ] Tela de pause

### Fase 7: Áudio
- [ ] Carregar músicas
- [ ] Carregar SFX
- [ ] Sistema de música por zona
- [ ] Efeitos sonoros de gameplay

### Fase 8: Outras Zonas
- [ ] Bridge Zone
- [ ] Jungle Zone
- [ ] Labyrinth Zone
- [ ] Scrap Brain Zone
- [ ] Sky Base Zone

### Fase 9: Boss Fights
- [ ] Sistema base de boss
- [ ] Boss de cada zona
- [ ] Padrões de ataque

### Fase 10: Special Stages
- [ ] Física especial tipo pinball
- [ ] Sistema de continues
- [ ] Recompensas

### Fase 11: Polimento
- [ ] Particle effects
- [ ] Transições de cena
- [ ] Animações extras
- [ ] Ajuste fino de física

### Fase 12: Mobile
- [ ] Controles touch
- [ ] Otimizações de performance
- [ ] Teste em diferentes resoluções
- [ ] Safe area handling

### Fase 13: Capacitor
- [ ] Setup Capacitor
- [ ] Build Android
- [ ] Ícone e splash screen
- [ ] Testes em dispositivo real

### Fase 14: Publicação
- [ ] Build release
- [ ] Testes finais
- [ ] Assets da Play Store
- [ ] Submissão

---

## Conclusão

Esta documentação fornece uma base sólida para recriar Sonic the Hedgehog Master System usando Phaser e exportar para Android com Capacitor. 

### Próximos Passos Recomendados

1. **Estudar o jogo original**: Jogue ou assista gameplay para entender nuances
2. **Começar pequeno**: Implemente Green Hill Zone Ato 1 primeiro
3. **Iterar rapidamente**: Teste frequentemente em diferentes dispositivos
4. **Comunidade**: Busque feedback em comunidades de gamedev
5. **Documentar**: Mantenha documentação do seu código

### Considerações Finais

- **Direitos Autorais**: Este é um projeto educacional/fan-made. SEGA detém direitos sobre Sonic.
- **Não comercialize** sem permissão apropriada
- **Aprenda e compartilhe**: Use como oportunidade de aprendizado
- **Divirta-se**: O mais importante é o processo de criação!

Boa sorte com seu projeto! 🦔💨💙
