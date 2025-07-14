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
      <li id="tab-all"      class="active"><a href="javascript:showPubType('all')">All</a></li>
      <li id="tab-article"           ><a href="javascript:showPubType('article')">Articles</a></li>
      <li id="tab-chapter"           ><a href="javascript:showPubType('chapter')">Chapters</a></li>
      <li id="tab-dissertation"      ><a href="javascript:showPubType('dissertation')">Dissertations</a></li>
      <li id="tab-other"             ><a href="javascript:showPubType('other')">Others</a></li>
    </ul>
  </div>
</div>

{% comment %}
  Fetch data & sort years
{% endcomment %}
{% assign pubs  = site.data.publications %}
{% assign years = pubs | map: "Year" | uniq | sort | reverse %}

{% comment %}
  Output every year + all pubs. Each <li> gets a "type-*" class via simple key checks.
{% endcomment %}
{% for y in years %}
  <h3 class="pubyear">{{ y }}</h3>
  <ol>
  {% for p in pubs %}
    {% if p.Year == y %}
      {% assign type = "other" %}
      {% if p.Conference or p.Journal %}        {% assign type = "article" %}{% endif %}
      {% if p.Book                       %}    {% assign type = "chapter" %}{% endif %}
      {% if p.Institution                %}    {% assign type = "dissertation" %}{% endif %}
      <li class="pub-entry type-{{ type }}">
        <span class="pub-title">{{ p.title }}</span><br>
        <span class="pub-authors">{{ p.Authors }}</span><br>
        <em>
          {% if p.Book       %}{{ p.Book       }}
          {% elsif p.Journal %}{{ p.Journal    }}
          {% elsif p.Conference %}{{ p.Conference }}
          {% elsif p.Publisher  %}{{ p.Publisher }}
          {% endif %}
        </em>
        <div class="pub-icons">
          {% if p.Description %}
            <a href="javascript:void(0);"
               onclick="toggleSection('abs-{{ p.id }}')"
               class="pub-action">
              <i class="fas fa-file-alt"></i> Abstract
            </a>
          {% else %}
            <i class="fas fa-file-alt disabled"></i>
          {% endif %}
          <a href="javascript:void(0);"
             onclick="toggleSection('bib-{{ p.id }}')"
             class="pub-action">
            <i class="fas fa-code"></i> BibTeX
          </a>
          {% if p.pdf_link %}
            <a href="{{ p.pdf_link }}"
               target="_blank"
               class="pub-action">
              <i class="fas fa-file-pdf"></i> PDF
            </a>
          {% else %}
            {% assign fn = p.id | split: "-" | last | append: ".pdf" | prepend: "/pdfs/pdfs/pubs/" %}
            {% assign hasPdf = site.static_files | map: "path" | join: " " | contains: fn %}
            {% if hasPdf %}
              <a href="{{ fn }}"
                 target="_blank"
                 class="pub-action">
                <i class="fas fa-file-pdf"></i> PDF
              </a>
            {% else %}
              <i class="fas fa-file-pdf disabled"></i>
            {% endif %}
          {% endif %}
        </div>
        {% if p.Description %}
          <div id="abs-{{ p.id }}" class="pub-abstract">
            <strong>Abstract:</strong> {{ p.Description }}
          </div>
        {% endif %}
        <pre id="bib-{{ p.id }}" class="pub-bibtex">
@article{ {{ p.id }},
  title     = { {{ p.title }} },
  author    = { {{ p.bibAuthors | default: p.Authors }} },
  {% if p.Journal   %}journal   = { {{ p.Journal }} },{% endif %}
  {% if p.Publisher %}publisher = { {{ p.Publisher }} },{% endif %}
  year      = {{ p.Year }},
}
        </pre>
      </li>
    {% endif %}
  {% endfor %}
  </ol>
{% endfor %}

<script>
// on load, show everything:
document.addEventListener("DOMContentLoaded", () => showPubType('all'));

function showPubType(type) {
  // Show/hide entries
  document.querySelectorAll('.pub-entry').forEach(li => {
    li.style.display = (type === 'all' || li.classList.contains('type-' + type))
                       ? '' : 'none';
  });
  // Hide headings if no visible children
  document.querySelectorAll('.pubyear').forEach(h3 => {
    const ol = h3.nextElementSibling;
    if (!ol) return;
    const any = Array.from(ol.children).some(li => li.style.display !== 'none');
    h3.style.display = ol.style.display = any ? '' : 'none';
  });
  // Update active tab
  document.querySelectorAll('#pub-tabs li').forEach(li => {
    li.classList.toggle('active', li.id === 'tab-' + type);
  });
}

function toggleSection(id) {
  const el = document.getElementById(id);
  if (el) el.classList.toggle('show');
}
</script>
