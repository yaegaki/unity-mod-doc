MODについては<a href="http://ja.wikipedia.org/wiki/Mod_%28%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%82%BF%E3%82%B2%E3%83%BC%E3%83%A0%29">Mod (コンピュータゲーム) - Wikipedia</a>にあるようにゲームに後から改造を加えることを言います。  

MineCraftやSkyrimなどのゲームがMODで有名です。  

Skyrimのようなゲームの場合は公式でMODを作るための仕組みが用意されています。  
しかし、全てのゲームにその仕組みが用意されているわけではありません。  
仕組みが用意されていないゲームについては自分でゲームを改造しなければならないです。  

C言語で作成されたゲームの場合、改造することはそれなりに難しいですが  
C#で作成されたゲームは簡単に改造することができます。  
ただし、ある程度のUnityとC#の知識が必要になります。  

使用するツールは  
<ul>
	<li>JustDecompile</li>
	<li>Visual Studio</li>
	<li>ILSpy</li>
</ul>
です。  

Visual Studioはなじみ深いツールだと思いますが、他の二つはあまり聞いたことがないかもしれません。  
この二つはC#のデコンパイラです。  
このツールを使用するとコンパイル済みのバイナリを人間に読めるようなコードに変換してくれます。  

それでは前置きが長くなりましたがさっそく始めていきましょう。  
カスタムメイド3D2の体験版です。  
R-18のソフトなので一応注意しましょう。  

まず、Visual Studioをインストールします。  
たぶん既にインストールしている人のほうが多いと思うので詳しくは省きます。  

次にJust Decompileをインストールします。  
<a href="http://www.telerik.com/download/justdecompile">Just Decompile</a>からダウンロードできます。  

インストールするときにいろいろ設定しますが、以下のように設定すればいいです。  
最後の名前やメールアドレスを聞かれるところはてきとうに答えても大丈夫です。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/install.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/install.png" alt="install" width="300" height="270" /></a>  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/vs.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/vs.png" alt="vs" width="300" height="270" /></a>  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/cp.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/cp.png" alt="cp" width="300" height="270" /></a>  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/logon.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/logon.png" alt="logon" width="300" height="270" /></a>  

ILSpyはあってもなくても大丈夫です。  
ただ個人的にはJust Decompileより見やすいのでお勧めです。  
<a href="http://ilspy.net/">ILSpy</a>からダウンロードできます。  

Just Decompileのインストールが完了したらさっそく開いてみましょう。  
なにもない画面が表示されると思います。  

次に、Just Decompileに改造用のプラグインを導入します。  
上部のメニューからPlugins &gt; Plugins Managerを選択します。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/pm.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/pm.png" alt="pm" width="300" height="196" /></a>  

出てきた画面の中からAssembly Editorをダウンロードします。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/ae.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/ae.png" alt="ae" width="300" height="188" /></a>  

ダウンロードが完了すると一度Just Decompileを再起動します。  
するとPluginsにReflexilという項目が追加されています。  
追加されていればプラグインのインストール完了です。  

インストールが完了したら試しにゲームのバイナリをデコンパイルしてみましょう。  
今回の対象とするゲームならインストールフォルダの中のCM2D2x64_Data/Managed/Assembly-CSharp.dllです。  
PCによってはx64の部分がx86だったりするかもしれません。  
Assembly-CSharpをJust Decompileにドラッグドロップすると以下のような画面になります。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/dc.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/dc.png" alt="dc" width="300" height="195" /></a>  

左のツリーは開くことができるのでどんどん開けていくとコードが見えてきます。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/code.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/code.png" alt="code" width="300" height="195" /></a>  

このコードをじっくり読んでゲームがどんなふうに実装されているか、どのような処理を付け加えるかを考えます。  

ある程度コードを読んだ後はようやく改造に取り掛かります。  
<ol>
	<li>付け加えたい処理を書いたDLLを作成する</li>
	<li>ゲームが始まった時に絶対呼ばれる処理を探す</li>
	<li>その部分に自作クラスの関数を呼ぶ処理を付け加える</li>
</ol>
です。  

まず、付け加えたい処理を書いたDLLを作成します。  
今回は画面にGUIのテキストを表示するだけの簡単なものを作成します。  

Visual Studioでクラスライブラリを作成します。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/vs_cl_.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/vs_cl_.png" alt="vs_cl_" width="300" height="163" /></a>  

ソリューションエクスプローラーから参照の追加を選択し、UnityEngine.dllの参照を加えます。  
UnityEngine.dllはAssembly-CSharp.dllと同じフォルダに存在するので、それを選択します。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/sansyou_.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/sansyou_.png" alt="sansyou_" width="300" height="163" /></a>  

OKを押すとコード内でUnityEngineの機能が使用できるようになります。  

次にコードを書いていきます。  
必要となるのはエントリポイントとなる関数、MonoBehaiviourを継承したクラスです。  

今回の場合は以下のようなコードになりました。  


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using UnityEngine;

namespace ClassLibrary1
{
    public class Class1 : MonoBehaviour
    {
        public static void Init()
        {
            var go = new GameObject();
            DontDestroyOnLoad(go);
            go.AddComponent<Class1>();
        }

        void OnGUI()
        {
            GUI.TextArea(new Rect(10, 10, 300, 100), "Hello, Mod!");
        }
    }
}

```


エントリポイントとなる関数はInitです。  
このInitをどうにかして呼び出してもらうことができれば画面にTextAreaを表示することができます。  

実際にビルドをすると、プロジェクトのフォルダの中のbin/Debugフォルダにプロジェクト名.dllというものができていると思います。  
今回の場合ではClassLibrary1.dllという名前でしたが、プロジェクト名によっては変わります。  
このClassLibrary1.dllをAssembly-CSharp.dllと同じフォルダに置いておきます。  

次にInitを呼び出す処理をAssembly-CSharp.dllに加えていきます。  
ClassLibrary1.dllを同じようにJust Decompileの中にドラッグドロップします。  

Assembly-CSharp.dllの中から絶対にゲーム中で呼んでもらえそうな処理を探します。  
今回の場合はGameUty.Initという処理がよさそうだということがわかります。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/gameuty_init.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/gameuty_init.png" alt="gameuty_init" width="300" height="243" /></a>  

GameUty.Init()をツリーから選択した状態で上部のメニューからPlugins &gt; reflexilを選択します。  
すると下のように怪しげな項目がでてきますが、これはMSILというもので.Net版のアセンブリみたいなものです。  
詳しくは難しいので書きませんが<a href="http://www.asukaze.net/etc/cil/opcode.html">ここ</a>を参考にしながら処理を読み解いていきます。  

ただ、今回の場合なら引数なしのstaticな関数を呼び出させたいだけなので簡単です。  

retの行を右クリックしてCreate New...を選択します。  

ダイアログがでてくるので  
OpCodeをcallに  
Operand typeを -&gt; Method referenceに  
OperandをClassLibrary1.dllのClass1.Initに設定します。  
そしてInsert Before Selectionをクリックします。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/call.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/call.png" alt="call" width="300" height="95" /></a>  

するとMSILの欄が以下のようになります。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/msil.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/msil.png" alt="msil" width="300" height="243" /></a>  

ここまでできたら作業は完了で変更をセーブします。  
左のツリーからAssembly-CSharpを右クリックし、Reflexil &gt; Save and Reloadを選択します。  

後はゲームを起動すればテキストエリアがでてきています。  

<a href="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/hello_mod.png"><img src="https://raw.githubusercontent.com/yaegaki/unity-mod-doc/images/images/hello_mod.png" alt="hello_mod" width="300" height="175" /></a>  

後はAssembly-CSharpを読みながら適当に処理を変えていくだけです。  
注意が必要なのは一部動かない関数があったりするので、その関数を使ってしまうとエラーもでないので  
間違えていることに気が付きにくいです。(LINQのSelectなど)  

常にテキストエリアなどに情報を表示しておくと開発がはかどります。  

後は<a href="http://qiita.com/rita0222/items/609583c31cb7f0132086">C#でプラグインDLLを作る方法(入門編) - Qiita</a>などを参考にしながらプラグインシステムなどを盛り込めばMODの完成です！  
