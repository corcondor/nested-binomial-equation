# コードの閲覧と再現

このリポジトリの `research/` に、v1〜v8のPython、C++、Leanのソース、証明ノート、実行記録を直接配置しています。全体の解の分類は未完了で、具体的な解も未発見です。有限検算の成功を、元の四変数方程式全体の証明と混同しないでください。

## 最初に動かす小さい検算

Python 3.10以降を推奨します。以下のv7・v8スクリプトは標準ライブラリだけで動きます。

```sh
git clone https://github.com/corcondor/nested-binomial-equation.git
cd nested-binomial-equation/research

# v7の固定オフセットの厳密な定数・算術チェック
python3 v7_fixed_offset_checks.py

# v8の全2,903件の証明書の再生成と独立検証
python3 v8_shifted_prime_power_completion_checks.py

# 同じ有限領域を証明書を読まずに再構成する別の検算
python3 v8_composite_shifted_completion_audit.py

# 別途扱った底3の枝
python3 v8_shifted_base_three_checks.py
```

スクリプトは自分と同じディレクトリにJSON結果や証明書を書き出すものがあります。配布時のファイルを残したい場合は、チェックアウトのコピーで実行してください。生成される記録の時刻や実行パスは環境によって変わります。実行に成功すると `PASS` を表示します。

その他のv8の構造検算：

```sh
python3 v8_composite_dominant_prime_powers_checks.py
python3 v8_cyclotomic_even_exponents_checks.py
```

これらは証明ノートに記された主張の範囲を対象とします。解析的な有限還元の全体をPythonが証明するという意味ではありません。

## Lean

Lean 4.35.0と、各 `lakefile.toml` に固定されたmathlibのコミットを使用します。標準のelan/Lake環境が必要です。

- [v4：Divisibility.leanと実行手順](research/v4_formal/README.md)
- [v5：OffsetThree.leanと実行手順](research/v5_formal/README.md)
- [v6：CommonDivisor.leanと実行手順](research/v6_formal/README.md)
- [v8：CubeCertificates.leanと実行手順](research/v8_formal/README.md)

v8の例（リポジトリ直下から）：

```sh
cd research/v8_formal
python3 generate_certificate_source.py
lake update
lake exe cache get
lake env lean CubeCertificates.lean
```

生成器は凍結したJSON証明書のSHA-256を照合してからLeanソースを生成します。保存済みの成功ログ・終了コード・ソースハッシュは各フォルダの `kernel_check.log` と `kernel_check.json` にあります。今回のGitHubへのソース配置は、Leanを再実行したという主張ではありません。形式化済みの範囲と未形式化の解析・付値の議論は各READMEを参照してください。

## 前段階のPython・C++・SMT

- [v1〜v7の再計算案内](research/README.md#再計算)
- [初期の日本語案内](research/README_ja.md)
- [C++のj=1側の証明書と検証方法](research/v4_upper_complement_two_j1_certification.md)
- [C++のell=1側の証明書と検証方法](research/v4_upper_complement_two_l1_certification.md)
- [厳密区間演算の説明](research/v4_upper_interval_verifier.md)

大半のPythonコードは標準ライブラリのみです。初期の `modular_reduction_experiment.py` と `hensel_upper_experiment.py` はNumPy、`v4_valuation_synthesis.py` は `z3-solver` を使用します。必要なものだけ仮想環境へインストールしてください。

```sh
python3 -m venv .venv
. .venv/bin/activate
python3 -m pip install numpy z3-solver
```

C++はGCC/Clangの `__int128` を使用するコードです。コンパイル方法、引数、実行範囲は対応する証明書ノートを参照してください。大きい有限領域の再生成には時間とディスク容量が必要です。

## 大きい生成済み証明書16件

約203 MBのJSONやバイナリ出力など、合計16ファイルはGitの通常のファイル一覧には置かず、既存の [v1〜v7完全版Release ZIP](https://github.com/corcondor/nested-binomial-equation/releases/download/v1-v7/nested_binomial_v1_v7_complete.zip) にすべて収録しています。

それらを使う検証器を実行する場合は、完全版ZIPを別の場所へ展開し、`research/` の必要な証明書をこのリポジトリの `research/` にコピーしてください。名前、サイズ、ハッシュは [SOURCE_MANIFEST.json](SOURCE_MANIFEST.json) の `omitted_generated_files` と [ARCHIVE_MANIFEST.json](research/ARCHIVE_MANIFEST.json) にあります。各生成器からの再生成も可能です。v8の2,903件の証明書はリポジトリに含まれています。

## 配布元との照合

[SOURCE_MANIFEST.json](SOURCE_MANIFEST.json) は、公開元の2つのRelease ZIPのSHA-256、直接配置した194ファイルの現行SHA-256、元アーカイブ内のSHA-256を記録します。証明本文、プログラム、証明書、Leanソース、カーネルログは公開ZIPと同一です。4つの実行メタデータJSONだけは、実行環境の絶対パスを例示パスへ置換し、セッション識別子を除きました。

`research/` 内の版別manifestは当時のアーカイブの記録として保持しています。そのため、この4つのメタデータに関する現行ハッシュはルートの `SOURCE_MANIFEST.json` を参照してください。
