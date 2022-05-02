---
keywords: fastai
description: "fastai and related projects are great resources but they might lack documentation, examples, and tips from the community. Therefore this is my small addition to fastai community."
title: "Downloading datasets in Python like a pro using fastai's fastdownload"
toc: true
branch: master
badges: false
comments: true
categories: [fastai, python, ]
image: images/mac-minimal.jpg
hide: false
search_exclude: false
nb_path: _notebooks/2022-05-02-fastdownload.ipynb
layout: notebook
---

<!--
#################################################
### THIS FILE WAS AUTOGENERATED! DO NOT EDIT! ###
#################################################
# file to edit: _notebooks/2022-05-02-fastdownload.ipynb
-->

<div class="container" id="notebook-container">
        
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p><img src="/biology-crash-course/images/copied_from_nb/my_icons/mac-minimal.jpg" alt=""></p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Introduction">Introduction<a class="anchor-link" href="#Introduction"> </a></h2><p>Being in a Deep Learning field, you most probably know Jeremy Howard and his <code>fastai</code> <a href="#resources">[1]</a> and projects related to it. One of these related projects is <code>fastdownload</code> <a href="#resources">[1]</a> that I found only recently (even tho it is out almost a year) and tried to use it. It was not without problems, but I found it useful and I decided to share my findings and enhancements.</p>
<blockquote><p>If you have datasets or other archives that you want to make available to your users and ensure they always have the latest versions and that they are downloaded correctly, <code>fastdownload</code> can help.</p>
</blockquote>
<p><code>fastdownload</code> can handle multiple URLs pointing at the same archive and ensure that users always get the latest version of the archive. Getting a dataset is as easy as calling the <code>FastDownload.get</code> method and passing the URL of the archive. The URL will be downloaded and extracted to the specified location and the path to the extracted file will be returned.</p>
<p>For instance, <code>fastai</code> uses <code>fastdownload</code> to provide access to datasets for deep learning. <code>fastai</code> users can download and extract them with a single command, using the return value to access the files. The files are automatically placed in appropriate subdirectories of a <code>.fastai</code> folder in the user's home dir. If a dataset is updated, users are informed the next time they use the dataset, and the latest version is automatically downloaded and extracted for them.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Basic-example">Basic example<a class="anchor-link" href="#Basic-example"> </a></h2><p>As described above, the most common usage for <code>fastdownload</code> is to download a dataset from the internet. Datasets are usually formatted as a bunch of files separated into directories based on the class of the data in a file. All of these class directories are then placed in a single dataset directory that is compressed. So usually we have an URL to the  compressed dataset and we want to do the following:</p>
<ul>
<li>check if we already have the dataset, if it is up to date and not corrupted,</li>
<li>download the dataset, if needed,</li>
<li>extract the dataset, if needed,</li>
<li>have a path to the extracted dataset.</li>
</ul>
<p>With <code>fastdownload</code> we can do all of this in two lines (after installing a package and importing it).</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="o">!</span>pip uninstall -y -q fastdownload
<span class="o">!</span>pip install -q fastdownload
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">import</span> <span class="nn">fastdownload</span>
<span class="kn">from</span> <span class="nn">fastdownload</span> <span class="kn">import</span> <span class="n">FastDownload</span>

<span class="n">d</span> <span class="o">=</span> <span class="n">FastDownload</span><span class="p">(</span><span class="n">module</span><span class="o">=</span><span class="n">fastdownload</span><span class="p">)</span>
<span class="n">path</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;https://s3.amazonaws.com/fast-ai-sample/mnist_tiny.tgz&#39;</span><span class="p">)</span>
<span class="n">path</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">



<div class="output_text output_subarea output_execute_result">
<pre>Path(&#39;/home/jovyan/.fastdownload/data/mnist_tiny&#39;)</pre>
</div>

</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>Download seems to be successful and we can also see that <code>path</code> is pointing to <code>mnist_tiny/</code> directory that is stored in <code>.fastdownload/data/</code> directory that is stored in my home directory.</p>
<p>This is it, you now know how to download datasets in Python using <code>fastai</code>'s <code>fastdownload</code>. But if you are interested in more advanced usage, keep reading.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Parametrization">Parametrization<a class="anchor-link" href="#Parametrization"> </a></h2><p>In Basic example we pretty much used default parameters for <code>fastdownload</code> but we can also parametrize it. When creating a <code>FastDownload</code> object we can specify four parameters: <code>base</code>, <code>archive</code>, <code>data</code> and <code>module</code>. First three parameters are connected with location where files will be downloaded and extracted. <code>base</code> is a path to parent directory where it all will live in. Archives are then saved to <code>{base}/{archive}</code>, and extracted to <code>{base}/{data}</code>. When no values are specified, default values are as follows:</p>
<ul>
<li><code>base = ~/.fastdownload</code></li>
<li><code>archive = archive</code></li>
<li><code>data = data</code></li>
</ul>
<p>This is exactly matching with our <code>path</code> in Basic example.</p>
<p>Last parameter is <code>module</code> that is useful when downloading datasets connected with some package. One part of downloading is a check if the dataset is not corrupted. To do so, we need to have access to true values of file size and hash. <code>fastdownload</code> is using file <code>download_checks.py</code> for this and it is expected to be located in the same directory as a module we specified using <code>module</code> parameter. Author of a package and datasets should provide this file.</p>
<p>When we look back at the Basic example, we specified <code>module=fastdownload</code> which was pretty useless since <code>fastdownload</code> module contains no <code>download_checks.py</code> file. There is a small bug in the current distribution of <code>fastdownload</code> package that causes it to fail when <code>module</code> is not specified. Fix is already in <code>fastdownload</code>'s github but it is not released yet. Therefore we need to specify some <code>module</code> parameter for now even tho it is not really used.</p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Enhancement-1:-Working-with-a-compressed-files">Enhancement 1: Working with a compressed files<a class="anchor-link" href="#Enhancement-1:-Working-with-a-compressed-files"> </a></h2><p>Without doubts, <code>fastdownload</code> is a good tool for downloading datasets. However, I stumbled upon a problem when I tried to work with a compressed file (not a compressed directory). This problem might not be apparent at first because first downloading and unpacking of a compressed file is without a problem.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="n">path</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;https://silkdb.bioinfotoolkits.net/__resource/Bombyx_mori/download/cds.fa.tar.gz&#39;</span><span class="p">)</span>
<span class="n">path</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">



<div class="output_text output_subarea output_execute_result">
<pre>Path(&#39;/home/jovyan/.fastdownload/data/cds.fa&#39;)</pre>
</div>

</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>Problem shows when removing file with <code>d.rm(url)</code> or using <code>force=True</code> parameter in <code>d.get()</code> (which forces new download of file even it was downloaded before).</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="k">try</span><span class="p">:</span>
    <span class="n">path</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;https://silkdb.bioinfotoolkits.net/__resource/Bombyx_mori/download/cds.fa.tar.gz&#39;</span><span class="p">,</span> <span class="n">force</span><span class="o">=</span><span class="kc">True</span><span class="p">)</span>
<span class="k">except</span> <span class="ne">Exception</span> <span class="k">as</span> <span class="n">e</span><span class="p">:</span>
    <span class="nb">print</span><span class="p">(</span><span class="n">e</span><span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">


<div class="output_html rendered_html output_subarea ">

<style>
    /* Turns off some styling */
    progress {
        /* gets rid of default border in Firefox and Opera. */
        border: none;
        /* Needs to be in here for Safari polyfill so background images work as expected. */
        background-size: auto;
    }
    .progress-bar-interrupted, .progress-bar-interrupted::-webkit-progress-bar {
        background: #F44336;
    }
</style>

</div>

</div>

<div class="output_area">


<div class="output_html rendered_html output_subarea ">

    <div>
      <progress value='5578752' class='' max='5577257' style='width:300px; height:20px; vertical-align: middle;'></progress>
      100.03% [5578752/5577257 00:03<00:00]
    </div>
    
</div>

</div>

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">
<pre>[Errno 20] Not a directory: Path(&#39;/home/jovyan/.fastdownload/data/cds.fa&#39;)
</pre>
</div>
</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>The file was downloaded but unpacking failed. More precisely, removing an old uncompressed file before unpacking a new one failed. <code>fastdownload</code> is expecting only directories as it is the most used format for datasets. I understand that downloading compressed files is not a primary use case for <code>fastdownload</code> but I would like to use it for it. Luckily, I was able to add support for compressed files into <code>fastdownload</code>. Enhancement lives in a fork of <code>fastdownload</code> on my github <a href="#resources">[3]</a> for now but I will try to get it into <code>fastdownload</code>. It can be easily installed as a python package using the <code>pip</code> command.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="o">!</span>pip uninstall -y -q fastdownload
<span class="o">!</span>pip install -q git+https://github.com/katarinagresova/fastdownload
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>Now we need to import the newly installed <code>fastdownload</code> module and create a <code>FastDownload</code> object again.
{% include note.html content='we don&#8217;t have to specify <code>module</code> parameter here since we are installing a version with a fix already in it. However, if you would download a dataset where <code>download_checks.py</code> file is created, you should specify a <code>module</code> where it is located.' %}</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">fastdownload</span> <span class="kn">import</span> <span class="n">FastDownload</span>

<span class="n">d</span> <span class="o">=</span> <span class="n">FastDownload</span><span class="p">()</span>
<span class="n">path</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;https://silkdb.bioinfotoolkits.net/__resource/Bombyx_mori/download/cds.fa.tar.gz&#39;</span><span class="p">,</span> <span class="n">force</span><span class="o">=</span><span class="kc">True</span><span class="p">)</span>
<span class="n">path</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">


<div class="output_html rendered_html output_subarea ">

<style>
    /* Turns off some styling */
    progress {
        /* gets rid of default border in Firefox and Opera. */
        border: none;
        /* Needs to be in here for Safari polyfill so background images work as expected. */
        background-size: auto;
    }
    .progress-bar-interrupted, .progress-bar-interrupted::-webkit-progress-bar {
        background: #F44336;
    }
</style>

</div>

</div>

<div class="output_area">


<div class="output_html rendered_html output_subarea ">

    <div>
      <progress value='5578752' class='' max='5577257' style='width:300px; height:20px; vertical-align: middle;'></progress>
      100.03% [5578752/5577257 00:03<00:00]
    </div>
    
</div>

</div>

<div class="output_area">



<div class="output_text output_subarea output_execute_result">
<pre>Path(&#39;/home/jovyan/.fastdownload/data/cds.fa&#39;)</pre>
</div>

</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>We can also verify that our <code>path</code> is really pointing to the extracted file and not a directory.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="nb">print</span><span class="p">(</span><span class="n">path</span><span class="o">.</span><span class="n">is_file</span><span class="p">())</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">
<pre>True
</pre>
</div>
</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Enhancement-2:-Adding-support-for-new-compression-formats">Enhancement 2: Adding support for new compression formats<a class="anchor-link" href="#Enhancement-2:-Adding-support-for-new-compression-formats"> </a></h2><p>While trying to use <code>fastdownload</code> I found another use case that was not supported - downloading a compressed file with a <code>.gz</code> extension.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="k">try</span><span class="p">:</span>
    <span class="n">path</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;http://ftp.ensembl.org/pub/release-106/mysql/regulation_mart_106/dmelanogaster_external_feature__external_feature__main.txt.gz&#39;</span><span class="p">,</span> <span class="n">force</span><span class="o">=</span><span class="kc">True</span><span class="p">)</span>
<span class="k">except</span> <span class="ne">Exception</span> <span class="k">as</span> <span class="n">e</span><span class="p">:</span>
    <span class="nb">print</span><span class="p">(</span><span class="n">e</span><span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">


<div class="output_html rendered_html output_subarea ">

<style>
    /* Turns off some styling */
    progress {
        /* gets rid of default border in Firefox and Opera. */
        border: none;
        /* Needs to be in here for Safari polyfill so background images work as expected. */
        background-size: auto;
    }
    .progress-bar-interrupted, .progress-bar-interrupted::-webkit-progress-bar {
        background: #F44336;
    }
</style>

</div>

</div>

<div class="output_area">


<div class="output_html rendered_html output_subarea ">

    <div>
      <progress value='5152768' class='' max='5152564' style='width:300px; height:20px; vertical-align: middle;'></progress>
      100.00% [5152768/5152564 00:01<00:00]
    </div>
    
</div>

</div>

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">
<pre>Unknown archive format &#39;/home/jovyan/.fastdownload/archive/dmelanogaster_external_feature__external_feature__main.txt.gz&#39;
</pre>
</div>
</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>I dug a little deeper into <code>fastdownload</code> and found that it uses <code>shutil</code> module <a href="#resources">[4]</a> for decompressing files. This module supports only some of compression formats and <code>.gz</code> is not one of them.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">import</span> <span class="nn">shutil</span>

<span class="n">shutil</span><span class="o">.</span><span class="n">get_archive_formats</span><span class="p">()</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">



<div class="output_text output_subarea output_execute_result">
<pre>[(&#39;bztar&#39;, &#34;bzip2&#39;ed tar-file&#34;),
 (&#39;gztar&#39;, &#34;gzip&#39;ed tar-file&#34;),
 (&#39;tar&#39;, &#39;uncompressed tar file&#39;),
 (&#39;xztar&#39;, &#34;xz&#39;ed tar-file&#34;),
 (&#39;zip&#39;, &#39;ZIP file&#39;)]</pre>
</div>

</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>However, we can add support for a new compression format by creating a decompression function and registering it in <code>shutil</code>. There is a native support for this in <code>shutil</code>.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="nb">print</span><span class="p">(</span><span class="n">shutil</span><span class="o">.</span><span class="n">register_archive_format</span><span class="o">.</span><span class="vm">__doc__</span><span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">

<div class="output_subarea output_stream output_stdout output_text">
<pre>Registers an archive format.

    name is the name of the format. function is the callable that will be
    used to create archives. If provided, extra_args is a sequence of
    (name, value) tuples that will be passed as arguments to the callable.
    description can be provided to describe the format, and will be returned
    by the get_archive_formats() function.
    
</pre>
</div>
</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>Thanks to the smart people at <code>stackoverflow</code> I was able to figure out how to do it. I adapted the code from <code>stackoverflow</code> <a href="#resources">[5]</a> and I replaced problematic file name extraction with a <code>pathlib.Path</code> <a href="#resources">[6]</a>. We might register any compression format in this way.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">pathlib</span> <span class="kn">import</span> <span class="n">Path</span>
<span class="kn">import</span> <span class="nn">gzip</span>
<span class="kn">import</span> <span class="nn">shutil</span>


<span class="k">def</span> <span class="nf">gunzip_something</span><span class="p">(</span><span class="n">gzipped_file_name</span><span class="p">,</span> <span class="n">work_dir</span><span class="p">):</span>
    <span class="sd">&quot;&quot;&quot;gunzip the given gzipped fil</span>

<span class="sd">    Args:</span>
<span class="sd">        gzipped_file_name (str): path to the gzipped file</span>
<span class="sd">        work_dir (str): path to the directory where the file will be unzipped</span>
<span class="sd">    &quot;&quot;&quot;</span>

    <span class="n">filename</span> <span class="o">=</span> <span class="n">Path</span><span class="p">(</span><span class="n">gzipped_file_name</span><span class="p">)</span><span class="o">.</span><span class="n">stem</span>

    <span class="k">with</span> <span class="n">gzip</span><span class="o">.</span><span class="n">open</span><span class="p">(</span><span class="n">gzipped_file_name</span><span class="p">,</span> <span class="s1">&#39;rb&#39;</span><span class="p">)</span> <span class="k">as</span> <span class="n">f_in</span><span class="p">:</span>
        <span class="k">with</span> <span class="nb">open</span><span class="p">(</span><span class="n">Path</span><span class="p">(</span><span class="n">work_dir</span><span class="p">,</span> <span class="n">filename</span><span class="p">),</span> <span class="s1">&#39;wb&#39;</span><span class="p">)</span> <span class="k">as</span> <span class="n">f_out</span><span class="p">:</span>
            <span class="n">shutil</span><span class="o">.</span><span class="n">copyfileobj</span><span class="p">(</span><span class="n">f_in</span><span class="p">,</span> <span class="n">f_out</span><span class="p">)</span>


<span class="n">shutil</span><span class="o">.</span><span class="n">register_unpack_format</span><span class="p">(</span>
    <span class="n">name</span><span class="o">=</span><span class="s1">&#39;gz&#39;</span><span class="p">,</span>
    <span class="n">extensions</span><span class="o">=</span><span class="p">[</span><span class="s1">&#39;.gz&#39;</span><span class="p">],</span>
    <span class="n">function</span><span class="o">=</span><span class="n">gunzip_something</span><span class="p">,</span>
    <span class="n">description</span><span class="o">=</span><span class="s1">&#39;Gzipped file&#39;</span>
<span class="p">)</span>
</pre></div>

    </div>
</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>And now we can download also files with <code>.gz</code> extension.</p>

</div>
</div>
</div>
    {% raw %}
    
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
    <div class="input_area">
<div class=" highlight hl-ipython3"><pre><span></span><span class="n">path</span> <span class="o">=</span> <span class="n">d</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;http://ftp.ensembl.org/pub/release-106/mysql/regulation_mart_106/dmelanogaster_external_feature__external_feature__main.txt.gz&#39;</span><span class="p">)</span>
<span class="n">path</span>
</pre></div>

    </div>
</div>
</div>

<div class="output_wrapper">
<div class="output">

<div class="output_area">



<div class="output_text output_subarea output_execute_result">
<pre>Path(&#39;/home/jovyan/.fastdownload/data/dmelanogaster_external_feature__external_feature__main.txt&#39;)</pre>
</div>

</div>

</div>
</div>

</div>
    {% endraw %}

<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Conclusion">Conclusion<a class="anchor-link" href="#Conclusion"> </a></h2><p><code>fastdownload</code> is a quite useful tool for managing datasets. If you like to use it also for managing any type of compressed files, use <a href="https://github.com/katarinagresova/fastdownload">my extended version</a>. You can also use it for any compression type by registering a decompression function in <code>shutil</code> as described above.</p>
<p>Fast downloading!</p>

</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Resources">Resources<a class="anchor-link" href="#Resources"> </a></h2><p>[1] <a href="https://www.fast.ai/">https://www.fast.ai/</a><br>
[2] <a href="https://fastdownload.fast.ai/">https://fastdownload.fast.ai/</a><br>
[3] <a href="https://github.com/katarinagresova">https://github.com/katarinagresova</a><br>
[4] <a href="https://docs.python.org/3/library/shutil.html">https://docs.python.org/3/library/shutil.html</a><br>
[5] <a href="https://stackoverflow.com/a/57923425">https://stackoverflow.com/a/57923425</a><br>
[6] <a href="https://docs.python.org/3/library/pathlib.html">https://docs.python.org/3/library/pathlib.html</a></p>

</div>
</div>
</div>
</div>
 
