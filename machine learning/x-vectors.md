<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>x-vectors</title>
  <link rel="stylesheet" href="https://stackedit.io/style.css" />
</head>

<body class="stackedit">
  <div class="stackedit__html"><h1 id="x-vectors-robust-dnn-embeddings-for-speaker-recognition">X-Vectors: Robust DNN Embeddings for Speaker Recognition</h1>
<p>a summary of the paper</p>
<h2 id="abstract">Abstract</h2>
<ul>
<li>data augmentation to improve performance of DNN</li>
<li>DNN maps variable-length utterances to fixed-dimensional embeddings: call as x-vectors
<ul>
<li>embeddings leverage large-scale training datasets (better than i-vectors)</li>
<li>but, it’s challenging to collect data</li>
<li>so use data augmentation - added noise and reverberation <em>반향</em></li>
</ul>
</li>
<li>augmentation is beneficial in the <strong>PLDA</strong>(probabilistic linear discriminant analysis) classifier
<ul>
<li>but not helpful in the i-vector extractor</li>
<li>however on the evaluation datasets, x-vector achieve superior performance</li>
</ul>
</li>
</ul>
<h2 id="introduction">Introduction</h2>
<ul>
<li>x-vectors: the representations that are extracted from DNN and used like i-vectors</li>
<li>to show augmenting the training data is a effective strategy</li>
<li>i-vectors
<ul>
<li>the standard approach consists of a UBM(universial background model)</li>
<li>and a large projection matrix T <em>투영행렬</em> (learned in an unsupervised way)</li>
<li>projection maps: high-dimensional statistics (from the UBM) into low-dimensional representation → i-vectors</li>
<li>PLDA classifier is used to compare i-vectors, enable speaker decisions</li>
</ul>
</li>
<li>DNN are trained as <em>acoustic models</em> for <strong>ASR</strong>(automatic speech recognition) then used to enhance <em>phonetic modeling</em> in the i-vector UBM
<ul>
<li>either ASR DNN replace <strong>GMM</strong>(Gaussian mixture model)</li>
<li>or bottleneck features are extracted from the DNN and combined with acoustic features</li>
</ul>
</li>
<li>if the ASR DNN is trained on <em>in-domain data</em> the improvement is substantial
<ul>
<li>the need for <em>transcribed training data</em></li>
</ul>
</li>
<li>early neural networks
<ul>
<li>to separate speakers, frame-level representations for Gaussian speaker models</li>
<li>Heigold: jointly learns an embedding with a similarity metric to compare pairs of embeddings</li>
<li>Snyder: end-to-end. adapted to a text-independent application and inserted pooling layer to handle variable-length segments</li>
<li>end-to-end into two parts:
<ul>
<li>a DNN to produce embeddings</li>
<li>a separately trained classifier to compare them</li>
<li>used: length-normalization, PLDA scoring, domain adaptation techniques</li>
</ul>
</li>
</ul>
</li>
<li>DNN embedding performance is highly scalable with the data (large datasets)
<ul>
<li>however, recent studies have shown promising performance with publicly available speaker recognition corpora <em>말뭉치</em></li>
</ul>
</li>
</ul>
<h2 id="speaker-recognition-systems">Speaker Recognition Systems</h2>
<ul>
<li>two i-vector baselines &amp; the DNN x-vector system</li>
</ul>
<h3 id="acoustic-i-vector">Acoustic i-vector</h3>
<ul>
<li>traditional i-vector system based on the GMM-UBM recipe</li>
<li>features: 20 MFCCs - frame-length 25ms, mean-normalized over  a sliding window(3s)</li>
<li>delta and acceleration appended - creates 60 dimension feature vectors</li>
<li>energy-based SAD(speech activity detection) selects features</li>
<li>UBM is a 2048 component full-covariance GMM</li>
<li>600 dimensional i-vector extractor and PLDA for scoring</li>
</ul>
<h3 id="phonetic-bottleneck-i-vector">Phonetic bottleneck i-vector</h3>
<ul>
<li>this i-vector system incorporates phonetic bottleneck features(<strong>BNF</strong>) from an ASR DNN acoustic model</li>
<li>DNN is a time-delay acoustic model with <em>p-norm nonlinearities</em></li>
<li><em>penultimate layer</em> is replaced with a 60 dimensional linear bottleneck layer</li>
<li>excluding softmax output layer, DNN has 9.2 million parameters</li>
</ul>
<h3 id="the-x-vector-system">The x-vector system</h3>
<ol>
<li>first five layers operate on speech frames, with a small temporal context centered at the current frame t</li>
<li>statistics pooling layer aggregates all T frame-level outputs from layer frame5
<ul>
<li>and computes its mean and standard deviation</li>
<li>aggregates information across the time dimension - so subsequent layers operate on the entire segment</li>
</ul>
</li>
<li>mean and standard deviation concatenated together and propagated through segment-level layers, and softmax output layer. (nonlinearities are ReLUs)</li>
</ol>
<ul>
<li>DNN is trained to classify the N speakers in the training data</li>
<li>training example consists of a chunk of speech features (3s avg.) &amp; corresponding speaker level</li>
<li>after training, embeddings are extracted from the affine component of layer segment6 (excluding sofmtmax output layer and segment 7) → total of 4.2 million parameters</li>
</ul>
<h3 id="plda-classifier">PLDA classifier</h3>
<ul>
<li>the representations(x-vectors or i-vectors) are centered, and projected using <strong>LDA</strong></li>
<li>LDA dimension was tuned on the SITW development set to 200 for i-vectors and 150 for x-vectors</li>
<li>after dimensionality reduction, the representations are length-normalized and modeled by PLDA
<ul>
<li>normalized using adaptive <em>s-norm</em></li>
</ul>
</li>
</ul>
</div>
</body>

</html>
