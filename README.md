# 🛗 Simulador de Elevador de 6 Andares (Arduino + Tinkercad)

Este projeto consiste na simulação de um sistema de controle para um elevador de 6 andares utilizando a plataforma Arduino. O circuito e o código foram desenvolvidos e testados utilizando o ambiente virtual do **Tinkercad**.

O projeto foi desenvolvido em **dupla**, com o objetivo de aplicar conceitos de eletrônica digital, mapeamento de entradas/saídas e otimização de lógica de programação para sistemas embarcados.

---

## 🛠️ Componentes Utilizados

* 1x Arduino Uno R3
* 2x Protoboards (Placas de ensaio)
* 6x LEDs (Representando a posição física do elevador em cada andar)
* 6x Botões Pulsadores (Representando os botões de chamada de cada andar)
* 12x Resistores (Para limitação de corrente dos LEDs e configuração de pull-down/pull-up dos botões)
* Fios de conexão Jumper

---

## 🧠 Explicação da Lógica Utilizada para Expansão dos Andares

Controlar um sistema conforme ele cresce em complexidade (passando de 2 ou 3 andares para 6 ou mais) exige uma mudança de abordagem no código para evitar redundâncias e caminhos difíceis de manter. A lógica do nosso projeto evoluiu em três etapas principais para permitir essa expansão de forma limpa e escalável:

### 1. Mapeamento Sequencial de Pinos

Em vez de espalhar pinos aleatórios, organizamos as portas digitais do Arduino de forma estritamente sequencial. Os LEDs ocupam as portas **13 a 8** (do 1º ao 6º andar) e os botões ocupam as portas **7 a 2** (do 1º ao 6º andar). Essa ordem física facilita a tradução direta de "número do andar" para "índice do componente".

### 2. Transição para Vetores (Arrays) — Chave da Escalabilidade

Para viabilizar a expansão sem criar linhas de código repetitivas para cada novo andar adicionado, o código foi refatorado para agrupar os pinos em vetores:

```cpp
int leds[] = {led1, led2, led3, led4, led5, led6};
int botoes[] = {bot1, bot2, bot3, bot4, bot5, bot6};

```

**Por que isso importa para a expansão?**
Com essa estrutura, a inicialização no `setup()` e a varredura de botões no `loop()` deixam de usar blocos massivos de `if/else`. Conseguimos configurar ou ler todos os componentes usando estruturas de repetição simples (`for`), onde o índice do laço corresponde diretamente ao andar em movimento. Se quiséssemos expandir o elevador para 10 andares, bastaria adicionar os novos pinos ao vetor e alterar o limite do laço `for`.

### 3. Lógica Numérica Dinâmica de Movimentação

A movimentação entre o andar `atual` e o andar solicitado (`andar`) é calculada dinamicamente por comparativos matemáticos diretos, eliminando rotinas fixas de caminhos:

* **Subida (`atual < andar`):** Um laço incremental (`for`) acende e apaga sequencialmente os LEDs a partir do andar atual até o destino.
* **Descida (`atual > andar`):** Um laço decremental realiza o mesmo processo no sentido inverso.

A função auxiliar `led(int numero)` atua como uma camada de abstração que converte dinamicamente o número do andar requisitado no pino correto do Arduino, garantindo que o comportamento lógico permaneça idêntico, independentemente de quantos andares existam no sistema.

---

## 💻 Estrutura do Código

A estrutura base do controle segue o fluxo abaixo:

1. **Varredura:** O `loop()` monitora constantemente o estado dos botões.
2. **Registro:** Ao pressionar um botão, a variável `andar` armazena o destino.
3. **Movimento:** O elevador desliga a posição anterior, percorre os andares intermediários com um atraso de tempo (`delay(3000)`) simulando o deslocamento físico, e atualiza a variável `atual`.
4. **Estabilização:** O LED do andar de destino permanece aceso até uma nova chamada.

Autores: Danielli Maritns e Ana Julia Valentim 🤗🙄
