### Eventos notificadores

Hoje, construíndo uma classe abstrata para abstrair problemas da linguagem ESIm EPL, geralmente suportada por impressoras térmicas honeyhell, me deparei com
problemas em que em ObjectPascal , uso com muita frequência,Eventos.

Acontecimentos em determinadas partes da construção em que precisam ou dão suporte, criando pontos onde são possíveis recuperar informações em tempo de 
execução.

Em ObjectPascal, podemos construir isso de uma forma simples.

```Delphi
type TAfterEvent  = procedure( Arg: string ) of object;
type TBeforeEvent = procedure( Arg: string ) of object;
type 
    TFoo = class()  
  private
   FTAfterEvent :TAfterEvent;
   FTBeforeEvent: TBeforeEvent;       
   public     
      procedure Algumacoisa();
      property BeforeAlgumacoisa : TBeforeEvent read FTBeforeEvent write FTBeforeEvent;
      property AfterAlgumacoisa  : TAfterEvent read FTAfterEvent write FTAfterEvent;   
    end;

implementation

procedure TFoo.Algumacoisa();
begin
  if Assigned(FTBeforeEvent) then FTBeforeEvent('Antes de fazer .. estou de notificando alguma coisa ');
    for I = 0 to ....

  if Assigned(FTAfterEvent) then FTAfterEvent('Depois de fazer .. estou de notificando alguma coisa ');
  
end;

```

Explicando, criamos um tipo , um tipo que representa literalmente o que ele representa, é um Evento de After ,.. Antes de alguma coisa , Before outra coisa.
Criamos as propriedades de acesso para ele, para que a visibilidade esteja protegida, e a variáveis que aloca a instância do type event, não seja 
publicamente acessada, mas sim por meio de acessos Get e Setlers.

Na chamada de procedimento Algumacoisa(), temos que verificar se a Variável que Aloca a instância do Type Event, não está nil e consecutivamente. 
nós invokamos o método, passando algum argumento válido de qualquer tipo, basta tipar os argumentos  do evento caso precise de argumentos.

Agora vamos ver isso em C++, só que de uma forma diferente , vamos ver algo mais semelhante ao padrão de projeto Observer, vamos estar tratando isso de uma mais ampla, com Notificadores e Escutadores, para tal, um Vector será necessário e é a forma mais simples e óbvia de construir um sistema notificador de eventos.


```c++

#include <iostream>
#include <vector>

class EventPublisher;

using MyEvent = void (*)(const std::string&);

class EventPublisher {
public:
    void AddListener(MyEvent listener) {
        listeners.push_back(listener);
    }

    void TriggerEvent(const std::string& message) {
        for (const auto& listener : listeners) {
            listener(message);
        }
    }

private:
    std::vector<MyEvent> listeners;
};



class EventSubscriber {
public:
    static void HandleEvent(const std::string& message) {
        std::cout << "Aconteceu algo e você foi notificado: " << message << std::endl;
    }
};

int main() {
    EventPublisher publisher;
    EventSubscriber subscriber;

    publisher.AddListener(&EventSubscriber::HandleEvent);
    publisher.TriggerEvent("Ola meu amigo C++!");
    return 0;
}
```
Vamos analisar agora, é obvio que em Delphi / Objectpascal, temos a mesma possibilidade , pois isso é um Padrão comportamental conhecido e talve o mais básico deles.

Consiste no seguinte:

Em ambos os idiomas, Object Pascal (Delphi) e C++, temos a capacidade de criar eventos notificadores para permitir a comunicação entre partes distintas do código. Embora a implementação possa diferir, o conceito fundamental é o mesmo - criar pontos no código onde os objetos podem se inscrever para serem notificados quando algo acontecer, mas particulamente eu quero falar dessa implementação em C++.

Eu precisei implementar esse padão em uma biblioteca de comunicação serial e Paralela para linguagem ZSIM , ESIM dos equipamentos Honeyhell( impressoras térmicas ).
A construção é Abstrata e ela serve, pode ser especializada , ela não é selada , e permite que novas linguagens ou outras linguagens como PPLA e PPLB possam ser construídas herdando da Abstração base.

A Classe em diversos momentos , precisa notificar ou fornecer pontos de eventos, como, AfterPrint, BeforePrint, OnError, Beforeisso, After Aquilo, são muitos pontos, extremidades de eventos para serem tratados.

A Biblioteca padrão é C++, ela pode ser abstraída em ObjectPascal delphi sem problemas, inclusive eu ja tenho isso abstraído. O grande porém é que, irei precisar de uma portabilidade disso para sistemas operacionais Linux e também por que irei disponibilizar isso para a fabricante, uma parceria entre a softwareHouse e a fabricante.

Começa importando as bibliotecas necessárias, incluindo a "iostream" para saída no console e "vector" para a estrutura de dados de vetor, ok até ai, bem simples.

A seguir, é definida a classe ```EventPublisher```. Essa classe é responsável por gerenciar os observadores e notificar quando ocorrer um evento. Ela possui uma função AddListener que permite adicionar observadores (representados por funções) a uma lista interna chamada listeners. 


using MyEvent = void (*)(const std::string&); ```

```using``` é uma construção da linguagem C++ que permite criar um alias (um novo nome) para um tipo existente, permitindo que você use um nome mais descritivo para um tipo complexo ou longo de uma forma que seja mais simples referencia-lo.

```= void (*)(const std::string&) ``` --> O ```=``` significa que estamos atribuindo um significado ao novo tipo MyEvent. Agora, vamos quebrar a parte após o ( = ).

```void (*)``` --> Isso é um ponteiro para uma função. O void indica que a função não retorna um valor específico, ou seja, é uma função que não possui valor de retorno.

Em C++, as funções são representadas por ponteiros para funções, quando você deseja definir um alias para um tipo de função, você precisa usar a sintaxe de ponteiro, ( Isso eu ainda não explorei, mas vou tentar sem ponteiros ou ver se é possível! )

```(const std::string&)``` --> Isso é a assinatura da função que o ponteiro está apontando. Indica que a função recebe um argumento, que é uma referência constante para uma string.

```c++
void AddListener(MyEvent listener) {
    listeners.push_back(listener);
}
```

void AddListener(MyEvent listener) --> Esta é a assinatura de uma função chamada AddListener, que recebe um parâmetro chamado listener do tipo MyEvent. A função não possui valor de retorno (void indica que não retorna nada).
Esse método pede um parametro do tipo ````MyEvent``` , por que ?, Ele precisa adicionar ao vetor de Escutadores quem se inscreve na lista de escutadores, então quem Herdar de um MyEvent , será adicionado ao vetor, isso nos possibilitará , depois percorrer o vetor.

MyEvent listener: --> MyEvent é um tipo definido previamente como um ponteiro para uma função que recebe um argumento const std::string& (uma referência constante para uma string) e não retorna nada (void). Esse tipo representa a assinatura de uma função que pode ser usada como um observador de eventos.

listeners.push_back(listener): Aqui, listeners é um vetor que armazena funções observadoras (MyEvent). A função push_back é um método do vetor que insere o valor passado como argumento no final do vetor. Neste caso, ela insere a função listener no final da lista de observadores.

A função TriggerEvent percorre essa lista de observadores e invoca cada um deles, passando a eles uma mensagem.

```c++

void TriggerEvent(const std::string& message) {
    for (const auto& listener : listeners) {
        listener(message);
    }
}

```
Ela dispara um evento para todos os ouvintes (listeners) adicionais ao vector.
``` auto& listener ``` cria uma referência constante para cada elemento da coleção listeners à medida que o loop itera.

```auto ``` --> O uso de auto é uma característica do C++ chamada "inferência de tipo". Isso significa que o compilador deduz automaticamente o tipo de dados com base na expressão à qual é atribuído. No caso do loop, o tipo de dados é deduzido do tipo dos elementos na coleção listeners.

```&``` --> O operador & usado após auto indica que estamos criando uma referência para o tipo de dados deduzido.

```const``` -> A palavra-chave const indica que a referência criada é constante, ou seja, não pode ser usada para modificar o valor do elemento original na coleção.





