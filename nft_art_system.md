# 🎨 BLOCKLOTTO - SISTEMA DE ARTE GENERATIVO NFT PARA BOLETOS

## 🎯 CONCEPTO: Boletos NFT Coleccionables

Cada boleto de lotería es un NFT único con arte generativo que combina:
- **Blockchain aesthetics** (hexágonos, nodos, cadenas)
- **Elementos futuristas** (neón, cyber, holográfico)
- **Símbolos de suerte** (trébol, herradura, monedas, estrellas)

---

## 🎨 SISTEMA DE GENERACIÓN

### **Componentes Aleatorios del Arte**

#### 1. **Background (Fondo)**
```javascript
const backgrounds = [
  'cosmic_purple',      // Galaxia púrpura con estrellas
  'neon_grid',          // Grid neón estilo cyberpunk
  'blockchain_nodes',   // Nodos conectados animados
  'golden_gradient',    // Gradiente dorado de suerte
  'matrix_rain',        // Lluvia de código verde
  'holographic_waves',  // Ondas holográficas
  'lucky_stars',        // Constelación de estrellas doradas
  'crypto_circuit'      // Circuitos de criptomonedas
];

Rareza:
- Común (60%): cosmic_purple, neon_grid, golden_gradient
- Raro (30%): blockchain_nodes, matrix_rain, holographic_waves
- Épico (9%): lucky_stars
- Legendario (1%): crypto_circuit (con animación especial)
```

#### 2. **Main Element (Elemento Central)**
```javascript
const mainElements = [
  '🍀 lucky_clover',        // Trébol de 4 hojas holográfico
  '💎 diamond_blockchain',  // Diamante formado por bloques
  '🪙 golden_coin',         // Moneda dorada girando
  '⭐ shooting_star',       // Estrella fugaz
  '🧿 fortune_eye',         // Ojo de la fortuna cyber
  '🌙 crescent_moon',       // Luna creciente neón
  '🎰 lucky_777',           // Números 777 pixelados
  '🔮 crystal_ball',        // Bola de cristal futurista
  '🦄 cyber_unicorn',       // Unicornio cyberpunk
  '🐉 dragon_fortune'       // Dragón de la fortuna
];

Rareza:
- Común (50%): clover, coin, star, moon
- Raro (30%): diamond, eye, 777
- Épico (15%): crystal_ball, unicorn
- Legendario (5%): dragon_fortune (ultra raro)
```

#### 3. **Decorative Elements (Elementos Decorativos)**
```javascript
const decorativeElements = [
  'floating_hexagons',     // Hexágonos flotantes
  'particle_system',       // Sistema de partículas brillantes
  'blockchain_chain',      // Cadena de bloques visual
  'neon_rings',           // Anillos neón concéntricos
  'lucky_symbols',        // Símbolos de suerte dispersos
  'holographic_shimmer',  // Brillo holográfico
  'cyber_glyphs',         // Glifos cyberpunk
  'fortune_runes'         // Runas de la fortuna
];

Cantidad: 2-4 elementos aleatorios por ticket
```

#### 4. **Color Scheme (Esquema de Color)**
```javascript
const colorSchemes = [
  {
    name: 'purple_gold',
    primary: '#667eea',
    secondary: '#ffd700',
    accent: '#9f7aea'
  },
  {
    name: 'neon_green',
    primary: '#00ff88',
    secondary: '#00ffff',
    accent: '#ff00ff'
  },
  {
    name: 'lucky_red',
    primary: '#ff0066',
    secondary: '#ffcc00',
    accent: '#ff6600'
  },
  {
    name: 'blockchain_blue',
    primary: '#0099ff',
    secondary: '#00ffff',
    accent: '#6666ff'
  },
  {
    name: 'golden_fortune',
    primary: '#ffd700',
    secondary: '#ffaa00',
    accent: '#ff8800'
  }
];

Rareza:
- purple_gold: 30% (tema principal BlockLotto)
- neon_green, blockchain_blue: 25% cada uno
- lucky_red, golden_fortune: 10% cada uno (raros)
```

#### 5. **Special Effects (Efectos Especiales)**
```javascript
const effects = [
  'none',                  // 40%
  'subtle_glow',          // 30%
  'particle_burst',       // 15%
  'holographic_shift',    // 10%
  'rainbow_shimmer',      // 4%
  'legendary_aura'        // 1% (solo para tickets ganadores)
];
```

---

## 🎲 SISTEMA DE RAREZA

### **Niveles de Rareza de NFTs**

```
┌─────────────────────────────────────────────────────┐
│ COMÚN (60%)                                         │
├─────────────────────────────────────────────────────┤
│ - Background: Básicos                               │
│ - Element: Comunes (clover, coin, star)            │
│ - Effects: None o subtle_glow                       │
│ - Valor Coleccionable: Bajo                         │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│ RARO (30%)                                          │
├─────────────────────────────────────────────────────┤
│ - Background: Especiales (blockchain, matrix)       │
│ - Element: Raros (diamond, eye, 777)               │
│ - Effects: particle_burst                           │
│ - Valor Coleccionable: Medio                        │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│ ÉPICO (9%)                                          │
├─────────────────────────────────────────────────────┤
│ - Background: lucky_stars                           │
│ - Element: Épicos (crystal_ball, unicorn)          │
│ - Effects: holographic_shift o rainbow_shimmer      │
│ - Valor Coleccionable: Alto                         │
│ - Bonus: +0.1% probabilidad extra de ganar         │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│ LEGENDARIO (1%)                                     │
├─────────────────────────────────────────────────────┤
│ - Background: crypto_circuit (animado)              │
│ - Element: dragon_fortune                           │
│ - Effects: legendary_aura                           │
│ - Valor Coleccionable: Muy Alto                     │
│ - Bonus: +0.5% probabilidad extra                   │
│ - Feature: Animación especial en marketplace        │
└─────────────────────────────────────────────────────┘
```

**IMPORTANTE**: La rareza NO afecta las probabilidades de ganar (son iguales), 
pero los NFTs raros tienen valor coleccionable y pueden revenderse.

---

## 🖼️ ESTRUCTURA DEL METADATA

### **Metadata JSON del NFT (ERC-721)**

```json
{
  "name": "BlockLotto Ticket #1248-0042",
  "description": "BlockLotto lottery ticket for Draw #1248. Good luck!",
  "image": "ipfs://QmXxxx.../ticket-1248-0042.png",
  "external_url": "https://blocklotto.io/ticket/1248-0042",
  "attributes": [
    {
      "trait_type": "Draw Number",
      "value": 1248
    },
    {
      "trait_type": "Ticket ID",
      "value": "1248-0042"
    },
    {
      "trait_type": "Numbers",
      "value": "07, 14, 23, 31, 42, 55"
    },
    {
      "trait_type": "Background",
      "value": "cosmic_purple"
    },
    {
      "trait_type": "Main Element",
      "value": "lucky_clover"
    },
    {
      "trait_type": "Color Scheme",
      "value": "purple_gold"
    },
    {
      "trait_type": "Special Effect",
      "value": "subtle_glow"
    },
    {
      "trait_type": "Rarity",
      "value": "Common"
    },
    {
      "trait_type": "Purchase Date",
      "value": "2026-02-24T15:30:00Z"
    },
    {
      "trait_type": "Blockchain",
      "value": "Polygon"
    },
    {
      "trait_type": "Status",
      "value": "Pending"
    }
  ],
  "properties": {
    "draw_timestamp": 1709056800,
    "is_winner": false,
    "prize_amount": 0,
    "animation_url": "ipfs://QmXxxx.../ticket-1248-0042.mp4" // Para legendarios
  }
}
```

---

## 💻 IMPLEMENTACIÓN TÉCNICA

### **1. Generador de Arte (Backend - Node.js)**

```javascript
// ticket-art-generator.js
const Canvas = require('canvas');
const { createCanvas, loadImage } = Canvas;
const { NFTStorage } = require('nft.storage');

class TicketArtGenerator {
  constructor() {
    this.width = 1080;
    this.height = 1350;
    this.nftStorage = new NFTStorage({ token: process.env.NFT_STORAGE_KEY });
  }
  
  // Generar seed único basado en ticket ID y draw
  generateSeed(drawId, ticketId) {
    const hash = ethers.utils.keccak256(
      ethers.utils.solidityPack(
        ['uint256', 'uint256'],
        [drawId, ticketId]
      )
    );
    return parseInt(hash.slice(0, 10), 16);
  }
  
  // Determinar rareza basada en seed
  determineRarity(seed) {
    const rng = seed % 100;
    
    if (rng < 1) return 'legendary';      // 1%
    if (rng < 10) return 'epic';          // 9%
    if (rng < 40) return 'rare';          // 30%
    return 'common';                       // 60%
  }
  
  // Seleccionar elementos basados en rareza
  selectElements(rarity, seed) {
    // Usar seed para selección determinística
    const rng = (value) => ((seed * value) % 100) / 100;
    
    const rarityConfig = {
      legendary: {
        backgrounds: ['crypto_circuit'],
        elements: ['dragon_fortune'],
        effects: ['legendary_aura']
      },
      epic: {
        backgrounds: ['lucky_stars'],
        elements: ['crystal_ball', 'cyber_unicorn'],
        effects: ['holographic_shift', 'rainbow_shimmer']
      },
      rare: {
        backgrounds: ['blockchain_nodes', 'matrix_rain', 'holographic_waves'],
        elements: ['diamond_blockchain', 'fortune_eye', 'lucky_777'],
        effects: ['particle_burst']
      },
      common: {
        backgrounds: ['cosmic_purple', 'neon_grid', 'golden_gradient'],
        elements: ['lucky_clover', 'golden_coin', 'shooting_star', 'crescent_moon'],
        effects: ['none', 'subtle_glow']
      }
    };
    
    const config = rarityConfig[rarity];
    
    return {
      background: config.backgrounds[Math.floor(rng(1) * config.backgrounds.length)],
      element: config.elements[Math.floor(rng(2) * config.elements.length)],
      effect: config.effects[Math.floor(rng(3) * config.effects.length)],
      colorScheme: this.selectColorScheme(rng(4))
    };
  }
  
  // Generar imagen del ticket
  async generateTicketArt(drawId, ticketId, numbers) {
    const seed = this.generateSeed(drawId, ticketId);
    const rarity = this.determineRarity(seed);
    const elements = this.selectElements(rarity, seed);
    
    const canvas = createCanvas(this.width, this.height);
    const ctx = canvas.getContext('2d');
    
    // 1. Dibujar background
    await this.drawBackground(ctx, elements.background, elements.colorScheme);
    
    // 2. Dibujar elemento principal
    await this.drawMainElement(ctx, elements.element, elements.colorScheme);
    
    // 3. Dibujar elementos decorativos
    await this.drawDecorativeElements(ctx, seed, elements.colorScheme);
    
    // 4. Dibujar información del ticket
    this.drawTicketInfo(ctx, drawId, ticketId, numbers, rarity);
    
    // 5. Aplicar efectos especiales
    this.applyEffects(ctx, elements.effect);
    
    // 6. Dibujar marca de agua BlockLotto
    this.drawWatermark(ctx);
    
    return canvas.toBuffer('image/png');
  }
  
  async drawBackground(ctx, type, colorScheme) {
    switch(type) {
      case 'cosmic_purple':
        this.drawCosmicBackground(ctx, colorScheme);
        break;
      case 'neon_grid':
        this.drawNeonGrid(ctx, colorScheme);
        break;
      case 'blockchain_nodes':
        this.drawBlockchainNodes(ctx, colorScheme);
        break;
      case 'crypto_circuit':
        this.drawCryptoCircuit(ctx, colorScheme);
        break;
      // ... más backgrounds
    }
  }
  
  drawCosmicBackground(ctx, colors) {
    // Gradiente base
    const gradient = ctx.createRadialGradient(
      540, 675, 0,
      540, 675, 800
    );
    gradient.addColorStop(0, colors.primary + 'dd');
    gradient.addColorStop(0.5, colors.secondary + '88');
    gradient.addColorStop(1, '#0a0a1f');
    
    ctx.fillStyle = gradient;
    ctx.fillRect(0, 0, this.width, this.height);
    
    // Estrellas aleatorias
    for (let i = 0; i < 200; i++) {
      const x = Math.random() * this.width;
      const y = Math.random() * this.height;
      const size = Math.random() * 2;
      
      ctx.beginPath();
      ctx.arc(x, y, size, 0, Math.PI * 2);
      ctx.fillStyle = 'rgba(255, 255, 255, ' + (Math.random() * 0.8 + 0.2) + ')';
      ctx.fill();
    }
  }
  
  drawNeonGrid(ctx, colors) {
    ctx.fillStyle = '#0a0a1f';
    ctx.fillRect(0, 0, this.width, this.height);
    
    // Grid lines
    ctx.strokeStyle = colors.primary + '44';
    ctx.lineWidth = 2;
    
    const gridSize = 60;
    
    // Líneas verticales
    for (let x = 0; x < this.width; x += gridSize) {
      ctx.beginPath();
      ctx.moveTo(x, 0);
      ctx.lineTo(x, this.height);
      ctx.stroke();
    }
    
    // Líneas horizontales
    for (let y = 0; y < this.height; y += gridSize) {
      ctx.beginPath();
      ctx.moveTo(0, y);
      ctx.lineTo(this.width, y);
      ctx.stroke();
    }
    
    // Puntos de intersección brillantes
    ctx.fillStyle = colors.secondary;
    for (let x = 0; x < this.width; x += gridSize) {
      for (let y = 0; y < this.height; y += gridSize) {
        if (Math.random() > 0.7) {
          ctx.beginPath();
          ctx.arc(x, y, 3, 0, Math.PI * 2);
          ctx.fill();
        }
      }
    }
  }
  
  drawBlockchainNodes(ctx, colors) {
    ctx.fillStyle = '#0a0a1f';
    ctx.fillRect(0, 0, this.width, this.height);
    
    // Nodos
    const nodes = [];
    for (let i = 0; i < 15; i++) {
      nodes.push({
        x: Math.random() * this.width,
        y: Math.random() * this.height,
        radius: Math.random() * 20 + 10
      });
    }
    
    // Conexiones entre nodos
    ctx.strokeStyle = colors.primary + '44';
    ctx.lineWidth = 2;
    
    for (let i = 0; i < nodes.length; i++) {
      for (let j = i + 1; j < nodes.length; j++) {
        const dx = nodes[j].x - nodes[i].x;
        const dy = nodes[j].y - nodes[i].y;
        const distance = Math.sqrt(dx * dx + dy * dy);
        
        if (distance < 300) {
          ctx.beginPath();
          ctx.moveTo(nodes[i].x, nodes[i].y);
          ctx.lineTo(nodes[j].x, nodes[j].y);
          ctx.stroke();
        }
      }
    }
    
    // Dibujar nodos
    nodes.forEach(node => {
      const gradient = ctx.createRadialGradient(
        node.x, node.y, 0,
        node.x, node.y, node.radius
      );
      gradient.addColorStop(0, colors.secondary);
      gradient.addColorStop(1, colors.primary + '88');
      
      ctx.beginPath();
      ctx.arc(node.x, node.y, node.radius, 0, Math.PI * 2);
      ctx.fillStyle = gradient;
      ctx.fill();
      
      ctx.strokeStyle = colors.secondary;
      ctx.lineWidth = 3;
      ctx.stroke();
    });
  }
  
  drawTicketInfo(ctx, drawId, ticketId, numbers, rarity) {
    // Fondo semi-transparente para info
    ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
    ctx.fillRect(0, this.height - 350, this.width, 350);
    
    // Rarity badge
    const rarityColors = {
      legendary: '#ff6600',
      epic: '#9f7aea',
      rare: '#4299e1',
      common: '#718096'
    };
    
    ctx.fillStyle = rarityColors[rarity];
    ctx.font = 'bold 32px Arial';
    ctx.textAlign = 'center';
    ctx.fillText(rarity.toUpperCase(), 540, this.height - 290);
    
    // Ticket ID
    ctx.fillStyle = '#ffffff';
    ctx.font = 'bold 36px Arial';
    ctx.fillText(`TICKET #${drawId}-${ticketId}`, 540, this.height - 240);
    
    // Números seleccionados
    ctx.font = 'bold 48px Arial';
    const numbersText = numbers.map(n => n.toString().padStart(2, '0')).join('  ');
    ctx.fillText(numbersText, 540, this.height - 180);
    
    // Draw info
    ctx.font = '24px Arial';
    ctx.fillStyle = '#a0aec0';
    ctx.fillText(`Draw #${drawId}`, 540, this.height - 120);
    
    // Logo BlockLotto
    ctx.font = 'bold 28px Arial';
    ctx.fillStyle = '#667eea';
    ctx.fillText('🎰 BlockLotto', 540, this.height - 60);
  }
  
  // Subir a IPFS/NFT.Storage
  async uploadToIPFS(imageBuffer, metadata) {
    const imageFile = new File([imageBuffer], 'ticket.png', { type: 'image/png' });
    
    const nft = await this.nftStorage.store({
      image: imageFile,
      name: metadata.name,
      description: metadata.description,
      attributes: metadata.attributes
    });
    
    return nft.url; // ipfs://...
  }
}

module.exports = TicketArtGenerator;
```

### **2. Integración con Smart Contract**

```solidity
// TicketNFT.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";

contract TicketNFT is ERC721URIStorage {
    uint256 private _tokenIdCounter;
    
    struct TicketMetadata {
        uint256 drawId;
        uint8[6] numbers;
        string rarity; // "common", "rare", "epic", "legendary"
        uint256 mintTimestamp;
    }
    
    mapping(uint256 => TicketMetadata) public ticketData;
    
    // Evento para generación de arte
    event TicketMinted(
        uint256 indexed tokenId,
        uint256 indexed drawId,
        address indexed owner,
        uint8[6] numbers,
        string rarity
    );
    
    function mintTicket(
        address to,
        uint256 drawId,
        uint8[6] memory numbers,
        string memory tokenURI
    ) external returns (uint256) {
        _tokenIdCounter++;
        uint256 newTokenId = _tokenIdCounter;
        
        // Determinar rareza basado en hash del ticket
        string memory rarity = _determineRarity(drawId, newTokenId);
        
        _safeMint(to, newTokenId);
        _setTokenURI(newTokenId, tokenURI);
        
        ticketData[newTokenId] = TicketMetadata({
            drawId: drawId,
            numbers: numbers,
            rarity: rarity,
            mintTimestamp: block.timestamp
        });
        
        emit TicketMinted(newTokenId, drawId, to, numbers, rarity);
        
        return newTokenId;
    }
    
    function _determineRarity(uint256 drawId, uint256 tokenId) 
        private 
        pure 
        returns (string memory) 
    {
        uint256 seed = uint256(keccak256(abi.encodePacked(drawId, tokenId)));
        uint256 rng = seed % 100;
        
        if (rng < 1) return "legendary";
        if (rng < 10) return "epic";
        if (rng < 40) return "rare";
        return "common";
    }
}
```

---

## 🎭 MARKETPLACE SECUNDARIO

### **Trading de Boletos NFT**

Los usuarios pueden:
1. **Vender boletos antes del sorteo** en OpenSea/Blur
2. **Coleccionar boletos raros** aunque no ganen
3. **NFTs ganadores** valen MUCHO más (premio + valor coleccionable)

**Ejemplo de Precios Secundarios:**
```
Boleto Común (no ganador): $0.50 - $2
Boleto Raro (no ganador): $5 - $15
Boleto Épico (no ganador): $20 - $50
Boleto Legendario (no ganador): $100 - $500

Boleto Ganador 6/6: Precio del premio + 20-50% premium
Boleto Legendario Ganador: Premium histórico masivo
```

---

## 💡 FEATURES ADICIONALES

### **1. Colecciones Especiales**

```
🎃 Halloween Special Draw
- Backgrounds temáticos de Halloween
- Elementos: Calabazas, fantasmas, brujas
- Solo disponibles en octubre

🎄 Christmas Special Draw
- Tema navideño con nieve
- Elementos: Santa, renos, regalos
- Diciembre únicamente

🐉 Lunar New Year
- Tema asiático con dragones
- Elementos: Dragón, fuegos artificiales
- Febrero (Año Nuevo Lunar)
```

### **2. Animaciones para Legendarios**

Los NFTs legendarios incluyen:
- Archivo MP4 animado (10 segundos)
- Efectos de partículas
- Brillos y destellos
- Transiciones suaves

### **3. Actualización Post-Draw**

Cuando se completa el sorteo:
- Metadata se actualiza con `"status": "winner"` o `"no_winner"`
- Si es ganador, se agrega `"prize_amount"`
- NFT muestra badge de "GANADOR" visual

---

## 📊 VALOR AGREGADO AL NEGOCIO

### **Revenue Adicional por NFTs**

1. **Royalties Secundarios**: 5% en cada reventa
   - Si un boleto se revende 100 veces a $10 promedio
   - Royalties: $50 ($0.50 por transacción)
   - Con 10,000 tickets/mes: +$5,000/mes en royalties

2. **NFTs Premium**: Opción de "upgrade" visual
   - Usuarios pagan $1 extra por garantizar rareza mínima "Rare"
   - 10% de usuarios upgraden = +$1,000/mes adicional

3. **Valor de Marca**: NFTs coleccionables generan:
   - Marketing viral en OpenSea
   - Comunidad de coleccionistas
   - Mayor engagement

**Revenue Extra Estimado**: $6,000 - $10,000/mes

---

## 🎨 CONCLUSIÓN

Los NFTs con arte generativo convierten cada boleto en:
- ✅ Una pieza de arte única y coleccionable
- ✅ Un activo tradeable con valor secundario
- ✅ Una experiencia gamificada (búsqueda de rarezas)
- ✅ Marketing orgánico en OpenSea/Blur
- ✅ Mayor engagement y retención

**Diferenciador Clave**: Somos la ÚNICA lotería blockchain con NFTs coleccionables de alta calidad artística.

