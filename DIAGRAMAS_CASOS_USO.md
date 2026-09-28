# Diagramas de Caso de Uso - Aplicativo de Mobilidade Urbana Integrada

## 1. Diagrama de Atores e Casos de Uso Principal

```mermaid
graph TB
    subgraph Atores
        USER["👤 Usuário"]
        WEATHER["🌦️ Serviço de Clima"]
        TRANSIT["🚌 Sistema de Transporte Público"]
        MAP["🗺️ Serviço de Mapas"]
    end

    subgraph Aplicativo["Sistema de Mobilidade Urbana"]
        UC1["Buscar Rota"]
        UC2["Inserir Ponto de Partida"]
        UC3["Inserir Destino"]
        UC4["Consultar Previsão do Clima"]
        UC5["Filtrar Meios de Transporte"]
        UC6["Recomendar Melhor Rota"]
        UC7["Visualizar Rota no Mapa"]
        UC8["Exibir Detalhes da Viagem"]
        UC9["Calcular Tempo de Viagem"]
        UC10["Estimar Custo da Viagem"]
    end

    USER -->|Interage| UC1
    USER -->|Interage| UC2
    USER -->|Interage| UC3
    USER -->|Interage| UC7
    USER -->|Recebe| UC8
    
    UC1 -->|Depende| UC2
    UC1 -->|Depende| UC3
    UC1 -->|Depende| UC4
    UC1 -->|Depende| UC5
    UC1 -->|Depende| UC6
    
    UC4 -->|Consulta| WEATHER
    UC6 -->|Usa| UC4
    UC5 -->|Usa| UC4
    
    UC6 -->|Resulta| UC7
    UC6 -->|Resulta| UC8
    UC7 -->|Usa| MAP
    UC8 -->|Inclui| UC9
    UC8 -->|Inclui| UC10
    UC8 -->|Usa| TRANSIT
```

## 2. Diagrama de Casos de Uso - Fluxo de Busca de Rota

```mermaid
graph LR
    Start([Usuário Abre App]) --> UC1["Inserir Ponto A<br/>e Ponto B"]
    
    UC1 --> UC2{"Clima<br/>Favorável?"}
    
    UC2 -->|Sim| UC3["Recomendar:<br/>🚌 Ônibus<br/>🚗 Carro<br/>🏍️ Moto<br/>🚲 Bicicleta<br/>🚶 A Pé"]
    
    UC2 -->|Não| UC4{"Tipo de<br/>Chuva?"}
    
    UC4 -->|Leve| UC5["Recomendar:<br/>🚌 Ônibus<br/>🚗 Carro<br/>🏍️ Moto"]
    
    UC4 -->|Forte| UC6["Recomendar:<br/>🚌 Ônibus<br/>🚗 Carro"]
    
    UC3 --> UC7["Calcular Tempos<br/>e Custos"]
    UC5 --> UC7
    UC6 --> UC7
    
    UC7 --> UC8["Exibir Ranking<br/>de Rotas"]
    UC8 --> UC9["Usuário Seleciona<br/>Rota Preferida"]
    UC9 --> End([Iniciar Navegação])
```

## 3. Diagrama de Casos de Uso - Detalhado

```mermaid
graph TB
    subgraph "Usuário Final"
        USER["👤 Usuário"]
    end

    subgraph "Sistema de Mobilidade"
        subgraph "Entrada de Dados"
            UC1["UC1: Definir Ponto de Partida"]
            UC2["UC2: Definir Destino"]
        end

        subgraph "Processamento de Clima"
            UC3["UC3: Consultar Previsão do Clima"]
            UC4["UC4: Analisar Condições Climáticas"]
            UC5["UC5: Determinar Meios Viáveis"]
        end

        subgraph "Cálculo de Rotas"
            UC6["UC6: Buscar Rotas Alternativas"]
            UC7["UC7: Calcular Tempo Estimado"]
            UC8["UC8: Estimar Custo"]
            UC9["UC9: Ordenar Rotas por Critério"]
        end

        subgraph "Apresentação"
            UC10["UC10: Exibir Rotas Recomendadas"]
            UC11["UC11: Mostrar Detalhes da Viagem"]
            UC12["UC12: Visualizar Rota no Mapa"]
        end

        subgraph "Finalização"
            UC13["UC13: Selecionar Rota"]
            UC14["UC14: Iniciar Navegação"]
        end
    end

    subgraph "Sistemas Externos"
        WEATHER["🌦️ API de Clima"]
        MAP["🗺️ API de Mapas"]
        TRANSIT["🚌 API de Transporte Público"]
    end

    USER -->|Interage| UC1
    USER -->|Interage| UC2
    
    UC1 --> UC3
    UC2 --> UC3
    
    UC3 -->|Conecta| WEATHER
    UC3 --> UC4
    UC4 --> UC5
    
    UC5 --> UC6
    UC6 --> UC7
    UC6 --> UC8
    
    UC7 --> UC9
    UC8 --> UC9
    
    UC9 --> UC10
    UC10 --> USER
    
    USER -->|Seleciona| UC13
    UC13 --> UC11
    UC13 --> UC12
    
    UC12 -->|Usa| MAP
    UC11 --> UC14
    UC14 --> USER

    UC6 -->|Consulta| TRANSIT
    UC7 -->|Consulta| MAP
```

## 4. Diagrama de Sequência - Busca de Rota

```mermaid
sequenceDiagram
    participant User as 👤 Usuário
    participant App as 📱 App Mobilidade
    participant Weather as 🌦️ Serviço Clima
    participant Maps as 🗺️ Serviço Mapas
    participant Transit as 🚌 Serviço Transporte

    User->>App: 1. Solicita rota A → B
    
    App->>Maps: 2. Buscar localização A e B
    Maps-->>App: 3. Coordenadas recebidas
    
    App->>Weather: 4. Consultar previsão do clima
    Weather-->>App: 5. Dados climáticos recebidos
    
    App->>App: 6. Analisar condições climáticas
    
    alt Clima Favorável ☀️
        App->>Transit: 7a. Buscar rotas com todos os meios
        Note over App: Incluir: 🚌 Ônibus, 🚗 Carro, 🏍️ Moto,<br/>🚲 Bicicleta, 🚶 A Pé
    else Chuva Leve 🌧️
        App->>Transit: 7b. Buscar rotas protegidas
        Note over App: Incluir: 🚌 Ônibus, 🚗 Carro, 🏍️ Moto
    else Chuva Forte ⛈️
        App->>Transit: 7c. Buscar apenas meios cobertos
        Note over App: Incluir: 🚌 Ônibus, 🚗 Carro
    end
    
    Transit-->>App: 8. Rotas recomendadas recebidas
    
    App->>App: 9. Calcular tempos e custos para cada rota
    App->>App: 10. Ordenar rotas por eficiência
    
    App-->>User: 11. Exibir ranking de rotas
    
    User->>App: 12. Seleciona rota preferida
    App-->>User: 13. Exibir mapa com rota detalhada
    App-->>User: 14. Iniciar navegação
```

## 5. Diagrama de Estados - Ciclo de Vida da Busca

```mermaid
stateDiagram-v2
    [*] --> AguardandoEntrada: App Iniciado

    AguardandoEntrada --> ColetandoDados: Usuário abre busca
    ColetandoDados --> InsindoPontoA: 
    InsindoPontoA --> InsindoPontoB: 
    InsindoPontoB --> BuscandoClima: Ambos os pontos validados

    BuscandoClima --> AnalisandoClima: Dados climáticos recebidos
    AnalisandoClima --> DefinindoMeios: Análise concluída

    DefinindoMeios --> ClimaFavorável: Condição: Ensolarado/Nublado
    DefinindoMeios --> ChuvaLeve: Condição: Chuva leve
    DefinindoMeios --> ChuvaForte: Condição: Chuva forte

    ClimaFavorável --> BuscandoRotas: Meios definidos
    ChuvaLeve --> BuscandoRotas: 
    ChuvaForte --> BuscandoRotas: 

    BuscandoRotas --> CalculandoRotas: Rotas recebidas
    CalculandoRotas --> OrdenandoRotas: Tempos e custos calculados
    OrdenandoRotas --> ExibindoRotas: Rotas ordenadas

    ExibindoRotas --> SelecionandoRota: Usuário visualiza opções
    SelecionandoRota --> VisualizandoMapa: Rota selecionada
    VisualizandoMapa --> Navegando: Usuário inicia navegação

    Navegando --> AguardandoEntrada: Viagem finalizada ou nova busca
```

## 6. Diagrama de Componentes - Arquitetura do Sistema

```mermaid
graph TB
    subgraph UI["Interface do Usuário"]
        UI1["Tela de Busca"]
        UI2["Tela de Resultados"]
        UI3["Mapa Interativo"]
        UI4["Detalhes da Viagem"]
    end

    subgraph LOGIC["Lógica de Negócio"]
        LOGIC1["Motor de Recomendação"]
        LOGIC2["Analisador de Clima"]
        LOGIC3["Calculador de Rotas"]
        LOGIC4["Filtrador de Meios"]
    end

    subgraph DATA["Gerenciamento de Dados"]
        DATA1["Histórico de Viagens"]
        DATA2["Preferências do Usuário"]
        DATA3["Cache de Rotas"]
    end

    subgraph API["Integrações Externas"]
        API1["API de Clima<br/>OpenWeatherMap"]
        API2["API de Mapas<br/>Google Maps"]
        API3["API de Transporte<br/>Sistemas Locais"]
    end

    UI1 --> LOGIC1
    UI2 --> LOGIC1
    UI3 --> LOGIC3
    UI4 --> LOGIC1

    LOGIC1 --> LOGIC2
    LOGIC1 --> LOGIC3
    LOGIC1 --> LOGIC4
    
    LOGIC2 --> API1
    LOGIC3 --> API2
    LOGIC3 --> API3
    LOGIC4 --> LOGIC2

    LOGIC1 --> DATA1
    LOGIC1 --> DATA2
    LOGIC3 --> DATA3

    DATA1 -.->|Otimiza| LOGIC1
    DATA2 -.->|Personaliza| LOGIC1
```

## 7. Matriz de Recomendação - Meios de Transporte por Clima

```mermaid
graph TB
    subgraph Clima["Análise Climática"]
        C1["☀️ Ensolarado<br/>Temperatura Ideal"]
        C2["☁️ Nublado<br/>Sem Precipitação"]
        C3["🌧️ Chuva Leve<br/>Precipitação ≤ 10mm"]
        C4["⛈️ Chuva Forte<br/>Precipitação > 10mm"]
        C5["❄️ Neve/Granizo"]
        C6["💨 Ventania Forte"]
    end

    subgraph Meios["Meios de Transporte"]
        M1["🚶 A Pé"]
        M2["🚲 Bicicleta"]
        M3["🏍️ Motocicleta"]
        M4["🚗 Carro"]
        M5["🚌 Ônibus/Metrô"]
    end

    subgraph Score["Recomendação"]
        S1["⭐⭐⭐⭐⭐ Altamente Recomendado"]
        S2["⭐⭐⭐⭐ Recomendado"]
        S3["⭐⭐⭐ Aceitável"]
        S4["⭐⭐ Pouco Recomendado"]
        S5["⭐ Não Recomendado"]
    end

    C1 --> M1
    C1 --> M2
    C1 --> M3
    C1 --> M4
    C1 --> M5
    
    M1 -.-> S1
    M2 -.-> S1
    M3 -.-> S2
    M4 -.-> S2
    M5 -.-> S1

    C2 --> M1
    C2 --> M2
    C2 --> M3
    C2 --> M4
    C2 --> M5
    
    C3 --> M3
    C3 --> M4
    C3 --> M5
    M3 -.-> S3
    M4 -.-> S1
    M5 -.-> S1
    M1 -.-> S5
    M2 -.-> S5

    C4 --> M4
    C4 --> M5
    M4 -.-> S2
    M5 -.-> S1

    C5 --> M4
    C5 --> M5
    
    C6 --> M4
    C6 --> M5
```

## Legenda de Ícones

| Ícone | Significado |
|-------|-------------|
| 👤 | Usuário |
| 🌦️ | Serviço de Clima |
| 🗺️ | Serviço de Mapas |
| 🚌 | Transporte Público/Ônibus |
| 🚗 | Automóvel |
| 🏍️ | Motocicleta |
| 🚲 | Bicicleta |
| 🚶 | A Pé |
| ☀️ | Ensolarado |
| ☁️ | Nublado |
| 🌧️ | Chuva Leve |
| ⛈️ | Chuva Forte |
| ❄️ | Neve/Granizo |
| 💨 | Ventania Forte |

## Resumo dos Casos de Uso

### Atores Principais
1. **Usuário** - Pessoa que busca uma rota de mobilidade
2. **Serviço de Clima** - Fornece previsão meteorológica
3. **Serviço de Mapas** - Fornece informações geográficas
4. **Serviço de Transporte Público** - Fornece dados de transporte

### Casos de Uso Principais
1. **Buscar Rota** - Caso de uso central que coordena todos os outros
2. **Consultar Previsão do Clima** - Obtém dados meteorológicos para filtração
3. **Filtrar Meios de Transporte** - Determina quais meios são viáveis
4. **Recomendar Melhor Rota** - Seleciona as melhores opções baseado em clima e preferências
5. **Visualizar Rota no Mapa** - Exibe a rota graficamente

### Lógica de Filtração por Clima
- **Clima Favorável** (Ensolarado/Nublado): Todos os 5 meios disponíveis
- **Chuva Leve**: 🚌 Ônibus, 🚗 Carro, 🏍️ Moto (sem a pé ou bicicleta)
- **Chuva Forte**: 🚌 Ônibus, 🚗 Carro (apenas meios totalmente cobertos)

### Critérios de Recomendação
1. Viabilidade (baseado no clima)
2. Tempo de deslocamento
3. Custo estimado
4. Conforto
5. Preferências do usuário
