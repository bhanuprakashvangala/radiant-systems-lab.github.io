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
      <li id="tab-article" class="active">
        <a href="javascript:showPubType('Article')">Articles</a>
      </li>
      <li id="tab-chapter">
        <a href="javascript:showPubType('Chapter')">Chapters</a>
      </li>
      <li id="tab-dissertation">
        <a href="javascript:showPubType('Dissertation')">Dissertations</a>
      </li>
      <li id="tab-editorial">
        <a href="javascript:showPubType('Editorial')">Editorials</a>
      </li>
      <li id="tab-other">
        <a href="javascript:showPubType('Other')">Others</a>
      </li>
    </ul>
  </div>
</div>

<div id="pub-lists">
  {% assign types = "Article,Chapter,Dissertation,Editorial,Other" | split: "," %}
  {% for t in types %}
    <div id="pub-{{ t | downcase }}"
         class="pub-type{% if forloop.first %} active{% endif %}">
      {% assign block = site.data.publications | where: "Type", t %}
      {% assign years = block | map: "Year" | uniq | sort | reverse %}
      {% for y in years %}
        <h3 class="pubyear">{{ y }}</h3>
        <ol>
        {% for p in block %}
          {% if p.Year == y %}
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
                  <a href="{{ p.pdf_link }}" target="_blank"
                     class="pub-action">
                    <i class="fas fa-file-pdf"></i> PDF
                  </a>
                {% else %}
                  {% assign fn = p.id | split: "-" | last | append: ".pdf" | prepend: "/pdfs/pdfs/pubs/" %}
                  {% assign hasPdf = site.static_files | map: "path" | join: " " | contains: fn %}
                  {% if hasPdf %}
                    <a href="{{ fn }}" target="_blank" class="pub-action">
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
  {% if p.Journal %}journal   = { {{ p.Journal }} },{% endif %}
  {% if p.Publisher %}publisher = { {{ p.Publisher }} },{% endif %}
  year      = {{ p.Year }},
}
              </pre>
            </li>
          {% endif %}
        {% endfor %}
        </ol>
      {% endfor %}
    </div>
  {% endfor %}
</div>

<script>
function showPubType(type) {
  document.querySelectorAll('.pub-type').forEach(div => {
    div.id === 'pub-' + type.toLowerCase()
      ? div.classList.add('active')
      : div.classList.remove('active');
  });
  document.querySelectorAll('#pub-tabs li').forEach(li => {
    li.id === 'tab-' + type.toLowerCase()
      ? li.classList.add('active')
      : li.classList.remove('active');
  });
}

function toggleSection(id) {
  const el = document.getElementById(id);
  if (el) el.classList.toggle('show');
}
</script>
