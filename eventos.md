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

Na chamada de procedimento Algumacoisa(), temos que verificar se a Variável que Aloca a instância do Type Event, não está nil e consecutivamente 
nós invokamos o método, passando algum argumento válido de qualquer tipo, basta tipar os argumentos  do evento caso precise de argumentos.

  
