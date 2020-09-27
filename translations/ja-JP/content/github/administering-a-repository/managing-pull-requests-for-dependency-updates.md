---
title: 依存関係の更新に関するプルリクエストを管理する
intro: '{{ site.data.variables.product.prodname_dependabot }} によって生成されたプルリクエストは、他のプルリクエストとほぼ同じ方法で管理しますが、いくつかの追加オプションがあります。'
versions:
  free-pro-team: '*'
---

{{ site.data.reusables.dependabot.beta-note }}

### {{ site.data.variables.product.prodname_dependabot }} のプルリクエストについて

{{ site.data.variables.product.prodname_dependabot }} は、依存関係を更新するプルリクエストを生成します。 リポジトリの設定によっては、{{ site.data.variables.product.prodname_dependabot_short }} がバージョン更新やセキュリティアップデートのプルリクエストを発行する場合があります。 これらのプルリクエストは、他のプルリクエストと同じ方法で管理しますが、追加のコマンドもいくつか用意されています。 {{ site.data.variables.product.prodname_dependabot }} 依存関係の更新を有効にする方法については、「[{{ site.data.variables.product.prodname_dependabot_security_updates }} を構成する](/github/managing-security-vulnerabilities/configuring-github-dependabot-security-updates)」および「[バージョン更新の有効化と無効化](/github/administering-a-repository/enabling-and-disabling-version-updates)」を参照してください。

{{ site.data.variables.product.prodname_dependabot }} がプルリクエストを発行すると、リポジトリに対して選択した方法で通知されます。 各プルリクエストには、パッケージ管理システムから取得した、提案された変更に関する詳細情報が含まれています。 これらのプルリクエストは、リポジトリで定義されている通常のチェックとテストに従います。 また、十分な情報がある場合は、互換性スコアが表示されます。 これは、変更をマージするかどうかを決める際にも役立ちます。 このスコアについての詳しい情報は、「[{{ site.data.variables.product.prodname_dependabot_security_updates }} を設定する](/github/managing-security-vulnerabilities/configuring-github-dependabot-security-updates)」を参照してください。

管理する依存関係が多数ある場合は、各パッケージマネージャーの設定をカスタマイズして、プルリクエストに特定のレビュー担当者、アサインされた人、ラベルを付けることができます。 詳しい情報については、「[依存関係の更新をカスタマイズする](/github/administering-a-repository/customizing-dependency-updates)」をご覧ください。

### {{ site.data.variables.product.prodname_dependabot }} のプルリクエストを表示する

{{ site.data.reusables.repositories.navigate-to-repo }}
{{ site.data.reusables.repositories.sidebar-pr }}
1. セキュリティおよびバージョン更新のプルリクエストは、簡単に特定できます。
    - 作者は [dependabot](https://github.com/dependabot) で、{{ site.data.variables.product.prodname_dependabot }} で使用されるボットアカウントです。
    - デフォルトでは、`dependencies` ラベルが付いています。

### {{ site.data.variables.product.prodname_dependabot }} プルリクエストのリベース戦略を変更する

デフォルトでは、{{ site.data.variables.product.prodname_dependabot }} は自動的にプルリクエストをリベースして競合を解決します。 マージの競合を手動で処理する場合は、`rebase-strategy` オプションを使用してこれを無効にできます。 詳細については、「[依存関係の更新の設定オプション](/github/administering-a-repository/configuration-options-for-dependency-updates#rebase-strategy) 」を参照してください。

### {{ site.data.variables.product.prodname_dependabot }} プルリクエストをコメントコマンドで管理する

{{ site.data.variables.product.prodname_dependabot }} はコメント内の単純なコマンドに応答します。 各プルリクエストには、プルリクエストのマージ、squash、再オープン、クローズ、リベースなど、プルリクエストの処理に使用できるコマンドの詳細が含まれています。 これらの自動生成されたプルリクエストをできるだけ簡単にトリアージできるようにすることが目的です。

依存関係やバージョンを無視するコマンドを実行すると、{{ site.data.variables.product.prodname_dependabot }} はリポジトリの設定を一元的に保存します。 これは簡単な解決策ですが、複数のコントリビューターがいるリポジトリの場合は、設定ファイルで無視する依存関係とバージョンを明示的に定義することをお勧めします。 これにより、特定の依存関係が自動的に更新されない理由をすべてのコントリビューターが簡単に確認できます。 詳しい情報については、「[依存関係の更新の設定オプション](/github/administering-a-repository/configuration-options-for-dependency-updates#ignore) 」を参照してください。