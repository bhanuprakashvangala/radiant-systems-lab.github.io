---
layout: frontpage
title: Publications
---

<link rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
<link rel="stylesheet" href="{{ ASSET_PATH }}/css/publications.css">

# Publications

<p>
  You can also find our articles on our
  <a href="https://scholar.google.com/citations?hl=en&user=ZvYwdsUAAAAJ" target="_blank">Google Scholar profile</a>.
</p>

<div class="navbar">
  <div class="navbar-inner">
    <ul id="pub-tabs" class="nav nav-tabs">
      <li id="tab-all" class="active"><a href="javascript:showPubType('all')">All</a></li>
      <li id="tab-article"><a href="javascript:showPubType('article')">Articles</a></li>
      <li id="tab-chapter"><a href="javascript:showPubType('chapter')">Chapters</a></li>
      <li id="tab-dissertation"><a href="javascript:showPubType('dissertation')">Dissertations</a></li>
      <li id="tab-editorial"><a href="javascript:showPubType('editorial')">Editorials</a></li>
      <li id="tab-other"><a href="javascript:showPubType('other')">Others</a></li>
    </ul>
  </div>
</div>

{% comment %}
  Title lists for classification (exact match, case-sensitive)
{% endcomment %}
{% assign article_titles = "Kondo: Efficient Provenance-driven Data Debloating|Reproducible eScience: The Data Containerization Challenge|Efficient Differencing of System-level Provenance Graphs|Towards Shareable and Reproducible Cloud Computing Experiments|Querying Container Provenance|IOSPReD: I/O Specialized Packaging of Reduced Datasets and Data-Intensive Applications for Efficient Reproducibility|SOLE: towards descriptive and interactive publications|Leveraging Scientific Cyberinfrastructures to Achieve Computational Hydrologic Model Reproducibility|Report on the first international workshop on incremental re-computation: Provenance and beyond|Cyberinfrastructure to Support Collaborative and Reproducible Computational Hydrologic Modeling|Sharing and reproducing database applications|PLI+: Efficient Clustering of Cloud Databases|Integrating scientific cyberinfrastructures to improve reproducibility in computational hydrology: Example for HydroShare and GeoTrust|GeoTrust Hub: A Platform For Sharing And Reproducing Geoscience Applications|A taxonomy for reproducible and replicable research in environmental modelling|Detecting database file tampering through page carving|Utilizing provenance in reusable research objects|The SDSS SkyServer - Public Access to the Sloan Digital Sky Server Data|Plenario: An Open Data Discovery and Exploration Platform for Urban Science.|Database forensic analysis with DBCarver|SkyQuery: A WebService approach to federate databases|DF-toolkit: interacting with low-level database storage|Personalized, Shareable Geoscience Dataspaces For Simplifying Data Management and Improving Reproducibility|GeoDataspaces: Simplifying Data Management Tasks with Globus|PDACS: a portal for data analysis services for cosmological simulations|CHEX: Multiversion Replay with Ordered Checkpoints|An invariant framework for conducting reproducible computational science|Liferaft: Data-driven, batch processing for the exploration of scientific databases|Towards a provenance-aware distributed filesystem|Reproducibility Practice in High-Performance Computing: Community Survey Results|An Approach for Open and Reproducible Hydrological Modeling using Sciunit and HydroShare" | split: "|" %}
{% assign chapter_titles = "Sketching distributed data provenance|GEN: a database interface generator for HPC programs|Improving Reproducibility of Distributed Computational Experiments|Ontology-based urban data exploration|GeoBase: indexing NetCDF files for large-scale data analysis|Improving the efficiency of subset queries on raster images|Efficient querying of distributed provenance stores|Artifact Description/Artifact Evaluation: A Reproducibility Bane or a Boon|PLI: Augmenting live databases with custom clustered indexes|Documenting computing environments for reproducible experiments|MiDas: Containerizing Data-Intensive Applications with I/O Specialization|Provenance-based Workflow Diagnostics Using Program Specification|Reproducible Notebook Containers using Application Virtualization" | split: "|" %}
{% assign dissertation_titles = "Large scale data management for the sciences" | split: "|" %}

{% assign publications = site.data.publications %}
{% assign years = publications | map: "Year" | uniq | sort | reverse %}

<div id="pub-lists">
  {% assign types = "all,article,chapter,dissertation,editorial,other" | split: "," %}
  {% for type in types %}
    <div id="pub-{{ type }}" class="pub-type{% if forloop.first %} active{% endif %}">
      {% for y in years %}
        {% assign count = 0 %}
        {% for p in publications %}
          {% if p.Year == y and (
            type == "all"
            or (type == "article" and article_titles contains p.title)
            or (type == "chapter" and chapter_titles contains p.title)
            or (type == "dissertation" and dissertation_titles contains p.title)
            or (type == "other" and not(article_titles contains p.title or chapter_titles contains p.title or dissertation_titles contains p.title))
          ) %}
            {% assign count = count | plus: 1 %}
          {% endif %}
        {% endfor %}
        {% if count > 0 %}
          <h3 class="pubyear">{{ y }}</h3>
          <ol>
          {% for p in publications %}
            {% if p.Year == y %}
              {% assign isArt = article_titles contains p.title %}
              {% assign isCha = chapter_titles contains p.title %}
              {% assign isDis = dissertation_titles contains p.title %}
              {% if type == "all"
                or (type == "article" and isArt)
                or (type == "chapter" and isCha)
                or (type == "dissertation" and isDis)
                or (type == "other" and not(isArt or isCha or isDis))
              %}
                <li class="pub-entry">
                  <span class="pub-title">{{ p.title }}</span><br>
                  <span class="pub-authors">{{ p.Authors }}</span><br>
                  <em>
                    {% if p.Book %}{{ p.Book }}
                    {% elsif p.Journal %}{{ p.Journal }}
                    {% elsif p.Conference %}{{ p.Conference }}
                    {% elsif p.Publisher %}{{ p.Publisher }}
                    {% endif %}
                  </em>
                  <div class="pub-icons">
                    {% if p.Description %}
                      <a href="javascript:void(0);" onclick="toggleSection('abs-{{ p.id }}')" class="pub-action"><i class="fas fa-file-alt"></i> Abstract</a>
                    {% else %}
                      <i class="fas fa-file-alt disabled"></i>
                    {% endif %}
                    <a href="javascript:void(0);" onclick="toggleSection('bib-{{ p.id }}')" class="pub-action"><i class="fas fa-code"></i> BibTeX</a>
                    {% if p.pdf_link %}
                      <a href="{{ p.pdf_link }}" target="_blank" class="pub-action"><i class="fas fa-file-pdf"></i> PDF</a>
                    {% else %}
                      {% assign fn = p.id | split: "-" | last | append: ".pdf" | prepend: "/pdfs/pdfs/pubs/" %}
                      {% assign hasPdf = site.static_files | map: "path" | join: " " | contains: fn %}
                      {% if hasPdf %}
                        <a href="{{ fn }}" target="_blank" class="pub-action"><i class="fas fa-file-pdf"></i> PDF</a>
                      {% else %}
                        <i class="fas fa-file-pdf disabled"></i>
                      {% endif %}
                    {% endif %}
                  </div>
                  {% if p.Description %}
                    <div id="abs-{{ p.id }}" class="pub-abstract"><strong>Abstract:</strong> {{ p.Description }}</div>
                  {% endif %}
                  <pre id="bib-{{ p.id }}" class="pub-bibtex">@article{ {{ p.id }},
  title     = { {{ p.title }} },
  author    = { {{ p.bibAuthors | default: p.Authors }} },
  {% if p.Journal %}journal   = { {{ p.Journal }} },{% endif %}
  {% if p.Publisher %}publisher = { {{ p.Publisher }} },{% endif %}
  year      = {{ p.Year }},
}</pre>
                </li>
              {% endif %}
            {% endif %}
          {% endfor %}
          </ol>
        {% endif %}
      {% endfor %}
    </div>
  {% endfor %}
</div>

<script>
  document.addEventListener("DOMContentLoaded", function(){ showPubType('all'); });
  function showPubType(type) {
    document.querySelectorAll('.pub-type').forEach(div => {
      (div.id === 'pub-'+type) ? div.classList.add('active') : div.classList.remove('active');
    });
    document.querySelectorAll('#pub-tabs li').forEach(li => {
      (li.id === 'tab-'+type) ? li.classList.add('active') : li.classList.remove('active');
    });
  }
  function toggleSection(id) {
    const el = document.getElementById(id); if(el) el.classList.toggle('show');
  }
</script>
