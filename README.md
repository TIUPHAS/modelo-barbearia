# Modelo de site — Barbearia

Modelo de site de barbearia em **arquivo único**, sem build, sem framework e sem backend.
Abre com dois cliques no navegador e publica arrastando para o Netlify Drop.

O site inteiro se configura por dois blocos de JavaScript no começo do `<script>`:

- **`TEMAS`** — seis paletas prontas (cores + fontes). Trocar de identidade visual é mudar uma linha.
- **`CONFIG`** — nome, endereço, WhatsApp, serviços, preços, equipe, horários, FAQ.

Nenhum texto de negócio, preço ou cor existe fora desses dois blocos.

---

## O que o site faz

- Pedido de horário em 6 passos (serviço → profissional → dia → hora → nome → conferência)
  que termina abrindo o WhatsApp com a mensagem montada
- A agenda respeita os dias fechados, os dias de cada barbeiro e a duração de cada serviço —
  nenhum horário oferecido termina depois do fechamento
- Tabela de preços, equipe, galeria com filtro e lightbox, FAQ, mapa e horários
- SEO com JSON-LD (`HairSalon` + `FAQPage`), Open Graph e favicon gerados do `CONFIG`
- Ilustrações vetoriais desenhadas em código, nas cores do tema, no lugar das fotos que
  o cliente ainda não mandou

**O site não confirma horário sozinho.** Ele envia o pedido; a barbearia responde no
WhatsApp confirmando. Isso está escrito na interface de propósito — prometer confirmação
automática gera cliente na porta sem cadeira livre.

**Sem `localStorage`, `sessionStorage` ou cookie.** Nenhum dado de visitante é guardado.
É requisito de LGPD e é argumento de venda.

---

## Como usar num cliente novo

1. Duplique o `index.html` para `clientes/nome-do-cliente.html`
2. Abra e edite **só** o `CONFIG` (e a linha `tema:`, se for trocar a paleta)
3. `Ctrl+K Ctrl+0` dobra tudo no VS Code e ajuda a achar o bloco rápido
4. Abra com Live Server e confira no celular pelo IP da rede
5. Publique

### Trocar a paleta

```js
tema: 'cobre-mata',
```

| Tema | Cara |
|---|---|
| `ouro-noturno` | azul quase preto e dourado |
| `cobre-mata` | verde escuro e cobre |
| `navalha` | preto e vermelho, pesado |
| `classica` | marrom e bege, barbearia antiga |
| `gelo` | grafite e azul, mais moderno |
| `linho` | **tema claro**, bege e verde-oliva |

Para ajustar só uma cor sem criar tema novo:

```js
temaCustom: { acento: '#E8B53A' },
```

### Ligar e desligar seções

```js
secoes: { numeros: false, depoimentos: false, marcas: false, ... }
```

`numeros`, `depoimentos` e `marcas` vêm **desligados de propósito**. Só ligue com dado real
do cliente — número inventado e depoimento falso queimam a venda e são risco jurídico.

### Trocar as ilustrações por fotos

```js
fotoHero: 'fotos/salao.jpg',                                    // 1600x1000
fotosAmbiente: ['fotos/fachada.jpg', '', ''],                   // 800x700 cada
```

E o campo `foto` de cada barbeiro (600x740) e de cada item da galeria (700x700).
Preencheu, a ilustração some sozinha. Deixou vazio, a ilustração fica.

---

## Publicar

**Netlify Drop** (mais rápido, domínio grátis): arraste o `.html` para
[app.netlify.com/drop](https://app.netlify.com/drop). Renomeie para `index.html` antes.

**GitHub Pages**: em *Settings → Pages*, escolha a branch `main` e a pasta `/ (root)`.
O `index.html` da raiz vira a demonstração pública do modelo.

---

## Estrutura

```
.
├── index.html                 modelo (demonstração: barbearia fictícia Aço & Óleo)
├── clientes/                  um .html por cliente
├── fotos/                     fotos recebidas, já otimizadas
├── docs/
│   ├── prompt-mestre.md       prompt que gera o modelo do zero
│   └── checklist-cliente.md   o que pedir antes de começar
└── .vscode/settings.json      desliga o Prettier neste repo
```

O Prettier vem desligado porque reformatar o arquivo inteiro deixa o diff entre versões
de clientes ilegível.

---

## Aviso

Trabalho comercial. Repositório privado. Os dados da barbearia Aço & Óleo no `index.html`
são fictícios e servem só de demonstração.
