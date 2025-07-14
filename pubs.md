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
  <a href="https://scholar.google.com/citations?hl=en&user=ZvYwdsUAAAAJ"
     target="_blank">Google Scholar profile</a>.
</p>

<div class="navbar">
  <div class="navbar-inner">
    <ul id="pub-tabs" class="nav nav-tabs">
      <li id="tab-all"         class="active"><a href="javascript:showPubType('all')">All</a></li>
      <li id="tab-article"               ><a href="javascript:showPubType('article')">Articles</a></li>
      <li id="tab-chapter"               ><a href="javascript:showPubType('chapter')">Chapters</a></li>
      <li id="tab-dissertation"          ><a href="javascript:showPubType('dissertation')">Dissertations</a></li>
      <li id="tab-editorial"             ><a href="javascript:showPubType('editorial')">Editorials</a></li>
      <li id="tab-other"                 ><a href="javascript:showPubType('other')">Others</a></li>
    </ul>
  </div>
</div>

{% assign pubs = site.data.publications %}
{% assign years = pubs | map: "Year" | uniq | sort | reverse %}

<div id="pub-list">
  {% for y in years %}
    <h3 class="pubyear">{{ y }}</h3>
    <ol>
      {% for p in pubs %}
        {% if p.Year == y %}
          {% assign isArticle     = p.Journal or p.Conference %}
          {% assign isChapter     = p.Book %}
          {% assign isDissertation= p.Institution %}
          {% assign isOther       = not (isArticle or isChapter or isDissertation) %}
          {% assign klass = 
               isArticle      and 'type-article'      or
               isChapter      and 'type-chapter'      or
               isDissertation and 'type-dissertation' or
               isOther         and 'type-other'
          %}
          <li class="pub-entry {{ klass }}">
            <div class="pub-header">
              <span class="pub-title">{{ p.title }}</span>
              <span class="pub-authors">{{ p.Authors }}</span>
            </div>
            <div class="pub-venue">
              <em>
                {% if p.Book       %}{{ p.Book       }}
                {% elsif p.Conference %}{{ p.Conference }}
                {% elsif p.Journal  %}{{ p.Journal   }}
                {% elsif p.Publisher %}{{ p.Publisher }}
                {% endif %}
              </em>
            </div>
            <div class="pub-icons">
              <button class="pub-action" onclick="toggleSection('abs-{{ p.id }}')">
                <i class="fas fa-file-alt"></i> Abstract
              </button>
              <button class="pub-action" onclick="toggleSection('bib-{{ p.id }}')">
                <i class="fas fa-code"></i> BibTeX
              </button>
              {% if p.pdf_link %}
                <a href="{{ p.pdf_link }}" target="_blank" class="pub-action">
                  <i class="fas fa-file-pdf"></i> PDF
                </a>
              {% else %}
                {% assign fn = p.id | split:"-" | last | append:".pdf" | prepend:"/pdfs/pdfs/pubs/" %}
                {% assign hasPdf = site.static_files | map:"path" | join:" " | contains: fn %}
                {% if hasPdf %}
                  <a href="{{ fn }}" target="_blank" class="pub-action">
                    <i class="fas fa-file-pdf"></i> PDF
                  </a>
                {% else %}
                  <span class="pub-action disabled">
                    <i class="fas fa-file-pdf"></i> PDF
                  </span>
                {% endif %}
              {% endif %}
            </div>
            {% if p.Description %}
              <div id="abs-{{ p.id }}" class="pub-section pub-abstract">
                <h4>Abstract</h4>
                <p>{{ p.Description }}</p>
              </div>
            {% endif %}
            <div id="bib-{{ p.id }}" class="pub-section pub-bibtex">
              <h4>BibTeX</h4>
              <pre>
@article{ {{ p.id }},
  title     = { {{ p.title }} },
  author    = { {{ p.bibAuthors | default: p.Authors }} },
  {% if p.Journal   %}journal   = { {{ p.Journal }} },{% endif %}
  {% if p.Publisher %}publisher = { {{ p.Publisher }} },{% endif %}
  year      = {{ p.Year }},
}
              </pre>
            </div>
          </li>
        {% endif %}
      {% endfor %}
    </ol>
  {% endfor %}
</div>

<script>
document.addEventListener("DOMContentLoaded", ()=>showPubType('all'));

function showPubType(type) {
  // show/hide each entry by class
  document.querySelectorAll('.pub-entry').forEach(li=>{
    li.style.display = (type==='all' || li.classList.contains('type-'+type))
                       ? '' : 'none';
  });
  // hide any year with no visible children
  document.querySelectorAll('.pubyear').forEach(h3=>{
    const ol = h3.nextElementSibling;
    const any = ol && Array.from(ol.children).some(li=>li.style.display!=='none');
    h3.style.display = ol.style.display = any ? '' : 'none';
  });
  // update active nav
  document.querySelectorAll('#pub-tabs li').forEach(li=>{
    li.classList.toggle('active', li.id === 'tab-'+type);
  });
}

function toggleSection(id) {
  const el = document.getElementById(id);
  el && el.classList.toggle('show');
}
</script>
