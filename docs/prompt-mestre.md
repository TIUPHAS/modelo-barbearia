# Prompt mestre — Site de barbearia com pedido de horário e identidade visual trocável

Junção dos dois prompts anteriores (análise visual do abarbearia.net + fluxo de pedido de horário via WhatsApp), com um sistema de temas que troca a paleta e as fontes inteiras mudando uma linha.

**Como usar:** copie tudo dentro do bloco `PROMPT` abaixo, cole numa conversa nova, e antes de enviar edite as duas últimas seções (`TEMA ESCOLHIDO` e `DADOS DA BARBEARIA`). O que ficar entre `[COLCHETES]` a IA preenche com exemplo plausível.

---

## PROMPT

````
Você é um desenvolvedor front-end sênior especializado em sites de conversão para pequenos negócios brasileiros. Construa um site completo de barbearia, pronto para publicar.

## ENTREGA

Um único arquivo `.html` autocontido, com todo o CSS e o JavaScript embutidos. Sem build, sem framework, sem backend, sem dependência externa obrigatória. Precisa funcionar abrindo direto no navegador com dois cliques e também ao ser arrastado para o Netlify Drop.

Nada de React, jQuery, Bootstrap, Tailwind ou Font Awesome. Ícones em SVG inline desenhados dentro do próprio HTML. Animações em CSS puro e `IntersectionObserver`. Única exceção permitida a recurso externo: as fontes do Google Fonts, carregadas com `display=swap` e com fallback de sistema declarado, para o site não quebrar offline.

**Proibido `localStorage`, `sessionStorage` e qualquer API de armazenamento do navegador.** Nenhum dado de visitante é guardado em lugar nenhum. Isso é requisito de LGPD e argumento de venda.

Comente o CSS e o JS em português, marcando onde começa e termina cada seção, porque eu preciso conseguir editar depois.

---

## ARQUITETURA DE CONFIGURAÇÃO

Todo dado variável do negócio e toda decisão visual ficam em **dois blocos no topo do `<script>`**, nessa ordem: `TEMAS` e `CONFIG`. A regra é dura: **eu preciso conseguir adaptar o site inteiro para outra barbearia editando só esses dois blocos, sem tocar em mais nenhuma linha.** Se algum texto, cor, telefone, preço ou nome estiver escrito fora deles, está errado.

### Bloco 1 — `TEMAS`

Um objeto com as paletas prontas listadas na seção "PALETAS" abaixo. Cada tema tem exatamente as mesmas chaves, para serem intercambiáveis:

```js
const TEMAS = {
  'ouro-noturno': {
    nome: 'Ouro noturno',
    fundo:        '#02101F',  // fundo principal
    fundoAlt:     '#061E37',  // faixas e blocos alternados
    acento:       '#F6AD00',  // botões, ícones, preços, etiquetas
    acentoEscuro: '#C98D00',  // hover do acento
    claro:        '#F6F6F9',  // fundo das seções claras e texto sobre escuro
    texto:        '#F6F6F9',  // texto sobre fundo escuro
    textoSuave:   '#A9B4C0',  // texto secundário
    contraste:    '#02101F',  // texto sobre o acento
    zap:          '#25D366',
    fonteTitulo:  "'Red Rose', Georgia, serif",
    fonteTexto:   "'Montserrat', system-ui, sans-serif",
    googleFonts:  'Red+Rose:wght@400;600;700|Montserrat:wght@300;400;600;700',
    raio:         '4px',      // border-radius padrão do tema
    tomImagem:    'escuro'    // 'escuro' ou 'claro': define a força do overlay sobre fotos
  },
  // ... demais temas
};
```

No boot, o script lê `CONFIG.tema`, pega o objeto correspondente e **injeta todas as chaves como variáveis CSS no `:root`** via `document.documentElement.style.setProperty()`. Injete também o `<link>` do Google Fonts dinamicamente a partir de `googleFonts`. Todo o CSS do site usa exclusivamente essas variáveis (`var(--acento)`, `var(--fundo)`, etc.). **Nenhuma cor literal em hex pode aparecer no CSS fora do `:root` de fallback.**

Consequência exigida: trocar `tema: 'ouro-noturno'` por `tema: 'cobre-mata'` muda o site inteiro, incluindo fontes, e nada quebra o contraste nem o layout.

Se `CONFIG.temaCustom` existir e não for nulo, ele sobrescreve chave a chave o tema escolhido, para eu poder ajustar só uma cor sem criar um tema novo.

### Bloco 2 — `CONFIG`

Contém, no mínimo:

- `tema` (string, uma das chaves de `TEMAS`) e `temaCustom` (objeto ou `null`)
- `secoes`: objeto com um booleano por seção, para eu ligar e desligar blocos sem apagar código — `{ barraContato, social, sobre, numeros, marcas, servicos, equipe, galeria, pedido, depoimentos, faq, localizacao }`
- nome da barbearia, slogan/frase de posicionamento, texto "sobre", ano de fundação
- WhatsApp em formato internacional (`5519999999999`) e telefone fixo
- endereço completo, link do Google Maps, `src` do iframe do mapa e coordenadas (lat/lng, usadas no JSON-LD)
- redes sociais (Instagram, Facebook, cada uma opcional)
- `barbeiros`: nome, especialidade, foto, e os dias da semana que cada um atende
- `servicos`: agrupados por categoria (ex.: Cabelo, Barba, Periféricos, Tratamentos), cada item com nome, preço, duração em minutos e descrição curta
- `horarios`: por dia da semana, com abertura, fechamento e a marcação de fechado
- `intervaloGrade`: minutos entre um horário e outro na grade (padrão 30)
- `numeros`: array de contadores, **opcional e vazio por padrão** (ver regra de honestidade)
- `depoimentos`: array, **vazio por padrão**
- `marcas`: array de logos, vazio por padrão
- `faq`: array de pergunta/resposta
- `galeria`: array de imagens com legenda e categoria
- `desenvolvedor`: nome e link, para o crédito discreto no rodapé

Preencha o `CONFIG` com os dados que estão em "DADOS DA BARBEARIA", no fim deste prompt.

**Regra de honestidade, sem exceção:** `numeros`, `depoimentos` e `marcas` começam como arrays vazios, e a seção correspondente **não renderiza** quando o array está vazio (não deixa buraco no layout, não mostra placeholder de depoimento). Não invente quantidade de clientes, anos de mercado, nota do Google nem avaliação nenhuma. Deixe no comentário do código o formato de cada item, para eu preencher quando tiver o dado real do cliente.

---

## PALETAS (criar todas as seis)

Todas foram pensadas para fundo escuro com um acento único, exceto onde indicado. Mantenha contraste mínimo de 4.5:1 em todas.

**1. `ouro-noturno`** — azul-marinho e dourado. É o padrão.
fundo `#02101F` · fundoAlt `#061E37` · acento `#F6AD00` · acentoEscuro `#C98D00` · claro `#F6F6F9` · textoSuave `#A9B4C0` · contraste `#02101F`
Fontes: Red Rose (títulos) + Montserrat (texto).

**2. `cobre-mata`** — verde profundo e cobre. Sofisticado, foge do lugar-comum.
fundo `#0E1A15` · fundoAlt `#152A21` · acento `#C87941` · acentoEscuro `#A05F31` · claro `#F2F0EA` · textoSuave `#9DAB9F` · contraste `#0E1A15`
Fontes: Playfair Display + Inter.

**3. `navalha`** — preto, grafite e vermelho-sangue. Agressivo, urbano.
fundo `#0A0A0A` · fundoAlt `#171717` · acento `#D62828` · acentoEscuro `#A81E1E` · claro `#F5F5F5` · textoSuave `#9E9E9E` · contraste `#FFFFFF`
Fontes: Oswald + Barlow.

**4. `classica`** — marrom couro e creme. Barbearia tradicional, clientela mais velha.
fundo `#1C1410` · fundoAlt `#2A201A` · acento `#D4A574` · acentoEscuro `#B08553` · claro `#F5EDE3` · textoSuave `#B3A392` · contraste `#1C1410`
Fontes: Bebas Neue (títulos) + Lora (texto).

**5. `gelo`** — grafite azulado e azul elétrico. Moderna, público jovem.
fundo `#0D1117` · fundoAlt `#161B22` · acento `#3B9EFF` · acentoEscuro `#2A7ACC` · claro `#E6EDF3` · textoSuave `#8B949E` · contraste `#0D1117`
Fontes: Space Grotesk + Inter.

**6. `linho`** — a única clara: bege, off-white e verde-oliva. Para barbearia com pegada mais leve.
fundo `#F4F1EA` · fundoAlt `#E8E3D8` · acento `#5A6B4A` · acentoEscuro `#425136` · claro `#2B2B2B` (aqui o "claro" vira o tom escuro do texto) · textoSuave `#6B6B6B` · contraste `#F4F1EA` · tomImagem `'claro'`
Fontes: Cormorant Garamond + Karla.

Importante: o CSS não pode assumir que o fundo é escuro. Use as variáveis de forma que o tema `linho` funcione sem ajuste manual — overlay de imagem, cor de texto sobre foto e sombras saem todos de variáveis definidas por tema.

---

## SISTEMA DE DESIGN

Mobile-first de verdade: projete para 375px primeiro e expanda. Alvos de toque de 44px no mínimo. Breakpoints em 480px, 768px e 1024px. Conteúdo com largura máxima de 1200px, centralizado, 20px de padding lateral. Fontes com `clamp()`.

Estética de barbearia moderna, não template genérico: um acento de cor só, tipografia com peso e presença nos títulos, muito respiro entre as seções. Nada de degradê arco-íris, nada de sombra em tudo, nada de emoji na interface. A régua é "parece caro", não "tem muita animação".

**Padrão de cabeçalho de seção, repetido em todas:**
1. Etiqueta pequena em caixa alta, cor do acento, fonte de texto, ~11px, `letter-spacing` de 3px, precedida de um pequeno losango do acento.
2. Abaixo, o título grande na fonte de título, ~40px no desktop.
3. Ao lado do título, uma linha horizontal fina de 1px preenchendo o espaço restante.

Transições sutis: fade com translação leve de 30px na entrada das seções, hover discreto nos cards. Tudo respeitando `prefers-reduced-motion` — se o usuário pediu menos animação no sistema, desligue todas.

---

## SEÇÕES, NA ORDEM

Cada uma respeita seu booleano em `CONFIG.secoes`.

**1. Header** — fixo no topo, fundo transparente sobre o hero; ao rolar mais de 100px ganha fundo `--fundo` com sombra leve, em transição suave. Logo em texto à esquerda, âncoras à direita (Serviços · Equipe · Valores · Contato) com rolagem suave. Abaixo de 768px vira hambúrguer que abre em tela cheia sobre fundo `--fundo`, com botão X e trava do scroll do body enquanto aberto.

**2. Hero** — 100vh. Foto de fundo em `cover` com overlay definido pelo tema. Em três segundos o visitante entende o que é, onde fica e como marcar: nome da barbearia em duas linhas (primeira no acento, maior; segunda no texto claro), frase de posicionamento, linha com bairro e cidade. Dois botões: **"Pedir horário"** (primário, preenchido no acento, rola até a seção de pedido) e **"Chamar no WhatsApp"** (secundário, vazado, abre `wa.me` com mensagem pronta). Um único `<h1>` na página, aqui.

**3. Barra de contato** — faixa em `--fundoAlt`, três colunas com ícone do acento: WhatsApp, telefone fixo, endereço. Cada uma clicável (`wa.me`, `tel:`, link do Maps). Vira coluna única no celular.

**4. Bloco social** — retângulo no acento alinhado à esquerda ocupando ~35% da largura, com ícones de Instagram e Facebook na cor de contraste. O resto da faixa segue no fundo. Some se não houver rede social no `CONFIG`.

**5. Sobre** — cabeçalho padrão, parágrafo de 3 a 4 linhas com a história, e abaixo uma faixa com 3 fotos lado a lado em Grid sem espaço entre elas, em preto e branco que volta a colorir no hover.

**6. Números** — só renderiza se `CONFIG.numeros` tiver itens. Foto à esquerda, contadores no acento à direita, cada um contando de 0 até o valor em ~2 segundos, disparados uma única vez por `IntersectionObserver`.

**7. Marcas que usamos** — só renderiza se houver logos. Carrossel horizontal em loop infinito feito só com `@keyframes`, sem biblioteca. Logos em cinza que ficam brancos no hover, animação pausada no hover.

**8. Valores (serviços)** — a seção mais importante depois do pedido. Separador em onda no topo e no fim, em SVG inline, fazendo a transição do fundo para `--claro` e de volta. Layout de cardápio impresso: cada categoria vira um título centralizado ladeado por faixas hachuradas (`repeating-linear-gradient`), e cada serviço é uma linha com nome à esquerda, preço à direita e **linha pontilhada preenchendo o meio** (flexbox com um elemento central de `border-bottom: 1px dashed`). Duração em texto pequeno abaixo do nome. Cada linha é clicável e leva ao fluxo de pedido com aquele serviço já selecionado.

**9. Equipe** — um card por barbeiro: foto, nome, especialidade, dias que atende, e botão que abre o fluxo de pedido com aquele barbeiro pré-selecionado.

**10. Galeria de cortes** — grade de imagens com filtro por categoria em JavaScript puro, `loading="lazy"`, e lightbox simples ao clicar (fecha no X, no Esc e no clique fora; devolve o foco ao elemento de origem).

**11. Pedido de horário** — detalhada na seção seguinte deste prompt.

**12. Depoimentos** — só renderiza se `CONFIG.depoimentos` tiver itens.

**13. FAQ** — acordeão acessível (`button` + `aria-expanded`), com marcação JSON-LD `FAQPage`. Perguntas padrão: precisa marcar antes, aceita cartão, tem estacionamento, atende criança, quanto tempo demora.

**14. Localização e horários** — cabeçalho padrão com o bairro no título. Duas colunas: à esquerda endereço e telefones com ícones do acento; à direita um card de cantos arredondados com os horários, um dia por linha (dia em negrito à esquerda, horário à direita, linha fina separando, dia fechado em texto suave). Abaixo, mapa em iframe, largura total, ~400px de altura, cantos arredondados, `loading="lazy"`.

**15. Rodapé** — curva no topo em SVG, logo em texto centralizado, redes sociais, CNPJ (placeholder), copyright com o ano vindo do JavaScript, e crédito discreto do desenvolvedor com link.

**16. Botão flutuante de WhatsApp** — círculo de 60px na cor `--zap`, fixo no canto inferior direito, ícone branco, sombra suave, pulsar sutil, `aria-label` descritivo. Visível em todas as seções, e sempre acima de qualquer outro elemento.

---

## SISTEMA DE PEDIDO DE HORÁRIO — REQUISITOS EXATOS

Este é o núcleo do projeto. Siga ao pé da letra.

**Fluxo em etapas, uma por tela, com indicador de progresso:**

1. **Serviço** — lista vinda do `CONFIG`, agrupada por categoria, mostrando preço e duração.
2. **Barbeiro** — cards com foto, mais a opção "tanto faz", que aceita qualquer um disponível.
3. **Dia** — os próximos 14 dias, pulando automaticamente os dias em que a barbearia está fechada e os dias em que aquele barbeiro não atende. Mostre dia da semana e data.
4. **Horário** — grade gerada a partir do horário de funcionamento daquele dia, no intervalo do `CONFIG`, respeitando a duração do serviço para nunca oferecer um horário que termine depois do fechamento. No dia de hoje, não mostre horários que já passaram.
5. **Nome** — apenas o nome. Nada de telefone, e-mail ou CPF: o telefone já vem junto no WhatsApp e cada campo a mais derruba a conversão.
6. **Revisão** — resumo completo, botão para voltar e editar qualquer etapa, e o botão final.

Navegação por teclado funcionando no fluxo inteiro, com foco visível no acento. Botão "voltar" em todas as etapas a partir da segunda.

**Terminologia obrigatória.** Em nenhum lugar do site escreva "agendamento confirmado", "horário garantido", "reserva" ou equivalente. Use sempre "pedir horário" ou "solicitar horário". A tela de sucesso diz que o pedido foi enviado e que a barbearia confirma pelo WhatsApp. O motivo: não existe banco de dados, o horário não fica bloqueado, e o cliente não pode chegar no sábado achando que tinha vaga garantida.

**Ao concluir**, monte a mensagem em texto e abra `https://wa.me/<numero>?text=<mensagem>` com `encodeURIComponent`. Formato:

```
Olá! Vim pelo site e queria pedir um horário.

Serviço: Corte + Barba (R$ 85 — 60 min)
Barbeiro: Rafael
Dia: sábado, 12/09
Horário: 15h00
Nome: João Pedro

Fica de pé?
```

Inclua um botão secundário **"Copiar mensagem"**, que copia o mesmo texto para a área de transferência como alternativa caso o WhatsApp não abra (acontece em alguns navegadores de desktop), com feedback visual ao copiar.

O estado do fluxo vive só em memória, em variáveis JavaScript. Recarregou a página, começa de novo, e tudo bem.

---

## QUALIDADE, SEO E ACESSIBILIDADE

- HTML semântico: `header`, `nav`, `main`, `section`, `footer`. Um único `h1`, hierarquia correta de `h2` e `h3`.
- `alt` descritivo em todas as imagens, `aria-label` em todo link ou botão que só tem ícone.
- Contraste mínimo de 4.5:1 em todos os seis temas.
- Imagens com `loading="lazy"` (menos a do hero) e `width`/`height` definidos, para não haver salto de layout.
- `title` e `meta description` específicos, com bairro e cidade, montados a partir do `CONFIG`.
- Open Graph completo, pensado para o link ser colado no WhatsApp.
- JSON-LD `HairSalon` com nome, endereço, telefone, `openingHoursSpecification`, geo e `priceRange`, gerado a partir do `CONFIG` (não escrito à mão no HTML).
- Favicon em SVG inline ou data URI.
- CSS crítico primeiro; alvo de abrir em menos de 2 segundos no 4G.
- Placeholders de imagem que funcionem offline (SVG inline ou `data:` URI com cor sólida e o nome da foto), com comentário indicando o tamanho ideal em pixels de cada uma.

---

## O QUE NÃO FAZER

- Não prometa lembrete automático, confirmação por SMS, integração com sistema de gestão ou bloqueio de horário. Nada disso existe nesta versão.
- Não invente depoimentos, avaliações, nota do Google ou número de clientes atendidos.
- Não use lorem ipsum: escreva textos reais e plausíveis em português do Brasil.
- Não crie área de login, painel administrativo ou cadastro.
- Não use `<form>` com `action` apontando para servidor nenhum.
- Não coloque nada que dependa de conexão para a página funcionar.
- Não use mais de duas famílias de fonte por tema.
- Não coloque carrossel na dobra principal.
- Não escreva cor, texto de negócio, preço ou telefone fora de `TEMAS` e `CONFIG`.

---

## AO FINAL DA RESPOSTA

Escreva um bloco curto, em português simples, explicando:

1. Quais linhas do `CONFIG` eu edito para adaptar o site a outra barbearia
2. Como trocar de tema, e como usar `temaCustom` para ajustar uma cor só
3. Como trocar as fotos placeholder pelas reais
4. Como publicar no Netlify Drop
5. As limitações honestas do sistema, na linguagem exata que eu devo usar ao explicar para o dono da barbearia

---

## TEMA ESCOLHIDO

`tema: 'ouro-noturno'`

---

## DADOS DA BARBEARIA

- **Nome:** [Navalha & Cia]
- **Cidade/bairro:** [Campinas, SP — Cambuí]
- **Fundação:** [2016]
- **Frase de posicionamento:** [A melhor barbearia do Cambuí]
- **Endereço:** [Rua Exemplo, 123, Cambuí, Campinas/SP, 13025-000]
- **WhatsApp:** [5519999999999]
- **Telefone fixo:** [(19) 3255-0000]
- **Instagram:** [@navalhaecia] · **Facebook:** [/navalhaecia]
- **Funcionamento:** [terça a sexta 9h–20h · sábado 8h–18h · domingo e segunda fechado]
- **Barbeiros:** [Rafael — degradê e navalha, terça a sábado · Bruno — barba e barboterapia, quarta a sábado · Diego — corte clássico e infantil, terça a sexta]
- **Serviços:**
  - Cabelo: [Corte masculino R$ 55 / 40 min · Corte infantil R$ 45 / 30 min · Acabamento R$ 25 / 15 min]
  - Barba: [Barba completa R$ 40 / 30 min · Barboterapia R$ 60 / 45 min]
  - Combos: [Corte + Barba R$ 85 / 60 min]
- **Desenvolvedor (crédito no rodapé):** [Seu nome] — [seu link]
````

---

## Como trocar a identidade visual depois

**Trocar o tema inteiro:** no `CONFIG`, mude `tema: 'ouro-noturno'` para qualquer uma das seis chaves. Paleta, fontes, overlay das fotos e arredondamento mudam juntos.

**Ajustar só uma cor:** preencha `temaCustom`. Ele sobrescreve chave a chave.

```js
tema: 'navalha',
temaCustom: { acento: '#E8B53A', acentoEscuro: '#B88A22' }
```

**Criar um tema novo:** copie um bloco de `TEMAS` inteiro, troque a chave, os hexes e o par de fontes. Confira o contraste do texto sobre o acento antes de mostrar ao cliente.

**Ligar e desligar seções:** `secoes: { numeros: false, marcas: false, faq: true, ... }`. Barbearia sem logo de marca e sem número real fica com o site mais limpo, não mais vazio.

## Como escolher a paleta na hora da venda

| Perfil da barbearia | Tema |
|---|---|
| Padrão, funciona em quase tudo | `ouro-noturno` |
| Quer se diferenciar do concorrente da esquina | `cobre-mata` |
| Urbana, tatuagem, público jovem | `navalha` |
| Tradicional, clientela mais velha, cadeira antiga | `classica` |
| Moderna, fade, público de 18 a 30 | `gelo` |
| Pegada leve, atende homem e mulher | `linho` |

Na prévia, monte duas versões da mesma barbearia com temas diferentes e mande as duas. O dono escolher entre A e B converte melhor do que ele julgar uma opção sozinha.

## Checklist antes de enviar o prompt

- [ ] Troquei todos os `[COLCHETES]` pelos dados reais
- [ ] WhatsApp em formato internacional, só dígitos (55 + DDD + número)
- [ ] Preços e durações confirmados com o dono
- [ ] Peguei o `src` do embed do Google Maps do endereço certo
- [ ] Escolhi o tema e conferi se combina com o que já existe no Instagram deles
- [ ] `numeros`, `depoimentos` e `marcas` ficaram vazios, a menos que eu tenha o dado real
- [ ] Coloquei meu nome e link no crédito do rodapé
