# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
  - 第1回では、モデルが保持している知識のみを用いて回答していた。
  - 第3回では、回答前にツールを利用して用語集を検索し、その結果を根拠として回答していた。
  - エージェントは必要に応じてツールを呼び出し、外部情報を参照しながら回答を生成できることを確認した。 【1-243081】

- `--trace` で見えたツール呼び出し（ツール名と引数）：
  - `search_glossary({'query': 'LoRA'})`
  - `search_glossary({'query': 'QLoRA'})` 【1-243081】

- 1つの質問での LLM 呼び出し回数（`requests`）：
  - `requests=2` 【1-243081】

- 追加したツールと、docstring に書いた説明：
  - `search_glossary`
  - AIエンジニアリングや講座固有の用語を検索し、定義や講義回との対応関係を取得するためのツール。
  - 用語に関する質問へ回答する前に使用するよう説明されている。 【1-243081】

## 宿題

- 取り組んだレベル：
  - 基礎 【1-243081】

- やったこと：
  - `app/tools.py` の `search_glossary()` の docstring を変更した。
  - 変更前は「query には調べたい用語を1つだけ入れる」とされていたが、変更後は「queryには関連する複数の用語をまとめて指定してよい」とした。
  - 変更前後で以下のコマンドを実行し、ツール呼び出しの違いを比較した。

```bash
uv run python -m app.agent --trace "LoRAとQLoRAの違いは？"
```

- 結果：

  **変更前**

```text
[tool call] search_glossary({'query': 'LoRA'})
[tool call] search_glossary({'query': 'QLoRA'})

input_tokens=1196
output_tokens=164
requests=2
tool_calls=2
```

  **変更後**

```text
[tool call] search_glossary({'query': 'LoRAとQLoRAの違いは？'})

input_tokens=1031
output_tokens=249
requests=2
tool_calls=1
```

  比較結果は以下のとおり。

| 項目 | 変更前 | 変更後 |
|------|------|------|
| query | LoRA、QLoRA | LoRAとQLoRAの違いは？ |
| tool_calls | 2 | 1 |
| requests | 2 | 2 |
| input_tokens | 1196 | 1031 |
| output_tokens | 164 | 249 |

  docstringのみを変更したにもかかわらず、モデルがツールへ渡す引数やツール呼び出し回数が変化した。また、tool_calls は 2 回から 1 回に減少し、input_tokens も減少した。 【1-243081】

- 気づき・疑問：
  - Python の実装を一切変更していないにもかかわらず、docstring を変更しただけでエージェントの挙動が変化した。
  - モデルはツールの説明文を参照して、どのツールをどのような引数で呼び出すかを判断していることが分かった。
  - tool_calls は 2 回から 1 回に減少した一方で requests は変化しなかったため、tool_calls と requests は異なる指標であることも確認できた。
  - エージェントの品質改善では、コードだけでなく、docstring やプロンプト設計も重要な要素であることを学んだ。 【1-243081】
