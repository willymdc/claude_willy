# Legendas ao vivo PT → ES

Você fala em português e a plateia lê, no telão, a tradução em espanhol latino-americano. O atraso
esperado é de 1 a 2 segundos. O valor real aparece no painel como "Atraso mediano".

Tudo roda no **Google Chrome do computador** (Windows, Mac ou Linux; versão 138 ou mais nova).
Não há servidor: a página reconhece a sua voz, manda o texto para o Claude e desenha as legendas.

## Antes da primeira vez

1. Abra a página no Chrome, clique em **Configurações** → **Preparar modo offline** e espere aparecer
   "Pronto". Isso baixa o tradutor português → espanhol embutido no Chrome, que é o motor padrão:
   **grátis, sem chave e funciona offline**. Faça isso com antecedência: enquanto o pacote não estiver
   baixado, a tela mostra o português.
2. (Opcional) Para mais qualidade, dá para trocar o motor para o **Claude** em Configurações (precisa de
   uma chave da API da Anthropic, criada em <https://platform.claude.com>, com custo de alguns dólares
   por hora de fala). Com o Claude, o glossário e o contexto da palestra são usados; com o tradutor do
   Chrome, eles são ignorados.

A chave e as configurações ficam salvas só neste navegador.

## No dia

O painel mostra **4 passos de preparação** no topo. Cada um fica verde quando está pronto:

1. **Microfone.** O sistema usa o microfone padrão do computador e mostra o nome dele e o nível do
   sinal. O ideal é um microfone de lapela ou headset, ou uma saída da mesa de som ligada por uma
   interface USB. Evite o microfone embutido do notebook perto das caixas: o eco piora o reconhecimento.
   Se não chegar som por 20 s, o painel avisa.
2. **Tradutor.** O passo fica verde quando o tradutor do Chrome está baixado. Se aparecer
   **Baixar**, clique uma vez, com internet.
3. **Telão de legendas.** Clique em **Abrir**, arraste a janela para o telão ou TV e dê
   **duplo clique** (ou tecle **F**) para entrar em tela cheia. Antes de começar, o telão mostra uma
   tela de espera com o título da palestra.
4. **Faixa nos slides.** Clique em **Abrir**. A faixa fica sempre por cima, inclusive do PowerPoint
   e do Google Slides em tela cheia. Arraste-a para a base do projetor; o Chrome lembra a posição.

Use **Texto de exemplo** para ajustar o tamanho da letra e o número de linhas sem precisar falar.
Depois, clique em **Iniciar legendas** (ou tecle **Espaço**).

**Como a legenda aparece para a plateia:**
- Cada pausa na fala começa uma **linha nova**.
- A frase atual fica em destaque, e as anteriores vão ficando mais apagadas.
- O texto sobe suavemente, sem pular.
- Uma frase de cima que não cabe inteira fica bem apagada, para ninguém ler um pedaço solto.
- Depois de 15 s de silêncio, a legenda esmaece. Ela volta assim que você fala.

| Tecla | Ação |
|---|---|
| Espaço | iniciar / pausar |
| Esc | limpar a tela (por exemplo, numa troca de assunto) |
| P | abrir o telão |
| B | abrir a faixa sobre os slides |
| F ou duplo clique (no telão) | tela cheia |

Em **Configurações → Legenda na tela** você ajusta:
- título mostrado no telão;
- fundo: carvão, grafite, preto ou verde para OBS;
- cor do texto: branco ou dourado;
- tamanho da letra e número de linhas;
- posição: no centro, que é visível por cima das cabeças, ou embaixo.

Ao final, **Transcrição** salva tudo o que foi dito, em português e em espanhol, num arquivo `.txt`.

## Ensaio obrigatório (15 minutos, no local se possível)

- [ ] Configurações → **Testar** a chave e **Preparar modo offline**.
- [ ] Conferir o microfone no medidor do painel: falando, a barra verde deve se mexer.
- [ ] Abrir a faixa flutuante sobre os slides em **tela cheia** (PowerPoint, Keynote ou Google
  Slides). Falar por **5 minutos ou mais**, trocando slides com o passador, e confirmar que as
  legendas continuam. Isso prova que o Chrome segue ouvindo mesmo sem estar em primeiro plano.
  Se as legendas pararem, deixe a janela do painel visível em parte da tela do notebook.
- [ ] No Keynote, o modo apresentação às vezes esconde janelas flutuantes. Nesse caso, use a tela
  dedicada ou o OBS.
- [ ] Ver no painel o **Atraso mediano** e o **Custo da sessão**.
- [ ] Com **Texto de exemplo** ligado, ler as legendas do fundo da sala e ajustar **tamanho** e **linhas** em Configurações.
- [ ] Desligar a internet por um momento e ver o tradutor offline assumir (aparece "Chrome" no painel).

Quer testar o visual sem falar? Abra a página com `?demo=1` no fim do endereço. O português passa a
ser simulado, mas a tradução é a de verdade. Com `?demo=1&motor=falso`, a tradução também é simulada.

## Como funciona (resumo técnico)

- **Reconhecimento:** Web Speech API do Chrome (`pt-BR`), com resultados provisórios a cada fração de
  segundo e resultados finais nas pausas. Se o reconhecimento parar, ele reinicia sozinho, e um vigia
  reinicia o reconhecimento se o microfone tem som e nenhum texto chega há 8 s.
- **Tradução:** o Claude (SDK oficial `@anthropic-ai/sdk`, chamado direto do navegador com a sua chave)
  traduz o trecho que está sendo falado. Ele recebe o contexto dos trechos anteriores, o glossário e a
  tradução provisória já exibida, para a legenda não "pular". Só uma requisição por trecho fica em
  andamento, sempre com o texto mais recente. Quando a frase termina, a versão final substitui a
  provisória.
- **Reserva:** quando o Claude falha, demora mais de 4 a 5 s ou recusa, aquele trecho é traduzido pelo
  Translator API do Chrome. Depois de 3 falhas seguidas, só o Chrome é usado por 30 s.
- **Privacidade:** o áudio vai para o serviço de reconhecimento de voz do Google (via Chrome) e o texto
  vai para a API da Anthropic.
