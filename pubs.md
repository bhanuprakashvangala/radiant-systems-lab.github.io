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

{% assign pubs  = site.data.publications %}
{% assign years = pubs | map: "Year" | uniq | sort | reverse %}

<div id="pub-list">
  {% for y in years %}
    <h3 class="pubyear">{{ y }}</h3>
    <ol>
      {% for p in pubs %}
        {% if p.Year == y %}
          {% assign isArticle      = p.Conference or p.Journal %}
          {% assign isChapter      = p.Book %}
          {% assign isDissertation = p.Institution %}
          {% assign isOther        = not (isArticle or isChapter or isDissertation) %}
          {% assign klass = 
               isArticle      and 'type-article'      or
               isChapter      and 'type-chapter'      or
               isDissertation and 'type-dissertation' or
               isOther         and 'type-other'
          %}
          <li class="pub-entry {{ klass }}">
            <h4 class="pub-title">{{ p.title }}</h4>
            <div class="pub-authors">{{ p.Authors }}</div>
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
              {% if p.Description %}
                <button class="pub-action"
                        onclick="toggleSection('abs-{{ p.id }}')">
                  <i class="fas fa-file-alt"></i> Abstract
                </button>
              {% endif %}
              <button class="pub-action"
                      onclick="toggleSection('bib-{{ p.id }}')">
                <i class="fas fa-code"></i> BibTeX
              </button>
              {% if p.pdf_link %}
                <a href="{{ p.pdf_link }}"
                   class="pub-action" target="_blank">
                  <i class="fas fa-file-pdf"></i> PDF
                </a>
              {% endif %}
            </div>

            {% if p.Description %}
              <div id="abs-{{ p.id }}" class="pub-section pub-abstract">
                <p>{{ p.Description }}</p>
              </div>
            {% endif %}

            <div id="bib-{{ p.id }}" class="pub-section pub-bibtex">
<pre>@article{ {{ p.id }},
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
// Default to “All”
document.addEventListener("DOMContentLoaded", ()=>showPubType('all'));

function showPubType(type) {
  document.querySelectorAll('.pub-entry').forEach(li => {
    li.style.display =
      (type==='all' || li.classList.contains('type-'+type)) ? '' : 'none';
  });
  document.querySelectorAll('.pubyear').forEach(h3 => {
    const ol = h3.nextElementSibling;
    const any = ol && Array.from(ol.children)
                         .some(li=>li.style.display!=='none');
    h3.style.display = ol.style.display = any ? '' : 'none';
  });
  document.querySelectorAll('#pub-tabs li').forEach(li => {
    li.classList.toggle('active', li.id==='tab-'+type);
  });
}

// Toggle just the one section
function toggleSection(id) {
  const el = document.getElementById(id);
  if (el) el.classList.toggle('show');
}
</script>
