---
class: text-center
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: fade-out
title: oxidedb
mdc: true
fonts:
  sans: "HiraMaruProN-W4"
---

<h1>データベースの仕組みを</h1>
<h1>独自実装して理解してみよう</h1>

<br>
<br>

<h2 class="name">Yoshisaur</h2>

---
transition: fade-out
fonts:
  sans: HiraMaruProN-W4
---

# 自己紹介

<br>
<br>

<div grid="~ cols-2 gap-4">
  <div>
    <h2>名前: Yoshisaur(ヨシザウルス)</h2>
    <br>
    <h2>大学: 琉球大学工学部 B4</h2>
    <br>
    <h2>学科: 知能情報コース</h2>
    <br>
    <h2>研究室: 並列信頼研</h2>
    <br>
    <h2>X(Twitter): @ie_Yoshisaur</h2>
    <br>
    <h2>趣味/特技: 英話, 将棋</h2>
  </div>
  <div>
  <img src="/images/Yoshisaur.jpg" width="300" height="300" style="margin-left: 50px;">
  </div>
</div>

<!--
15秒
-->

---
transition: fade-out
fonts:
  sans: HiraMaruProN-W4
---

# 研究室紹介

<br>
<br>
<br>

<div grid="~ cols-2 gap-4">
  <div>
    <h2>名前: 並列信頼研</h2>
    <br>
    <br>
    <h2>研究分野: 低レイヤー</h2>
    <br>
    <br>
    <h3>  ・独自言語 -> Continuation based C</h3>
    <br>
    <h3>  ・独自OS   -> GearsOS</h3>
  </div>
  <div>
  <img src="/images/611.png">
  </div>
</div>

<!--
15秒
-->

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# 発表の目的

<div>
  <br>
  <br>
  <br>
  <h2>・データベースの内部実装の知識の共有</h2>
  <br>
  <br>
  <br>
  <h2>・データベースを自作して得られた経験の共有</h2>
  <br>
  <br>
  <br>
  <h2>・チャレンジの題材としてのDB自作を布教</h2>
</div>

---
transition: none
fonts:
  sans: "HiraMaruProN-W4"
---

# データベースを自作しようとしたきっかけ

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <h2>・イノシシ本を理解したかった</h2>
    <br>
    <h2>・データの量や複雑さ、変化が課題となるアプリケーションを信頼性、拡張性、保守性を守りながらデザインするための技術書</h2>
    <br>
    <h2>・600ページ以上あり、内容も高度で当時の自分では理解ができなかった</h2>
  </div>
  <div>
    <img src="https://www.oreilly.co.jp/books/images/picture_large978-4-87311-870-3.jpeg" width="300" height="300" style="margin-left: 65px;">
    <h4 style="margin-left: 50px; margin-top: 20px;">データ指向アプリケーションデザイン</h4>
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# データベースを自作しようとしたきっかけ

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <h2>・イノシシ本の内容にデータベース内部の実装の話が多く載せられていた</h2>
    <br>
    <h3>&nbsp;&nbsp;・2章　データモデルとクエリ言語</h3>
    <h3>&nbsp;&nbsp;・3章　ストレージと抽出</h3>
    <h3>&nbsp;&nbsp;・7章　トランザクション</h3>
    <br>
    <h2>・「自作DBを経験すれば、イノシシ本を読めるようになる!」と思い、自作DBを始めた</h2>
  </div>
  <div>
    <img src="https://www.oreilly.co.jp/books/images/picture_large978-4-87311-870-3.jpeg" width="300" height="300" style="margin-left: 65px;">
    <h4 style="margin-left: 50px; margin-top: 20px;">データ指向アプリケーションデザイン</h4>
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# 自作DBにおける目標

<div>
  <br>
  <h2>・Rustを使う</h2>
  <br>
  <h2>・並行処理を含むトランザクション</h2>
  <br>
  <h2>・SQL対応</h2>
  <br>
  <h2>・1ヶ月で0から完成させる</h2>
  <br>
  <h2>・コードを大量に書く経験を得る</h2>
  <br>
  <h2>・自分だけのクールな名前のソフトを作る → OxideDB</h2>
  <br>
</div>


---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# 参考にするDB

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <br>
    <h2>・Database Design and Implementationという本でJavaで実装されているSimpleDBを参考にする</h2>
    <br>
    <br>
    <h2>・Rust特有の言語ルールに適応させて再実装</h2>
  </div>
  <div>
    <img src="https://media.springernature.com/full/springer-static/cover-hires/book/978-3-030-33836-7?as=webp" width="250" height="200" style="margin-left: 65px;">
  </div>
</div>

---
transition: fade-out
fonts:
  sans: HiraMaruProN-W4
---

# 設計方針

<div>
  <br>
  <br>
  <h2>・扱える属性が4バイトのIntegerと可変長のVarchar(ASCII)</h2>
  <br>
  <br>
  <h2>・ロックの単位はブロック(後に解説)</h2>
  <br>
  <br>
  <h2>・SQLを処理する</h2>
  <br>
  <br>
  <h2>・リカバリー方式はundo(後に解説)</h2>
</div>

<!--

-->

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# DB実装時に直面した問題点

<div>
  <br>
  <br>
  <h2>・参考にしたSimpleDBでは循環参照を含む実装だったが、Rustでそれを実現しようとすると参照カウンタが機能しなくなり、メモリーリークが起きる</h2>
  <br>
  <br>
  <h2>・可変シングルトンがRustで実装できない</h2>
  <br>
  <br>
  <h2>・デッドロック検出器をせずにデバッグに苦労した</h2>
  <h1 v-click class="absolute bottom-10 right-45 transform">あとで解説します</h1>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# DB実装のモジュール構成

<div>
  <br>
  <h2>・file - Diskにブロック単位で読み書き</h2>
  <h2>・buffer - ブロックのバッファの管理</h2>
  <h2>・log - トランザクションのログの読み書き</h2>
  <h2>・transaction - リカバリーや並行性制御</h2>
  <h2>・metadata - テーブルのスキーマやインデックスの情報の管理</h2>
  <h2>・record - ディスク上のレコードデータの位置の管理</h2>
  <h2>・query - 関係代数クエリの実行</h2>
  <h2>・parse -  SQLの解析</h2>
  <h2>・plan - parseで解析したデータを関係代数クエリツリーに変換</h2>
  <h2>・index - BTreeを用いたレコードの特定</h2>
  <h2>・optimizer - 関係代数クエリを最適化する</h2>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# 解説するDB実装の詳細

<div>
  <br>
  <br>
  <br>
  <h2>・file</h2>
  <br>
  <br>
  <h2>・buffer</h2>
  <br>
  <br>
  <h2>・log</h2>
  <br>
  <br>
  <h2>・transaction</h2>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# file

<div>
  <br>
  <br>
  <br>
  <h2>・DBのとして最低限求められる機能</h2>
  <br>
  <br>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・ディスクにデータをwriteする</h3>
  <br>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・ディスクからデータをreadする</h3>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# file

<br>

## 整数のwrite/read

<div grid="~ cols-2 gap-4">
  <div>
    <br>
```rs{all|2|4-6|7|all|11|12-15}
pub fn get_int(&mut self, offset: usize) -> Result<i32, PageError> {
    self.byte_buffer.set_position(offset as u64);
    let mut bytes = [0; I32_SIZE];
    self.byte_buffer
        .read_exact(&mut bytes)
        .map_err(PageError::IoError)?;
    Ok(i32::from_le_bytes(bytes))
}

pub fn set_int(&mut self, offset: usize, value: i32) -> Result<(), PageError> {
    self.byte_buffer.set_position(offset as u64);
    self.byte_buffer
        .write_all(&value.to_le_bytes())
        .map_err(PageError::IoError)
}

```
  </div>
  <div>
    <br>
    <br>
    <br>
    <img src="/images/file_int.svg" width="500" height="500">
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# file

<br>

## 可変長文字列のread/write

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <br>
    <br>
    <h2>可変長なデータは、</h2>
    <h2>最初のoffsetにデータの</h2>
    <h2>バイト長を整数で書き込む</h2>
  </div>
  <div>
    <br>
    <img src="/images/file_str.svg" width="500" height="500">
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# file

<br>

## 「ブロック」について

<div>
  <br>
  <br>
  <h2>・整数や可変長文字列がオフセットの位置に制約なく書き込むようにしたら実装が難しい</h2>
  <br>
  <h2>・ディスクファイル内を等間隔で区分けしたほうが実装が楽</h2>
  <br>
  <h2>・ディスク上の区分けの単位を「ブロック」という</h2>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・ブロックのサイズはしばしば4096 bytes</h3>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# file

<br>

## なぜブロックサイズが4096 bytesか?

<div>
  <br>
  <br>
  <h2>・OSのファイルシステムでも「ブロック」が存在</h2>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・例えばLinuxのファイルシステム ext4も4096 bytesのブロック</h3>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・4096 bytesからずれると、読み書き時にオーバーヘッドが生じる</h3>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・DBのブロックが4097 bytesのサイズだと、</h3>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ファイルシステム側で8192 bytesを読み書きすることになる</h3>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# buffer

<div>
  <br>
  <br>
  <h2>・「file」でディスクに対して整数と文字列を読み書きできるようになった! Happy!</h2>
  <br>
  <h3 v-click>&nbsp;&nbsp;&nbsp;&nbsp;・とはならない</h3>
  <h3 v-click>&nbsp;&nbsp;&nbsp;&nbsp;・Disk I/Oは遅すぎる</h3>
  <h3 v-click>&nbsp;&nbsp;&nbsp;&nbsp;・HDDは大半が7200rpm(毎秒120回転)、データの読み書きが最大120回</h3>
  <br>
  <br>
  <h2>・メモリー上にブロックのバッファを作ることで読み書きを高速化する</h2>
  <br>
</div>

---
transition: fade-out
fonts:
  sans: "hiramarupron-w4"
---

# buffer

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <br>
    <br>
    <h2>・バッファを経由してデータの読み書きを行う</h2>
    <br>
    <h2>・ユーザーからはデータの読み書きが高速化したように見える</h2>
    <br>
    <h2>・happy!</h2>
    <br>
    <h3 v-click>&nbsp;&nbsp;&nbsp;&nbsp;・とはならない</h3>
  </div>
  <div>
    <div>
        <img src="/images/buffer.svg" width="500" height="500" style="margin-top: 50px;">
    </div>
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "hiramarupron-w4"
---

# buffer

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <h2>・複数のスレッドがバッファにアクセスして読み書きができる場合は、データの整合性がカオスになる</h2>
    <br>
    <h2>・実装的には単一のブロックに対して2つ以上のバッファが割り振られることも可能</h2>
    <br>
    <h2>・「transaction」がこの問題を解決する(後に解説)</h2>
    <br>
  </div>
  <div>
    <div>
        <img src="/images/buffer_multi_user.svg" width="500" height="500" style="margin-top: 50px;">
    </div>
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# log

<div>
  <br>
  <br>
  <h2>・ログファイルにログ(レコード)を読み書きする</h2>
  <br>
  <h2>・DBがクラッシュした場合のリカバリーとトランザクションのロールバックに使われる</h2>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・ロールバックはトランザクションがデータ操作を永続化(コミットという)</h3>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;しないで、キャンセルすること</h3>
  <br>
  <h2>・リカバリーする際は、最新のログレコードから</h2>
  <h2>&nbsp;&nbsp;&nbsp;読み込んでundoするので、ファイル内のログレコードを</h2>
  <h2>&nbsp;&nbsp;&nbsp;逆順に回すイテレータも実装している</h2>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# log

## logイテレータの仕組み

<div>
  <br>
  <h2>ブロック内の先頭側のブロックは新しいレコードを持つ</h2>
  <br>
  <img src="/images/log.svg" width="700" height="400" style="margin-left: 100px;">
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# log

## logイテレータの仕組み

<div>
  <br>
  <h2>ファイルの先頭側のブロックは古いレコードを持つ</h2>
  <br>
  <img src="/images/log_greater_scale.svg" width="800" height="500" style="margin-left: 50px;">
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction

<div>
  <br>
  <br>
  <h2>transactionの役割が2つある</h2>
  <br>
  <br>
  <h2>・リカバリー</h2>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・DBのクラッシュ時やトランザクションのロールバックを行う際に中途半端なデータの変更を取り消してデータの整合性を保つ</h3>
  <br>
  <h2>・並行性制御</h2>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;・同時に同一のデータに複数の読み書きが行われても矛盾が起きない順序でデータを操作してデータの整合静を保つ</h3>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - recovery

<div>
  <br>
  <br>
  <h2>transactionはリカバリーのためにログレコードを書く</h2>
  <br>
  <br>
  <h2>・transactionがDB(disk file)に何らかの操作を加えるときにログレコードをログファイルに書き込む</h2>
  <br>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;&lt;START&gt;, &lt;COMMIT&gt;, &lt;ROLLBACK&gt;, &lt;CHECKPOINT&gt;,</h3>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;&lt;SETINT transaction_id block_id offset value&gt;,</h3>
  <h3>&nbsp;&nbsp;&nbsp;&nbsp;&lt;SETSTRING transaction_id block_id offset value&gt;,</h3>
  <br>
  <h2>・リカバリーでは&lt;CHECKPOINT&gt;にたどり着くまで最新のレコードログから辿ってデータの変更の操作をundoし続ける</h2>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - recovery

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <h2>クラッシュ時のリカバリーのシュミレーション</h2>
    <br>
    <h2>クラッシュ時のリカバリーでは、ディスクに永続化されたコミットされていないデータ操作をundoする</h2>
    <br>
    <h2>CHECKPOINT前にCOMMITしていないトランザクションはundoされる</h2>
  </div>
  <div>
    <br>
```txt{all|12|9,13|10,11,14,15}
1. START 1
2. SETINT 1 1 10 1234
3. SETSTRING 1 2 20 "Hello"
4. COMMT 1
5. START 2
6. SETINT 2 3 30 5678
7. SETSTRING 2 4 40 "World"
8. ROLLBACK 2
9. START 3
10. SETINT 3 5 50 91011
11. SETSTRING 3 6 60 "Database"
12. CHECKPOINT
13. START 4
14. SETINT 4 7 70 1213
15. SETSTRING 4 8 80 "Recovery"

```
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - recovery

<br>
    
## Rustで実装するときの注意点 - 循環参照

<br>

### SimpleDBの実装ではtransactionとrecoveryを行うRecoveryMgrがお互いに参照を持っている

<h3 v-click> -> Rustだとアンチパターン、RustはGCがない代わりに参照カウンタが0になったら解放する仕組みで、循環参照を実装すると永遠に解放されないままメモリリークする</h3>

<div grid="~ cols-2 gap-4">
  <div>
```java{all|4}
public class Transaction {
   private static int nextTxNum = 0;
   private static final int END_OF_FILE = -1;
   private RecoveryMgr    recoveryMgr;
   private ConcurrencyMgr concurMgr;
   private BufferMgr bm;
   private FileMgr fm;
   private int txnum;
   private BufferList mybuffers;
}
```
  </div>
  <div>
    <br>
```java{all|4}
public class RecoveryMgr {
   private LogMgr lm;
   private BufferMgr bm;
   private Transaction tx;
   private int txnum;
}
```
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - recovery

<br>
    
## Rustの実装

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
```rs{all|8}
pub struct Transaction {
    file_manager: Arc<Mutex<FileManager>>,
    buffer_manager: Arc<Mutex<BufferManager>>,
    log_manager: Arc<Mutex<LogManager>>,
    transaction_number: i32,
    concurrency_manager: Arc<Mutex<ConcurrencyManager>>,
    buffer_list: Arc<Mutex<BufferList>>,
    recovery_manager: Arc<Mutex<RecoveryManager>>,
}
```
  </div>
  <div>
    <br>
```rs{all|1-5|7,11}
pub struct RecoveryManager {
    log_manager: Arc<Mutex<LogManager>>,
    buffer_manager: Arc<Mutex<BufferManager>>,
    transaction_number: i32,
}

pub fn rollback(&self, transaction: &mut Transaction) -> Result<(), RecoveryError> {
    //省略
}

pub fn recover(&self, transaction: &mut Transaction) -> Result<(), RecoveryError> {
    //省略
}
```
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - concurrency

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <h2>・実装的には単一のブロックに対して2つ以上のバッファが割り振られることも可能</h2>
    <br>
    <h2>・書き込んだデータに矛盾が生じてしまう</h2>
    <br>
    <h2>・矛盾したデータを保存するDBに意味はなくなる</h2>
    <br>
  </div>
  <div>
    <div>
        <img src="/images/buffer_multi_user.svg" width="500" height="500" style="margin-top: 50px;">
    </div>
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - concurrency

<br>

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <h2>・データが壊れるシナリオ</h2>
    <br>
    <h2>・データに対して、2人以上の人がデータを読んでる状態で書き込みを行う</h2>
    <br>
    <h2>・xは2人によってインクリメントされ2になるはずだが、読み込みと書き込みの順序によってはこのような矛盾が生じる</h2>
    <br>
  </div>
  <div>
    <div>
        <img src="/images/race_condition.svg" width="500" height="500" style="margin-top: 50px;">
    </div>
  </div>
</div>

---
transition: none
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - concurrency

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <br>
    <br>
    <h2>・lock_tableがブロックにかかっているロックの数を管理</h2>
    <br>
    <br>
    <h2>・locksがブロックにかかっているロックの種類を管理</h2>
    <br>
    <br>
  </div>
  <div>
    <br>
    <br>
    <br>
    <img src="/images/lock_table.svg" width="300" height="300" style="margin-left: 50px;">
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - concurrency

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <br>
    <br>
    <h2>・lock_tableがブロックにかかっているロックの数を管理</h2>
    <br>
    <br>
    <h2>・locksがブロックにかかっているロックの種類を管理</h2>
    <br>
    <br>
  </div>
  <div>
    <br>
    <br>
    <br>
```rs{all|7,8}
enum LockType {
    Shared,
    Exclusive,
}

pub struct ConcurrencyManager {
    lock_table: Arc<Mutex<LockTable>>,
    locks: HashMap<BlockId, LockType>,
}

pub struct LockTable {
    locks: HashMap<BlockId, i32>,
}
```
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - concurrency

## Rustで実装するときの注意点 - 可変シングルトン

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <h3> ・以下のjavaの実装はシングルトンである、しかも変更も加えられるので可変シングルトン</h3>
    <br>
    <h3> ・ConcurrencyMgrはTransactionがメンバーとして持つ</h3>
    <br>
```rs{all|2}
public class ConcurrencyMgr {
   private static LockTable locktbl = new LockTable();
   private Map<BlockId,String> locks  = new HashMap<BlockId,String>();
}
```
  </div>
  <div>
    <br>
    <br>
    <img src="/images/singleton.svg" width="500" height="500">
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# transaction - concurrency

## Rustで実装するときの注意点 - 可変シングルトン

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <br>
    <h3> ・Rustでは可変シングルトンの実装はunsafeを使わないと実装ができない</h3>
    <br>
    <br>
    <h3> ・ConcurrencyMgrはTransactionがメンバーとして持つ</h3>
    <br>
    <br>
    <h3> ・Rustでは可読性が落ちるが、transactionを生成するDB自体がlock tableを持つようにしている</h3>
  </div>
  <div>
    <br>
    <br>
```rs{all|6|11-18|16}
pub struct OxideDB {
    block_size: usize,
    file_manager: Arc<Mutex<FileManager>>,
    log_manager: Arc<Mutex<LogManager>>,
    buffer_manager: Arc<Mutex<BufferManager>>,
    lock_table: Arc<Mutex<LockTable>>,
    metadata_manager: Option<Arc<MetadataManager>>,
    planner: Option<Arc<Mutex<Planner>>>,
}

pub fn new_transaction(&self) -> Transaction {
    Transaction::new(
        self.file_manager.clone(),
        self.log_manager.clone(),
        self.buffer_manager.clone(),
        self.lock_table.clone(),
    )
}
```
  </div>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# DB実装の成果

<br>

## 自作結果/成果

<div>
  <br>
  <h2>・ソースコードの行数が14,000行を超えた(人生最高)</h2>
  <h3>&nbsp;&nbsp;・テストが3000行ほど</h3>
  <br>
  <h2>・SimpleDBでも実装されているユニットテストと同じテストをOxideDBに90%以上実装、すべてPASS(25個)</h2>
  <br>
  <h2>・対話式コンソールでSQLが実行できるようになった</h2>
  <br>
  <h2>・Rust完全に理解した</h2>
</div>

---
transition: fade-out
fonts:
  sans: HiraMaruProN-W4
---

# OxideDBのデモ

<!--
hoge
-->

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# OxideDBの実装課題

<div>
  <br>
  <br>
  <br>
  <h2>・インターフェースがシングルスレッドなので折角のtransactionの実装が活かされていない</h2>
  <br>
  <h2>・テストコードの量が足りない、実装と同じくらいコード量が必要</h2>
  <h3>&nbsp;&nbsp;・あと8000行ほど</h3>
  <br>
  <h2>・デッドロック検知機を実装していない</h2>
  <h3>&nbsp;&nbsp;・transactionの実装のデッドロック関連のデバッグで1週間溶かした</h3>
</div>
---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---

# まとめ

<div>
  <br>
  <br>
  <h2>・SQLを実行できるところまで実装できた</h2>
  <br>
  <br>
  <br>
  <h2>・1万以上のRustのプログラミング経験を得た</h2>
  <br>
  <br>
  <br>
  <h2 v-click>・理解したかったイノシシ本、今でも普通に難しい</h2>
</div>

---
transition: fade-out
fonts:
  sans: "HiraMaruProN-W4"
---


# 将来の構想

<div>
  <br>
  <br>
  <br>
  <h2>・Rustのモデル検査機</h2>
  <br>
  <br>
  <br>
  <h2>・Rustのデッドロック検査機が欲しい</h2>
  <br>
  <br>
  <br>
  <h2>・Continuation based Rust</h2>
</div>

---
fonts:
  sans: "HiraMaruProN-W4"
---


# ご清聴ありがとうございました

<div grid="~ cols-2 gap-4">
  <div>
    <br>
    <h3> OxideDB</h3>
    <br>
    <img src="/images/OxideDB.png" width="300" height="300" style="margin-left: 50px;">
    <br>
  </div>
  <div>
    <br>
    <h3> 発表資料/マインドマップ</h3>
    <br>
    <img src="/images/presentation.png" width="300" height="300" style="margin-left: 50px;">
    <br>
  </div>
</div>
