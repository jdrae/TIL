<h1 id="bitmask-instead-of-array">Bitmask instead of Array</h1>
<p>reference: <a href="http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&amp;mallGb=KOR&amp;barcode=9788966260546&amp;orderClick=LEa&amp;Kc=">Algorithmic Problem Solving Strategies Vol.2 Chap.16</a></p>
<p>Every CPU uses binary numbers to represent all of the data. So, using binary in calculation makes it faster than using integer number. And this kind of technique is called  <strong>bitmask</strong>.</p>

<table>
<thead>
<tr>
<th>calculation</th>
<th>code</th>
</tr>
</thead>
<tbody>
<tr>
<td>AND</td>
<td><code>a&amp;b</code></td>
</tr>
<tr>
<td>OR</td>
<td><code>a|b</code></td>
</tr>
<tr>
<td>XOR</td>
<td><code>a^b</code></td>
</tr>
<tr>
<td>NOT</td>
<td><code>~a</code></td>
</tr>
<tr>
<td>left shift</td>
<td><code>a&lt;&lt;b</code></td>
</tr>
<tr>
<td>right shift</td>
<td><code>a&gt;&gt;b</code></td>
</tr>
</tbody>
</table><h2 id="common-mistakes">Common mistakes</h2>
<h3 id="operator-precedence">Operator Precedence</h3>
<p>In C++ and Java, the precedence of bit operators are lower than comparison operators. To prevent mistakes, you should add parenthesis as precise as you can.</p>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> c <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token number">6</span> <span class="token operator">&amp;</span> <span class="token number">4</span> <span class="token operator">==</span> <span class="token number">4</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//wrong</span>
<span class="token keyword">int</span> d <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token number">6</span> <span class="token operator">&amp;</span> <span class="token number">4</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token number">4</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//correct</span>
</code></pre>
<h3 id="overflow">Overflow</h3>
<p>When using 64 bit integer as bitmask, the overflow often happens. Let’s see the code which checks whether bit number <code>b</code> is on at 64 bit unsigned bitmask <code>a</code>.</p>
<pre class=" language-c"><code class="prism ++ language-c">bool <span class="token function">isBitSet</span><span class="token punctuation">(</span><span class="token keyword">unsigned</span> <span class="token keyword">long</span> <span class="token keyword">long</span> a<span class="token punctuation">,</span> <span class="token keyword">int</span> b<span class="token punctuation">)</span><span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token punctuation">(</span>a <span class="token operator">&amp;</span> <span class="token punctuation">(</span><span class="token number">1</span><span class="token operator">&lt;&lt;</span>b<span class="token punctuation">)</span> <span class="token punctuation">)</span> <span class="token operator">&gt;</span><span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>In C++, <em>1</em> stands for signed 32 bit constant number, so when <code>b</code> is higher than 32, the operation <code>(1&lt;&lt;b)</code> causes overflow. (ex. 1 0000 0000 0000 … 0000)</p>
<p>To solve this problem, you should use <code>1ull</code> instead of <code>1</code>, to notify that the constant is unsigned 64 bit integer.</p>
<pre class=" language-c"><code class="prism ++ language-c">	<span class="token keyword">return</span> <span class="token punctuation">(</span>a <span class="token operator">&amp;</span> <span class="token punctuation">(</span><span class="token number">1ull</span><span class="token operator">&lt;&lt;</span>b<span class="token punctuation">)</span> <span class="token punctuation">)</span> <span class="token operator">&gt;</span><span class="token number">0</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="signed-integral-types">Signed Integral Types</h3>
<p><strong>Most significant bit</strong> is also correspond to the <strong>sign bit</strong> of a signed binary number.  If you want to use whole 32 bits, it’s better to use the <strong>unsigned binary number</strong>.</p>
<h3 id="left-shift">Left Shift</h3>
<p>When using left shift operation, it’s a trivial mistake to shift more than <em>N</em> times in <em>N</em> bit integer. Generally, you might think the result will be 0, but in C++ it doesn’t have definition of this operation. So it might cause different results according to the environment.</p>
<h2 id="implementation-of-array-using-bitmask">Implementation of Array using Bitmask</h2>
<p><em>N</em> bit integer can be an array that has <em>0</em> to <em>N-1</em> integer elements. Whether element <em>i</em> is in the array can be known by if the <em>2^i</em>bit is on. For example, an array {1, 3, 5, 8, 9} is represented as 810.<br>
<code>2^1 + 2^3 + 2^5 + 2^8 + 2^9</code> =  11 0010 1010(2)</p>
<p>In examples, we use 20 bit integer to represent the array that has arbitrary elements number between 0 to 19.</p>
<h3 id="empty-set--full-set">Empty set &amp; Full set</h3>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> emptySet <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token keyword">int</span> fullSet <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token number">1</span><span class="token operator">&lt;&lt;</span><span class="token number">20</span><span class="token punctuation">)</span> <span class="token operator">-</span><span class="token number">1</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="add">Add</h3>
<pre class=" language-c"><code class="prism ++ language-c">subset <span class="token operator">|</span><span class="token operator">=</span> <span class="token punctuation">(</span><span class="token number">1</span><span class="token operator">&lt;&lt;</span>e_No<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="check">Check</h3>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">if</span><span class="token punctuation">(</span>subset <span class="token operator">&amp;</span> <span class="token punctuation">(</span><span class="token number">1</span><span class="token operator">&lt;&lt;</span>e_No<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token comment">//don't use ==1</span>
	cout <span class="token operator">&lt;&lt;</span> <span class="token string">"e_No is in"</span> <span class="token operator">&lt;&lt;</span> endl<span class="token punctuation">;</span>
</code></pre>
<p>Beware of &amp; operation returns <em>0 or 1&lt;&lt;p</em>. It doesn’t return <em>true(1)</em>.</p>
<h3 id="delete">Delete</h3>
<pre class=" language-c"><code class="prism ++ language-c">subset <span class="token operator">-</span><span class="token operator">=</span> <span class="token punctuation">(</span><span class="token number">1</span><span class="token operator">&lt;&lt;</span>e_No<span class="token punctuation">)</span> <span class="token comment">//deprecated</span>
subset <span class="token operator">&amp;</span><span class="token operator">=</span> <span class="token operator">~</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token operator">&lt;&lt;</span>e_No<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>First line is deprecated because it works only when e_No element is already in the subset. Instead of subtracting the bit, it is safer to use AND operation with reversed bit. It always make the same result: remain <em>on</em> with other bits, and <em>off</em> with e_No element.</p>
<h3 id="toggle">Toggle</h3>
<pre class=" language-c"><code class="prism ++ language-c">subset <span class="token operator">^</span><span class="token operator">=</span> <span class="token punctuation">(</span><span class="token number">1</span><span class="token operator">&lt;&lt;</span>e_No<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="operation-between-two-arrays">Operation between two arrays</h3>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> added <span class="token operator">=</span> <span class="token punctuation">(</span>a<span class="token operator">|</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">int</span> intersection <span class="token operator">=</span> <span class="token punctuation">(</span>a<span class="token operator">&amp;</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">int</span> removed <span class="token operator">=</span> <span class="token punctuation">(</span>a<span class="token operator">&amp;</span><span class="token operator">~</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">int</span> toggled <span class="token operator">=</span> <span class="token punctuation">(</span>a<span class="token operator">^</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="size-of-arrays">Size of Arrays</h3>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> <span class="token function">bitCount</span><span class="token punctuation">(</span><span class="token keyword">int</span> x<span class="token punctuation">)</span><span class="token punctuation">{</span>
	<span class="token keyword">if</span><span class="token punctuation">(</span>x<span class="token operator">==</span><span class="token number">0</span><span class="token punctuation">)</span> <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> x<span class="token operator">%</span><span class="token number">2</span> <span class="token operator">+</span> <span class="token function">bitCount</span><span class="token punctuation">(</span>x<span class="token operator">/</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<ul>
<li>gcc/g++: <code>__builtin_popcount(subset)</code></li>
<li>Visual C++: <code>__popcnt(subset)</code></li>
<li>Java: <code>Integer.bitCount(subset)</code></li>
</ul>
<h3 id="find-minimum-element">Find Minimum element</h3>
<p>To find min element in the array, it requires to count number of <em>0</em> at the end. Because under the <strong>least significant bit</strong> there are only zeros.</p>
<ul>
<li>gcc/g++: <code>__builtin_ctz(subset)</code> //function can’t receive 0 for parameter.</li>
<li>Visual C++: <code>_BitScanForward(&amp;index, subset)</code></li>
<li>Java: <code>Integer.numberOfTrailingZeros(subset)</code></li>
</ul>
<p>If you want to find min element directly instead of finding its number, here is solution: use <strong>two’s complements</strong>.</p>
<pre class=" language-c"><code class="prism ++ language-c"><span class="token keyword">int</span> minE <span class="token operator">=</span> <span class="token punctuation">(</span>subset <span class="token operator">&amp;</span> <span class="token operator">-</span>subset<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>This technique is useful in <strong>Fenwick Tree</strong>.</p>
<h3 id="delete-minimum-element">Delete Minimum element</h3>
<pre class=" language-c"><code class="prism ++ language-c">subset <span class="token operator">&amp;</span><span class="token operator">=</span> <span class="token punctuation">(</span>subset <span class="token operator">-</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="traversing-subsets">Traversing subsets</h3>
<pre class=" language-c"><code class="prism ++ language-c">	<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">int</span> subset <span class="token operator">=</span> arr1<span class="token punctuation">;</span> subset<span class="token punctuation">;</span> subset<span class="token operator">=</span><span class="token punctuation">(</span><span class="token punctuation">(</span>subset<span class="token number">-1</span><span class="token punctuation">)</span><span class="token operator">&amp;</span>arr1<span class="token punctuation">)</span><span class="token punctuation">{</span>
	<span class="token comment">//subset is a subset of arr1</span>
<span class="token punctuation">}</span>
</code></pre>

