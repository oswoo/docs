---
title: コードスキャンの SARIF サポート
shortTitle: SARIF サポート
intro: '{{ site.data.variables.product.prodname_dotcom }} のリポジトリにあるサードパーティの静的分析ツールからの結果を表示するには、コードスキャン用に SARIF 2.1.0 JSON スキーマの特定のサブセットをサポートする SARIF ファイルに結果を保存する必要があります。 デフォルトの {{ site.data.variables.product.prodname_codeql }} 静的分析エンジンを使用すると、結果は {{ site.data.variables.product.prodname_dotcom }} のリポジトリに自動的に表示されます。'
product: '{{ site.data.reusables.gated-features.code-scanning }}'
redirect_from:
  - /github/finding-security-vulnerabilities-and-errors-in-your-code/about-sarif-support-for-code-scanning
versions:
  free-pro-team: '*'
  enterprise-server: '>=2.22'
---

{{ site.data.reusables.code-scanning.beta }}
{{ site.data.reusables.code-scanning.enterprise-enable-code-scanning }}

### SARIF サポートについて

SARIF（Static Analysis Results Interchange Format）は、出力ファイル形式を定義する [OASIS 標準](https://docs.oasis-open.org/sarif/sarif/v2.1.0/sarif-v2.1.0.html)です。 SARIF 標準は、静的分析ツールが結果を共有する方法を合理化するために使用されます。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、SARIF 2.1.0 JSON スキーマのサブセットをサポートしています。

サードパーティの静的コード分析エンジンから SARIF ファイルをアップロードするには、アップロードされたファイルが SARIF 2.1.0 バージョンを使用していることを確認する必要があります。 {{ site.data.variables.product.prodname_dotcom }} は SARIF ファイルを解析し、コードスキャンエクスペリエンスの一部としてリポジトリの結果を使用してアラートを表示します。 詳しい情報については、「[SARIF ファイルを {{ site.data.variables.product.prodname_dotcom }} にアップロードする](/github/finding-security-vulnerabilities-and-errors-in-your-code/uploading-a-sarif-file-to-github)」を参照してください。 SARIF 2.1.0 JSON スキーマの詳細については、「[`sarif-schema-2.1.0.json`](https://github.com/oasis-tcs/sarif-spec/blob/master/Schemata/sarif-schema-2.1.0.json)」を参照してください。

SARIF ファイルに `partialFingerprints` が含まれていない場合、{{ site.data.variables.product.prodname_actions }} を使用して SARIF ファイルをアップロードすると、`partialFingerprints` フィールドが計算されます。 詳しい情報については、「[SARIF ファイルを {{ site.data.variables.product.prodname_dotcom }} にアップロードする](/github/finding-security-vulnerabilities-and-errors-in-your-code/uploading-a-sarif-file-to-github#uploading-a-code-scanning-analysis-with-github-actions)」を参照してください。

{{ site.data.variables.product.prodname_dotcom }} は、SARIF ファイルのプロパティを使用してアラートを表示します。 たとえば、`shortDescription` と `fullDescription` は、{{ site.data.variables.product.prodname_code_scanning }} アラートの上部に表示されます。 `location` により、{{ site.data.variables.product.prodname_dotcom }} がコードファイルに注釈を表示できるようになります。 詳しい情報については、「[{{ site.data.variables.product.prodname_code_scanning }} からのアラートを管理する](/github/finding-security-vulnerabilities-and-errors-in-your-code/managing-alerts-from-code-scanning)」を参照してください。

SARIF の使用が初めてで、詳細を確認する必要がある場合は、Microsoft の [`SARIF tutorials`](https://github.com/microsoft/sarif-tutorials) リポジトリを参照してください。

### フィンガープリントを使用してアラートの重複を防止する

{{ site.data.variables.product.prodname_actions }} ワークフローが新しいコードスキャンを実行するたびに、それぞれの実行結果が処理され、アラートがリポジトリに追加されます。 同じ問題に対するアラートの重複を防ぐために、{{ site.data.variables.product.prodname_code_scanning }} はフィンガープリントを使用してさまざまな実行結果を照合し、選択したブランチの最新の実行で 1 回だけ表示されるようにします。

{{ site.data.variables.product.prodname_dotcom }} は、OASIS 標準の `partialFingerprints` プロパティを使用して、2 つの結果が論理的に同一の場合に検出します。 詳しい情報については、OASIS ドキュメントの「"[partialFingerprints プロパティ](https://docs.oasis-open.org/sarif/sarif/v2.1.0/cs01/sarif-v2.1.0-cs01.html#_Toc16012611)」エントリを参照してください。

`id` は SARIF ファイルの他の部分から参照され、{{ site.data.variables.product.prodname_code_scanning }} が {{ site.data.variables.product.prodname_dotcom }} に URL を表示するために使用できます。 If your SARIF file doesn't include `partialFingerprints`, the `partialFingerprints` field will be calculated if you upload the SARIF file using {{ site.data.variables.product.prodname_actions }}. 詳しい情報については、「[SARIF ファイルを {{ site.data.variables.product.prodname_dotcom }} にアップロードする](/github/finding-security-vulnerabilities-and-errors-in-your-code/uploading-a-sarif-file-to-github#uploading-a-code-scanning-analysis-with-github-actions)」を参照してください。

### サポートされている SARIF 出力ファイルのプロパティ

{{ site.data.variables.product.prodname_codeql }} 以外のコード分析エンジンを使用する場合、サポートされている SARIF プロパティを確認して、{{ site.data.variables.product.prodname_dotcom }} での分析結果の表示方法を最適化できます。

有効な SARIF 2.1.0 出力ファイルはすべてアップロードできますが、{{ site.data.variables.product.prodname_code_scanning }} は以下のサポートされているプロパティのみを使用します。

#### `sarifLog` オブジェクト

| 名前        | 説明                                                                                                                                             |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `$schema` | **必須。**バージョン 2.1.0 の SARIF JSON スキーマの URI。 例: `https://raw.githubusercontent.com/oasis-tcs/sarif-spec/master/Schemata/sarif-schema-2.1.0.json` |
| `version` | **必須。**{{ site.data.variables.product.prodname_code_scanning_capc }} は、SARIF バージョン `2.1.0` のみをサポートしています。                                     |
| `runs[]`  | **必須。**SARIF ファイルには、1 つ以上の実行の配列が含まれています。 各実行は、分析ツールの 1 回の実行を表します。 `run` の詳細については、「[`run` オブジェクト](#run-object)」を参照してください。                       |

#### `run` オブジェクト

{{ site.data.variables.product.prodname_code_scanning_capc }} は `run` オブジェクトを使用して、ツールで結果をフィルタし、結果のソースに関する情報を提供します。 `run` オブジェクトには、結果を生成したツールに関する情報を含む `tool.driver` ツールコンポーネントオブジェクトが含まれます。 `run` ごとに、1 つの分析ツールの結果のみを取得できます。

| 名前                            | 説明                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tool.driver.name`            | **必須。**分析ツールの名前。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、{{ site.data.variables.product.prodname_dotcom }} に名前を表示して、ツールで結果をフィルタできるようにします。                                                                                                                                                                                                                                         |
| `tool.driver.version`         | **任意。**分析ツールのバージョン。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、バージョン番号を使用して、分析されているコードでの変更ではなく、ツールのバージョン変更により結果が変更された可能性がある場合に追跡します。 SARIF ファイルに `semanticVersion` フィールドが含まれている場合、`version` は {{ site.data.variables.product.prodname_code_scanning }} で使用されません。                                                                                                                     |
| `tool.driver.semanticVersion` | **任意。**セマンティックバージョニング 2.0 形式で指定された分析ツールのバージョン。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、バージョン番号を使用して、分析されているコードでの変更ではなく、ツールのバージョン変更により結果が変更された可能性がある場合に追跡します。 SARIF ファイルに `semanticVersion` フィールドが含まれている場合、`version` は {{ site.data.variables.product.prodname_code_scanning }} で使用されません。 詳しい情報については、セマンティックバージョニングのドキュメントの「[セマンティックバージョニング 2.0.0](https://semver.org/)」を参照してください。 |
| `tool.driver.rules[]`         | **必須。**ルールを表す `reportingDescriptor` オブジェクトの配列。 分析ツールはルールを使用して、分析対象のコードの問題を見つけます。 詳しい情報については、「[`reportingDescriptor` オブジェクト](#reportingdescriptor-object)」を参照してください。                                                                                                                                                                                                                                           |
| `results[]`                   | **必須。**分析ツールの結果。 {{ site.data.variables.product.prodname_code_scanning_capc }} は {{ site.data.variables.product.prodname_dotcom }} に結果を表示します。 詳しい情報については、「[`result` オブジェクト](#result-object)」を参照してください。                                                                                                                                                                                                    |

#### `reportingDescriptor` オブジェクト

| 名前                           | 説明                                                                                                                                                                                                                                                                                                                            |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`                         | **必須。**ルールの一意の識別子。 `id` は SARIF ファイルの他の部分から参照され、{{ site.data.variables.product.prodname_code_scanning }} が {{ site.data.variables.product.prodname_dotcom }} に URL を表示するために使用できます。                                                                                                                                          |
| `name`                       | **任意。**ルールの名前。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、{{ site.data.variables.product.prodname_dotcom }} のルールで結果をフィルタできるように名前を表示します。                                                                                                                                                              |
| `shortDescription.text`      | **必須。**ルールの簡単な説明。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、関連する結果の横にある {{ site.data.variables.product.prodname_dotcom }} の簡単な説明を表示します。                                                                                                                                                             |
| `fullDescription.text`       | **必須。**ルールの説明。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、関連する結果の横にある {{ site.data.variables.product.prodname_dotcom }} の説明全体を表示します。 文字数は最大 1000 文字に制限されています。                                                                                                                                        |
| `defaultConfiguration.level` | **任意。**ルールのデフォルトの重要度レベル。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、特定のルールの結果がどの程度重要であるかを理解するために、重要度レベルを使用します。 この値は、`result` オブジェクトの `level` 属性でオーバーライドできます。 詳しい情報については、「[`result` オブジェクト](#result-object)」を参照してください。 デフォルト: `Warning`                                                                 |
| `help.text`                  | **必須。**テキスト形式を使用したルールのドキュメント。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、関連する結果の横にこのヘルプドキュメントを表示します。                                                                                                                                                                                                   |
| `help.markdown`              | **推奨。**Markdown 形式を使用したルールのドキュメント。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、関連する結果の横にこのヘルプドキュメントを表示します。 When `help.markdown` is available, it is displayed instead of `help.text`.                                                                                                                   |
| `properties.tags[]`          | **任意。**文字列の配列。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、`tags` を使用して、{{ site.data.variables.product.prodname_dotcom }} の結果をフィルタできます。 たとえば、`security` タグを含むすべての結果をフィルタすることができます。                                                                                                                      |
| `properties.precision`       | **推奨。**このルールで示される結果が true である頻度を示す文字列。 たとえば、ルールに既知の高誤検知率がある場合、精度は `low` である必要があります。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、{{ site.data.variables.product.prodname_dotcom }} の精度で結果を並べ替えるため、最高 `level` の精度と最高 `precision` の結果が最初に表示されます。 Can be one of: `very-high`, `high`, `medium`, or `low`. |

#### `result` オブジェクト

| 名前                                      | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ruleId`                                | **任意。**ルールの一意の識別子（`reportingDescriptor.id`）。 詳しい情報については、「[`reportingDescriptor` オブジェクト](#reportingdescriptor-object)」を参照してください。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、ルール識別子を使用して、{{ site.data.variables.product.prodname_dotcom }} のルールで結果をフィルタします。                                                                                                                                                                                                                                                                                                                                |
| `ruleIndex`                             | **任意。**ツールコンポーネントの `rules` 配列内の関連するルール（`reportingDescriptor` オブジェクト）のインデックス。 詳しい情報については、「[`run` オブジェクト](#run-object)」を参照してください。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `rule`                                  | **任意。**この結果のルール（レポート記述子）を見つけるために使用される参照。 詳しい情報については、「[`reportingDescriptor` オブジェクト](#reportingdescriptor-object)」を参照してください。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `level`                                 | **任意。**結果の重要度。 このレベルは、ルールで定義されているデフォルトの重要度をオーバーライドします。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、レベルを使用して、{{ site.data.variables.product.prodname_dotcom }} の重要度で結果をフィルタします。                                                                                                                                                                                                                                                                                                                                                                                                            |
| `message.text`                          | **必須。**結果を説明するメッセージ。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、結果のタイトルとしてメッセージテキストを表示します。 表示スペースが限られている場合、メッセージの最初の文のみが表示されます。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `locations[]`                           | **必須。**結果が検出された場所。 指定された場所ごとに変更を加えることでのみ問題を修正できる場合を除き、1 つの場所のみを含める必要があります。 **注釈:** {{ site.data.variables.product.prodname_code_scanning }} が結果を表示するには、少なくとも 1 つの場所が必要です。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、このプロパティを使用して、結果を注釈するファイルを決定します。 この配列の最初の値のみが使用されます。 他のすべての値は無視されます。                                                                                                                                                                                                                                                                                                 |
| `partialFingerprints`                   | **必須。**結果の一意の ID を追跡するために使用される文字列。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、`partialFingerprints` を使用して、コミットとブランチで同じ結果であるものを正確に識別します。 {{ site.data.variables.product.prodname_code_scanning_capc }} は、`partialFingerprints` がある場合、それを使用しようとします。 `upload-action` を使用してサードパーティの SARIF ファイルをアップロードする場合、SARIF ファイルに含まれていないときに、アクションによって `partialFingerprints` が作成されます。 詳しい情報については、「[フィンガープリントを使用してアラートの重複を防止する](#preventing-duplicate-alerts-using-fingerprints)」を参照してください。  **注釈:** {{ site.data.variables.product.prodname_code_scanning_capc }} は、`primaryLocationLineHash` のみを使用します。 |
| `codeFlows[].threadFlows[].locations[]` | **任意。**`threadFlow` オブジェクトに対する `location` オブジェクトの配列。実行スレッドを通してプログラムの進行状況を記述します。 `codeFlow` オブジェクトは、結果の検出に使用されるコード実行パターンを記述します。 コードフローが入力されている場合、{{ site.data.variables.product.prodname_code_scanning }} は、関連する結果の {{ site.data.variables.product.prodname_dotcom }} のコードフローを拡張します。 詳しい情報については、「[`location` オブジェクト](#location-object)」を参照してください。                                                                                                                                                                                                                                                                |
| `relatedLocations[]`                    | この結果に関連する場所。 結果メッセージに埋め込まれている場合、{{ site.data.variables.product.prodname_code_scanning_capc }} は、関連する場所にリンクします。 詳しい情報については、「[`location` オブジェクト](#location-object)」を参照してください。                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `suppressions[].state`                  | **任意。**`state` が `accepted` に設定されている場合、{{ site.data.variables.product.prodname_code_scanning }} は {{ site.data.variables.product.prodname_dotcom }} の状態を `Closed` に更新します。                                                                                                                                                                                                                                                                                                                                                                                                                                       |

#### `location` オブジェクト

プログラミングアーティファクト内の場所（リポジトリ内のファイルやビルド中に生成されたファイルなど）。

| 名前                          | 説明                                                                                                 |
| --------------------------- | -------------------------------------------------------------------------------------------------- |
| `location.id`               | **任意。**この場所を単一の結果オブジェクト内の他のすべての場所と区別する一意の識別子。                                                      |
| `location.physicalLocation` | **必須。**アーティファクトとリージョンを識別します。 詳しい情報については、「[`physicalLocation`](#physicallocation-object)」を参照してください。 |
| `location.message.text`     | **任意。**場所に関連するメッセージ。                                                                               |

#### `physicalLocation` オブジェクト

| 名前                     | 説明                                                                                                                                                                                                                                                                                                                                                                                                       |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `artifactLocation.uri` | **必須。**アーティファクトの場所を示す URI（通常はリポジトリ内のファイル、またはビルド中に生成されたファイル）。 URI が相対の場合、分析されている {{ site.data.variables.product.prodname_dotcom }} リポジトリのルートに相対である必要があります。 たとえば、main.js または src/script.js は、リポジトリのルートを基準にしています。 URI が絶対の場合、{{ site.data.variables.product.prodname_code_scanning }} は URI を使用してアーティファクトをチェックアウトし、リポジトリ内のファイルを照合できます。 例: `https://github.com/github/example/blob/00/src/promiseUtils.js` |
| `region.startLine`     | **必須。**リージョンの最初の文字の行番号。                                                                                                                                                                                                                                                                                                                                                                                  |
| `region.startColumn`   | **必須。**リージョンの最初の文字の列番号。                                                                                                                                                                                                                                                                                                                                                                                  |
| `region.endLine`       | **必須。**リージョンの最後の文字の行番号。                                                                                                                                                                                                                                                                                                                                                                                  |
| `region.endColumn`     | **必須。**リージョンの末尾に続く文字の列番号。                                                                                                                                                                                                                                                                                                                                                                                |

### SARIF 出力ファイルの例

次の例の SARIF 出力ファイルは、サポートされているプロパティと値の例を示しています。

#### 最低限必要なプロパティの例

次の SARIF 出力ファイルには、{{ site.data.variables.product.prodname_code_scanning }} の結果が期待どおりに機能するために最低限必要なプロパティを示す値の例が示されています。 プロパティを削除したり、値を含めていない場合、このデータは正しく表示されないか、{{ site.data.variables.product.prodname_dotcom }} で同期されません。


```json
{
    "$schema" : "https://raw.githubusercontent.com/oasis-tcs/sarif-spec/master/Schemata/sarif-schema-2.1.0.json",
    "version" : "2.1.0",
    "runs" :
    [
    {
        "tool" : {
        "driver" : {
            "name" : "Tool Name"
        }
        },
        "results" : [ {
        "message" : {
            "text" : "Result text. This result does not have a rule associated."
        },
        "locations" : [ {
            "physicalLocation" : {
            "artifactLocation" : {
                "uri" : "src/build.cmd"
            },
            "region" : {
                "startLine" : 2,
                "startColumn" : 7,
                "endColumn" : 10
            }
            }
        } ],
        "partialFingerprints" : {
            "primaryLocationLineHash" : "39fa2ee980eb94b0:1"
        }
        }]
    }       
    ]
}
```

#### サポートされているすべての SARIF プロパティを示す例

次の SARIF 出力ファイルには、{{ site.data.variables.product.prodname_code_scanning }} でサポートされているすべての SARIF プロパティを示す値の例が示されています。

```json
{
  "$schema": "https://raw.githubusercontent.com/oasis-tcs/sarif-spec/master/Schemata/sarif-schema-2.1.0.json",
  "version": "2.1.0",
  "runs": [
    {
      "tool": {
        "driver": {
          "name": "Tool Name",
          "semanticVersion": "2.0.0",
          "rules": [
            {
              "id": "js/unused-local-variable",
              "name": "js/unused-local-variable",
              "shortDescription": {
                "text": "Unused variable, import, function or class"
              },
              "fullDescription": {
                "text": "Unused variables, imports, functions or classes may be a symptom of a bug and should be examined carefully."
              },
              "defaultConfiguration": {
                "level": "note"
              },
              "properties": {
                "tags": [
                  "maintainability"
                ],
                "precision": "very-high"
              }
            },
            {
              "id": "js/inconsistent-use-of-new",
              "name": "js/inconsistent-use-of-new",
              "shortDescription": {
                "text": "Inconsistent use of 'new'"
              },
              "fullDescription": {
                "text": "If a function is intended to be a constructor, it should always be invoked with 'new'. Otherwise, it should always be invoked as a normal function, that is, without 'new'."
              },
              "defaultConfiguration": null,
              "properties": {
                "tags": [
                  "reliability",
                  "correctness",
                  "language-features"
                ],
                "precision": "very-high"
              }
            }
          ]
        }
      },
      "results": [
        {
          "ruleId": "js/unused-local-variable",
          "ruleIndex": 0,
          "message": {
            "text": "Unused variable foo."
          },
          "locations": [
            {
              "physicalLocation": {
                "artifactLocation": {
                  "uri": "main.js",
                  "uriBaseId": "%SRCROOT%",
                  "index": 0
                },
                "region": {
                  "startLine": 2,
                  "startColumn": 7,
                  "endColumn": 10
                }
              }
            }
          ],
          "partialFingerprints": {
            "primaryLocationLineHash": "39fa2ee980eb94b0:1",
            "primaryLocationStartColumnFingerprint": "4"
          }
        },
        {
          "ruleId": "js/inconsistent-use-of-new",
          "ruleIndex": 1,
          "message": {
            "text": "Function resolvingPromise is sometimes invoked as a constructor (for example [here](1)), and sometimes as a normal function (for example [here](2))."
          },
          "locations": [
            {
              "physicalLocation": {
                "artifactLocation": {
                  "uri": "https://github.com/github/example/blob/0000000000000000000000000000000000000000/src/promiseUtils.js",
                  "index": 1
                },
                "region": {
                  "startLine": 2
                }
              }
            }
          ],
          "partialFingerprints": {
            "primaryLocationLineHash": "5061c3315a741b7d:1",
            "primaryLocationStartColumnFingerprint": "7"
          },
          "relatedLocations": [
            {
              "id": 1,
              "physicalLocation": {
                "artifactLocation": {
                  "uri": "src/ParseObject.js",
                  "uriBaseId": "%SRCROOT%",
                  "index": 3
                },
                "region": {
                  "startLine": 2281,
                  "startColumn": 33,
                  "endColumn": 55
                }
              },
              "message": {
                "text": "here"
              }
            },
            {
              "id": 2,
              "physicalLocation": {
                "artifactLocation": {
                  "uri": "src/LiveQueryClient.js",
                  "uriBaseId": "%SRCROOT%",
                  "index": 2
                },
                "region": {
                  "startLine": 166
                }
              },
              "message": {
                "text": "here"
              }
            }
          ]
        },
        {
          "message": {
            "text": "Specifying both [ruleIndex](1) and [ruleID](2) might lead to inconsistencies."
          },
          "level": "error",
          "locations": [
            {
              "physicalLocation": {
                "artifactLocation": {
                  "uri": "full.sarif",
                  "uriBaseId": "%SRCROOT%",
                  "index": 0
                },
                "region": {
                  "startLine": 54,
                  "startColumn": 10,
                  "endLine": 55,
                  "endColumn": 25
                }
              }
            }
          ],
          "relatedLocations": [
            {
              "id": 1,
              "physicalLocation": {
                "artifactLocation": {
                  "uri": "full.sarif"
                },
                "region": {
                  "startLine": 81,
                  "startColumn": 10,
                  "endColumn": 18
                }
              },
              "message": {
                "text": "here"
              }
            },
            {
              "id": 2,
              "physicalLocation": {
                "artifactLocation": {
                  "uri": "full.sarif"
                },
                "region": {
                  "startLine": 82,
                  "startColumn": 10,
                  "endColumn": 21
                }
              },
              "message": {
                "text": "here"
              }
            }
          ],
          "codeFlows": [
            {
              "threadFlows": [
                {
                  "locations": [
                    {
                      "location": {
                        "physicalLocation": {
                          "region": {
                            "startLine": 11,
                            "endLine": 29,
                            "startColumn": 10,
                            "endColumn": 18
                          },
                          "artifactLocation": {
                            "uriBaseId": "%SRCROOT%",
                            "uri": "full.sarif"
                          }
                        },
                        "message": {
                          "text": "Rule has index 0"
                        }
                      }
                    },
                    {
                      "location": {
                        "physicalLocation": {
                          "region": {
                            "endColumn": 47,
                            "startColumn": 12,
                            "startLine": 12
                          },
                          "artifactLocation": {
                            "uriBaseId": "%SRCROOT%",
                            "uri": "full.sarif"
                          }
                        }
                      }
                    }
                  ]
                }
              ]
            }
          ],
          "partialFingerprints": {
            "primaryLocationLineHash": "ABC:2"
          }
        }
      ],
      "newlineSequences": [
        "\r\n",
        "\n",
        "",
        "  "
      ],
      "columnKind": "utf16CodeUnits"
    }
  ]
}
```