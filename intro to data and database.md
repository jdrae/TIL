<h1 id="intro-to-data-and-database">Intro to Data and Database</h1>
<h3 id="dikw-pyramid"><a href="https://en.wikipedia.org/wiki/DIKW_pyramid">DIKW pyramid</a></h3>
<p><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/06/DIKW_Pyramid.svg/330px-DIKW_Pyramid.svg.png" alt="enter image description here"><br>
Data itself is just a simple materials based on fact. Data can be processed to show meaningful patterns. And those information becomes Knowledge by generalization.</p>
<h3 id="type-of-data">Type of Data</h3>
<ol>
<li>
<p>Structured Data<br>
If data has schema, that defines structure of data fields, it is called Structured data. There is separate DB(dictionary) to save schemas.<br>
Usually used with SQL(structured query language)</p>
</li>
<li>
<p>Semi-Structured Data<br>
If schema is not separately managed, but exits inside of data, it is semi-structured data. This inherent schema calls metadata, and usually saved as file types.<br>
ex) HTML, XML, JSON, web log …</p>
</li>
<li>
<p>Unstructured Data<br>
Scrapped and crawled data is usually unstructured.</p>
</li>
</ol>
<h3 id="database">Database</h3>
<p>Behind <strong>file system</strong>, that is used before database, database rised in respond to the need of shared data. Although database can shared by many systems, it is <strong>integrated</strong> by minimizing <strong>redundancy</strong>. Other features are that database is <strong>operational</strong> and <strong>stored</strong>.</p>
<ul>
<li>advantages
<ul>
<li>minimize redundancy</li>
<li>prevent inconsistency</li>
<li>shared</li>
<li>standard</li>
<li>security</li>
<li>integrity</li>
</ul>
</li>
</ul>
<h3 id="level-architecture">3-Level Architecture</h3>
<p>ANSI/SPARC has defined 3-Level Architecture based on the different view of database. Each level has schema that uses different <strong>DDL</strong> and <strong>DML</strong>.<br>
Levels interact by mapping from each other.</p>
<ol>
<li>External Schema<br>
User View. Logically independent.</li>
<li>Conceptual Schema<br>
Integrated view.</li>
<li>Internal Schema<br>
Physically saved database</li>
</ol>
<h3 id="transaction-logical-unit-of-work">Transaction: logical unit of work</h3>
<ul>
<li>
<p>Status</p>
<ul>
<li>active</li>
<li>partial committed</li>
<li>failed</li>
<li>aborted</li>
<li>committed</li>
</ul>
</li>
<li>
<p>ACID</p>
<ul>
<li>atomicity: should succeed of failed</li>
<li>consistency: guarantee consistency of data</li>
<li>isolation: each transaction implements independently</li>
<li>durability: after it’s committed, data should’n be lost</li>
</ul>
</li>
<li>
<p>Operation in transaction</p>
<ul>
<li>restart, kill</li>
<li>commit</li>
<li>rollback</li>
</ul>
</li>
</ul>
<h3 id="type-of-database">Type of Database</h3>
<ol>
<li>ORDBMS</li>
<li>Data Warehouse(DW)<br>
focused on reading data.</li>
<li>NoSQL(Not Only SQL)
<ul>
<li>Key/value store: Oracle Coherence, Redis</li>
<li>Ordered K/V store: HBase, Cassandra</li>
<li>Document Store: MongoDB, CouchDB, Riak</li>
<li>Graph Store: Node4J</li>
</ul>
</li>
</ol>
<p><strong>CAP theory</strong> consistency, availability and partition tolerance cannot satisfied altogether, therefore need to choose two of it strategically in NoSQL.</p>
<p>While there is ACID features in RDBMS, NoSQL has <strong>BASE</strong> features: Basically Available, Soft-state Eventually consistent.</p>

