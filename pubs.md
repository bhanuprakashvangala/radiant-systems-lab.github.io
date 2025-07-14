---
layout: frontpage
title: Publications
---

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
<link rel="stylesheet" href="{{ ASSET_PATH }}/css/publications.css">

# Publications

<p>
You can also find our articles on our
<a href="https://scholar.google.com/citations?hl=en&user=ZvYwdsUAAAAJ">Google Scholar profile</a>.
</p>

{% assign publications = site.data.publications %}
{% assign years = publications | map: 'Year' | uniq | sort | reverse %}
{% for year in years %}
<h3 class="pubyear">{{ year }}</h3>
<ol>
{% for pub in publications %}
  {% if pub.Year == year %}
    <li class="pub-entry">
      <span class="pub-title">{{ pub.title }}</span><br>
      <span class="pub-authors">{{ pub.Authors }}</span><br>
      <em>{% if pub.Book %}{{ pub.Book }}{% elsif pub.Journal %}{{ pub.Journal }}{% elsif pub.Conference %}{{ pub.Conference }}{% elsif pub.Publisher %}{{ pub.Publisher }}{% endif %}</em>
      <div class="pub-icons">
        {% if pub.Description %}
          <a href="javascript:void(0);" onclick="toggleSection('abs-{{ pub.id }}')" class="pub-action"><i class="fas fa-file-alt"></i> Abstract</a>
        {% else %}
          <i class="fas fa-file-alt disabled"></i>
        {% endif %}
        <a href="javascript:void(0);" onclick="toggleSection('bib-{{ pub.id }}')" class="pub-action"><i class="fas fa-code"></i> BibTeX</a>
        {% if pub.pdf_link %}
          <a href="{{ pub.pdf_link }}" target="_blank" class="pub-action"><i class="fas fa-file-pdf"></i> PDF</a>
        {% else %}
          {% assign pdfName = pub.id | split: '-' | last | append: '.pdf' | prepend: '/pdfs/pdfs/pubs/' %}
          {% assign hasPdf = site.static_files | map:'path' | join:' ' | contains: pdfName %}
          {% if hasPdf %}
            <a href="{{ pdfName }}" target="_blank" class="pub-action"><i class="fas fa-file-pdf"></i> PDF</a>
          {% else %}
            <i class="fas fa-file-pdf disabled"></i>
          {% endif %}
        {% endif %}
      </div>
      {% if pub.Description %}
      <div id="abs-{{ pub.id }}" class="pub-abstract">
        <strong>Abstract:</strong> {{ pub.Description }}
      </div>
      {% endif %}

      <pre id="bib-{{ pub.id }}" class="pub-bibtex">@article{ {{ pub.id }},
  title = { {{ pub.title }} },
  author = { {{ pub.bibAuthors | default: pub.Authors }} },
  {% if pub.Journal %}journal = { {{ pub.Journal }} },{% endif %}
  {% if pub.Publisher %}publisher = { {{ pub.Publisher }} },{% endif %}
  year = {{ pub.Year }},
}</pre>

    </li>
  {% endif %}
{% endfor %}
</ol>
{% endfor %}

<script>
function toggleSection(sectionId){
  var el = document.getElementById(sectionId);
  if(!el) return;
  el.classList.toggle('show');
}
</script>
