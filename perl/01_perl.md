!SLIDE bullets incremental
# Perl6s of Wisdom
* Rakudo Star ships with JSON::Tiny
* JSON::Pretty gives you formatted output
* YAML is unfinished, so use YAMLish

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

