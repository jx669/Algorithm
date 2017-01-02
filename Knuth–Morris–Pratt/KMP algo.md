
# Knuth–Morris–Pratt 字符串搜索算法

	如果你喜欢看视频，推荐去看下的youtube视频：
    - https://www.youtube.com/watch?v=5i7oKodCRJo&t=346s
    - https://www.youtube.com/watch?v=GTJr8OvyEVQ 
    本文中的伪代码出自第一个视频。写这篇文章因为视频过知识点可能有些过快，觉得还是文字更清楚。

test
##KMP算法的目的

KMP是一种用于搜索匹配字符串的算法。为什么我们需要一种算法来搜索字符串呢？for循环逐一匹配不就可以了吗？<br />
下面给了两个例子: <br />
第一个例子，逐一匹配没什么弊端。<br />
第二个例子我们聪明的大脑就会觉得逐一匹配好像有点问题。第二个例子的这类问题促使我们想找一种更好的算法。

###例1：<br />
	text = "ecdabcdabeab"
	pattern = "abcdabe" 
	需要求pattern在text中出现的index. 注意index[0]是1号位。

肉眼看过去很容易，pattern出现在 index = 3 的位置。text[3] = 'a'。<br />
这个情况，用暴力算法跟用KMP没区别，都是逐一比对。<br />

pattern[0] = 'a' <br />
text[0]= 'e' <br />
e != a <br />
所以，patern[0]不动，text计数位右移。<br />

下面我们要比较：text[1] vs pattern[0] <br />
而 text [1] ! = pattern[0] <br />
所以，text计数位继续右移。 <br />
text [2] ! = pattern[0] <br />
所以，text计数位继续右移。我们要比较text[3] ! = pattern[0]  <br />

而text[3] = ‘a' <br />
所以 pattern[0] = text[3]  <br />
此时，不仅text计数位右移，pattern计数位也右移。 <br />
然后我们发现text[3]开始的7位数 跟 pattern中的7位，完全一致，我们找到答案了: index = 3  <br />

在这个例子的情况下，暴力解法没什么弊端，text的计数位逐一右移就找到答案了。 <br />

###例2： 
	text = "ABDABDABDABC" 
	pattern = "ABDABC" 

这个例子中如果我们用暴力解法，就发现AB重复两次的优势没有发挥出来。从text[0]开始的6个字母里，我们发现
这六个字母不等于pattern中的6个字母。暴力解法就会全扔掉，再从text[1]开始比较text[1]-text[6]的6个字
母，看它们跟pattern的6个字母是否一致。

但我们如果用人脑想想，就觉得我们其实没必要这样。我们在判断从text[0]-text[5]的6个字母跟pattern不完全
一致的时候，我们没必要全扔掉。在第一轮比较中text[3]=A， text[4]=B，这部分比较信息是有用的。再进行下
一次比较的时候，我们没必要从text[1]=B开始，而可以直接从text[5]=D开始，比较text[5]跟pattern[2]是否一致。

**换句话说，我们就是想找到一个算法，这个算法让我们在合适的情况下（比如例2），在第一轮把字符串跟pattern
比对结束之后后，第二轮不必从text[1]开始再次比较pattern所有字母，可以 跳！一！下！**

KMP就是计算科学家们找出的一种能达到这个目的的一种最优算法。至于为什么这种算法最优呢？因为大家想一想这个问题
——每轮比较的时候避免完全的重复，我们应该可以想到不同的实施方法。KMP只是其中一种，但既然它这么出名，应该是
目前科学家们想出来的最优解法了。


##KMP的核心步骤
	两步：
	1. 算出prefix table 
	2. 用prefix table 去做校验。

我自己觉得第一步相对更绕，把prefix table的算法弄清楚了，后面一个操作起来比较容易。

## KMP伪代码：
### KMP-Prefix部分伪代码
这部分要达到的目的是给pattern中的每一个字母一个值，这些值组成一列：π[i]
例2中我们第二轮比较的时候是从text[5], pattern[2]开始比较，pattern[2]中的2, 就是跟我们这个prefix表的值直接相关。
这部分伪代码要达到的目的就是找到π[i]的值。

      t | 1 | 2 | 3 | 4 | 5 | 6  
    ----|---|---|---|---|---|--
    P(t)| A | B | D | A | B | C
    π[t]| 0 | 0 | 0 | 1 | 2 | 0
	(表中的t为序列号，仅方便人脑直观理解，并未出现在代码中。代码中的计数位用i，j表示，i j != t)

	m : Pattern (P)的长度 
	i : pattern中每轮比较的时候的计数位
	j : pattern中的计数位，j的范围是0到m-1 
	π[t] : 用来存pattern中字母对应移动值得数列, KMP-PREFIX计算的终极目标 

	begin 
		m <- |P|                 //m = length(P)
		π[1] <- 0                //pattern中第1位字母的π值为零
		i <- 0                   //i的值初始化为0
		for j = 2 upto m do      //把pattern中从第二位开始的字母跟第1位的字母进行比较，for循环的返回1值：π[j] <- i
	    	while i > 0 and P[i+1] ≠ P[j] do    
	    		i <- π[i]                        
	   		 if P[i+1] = P[j] then              
	    		i <- i +1                        
	 	π[j] <- i
	 	return π
	end 


### KMP-matcher部分伪代码
这部分要达到的目的找到第一次完全匹配的时候的计数位，也就是例2中的答案 3. <br />
但真正的难点依然是如何实现“省事”--也就是例2中，第一次对比text[0]失败后，第二次对比时，不用从text[1]开始，而是可以从text[5]开始。
不过如果你理解了prefix部分，matcher部分原理一样。

	KMP-MATCHER(T, P)
	begin
		n <- |T|
		m <- |P|
		π <- KMP-PREFIX(P)
		i <- 0
		for j = 1 upto m do 
			while i > 0 and P[i+1] ≠ T[j] do1 
				i <- π[j]                 //根据prefix部分算出的π值跳
				if P[i+1] = T[j] then
					i <- i+1              //找到pattern中的字母跟text中相等时，i++, j++
				if i = m then             
				OUTPUT(j-m)               
				i <- π(i)                 
	end 

##总结
本文一开始的疑问，字符串匹配用for 循环逐一匹配不行吗？</br>
经过分析，我们发现暴力算法可行。但是KMP避免了重复比对，更优。</br>
而KMP也用了for 循环，它的优化在于在for循环中嵌入了while和if命令. 

##源代码
本repo也给出了java和测试部分源代码，你可以随意更换JUnit中的代入值，更好的理解KMP算法。

##参考资料：
- https://www.youtube.com/watch?v=5i7oKodCRJo&t=346s 
- https://www.youtube.com/watch?v=GTJr8OvyEVQ
- https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm





