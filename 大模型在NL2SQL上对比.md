# 大模型在NL2SQL上对比

基于论文：Battle of the Large Language Models: Dolly vs LLaMA vs Vicuna vs Guanaco vs Bard vs ChatGPT - A Text-to-SQL Parsing Comparison (2023.10)结果进行对比

本文对六种语言模型进行了综合评估：Dolly、LLaMA、Vicuna、Guanaco、Bard 和 ChatGPT，利用五种不同的提示策略，直接比较它们在九个基准数据集上的性能。

我们的主要发现是：

- 在大多数文本到 SQL 数据集中，开源模型的性能明显低于闭源模型。
- 虽然LLM在生成语法上有效的 SQL 语句方面表现出熟练程度，但他们通常很难生成语义上准确的查询。
- 事实证明，LLM 对用于小样本学习( few-shot learning)的示例高度敏感。

## 3.1 提示词策略

- Informal Schema (IS) ：非正式模式 (IS) 策略以自然语言提供表及其关联列的描述。在这种方法中，模式信息以不太正式的方式表达。
- API Docs (AD) ：相比之下，Rajkumar (2022)等人进行的评估中概述的 API 文档 (AD) 策略，遵循OpenAI 文档4中提供的默认 SQL 翻译提示。此提示遵循稍微更正式的数据库模式定义。
- Select 3 ：Select 3 策略包括数据库中每个表的三个示例行。 此附加信息旨在提供每个表中包含的数据的具体示例，以补充模式描述。
- 1SL：1-Shot Learning (1SL)，在提示中提供 1 个黄金示例。
- 5SL ：5 Shot Learning (5SL) ，在提示中提供 5 个黄金示例。

## 3.2 在Spider和8大传统数据集表现

- 数据集简介

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/WZBibiatAX4xzHpHKb2vgonPcZ2bukGSnhibrb37r11Fy5PbVYoL9vC0I7Kw74jicxQ814rdBibK1ArF95T4tiaOYNWw/640?wx_fmt=jpeg&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- 在spider 数据集表现

  ![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/WZBibiatAX4xzHpHKb2vgonPcZ2bukGSnh024AE6gbX9LicME45YpCA7VXibl6ulSGBV3yzHaZNTkZzA0C617sJyjw/640?wx_fmt=jpeg&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

  - 开源模型在 Spider 数据集上遇到了困难：尽管参数数量和模型性能之间存在正相关关系，但开源模型在 Spider数据集上实现高精度方面面临着挑战。 例如，尽管 Vicuna 7B 和 13B 已证明比原始预训练的 LLaMA 7B 和 13B模型有所改进，但与 Bard 和 GPT-3.5 相比，性能仍然存在显着差距。 此外，与 LLaMA 的 13B 版本相比，Dolly模型在不同的提示策略上也表现不佳。
  - LLM的表现对提示风格高度敏感：我们的实证研究结果证实，不存在适用于所有模型的通用提示策略。 虽然 IS 提示策略对于GPT-3.5、Bard、Vicuna 和guanaco 被证明是有效的，但对于 Dolly 和 LLaMA 来说却产生了次优的准确度。令人惊讶的是，LLaMA 在使用 S3 提示时实现了最佳结果，相比之下，GPT-3.5 的性能显著恶化。
  - 使用随机示例的小样本学习提供的性能提升有限：从 1SL 和 5SL获得的大多数结果往往表现不佳，或者充其量只能达到与其他提示策略相当的结果。 然而，这种趋势也有一些例外。 Dolly 模型是一个例外，与12B 变体中的其他提示策略相比，该模型显示 1SL 提示策略的性能有所提高。 这个结果似乎是反常的，因为在其他 1SL 和 5SL结果中没有观察到类似的性能提升。 另一个例外是 LLaMA 模型，其中少样本提示策略优于一些零样本策略。 例如，30B LLaMA模型仅用 5 个给定示例就实现了 22.4% EX 和 19.9% TS 准确率，这接近于guanaco 模型的性能（24.4% EX 和19.0% TS）。

更多模型的表现：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/WZBibiatAX4xzHpHKb2vgonPcZ2bukGSnhwcL24f9gWSpIcSqMqKKm9m6EFqwke9AjNtBp7ZV9IxVpn9yJcrkX6g/640?wx_fmt=jpeg&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

闭源模型如GPT和BARD在NL2SQL任务中显著优于开源模型，这得益于它们接受了更多的参数训练。通过额外的监督微调，模型性能得到显著提升，例如Alpaca-7B模型相比其前身Llama-7B改进了近16%，突显了微调对性能增强的潜力。与此同时，较新的开源模型如Mistral-7B和Llama2性能更优，正逐步缩小与闭源模型的差距。

- 经典数据集下情况

  ![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/WZBibiatAX4xzHpHKb2vgonPcZ2bukGSnh9iaRB2mBkttbMCpiaaTSuLyo6shykzQPc2yDgqqSANb28T4wicQOS0CYw/640?wx_fmt=jpeg&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

  - LLM在大多数经典数据集上表现不佳：特别是，与之前研究中报告的基线性能相比，这些数据集上达到的最高准确率分别仅为 2.9% 和 2.4%，明显低于使用 LSTM 或 BERT 的传统 seq2seq模型的其他研究中观察到的基线结果 34.0% 和 45.2%（Devlin 等人， 2019）。此外，即使进行了指令调整，Vicuna、Guanaco 和 Dolly 在经典数据集上也面临着相当大的挑战。它们在各种提示策略和数据集组合中的执行精度通常几乎为零。
  - 不同模型的少样本学习的有效性有所不同：与 Spider 数据集的发现相比，我们观察到 LLaMA 和 GPT-3.5 在 1SL 和5SL 上的性能有所改进。 例如，使用 1SL，GPT-3.5 在 GeoQuery 数据集上的性能从 15.4% 提高到42.3%，而使用 5SL，LLaMA 在同一数据集上的性能也从 12.1% 显着提高到 15.4%。 然而，我们没有看到 Dolly、Vicuna 和 Bard 的 1SL或 5SL 具有类似的性能改进。
  - 附加数据库示例行是无效的：就像使用Spider数据集观察到的结果一样，S3 提示策略在应用于不同模型的经典数据集时会产生低于标准的结果。因此，很明显，S3 提示策略在 Text-to-SQL 环境中可能并不有效。

## 3.3 大模型在SQL生成效果分析

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/WZBibiatAX4xzHpHKb2vgonPcZ2bukGSnhCHenjl6BdGqxE8g9S9csBMXFS6UnwfA5plJlol9r1bAqtVdXYTzTYg/640?wx_fmt=jpeg&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/WZBibiatAX4xzHpHKb2vgonPcZ2bukGSnhjNkAN4w24dNkFSI0DbOvrmibn4TBYVSGplTssf13kdHuS1T5WaFLvrw/640?wx_fmt=jpeg&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

大型语言模型在生成SQL语句时常表现不佳，可能因为它们难以理解提示背后的真实意图。我们在多个数据集上测试了不同模型，发现除Dolly外，大多数模型在特定提示策略下能生成90%以上的有效SQL。尽管LLaMA未经指令数据集的微调，它仍能生成有效SQL。然而，开源模型如Vicuna和Dolly在达到高有效SQL百分比方面存在挑战。值得注意的是，LLaMA通过小样本学习提升性能，而guanaco则随着示例增多性能下降。另外，某些提示策略并不理想，尤其是S3策略，它显著降低了GPT-3.5在多个数据集上的有效SQL生成率。尽管模型能生成SQL，但语义不准确，导致执行精度低。

