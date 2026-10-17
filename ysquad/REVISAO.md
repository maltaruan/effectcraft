# Revisão do EffectCraft pro uso da ySquad (17/10/2026)

Versão 0.10.0 (upstream `storytold/effectcraft`, fase alfa, primeiro commit em 01/10/2026). Medido
no nosso uso real: cena de motion por script → `.mov` com transparência → montada no FilmCraft.

## Pronto e funcionando bem

| O quê | Prova |
|---|---|
| Compila no Mac | release em 9 min, saída no SSD |
| Script no formato do After (ExtendScript) | cena de 2 camadas montada em 0,1 s, com os nomes internos do After (`ADBE Glo2`, `ADBE Vector Shape - Rect`) |
| Quadros-chave, escala, posição, opacidade | mola de 0 a 115 a 100 %, barra deslizando, saída suave: conferido quadro a quadro |
| ProRes 4444 com transparência | `yuva444p12le`, 1080×1920, 90 quadros, 7 s pra 3 s de cena |
| Ponte com o FilmCraft | `montar_video.py --motion` montou a cena sobre o corte `corte_motion` em 71 s, com legenda por cima |
| Fonte da marca | Druk aplicada pelo nome interno (PostScript) |
| Acento em PT-BR | Ê Ç Ã corretos; quando a fonte não tem a letra, só ela troca de fonte |
| Servidor de ferramentas (MCP) | `effectcraft-headless` e `effectcraft` conectados |
| Testes | 481 passaram, 0 falharam (script, export, text, render, lottie) |
| Exportar Lottie (animação leve pra site) | sai, mas tira o brilho e avisa |

## Precisa ajustar

1. **Fonte errada passa calada.** Nome que não existe (ex.: `Druk-Super-Trial`, nome do arquivo) é
   aceito, o script devolve o nome como se valesse e o quadro sai na fonte padrão. O After marca
   como faltando. Fazer: aviso no script/CLI e um atalho que aceite nome do arquivo ou família.
2. **Quem posiciona a cena.** A receita do FilmCraft sobe 220 px todo clipe de `--motion` (pra não
   pisar na legenda). Cena desenhada já no lugar certo no EffectCraft sobe de novo. Fazer: combinar
   uma regra só (cena em quadro cheio, no lugar final, e a receita só encaixa).
3. **Placa de vídeo na primeira vez.** O 1º render com `--gpu` leva ~35 s preparando; os seguintes
   7 s. Em cena simples a placa não ganhou do processador. Usar o processador por padrão.
4. **Druk de teste sem acento.** Os arquivos `*-Trial` não têm Ê Ç Ã; a Druk Medium completa tem.
   Pra peça com acento: Druk completa ou outra fonte da marca.

## Precisa revisar (ainda não medido)

- Moldes reaproveitáveis (`.ectemplate` + `--values`): o caminho natural pro Nolan gerar a mesma
  cena com textos diferentes. Não testado.
- Efeitos mais pesados que usamos no motion (desfoque de movimento, 3D, partículas, máscaras de
  texto animadas): só o brilho foi testado.
- Velocidade em cena carregada (muitas camadas, 4K).
- Estabilidade do app com janela (`--control 9877`): só a linha de comando foi usada.
- Fidelidade com o After: o projeto só mediu um caso (suavização de curva); os efeitos não foram
  comparados quadro a quadro. Projeto `.aep` não abre (não importa pra nós: criamos do zero).
