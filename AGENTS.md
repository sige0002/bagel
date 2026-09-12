# rosbag2lerobot

ROS 2 rosbag を LeRobot Dataset v3 に変換する純 Python CLI。ROS のインストールは不要。使い方とコマンドは [CATALOG.md](CATALOG.md)、詳細の入口は [docs/README.md](docs/README.md)。

## 固有の規約

- Python 3.11+、環境管理と実行は `uv`。依存は `pyproject.toml` で管理し、素の `pip install` は使わない。
- topic → feature、デコーダー、resampling は `configs/` の YAML で指定する。既存の `RobotConfig` / `FeatureMapping` 等の契約を再利用し、依頼にない設定・引数・抽象化を増やさない。
- 型ヒント、`from __future__ import annotations`、Google style docstring を既存コードに合わせる。ログは `logging.getLogger(__name__)`、CLI 表示は Click を使う。
- 診断・計算ロジックは I/O から分離し、hot path の配列処理は NumPy を使う。`subprocess` の `stdin` を明示し、呼出元の TTY を奪わない。
- 変更に必要なモジュール・設定・テストだけ読む。通常の実装判断は既存契約から決め、互換性や出力の意味が変わる未確定事項だけを確認する。

## 検証

- 通常のロジックは対象テストを `uv run pytest tests/<対象> -q` で確認する。登録マーカーは `pyproject.toml`、fixture と実データ要件は `tests/conftest.py` と対象テストを参照する。
- 変換・デコード・writer を変えたら、関連する `tests/*e2e*.py` と利用可能な実 bag で結果を検証する。入力と既存 `output/` を保持し、新しい出力先を使う。実データがなければ未検証と明記する。
- マージ前は `uv run ruff check .`、`uv run ruff format --check .`、依頼の変更に対応する回帰・E2E を完了する。必要なフォーマット修正は対象ファイルへ適用する。
- 文書だけの変更では参照先とコマンドを照合する。新規テスト、全データ変換、NVENC / Hub の検証を一律に要求しない。

## ドキュメント

- README は英語 [README.md](README.md) と日本語 [README_ja.md](README_ja.md) を同期し、相互リンクを保つ。詳細は `docs/` に置く。
- CLI を変えたら README と [CLI reference](docs/cli_reference.md) を更新する。変更・関連検証・確認できなかった範囲を報告して完了する。
