###不是数学公式###
早在 C 语言出现之前，用以进行科学计算的 Fortran 语言就有了。所以 C 语言也吸收了很多数学运算的功能。但是编程语言毕竟是动态的，而数学公式是静态的，其间有诸多不同。
####赋值还是相等####
当我初学 C 语言的时候，这样的代码让我想破头不知道什么意思。

	int a = 1;
	a = a + 1;

在数学上，这种等式是不可能成立的。之后才明白这个符号严格来说不是等号，而是**赋值号** (Assignment) ，意思是把等号右边的值计算出来，然后给左边的变量。因此，左边的变量必须是一个能够“接受”赋值的表达式。什么意思？比如说``a + 1 = a``这就是一个错误的赋值表达式，因为左边的``a + 1``不可能被赋一个值。在 C 语言中，这两种东西有专业的名字。可以被赋值的东西叫**左值** (L-value)，不能被赋值的东西叫**右值** (R-value). 然而这两个名词的翻译是失败的。因为所谓的左值，同样可以出现在表达式的右边。L-value 的 L 的原意是 location，即在内存中有一个对应的地址，可以修改。而 R 的意思是 read，表示可读。

	int a = 1;
	a + 1 = 15;

这段代码编译会产生这样的错误：

	a.c:6:11: error: expression is not assignable
    a + 1 = 15;
    ~~~~~ ^
	1 error generated.

一看就知道，不可赋值。所以说有些时候不要一报错就着急，自己可以先看看错误提示（貌似 VS 的错误提示还是中文的），然后可以在网上搜索一下，试图自己解决问题。要知道，对于一个程序员来说，调试能力甚至比编码能力更重要。

C 语言有一个非常奇特的特性，即赋值表达式本身是有值的。什么意思？比如说``printf("%d", a = 3);``这样的语句并没有错，这个参数返回的是**变量被赋值以后的值**。像这样的语句：

	int a, b, c;
	a = b = c = 3;

不是说像一个管道一样直接从最左边通到最右边。这个赋值语句是一部分一部分求值的。先计算并执行``c = 3``，然后整个返回结果 3，然后``b = 3``，以此类推。也就是说，加上括号以后，这才是赋值表达式的执行顺序：

	a = (b = (c = 3));

所谓教科书上写的，C 语言的赋值符号是“从右向左结合”，就是这个意思。

C 的赋值符号非常容易让人误解。像 Pascal 语言，赋值号就不是``=``，而是``:=``，意思就明白得多了。

那什么时候我们需要真正的数学意义上的等号呢？想一想，对，条件当中。我们判断两个值是否相等，这个时候需要用到“等号”而不是“赋值号”。C 语言的赋值号是``==``，两个等号在一起，注意不要写分开，虽然是两个字符但是是一个**符文** (Token). 这个符号在 C 语言中的结果是 0 或者 1，0 表示不对，1 表示正确。你可以这样试试：

	printf("%d\n", 1 == 1);

其实在 C 当中，**条件判断里所有非 0 的值都会和 1 同等看待**。发现什么了吗？对。像这样……

	if (a = 3) {
		/* xxx */
	} else {
		/* xxx */
	}

后面那个``else``是没有意义的，因为这个``if``语句里的条件永远是正确的。少打了一个等号，常事。有一些经验丰富的工程师会习惯把右值写在左边，比如``if (3 == a)``，这样如果少打了一个等号编译器就会报错。这不可不说是一个非常好的经验。但我个人总觉得这样写出来也太不好看了。令人欣慰的是，现代的编译器十分智能，对于上面的代码，它会给出这样的警告：

	a.c:6:11: warning: using the result of an assignment as a condition without
      parentheses [-Wparentheses]
    if (a = 3) {
        ~~^~~
	a.c:6:11: note: place parentheses around the assignment to silence this
      warning
    if (a = 3) {
          ^
        (    )
	a.c:6:11: note: use '==' to turn this assignment into an equality
      comparison
    if (a = 3) {
          ^
          ==
	1 warning generated.

当然，也不能说这种设计就是一无是处，它还是很简洁的。大概给我们留下的启示就是，尽量用新版本的编程环境。

####人是比不过机器的####
说到这个现代编译器的问题。确实，有很多高手在长期的编程实践中总结出来了一些很好用的技巧。但是这些技巧不能说是亘古不变的真理。随着电脑硬件、语言标准和开发环境的演进，有些所谓的奇技淫巧，不仅没有了用处，甚至还可能带来负面影响。

谭浩强的书笔者没怎么看（你会看一本六年前看过的烂书吗），但是印象很深的是，他在反复强调一些所谓的“写法”能够提升程序的功能。比如说把``a = a + 1``写成``a += 1``. （这种写法很简洁，是推荐的写法，笔者只是针对谭反复强调一些过时的所谓细枝末节举个例子） 怎么说呢，谭作为一个长者，在那个互联网并不发达的年代（他的书很多年了）能够写出这些作品来在中国大众里普及编程知识，很有意义。但是时代毕竟不同了，如果还是抱残守缺，对着这样一本内容落后的书籍反复啃，好意思？不是说要批评他，谭老都是八九十岁的人了，不可能指望他还能时时跟随新标准新技术的脚步，但是年轻的教育者不该反思一下吗？离了 VC6 就啥也不会的人不该反思一下吗？至于谭的书到底有什么不好，这里先不想多说，随便百度都能找到很多。

言归正传，编译技术好歹也是个前沿领域，编译过程中的优化是高级工程师乃至国内外大学学者研究的重要课题。前文的那种优化作为最基本的已经不说了。还有像``register``关键字、内联函数、``i++``和``++i``等等，编译器也会自动完成，我们添加反而是添乱。什么动作也没做的空循环也会被直接优化掉。特别是苹果的 _llvm_ 这种神器出现以后，可以说，一般水平的程序员手写汇编都没有编译器优化出来的程序的效率高。**代码更多是面向人而不是机器的，实际中开发效率的重要性远胜于运行效率。**所以，把眼光放在代码结构和算法上吧。

####诡异的自增####
C 语言有一个特别奇妙的运算符，即自加自减运算符，可放在前后，一共是四个系列。那么加减的意思自不必说，我们来看一下这个运算符放在变量前后的区别。

	int i = 0;
	printf("%d\n", i++);
	printf("%d\n", ++i);

结果很明确，一个是 0 ，一个是 2. 我们前面说过，C 语言里面连赋值语句都是有值的，这个自加自减运算当然也有。放后面，返回值是加之前的；放前面，返回值是加之后的。所以，顾名思义，C++ 的意思就是先当 C 用，之后也可以当 C 的进化版用。不过在我看来 C++ 现在有点名不符实了，倒是 Objective-C 确实是 C 的完全超集。

这个符号有什么用呢？大概就是简洁吧，比如这样：

	/* 我们假设some_array是一个字符数组 */
	int i = 0;
	while (some_array[i++] != '=');

意思是在一个字符数组中找到第一个等于“等号”字符后面的一个位置，跟标准库里的``strchr``函数类似。你看是不是很简洁？唔，插一句关于编码风格的东西，这里的``i``主要是用于循环变量的命名（跟数学上类似），以此类推多重循环就是``j``、``k``……其它时候就不要这么随意地命名了。变量命名的问题属于编码风格的内容，以后会提到的。至于某位老师无比推崇匈牙利命名法的行为……我在这里还是不予置评吧。

据说国内很多的考试里面都有要求计算``i = i+++++i;``这种题，我故意没打空格，因为题目里也不会有空格。如果说真的有考试要求我回答这种内容，我想我的选择是弃考。因为这个东西，按照 C 语言标准，根本就是**未定义** (Undefined) 的！每个编译器、操作系统乃至电脑可能都会不一样！事实上，这种代码在我的编译器上直接报错了：

	a.c:6:12: error: expression is not assignable
    i = i+++++i;
        ~~~^
	1 error generated.

而且就算我把空格分开，编译器也给了一个警告：

	a.c:6:25: warning: multiple unsequenced modifications to 'i'
      [-Wunsequenced]
    printf("%d\n", i = i++ + ++i);
                        ^    ~~
	1 warning generated.

用贴吧里某位前辈的话说：**如果一个人整天写的代码都像这样，我真的不知道有哪个公司愿意要你。**