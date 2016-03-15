!SLIDE bullets
# The Script
* Minimal testing, but It Works For Me (TM)
* No error handling
    * Perl will tell you if files can't be read or written
    * YAMLish will complain if the input is not valid YAML
    * Packer will tell you if the JSON output is wrong

~~~SECTION:notes~~~

At this stage error handling is more of a "neatness counts" thing than a real
need. Plus I'm still in the "how does this work/look" stage with Perl 6.

YAMLish doesn't say what's wrong, just that parsing failed.

Since the script does almost no transformation, you can use Packer's error on
your YAML just as well as on the JSON.

~~~ENDSECTION~~~

!SLIDE bullets incremental
# Perls of Wisdom
* Rakudo Star ships with JSON::Tiny
* JSON::Pretty gives you formatted output
* YAML is unfinished, so use YAMLish

~~~SECTION:notes~~~

Lessons learned.

JSON::Tiny should work, but it's untested.

~~~ENDSECTION~~~

!SLIDE bullets incremental
# Modules, you say?
* Modules you expect to use may be unfinished
* Documentation / examples may be lacking
* YAMLish output fails to quote some strings

~~~SECTION:notes~~~

You can't yet assume that everything will "just work" in Perl 6.

I filed an issue on github about quoting numeric strings and the YAMLish author
said he needed to have it quote a lot more, and that output was generally less
mature than input.

~~~ENDSECTION~~~

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

~~~SECTION:notes~~~

Done for the initial file, but don't forget the quoting problem.

~~~ENDSECTION~~~
