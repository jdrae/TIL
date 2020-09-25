<h1 id="unknown-length-string-from-standard-input">Unknown Length String from Standard Input</h1>
<p><em>Simple tricks but easy to FORGEEEEET! X-(</em></p>
<p>If you want to get an integer array, that storing each digits separately, what should you do? It’ll be easier if you know the length of array, and the given numbers has an space between them. But, life of programmer isn’t easy as you think. Let’s take a look for handling the problem: what if we don’t know the length, and the digits aren’t given separately?</p>
<h3 id="mathematical-solution">Mathematical Solution</h3>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token macro property">#<span class="token directive keyword">include</span> <span class="token string">&lt;stack&gt;</span></span>

stack<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> digits<span class="token punctuation">;</span>
<span class="token keyword">int</span> num<span class="token punctuation">;</span> cin <span class="token operator">&gt;&gt;</span> num<span class="token punctuation">;</span>
<span class="token keyword">while</span><span class="token punctuation">(</span>num <span class="token operator">!=</span> <span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
	digits<span class="token punctuation">.</span><span class="token function">push</span><span class="token punctuation">(</span>num<span class="token operator">%</span><span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	num <span class="token operator">/</span><span class="token operator">=</span> <span class="token number">10</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>It simply repeats the principle of ‘digits’, but the code is too long and needs external data structure only for handling inputs. Also to iterate elements, it should delete the former one. (You can use other data structures, but it won’t help that much efficiency.</p>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">while</span> <span class="token punctuation">(</span><span class="token operator">!</span>digits<span class="token punctuation">.</span><span class="token function">empty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	cout <span class="token operator">&lt;&lt;</span> digits<span class="token punctuation">.</span><span class="token function">top</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	digits<span class="token punctuation">.</span><span class="token function">pop</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="string-as-integer">String as Integer</h3>
<pre class=" language-c"><code class="prism ++ language-c">string s<span class="token punctuation">;</span>
cin <span class="token operator">&gt;&gt;</span> s<span class="token punctuation">;</span>

<span class="token comment">// show ouput</span>
<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> s<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span>
	<span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">"%c "</span><span class="token punctuation">,</span> s<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>The simplest way to get digits. You just save all numbers in string variable, regardless of size. Of course it has limit to save chars, and it varies based on the environment in which implements. Check the size with <code>std::string::max_size</code>. For me, the result was ‘2,147,483,647’  and it is the same range of signed integer ‘-2,147,483,648 ~ 2,147,483,647’ (if you don’t use negative numbers).</p>
<p>But, still has a problem. Some algorithms requires operation between numbers, but as we saved digits in char type, that’s impossible unless we convert the type one by one.</p>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span><span class="token operator">*</span> arr <span class="token operator">=</span> new <span class="token keyword">int</span><span class="token punctuation">[</span>s<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> s<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span>
	arr<span class="token punctuation">[</span>i<span class="token punctuation">]</span> <span class="token operator">=</span> s<span class="token punctuation">[</span>i<span class="token punctuation">]</span> <span class="token operator">-</span> <span class="token string">'0'</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="if-you-know-the-range-of-number">If you know the range of number</h3>
<ol>
<li>sprintf()</li>
</ol>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token macro property">#<span class="token directive keyword">include</span> <span class="token string">&lt;stdio.h&gt;</span></span>

<span class="token keyword">int</span> num<span class="token punctuation">;</span> <span class="token keyword">char</span> str<span class="token punctuation">[</span>LEN<span class="token punctuation">]</span><span class="token punctuation">;</span>
cin <span class="token operator">&gt;&gt;</span> num<span class="token punctuation">;</span>
<span class="token keyword">int</span> array_size <span class="token operator">=</span> <span class="token function">sprintf</span><span class="token punctuation">(</span>str<span class="token punctuation">,</span> <span class="token string">"%d"</span><span class="token punctuation">,</span> num<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>This solution is similar to former one. But first it receives integer number and then change it to char type array for dealing with digits.</p>
<ol start="2">
<li>scanf_s();</li>
</ol>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> arr<span class="token punctuation">[</span>LEN<span class="token punctuation">]</span><span class="token punctuation">;</span>
<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> LEN<span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token function">scanf_s</span><span class="token punctuation">(</span><span class="token string">"%1d"</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>arr<span class="token punctuation">[</span>i<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>But it’s only for strict length of number.</p>

