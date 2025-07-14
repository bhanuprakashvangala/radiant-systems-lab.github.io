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

{% assign pubs  = site.data.publications %}
{% assign years = pubs | map: "Year" | uniq | sort | reverse %}

<div id="pub-lists">
  {% for type in "all,article,chapter,dissertation,other" | split: "," %}
    <div id="pub-{{ type }}"
         class="pub-type{% if type == 'all' %} active{% endif %}">
      {% for y in years %}
        {% assign has = false %}
        {% for p in pubs %}
          {% if p.Year == y %}
            {% assign isA = p.Conference or p.Journal %}
            {% assign isC = p.Book %}
            {% assign isD = p.Institution %}
            {% assign isO = not (isA or isC or isD) %}
            {% if type == 'all' 
                  or (type=='article'     and isA)
                  or (type=='chapter'     and isC)
                  or (type=='dissertation'and isD)
                  or (type=='other'       and isO)
               %}
              {% assign has = true %}
              {% break %}
            {% endif %}
          {% endif %}
        {% endfor %}
        {% if has %}
          <h3 class="pubyear">{{ y }}</h3>
          <ol>
            {% for p in pubs %}
              {% if p.Year == y %}
                {% assign isA = p.Conference or p.Journal %}
                {% assign isC = p.Book %}
                {% assign isD = p.Institution %}
                {% assign isO = not (isA or isC or isD) %}
                {% if type == 'all'
                      or (type=='article'     and isA)
                      or (type=='chapter'     and isC)
                      or (type=='dissertation'and isD)
                      or (type=='other'       and isO)
                   %}
                  <li class="pub-entry">
                    <span class="pub-title">{{ p.title }}</span><br>
                    <span class="pub-authors">{{ p.Authors }}</span><br>
                    <em>
                      {% if p.Book       %}{{ p.Book       }}
                      {% elsif p.Journal %}{{ p.Journal    }}
                      {% elsif p.Conference %}{{ p.Conference }}
                      {% elsif p.Publisher  %}{{ p.Publisher }}
                      {% endif %}
                    </em>

                    <!-- DETAILS for Abstract -->
                    {% if p.Description %}
                    <details class="pub-details">
                      <summary><i class="fas fa-file-alt"></i> Abstract</summary>
                      <p>{{ p.Description }}</p>
                    </details>
                    {% endif %}

                    <!-- DETAILS for BibTeX -->
                    <details class="pub-details">
                      <summary><i class="fas fa-code"></i> BibTeX</summary>
<pre>@article{ {{ p.id }},
  title     = { {{ p.title }} },
  author    = { {{ p.bibAuthors | default: p.Authors }} },
  {% if p.Journal   %}journal   = { {{ p.Journal }} },{% endif %}
  {% if p.Publisher %}publisher = { {{ p.Publisher }} },{% endif %}
  year      = {{ p.Year }},
}</pre>
                    </details>

                    <!-- PDF Link -->
                    <div class="pub-icons">
                      {% if p.pdf_link %}
                        <a href="{{ p.pdf_link }}" target="_blank" class="pub-action">
                          <i class="fas fa-file-pdf"></i> PDF
                        </a>
                      {% else %}
                        {% assign fn = p.id | split: "-" | last | append: ".pdf" | prepend: "/pdfs/pdfs/pubs/" %}
                        {% assign hasPdf = site.static_files | map:"path" | join:" " | contains: fn %}
                        {% if hasPdf %}
                          <a href="{{ fn }}" target="_blank" class="pub-action">
                            <i class="fas fa-file-pdf"></i> PDF
                          </a>
                        {% else %}
                          <i class="fas fa-file-pdf disabled"></i>
                        {% endif %}
                      {% endif %}
                    </div>
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
document.addEventListener("DOMContentLoaded", ()=>showPubType('all'));

function showPubType(type) {
  document.querySelectorAll('.pub-entry').forEach(li=>{
    li.style.display =
      (type==='all'||li.matches('.type-'+type)) ? '' : 'none';
  });
  document.querySelectorAll('.pubyear').forEach(h3=>{
    const next = h3.nextElementSibling,
          any  = next && Array.from(next.children).some(li=>li.style.display!=='none');
    h3.style.display = next.style.display = any?'':'none';
  });
  document.querySelectorAll('#pub-tabs li').forEach(li=>{
    li.classList.toggle('active', li.id==='tab-'+type);
  });
}
</script>
