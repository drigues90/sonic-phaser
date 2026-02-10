# Sonic Master System - Guia Rápido de Referência

Este é um guia de consulta rápida para desenvolvedores. Para documentação completa, veja [SONIC_MASTER_SYSTEM_DOCUMENTATION.md](./SONIC_MASTER_SYSTEM_DOCUMENTATION.md).

## 🎮 Especificações Técnicas Básicas

### Resolução Original
- **Master System**: 256x192 pixels
- **Taxa de atualização**: ~60 FPS
- **Paleta de cores**: 32 cores simultâneas (de 64 possíveis)

### Física do Sonic

```javascript
// Valores de referência para implementação
const SONIC_PHYSICS = {
    maxSpeed: 6,              // pixels/frame
    acceleration: 0.5,        // aceleração ao correr
    deceleration: 0.3,        // desaceleração ao parar
    airAcceleration: 0.3,     // controle no ar
    jumpVelocity: -350,       // força do pulo (Phaser)
    gravity: 500,             // gravidade (Phaser)
    rollingBoost: 1.1         // multiplicador ao rolar
};
```

## 🗺️ Zonas e Estrutura

| Zona        | Atos | Tema              | Dificuldade | Emerald |
|-------------|------|-------------------|-------------|---------|
| Green Hill  | 3    | Pradaria          | ⭐          | Ato 2   |
| Bridge      | 3    | Pontes/Água       | ⭐⭐        | Ato 1   |
| Jungle      | 3    | Selva             | ⭐⭐        | Ato 2   |
| Labyrinth   | 3    | Ruínas Aquáticas  | ⭐⭐⭐      | Ato 2   |
| Scrap Brain | 3    | Fábrica           | ⭐⭐⭐⭐    | Ato 2   |
| Sky Base    | 3    | Base Aérea        | ⭐⭐⭐⭐⭐  | Ato 2   |

*Ato 3 de cada zona = Boss Fight*

## 👾 Inimigos (Badniks) por Zona

### Green Hill Zone
- **Motobug**: Rola horizontalmente
- **Buzz Bomber**: Voa e atira
- **Crabmeat**: Caminha e atira

### Bridge Zone  
- **Chopper**: Pula da água
- **Buzz Bomber**: Patrulha aérea
- **Crabmeat**: Atira projéteis

### Jungle Zone
- **Monkey Dude**: Inimigo na selva
- **Buzz Bomber**: Voa entre árvores

### Labyrinth Zone
- **Jaws**: Peixe robô subaquático
- **Burrobot**: Escava no chão

### Scrap Brain Zone
- **Orbinaut**: Flutua com bolas orbitando
- **Burrobot**: Emerge de buracos

### Sky Base Zone
- **Orbinaut**: Patrulha complexa
- **Flying robots**: Robôs voadores

## 📦 Power-ups (Monitors)

| Ícone         | Item          | Efeito                              |
|---------------|---------------|-------------------------------------|
| 💍            | Super Ring    | +10 anéis                           |
| 🛡️            | Shield        | Protege de 1 hit                    |
| ⭐            | Invincibility | Imunidade temporária (~10-15s)      |
| 🦔            | Extra Life    | +1 vida                             |
| 👟            | Speed Shoes   | Velocidade aumentada (~10-15s)      |
| ➡️            | Checkpoint    | Define ponto de respawn             |
| ▶️            | Continue      | Continuar após Game Over (SS only)  |

## 🎵 Lista de Músicas

```
Zona:
├── Green Hill Zone    - Tema alegre, energético
├── Bridge Zone        - Ritmo aventureiro
├── Jungle Zone        - Tom tribal, misterioso
├── Labyrinth Zone     - Atmosfera tensa, subaquática
├── Scrap Brain Zone   - Industrial, urgente
└── Sky Base Zone      - Épico, intenso

Especiais:
├── Title Screen       - Apresentação
├── Boss Theme         - Luta contra Robotnik
├── Invincibility      - Power-up invencibilidade
├── Speed Shoes        - Música acelerada
├── Extra Life         - Jingle de 1-Up
├── Game Over          - Fim de jogo
├── Special Stage      - Fase bônus
└── Drowning Alert     - Aviso de afogamento
```

## 🎮 Controles

### Teclado (Desktop)
```
←/→       : Mover
↑         : Olhar para cima
↓         : Agachar / Rolar
Espaço/Z  : Pular
P         : Pause
```

### Touch (Mobile)
```
Virtual D-Pad: Movimento
Botão A      : Pular
```

## 🔢 Sistema de Regras

### Ganhar Vidas
- 100 anéis coletados = +1 vida
- 1-Up Monitor = +1 vida
- Encontrar em Special Stages

### Perder Vidas
- Hit sem anéis ou escudo
- Cair em abismo
- Afogamento (>20s submerso)
- Time Over (9:59)

### Continues
- 50+ anéis ao fim do ato → Special Stage → Continue Monitor
- Continue permite recomeçar após Game Over

### Chaos Emeralds
- 6 no total
- **Escondidos nos atos principais** (geralmente Ato 2)
- Necessários para final verdadeiro

## 💻 Código Phaser - Templates Rápidos

### Configuração Básica
```javascript
const config = {
    type: Phaser.AUTO,
    width: 256,
    height: 192,
    scale: {
        mode: Phaser.Scale.FIT,
        autoCenter: Phaser.Scale.CENTER_BOTH,
        zoom: 2
    },
    physics: {
        default: 'arcade',
        arcade: {
            gravity: { y: 500 },
            debug: false
        }
    },
    pixelArt: true
};
```

### Criar Sonic (Player)
```javascript
class Player extends Phaser.Physics.Arcade.Sprite {
    constructor(scene, x, y) {
        super(scene, x, y, 'sonic');
        scene.add.existing(this);
        scene.physics.add.existing(this);
        
        this.maxSpeed = 360;
        this.acceleration = 30;
        this.jumpVelocity = -350;
        this.rings = 0;
        this.lives = 3;
    }
    
    update(cursors) {
        if (cursors.left.isDown) {
            this.setVelocityX(-this.maxSpeed);
        } else if (cursors.right.isDown) {
            this.setVelocityX(this.maxSpeed);
        } else {
            this.setVelocityX(0);
        }
        
        if (cursors.up.isDown && this.body.touching.down) {
            this.setVelocityY(this.jumpVelocity);
        }
    }
}
```

### Criar Inimigo Simples
```javascript
class Motobug extends Phaser.Physics.Arcade.Sprite {
    constructor(scene, x, y) {
        super(scene, x, y, 'motobug');
        scene.add.existing(this);
        scene.physics.add.existing(this);
        
        this.speed = 60;
        this.direction = 1;
        this.setVelocityX(this.speed);
    }
    
    update() {
        if (this.body.blocked.right || this.body.blocked.left) {
            this.direction *= -1;
            this.setVelocityX(this.speed * this.direction);
            this.setFlipX(this.direction < 0);
        }
    }
}
```

### HUD Básico
```javascript
class HUDScene extends Phaser.Scene {
    create() {
        this.ringsText = this.add.text(10, 5, 'RINGS 0', {
            font: '8px Arial',
            fill: '#FFFF00'
        }).setScrollFactor(0);
        
        this.livesText = this.add.text(120, 5, 'LIVES 3', {
            font: '8px Arial',
            fill: '#FFFFFF'
        }).setScrollFactor(0);
        
        this.timerText = this.add.text(200, 5, '0:00', {
            font: '8px Arial',
            fill: '#FFFFFF'
        }).setScrollFactor(0);
    }
}
```

## 📱 Capacitor - Comandos Essenciais

```bash
# Setup inicial
npm install @capacitor/core @capacitor/cli @capacitor/android
npx cap init

# Adicionar plataforma Android
npx cap add android

# Build e sync
npm run build
npx cap sync

# Abrir no Android Studio
npx cap open android

# Update após mudanças
npm run build && npx cap sync
```

## 🎨 Assets Necessários

### Sprites
- Sonic (idle, run, jump, roll, hurt, death)
- Inimigos (cada tipo + animações)
- Itens (anéis, monitors, springs)
- Tileset de cada zona
- Boss sprites

### Audio
- Música de cada zona (OGG/M4A)
- ~30 efeitos sonoros (WAV)
- Jingles especiais

### UI
- HUD elements
- Ícones
- Telas de menu
- Splash screen

## ⚡ Otimizações Importantes

### Performance
```javascript
// Object pooling para anéis
this.ringPool = this.physics.add.group({
    classType: Ring,
    maxSize: 100,
    runChildUpdate: true
});

// Desativar objetos fora da câmera
this.physics.world.bounds.setTo(0, 0, mapWidth, mapHeight);
this.physics.world.setBoundsCollision(true, true, true, false);
```

### Mobile
```javascript
// Detectar mobile
const isMobile = /Android|iPhone|iPad|iPod/i.test(navigator.userAgent);

// Ajustar controles
if (isMobile) {
    this.touchControls = new TouchControls(this);
}

// Orientação landscape
// No AndroidManifest.xml: android:screenOrientation="landscape"
```

## 🐛 Debug Tips

```javascript
// Visualizar hitboxes
physics: {
    default: 'arcade',
    arcade: {
        debug: true  // Ative durante desenvolvimento
    }
}

// Console log posição do Sonic
console.log(`Sonic: x=${this.player.x}, y=${this.player.y}`);

// Inspecionar no Android
chrome://inspect  // Em Chrome desktop
```

## 📚 Links Úteis

- **Phaser 3 Docs**: https://photonstorm.github.io/phaser3-docs/
- **Phaser Examples**: https://labs.phaser.io/
- **Capacitor Docs**: https://capacitorjs.com/docs
- **Sonic Retro**: https://info.sonicretro.org/
- **The Spriters Resource**: https://www.spriters-resource.com/

---

**Para informações detalhadas, consulte: [SONIC_MASTER_SYSTEM_DOCUMENTATION.md](./SONIC_MASTER_SYSTEM_DOCUMENTATION.md)**
