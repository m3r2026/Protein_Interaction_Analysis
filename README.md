# Protein Interaction & Conservation Analysis Pipeline

## 概要
PDB構造データからタンパク質-DNA（またはタンパク質間）、およびタンパク質-低分子リガンドの相互作用界面を特定し、進化的な保存度（Conservation Score）と統合して解析する自動化パイプラインである。

## 主な機能
- **構造解析**: SASA（溶媒接近可能表面積）を用いたBSA（埋没表面積）の計算。
- **相互作用判定**: 原子間距離に基づく水素結合、疎水性相互作用等の自動分類。
- **リガンド界面解析**: タンパク質と低分子リガンド（HETATM）の接触残基を解析する（`analyze_ligand_interface`）。手元のPDBファイルや、ドッキングで予測したポーズも入力できる。
- **進化解析**: BLAST検索とMAFFTを用いた多重配列整列（MSA）の実行、およびシャノン・エントロピーによる保存度算出。
- **可視化準備**: 保存度スコアをPDBのB-factor列に書き込み、PyMOLでの色分け表示を可能にする。

## 解析例（ノートブック内で実行）
- **BRD4 BD1とJQ1（PDB 3MXF）**: 阻害剤JQ1の接触残基を解析し、[BRD4結合親和性予測プロジェクト](https://github.com/m3r2026/brd4-binding-affinity-prediction)で用いたBD1ドメインの残基範囲（44–168）の妥当性を確認した（セクション7）。
- **DiffDockのドッキング結果の解析**: 同プロジェクトで、DiffDockが予測した4化合物のポーズを構造に結合し、接触残基・BSA・JQ1からの重心のずれを比較した（セクション9）。入力ファイルは `scripts/brd4_diffdock/` にある。

## 動作環境
- **OS**: Windows 10/11 (MAFFTの呼び出しに `.bat` を使用するため)
  ※今後、Windows以外にも対応できるよう更新する予定である。
- **Language**: Python 3.x
- **Dependencies**: Biopython, Pandas, NumPy, RDKit（セクション9のみ）

## 使用方法
1. 本リポジトリをクローンまたはダウンロードする。
2. MAFFT公式サイトからWindows版をダウンロードする。
3. bin/mafft-win/ フォルダを新規作成し、解凍したMAFFTの中身をすべて配置する（`bin/mafft-win/` は `.gitignore` で除外している）。
4. `targets` リストに解析したいPDB IDとチェーンを指定する。
5. `scripts/` 内のノートブックを実行すると、最終的な解析結果がCSVおよび可視化用PDBとして出力される。

## 注意事項
- 本プログラムは残基番号が連続している構造を想定している。
- 外部データベース（RCSB PDB、NCBI）へのアクセスを含むため、実行にはインターネット環境が必要である。
- NCBIのBLAST検索は、サーバーの混雑状況によって数時間かかることがある。結果（`blast_*.xml`）がすでにあれば検索をスキップするため、一度取得した結果は保存しておくとよい。
- Windowsで証明書エラー（`[ASN1: NOT_ENOUGH_DATA]`）が出る場合は、ノートブック冒頭の回避用セルを実行する。
