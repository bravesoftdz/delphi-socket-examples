# delphi-socket-examples
delphi socket examples

Tcpserver �������
 LocalHostAddr  ��ȡ���ػ� IP;
 localport      ��ȡ���ض˿�;
 LocalHostName  ���ؼ��������;
 BlockMode ����, �����趨 TTCPServer Ϊ bmBlocking\bmNonBlocking\bmThreadBlocking ������ͨѶģʽ;   
    
   bmBlocking       Ϊ����ģʽ, ��ͬ���ķ�ʽ�������ݴ���,   
   bmNonBlocking    ������ģʽ, ���첽�ķ�ʽ�������ݴ���,   
   bmThreadBlocking �߳��첽ģʽ, ����һ�����߳��� TcpClient ����ͨѶ, ���߳�����ͬ���ķ�ʽ������    ��;

   ����� bmBlocking ģʽ, ���� OnAccept �¼��б�д����     
   procedure   TForm1.TcpServer1Accept(Sender:   TObject;  ClientSocket:   TCustomIpClient);   
         // ���� tcpclient ������   
         ClientSocket.ReceiveFrom()   
          ClientSocket.ReceiveBuf()   
    
        // �� tcpclient ��������   
        ClientSocket.SendBuf()   
        ClientSocket.SendTo()   
    
 ����� bmThreadBlocking ģʽ, ���� OnGetThread �¼��б�д����     
   procedure   TForm1.TcpServer1GetThread(Sender: TObject; var ClientSocketThread: TClientSocketThread);   
    begin   
         ClientSocketThread.ClientSocket.     
        // ���� tcpclient ������   
         ClientSocketThread.ClientSocket.ReceiveFrom()   
         ClientSocketThread.ClientSocket.ReceiveBuf()   
    
         // �� tcpclient ��������   
          ClientSocketThread.ClientSocket.SendBuf()   
          ClientSocketThread.ClientSocket.SendTo()   
 Listening    ���ز���ֵ, �Ƿ���������
 LookupHostName  ָ������ IP ���Ի�ȡ��������;

D7-->TCPserver ͨ�ŷ���
1��ָ���� Localport ����Ϊ���� 1024 ֵ������ֵ��Ŀ����ָ�� TCPsever1 �ļ����˿ڣ�
2���趨��ͨ�ŷ�ʽ��Blockmode��
3���� TCPsever1 �����ͻ�����������TCPsever1.active:=true��
4���� TCPserver1.OnAccept �¼���д��д�ͻ��˴��룻Receivebuf(char ���飬����),Sendbuf(char ���飬����) ��д�ͻ������ӣ�

var buff:array[0..255]of char;
      tmpbuf:string;
  begin
    Clientsocket.OnReceive:=onbuff;// �Զ��� Onbuff ��ʾ���յ�����
    while ClientSocket.Connected  do
    begin
        if ClientSocket.WaitForData(50)then// ��֤�������Ķ����ݣ�
        ClientSocket.receivebuf(buff[0],sizeof(buff)); // �����������ж���Ȼ����������

        if outbuf<>'' then   //outbuf Ϊȫ�ֱ������ڽ��� Form �е� Memo ֵ��
        begin
        tmpbuf:=outbuf;
        outbuf:='';
        ClientSocket.SendBuf(pchar(tmpbuf)^,length(tmpbuf));
    end;

// �����յ���������ʾ�� Memo
procedure Tform1.onbuff(Sender: TObject; Buf: pchar; var DataLen: Integer);
var buff:pchar;
    st:string;
    I:integer;
begin
  st:='';
  buff:=buf;
  for i:=0  to datalen do
  begin
  st:=st+ buff^;
  inc(buff);
  end;
  self.memo1.Lines.Add(st) ;
end;


## �ͻ���

D7-->internet-->TcpClient ��ʹ�÷���
1���趨���� Blockmode TCP ͨ�ŷ�ʽ��
                       RmoteHost  Զ�˷����� IP �����ƣ�
     RmotePort   ָ����˿ڣ�
2��TcpClient.active:=true;  ���� SOCKET���������������������
3�����ӳɹ���ͨ�� TcpClient �� ReceiveBuf �������� --->server �����ݣ�
     �����ʵʱ���տ���ͨ�� Timer ��д���´���ʵ�֣�
 var
   Buf: array[0..2048] of char;// �������ݴ浽���
 begin
   if TCPClient1.Connected and TCPClient1.WaitForData(50)
      then
        begin
          TCPClient1.ReceiveBuf(buf, sizeof(buf));
        end;
                end;
      ���У�WaitFData() ���ò��� socket �Ƿ�׼���ö��ˣ�WaitForData(50)=False ˵����ʱ 50 �����δ׼��������
4����Ϊ���� ReceiveBuf ���� OnReceive �¼�����, ���ԣ������� Tcpclient1.onreceive �¼���д���ݴ�����룻
   �罫���յ���������ʾ�� Memo �У�
  var
  Astr: string;
  Achar: Pchar;
  index: integer;
     begin
   Astr := '';
   Achar := buf;
   for index := 1 to datalen do
     begin
       Astr := Astr + char(Achar^);
       INC(Achar);
     end;
  self.memo1.lines.add(Astr);
5���� -->Sever ��������ʹ�� SendBuf ��������ȷ�����ӵ�״̬�£�
     TcpClient1.SendBuf(pchar(self.SendData.text)^, length(senddata.text), 0);



## code

unit ClientMain;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms, Dialogs, StdCtrls, Sockets;

type
  TForm1 = class(TForm)
    Client: TTcpClient;
    btnConnect: TButton;
    btnSend: TButton;
    edText: TEdit;
    Label3: TLabel;
    procedure Button1Click(Sender: TObject);
    procedure btnSendClick(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

var
  Form1: TForm1;

implementation

{$R *.dfm}

procedure TForm1.btnConnectClick(Sender: TObject);
begin
  Client.Disconnect;
  Client.RemoteHost := '127.0.0.1';
  Client.RemotePort := '2609';
  Client.Connect;
  if Client.Connected then
    Form1.Caption := 'Client Connected';
end;

procedure TForm1.btnSendClick(Sender: TObject);
begin
  if Client.Connected then
    Client.SendLn( 'abcdefghijklmnop' );
end;

end.

This is the code for Server
CODE
unit ServerMain;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, StdCtrls, Sockets;

type
  TForm1 = class(TForm)
    Server: TTcpServer;
    btnConnect: TButton;
    log: TMemo;
    procedure btnConnectClick(Sender: TObject);
    procedure ServerAccept(Sender: TObject; ClientSocket: TCustomIpClient);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

var
  Form1: TForm1;

implementation

{$R *.dfm}

procedure TForm1.btnConnectClick(Sender: TObject);
begin
  Server.Active := false;
  Server.LocalHost := '127.0.0.1';
  Server.LocalPort := '2609';
  Server.Active := true;
  if Server.Active then
    Form1.Caption := 'Server Connected';
end;

procedure TForm1.ServerAccept(Sender: TObject;
  ClientSocket: TCustomIpClient);
var
  s: string;
begin
//  log.Lines.Append( ClientSocket.LookupHostName( ClientSocket.RemoteHost ) );
  s := ClientSocket.ReceiveLn;
  while s <> '' do begin
    log.Lines.Append( s );
    s := ClientSocket.ReceiveLn;
  end;
end;

end.