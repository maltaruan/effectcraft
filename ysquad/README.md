# ysquad/

Ferramentas do nosso fork que ficam fora do produto (o compositor continua em Rust puro). Meta:
EffectCraft sólido pro uso da ySquad, como a mesa de motion do Nolan ao lado do FilmCraft (o
After Effects da dupla Premiere + After), não paridade total com o After Effects.

- Fork: `git@github.com:maltaruan/effectcraft.git` (origin), `storytold/effectcraft` (upstream).
- Compilação no SSD: `target` -> `/Volumes/SSD /build/effectcraft-target` (disco interno cheio).
  `cargo` vem do rustup do Homebrew: `export PATH=/opt/homebrew/opt/rustup/bin:$PATH`.
- MCP registrado no escopo do usuário: `effectcraft-headless` (sem janela, `--demo`) e
  `effectcraft` (ponte pro app aberto com `--control 9877`; o FilmCraft usa 9876).

## Ponte com o FilmCraft

Cada cena de motion sai daqui como `.mov` ProRes 4444 com alfa, no tamanho do vídeo final e já no
lugar, e entra no FilmCraft pela chave `motion` do `efeitos.json` (ver `~/motion-ysquad/FILMCRAFT.md`).
O FilmCraft só confere transparência e tamanho e põe a 100 %: não encaixa nem move.

- `receitas/legenda_viva.py PASTA_DO_CORTE SAIDA.mov [--estilo caixa|acende|pop|acumula|limpa]`: a
  legenda animada do corte (HyperFrames, ProRes 4444 com alfa), veio da receita do FilmCraft em
  17/10. Lê o `.fcpxml` e o `transcript.json` do corte; o FilmCraft recebe o `.mov` em
  `montar_video.py --legenda-viva`. `--amostras` gera um vídeo e um quadro de cada estilo.

## Estado (17/10/2026)

Fork criado, clonado e compilado (9 min). Cena de teste feita por script no formato do After
(texto com mola, barra que desliza, brilho, saída suave), exportada em ProRes 4444 com alfa e montada
no FilmCraft pela receita `--motion`: funcionou de ponta a ponta. 481 testes das partes que usamos
passaram. Análise completa em [REVISAO.md](REVISAO.md).
