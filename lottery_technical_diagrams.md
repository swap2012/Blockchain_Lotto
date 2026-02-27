# 🎰 LOTERÍA BLOCKCHAIN - DIAGRAMAS TÉCNICOS DETALLADOS

## DIAGRAMA 1: Arquitectura General del Sistema

```mermaid
graph TB
    subgraph "Frontend Layer"
        A[Web App - Next.js]
        B[Mobile App - React Native]
    end
    
    subgraph "Web3 Connection"
        C[Wagmi + Viem]
        D[RainbowKit/WalletConnect]
    end
    
    subgraph "Polygon PoS Blockchain"
        E[LotteryCore Contract]
        F[TicketNFT Contract]
        G[PrizePool Contract]
        H[DrawManager Contract]
    end
    
    subgraph "Chainlink Services"
        I[VRF v2 - Random Numbers]
        J[Automation - Auto Draw]
        K[Price Feeds - USD/MATIC]
    end
    
    subgraph "Data Layer"
        L[The Graph - Indexing]
        M[IPFS - Metadata]
        N[PostgreSQL - Cache]
    end
    
    A --> C
    B --> C
    C --> D
    D --> E
    E --> F
    E --> G
    E --> H
    E --> I
    E --> J
    E --> K
    E --> L
    F --> M
    L --> N
    N --> A
    N --> B
    
    style E fill:#667eea,color:#fff
    style I fill:#375bd2,color:#fff
    style J fill:#375bd2,color:#fff
```

## DIAGRAMA 2: Flujo de Compra de Boleto

```mermaid
sequenceDiagram
    participant User
    participant Wallet
    participant Frontend
    participant LotteryCore
    participant TicketNFT
    participant IPFS
    participant TheGraph
    
    User->>Frontend: Selecciona 6 números
    Frontend->>Wallet: Solicita firma transacción
    Wallet->>User: Confirma (Gas: ~$0.02)
    User->>Wallet: Aprueba
    Wallet->>LotteryCore: buyTicket(numbers, value)
    
    LotteryCore->>LotteryCore: Valida números únicos
    LotteryCore->>LotteryCore: Valida pago >= ticketPrice
    LotteryCore->>PrizePool: Transfiere fondos
    LotteryCore->>TicketNFT: mintTicket(user, ticketId)
    
    TicketNFT->>IPFS: Sube metadata
    IPFS-->>TicketNFT: Retorna URI
    TicketNFT->>User: Transfiere NFT
    
    LotteryCore->>TheGraph: Emite evento TicketPurchased
    TheGraph->>Frontend: Actualiza UI
    Frontend->>User: Muestra confirmación + NFT
```

## DIAGRAMA 3: Flujo de Sorteo Automático

```mermaid
sequenceDiagram
    participant Automation as Chainlink Automation
    participant DrawManager
    participant VRF as Chainlink VRF
    participant LotteryCore
    participant Winners as Usuarios Ganadores
    
    Note over Automation: Cada 3 días (timestamp alcanzado)
    Automation->>DrawManager: checkUpkeep()
    DrawManager-->>Automation: upkeepNeeded = true
    Automation->>DrawManager: performUpkeep()
    
    DrawManager->>VRF: requestRandomWords()
    Note over VRF: Genera números aleatorios verificables
    VRF->>DrawManager: fulfillRandomWords(randomness)
    
    DrawManager->>DrawManager: Genera 6 números únicos (1-56)
    DrawManager->>LotteryCore: setWinningNumbers(drawId, numbers)
    
    LotteryCore->>LotteryCore: Emite DrawCompleted event
    
    Note over Winners: Usuarios verifican boletos
    Winners->>LotteryCore: claimPrize(ticketId)
    LotteryCore->>LotteryCore: Calcula aciertos
    LotteryCore->>LotteryCore: Valida no reclamado
    LotteryCore->>Winners: Transfiere premio
    LotteryCore->>LotteryCore: Marca como claimed
```

## DIAGRAMA 4: Arquitectura de Smart Contracts

```mermaid
classDiagram
    class LotteryCore {
        +uint256 currentDrawId
        +uint256 drawTimestamp
        +uint256 prizePool
        +mapping tickets
        +buyTicket(numbers[])
        +claimPrize(ticketId)
        +getWinningNumbers(drawId)
        -validateTicket()
        -calculatePrize()
    }
    
    class TicketNFT {
        +uint256 ticketCounter
        +string baseURI
        +mint(to, ticketId)
        +tokenURI(ticketId)
        +getTicketData(ticketId)
    }
    
    class PrizePool {
        +uint256 totalPool
        +uint256 reservePool
        +mapping prizeDistribution
        +depositFunds()
        +distributePrize(winner, amount)
        +getPoolBalance()
    }
    
    class DrawManager {
        +uint256 drawInterval
        +uint256 lastDrawTime
        +requestRandomNumbers()
        +fulfillRandomWords(randomness)
        +triggerDraw()
        -generateWinningNumbers()
    }
    
    class VRFConsumer {
        <<Chainlink VRF>>
        +bytes32 keyHash
        +uint64 subscriptionId
        +requestRandomWords()
        #fulfillRandomWords()
    }
    
    class AutomationCompatible {
        <<Chainlink Automation>>
        +checkUpkeep()
        +performUpkeep()
    }
    
    LotteryCore --> TicketNFT : mints
    LotteryCore --> PrizePool : manages
    LotteryCore --> DrawManager : coordinates
    DrawManager --|> VRFConsumer : inherits
    DrawManager --|> AutomationCompatible : implements
```

## DIAGRAMA 5: Distribución de Premios

```mermaid
pie title Distribución del Prize Pool
    "6 Aciertos (Jackpot)" : 50
    "5 Aciertos" : 20
    "4 Aciertos" : 15
    "3 Aciertos" : 10
    "Reserva Protocolo" : 5
```

## DIAGRAMA 6: Estrategia Multi-Chain (Fase 2)

```mermaid
graph LR
    subgraph "Chain Independent Instances"
        A[Polygon Instance]
        B[Arbitrum Instance]
        C[Base Instance]
    end
    
    subgraph "Shared Services"
        D[The Graph - Multi-chain]
        E[IPFS - Metadata]
    end
    
    subgraph "Frontend Aggregator"
        F[Unified dApp]
    end
    
    A --> D
    B --> D
    C --> D
    
    A --> E
    B --> E
    C --> E
    
    D --> F
    E --> F
    
    style A fill:#8247e5,color:#fff
    style B fill:#28a0f0,color:#fff
    style C fill:#0052ff,color:#fff
```

## DIAGRAMA 7: Flujo de Seguridad - Prevención de Reentrancy

```mermaid
stateDiagram-v2
    [*] --> CheckWinner: claimPrize(ticketId)
    CheckWinner --> CheckClaimed: isWinner = true
    CheckClaimed --> SetClaimed: not claimed
    SetClaimed --> TransferFunds: claimed = true
    TransferFunds --> EmitEvent: transfer success
    EmitEvent --> [*]
    
    CheckWinner --> [*]: not winner (revert)
    CheckClaimed --> [*]: already claimed (revert)
    TransferFunds --> [*]: transfer failed (revert)
    
    note right of SetClaimed
        CRITICAL: Marca como claimed
        ANTES de transferir fondos
        (Patrón Checks-Effects-Interactions)
    end note
```

## DIAGRAMA 8: Tokenomics y Flujo de Fondos

```mermaid
graph TD
    A[Usuario Compra Boleto: $2] --> B{PrizePool Contract}
    
    B -->|70%| C[Current Draw Pool: $1.40]
    B -->|20%| D[Jackpot Acumulado: $0.40]
    B -->|5%| E[Dev Fund: $0.10]
    B -->|5%| F[Protocol Reserve: $0.10]
    
    C --> G[Ganadores Draw Actual]
    D --> H[Ganador Jackpot 6/6]
    E --> I[Desarrollo & Mantenimiento]
    F --> J[Emergencias & Seguros]
    
    style A fill:#667eea,color:#fff
    style G fill:#48bb78,color:#fff
    style H fill:#f6ad55,color:#fff
```

## DIAGRAMA 9: Stack de Desarrollo y Deploy

```mermaid
graph TB
    subgraph "Development"
        A[Hardhat]
        B[Foundry]
        C[Solidity 0.8.20+]
    end
    
    subgraph "Testing"
        D[Mumbai Testnet]
        E[Unit Tests]
        F[Integration Tests]
        G[Fork Tests]
    end
    
    subgraph "Auditing"
        H[OpenZeppelin]
        I[CertiK]
        J[Internal Review]
    end
    
    subgraph "Deployment"
        K[Polygon Mainnet]
        L[OpenZeppelin Defender]
        M[Gnosis Safe Multisig]
    end
    
    A --> D
    B --> D
    C --> D
    D --> E
    D --> F
    D --> G
    
    E --> H
    F --> H
    G --> H
    H --> I
    I --> J
    
    J --> K
    K --> L
    K --> M
```

## DIAGRAMA 10: Monitoring y Alertas

```mermaid
graph LR
    A[Smart Contracts] --> B[Tenderly Monitoring]
    A --> C[OpenZeppelin Defender]
    A --> D[The Graph]
    
    B --> E{Alert System}
    C --> E
    D --> E
    
    E -->|Anomalía Detectada| F[Telegram Bot]
    E -->|Error Crítico| G[Email Team]
    E -->|Tx Sospechosa| H[Pause Contract]
    
    style H fill:#f56565,color:#fff
```

## CONSIDERACIONES TÉCNICAS CLAVE

### 1. Generación de Números Aleatorios (CRÍTICO)

**❌ NUNCA USAR:**
```solidity
// INSEGURO - Manipulable por mineros
uint256 random = uint256(keccak256(abi.encodePacked(block.timestamp)));
uint256 random = uint256(blockhash(block.number - 1));
```

**✅ USAR SIEMPRE:**
```solidity
// SEGURO - Chainlink VRF
function requestRandomWords() external onlyOwner {
    requestId = COORDINATOR.requestRandomWords(
        keyHash,
        subscriptionId,
        requestConfirmations,
        callbackGasLimit,
        numWords
    );
}
```

### 2. Patrón Checks-Effects-Interactions (Anti-Reentrancy)

```solidity
function claimPrize(uint256 ticketId) external nonReentrant {
    // 1. CHECKS
    require(isWinner[ticketId], "Not winner");
    require(!claimed[ticketId], "Already claimed");
    require(msg.sender == ticketOwner[ticketId], "Not owner");
    
    // 2. EFFECTS
    claimed[ticketId] = true;
    
    // 3. INTERACTIONS
    (bool success, ) = msg.sender.call{value: prize}("");
    require(success, "Transfer failed");
}
```

### 3. Upgradability Pattern

```solidity
// Usar UUPS Proxy Pattern (OpenZeppelin)
import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

contract LotteryCore is UUPSUpgradeable, OwnableUpgradeable {
    function _authorizeUpgrade(address newImplementation) 
        internal override onlyOwner {}
}
```

### 4. Gas Optimization

```solidity
// Optimizar lectura/escritura de storage
struct Ticket {
    address player;      // 20 bytes
    uint48 drawId;       // 6 bytes (suficiente para 281 billones de sorteos)
    uint8[6] numbers;    // 6 bytes
    bool claimed;        // 1 byte
    // Total: 33 bytes en 1 slot = GAS EFICIENTE
}
```

### 5. Emergency Mechanisms

```solidity
// Circuit breaker para emergencias
bool public paused;

modifier whenNotPaused() {
    require(!paused, "Contract paused");
    _;
}

function emergencyPause() external onlyOwner {
    paused = true;
    emit EmergencyPaused(block.timestamp);
}
```

---

## RESUMEN EJECUTIVO

### ✅ Decisión Final: POLYGON PoS

**Justificación Técnica:**
1. **Costos**: $0.02/tx vs $15+ en Ethereum
2. **Velocidad**: 7,000 TPS vs 30 TPS en Ethereum
3. **Seguridad**: Chainlink VRF nativo y probado
4. **Adopción**: Amplio soporte de wallets y exchanges
5. **Desarrollo**: EVM compatible, stack conocido

### 🚀 Roadmap

| Fase | Timeline | Objetivo |
|------|----------|----------|
| **MVP** | Mes 1-4 | Deploy en Polygon, primer sorteo |
| **Expansión** | Mes 5-8 | Arbitrum + Base deployment |
| **Scale** | Mes 9-12 | Optimizaciones, governance token |

### 💰 Proyección de Costos

**Desarrollo MVP:**
- Smart Contracts Development: $15,000 - $25,000
- Frontend Development: $10,000 - $20,000
- Auditoría (CertiK/OpenZeppelin): $20,000 - $40,000
- Legal & Compliance: $10,000 - $30,000
- **TOTAL**: $55,000 - $115,000

**Operación Mensual:**
- Chainlink VRF: $100 - $500/mes
- Chainlink Automation: $50 - $200/mes
- Infraestructura (RPC, IPFS): $200 - $500/mes
- Monitoring & Support: $500 - $1,000/mes
- **TOTAL**: $850 - $2,200/mes

---

*Documento generado por experto en Blockchain Architecture & Security*
*Versión 1.0 - Febrero 2026*
