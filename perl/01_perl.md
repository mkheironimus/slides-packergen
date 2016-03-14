!SLIDE bullets incremental
# Perls of Wisdom
* Rakudo Star ships with JSON::Tiny
* JSON::Pretty gives you formatted output
* YAML is unfinished, so use YAMLish

!SLIDE bullets incremental
# Modules, you say?
* Modules you expect to use may be unfinished
* Documentation / examples may be lacking
* YAMLish output is not very mature - fails to quote some strings

!SLIDE
# Building a YAML starting point

<pre class="hilight">

<code class="hljs"><span class="PreProc">use</span> <span class="Normal">JSON</span>::<span class="Normal">Pretty</span><span class="Statement">;</span>
<span class="PreProc">use</span> <span class="Normal">YAMLish</span><span class="Statement">;</span>

<span class="Special">my</span> <span class="Identifier">$</span><span class="Identifier">f</span> <span class="Statement">=</span> <span class="Identifier">slurp</span>(<span class="Special">'</span><span class="Constant">centos.json</span><span class="Special">'</span>)<span class="Statement">;</span>
<span class="Special">my</span> <span class="Identifier">$</span><span class="Identifier">cfg</span> <span class="Statement">=</span> <span class="Normal">from-json</span>(<span class="Identifier">$</span><span class="Identifier">f</span>)<span class="Statement">;</span>
<span class="Special">my</span> <span class="Identifier">$</span><span class="Identifier">yaml</span> <span class="Statement">=</span> <span class="Normal">save-yaml</span>(<span class="Identifier">$</span><span class="Identifier">cfg</span>)<span class="Statement">;</span>
<span class="Identifier">say</span> <span class="Identifier">$</span><span class="Identifier">yaml</span><span class="Statement">;</span>
</code></pre>

