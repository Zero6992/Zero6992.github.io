---
title: "類神經網路中神經元對特定單字的預測"
description: "深入了解 GPT 語言模型如何透過類神經網路中的神經元來預測 Token。本文探討神經元與 Token 之間的一致性與互斥一致性，以及它們在語言理解與生成中扮演的角色"
date: 2023-04-17T16:19:00+08:00
draft: false
tags: [gpt, gpt-2, ChatGPT, 類神經網路, 大型語言模型]
mathjax: true
---

> 本文譯自 [We Found An Neuron in GPT-2](https://clementneo.com/posts/2023/02/11/we-found-an-neuron)，作者 Clement Neo 是倫敦的大學生。文章是他黑客松題目的延伸。二月在 Hacker News 上看到，覺得很好玩，趁這次期中考結束順便翻譯一次。有些術語還沒有公認的中譯，我會盡量在後面附上原文，讀起來如果有點彆扭請見諒。文章談的是怎麼找到 LLM 處理特定 token 時負責的神經元——類神經網路最常被詬病的就是像個黑盒子，沒人知道中間發生了什麼事，所以這篇文章可以看成解開黑盒子的一次嘗試。

> 2023/05/10 更新：OpenAI 最近也做了類似的事。他們拿 GPT-4 去標記 GPT-2 每個神經元對輸出文字的影響，試著解釋 GPT-2 的運作方式。
> [OpenAI 用 GPT-4 來理解 GPT-2 是什麼](https://www.inside.com.tw/article/31577-openais-new-tool-attempts-to-explain-llm-behavior)

## 我們在 GPT-2 中找到了 "an" 神經元
`作者：Joseph Miller、Clement Neo`

這份研究的起點是一個問題：***GPT-2 怎麼知道什麼時候該用 "an" 而不是 "a"？*** 對人類來說，這取決於後面那個字是不是母音開頭，但 GPT-2 一次只能吐一個字（嚴格說是一個 token），它是怎麼判斷的？

完整答案還沒有，不過我們在 GPT-2 Large 找到一顆對預測 " an" 至關重要的 MLP 神經元。這顆神經元的權重剛好對應到 " an" 這個 token 的 embedding；沿用同樣方法，還能找出其他專門預測特定 token 的神經元。

### 找出神經元

##### 挑提示詞（prompt）
要設計一個 prompt 讓 GPT-2 把 " an"（前面那個空格是 token 的一部分）當成最佳預測，比想像中難。試了一輪後，我們放棄 GPT-2_small，改用 GPT-2_large。後面會看到，即使 GPT-2_large 也會系統性低估 " an" 這個 token。原因可能是較小的語言模型更依賴 " a" 的出現頻率，更容易把 " a" 當成最佳猜測。最後找到能讓 " an" 機率拉到 64% 的 prompt 是：

> "I climbed up the pear tree and picked a pear. I climbed up the `apple` tree and picked"

第一句不能省。少了它，模型不會往「不定冠詞」的方向走，反而會給出像 "[picked] up" 這種預測。

開始拆解前先複習一下 [Transformer 架構](https://transformer-circuits.pub/2021/framework/index.html)。每個 attention block 跟 MLP 都會把自己的輸出加進 residual stream。
   ![image](https://user-images.githubusercontent.com/89479282/232722119-09e06fca-baab-48e6-b887-4ca3c91cdd48.png)

#### Logit Lens
[Logit lens](https://www.lesswrong.com/posts/AcKRB8wDpdaN6v6ru/interpreting-gpt-the-logit-lens) 的做法是在模型每一層之間從 residual stream 拉出 logit，畫出 logit(" an") 跟 logit(" a") 的差。我們在第 31 層 MLP 之後看到一個很明顯的峰值，如下圖。


   ![image](https://user-images.githubusercontent.com/89479282/232725226-748c4121-8afe-49bf-8e03-49cd58f9d083.png)


#### 分層做 Activation Patching
Activation patching 是 [Meng 等人 (2022)](https://arxiv.org/abs/2202.05262) 提出的技巧，用來分析 Transformer 單層的重要性。做法是先把原 prompt 餵進模型，把每一層的 activation 存下來，這些叫「淨 activation（clean activation）」。

接著把 prompt 裡的 apple tree 換成 lemon tree，再餵一次：

> "I climbed up the pear tree and picked a pear. I climbed up the `apple` tree and picked"

> "I climbed up the pear tree and picked a pear. I climbed up the `lemon` tree and picked"

把 "apple" 換成 "lemon" 後，模型會改成預測 " a"。這時把某一層的 activation 換回淨 activation，看模型往 " an" 偏多少，就能評估那一層對預測 " an" 的貢獻。對每一層都重複一次。

![image](https://user-images.githubusercontent.com/89479282/232732236-84a664ab-c26f-430b-80b7-882819a83ba2.png)

![image](https://user-images.githubusercontent.com/89479282/232732433-dd91af7e-3450-4b34-a1a9-76b3fa84d728.png)

這篇文章不會深入談 attention layer，但這些結果指出：第 26 層是 " picked" 開始大量參考 " apple" 的地方，這顯然是預測 " an" 的前提。

註：圖表刻度是 relative logit difference recovery

$$
\frac{PatchedLogitDiff − CorruptedLogitDiff}{CleanLogitDiff − CorruptedLogitDiff}
$$

（也就是說，圖代表 patching 把原 prompt 中 " an" 跟 " a" 的 logit 差距還原了多少）

![image](https://user-images.githubusercontent.com/89479282/232733478-84b5de26-3ddc-42ca-ac8f-ea82d8bd2794.png)

最突出的兩層 MLP 是第 0 層跟第 31 層。第 0 層 MLP 對 GPT-2 很關鍵這件事我們早就知道（雖然不確定為什麼第 0 層的 attention 也重要）[註1]。第 31 層更有趣——結果顯示它在預測 " an" 這個 token 上有顯著作用。（如果你看完上面 logit lens 的結論還覺得對不上，可以參考[這則討論](https://www.lesswrong.com/posts/cgqh99SHsCv3jJYDS/we-found-an-neuron-in-gpt-2?commentId=FLpxtfnwnMjZwXv3B#comments)。）

### 發現一：<br />對單一神經元做 activation patching，可以找出有預測力的神經元

Activation patching 過去是按層研究 Transformer，能不能再往下推到單一神經元？Transformer 每個 MLP 只有一個隱藏層，同一層裡每顆神經元的 activation 彼此不會影響。換句話說，單顆神經元也可以被 patch——它們跟同層 attention head 之間是獨立的。

做法跟分層 patching 類似，這次對象是第 31 層 MLP 的神經元。在被改過的 prompt 上跑模型，把單顆神經元的淨 activation 重新塞回 MLP，看那顆神經元對 " a"／" an" logit 差距的還原貢獻多少。

![image](https://user-images.githubusercontent.com/89479282/233306391-41c0f3f6-7dec-4b63-b9f7-48fb11d7c492.png)

只 patch 892 號神經元，就能還原 50% 的 logit 差距；patch 整層其實只有 49%——比單顆還少。

簡單講：我們在 GPT-2 Large 找到一顆對預測 " an" 至關重要的 MLP 神經元。順著 activation patching 這條路，可以繼續往單一神經元下手，理解它們在 token 預測上扮演的角色。一開始的問題還沒完全回答，但這些結果至少推進了一步，讓語言模型的運作機制清楚了一些。

### 發現二：「an 神經元」的激發與「an」token 的預測有相關性

#### Neuroscope [第 31 層 892 號神經元](https://neuroscope.io/gpt2-large/31/892.html) 最大激發案例

![image](https://user-images.githubusercontent.com/89479282/233309645-a7705dd3-c1c6-449d-a39c-0690357067b6.png)
Neuroscope 是一個線上工具，能看 GPT-2 每顆神經元在大型資料集裡的最大激發案例。看第 31 層 892 號神經元時會發現：它在「下一個 token 是 an」的情況下達到最大 activation。

但 Neuroscope 只顯示最高激發的前 20 個案例。在更廣的激發範圍裡，神經元跟 token 的相關性還成立嗎？

#### 拿更大的資料集驗證
我們把 pile-10k 資料集丟進模型。這份資料集從 The Pile 取了大約 1000 萬個 token，切成 1024 token 一段的 prompt。在神經元 activation 的範圍上，畫出「an」預測的比例：

![image](https://user-images.githubusercontent.com/89479282/233310982-62139036-2b3f-4a48-9d1a-4b46b65b79ed.png)
「an」的預測比例會隨 activation 升高而升高，最後甚至成為最頂端的預測。圖上有些 noise，暗示模型裡可能還有其他機制也在影響「an」的預測。也可能是「an」的 logit 增加時，其他 logit 跟著一起上來。

值得注意的是：資料集中「an」實際出現 12,000 次，模型只預測了 1,500 次。難怪要找到好用的 prompt 那麼難。

#### 神經元的輸出權重（output weights）與「an」token 的內積很高
這顆神經元怎麼影響輸出？它的輸出權重跟「an」token 的 embedding 內積特別高。我們把這個內積叫做這顆神經元與 token 的一致性（congruence）。對照其他 token（像「any」、「had」），它跟「an」的一致性顯然超出一截：

![image](https://user-images.githubusercontent.com/89479282/233312469-290d6f35-d93f-478d-9597-1ad12848c5ef.png)
計算它跟所有 token 的一致性（congruence），有幾個明顯的 outlier 跳出來：

![image](https://user-images.githubusercontent.com/89479282/233313512-0a4b2991-f948-48c4-924e-0d0458fdd686.png)
這顆神經元基本上就是把「an」的 embedding 加進 residual stream，提升「an」的輸出機率——因為 unembedding 那一步其實就是把最終 residual 跟每個 token 的 embedding 做內積。[註2]

那其他神經元呢？也跟「an」一致嗎？我們把所有神經元跟「an」token 的一致性畫出來：
![image](https://user-images.githubusercontent.com/89479282/233314247-d93a0938-8b36-4992-be48-6cd93076e922.png)

第 31-892 號這顆遙遙領先，但還是有少數神經元的一致性（congruence）偏高。這也可能是「an 神經元 activation」跟「an token 預測」相關性不夠完美的原因之一：某些 prompt 上，模型可能改用別的神經元來輸出「an」。

如果是這樣，能不能反過來——用一致性，找到跟單一 token 預測完全相關的神經元？

### 發現三：可以用神經元的輸出一致性，找到專門預測某個 token 的神經元
#### 找出跟特定 token 對應的神經元

搜尋步驟：

1. 對每個 token，找輸出一致性（congruence）最高的神經元
2. 對這些神經元，比較它在預測同一個 token 時的最高一致性與第二高的差距
3. 找出擁有「最獨佔一致性（the most exclusively congruent）」的神經元。

目標是找到對特定 token 影響力特別大的神經元。直覺是：這些神經元的激發跟對應 token 的預測會更相關，因為那個 token 的任何預測都「依賴」這顆神經元。

![image](https://user-images.githubusercontent.com/89479282/233317704-3d37d708-ef82-4249-a080-0b7257ff539e.png)

拿「though」神經元——第 28 層 1921 號——跑一次，看圖會不會比較乾淨。
![image](https://user-images.githubusercontent.com/89479282/233318279-2d83bf69-9dcd-4831-86d3-18410b827a08.png)

結果比 " an" 神經元的圖還亂。怎麼回事？

去看 Neuroscope 提供的資料，這顆神經元同時負責預測「though」跟「however」兩個 token。事情變得更複雜了——它對應的是一組語意相近的 token（連接副詞），不是單一個 token。
[註3]
![image](https://user-images.githubusercontent.com/89479282/233319211-2922b2fc-230d-4c48-b0eb-142eb88e5b53.png)

把它跟所有 token 的一致性算出來，類似的 token 常常一起冒在 outlier 區：
![image](https://user-images.githubusercontent.com/89479282/233319375-96407240-e7b3-4544-a0e8-645b4f8dc136.png)

剛剛大資料集那張相關圖之所以有 noise，是因為「神經元激發、預測 however」被算成負面案例——畢竟 however 不是這顆神經元最頂的預測 token。同樣道理也能解釋「an」相關圖裡的 noise，因為「An」、「 An」、「an」意思相同，這幾個會互相干擾。[註4]

有沒有更單純的神經元可以研究？最好是只預測單一 token 的那種。

#### 找一顆乾淨對應到單一 token 的神經元
「乾淨對應」是指神經元跟 token 之間的一致性要互斥：

1. 神經元跟 token 的一致性，遠大於它跟其他所有神經元的一致性。
2. 神經元跟 token 的一致性，遠大於它跟其他所有 token 的一致性。
（再強調一次，這裡的「一致性（congruence）」就是內積的代稱。）

這兩條把神經元跟 token 的關係收得更乾淨。如果「神經元對 token 的一致性」代表這顆神經元對該 token 預測的貢獻程度，那麼第一條保證「只有這顆神經元」負責預測那個 token；第二條保證這顆神經元「只負責預測那個 token」。

搜尋流程：

1. **對每個 token，找最一致的神經元。**
2. **對每個神經元，找最一致的 token。**[註5]
3. **找出在兩份清單上互相對應的 token-神經元配對——也就是說，神經元最一致的 token，反過來也是這顆神經元最一致的 token！**
4. **把這對配對在「token 前兩名一致性的差」乘上「神經元前兩名一致性的差」，當作互斥分數。**
5. **找出互斥一致性最高的配對。**

![image](https://user-images.githubusercontent.com/89479282/233319494-b978d131-5e9d-4858-b3ae-b254692bbd12.png)
GPT-2_large 上，第 33 層 4142 號神經元跟 "i" 的配對拿下這個指標的冠軍。對照 Neuroscope [註6]，相關性確實成立：

![image](https://user-images.githubusercontent.com/89479282/233319606-3e8f5dc0-43cf-47cb-aecd-33dd83d7334e.png)
畫出前五名配對「神經元 activation 對 top-1 預測比例」的圖：[註7]

![image](https://user-images.githubusercontent.com/89479282/233319772-b1fafa3e-eb7f-4379-8abb-adcdd0065ada.png)
每一對的相關性都很強。

### 這代表什麼？
「神經元跟 token 的一致性」是否真的衡量了那顆神經元對 token 預測的貢獻？我們不知道。即使是互斥一致性很高的配對，也不一定每次都會出現預測上的相關性。原因可能有幾個：

1. token 也可能由一群一致性都不高、但加起來夠的神經元共同預測。
2. token 可能由 attention head 預測。
3. 即使神經元 activation 跟 token logit 高度相關，它也可能間接拉動其他 token 的 logit，導致 activation 跟「該 token 出現機率」之間不相關。
4. 後面的層可能在 residual stream 上加進反向作用，把這顆神經元的效果抵銷掉。

話雖如此，互斥一致性前五名的神經元，跟對應 token 的預測之間都顯示出很強的相關性。

### TL; DR
1. 我們用神經元層級的 activation patching，找到一顆對特定 prompt 中預測 " an" 極為關鍵的神經元。
2. 這顆「" an" 神經元」的 activation 跟 " an" token 的預測有一定相關性。
3. 原因可能是這顆神經元的輸出權重跟 " an" token 的內積特別高（神經元與 token 的一致性很高）。它跟這個 token 的內積大於跟其他任何 token 的內積，反過來這個 token 跟它的內積也大於跟其他任何神經元的內積（互斥一致性極高）。
4. 神經元跟 token 之間的一致性是個很有意思的指標。我們找到互斥一致性最高的五對神經元-token 配對，這些神經元的 activation 跟對應 token 的預測之間有很強的相關性。

重現結果的程式碼：https://github.com/UFO-101/an-neuron

這篇是我們在 [Apart Research](https://apartresearch.com/) 的[黑客松](https://itch.io/jam/mechint) 獲勝作品的延伸。感謝倫敦 [EA Hub](https://krischari.notion.site/Our-Coworking-Space-daff577809a84832a0d8bb28940c78c0) 提供共同工作空間、[Fazl Barez 的評論](https://fbarez.github.io/) 跟 [Neel Nanda 的建議](https://www.neelnanda.io/)，以及 [Neuroscope](https://neuroscope.io/)、[pile-10k 資料集](https://huggingface.co/datasets/NeelNanda/pile-10k) 跟 [TransformerLens](https://github.com/neelnanda-io/TransformerLens)。

------
{% notel default 註解 %}
##### [註1]
Neel Nanda 對 MLP 第 0 層的看法：
「GPT-2 Small 上常常觀察到 MLP 第 0 層極為重要，把它關掉幾乎會徹底破壞 LLM 的效能。我目前最好的猜測是：第一個 MLP 層幾乎等於 prompt embedding 的擴充版本（不管原因是什麼）。後面的層想存取輸入 token 時，主要讀的是第一個 MLP 層的輸出，而不是 prompt 本身。在這個框架下，第一個 attention 層其實沒做什麼事。
這也解釋了為什麼 MLP 第 0 層在「第二個主題 token」那個位置特別關鍵——那是唯一一個輸入 token 不一樣的位置。
我也不完全確定為什麼會這樣。猜測是 GPT-2 Small 把 embedding 跟 unembedding 矩陣綁在一起。其實這做法不太合理，因為 embed/unembed 並不是互為逆運算，但這是常見作法，模型可能得犧牲一些參數去處理這件事。
我手上只有間接證據，希望有人能好好研究一下。」

##### [註2]
這顆神經元還可能做什麼？比如壓低 " a" 的 logit——對 logit 差距會有同樣的效果。或者它往 residual stream 加進完全不同的方向，讓後面層的神經元再去把 " an" logit 拉高。

##### [註3]
有意思的是：" though" 神經元對應的是一組語意相近的 token，而 " an" 神經元對應的是一組語法相近的 token（例如 " an" 跟 " Ancients"）。

##### [註4]
為什麼 " an" 的相關性比較乾淨，雖然也有其他一致的 token（"an"、"An"、" An"）？無法確定。一個可能的解釋是：「An」跟「 An」實在太罕見，對相關性影響有限；而「an」（沒空格那個）跟這顆神經元的一致性又比另外三個低很多。

一般來說，只看「每個 token 前兩名神經元差距」找到的神經元，通常跟對應 token 沒有乾淨的相關性，因為這些神經元可能同時跟多個 token 一致。

##### [註5]
看每個 token 最一致的神經元時，會撞到一些一致性高得誇張的[麻煩製造者](https://www.lesswrong.com/posts/aPeJE8bSo6rAFoLqg/solidgoldmagikarp-plus-prompt-generation)：

![image](https://user-images.githubusercontent.com/89479282/233886186-94185a3f-d7da-4c88-8a87-bdb14572dfe9.png)

乍看之下，這些「禁忌 token」都跟同一顆「禁忌神經元」（第 35 層 3354 號）有關，一致性都很高。但實際上，畫許多其他神經元的最一致 token，也會看到這類奇怪 token 出現在排名頂端。我們的初步猜測是跟 [hubness effect（集束效應）](https://www.lesswrong.com/posts/Ya9LzwEbfaAMY8ABo/?commentId=M2uAwsCus2adqQsGc) 有關。

##### [註6]
這顆神經元在 Neuroscope 沒有資料，所以我們從 pile-10k 自己挑了最大激發案例。文本 1、2、3 分別是 prompt 1755、8528 跟 6375。

[註7]
值得注意的是，前五名 token 裡有一個是 "an"，但它跟前面講的 " an" 不一樣——這個 "an" 很少作為單字開頭或獨立使用。同樣地，跟它配對的神經元（第 34 層 4549 號）也不是前面提到的 " an" 神經元。
{% endnotel %}

