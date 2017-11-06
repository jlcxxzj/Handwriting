# 一个利用神经网络识别手写数字

![preview](https://github.com/Swift-AI/NeuralNet-Handwriting-iOS/blob/master/preview.gif)

人类视觉系统是大自然的一大奇迹。 考虑下面的手写数字序列：

<img src="http://og1yl0w9z.bkt.clouddn.com/17-11-6/50025427.jpg" width=150 height=35 />


大部分人能够毫不费力的识别出这些数字是 504192。这种简单性只是一个幻觉。在我们大脑各半球，有一个主要的视觉皮层，即V1，它包含1.4亿个神经元以及数以百亿的神经元连接。而且人类不只是有V1，还有一系列的视觉皮层——V2,V3,V4和V5，它们能够执行更加复杂的图像处理。我们可以将大脑想象成一台超级计算机，在几亿年的进化中不断改进，最终非常适合理解这个视觉世界。要识别手写数字不是一件非常容易的事。然而，我们人类却能非常惊人的通过我们的眼睛理解所看到的一切，但是几乎所有的工作都是在不知不觉中完成的，以至于我们不会赞叹我们的视觉系统解决的问题有多么艰难。

视觉模式识别困难在于如何让计算机程序识别上面数字变得显而易见。看上去非常简单的事操作起来却变得非常困难。我们识别这个形状的简单直觉是——“一个9头上有一个圆圈，右下角有一笔竖线”——但是对于识别算法却不是那么简单。当试图让这些规则更加精确，你将迅速迷失在大量的例外、警告和特殊案例，而且似乎看不到解决的希望。

神经网络用不同的方法来处理这个问题。它的思想就是利用大量的手写数字（训练样本）

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/77922233.jpg)

然后开发出一套从训练样本中进行学习的系统。换句话说，神经网络使用样本来自动推理出识别手写数字的规则。此外，通过增加训练样本规模，神经网络能学到手写体的更多规则从而提升它的识别精度。因此在我们像上面一样只有100张训练数字同时，有可能我们能通过成千上万更多的训练样本来构建更好的手写识别算法。

本章我们将编写一段计算机程序来实现一个能识别手写数字的神经网络。这个程序大概有74行，而且没有使用其他特别的神经网络库，但是这段程序能够具有96%的数字识别精度，而且没有人工干预。此外，在后面的章节中我们将改进算法达到99%以上的精度。实际上，最好的商业神经网络已经很好的应用在银行支票处理以及邮局识别地址等。

我们专注在手写体识别是因为它是一个很好的学习神经网络的原型问题。做为一个原型，它刚好合适：识别手写数字是一个挑战，不是那么容易，而它也不需要一个极其复杂的方案或者巨大的计算能力。此外，它也是开发更多高级技术的好方法，比如深度学习。因此整本书我们将不断重复回到手写识别这个问题。这本书的后面，我们将讨论这些算法思想如何应用到计算机视觉的其他问题，还包括语言识别、自然语言处理和其他领域。

当然，如果本章只是写一个识别手写数字的计算机程序，那么将非常短小！在编写过程中，我们将介绍很多神经网络的关键思想，包括人工神经网络的两大类别（感知器和sigmoid神经元）以及神经网络标准学习算法，即随机梯度下降。整个过程我们将关注在阐述为什么这样处理是有效的，从而让你构建起神经网络直觉。这比只陈述基础的机制更加冗长，但这对更深入理解所学到的内容是值得的。在这些收获上，本章结束你将明白深度学习是什么，且它为什么这么重要。

---
### 感知器(Perceptrons)

什么是神经网络？在开始之前，我将介绍一种人工的神经元，即感知器(perceptron)。感知器是由Frank Rosenblatt 在上世纪50到60年代发明的 , 灵感来源于 Warren McCulloch 和 Walter Pitts 的早期工作。今天人工神经网络使用更加通用的其他模型，本书以及许多现代的神经网络工作中，主要的神经网络模型是sigmoid神经元。 我们将很快讨论sigmoid神经元，但是为了更好了解它的原理，我们首先要理解感知器。

那么感知器如何工作呢？一个感知器获取几个二进制输入x1,x2,…，并且产生一个二进制数出：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/22211912.jpg)

在这个例子中，感知器具有三个输入x1,x2,x3。通常它会具有更多或更少的输入。Rosenblatt 提出了一个简单规则来计算最后数出。他引入了权重(weights) w1,w2,…，这些实数表示各个输入对输出的重要性。这个神经元输出(output) 0 或者 1是由这些输入的加权求和 ∑jwjxj 是否大于或者小于某一个阈值(threshold)。不像这些权重，阈值是这个神经元的实数参数。将它们放入更加精确的代数术语中：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/71177109.jpg)

这就是一个感知器如何工作的全部内容！
这是一个基础的数学模型。你可以这么理解感知器，它是一个通过加权凭据来进行决策的设备。让我们来看这个例子，可能它不是一个真实的例子，但是非常好理解，后面我们将很快进入真实的例子。假设周末到了，你听说在你所在的城市将有一个奶酪节，你很喜欢吃奶酪，并且正决定是否要去参加这个节日，你可能会通过以下三个方面来权衡你的决定:

1.天气好吗？
2.你的男(女)朋友是否愿意陪你去？
3.是否这个活动距离公共交通很近？（你自己没车）

我们将这三个因素用对应的二进制变量x1,x2和x3表示。比如，当天气还不错时，我们有x1=1，天气不好时x1=0；相似的，如果男或女朋友愿意去，x2=1，否则x2=0；对于公共交通x3同理赋值。

现在假设奶酪是你的最爱，以致于即便你的男或女朋友不感兴趣而且去那里也不太方便，你仍然非常想去参加这个节日活动。但是也许你真的讨厌坏天气，而且如果天气很糟，你也没办法去。你能使用感知器来模拟这类决策。一种决策方式是，让天气权重 w1=6 ，其他条件权重分别为w2=2，w3=2。权重w1值越大表示天气影响最大，比起男或女朋友加入或者交通距离的影响都大。最后，假设你选择5做为感知器阈值，按照这种选择，这个感知器就能实现这个决策模型：当天气好时候输出1，天气不好时候输出0，无论你男或女朋友是否愿意去，或者交通是否比较近。

通过更改权重和阈值，我们能得到不同的决策模型。例如，我们将阈值设为3，那么感知器会在以下条件满足时决定去参加活动：如果天气很好、或者男(女)朋友愿意去并且交通很近。换句话说，它将是决策的不同模型，阈值越低，表明你越想去参加这个节日活动。

显然，这个感知器不是人类决策的完整模型！但是这个例子说明了一个感知器如何能将各种凭据进行加权和来制定决策，而且一个复杂的感知器网络能做出非常微妙的决策：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/74377278.jpg)

在这个网络中，第一列感知器（我们称其为第一层感知器）通过加权输入凭据来做出三个非常简单的决策。那第二列感知器是什么呢？其中每一个感知器都是通过将第一列的决策结果进行加权和来做出自己的决策。通过这种方式，第二层感知器能够比第一层感知器做出更加复杂和抽象层的决策。第三层感知器能做出更加复杂的决策，以此类推，更多层感知器能够进行更加复杂的决策。
顺便说一句，当我们定义感知器时，它们都只有一个输出。但上面的网络中，这些感知器看上去有多个输出。实际上，它们也仍然只有一个输出，只不过为了更好的表明这些感知器输出被其他感知器所使用，因此采用了多个输出的箭头线表示，这比起绘制一条输出线然后分裂开更好一些。

让我们简化描述感知器的方式。加权求和大于阈值的条件 ∑jwjxj>threshold比较麻烦，我们能用两个符号变化来简化它。第一个改变是将 ∑jwjxj 改写成点乘方式，w⋅x≡∑jwjxj 分别是权重和输入的向量。第二个改变是将阈值放在不等式另一边，并用偏移 b≡−threshold 表示。通过使用偏移代替阈值，感知器规则能够重写为：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/42989424.jpg)

你可以将偏移想象成使感知器如何更容易输出 1，或者用更加生物学术语，偏移是指衡量感知器触发的难易程度。对于一个大的偏移，感知器更容易输出 1。如果偏移负值很大，那么感知器将很难输出 1。显然，引入偏移对于描述感知器改动不大，但是我们后面将看到这将简化符号描述。正因如此，本书剩下部分都将使用偏移，而不是用阈值。

我们已经介绍了用感知器，基于凭据(evidence)的加权求和来进行决策。感知器也能够被用来计算基本逻辑函数，像 AND, OR, and NAND 这些通常被看做是实现计算的基础。比如说， 假设我们有一个两个输入的感知器，每个输入具有权重−2，整个偏移为3，如下所示感知器：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/30975949.jpg)

那么我们将看到输入00会产生输出1，因为(−2)∗0+(−2)∗0+3=3是正数。这里，我们引入∗符号来进行显示乘法。与此类似，输入01和10都将产生输出1。但是输入11将产生输出0，因为(−2)∗1+(−2)∗1+3=−1是负数。因此这个感知器实现了一个NAND门!

这个NAND 门例子表明我们能够使用感知器来计算简单的逻辑函数。实际上，我们能够使用感知器网络来计算任意的逻辑函数。原因是NAND 门在计算中是通用的，也就是，我们能够用一些NAND 门来构造任意计算。比如，我们能够用NAND门来构建两个二进制变量x1和x2的位加法电路。这需要对它们按位求和x1+x2，同时当x1和x2都是1时候进位1。进位就是x1和x2的按位乘：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/47504996.jpg)

---
### Sigmoid神经元

学习算法听起来好极了。但是我们如何对一个神经网络设计算法。假设我们有一个想通过学习来解决问题的感知器网络。比如，网络的输入可能是一些扫描来的手写数字图像原始像素数据。且我们希望这个网络能够学会调权和偏移以便正确的对它们进行数字分类。为了看到学习如何进行，假设我们在网络中改变一点权重（或者偏移）。我们希望很小的权重改变能够引起网络输出的细微改变。后面我们将看到，这个特性将使学习成为可能。以下示意图就是我们想要的（显然这个网络对于手写识别太简单！）：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/63800015.jpg)

如果权重或者偏移的细小改变能够轻微影响到网络输出，那么我们会逐步更改权重和偏移来让网络按照我们想要的方式发展。比如，假设网络错误的将数字“9”的图像识别为“8”，我们将指出如何在权重和偏移上进行细小的改动来使其更加接近于“9”。（tensorfly.cn社区原创）并且这个过程将不断重复，不断地改变权重和偏移来产生更好的输出。于是这个网络将具有学习特性。

问题是在我们的感知器网络中并没有这样发生。实际上，任何一个感知器的权重或者偏移细小的改变有时都能使得感知器输出彻底翻转，从0到1。这种翻转可能导致网络剩余部分的行为以某种复杂的方式完全改变。因此当“9”可能被正确分类后，对于其他图片，神经网络行为结果将以一种很难控制的方式被完全改变。这使得很难看出如何逐渐的调整权重和偏移以至于神经网络能够逐渐按照预期行为接近。也许对于这个问题有一些聪明的方式解决，但是目前如何让感知器网络学习还不够清楚。

我们能通过引入一种新的人工神经元（sigmoid神经元）来克服这个问题。它和感知器类似，但是细微调整它的权重和偏移只会很细小地影响到输出结果。这就是让sigmoid神经元网络学习的关键原因。

好的，让我们来描述一下这个sigmoid神经元。我们将用描绘感知器一样来描绘它：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/73069756.jpg)

σ函数的代数形式是什么？我们如何理解它呢？实际上，σ函数的准确形式不太重要——真正有用的是绘制的函数形状，看看下面这个形状：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/37533519.jpg)

---
### 神经网络体系结构

在下一区段，我将介绍用神经网络能够很好的对手写数字进行区分。先做点准备，让我们命名一下网络中不同部分的术语。假如我们有如下网络：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/2842184.jpg)

就像先前说的，网络的最左边一层被称为输入层，其中的神经元被称为输入神经元。最右边及输出层包含输出神经元，在这个例子中，只有一个单一的输出神经元。中间层被称为隐含层，因为里面的神经元既不是输入也不是输出。“隐含”这个术语可能听起来很神秘——当我第一次听到时候觉得一定有深层的哲学或者数学意义——但实际上它只表示“不是输入和输出”而已。上面的网络只包含了唯一个隐含层，但是一些网络可能有多层。比如，下面的4层网络具有2个隐含层：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/96008867.jpg)

令人困扰的是，由于一些历史原因，这样的多层网络有时被叫做多层感知器或者MLPs尽管它是由sigmoid神经元构成的，而不是感知器。在这本书中，我将不会使用MLP，因为我觉得它太混淆了，但是提醒你它可能在其它地方出现。

但是，人工神经网络也有一些具有循环反馈。这样的模型被称为递归神经网络。这样的模型思想是让神经元在不活跃之前激励一段有限的时间。这种激励能刺激其它神经元，使其也能之后激励一小会。这就导致了更多神经元产生激励，等过了一段时间，我们将得到神经元的级联反应。在这样的模型中循环也不会有太大问题，因为一个神经元的输出过一会才影响它的输入，而不是瞬间马上影响到。

递归神经网络没有前馈神经网络具有影响力，因为递归网络的学习算法威力不大（至少到目前）。但是递归网络仍然很有趣，它们比起前馈网络更加接近于我们人脑的工作方式。并且递归神经网络有可能解决那些前馈网络很难解决的重要问题。但是为了限制本书的范围，我们将集中在已经被大量使用的前馈神经网络上。

---
### 一个分类手写数字的简单神经网络

定义了神经网络后，让我们回到手写识别。我们能将识别手写数字分成两个子问题。首先，我们想办法将一个包含很多数字的图像分成一系列独立的图像，每张包含唯一的数字。比如我们将把下面图像

<img src="http://og1yl0w9z.bkt.clouddn.com/17-11-6/28542473.jpg" width=150 height=35 />

分成6个分离的小图像，

<img src="http://og1yl0w9z.bkt.clouddn.com/17-11-6/40186974.jpg" width=150 height=35 />

我们人类能够很容易解决这个分段问题，但是对于计算机程序如何正确的分离图像却是一个挑战。然后，一旦这幅图像被分离，程序需要将各个数字进行分类。因此，比如，我们希望程序能够将上面的第一个数字

<img src="http://og1yl0w9z.bkt.clouddn.com/17-11-6/47364313.jpg" width=50 height=50 />

识别为5。

我们主要关注在第二个问题，也就是，分类这些独立的数字图像。我们这么做是因为一旦你能够找到一个好方式来分类独立的图像，分离问题就不是那么难解决了。有许多途径能够解决分离问题，一种途径是试验很多不同的分离图像方法，并采用独立图像分类器给每个分离试验打分。如果独立数字分类器坚信某种分离方式，那么打分较高。如果分类器在某些片断上问题很多，那么得分就低。这个思想就是如果分类器分类效果很有问题，那么很可能是分离方式不对造成。这种想法和一些变型能够很好解决分离问题。因此无须担心分离，我们将集中精力开发一个神经网络，它能解决更有趣和复杂的问题，即识别独立的手写数字。

为了识别这些数字，我们将采用三层神经网络：

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/42472175.jpg)

网络的输入层含有输入像素编码的神经元。跟下节讨论的一样，我们用于网络训练的数据将包含许多28像素手写数字扫描图像，因此输入层包含784=28×28个神经元。为了简化，我们在上图中忽略了许多输入神经元。输入像素是灰度值，白色值是0.0，黑色值是1.0，它们之间的值表明灰度逐渐变暗的程度。

网络的第二层是隐含层。我们将其神经元的数量表示为n，后面还对其采用不同的n值进行实验。这个例子中使用了一个小的隐含层，只包含n=15个神经元。

网络的输出层包含10个神经元。如果第一个神经元被触发，例如它有一个≈1的输出，那么这就表明这个网络认为识别的数字是0。更精确一点，我们将神经元输出标记为0到9，然后找出具有最大激励值的神经元。如果这个神经元是6，那么这个网络将认为输入数字是6。对于其他神经元也有类似结果。

它能够对输入图像与上面图像中像素重叠部分进行重加权，其他像素轻加权来实现。相似的方式，对隐含层的第二、三和四个神经元同样能检测到如下所示的图像：

<img src="http://og1yl0w9z.bkt.clouddn.com/17-11-6/96204736.jpg" width=150 height=35 />

你可能已经猜到，这四幅图像一起构成了我们之前看到的0的图像：

<img src="http://og1yl0w9z.bkt.clouddn.com/17-11-6/79412966.jpg" width=50 height=50 />

因此如果这四个神经元一起被触发，那么我们能够推断出手写数字是0。当然，这些不只是我们能推断出0的凭据类别——我们也可以合理的用其他方式得到0（通过对上述图像的平移或轻微扭曲）。但是至少这种方式推断出输入是0看起来是安全的。

---
### 梯度下降学习算法

现在我们已经有一个设计好的神经网络，它怎样能够学习识别数字呢？首要的事情是我们需要一个用于学习的数据集——也叫做训练数据集。我们将使用MNIST数据集，它包含成千上万的手写数字图像，同时还有它们对应的数字分类。MNIST的名字来源于NIST（美国国家标准与技术研究所）收集的两个修改后的数据集。这里是一些来自于MNIST的图像：

<img src="http://og1yl0w9z.bkt.clouddn.com/17-11-6/76115445.jpg" width=150 height=35 />

就像你看到的，事实上这些数字和本章开始展示的用于识别的图像一样。当然，测试我们的网络时候，我们会让它识别不在训练集中的图像！

MNIST数据来自两部分。第一部分包含60,000幅用于训练的图像。这些图像是扫描250位人员的手写样本得到的，他们一半是美国人口普查局雇员，一半是高中学生。这些图像都是28乘28像素的灰度图。MNIST数据的第二部分包含10,000幅用于测试的图像。它们也是28乘28的灰度图像。我们将使用这些测试数据来评估我们用于学习识别数字的神经网络。为了得到很好的测试性能，测试数据来自和训练数据不同的250位人员（仍然是来自人口普查局雇员和高中生）。 这帮助我们相信这个神经网络能够识别在训练期间没有看到的其他人写的数字。

![](http://og1yl0w9z.bkt.clouddn.com/17-11-6/60410790.jpg)

我们想要找到怎样才能使C达到全局最小化。现在，当然，对上面绘制的函数，我们能看出该图中最小化位置。从这种意义上讲，我可能展示了一个太简单的函数！一个通常的函数C可能由许多变量构成，并且很难看出该函数最小化位置在哪里。

如果我们使用默认的设置来跑scikit-learn的SVN分类器，对于10,000张测试图片，大概能猜对9,435张。（代码可以在 这里找到。）这相对于根据图片暗度值来猜，显然已经有很大的进步。确实，这说明SVM的表现相对于神经网络大致相当，仅仅稍差一点点。在后面的章节，我们将引入新的手段让神经网络的表现可以远远超过SVM。

然而这并不是故事的全部。对于scikit-learn向量机，10,000张图片中猜对9,435的识别率是对于默认设置而言的。SVM中有不少可以调节的参数，我们可以寻找那些可以让SVM表现更好的参数值。这里我就不具体去找了，有兴趣的可以参考 Andreas Mueller 的 这篇博客。Mueller 说明了通过优化SVM的参数值，有可能让其准确率超过98.5%。话句话说，参数合适的SVM在70个数字中只会认错1个。这个表现已经非常出色了！我们的神经网络可以做得更好吗？

事实上，确实可以更好。现在，对于解决MNIST问题，一个良好设计的神经网络，其表现超过所有其他方法，包括向量机。(机器学习社区tensorfly.cn原创, qq群号472113439) 目前（2013）的记录是 10,000张图片中认对9,979张。这个工作是由 Li Wan, Matthew Zeiler, Sixin Zhang, Yann LeCun, 和 Rob Fergus 实现的。后面我们会见到他们用过的大部分技术手段。对于那种程度的准确率，其表现已经和人眼相当了，某种意义上甚至比人眼更高了，因为 MNIST 中的不少图片对于人眼来说也很难认对，例如:

<img src="http://og1yl0w9z.bkt.clouddn.com/17-11-6/14811163.jpg" width=150 height=150 />

我相信你会同意这些图确实很难认！注意到MNIST数据集中还有这些难认的图片存在，神经网络居然可以精准识别10,000张除去21张以外的其他所有图片，这确实是非常出色的。编程的时候，我们通常会认为要解决类似于识别MNIST数字这样的复杂问题，需要一个复杂的算法。但即便是刚提到的Wan et al 文献，他们所使用的神经网络算法也是相当简单的。所有的复杂性都从训练数据中自动习得。某种意义上说，从我们的结果和其他更复杂文献中的结果中，对于许多问题，我们有这样的结论：

复杂的算法 ≤ 简单的学习算法 + 好的训练数据
