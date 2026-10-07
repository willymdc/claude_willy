# Legendas ao vivo PT → ES

Você fala em português e a plateia lê, no telão, a tradução em espanhol latino-americano. O atraso
esperado é de 1 a 2 segundos. O valor real aparece no painel como "Atraso mediano".

Tudo roda no **Google Chrome do computador** (Windows, Mac ou Linux; versão 138 ou mais nova).
Não há servidor: a página reconhece a sua voz, manda o texto para o Claude e desenha as legendas.

## Antes da primeira vez

1. **Chave da API Anthropic.** Crie uma chave em <https://platform.claude.com> (Console → API keys).
   Use uma chave só para este uso e defina um limite de gasto mensal. Com o Claude Haiku 4.5, o custo
   estimado é de US$ 3 a 5 por hora de fala, e o painel mostra o custo real da sessão.
2. Abra a página, clique em **Configurações**, cole a chave e clique em **Testar**. O teste mostra
   uma frase traduzida e quanto tempo levou.
3. Ainda em Configurações, clique em **Preparar modo offline**. Isso baixa o tradutor embutido do
   Chrome, que entra em ação sozinho se a internet ou o Claude falharem.
4. Revise o **glossário**. Ele já vem com termos de missiologia (Newbigin, Goheen, Padilla, missio
   Dei, misión integral…). Acrescente os nomes e termos da sua palestra, um por linha, no formato
   `português = español` ou só o nome. Em **Contexto da palestra**, escreva o título e o roteiro.
   Isso ajuda a acertar nomes que o reconhecimento de voz erra.

A chave e as configurações ficam salvas só neste navegador.

## No dia

1. **Microfone.** O sistema usa o microfone padrão do computador, e o painel mostra qual é e o nível
   do sinal. O ideal é o seu microfone de lapela ou headset, ou uma saída da mesa de som ligada ao
   computador por uma interface USB. Evite o microfone embutido do notebook perto das caixas de som:
   o eco piora muito o reconhecimento.
2. **Internet.** O reconhecimento de voz do Chrome e o Claude usam a internet. Tenha o hotspot do
   celular como reserva. Sem internet, o tradutor offline do Chrome assume, com qualidade menor.
3. **Onde mostrar as legendas:**
   - **Tela só de legendas:** clique em **Abrir janela do projetor**, arraste a janela para a tela do
     projetor ou TV e dê **duplo clique** (ou tecle **F**) para entrar em tela cheia.
   - **Sobre os slides:** clique em **Faixa flutuante sobre os slides**. Abre uma faixa que fica
     sempre por cima de outras janelas, inclusive do PowerPoint e do Google Slides em tela cheia.
     Arraste a faixa para a parte de baixo do projetor e ajuste o tamanho. O Chrome lembra onde ela
     ficou.
   - **Com OBS:** escolha o fundo **Verde (chroma key)** em Configurações, capture a janela do projetor
     no OBS e aplique o filtro Chroma Key.
4. Clique em **Iniciar legendas** (ou tecle **Espaço**) e fale normalmente.

| Tecla | Ação |
|---|---|
| Espaço | iniciar / pausar |
| Esc | limpar a tela (por exemplo, numa troca de assunto) |
| P | abrir a janela do projetor |
| B | abrir a faixa flutuante |
| F ou duplo clique (na janela do projetor) | tela cheia |

Ao final, **Baixar transcrição** salva tudo o que foi dito, em português e em espanhol, num arquivo `.txt`.

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
- [ ] Ler as legendas do fundo da sala e ajustar **tamanho** e **linhas** em Configurações.
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
