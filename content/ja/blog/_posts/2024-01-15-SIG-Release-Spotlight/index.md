---
layout: blog
title: "SIG Releaseスポットライト(リリース・チーム・サブプロジェクト)"
date: 2024-01-15
slug: sig-release-spotlight-2023
author: >
  Nitish Kumar
---

リリース・スペシャル・インタレスト・グループ(SIG Release)は、Kubernetesが4ヶ月ごとに最先端の機能とバグ修正でその刃を研ぐ場所です。Kubernetesのような大きなプロジェクトが、新バージョンをリリースするまでのタイムラインをどのように効率的に管理しているのか、またリリースチームの内部はどのようになっているのか、考えたことはありますか？このような疑問に興味がある方、もっと知りたい方、SIG Releaseの仕事に関わりたい方は、ぜひ読んでみてください！

SIG ReleaseはKubernetesの開発と進化において重要な役割を担っています。その主な責任は、Kubernetesの新バージョンのリリースプロセスを管理することです。[通常3〜4ヶ月ごと](https://www.kubernetes.dev/resources/release/)の定期的なリリースサイクルで運営されています。このサイクルの間、Kubernetesリリースチームは他のSIGやコントリビューターと密接に連携し、円滑でうまく調整されたリリースを保証します。これには、リリーススケジュールの計画、コードフリーズとテストフェーズの期限の設定、バイナリ、ドキュメント、リリースノートなどのリリース成果物の作成が含まれます。

さらに読み進める前に、SIG Releaseにはリリース・エンジニアリングとリリース・チームという2つのサブプロジェクトがあることに注意してください。

このブログ記事では、[Nitish Kumar](https://twitter.com/nitishfy)がSIG Releaseのテクニカル・リーダーであるVerónica López (PlanetScale)にインタビューし、Release Teamサブプロジェクトにスポットライトを当て、リリース・プロセスがどのように見えるか、そして参加する方法について説明します。

1. **最初の計画から最終的なリリースまで、Kubernetesの新バージョンの典型的なリリースプロセスはどのようなものですか？スムーズなリリースを保証するために使用している特定の方法論やツールはありますか？**

    Kubernetesの新バージョンのリリースプロセスは、十分に構造化されたコミュニティ主導の取り組みです。私たちが従う特定の方法論やツールはありませんが、物事を整理しておくための一連の手順を記載したカレンダーはあります。完全なリリースプロセスは次のようになります：

- **リリースチームの立ち上げ**： 新しいリリースのさまざまなコンポーネントの管理を担当するKubernetesコミュニティのボランティアを含むリリースチームの結成から始めます。これは通常、前のリリースが終了する前に行われます。チームが結成されると、リリースチームリーダーとブランチマネージャーが通常の成果物のカレンダーを提案する間に、新しいメンバーがオンボードされます。例として、SIG Releaseのリポジトリに作成された[v1.29チーム結成のissue](https://github.com/kubernetes/sig-release/issues/2307)を見てください。コントリビューターがリリースチームの一員になるには、通常リリースシャドウプログラムを通りますが、それがSIG Releaseに参加する唯一の方法というわけではありません。

- **初期段階**： 各リリースサイクルの最初の数週間で、SIG ReleaseはKubernetes機能強化提案(KEPs)で概説された新機能や機能強化の進捗を熱心に追跡します。これらの機能のすべてがまったく新しいものではありませんが、多くの場合、アルファ段階から始まり、その後ベータ段階に進み、最終的には安定したステータスに到達します。

- **機能の成熟段階**： 通常、コミュニティからのフィードバックを集めるため、実験的な新機能を含むアルファ・リリースを2、3回行い、その後、機能がより安定し、バグの修正が中心となるベータ・リリースを2、3回行います。この段階でのユーザーからのフィードバックは非常に重要で、この段階で発生する可能性のあるバグやその他の懸念に対処するために、追加のベータ・リリースを作成しなければならないこともあります。これがクリアされると、実際のリリースの前にリリース候補(RC)を作成します。このサイクルを通じて、リリースノートやユーザーガイドなどのドキュメントの更新や改善に努めます。

- **安定化段階**： 新リリースの数週間前にコードフリーズを実施し、この時点以降は新機能の追加を禁止します。メインリリースと並行して、私たちはKubernetesの古い公式サポートバージョンのパッチを毎月作成し続けているので、Kubernetesバージョンのライフサイクルはその後数ヶ月に及ぶと言えます。完全なリリースサイクル全体を通して、リリースノートやユーザーガイドを含むドキュメントの更新と改善に努めます。

    {{< figure src="sig-release-overview.png" alt="リリースチームのオンボーディング; 初期段階; 機能の成熟段階; 安定化段階" >}}

2. **各リリースで安定性と新機能の導入のバランスをどのように扱っていますか？どのような基準で、どの機能をリリースに含めるかを決定するのですか？**

    終わりのないミッションですが、重要なのは私たちのプロセスとガイドラインを尊重することだと考えています。私たちのガイドラインは、このプロジェクトに豊富な知識と経験をもたらしてくれるコミュニティの何十人ものメンバーから、何時間にもわたって議論とフィードバックを重ねた結果です。もし厳格なガイドラインがなかったら、私たちの注意を必要とするもっと生産的な議題に時間を使う代わりに、同じ議論を何度も繰り返してしまうでしょう。すべての重要な例外は、チームメンバーの大半の合意を必要とするため、品質を確保することができます。

    何がリリースになるかを決定するプロセスは、リリースチームがワークフローを引き継ぐずっと前から始まっています。各SIGと経験豊富なコントリビューターが、機能や変更を含めるかどうかを決定します。その後、リリースチームが、それらの貢献がドキュメント、テスト、後方互換性などの要件を満たしていることを確認し、正式に許可します。同様のプロセスは月例パッチリリースのチェリーピックでも行われ、完全なKEPを必要とするPRや、影響を受けるすべてのブランチを含まない修正は受け入れないという厳しいポリシーがあります。

3. **Kubernetesの開発とリリース中に遭遇した最も大きな課題は何ですか？これらの課題をどのように克服しましたか？**

    リリースのサイクルごとに、さまざまな課題が発生します。新たに発見されたCVE(Common Vulnerabilities and Exposures)のような土壇場の問題に取り組んだり、内部ツール内のバグを解決したり、以前のリリースの機能によって引き起こされた予期せぬリグレッションに対処したりすることもあります。私たちがしばしば直面するもう1つの障害は、私たちのチームは大規模ですが、私たちのほとんどがボランティアベースで貢献していることです。時には人手が足りないと感じることもありますが、私たちは常に組織化し、うまくやりくりしています。

4. **新しい貢献者として、SIG Releaseに参加するための理想的な道はどのようなものでしょうか？誰もが自分のタスクに忙殺されているコミュニティで、効果的に貢献するために適切なタスクを見つけるにはどうすればいいのでしょうか？**

    オープンソースコミュニティへの関わり方は人それぞれです。SIG Releaseは、リリースを出荷できるように自分たちでツールを書くという、自分勝手なチームです。[SIG K8s Infra](https://github.com/kubernetes/community/blob/master/sig-k8s-infra/README.md)のような他のSIGとのコラボレーションも多いのですが、私たちが使用するツールはすべて、コストを削減しつつ、私たちの大規模な技術的ニーズに合わせて作られたものでなければなりません。このため、「単に」リリースを作成するだけでなく、さまざまなタイプのプロジェクトを手伝ってくれるボランティアを常に探しています。

    私たちの現在のプロジェクトでは、[Go](https://go.dev/)プログラミング、Kubernetes内部の理解、Linuxパッケージング、サプライチェーンセキュリティ、テクニカルライティング、一般的なオープンソースプロジェクトのメンテナンスなどのスキルが必要です。このスキルセットは、プロジェクトの成長とともに常に進化しています。

    理想的な道筋として、私たちはこう提案します:

   - どのように機能が管理されているか、リリースカレンダー、リリースチームの全体的な構造など、コードに慣れる。
   - [Slack](https://communityinviter.com/apps/kubernetes/community)(#sig-release)などのKubernetesコミュニティのコミュニケーションチャンネルに参加する。
   - コミュニティ全員が参加できる[SIG Releaseウィークリーミーティング](https://github.com/kubernetes/community/tree/master/sig-release#meetings)に参加する。これらのミーティングに参加することは、あなたのスキルセットや興味に関連すると思われる進行中のプロジェクトや将来のプロジェクトについて学ぶ素晴らしい方法です。

   経験豊富な貢献者は皆、かつてあなたのような立場にあったことを忘れないでください。遠慮せずに質問し、議論に参加し、貢献するための小さな一歩を踏み出しましょう。

   {{< figure src="sig-release-meetings.png" alt="SIG Releaseに関する質問" >}}

5. **リリースシャドウプログラムとは何ですか？また、他の様々なSIGに含まれるシャドウプログラムとの違いは何ですか？**

    リリースシャドウプログラムは、Kubernetesのリリースサイクルを通して、リリースチームの経験豊富なメンバーをシャドウイングする機会を提供します。これは、Kubernetesのリリースに必要な、サブチームにまたがるすべての困難な仕事を見るまたとないチャンスです。多くの人は、私たちの仕事は3ヶ月ごとにリリースを切ることだけだと思っていますが、それは氷山の一角にすぎません。

    私たちのプログラムは通常、特定のKubernetesリリースサイクルに沿っており、それは約3ヶ月の予測可能なタイムラインを持っています。このプログラムではKubernetesの新機能を書くことはありませんが、リリースチームは新リリースと何千人ものコントリビューターとの最後のステップであるため、高い責任感が求められます。

6. **一般的に、次のKubernetesリリースのリリースシャドウ/リリースリードとしてボランティアに参加する人に求める資格は何ですか？**

    どの役割もある程度の技術的能力を必要としますが、Goの実践的な経験やKubernetes APIに精通していることを必要とするものもあれば、技術的な内容を明確かつ簡潔に伝えるのが得意な人を必要とするものもあります。技術的な専門知識よりも、熱意とコミットメントを重視しています。もしあなたが正しい姿勢を持っていて、Kubernetesやリリース・エンジニアリングの仕事を楽しんでいることが伝われば、たとえそれがあなたが余暇を利用して立ち上げた個人的なプロジェクトであったとしても、チームは必ずあなたを指導します。セルフスターターであること、そして質問をすることを恐れないことは、私たちのチームであなたを大きく前進させます。

7. **リリースシャドープログラムに何度も不合格になった人に何を勧めますか？**

    応募し続けることです。

    リリースサイクルごとに応募者数が飛躍的に増えているため、選ばれるのが難しくなり、落胆することもありますが、不採用になったからといって、あなたに才能がないというわけではないことを知っておいてください。すべての応募者を受け入れることは現実的に不可能です、しかし、ここに私たちが提案する代替案があります。:

    毎週開催されるKubernetes SIGのリリースミーティングに参加して、自己紹介をし、チームや私たちが取り組んでいるプロジェクトに慣れてください。

    リリースチームはSIG Releaseに参加する方法の1つですが、私たちは常に手伝ってくれる人を探しています。繰り返しになりますが、一定の技術的な能力に加えて、私たちが最も求めている特性は、信頼できる人であり、それには時間が必要です。

    {{< figure src="sig-release-motivation.png" alt="SIG Releaseのモチベーション" >}}

8. **リリースチームがKubernetes v1.28に特に期待している進行中の取り組みや今後の機能について教えてください。これらの進歩は、Kubernetesの長期的なビジョンとどのように整合しているのでしょうか？**

    Kubernetesのパッケージをコミュニティインフラ上でついに公開できることに興奮しています。数年前からやりたいと思っていたことですが、移行する前に整えなければならない技術的な意味合いが多いプロジェクトです。それが終われば、生産性を向上させ、ワークフロー全体をコントロールできるようになります。

## 最後に

さて、この対談はここで終わりですが、学習はこれで終わりではありません。このインタビューが、SIG Releaseが何をしているのか、そしてどのように手助けを始めたらいいのか、ある程度わかっていただけたと思います。重要なこととして、この記事はSIG Releaseの最初のサブプロジェクトであるリリース・チームを取り上げています。次回のSIG Releaseのスポットライトブログでは、Release Engineeringサブプロジェクトにスポットライトを当て、その活動内容や参加方法について紹介します。最後に、SIG Releaseの運営方法についてより深く理解するために、[SIG Release憲章](https://github.com/kubernetes/community/tree/master/sig-release)をご覧ください。
