!SLIDE small

<pre class="hilight">

<code class="hljs"><span class="PreProc">use</span> <span class="Statement">v6;</span>

<span class="PreProc">use</span> <span class="Normal">JSON</span>::<span class="Normal">Pretty</span><span class="Statement">;</span>
<span class="PreProc">use</span> <span class="Normal">YAMLish</span><span class="Statement">;</span>

<span class="Statement">class</span> <span class="Normal">PackerGen</span> {
    <span class="Special">has</span> <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span> <span class="Statement">=</span> ()<span class="Statement">;</span>
    <span class="Special">has</span> <span class="Identifier">%</span><span class="Special">.</span><span class="Identifier">output</span> <span class="Statement">=</span> ()<span class="Statement">;</span>

    <span class="Comment"># Constructor taking an optional input file, not inherited.</span>
    <span class="Statement">submethod</span> <span class="Normal">BUILD</span>(<span class="Type">Str</span> <span class="Statement">:</span><span class="Identifier">$</span><span class="Identifier">yaml</span><span class="Statement">=</span><span class="Special">''</span>) {
        <span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">load</span>(<span class="Identifier">$</span><span class="Identifier">yaml</span>)<span class="Statement">;</span>
        <span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">generate</span>()<span class="Statement">;</span>
    }

    <span class="Comment"># Load a YAML file from stdin or the named file.</span>
    <span class="Statement">method</span> <span class="Normal">load</span>(<span class="Type">Str</span> <span class="Identifier">$</span><span class="Identifier">in</span>) {
        <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span> <span class="Statement">=</span> <span class="Normal">load-yaml</span>((<span class="Identifier">$</span><span class="Identifier">in</span> <span class="Statement">ne</span> <span class="Special">''</span>) <span class="Statement">??</span> <span class="Identifier">slurp</span>(<span class="Identifier">$</span><span class="Identifier">in</span>)
                <span class="Statement">!!</span> <span class="Identifier">$</span><span class="Special">*</span><span class="Identifier">IN</span><span class="Statement">.</span><span class="Normal">slurp-rest</span>)<span class="Statement">;</span>
    }
</code></pre>

!SLIDE small

<pre class="hilight">

<code class="hljs">    <span class="Comment"># Write the generated JSON to stdout or the given filename.</span>
    <span class="Statement">multi</span> <span class="Statement">method</span> <span class="Normal">save</span>(<span class="Type">Str</span> <span class="Identifier">$</span><span class="Identifier">out</span> <span class="PreProc">where</span> { <span class="Identifier">$</span><span class="Identifier">out</span> <span class="Statement">eq</span> <span class="Special">''</span> }) {
        <span class="Identifier">say</span> <span class="Normal">to-json</span>(<span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">output</span>)<span class="Statement">;</span>
    }

    <span class="Statement">multi</span> <span class="Statement">method</span> <span class="Normal">save</span>(<span class="Type">Str</span> <span class="Identifier">$</span><span class="Identifier">out</span>) {
        <span class="Comment"># to-json doesn't have a final newline</span>
        <span class="Normal">spurt</span>(<span class="Identifier">$</span><span class="Identifier">out</span><span class="Statement">,</span> <span class="Normal">to-json</span>(<span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">output</span>) <span class="Statement">~</span> <span class="Special">&quot;</span><span class="Special">\n</span><span class="Special">&quot;</span>)<span class="Statement">;</span>
    }

    <span class="Comment"># If a config_template element exists, load that content and</span>
    <span class="Comment"># overlay the supplied data. Otherwise just return the data.</span>
    <span class="Statement">multi</span> <span class="Statement">method</span> <span class="Normal">expand_entry</span>(<span class="Identifier">%</span><span class="Identifier">data</span>
            <span class="PreProc">where</span> { <span class="Identifier">%</span><span class="Identifier">data</span><span class="Special">&lt;</span><span class="Constant">config_template</span><span class="Special">&gt;</span><span class="Statement">:</span><span class="Constant">exists</span> } <span class="Statement">--&gt;</span> <span class="Type">Hash</span>) {
        <span class="Special">my</span> <span class="Identifier">%</span><span class="Identifier">new</span> <span class="Statement">=</span> <span class="Identifier">%</span><span class="Identifier">data</span><span class="Special">&lt;</span><span class="Constant">config_template</span><span class="Special">&gt;</span><span class="Statement">:</span><span class="Constant">delete</span><span class="Statement">.</span><span class="Identifier">map</span>({
                <span class="Normal">load-yaml</span>(<span class="Identifier">slurp</span>(<span class="Identifier">$</span><span class="Special">^</span><span class="Identifier">a</span>)) })<span class="Statement">;</span>
        <span class="Statement">%</span>(<span class="Identifier">%</span><span class="Identifier">new</span><span class="Statement">,</span> <span class="Identifier">%</span><span class="Identifier">data</span>)<span class="Statement">;</span>
    }

    <span class="Statement">multi</span> <span class="Statement">method</span> <span class="Normal">expand_entry</span>(<span class="Identifier">%</span><span class="Identifier">data</span> <span class="Statement">--&gt;</span> <span class="Type">Hash</span>) {
        <span class="Identifier">%</span><span class="Identifier">data</span><span class="Statement">;</span>
    }
</code></pre>

!SLIDE small

<pre class="hilight">

<code class="hljs">    <span class="Comment"># Process templates and add the name key.</span>
    <span class="Statement">method</span> <span class="Normal">build</span>(<span class="Type">Pair</span> (<span class="Statement">:</span><span class="Constant">key</span>(<span class="Statement">:</span><span class="Identifier">$</span><span class="Identifier">name</span>)<span class="Statement">,</span> <span class="Statement">:</span><span class="Constant">value</span>(<span class="Statement">:</span><span class="Identifier">$</span><span class="Identifier">data</span>)) <span class="Statement">--&gt;</span> <span class="Type">Hash</span>) {
        <span class="Statement">%</span>(<span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">expand_entry</span>(<span class="Identifier">$</span><span class="Identifier">data</span>)<span class="Statement">,</span> <span class="Special">'</span><span class="Constant">name</span><span class="Special">'</span> <span class="Statement">=&gt;</span> <span class="Identifier">$</span><span class="Identifier">name</span>)<span class="Statement">;</span>
    }

    <span class="Comment"># Process templates and add the global override definition if</span>
    <span class="Comment"># there is no override in the provisioner.</span>
    <span class="Statement">method</span> <span class="Normal">provision</span>(<span class="Identifier">%</span><span class="Identifier">prov</span> <span class="Statement">--&gt;</span> <span class="Type">Hash</span>) {
        <span class="Special">my</span> <span class="Identifier">%</span><span class="Identifier">new</span> <span class="Statement">=</span> <span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">expand_entry</span>(<span class="Identifier">%</span><span class="Identifier">prov</span>)<span class="Statement">;</span>
        <span class="Statement">if</span> (<span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">provisioner_override</span><span class="Special">&gt;</span><span class="Statement">:</span><span class="Constant">exists</span> <span class="Statement">&amp;&amp;</span>
                <span class="Statement">!</span>(<span class="Identifier">%</span><span class="Identifier">prov</span><span class="Special">&lt;</span><span class="Constant">override</span><span class="Special">&gt;</span><span class="Statement">:</span><span class="Constant">exists</span>)) {
            <span class="Identifier">%</span><span class="Identifier">new</span><span class="Special">&lt;</span><span class="Constant">override</span><span class="Special">&gt;</span> <span class="Statement">=</span> <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">provisioner_override</span><span class="Special">&gt;</span><span class="Statement">;</span>
        }
        <span class="Identifier">%</span><span class="Identifier">new</span><span class="Statement">;</span>
    }

    <span class="Comment"># Process templates for a sequence or a single postprocessor.</span>
    <span class="Statement">multi</span> <span class="Statement">method</span> <span class="Normal">postproc</span>(<span class="Identifier">@</span><span class="Identifier">post</span> <span class="Statement">--&gt;</span> <span class="Type">Seq</span>) {
        <span class="Identifier">@</span><span class="Identifier">post</span><span class="Statement">.</span><span class="Identifier">map</span>({ <span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">expand_entry</span>(<span class="Identifier">%</span><span class="Special">^</span><span class="Identifier">a</span>) })<span class="Statement">;</span>
    }

    <span class="Statement">multi</span> <span class="Statement">method</span> <span class="Normal">postproc</span>(<span class="Identifier">%</span><span class="Identifier">post</span> <span class="Statement">--&gt;</span> <span class="Type">Hash</span>) {
        <span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">expand_entry</span>(<span class="Identifier">%</span><span class="Identifier">post</span>)<span class="Statement">;</span>
    }
</code></pre>

!SLIDE small

<pre class="hilight">

<code class="hljs">    <span class="Statement">method</span> <span class="Normal">generate</span>(<span class="Statement">--&gt;</span> <span class="Type">Hash</span>) {
        <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">output</span><span class="Special">&lt;</span><span class="Constant">builders</span><span class="Special">&gt;</span> <span class="Statement">=</span> <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">builders</span><span class="Special">&gt;</span><span class="Statement">.</span><span class="Identifier">map</span>({
                <span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">build</span>(<span class="Identifier">$</span><span class="Special">^</span><span class="Identifier">a</span>) })<span class="Statement">;</span>

        <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">output</span><span class="Special">&lt;</span><span class="Constant">provisioners</span><span class="Special">&gt;</span> <span class="Statement">=</span> <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">provisioners</span><span class="Special">&gt;</span><span class="Statement">.</span><span class="Identifier">map</span>({
            <span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">provision</span>(<span class="Identifier">$</span><span class="Special">^</span><span class="Identifier">a</span>) })
              <span class="Statement">if</span> (<span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">provisioners</span><span class="Special">&gt;</span><span class="Statement">:</span><span class="Constant">exists</span>)<span class="Statement">;</span>

        <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">output</span><span class="Special">&lt;</span><span class="Constant">post-processors</span><span class="Special">&gt;</span> <span class="Statement">=</span> <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">post-processors</span><span class="Special">&gt;</span><span class="Statement">.</span><span class="Identifier">map</span>({
            <span class="Identifier">self</span><span class="Statement">.</span><span class="Normal">postproc</span>(<span class="Identifier">$</span><span class="Special">^</span><span class="Identifier">a</span>) })
              <span class="Statement">if</span> (<span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">post-processors</span><span class="Special">&gt;</span><span class="Statement">:</span><span class="Constant">exists</span>)<span class="Statement">;</span>

        <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">output</span><span class="Special">&lt;</span><span class="Constant">description</span><span class="Special">&gt;</span> <span class="Statement">=</span> <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">description</span><span class="Special">&gt;</span>
            <span class="Statement">if</span> (<span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">description</span><span class="Special">&gt;</span><span class="Statement">:</span><span class="Constant">exists</span>)<span class="Statement">;</span>

        <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">output</span><span class="Special">&lt;</span><span class="Constant">variables</span><span class="Special">&gt;</span> <span class="Statement">=</span> <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">variables</span><span class="Special">&gt;</span>
            <span class="Statement">if</span> (<span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">cfg</span><span class="Special">&lt;</span><span class="Constant">variables</span><span class="Special">&gt;</span><span class="Statement">:</span><span class="Constant">exists</span>)<span class="Statement">;</span>

        <span class="Identifier">%</span><span class="Special">!</span><span class="Identifier">output</span><span class="Statement">;</span>
    }

}
</code></pre>

!SLIDE small

<pre class="hilight">

<code class="hljs"><span class="Comment"># --in=input.yaml --out=output.json</span>
<span class="Statement">sub</span> <span class="Normal">MAIN</span>(<span class="Type">Str</span> <span class="Statement">:</span><span class="Identifier">$</span><span class="Identifier">in</span><span class="Statement">=</span><span class="Special">''</span><span class="Statement">,</span> <span class="Type">Str</span> <span class="Statement">:</span><span class="Identifier">$</span><span class="Identifier">out</span><span class="Statement">=</span><span class="Special">''</span>) {
    <span class="Special">my</span> <span class="Identifier">$</span><span class="Identifier">pg</span> <span class="Statement">=</span> <span class="Normal">PackerGen</span><span class="Statement">.</span><span class="Identifier">new</span>(<span class="Constant">yaml</span> <span class="Statement">=&gt;</span> <span class="Identifier">$</span><span class="Identifier">in</span>)<span class="Statement">;</span>
    <span class="Identifier">$</span><span class="Identifier">pg</span><span class="Statement">.</span><span class="Normal">save</span>(<span class="Identifier">$</span><span class="Identifier">out</span>)<span class="Statement">;</span>
}
</code></pre>

