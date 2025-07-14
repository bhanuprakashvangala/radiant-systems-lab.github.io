---
layout: frontpage
title: Publications
---

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
<link rel="stylesheet" href="{{ ASSET_PATH }}/css/publications.css">

# Publications

<div class="navbar">
    <div class="navbar-inner">
        <ul id="pub-tabs" class="nav nav-tabs">
            <li id="tab-article" class="active"><a href="javascript:showPubType('article')">Articles</a></li>
            <li id="tab-chapter"><a href="javascript:showPubType('chapter')">Chapters</a></li>
            <li id="tab-dissertation"><a href="javascript:showPubType('dissertation')">Dissertations</a></li>
            <li id="tab-editorial"><a href="javascript:showPubType('editorial')">Editorials</a></li>
            <li id="tab-other"><a href="javascript:showPubType('other')">Others</a></li>
        </ul>
    </div>
</div>

<p>
You can also find our articles on our
<a href="https://scholar.google.com/citations?hl=en&user=ZvYwdsUAAAAJ">Google Scholar profile</a>.
</p>

<div id="pub-lists">
{% assign pub_types = "article,chapter,dissertation,editorial,other" | split: "," %}
{% for t in pub_types %}
  <div id="pub-{{ t }}" class="pub-type{% if forloop.first %} active{% endif %}">
    {% assign pubs_of_type = site.data.publications | where: 'type', t %}
    {% assign years = pubs_of_type | map: 'year' | uniq | sort | reverse %}
    {% for y in years %}
      <h3 class="pubyear">{{ y }}</h3>
      <ol>
      {% for p in pubs_of_type %}
        {% if p.year == y %}
          <li class="pub-entry">
            <span class="pub-title">{{ p.title }}</span><br>
            <span class="pub-authors">{{ p.Authors }}</span><br>
            <em>{{ p.venue }}</em>
            <div class="pub-icons">
              {% if p.Description %}
                <a href="javascript:void(0);" onclick="toggleSection('tab-abs-{{ p.id }}')" class="pub-action"><i class="fas fa-file-alt"></i> Abstract</a>
              {% else %}
                <i class="fas fa-file-alt disabled"></i>
              {% endif %}
              <a href="javascript:void(0);" onclick="toggleSection('tab-bib-{{ p.id }}')" class="pub-action"><i class="fas fa-code"></i> BibTeX</a>
              {% if p.pdf_link %}
                <a href="{{ p.pdf_link }}" target="_blank" class="pub-action"><i class="fas fa-file-pdf"></i> PDF</a>
              {% else %}
                {% assign pdfName = p.id | split: '-' | last | append: '.pdf' | prepend: '/pdfs/pdfs/pubs/' %}
                {% assign hasPdf = site.static_files | map:'path' | join:' ' | contains: pdfName %}
                {% if hasPdf %}
                  <a href="{{ pdfName }}" target="_blank" class="pub-action"><i class="fas fa-file-pdf"></i> PDF</a>
                {% else %}
                  <i class="fas fa-file-pdf disabled"></i>
                {% endif %}
              {% endif %}
            </div>
            {% if p.Description %}
            <div id="tab-abs-{{ p.id }}" class="pub-abstract">
              <strong>Abstract:</strong> {{ p.Description }}
            </div>
            {% endif %}
            <pre id="tab-bib-{{ p.id }}" class="pub-bibtex">@article{ {{ p.id }},
  title = { {{ p.title }} },
  author = { {{ p.bibAuthors | default: p.Authors }} },
  {% if p.venue %}journal = { {{ p.venue }} },{% endif %}
  year = {{ p.year }},
}</pre>
          </li>
        {% endif %}
      {% endfor %}
      </ol>
    {% endfor %}
  </div>
{% endfor %}
</div>

<script>
function showPubType(type){
  document.querySelectorAll('.pub-type').forEach(function(div){
    if(div.id === 'pub-'+type){
      div.classList.add('active');
    } else {
      div.classList.remove('active');
    }
  });
  document.querySelectorAll('#pub-tabs li').forEach(function(li){
    if(li.id === 'tab-'+type){
      li.classList.add('active');
    } else {
      li.classList.remove('active');
    }
  });
}

function toggleSection(sectionId){
  var el = document.getElementById(sectionId);
  if(!el) return;
  el.classList.toggle('show');
}
</script>
