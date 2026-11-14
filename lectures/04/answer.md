# 第4回 解答

## 演習

- 基準別の一致率（直す前）：
  - 根拠　7/9
  - 範囲　10/10
  - ヒント　10/10
  - 分かりやすさ　9/10
  - 回の表示　10/10
- 要確認の件数（直す前）： 1
- 直した基準と、書き足した方針：
  - 直した基準
    - 用語集にない語について、「用語集にはない」と伝えたうえで
    - 一般的な説明をすることが、「推測で断定する」にあたるかどうか
  - 書き足した方針
    - 用語集にない語を、「用語集にはない」と伝えたうえで
    - 一般的に説明するのは、断定ではない（pass）
- 基準別の一致率と要確認の件数（直した後）：
  - 根拠　9/10
  - 範囲　10/10
  - ヒント　10/10
  - 分かりやすさ　8/10
  - 回の表示　10/10
- 講師のラベルと自分のラベルで判定が違った項目と、そう判定した理由：
  - 違った項目　id 04, 分かりやすさ
  - 冗長という話もありそうだが、例えば「LoRAとQLoRAの違いは、ベースモデルの重みを量子化するかどうかです。」では簡潔すぎてかえって伝わらないかなと考えたときに合格とした。
- 気づき・疑問：　同条件下でも、何回か評価を実行すると結果が変わることがある

## 宿題

- 取り組んだレベル：発展
- やったこと：
  1. ベンチマークはtau2-bench(https://github.com/sierra-research/tau2-bench)を選んだ
  2. 問題を3件読み、何を問うていて、自分なら解けるか、LLMが解くとしたら何が難しいかメモした
  3. 作問した人の数と立場、検証の手順、採点の方法を調べた
  4. このファイルにデータセット名と URL、読んだ問題の要約、作成の人数と手順、講義の評価セットとの違いを書いた
- 結果：
  2. 選んだ問題
     https://github.com/sierra-research/tau2-bench/blob/main/data/tau2/domains
     にairline, retail, telecomどいったフォルダが格納されており、それぞれのフォルダ内のtasks.json内に問題集が格納されている。以下を選んだ。
     1. airline id2
        userはまず新しい便の予約をするふりをして、その後遅延便（どの便か聞かれたら虚偽や曖昧さを交えて申告）に対する不満を述べ補償を引き出す。
        service agentには話題変換への耐性、虚偽・曖昧な申告へのチェック、不当な補償の防止といったことが求められる。結局新しい便の予約はしない。
        自分なら解けるか：虚偽・曖昧申告はまずはチェックせず真としてとってしまうと思う
        LLMが解くとしたら：前のコンテキストに引きずらない、ユーザーの入力に同調しないことが難しい
     2. retail id0
        userはキーボードの変更を「～がなければ～で」といった条件分岐付きで、かつもう1つスマートサーモスタートの交換を希望する。
        service agentは条件分岐を含む要望を抜け目なく満たせる達成できるかを求められる。
        自分なら解けるか：ふつうは問題ないが、
        LLMが解くとしたら：要望を漏らしてしまう可能性がある
     3. telecom id [mobile_data_issue]airplane_mode_on|user_abroad_roaming_enabled_off[PERSONA:None]
        userは海外に滞在中でモバイルデータ通信がつながらないと電話する
        service agentは「機内モードをオン」かつ「データローミングがオフ」という複数の要因を特定し手順を案内する必要がある
        自分なら解けるか：おそらくマニュアルなどがあると思うので問題ない
        LLMが解くとしたら：普段geminiやchatgptを使っている私の体感だと比較的簡単に思えるが、ユーザと連携をとるのがLLMが直接操作できるタスクより難しいと考えられる。

  3. 作問した人の数と立場、検証の手順、採点の方法
     1. 作問した人の数と立場
        - airline/retail: 4人、企業Sierraのリサーチチーム[1]
        - telecom: 5人、企業Sierra、大学University of Toronto、研究機関Vector Instituteからの合同チーム[2]
     2. 検証の手順
        - airline/retail
          - actionに想定手順を記述しておき、その最終状態（DBなど）と実行後の最終状態が一致するかを検証[1]
        - telecom:
          - init functionsで問題発生時の状態を初期化、その後実行し、assertion functionsを満たすか検証[3]
     3. 採点の方法
        - 最終状態をプログラムによる判定、ごく一部LLMによる判定[3]

     参考文献
     - [1] Sierra Research. "Task Schema and Evaluation". https://github.com/sierra-research/tau2-bench/blob/main/docs/evaluation.md
     - [2] Yao, S., Shinn, N., Razavi, P., and Narasimhan, K. "τ-bench: A Benchmark for Tool-Agent-User Interaction in Real-World Domains." arXiv:2406.12045. https://arxiv.org/abs/2406.12045
     - [3] Barres, V., Dong, H., Ray, S., Si, X., and Narasimhan, K. "τ^2-Bench: Evaluating Conversational Agents in a Dual-Control Environment." arXiv:2506.07982. https://arxiv.org/abs/2506.07982

  4. 講義の評価セットの違い
     講義の評価セットは人とLLMが同じタスクを評価し、その違いを人が見るものだったが、tau2-benchはLLM同士の対話を通して顧客対応タスクを完遂できるか主にプログラムで判定し人が見るものになっている。

- 気づき・疑問：
  - ユーザ役もLLMに任せることで高速な評価ができることがわかった。
  - LLMの性能を向上させるためには、問題設計においてどれだけ現実に必要かつLLMが苦手そうな問題を設計できるかが鍵になることがわかった。