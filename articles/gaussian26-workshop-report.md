---
title: "Gaussian 26 講習会 レポート"
emoji: "🧪"
type: "tech"
topics: ["Gaussian", "DFT", "計算化学", "量子化学", "TS探索"]
published: false
publication_name: "poclab_techblog"
---

## はじめに

2026年2月16日〜20日に東京科学大学で開催された  
**Introduction to Gaussian: Theory and Practice** に参加しました。

この記事は次の資料を統合して書いています。

1. 公式ホームページ（募集要項・講習設計）
2. 日程表（タイムテーブル）
3. 当日メモ

## 講習会資料から分かる運営情報

公式情報と現地情報を合わせると、講習会の設計意図がかなり明確でした。

- 共催: Gaussian, Inc. + 東京科学大学
- 講義: 英語
- 毎日ハンズオン演習あり（参加者ごとにワークステーション提供）
- 可視化は GaussView を使用
- 配布物: 講義ノート + `Exploring Chemistry with Electronic Structure Methods (3rd ed.)`
- 参加費（公式PDF）: Faculty/Staff `$600`、Commercial/US Government `$800`、Student/Postdoc `$250`
- 登録締切: 2026年1月16日（先着順）

ポスターセッションは、進行中テーマの共有も歓迎される設計になっており、  
講師・参加者間での技術的な対話を促進する構成でした。

---

## 5日間の時系列サマリ

| Day | 公式テーマ | 現地での要点 |
| --- | --- | --- |
| Day1 | 研究設計, PES, 最適化, 振動, Job Files I | 入力準備と初期構造づくりの効率化 |
| Day2 | 電子相関, DFT, Orbital解析, Utilities, Poster | 可視化と説明力が主題 |
| Day3 | TS最適化, Reaction Path, Model Chemistry, NMR | TS/IRCの検証をどこまでやるか |
| Day4 | SCF安定化, Job Files II, Solvation, ONIOM, Chiroptical | 溶媒と多層モデルの使い分け |
| Day5 | Resources, CASSCF, MD, Q&A | 計算資源設計の実務化 |

以下、日ごとに「全体像」を押さえ、その後に3テーマを深掘りします。

---

## Day1 (2/16): 入力と前処理の改善が体感できた日

日程表では午前に研究設計とPES、午後に基底関数とJob Files I、そして演習でした。  
実際に触って分かりやすかったのは次です。

- SMILES/化合物名からの構造取得（PubChem, RCSB PDB）
- GUIでの2D作図から3D化
- RDKitベースの初期構造生成（MMFF/UFF）
- `run testrt` の事前チェック
- 複数gjfの連続実行

受講メモでは「g16にORCA + RDKitの機能を足した印象」と整理しました。  
日常の入力準備工程が短縮される点は、実務上の効果が大きいと感じます。

---

## Day2 (2/17): 可視化とポスター議論の日

午前は電子相関・DFT・軌道解析、午後はUtilitiesとPoster Session、その後に演習。  
この日は「計算を回す」より「結果をどう読んで伝えるか」が中心でした。

ポスターセッションが組み込まれていたことで、  
計算条件の選定理由や解釈の妥当性を口頭で検証できる機会が多くありました。

### 深掘り1: cube/cubeman解析で何が変わるか

メモとスライドで一致していた改善点は次の3つです。

- 表面積・体積の算出
- isovalue保存（`.surf` と可読形式）
- 勾配ベクトル場の表示

ここで重要だったのは「解析結果の再現性」です。  
isovalueを保存しておけるため、後日比較や共同研究で「同じ表示条件」を再現しやすくなります。

実際にやってみて気づいた点:

- ラジカルアニオンと基底状態の差電子密度で、直感と違う変化が見える
- フロンティア軌道だけでは説明しづらいケースがある
- 高度解析は依然としてMultiwfn等が強いが、Gaussian内で完結できる範囲は確実に増えた

---

## Day3 (2/18): TS探索とIRCの中核日

公式日程でも、朝からTS最適化とReaction Path Followingが続く「反応解析日」でした。  
午後はUV/Optical、NMR、招待講演という流れです。

### 深掘り2: TS探索は「Opt成功」で終わらない

メモで強く残ったのは、TSと平衡構造の最適化の難しさの差です。

- Berny法ではHessian情報の扱いが収束性を左右
- `ReadFC`（安価計算からの力定数流用）は実務的に有効
- `RecalcFC=N` は収束が怪しいときの打ち手
- QST2/QST3は初期構造の質で成否が大きく変わる

さらにIRCでは手法差が明確です。

- Euler法: 安価だが誤差蓄積しやすい
- Hessian-based predictor-corrector: 高価だが接続確認が安定

この講習会の文脈での実務ルールは次に落ち着きました。

1. `Opt(TS)` だけで完了にしない
2. `Freq` で虚振動1本を確認する
3. `IRC` 前後で反応物/生成物に接続するまで確認する

「TSが取れた」ではなく「反応経路が繋がった」までを完了条件にする、ということです。

---

## Day4 (2/19): 溶媒モデルと手法アップデートの整理

日程は `SCF stability -> Job Files II -> Solvation -> ONIOM`、午後にキロプティカルでした。  
演習はTD-DFTの垂直遷移が中心でした。

メモとスライドで共通していた要点:

- IEFPCMは扱いやすく収束性が高い
- SMDは考慮する寄与が広い
- ONIOMは有効だがレイヤー分割にコツが要る

この日出た手法キーワード（講習メモベース）:

- M11系（メモ上では M11plus）
- APFD
- `EmpiricalDispersion=D4`
- def2 + diffuse
- 3c（r2SCAN-3c, HF-3c）

ここは「既報再現条件を残しながら更新」するのが安全という結論でした。

---

## Day5 (2/20): 計算資源とQ&A

公式日程では `Resources & Efficiency`, `CASSCF`, `MD`, `Summary`, `Q&A`。  
最終日は理論そのものより、投入戦略の話が実務に直結しました。

### 深掘り3: メモリ・ディスクの具体値（講習会メモ由来）

この講習会で得た、かなり実務的な目安です。

- 小規模DFT（10原子程度）: 100MW（約800MB）でも動く
- 50原子・500基底超: 4GB/core程度を検討
- MP2/CCSD等post-SCF: メモリ要求が急増
- `Never use 100% installed memory`
- post-SCFはSSD推奨

一般論ではなく、この場で強調されていたのは  
「理論レベルを上げる前に、資源割り当て設計を先に決める」ことでした。

## 5日分を運用に落とした手順

講習内容を実運用へ落とす場合、以下の順が有効でした。

1. Day1の流れで入力準備を高速化（SMILES/GUI/RDKit）
2. Day2の流れで可視化条件を固定化（isovalue保存）
3. Day3の流れで TS = `Opt + Freq + IRC` を標準化
4. Day4の流れで溶媒・ONIOMを必要最小限で導入
5. Day5の流れで `%mem` とスクラッチ容量を先に設計

この順番にしてから、失敗ジョブの再実行回数が減りました。

---

## まとめ

この講習会の価値は、新機能一覧よりも  
**「理論講義 -> ハンズオン -> 議論 -> 運用設計」まで一体で体験できること** にありました。

特に本記事で深掘りした3点（cube解析、TS/IRC、資源設計）は、  
今すぐ実験計画・計算計画に反映できる内容です。

次回は、講習会で得た条件候補を使って、実データ比較（条件差で何が変わるか）まで書く予定です。

---

## 参考文献・リンク

- Gaussian workshop page (PDF化元): https://gaussian.com/jwshop26/
- [1] Weigend, Ahlrichs, def2 basis sets, PCCP (2005), DOI: https://doi.org/10.1039/B508541A
- [2] Grimme et al., r2SCAN-3c, J. Chem. Phys. (2021), DOI: https://doi.org/10.1063/5.0040021
- [3] Peng et al., STQN optimization, J. Comput. Chem. (1996), DOI: https://doi.org/10.1002/(SICI)1096-987X(19960115)17:1%3C49::AID-JCC5%3E3.0.CO;2-0
- [4] Gonzalez, Schlegel, IRC, J. Phys. Chem. (1990), DOI: https://doi.org/10.1021/j100377a021
- [5] Hratchian, Schlegel, Hessian-based IRC, J. Chem. Phys. (2004), DOI: https://doi.org/10.1063/1.1724823
- [6] Marenich, Cramer, Truhlar, SMD model, J. Phys. Chem. B (2009), DOI: https://doi.org/10.1021/jp810292n
- [7] Austin et al., APF-D functional, JCTC (2012), DOI: https://doi.org/10.1021/ct300778e
- [8] Caldeweyher et al., D4 dispersion correction, J. Chem. Phys. (2019), DOI: https://doi.org/10.1063/1.5090222
