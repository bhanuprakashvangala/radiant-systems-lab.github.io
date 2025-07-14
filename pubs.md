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
      <li id="tab-all" class="active">
        <a href="javascript:showPubType('all')">All</a>
      </li>
      <li id="tab-article">
        <a href="javascript:showPubType('article')">Articles</a>
      </li>
      <li id="tab-chapter">
        <a href="javascript:showPubType('chapter')">Chapters</a>
      </li>
      <li id="tab-dissertation">
        <a href="javascript:showPubType('dissertation')">Dissertations</a>
      </li>
      <li id="tab-editorial">
        <a href="javascript:showPubType('editorial')">Editorials</a>
      </li>
      <li id="tab-other">
        <a href="javascript:showPubType('other')">Others</a>
      </li>
    </ul>
  </div>
</div>

{% comment %}
  Title lists for mapping to types (as before)…
{% endcomment %}
{% assign article_titles = "… | split: '|'" %}
{% assign chapter_titles = "… | split: '|'" %}
{% assign dissertation_titles = "Large scale data management for the sciences" | split: "|" %}

{% assign publications = site.data.publications %}
{% assign years = publications | map: "Year" | uniq | sort | reverse %}

<div id="pub-lists">
  {% for type in "all,article,chapter,dissertation,editorial,other" | split: "," %}
    <div id="pub-{{ type }}" class="pub-type{% if type == "all" %} active{% endif %}">
      {% for y in years %}
        {% comment %} Count how many of this type & year {% endcomment %}
        {% assign ct = 0 %}
        {% for p in publications %}
          {% if p.Year == y %}
            {% assign isArt = article_titles contains p.title %}
            {% assign isCha = chapter_titles contains p.title %}
            {% assign isDis = dissertation_titles contains p.title %}
            {% assign isOth = not(isArt or isCha or isDis) %}
            {% if type == "all" 
                  or (type == "article" and isArt)
                  or (type == "chapter" and isCha)
                  or (type == "dissertation" and isDis)
                  or (type == "other" and isOth)
               %}
              {% assign ct = ct | plus: 1 %}
            {% endif %}
          {% endif %}
        {% endfor %}
        {% if ct > 0 %}
          <h3 class="pubyear">{{ y }}</h3>
          <ol>
            {% for p in publications %}
              {% if p.Year == y %}
                {% assign isArt = article_titles contains p.title %}
                {% assign isCha = chapter_titles contains p.title %}
                {% assign isDis = dissertation_titles contains p.title %}
                {% assign isOth = not(isArt or isCha or isDis) %}
                {% if type == "all" 
                      or (type == "article" and isArt)
                      or (type == "chapter" and isCha)
                      or (type == "dissertation" and isDis)
                      or (type == "other" and isOth)
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
  {% if p.Journal %}journal   = { {{ p.Journal }} },{% endif %}
  {% if p.Publisher %}publisher = { {{ p.Publisher }} },{% endif %}
  year      = {{ p.Year }},
}
                    </pre>
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
  // Ensure "All" is active on page load
  document.addEventListener("DOMContentLoaded", () => showPubType('all'));

  function showPubType(type) {
    document.querySelectorAll('.pub-type').forEach(div => {
      // if 'all', show every block, else only matching block
      if (type === 'all') div.classList.add('active');
      else div.id === 'pub-' + type
           ? div.classList.add('active')
           : div.classList.remove('active');
    });
    document.querySelectorAll('#pub-tabs li').forEach(li => {
      li.id === 'tab-' + type
        ? li.classList.add('active')
        : li.classList.remove('active');
    });
  }

  function toggleSection(id) {
    const el = document.getElementById(id);
    if (el) el.classList.toggle('show');
  }
</script>
