# Aniversário Deluxe

Página de convite de aniversário com contagem regressiva em tempo real,
barra de informações do evento (local, entrada, data/hora) e uma vitrine
de "presentes sugeridos" (Bitcoin, Esmeralda, Katana e Lamborghini), com
animações de entrada ao rolar a página.

> Página fictícia, criada para fins de prática e estudo de front-end.

## Tecnologias

- HTML5 e SCSS (Sass), organizados em módulos com `@use`
- [Parcel](https://parceljs.org/) como empacotador (bundler) — compila o
  SCSS, otimiza e resolve os caminhos das imagens automaticamente
- JavaScript puro (contador regressivo)
- [AOS – Animate On Scroll](https://michalsnik.github.io/aos/) (via CDN)
  para as animações de entrada das seções
- Google Fonts: Roboto

## Como rodar o projeto

Este projeto usa um bundler (Parcel), então **não é só abrir o
`index.html`** — é preciso instalar as dependências primeiro. Você
precisa ter o [Node.js](https://nodejs.org/) instalado.

```bash
# 1. instalar as dependências (uma vez só)
npm install

# 2. rodar em modo de desenvolvimento (com recarregamento automático)
npm run dev

# 3. ou gerar a versão final otimizada, na pasta dist/
npm run build
```

O comando `npm run dev` abre um servidor local (o próprio Parcel mostra
o endereço no terminal, normalmente `http://localhost:1234`).

## Arquitetura de pastas e arquivos

O projeto segue a separação padrão de um projeto Parcel: tudo que é
**código-fonte** fica dentro de `src/`; tudo que é **configuração da
ferramenta de build** fica solto na raiz; e a pasta `dist/` (build
final) e `node_modules/` (dependências) não fazem parte do pacote — elas
são geradas automaticamente pelos comandos acima e por isso ficam de
fora do controle de versão (veja `.gitignore`).

Descrição de cada nível, de fora para dentro:

**Raiz do projeto** — arquivos de configuração, nenhum é editado no
dia a dia:
- `package.json` — identifica o projeto, lista as dependências
  (Parcel e o transformador de Sass) e define os atalhos `npm run dev`
  e `npm run build`.
- `package-lock.json` — trava as versões exatas de cada dependência
  instalada, para que `npm install` sempre reproduza o mesmo resultado.
- `.gitignore` — diz ao Git para ignorar `node_modules/`, `dist/` e o
  cache do Parcel (`.parcel-cache/`), pois são gerados automaticamente.

**`src/`** — todo o código que você de fato edita:
- `index.html` — a única página do site; referencia o SCSS
  (`styles/main.scss`) e o JavaScript (`scripts/main.js`) diretamente
  com `<link>`/`<script>`, e é o próprio Parcel quem compila esses
  arquivos ao rodar `npm run dev` ou `npm run build`.
- `scripts/main.js` — inicializa a biblioteca AOS e roda o contador
  regressivo (calcula dias/horas/minutos/segundos até a data do evento
  e atualiza o elemento `#contador` a cada segundo).
- `images/` — todas as fotos usadas no site (fundo do cabeçalho, logo e
  as 4 fotos de presentes). O Parcel lê os caminhos usados no HTML e no
  SCSS e copia/otimiza cada imagem automaticamente para a pasta `dist/`
  no momento do build — por isso as imagens não precisam de um caminho
  absoluto nem de configuração manual.
- `styles/` — o SCSS, dividido por responsabilidade (ver abaixo).

**`src/styles/`** — em vez de um único CSS gigante, os estilos são
divididos em módulos ("partials", por isso o nome de cada arquivo
começa com `_`), e reunidos por um arquivo principal:
- `main.scss` — o arquivo "montador": importa todos os módulos com
  `@use` e define apenas o reset global (`*`) e o `.container`
  compartilhado por toda a página. É o único arquivo referenciado
  diretamente no `index.html`.
- `_variables.scss` — as cores e a paleta do site em um só lugar
  (`$textColor`, `$corAzulClaro`, `$corVerdeClaro`, `$corMarrom`,
  `$corPrataClaro`), para reuso pelos outros módulos.
- `_hero.scss` — estilo do cabeçalho (`.hero`): a imagem de fundo, o
  texto de destaque e o logo circular.
- `_gift.scss` — estilo das seções de presente (`.gift`): o layout
  lado a lado (foto + texto), os gradientes de fundo de cada categoria
  e o tamanho padronizado das fotos de presente.
- `components/_infos_bar.scss` — estilo da barra de informações
  (localização, entrada e data/hora), guardada em uma subpasta
  separada por já ser considerada um "componente" isolado dentro do
  cabeçalho, e não parte do `_hero.scss`.

Ou seja: a arquitetura segue o padrão "1 arquivo Sass por
componente/seção da página", todos reunidos pelo `main.scss` — o que
facilita achar rapidamente onde mexer (quer mudar só a barra de
informações? É só abrir `_infos_bar.scss`, sem precisar navegar por um
arquivo único com milhares de linhas).

## Padrão de tamanho das imagens

As fotos originais tinham proporções bem diferentes entre si (de 1,00 —
quadrada — até 1,82). A padronização foi feita via CSS, com uma
proporção fixa por contexto e `object-fit` cuidando do preenchimento sem
distorcer nada:

| Contexto              | Seletor          | Proporção | Preenchimento |
|------------------------|------------------|-----------|----------------|
| Logo do cabeçalho      | `.hero__logo`    | 1:1 (círculo) | `cover` (recorta para preencher o círculo) |
| Fotos de presente      | `.gift__image`   | 4:3       | `contain` (produto inteiro, fundo branco) |
| Fundo do cabeçalho     | `.hero` (background) | preenche a tela | `background-size: cover` |

Nas fotos de presente foi usado `contain` (não `cover`) de propósito:
como Bitcoin, Esmeralda, Katana e Lamborghini têm composições muito
diferentes entre si, `cover` arriscaria cortar parte da imagem (por
exemplo, a ponta da katana). Com `contain` + fundo branco + borda
arredondada, cada foto aparece inteira, dentro de uma caixa do mesmo
tamanho — efeito de vitrine.

## Observações importantes antes de publicar

- **Troque a data do evento.** O contador em `src/scripts/main.js`
  (`dataDoEvento`) e o texto exibido em `index.html`
  (`infos-bar__infos__item`, item "Quando") foram ajustados para uma
  data de exemplo no futuro. Troque os dois pela data real da sua
  festa antes de divulgar o link.
- **Imagem de reserva sem uso:** `src/images/esmeraldas_23-2150714266.jpg`
  está na pasta, mas o site usa `esmeraldas.png`. Pode apagar a que não
  usar ou trocar qual delas aparece na seção — veja o `erros.txt` para
  mais detalhes.

A lista completa de erros encontrados e corrigidos nesta revisão — 
incluindo um problema que fazia o projeto falhar ao instalar do zero —
está no `erros.txt`.
