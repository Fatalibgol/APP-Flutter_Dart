import 'package:flutter/material.dart';

void main() => runApp(const PerguntaApp());

// Widget principal do aplicativo, que gerencia o estado das perguntas.
class PerguntaApp extends StatefulWidget {
  const PerguntaApp({super.key});

  @override
  _PerguntaAppState createState() => _PerguntaAppState();
}

// Classe de estado para PerguntaApp, contendo a lógica e o estado mutável.
class _PerguntaAppState extends State<PerguntaApp> {
  var _perguntaSelecionada = 0;
  var _pontuacaoTotal = 0; // <-- NOVA VARIÁVEL PARA A PONTUAÇÃO TOTAL

  // -- ALTERAÇÃO 1: NOVA ESTRUTURA DE DADOS PARA PERGUNTAS E RESPOSTAS --
  // Substituímos a lista de Strings '_perguntas' por uma lista de Mapas.
  // Cada Mapa contém a 'textoPergunta' e uma lista de 'respostas'.
  // CADA RESPOSTA AGORA É UM MAPA COM 'texto' E 'pontuacao'.
  final List<Map<String, Object>> _perguntasERespostas = const [
    {
      'textoPergunta': 'Qual é a sua cor favorita?',
      'respostas': [
        {'texto': 'Azul', 'pontuacao': 10},
        {'texto': 'Vermelho', 'pontuacao': 1}, // Exemplo de pontuação diferente
        {'texto': 'Verde', 'pontuacao': 5},
        {'texto': 'Amarelo', 'pontuacao': 3},
      ],
    },
    {
      'textoPergunta': 'Qual é o seu animal favorito?',
      'respostas': [
        {'texto': 'Cachorro', 'pontuacao': 1},
        {'texto': 'Gato', 'pontuacao': 9},
        {'texto': 'Pássaro', 'pontuacao': 7},
        {'texto': 'Peixe', 'pontuacao': 10},
      ],
    },
    {
      'textoPergunta': 'Qual é o seu instrutor favorito?',
      'respostas': [
        {'texto': 'Max', 'pontuacao': 9},
        {'texto': 'Ana', 'pontuacao': 3},
        {'texto': 'Bia', 'pontuacao': 10},
        {'texto': 'Carlos', 'pontuacao': 6},
      ],
    },
    {
      'textoPergunta': 'Qual é o seu hobbie favorito?',
      'respostas': [
        {'texto': 'Ler', 'pontuacao': 1},
        {'texto': 'Jogar', 'pontuacao': 9},
        {'texto': 'Caminhar', 'pontuacao': 2},
        {'texto': 'Cozinhar', 'pontuacao': 10},
      ],
    },
  ];

  // -- ALTERAÇÃO 2: MÉTODO _responder ATUALIZADO --
  // Agora o método recebe a pontuação da resposta selecionada e a soma.
  void _responder(int pontuacao) {
    setState(() {
      _pontuacaoTotal += pontuacao; // <-- SOMA A PONTUAÇÃO
      _perguntaSelecionada++;
    });
    print('Pontuação atual: $_pontuacaoTotal'); // Para depuração
    if (_perguntaSelecionada >= _perguntasERespostas.length) {
      print('Acabaram as perguntas!'); // Mensagem no console para depuração
    }
  }

  // Método para reiniciar o quiz
  void _reiniciarQuiz() {
    setState(() {
      _perguntaSelecionada = 0;
      _pontuacaoTotal = 0;
    });
  }

  @override
  Widget build(BuildContext context) {
    // -- ALTERAÇÃO 3: LÓGICA CONDICIONAL PARA EXIBIR PERGUNTA/FIM --
    bool temPerguntas = _perguntaSelecionada < _perguntasERespostas.length;

    // Se houver perguntas, extrai a lista de respostas para a pergunta atual.
    List<Map<String, Object>> respostasDaPerguntaAtual = [];
    if (temPerguntas) {
      respostasDaPerguntaAtual =
          _perguntasERespostas[_perguntaSelecionada]['respostas']
              as List<Map<String, Object>>;
    }

    // -- LÓGICA PARA DEFINIR A MENSAGEM FINAL --
    String mensagemFinal;
    Color corMensagemFinal = Colors.blueAccent; // Cor padrão

    if (!temPerguntas) {
      if (_pontuacaoTotal < 15) {
        mensagemFinal = 'Parabéns! Sua pontuação foi $_pontuacaoTotal.';
        corMensagemFinal = Colors.orange;
      } else if (_pontuacaoTotal < 25) {
        mensagemFinal = 'Você é bom! Sua pontuação foi $_pontuacaoTotal.';
        corMensagemFinal = Colors.green;
      } else if (_pontuacaoTotal < 32) {
        mensagemFinal = 'Impressionante! Sua pontuação foi $_pontuacaoTotal.';
        corMensagemFinal = Colors.purple;
      } else {
        mensagemFinal = 'Nível Jedi! Sua pontuação foi $_pontuacaoTotal.';
        corMensagemFinal = Colors.red;
      }
    } else {
      mensagemFinal = ''; // Não usada quando há perguntas
    }

    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Perguntas'),
          centerTitle: true,
          backgroundColor: Colors.blueAccent,
        ),
        // -- ALTERAÇÃO 4: CORPO DO WIDGET ALTERADO PARA SER DINÂMICO --
        body: temPerguntas
            ? Column(
                mainAxisAlignment: MainAxisAlignment.center,
                crossAxisAlignment: CrossAxisAlignment.stretch,
                children: <Widget>[
                  // Exibe a pergunta atual do mapa
                  Pergunta(
                    _perguntasERespostas[_perguntaSelecionada]['textoPergunta']
                        as String,
                  ),
                  const SizedBox(height: 30),
                  // -- ALTERAÇÃO 5: GERAÇÃO DINÂMICA DOS BOTÕES DE RESPOSTA --
                  // Usa o método .map para transformar cada resposta em um widget Resposta.
                  // Agora, passamos o 'texto' e a 'pontuacao' para o Resposta.
                  ...respostasDaPerguntaAtual.map((resp) {
                    return Padding(
                      padding: const EdgeInsets.symmetric(
                        horizontal: 0,
                        vertical: 7.5,
                      ), // Espaçamento entre os botões
                      child: Resposta(
                        texto: resp['texto'] as String,
                        // Passa a pontuação da resposta para o método _responder
                        onPressed: () => _responder(resp['pontuacao'] as int),
                      ),
                    );
                  }).toList(),
                ],
              )
            : Center(
                child: Column(
                  mainAxisAlignment: MainAxisAlignment.center,
                  children: [
                    Text(
                      mensagemFinal, // <-- EXIBE A MENSAGEM FINAL
                      style: TextStyle(
                        fontSize: 28,
                        fontWeight: FontWeight.bold,
                        color: corMensagemFinal, // <-- COR DA MENSAGEM
                      ),
                      textAlign: TextAlign.center,
                    ),
                    const SizedBox(height: 20),
                    ElevatedButton(
                      onPressed: _reiniciarQuiz, // <-- BOTÃO DE REINICIAR
                      style: ElevatedButton.styleFrom(
                        backgroundColor: const Color.fromARGB(
                          255,
                          22,
                          155,
                          196,
                        ),
                        foregroundColor: const Color.fromARGB(
                          255,
                          228,
                          224,
                          231,
                        ),
                        padding: const EdgeInsets.symmetric(
                          horizontal: 30,
                          vertical: 15,
                        ),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text(
                        'Reiniciar Quiz',
                        style: TextStyle(fontSize: 18),
                      ),
                    ),
                  ],
                ),
              ),
      ),
    );
  }
}

// --- Componentes de Estilização ---

// Widget Stateless para exibir a pergunta com estilo.
class Pergunta extends StatelessWidget {
  final String texto;

  const Pergunta(this.texto, {super.key});

  @override
  Widget build(BuildContext context) {
    return Text(
      texto,
      style: const TextStyle(
        fontSize: 28,
        fontWeight: FontWeight.bold,
        color: Color.fromARGB(255, 2, 173, 11),
      ),
      textAlign: TextAlign.center,
    );
  }
}

// Widget Stateless para exibir um botão de resposta com estilo.
class Resposta extends StatelessWidget {
  final String texto;
  final VoidCallback onPressed;

  const Resposta({required this.texto, required this.onPressed, super.key});

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: onPressed,
      style: ElevatedButton.styleFrom(
        backgroundColor: Colors.blue,
        foregroundColor: Colors.white,
        padding: const EdgeInsets.symmetric(horizontal: 40, vertical: 15),
        shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(10)),
      ),
      child: Text(texto, style: const TextStyle(fontSize: 18)),
    );
  }
}
