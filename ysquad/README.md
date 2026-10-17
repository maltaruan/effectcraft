# ysquad/

Ferramentas do nosso fork que ficam fora do produto (o compositor continua em Rust puro). Meta:
EffectCraft sólido pro uso da ySquad no que depende da filmagem (rastreio, recorte da pessoa, efeito
sobre a imagem), ao lado do FilmCraft. Criar motion do zero é do HyperFrames/Remotion por enquanto;
aqui fica para uma sprint futura (curso de After Effects a destilar).

- Fork: `git@github.com:maltaruan/effectcraft.git` (origin), `storytold/effectcraft` (upstream).
- Compilação no SSD: `target` -> `/Volumes/SSD /build/effectcraft-target` (disco interno cheio).
  `cargo` vem do rustup do Homebrew: `export PATH=/opt/homebrew/opt/rustup/bin:$PATH`.
- MCP registrado no escopo do usuário: `effectcraft-headless` (sem janela, `--demo`) e
  `effectcraft` (ponte pro app aberto com `--control 9877`; o FilmCraft usa 9876).

## Ponte com o FilmCraft

Cada cena de motion sai daqui como `.mov` ProRes 4444 com alfa, no tamanho do vídeo final e já no
lugar, e entra no FilmCraft pela chave `motion` do `efeitos.json` (ver `~/motion-ysquad/FILMCRAFT.md`).
O FilmCraft só confere transparência e tamanho e põe a 100 %: não encaixa nem move.

A legenda viva (HyperFrames) mora em `~/motion-ysquad/receitas/legenda_viva.py` desde 17/10.

## Estado (17/10/2026)

Fork criado, clonado e compilado (9 min). Cena de teste feita por script no formato do After
(texto com mola, barra que desliza, brilho, saída suave), exportada em ProRes 4444 com alfa e montada
no FilmCraft pela receita `--motion`: funcionou de ponta a ponta. 481 testes das partes que usamos
passaram. Análise completa em [REVISAO.md](REVISAO.md). Rastreio, efeito sobre a filmagem e recorte da pessoa
(MobileSAM, com as regras do REVISAO.md) testados no vídeo do Ruan em 17/10.
