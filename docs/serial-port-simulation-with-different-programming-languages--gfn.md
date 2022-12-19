# 不同编程语言下的串口仿真

> 原文：<https://dev.to/helgayork/serial-port-simulation-with-different-programming-languages--gfn>

# 串口仿真的主要原理

创建多个虚拟串行端口的主要好处之一是，开发人员可以自由地在没有任何物理串行端口的计算机上测试和调试串行应用程序。

例如，当开发一个串行通信程序时，工程师需要使用至少两个串行端口。当今市场上的大多数计算机最多只有一个串行端口，而许多根本没有。解决方法包括使用两台机器或使用 PCI 卡向系统添加 COM 端口。使用串行端口模拟器可以让开发人员在单台计算机上创建所需数量的虚拟串行端口。这意味着任何可以运行仿真软件的机器都可以用来开发串行软件。

COM 接口的仿真意味着软件开发人员可以省去控制资源共享和进程间通信的复杂解决方案。虚拟端口之间的传输速度取决于系统的容量，与串行端口的物理限制无关。

当使用虚拟 COM 端口时，物理串行接口的参数被完全复制，包括严格的波特率仿真。

**[Eltima 软件的虚拟 COM 端口驱动](https://www.eltima.com/products/vspdxp/)** (VSPD)是一个全面的虚拟串口仿真器，适用于 Windows 7 以及所有更高版本的 Windows OS。该工具为用户提供了一个强大的虚拟串行端口仿真器，适用于 32 位和 64 位操作系统，所有这些都封装在一个非常用户友好和直观的界面中。使用这个 Windows 10 的串行端口仿真器可以利用使用 WHQL 进行数字签名的软件。VSPD 是串行开发人员可用的最有效的串行端口仿真应用程序之一

# 不同编程语言下的串口仿真

软件开发人员知道，在许多情况下，在测试和调试代码时，使用虚拟设备模拟行为比使用物理设备更有优势。

一个很好的例子是在开发复杂的串行通信系统时使用串行端口仿真器。有许多虚拟串行端口仿真器可用，它们通常在 Windows 操作系统上使用，并允许开发人员[创建虚拟串行端口](https://www.eltima.com/create-virtual-serial-port.html)用于测试目的。使用这些串行端口模拟器应用程序，可以轻松监控和测试 RS232 或 RS484 接口之间的通信。

开发您自己的虚拟端口模拟器可以帮助您监控应用程序和物理串行端口之间的串行传输。这可以测试通常与物理 COM 端口交互的程序，而不需要到端口的物理连接。专门的应用程序可以创建虚拟零调制解调器电缆，允许通过虚拟串行接口对进行通信。

VSPD 通过支持诸如 CreatePair、DeletePair 和 SetStrictBaudrate 之类的标准选项，使得处理文件和设备变得容易。这里有几个例子，说明了在使用当今更流行的编程语言时，如何结合虚拟串口驱动程序的强大功能。

## 德尔斐

Delphi 的函数声明如下:

```
Function CreatePair(Port1, Port2 : PChar) : Boolean; stdcall; external "VSPDCTL.DLL";
Function DeletePair(Port : PChar) : Boolean; stdcall; external "VSPDCTL.DLL";
Function DeleteAll : Boolean; stdcall; external "VSPDCTL.DLL";
Function SetStrictBaudrateName(Port: PChar, StrictBaudrate: Boolean) : Boolean; stdcall; external "VSPDCTL.DLL";
Function SetStrictBaudrateHandle(hPort: THandle, StrictBaudrate: Boolean) : Boolean; stdcall; external "VSPDCTL.DLL";
Function CreatePair(Port1, Port2 : PChar) : Boolean; stdcall; external "VSPDCTL.DLL";
Function DeletePair(Port : PChar) : Boolean; stdcall; external "VSPDCTL.DLL";
Function DeleteAll : Boolean; stdcall; external "VSPDCTL.DLL";
Function SetStrictBaudrate(Port: PChar, StrictBaudrate: Boolean) : Boolean; stdcall; external "VSPDCTL.DLL";
Function SetBreak (Port: PChar, bBreak: Boolean) : Boolean; stdcall; external "VSPDCTL.DLL";
Function QueryBus (InBuffer: Pointer, sizeInBuffer: Integer, OutBuffer: Pointer, sizeOutBuffer: Integer) : Boolean; stdcall; external "VSPDCTL.DLL";
Function SetWiring (Port: PChar Buffer: Pointer, sizeBuffer: Integer) : Boolean; stdcall; external "VSPDCTL.DLL";> 
```

ArmAccess 可以静态或动态加载。要使用动态加载，请使用相同的代码:

```
Function CreatePairFn(PortName1, PortName2 : String) : Boolean;
Type TCreatePair = function(Port1, Port2 : PChar) : Boolean; stdcall;
Var Handle : THandle;
CreatePair : TCreatePair;
Begin
 CreatePairFn := False;
 Handle := LoadLibrary("VSPDCTL.DLL");
 If (Handle <> 0) Then
  Begin
   @CreatePair:=GetProcAddress(Handle, "CreatePair");
   If (CreatePair <> Nil) Then
    CreatePairFn := CreatePair(PChar(PortName1), PChar(PortName2));
    FreeLibrary(Handle);
  End;
End; 
```

## C/C++

要使用虚拟端口仿真器，在加载 DLL 文件后动态地选择和调用你想要的函数..下面是它在 Visual C++中的样子:

```
typedef bool (stdcall *CreatePairFn)(char *Port1, char *Port2);
typedef bool (stdcall *DeletePairFn)(char *Port);
typedef bool (stdcall *DeleteAllFn)(void);
typedef bool (stdcall *SetStrictBaudrateFn) (char *Port, bool StrictBaudrate);
typedef bool (stdcall *SetBreakFn) (char *Port, bool bBreak);
typedef bool (stdcall *QueryBusFn) (void* InBuffer, long sizeInBuffer, void* OutBuffer, long sizeOutBuffer);
typedef bool (stdcall *SetWiringFn) (char *Port, void *Buffer, long sizeBuffer);
typedef bool (stdcall *SetAccessListFn) (char *Port, void *Buffer, long sizeBuffer);

typedef bool (stdcall *CreatePairExFn)(char *Port1, char *Port2, char * UserSession);
typedef bool (stdcall *CreatePairWFn)(WCHAR *Port1, WCHAR *Port2);
typedef bool (stdcall *CreatePairExWFn)(WCHAR *Port1, WCHAR *Port2, WCHAR * UserSession);
typedef bool (stdcall *DeletePairWFn)(WCHAR *Port);
typedef bool (stdcall *SetStrictBaudrateWFn) (WCHAR *Port, bool StrictBaudrate);
typedef bool (stdcall *SetBreakWFn) (WCHAR *Port, bool bBreak);
typedef bool (stdcall *SetWiringWFn) (WCHAR *Port, void *Buffer, long sizeBuffer);
typedef bool (stdcall *SetAccessListWFn) (WCHAR *Port, void *Buffer, long sizeBuffer);
typedef bool (stdcall *DeletePairExFn)(char *Port, char * UserSession);
typedef bool (stdcall *DeletePairExWFn)(WCHAR *Port, WCHAR * UserSession);

typedef bool (stdcall *GetUserSessionFn)(char *Session, int &iSize);
typedef bool (stdcall *GetUserSessionWFn)(WCHAR *Session, int &iSize);

typedef bool (stdcall *SetStrictBaudrateExWFn) (WCHAR *Port, WCHAR * UserSession, bool StrictBaudrate);
typedef bool (stdcall *SetStrictBaudrateExFn) (char *Port, char * UserSession, bool StrictBaudrate);
typedef bool (stdcall *SetBreakExWFn) (WCHAR *Port, WCHAR * UserSession, bool bBreak);
typedef bool (stdcall *SetBreakExFn) (char *Port, char * UserSession, bool bBreak);
typedef bool (stdcall *SetAccessListExWFn) (WCHAR *Port, WCHAR * WCHAR, void *Buffer, long sizeBuffer);
typedef bool (stdcall *SetAccessListExFn) (char *Port, char * WCHAR, void *Buffer, long sizeBuffer); 
```

。

```
bool CreateVSPair(char *Port1, char *Port2) {
OSVERSIONINFO VersionInfo;
HINSTANCE libInst;
libInst = LoadLibrary(VSPDCTL.DLL);
if (!libInst)
   return false; /* Couldn't load library */
/* Substitute the typedefs above for functions other than CreatePairFn */
CreatePairFn CreatePair=(CreatePairFn)GetProcAddress(libInst, CreatePair);
if (CreatePair==0) return false; /* Couldn`t find function */
returnvalue=CreatePair(Port1, Port2); /* For example, Port1 = COM5 and Port2 = COM6 */
FreeLibrary(libInst);
return returnvalue;
}; 
```

## visualbasic 语言

为了在 Visual Basic 中使用 VSPD，请将以下几行粘贴到源文件中:

```
Declare Function CreatePair Lib "VSPDCTL.DLL" (ByVal Port1$, ByVal Port2$) As Boolean
Declare Function DeletePair Lib "VSPDCTL.DLL" (ByVal Port$) As Boolean
Declare Function DeleteAll Lib "VSPDCTL.DLL" () As Boolean
Declare Function SetStrictBaudrateName Lib "VSPDCTL.DLL" (ByVal Port$, ByVal StrictBaudrate As Boolean) As Boolean
Declare Function SetStrictBaudrateHandle Lib "VSPDCTL.DLL" (ByVal Handle As Long, ByVal StrictBaudrate As Boolean) As Boolean
Declare Function CreatePair Lib "VSPDCTL.DLL" (ByVal Port1$, ByVal Port2$) As Boolean
Declare Function DeletePair Lib "VSPDCTL.DLL" (ByVal Port$) As Boolean
Declare Function DeleteAll Lib "VSPDCTL.DLL" () As Boolean
Declare Function SetStrictBaudrate Lib "VSPDCTL.DLL" (ByVal Port$, ByVal StrictBaudrate As Boolean) As Boolean
Declare Function SetBreak Lib "VSPDCTL.DLL" (ByVal Port$, ByVal bBreak As Boolean) As Boolean
Declare Function QueryBus Lib "VSPDCTL.DLL" (ByVal InBuffer As String, sizeInBuffer As Long, ByVal OutBuffer As String, sizeOutBuffer As Long) As Boolean
Declare Function SetWiring Lib "VSPDCTL.DLL" (ByVal Port1$, ByVal Buffer As String, sizeBuffer As Long) As Boolean 
```

如果将 VSPDCTL.DLL 存储在应用程序的目录中，就可以直接进行函数调用。