# GrasshopperのC#コンポーネントについて
## 入手先
Rhinocerosは有償のソフトです。使い続けるにはライセンスが必要です。3ヶ月間は無償で評価する事ができるので、直ぐに試して見ることができます。以下のサイトからダウンロードできます。  
https://www.rhino3d.com/jp/download

GrasshopperはRhinoceros上で複雑な形状を実現するためのノンプログラミングツールです。しかし、プログラミングをすることもできて、ここではむしろそのようにしか使いません。C#は標準で使えます。  
http://www.grasshopper3d.com/page/download-1

ここでは解説しませんが別途インストールすればPythonという言語も使うことが出来ます。  
www.food4rhino.com/project/ghpython

関係ありませんが、Windowsの仮想環境を簡単に作りたいときは以下のサイトに正規のイメージファイルがおいてあります。  
https://dev.modern.ie/tools/vms/windows/

ラボライセンスをはじめとするZooサーバーの利用の仕方は以下のサイトに公式のFAQがあります。  
http://www.rhino3d.co.jp/support/faq_detail.html
## C#コンポーネントの基本
インストールが出来たらスタートメニューからRhinoceros 5を起動したください。(64-bit)と書かれている方をお勧めします。  
起動したらgrasshopperと入力して起動してください。初回起動時には規約への同意が求められるので適宜対処してください。  
grasshopperが起動すると適当な場所でダブルクリックしてC#と入力すればC#コンポーネントが作成できます。

C#コンポーネントが出来たら真ん中の部分をダブルクリックしたください。
Script Editorというのが現れるはずです。ここでC#スクリプトを入力します。
白い部分が入力できるところです。左側の+とか-とか書いているところをクリックすると省略したり省略されている部分を展開したりできます。  
Script Editorを閉じて次はマウスのスクロールホイールでC#コンポーネントをズームしてみてください。
+とか-とかが見れるはずです。これは、入力と出力を増やしたり減らしたりするためのボタンです。  
xやyといった変数を右クリックしてみてください。一番上の変数が書いてある部分は実はテキストボックスになっていて変数名を変更できます。名前にはスペースを入れないでください。  
下の方のType Hintでは引数の型を設定できます。  
Item AccessとList Accessを切り替えれば複数の値が入力された場合の対処が変化します。
Item Accessの場合に複数の値が入力されればその回数だけ全体が繰り返されます。List Accessの場合はListとして入力されるので自前で処理することが出来ます。
Tree Accessはまず使うことはありません。

Rhinoceros画面上にあるオブジェクトを操作する場合にはC#コンポーネント以外のコンポーネントを使う必要があります。
例えば、Surfaceを扱いたいならSurfaceコンポーネントを追加します。それを右クリックしSet one Surfaceとした後に選択してください。  
そのようにして設定した値はRhinocerosを再起動する度に設定しなおさなければなりません。
grasshopper側にそれらを保存したい場合、コンポーネントを右クリックしたInternalise dataをクリックしたください。

C#コンポーネント等で作成したジオメトリはgrasshopperウィンドウを閉じると消えてしまいます。
それをRhinoceros上に配置するには各コンポーネントを右クリックしBakeを押します。
プレビューが邪魔な場合はPreviewをクリックします。
複数選択しスクロールボタンをクリックすることで複数のコンポーネントを一度に操作できます。
なおBakeはC#コンポーネント上でも出来るので後で解説します。

## 基本手法
GrasshopperのC#ではよく使う手法がいくつかあります。  
サンプルは以下においてあります。  
https://github.com/kurema/LearnGrasshopperCSharp/tree/master/src
### 基本的な形状の作成
Grasshopperやモデリングではよく使う手法はたいていC#上でそれなりに簡単に利用することができます。
Script Editor を開いたらRunScript内にまず``A=Rhino.Geometry.``と打ち込んでください。そうすると予測変換の要領でいろんな文字が表示されます。これはモデリングソフトでの基本的なジオメトリ作成に相当する操作です。以下に例を挙げます。適当な行を抜き出して試してみてください。  
000.basic.ghxにまとめてあります。
```
    A = new Rhino.Geometry.Point3d(1, 3, 4);
    A = new Rhino.Geometry.Vector3d(0, 4, 2);
    A = new Rhino.Geometry.Matrix(3, 3);
    A = new Rhino.Geometry.Plane(Point3d.Origin, Vector3d.XAxis, Vector3d.ZAxis);

    A = new Rhino.Geometry.Circle(4);
    A = new Rhino.Geometry.Arc(new Circle(5), Math.PI / 4.0);
    A = new Rhino.Geometry.BezierCurve(new Point3d[]{new Point3d(0, 0, 0),new Point3d(5, 3, 0),new Point3d(8, -1, 0)}).ToNurbsCurve();
    A = new Rhino.Geometry.Box(Plane.WorldXY, new Interval(0, 1), new Interval(-1, 1), new Interval(0, 3));
    A = new Rhino.Geometry.Cone(Plane.WorldXY, 10, 3);
    A = new Rhino.Geometry.Cylinder(new Circle(4), 10);
    A = new Rhino.Geometry.Line(Point3d.Origin, new Point3d(1, 4, 2));
    A = new Rhino.Geometry.Rectangle3d(Plane.WorldXY, 2, 4);
    A = new Rhino.Geometry.Torus(Plane.WorldXY, 10, 3);

    var p = new Rhino.Geometry.Polyline(0);
    p.Add(1, 3, 4);
    p.Add(4, 2, 8);
    p.Add(0, 2, 5);
    A = p;
    
    A = Rhino.Geometry.NurbsCurve.CreateInterpolatedCurve(new Point3d[]{new Point3d(0, 0, 0),new Point3d(5, 3, 0),new Point3d(8, -1, 0)}, 3);

```
### 基本的な生成手法
その他にいくつかの基本的な操作が可能です。
001.Generate.ghxにあります。

まず立体を作るときによく使うExtrusionです。
```
private void RunScript(Curve x, ref object A)
  {
    A = Surface.CreateExtrusion(x, Vector3d.ZAxis * 10);
  }
```
Loftですが、始点・終点の指定があるのが違っています。
```
  private void RunScript(List<Curve> x, object y, ref object A)
  {
    A = Brep.CreateFromLoft(x, Point3d.Origin, Point3d.Origin + Vector3d.ZAxis * 8, LoftType.Tight, false);
  }
```
Revolve(軸を中心とした回転)
```
    A = RevSurface.Create(NurbsCurve.CreateInterpolatedCurve(new Point3d[]{new Point3d(2, 0, 0),new Point3d(4, 0, 2),new Point3d(3, 0, 4)}, 3), new Line(0, 0, 0, 0, 0, 10)).ToNurbsSurface();
```
Sweep
```
    var curve1 = NurbsCurve.CreateInterpolatedCurve(new Point3d[]{new Point3d(2, 0, 0),new Point3d(4, 0, 2),new Point3d(3, 0, 4)}, 3);
    A = Brep.CreateFromSweep(curve1, new Arc(new Circle(1), Math.PI * 7 / 4.0).ToNurbsCurve(), false, 0.1);
```
Pipe
```
    var curve1 = NurbsCurve.CreateInterpolatedCurve(new Point3d[]{new Point3d(2, 0, 0),new Point3d(4, 0, 2),new Point3d(3, 0, 4)}, 3);
    A = Brep.CreatePipe(curve1, 1.0, false, PipeCapMode.Round, true, 0.1, 0.1);
```

### 変形する
たいていのGeometryにはいくつかの変形処理が可能です。002.Transform.ghxにあります。Script editor上でTransform.と入力すると一覧が確認可能です。

変形処理は、以下のような書き方で可能です。
```
    SomeGeometry.Transform(Transform.*));
```

移動はふたつの方法が出来ます。大抵、以下のように出来ます。
```
    SomeGeometry.Translate(1,3,4);
```
Translateが機能しない場合には次のように出来ます。
```
    SomeGeometry.Transform(Transform.Translation(1,3,4));
```

回転はTransform.Rotationで可能です。
```
    x.Transform(Transform.Rotation(Math.PI / 3.0, Vector3d.XAxis, Point3d.Origin));
    A = x;
```

反転はTransform.Mirrorで可能です。
```
    x.Transform(Transform.Mirror(Plane.WorldXY));
    A = x;
```

回転や平行移動を含めた座標平面の移動はTransform.PlaneToPlaneで可能です。  
一つ目の引数に移動元、ふたつ目の引数が移動先の平面です。順番を混乱しがちなので気をつけてください。
```
    x.Transform(Transform.PlaneToPlane(Plane.WorldXY, new Plane(Point3d.Origin, new Vector3d(-1, 1, 0))));
    A = x;
```
### 曲線の分析
曲線の評価にはたいてい、その曲線上の位置を示すパラメーターtを用います。tを用いない分析は長さやバウンディングボックスの取得程度です。
```
  private void RunScript(Curve x, ref object A)
  {
    double l=x.GetLength();//長さを評価

    BoundingBox b = x.GetBoundingBox(true);//バウンディングボックス(曲線を覆う箱)を取得。
  }
```
このパラメーターは曲面におけるuvのようなものです。
ただし、このパラメーターは必ずしも長さに対応するわけでもなければ、0～1の値をとるわけでもありません。
扱いやすくするために、このパラメーターを0～1の間の値を取るようにすると便利です。
```
    curve.Domain = new Interval(0, 1);
```
ためしに、t=0.5の場所と、長さがちょうど半分の場所を比べてみましょう。
```
  private void RunScript(Curve x, ref object A, ref object B)
  {
    x.Domain = new Interval(0, 1);
    A = x.PointAt(0.5);
    B = x.PointAtNormalizedLength(0.5);
  }
```
結構違いますね。パラメーターは直接指定することはむしろまれで、いくつかの手法を用いて取得することで得られます。
```
  private void RunScript(Curve x, ref object A)
  {
    double t;
    x.LengthParameter(1.4, out t);//始点からの長さでパラメーターを取得する。
    x.NormalizedLengthParameter(0.3, out t);//始点からの長さの曲線長に対する割合でパラメーターを取得する。
    x.ClosestPoint(new Point3d(0, 0, 0), out t);//ある点からもっとも近い曲線上の点を取得する。
  }
```
ここで出てきたoutというのは関数に変数を渡して代入してもらう時に使うキーワードです。
関数にoutが必要な場合にはあらかじめ変数を用意した上で、引数でもoutと書いておかなければなりません。
上の``RunScript(Curve x, ref object A)``のrefも似たようなもので、こちらは関数が渡された変数の中身の参照もする場合に使われます。  
たまに使うつもりのないのにoutで使うための変数を用意しなければならない場合もありますが、それでもあらかじめ用意しなければなりません。

これらの手法で得られたパラメーターを用いて、実際の座標や接線等を取得することが出来ます。
```
  private void RunScript(Curve x, ref object A, ref object B, ref object C)
  {
    double t;
    x.ClosestPoint(new Point3d(0, 0, 0), out t);//ある点からもっとも近い曲線上の点を取得する。

    A = x.PointAt(t);//パラメーターの示す座標を取得
    B = x.CurvatureAt(t);//その点での曲率ベクトルを取得。
    C = x.TangentAt(t);//その点での接線ベクトルを取得。
  }
```

## 典型的操作
### 関数の示す曲線
上で紹介した``NurbsCurve.CreateInterpolatedCurve``を利用して関数の示す曲線を簡単に作ることができます。ただし、関数は連続であると仮定し、有限のサンプルで近似するので場合によっては不適切な結果を示します。

010.functionCurve.ghxではふたつの例を示しています。ひとつの例はその場で作成したものです。
```
    var points = new List<Point3d>();//サンプル点。
    int tCount = 100;//サンプル点の数。
    var tInterval = new Interval(0, 2);//関数の描画範囲。
    for(double t = tInterval.Min;t <= tInterval.Max;t += tInterval.Length / tCount){//tIntervalの最小値からtIntervalの最大値までtLength/tCountずつ増やして繰り返し。
      points.Add(new Point3d(t, Math.Pow(t, 2), Math.Pow(t, 3)));//(t,t^2,t^3)のサンプルを追加。
    }
    A = Rhino.Geometry.NurbsCurve.CreateInterpolatedCurve(points, 3);//pointsを滑らかにつなぐ。
```
もうひとつの例はクラスを使って定義し、delegate等の難しい機能を使ったものです。まず、下のコードを``// <Custom additional code>``(下側の空白)に追加してください。
```
  /// <summary>
  /// 自由曲線を示すクラスです。
  /// </summary>
  public class CurveFunction{
    public delegate Point3d Function3d(double u);
    /// <summary>
    /// グラフで描画する関数。
    /// </summary>
    public Function3d F;

    /// <summary>
    /// 直線の描画範囲。
    /// </summary>
    public Interval tInterval = new Interval(-1, 1);

    /// <summary>
    /// コンストラクタ。
    /// </summary>
    /// <param name="f">グラフで描画する関数。</param>
    public CurveFunction(Function3d f){
      this.F = f;
    }

    /// <summary>
    /// 関数が示す曲線をサーフェスとして取得します。
    /// </summary>
    /// <param name="tCount">曲線の解像度</param>
    /// <returns>目的の形状。</returns>
    public Curve GetCurve(int tCount=100){
      var points = new List<Point3d>();

      for(int t = 0;t < tCount;t++){
        double tValue = ((double) t / (double) (tCount - 1)) * tInterval.Length + tInterval.Min;
        points.Add(F(tValue));
      }
      return Rhino.Geometry.NurbsCurve.CreateInterpolatedCurve(points, 3);
    }

    /// <summary>
    /// 関数が示すサーフェスをBakeします。
    /// </summary>
    /// <param name="RhinoDocument">通常はRhinoDocumentと入力してください。</param>
    /// <param name="tCount">直線の解像度。</param>
    /// <returns></returns>
    public void BakeCurve(RhinoDoc RhinoDocument, int tCount){
      RhinoDocument.Objects.Add(GetCurve(tCount));
    }
  }
```
この準備をしておくと簡単に関数の示す曲線を描画することが出来ます。
```
    var cf = new CurveFunction((t) => {return new Point3d(Math.Sin(t), Math.Cos(t), t);}); //(sin(t),cos(t),t)の関数からCurveFunction型の変数を作る。
    cf.tInterval = new Interval(0, 3 * Math.PI); //0～3πまで描画するように指定する。
    A = cf.GetCurve(100); //サンプル数100で実際の曲線を作成する。
    //cf.BakeCurve(RhinoDocument, 100); //サンプル数100で実際の曲線を作成してBakeする。
```
### 関数の示す曲面
3次元上の複数の点を通るサーフェスを表現する際に便利なのは``NurbsSurface.CreateFromPoints()``関数です。この関数はPoint3dの配列からそれを通るNurbsSurfaceを作ってくれます。
厳密な意味で正しい形状が出来るわけではありませんが、それっぽい形を作るには便利です。

011.3dGraph.ghxでは簡単な例を示しています。
```
  private void RunScript(Interval x, Interval y, ref object A)
  {
    int uCount = 100;//u方向の解像度
    int vCount = 100;//v方向の解像度
    var points = new List<Point3d>();

    for(int u = 0;u < uCount;u++){
      for(int v = 0;v < vCount;v++){
        double uValue = ((double) u / (double) (uCount - 1)) * x.Length + x.Min;
        double vValue = ((double) v / (double) (vCount - 1)) * y.Length + y.Min;
        points.Add(GetPoint(uValue, vValue));
      }
    }

    A = Rhino.Geometry.NurbsSurface.CreateFromPoints(points.ToArray(), uCount, vCount, 3, 3);
  }
```
012.3dGraphClass.ghxではより実践的なサンプルを載せています。クラスやdelegateといった説明していない複雑な手法を用いています。
時間がない場合にはあまり詳しく読み込む事はお勧めしません。そのままコピーして使ってください。
```
  /// <summary>
  /// 3dグラフを示すクラスです。
  /// </summary>
  public class Graph3d{
    public delegate Point3d Function3d(double u,double v);
    /// <summary>
    /// グラフで描画する関数。
    /// </summary>
    public Function3d F;

    /// <summary>
    /// u方向の描画範囲。
    /// </summary>
    public Interval uInterval = new Interval(-1, 1);
    /// <summary>
    /// v方向の描画範囲。
    /// </summary>
    public Interval vInterval = new Interval(-1, 1);

    /// <summary>
    /// コンストラクタ。
    /// </summary>
    /// <param name="f">グラフで描画する関数。</param>
    public Graph3d(Function3d f){
      this.F = f;
    }

    /// <summary>
    /// 関数が示すグラフをサーフェスとして取得します。
    /// </summary>
    /// <param name="uCount">u方向の解像度</param>
    /// <param name="vCount">v方向の解像度。</param>
    /// <returns>目的の形状。</returns>
    public Surface GetSurface(int uCount=100, int vCount=100){
      var points = new List<Point3d>();

      for(int u = 0;u < uCount;u++){
        for(int v = 0;v < vCount;v++){
          double uValue = ((double) u / (double) (uCount - 1)) * uInterval.Length + uInterval.Min;
          double vValue = ((double) v / (double) (vCount - 1)) * vInterval.Length + vInterval.Min;
          points.Add(F(uValue, vValue));
        }
      }
      return NurbsSurface.CreateFromPoints(points, uCount, vCount, 3, 3);
    }

    /// <summary>
    /// 関数が示すサーフェスをBakeします。
    /// </summary>
    /// <param name="RhinoDocument">通常はRhinoDocumentと入力してください。</param>
    /// <param name="uCount">u方向の解像度。</param>
    /// <param name="vCount">v方向の解像度。</param>
    /// <returns></returns>
    public void BakeSurface(RhinoDoc RhinoDocument, int uCount, int vCount){
      RhinoDocument.Objects.Add(GetSurface(uCount, vCount));
    }
  }
```
この準備をしておくと簡単に3dグラフを利用することが出来ます。
```
      var Graph = new Graph3d((u,v) => {return new Point3d(u, v, u * u - v * v);});
      Graph.BakeSurface(RhinoDocument, 100, 100);
```
### 自動的にBakeする
通常grasshopperではオブジェクトを追加する場合には手作業でBakeを行わなければならずレイヤー分けも自分で行わなければなりません。
その際に便利なのは、``RhinoDocument.Objects.Add(geometry);``関数です。
この関数はRhinoDocumentというScript_Instanceクラスで使われている関数なのでその外や子クラスでは受け渡しをしないと使えませんし、場合によっては"RhinoDocument"という部分が変わります。
つまり、RunScript関数内か引数にRhinoDoc型を持つ関数内で使える関数です。

この関数の使い方はとても簡単です。たとえば、C#コンポーネントをつくり入力xの型をGeometryBaseにして、RunScriptの中身を以下のように書き換えてみてください。
```
  RhinoDocument.Objects.Add(x);
```
入力xに適当なジオメトリ、例えば球を入力すれば入力した瞬間にRhinoceros画面上にそのジオメトリが追加されます。

このやり方で注意が必要なのは入力値が変わるたび毎にジオメトリが追加されることです。
例えば球のサイズをスライダーで入力していれば一度に動かすだけでいくつも球が追加されます。
その対策として、C#コンポーネントの入力のひとつをbool型にしてそれにBooleanToggleコンポーネントをつなぐという手法がよく使われます。
面倒なら右クリックのLock Solverで再計算を無効にしておき、必要になるたびにF5で一括Bakeをするというやり方もあります。
```
  private void RunScript(GeometryBase x, bool y, ref object A)
  {
    if(y){RhinoDocument.Objects.Add(x);}
  }
```
一方で便利な点はオプションでいくつもの設定が可能な点です。それはこの関数が引数にひとつ、ふたつどちらの数の引数でももてるからです。
ふたつ持つ場合にはふたつ目の引数は``ObjectAttributes``型になります。例えば、色を設定したかったら次のようにします。
```
    var objAttr = new ObjectAttributes();
    objAttr.ColorSource = ObjectColorSource.ColorFromObject;
    objAttr.ObjectColor = Color.Red;
    RhinoDocument.Objects.Add(x, objAttr);
```
その他にレイヤーを自動設定することもできます。その場合は既にあるレイヤーの場合``RhinoDocument.Layers.FindByFullPath(レイヤー名, false);``から、まだないレイヤーの場合には``RhinoDocument.Layers.Add(レイヤー名)``によって作成しレイヤー番号を取得できます。
レイヤーがない場合には前者は-1を返すのでifを使って対処できます。
その後上の例なら``objAttr.LayerIndex``に上で得たレイヤー番号を代入してください。

015.BakeAdvanced.ghxは簡単に色とレイヤー名を指定できるようにしたものです。
```
  private void RunScript(GeometryBase obj, List<Color> color, string layer, bool bake)
  {
    try{
      var objAttr = new ObjectAttributes();
      if(color.Count() != 0){
        objAttr.ColorSource = ObjectColorSource.ColorFromObject;
        objAttr.ObjectColor = color[0];
      }
      if(layer != ""){
        objAttr.LayerIndex = GetLayerNumber(RhinoDocument, layer);
      }
      if(bake){
        RhinoDocument.Objects.Add(obj, objAttr);
      }
    }catch(Exception e){
      Print(e.ToString());
    }
  }

  // <Custom additional code> 

  public int GetLayerNumber(RhinoDoc rd, string layerName){
    int layerCount = rd.Layers.FindByFullPath(layerName, true);
    if(layerCount == -1){
      layerCount = rd.Layers.Add(layerName, Color.White);
    }
    return layerCount;
  }
```
