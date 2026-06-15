# Teste QA - Performance AGI Blazedemo

## Descrição
Este projeto contém a automação de testes de performance do site BlazeDemo, desenvolvida como parte de um teste técnico para a vaga de QA.

O objetivo da automação é validar o fluxo fim-a-fim de compra de passagens aéreas, garantindo a estabilidade e a elasticidade do sistema sob carga estável de acordo com os critérios de aceitação definidos pelo negócio.

A automação foi implementada utilizando:

* Apache JMeter 5.x
* Java JDK 11 ou superior
* Precise Throughput Timer (Controle de RPS)
* HTTP Cookie Manager (Gerenciamento de Sessão)

---

## Cenários Automatizados
Com base nos requisitos propostos, foi modelado um cenário de carga contínua englobando as seguintes requisições sequenciais do fluxo de checkout:

### 1. Acessar a Home Page
Valida a abertura da página inicial da aplicação para o início da jornada do usuário.
* **Endpoint:** `GET /`

### 2. Buscar Voos disponíveis
Simula a seleção das cidades de origem e destino, enviando os dados do formulário de pesquisa.
* **Endpoint:** `POST /reserve.php`

### 3. Selecionar o Voo desejado
Valida a escolha de uma das opções de voo retornadas pelo sistema.
* **Endpoint:** `POST /purchase.php`

### 4. Confirmar a Compra com sucesso
Simula o preenchimento dos dados do comprador e do cartão de crédito, finalizando a aquisição da passagem.
* **Endpoint:** `POST /confirmation.php`

---

## Estrutura do Projeto
```text
testeperformance_qa
├── scripts
│   └── testeperformance-qa.jmx
│
└── reports
    ├── results.jtl
    └── dashboard_html
        ├── content
        ├── sbadmin2-1.0.7
        ├── index.html
        └── statistics.json
```

---

## Pré-requisitos
* Java JDK 8 ou superior instalado e configurado
* Apache JMeter 5.x configurado nas Variáveis de Ambiente do sistema (`Path`)

---

## Instalação
Clone o repositório:
```bash
git clone https://github.com
```

Acesse a pasta do projeto:
```bash
cd testeperformance_qa
```

---

## Execução dos Testes
Para mitigar o consumo de recursos de hardware locais, o teste foi projetado para rodar exclusivamente via linha de comando (**Non-GUI Mode**).

Executar os testes de performance limpando relatórios antigos automaticamente:
```bash
jmeter -f -n -t scripts/testeperformance-qa.jmx -l reports/results.jtl -e -o reports/dashboard_html
```

---

## Relatório de Execução dos Testes (KPIs Obtidos)
Abaixo está a consolidação exata extraída do dashboard gráfico em HTML gerado pelo JMeter após o encerramento da execução:

| Transação / Passo do Fluxo | Total de Amostras | Vazão (Throughput) | Tempo Médio | Percentil 90 (90th%) | Taxa de Erro |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **01_Home** | 7663 | 63.85 req/s | 322.02 ms | **499.00 ms** | 0.00% |
| **02_Search_Flights** | 7606 | 63.37 req/s | 311.03 ms | **520.00 ms** | 0.01% |
| **03_Select_Flight** | 7601 | 63.33 req/s | 307.03 ms | **528.00 ms** | 0.01% |
| **04_Confirm_Purchase** | 7481 | 62.33 req/s | 318.06 ms | **516.00 ms** | 0.00% |
| **Métrica Global Consolidada** | **30351** | **252.10 req/s** | **314.54 ms** | **511.00 ms** | **0.01%** |

---

## Considerações
O critério de aceitação foi considerado **SATISFATÓRIO** devido aos seguintes fatores comprovados pelo relatório:
* **Vazão Atingida:** O sistema manteve a estabilidade operando a uma taxa de **252.10 RPS**, superando com sucesso a meta de 250 RPS.
* **Tempo de Resposta em Conformidade:** O limite máximo para o Percentil 90 era de 2 segundos (2000 ms). No cenário executado, o 90th% global foi estável em **511 ms** (0,51 segundos), operando com folga de mais de 74% abaixo do limite de risco.
* **Resiliência a Erros:** A taxa total de erro limitou-se a uma margem desprezível de 0.01% (apenas 2 falhas isoladas em mais de 30 mil requisições consecutivas), atestando a robustez da infraestrutura simulada.
