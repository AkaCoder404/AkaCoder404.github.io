---
title: Projects
subtitle: >-
  This is a portfolio of projects that I have completed 
img_path: images/tokyonight.jpg
seo:
  title: Projects
  description: This is a portfolio of projects that I have completed 
  extra:
    - name: 'og:type'
      value: website
      keyName: property
    - name: 'og:title'
      value: Projects
      keyName: property
    - name: 'og:description'
      value: >-
        This is a portfolio of projects that I have completed
      keyName: property
    - name: 'og:image'
      value: images/style-guide.jpg
      keyName: property
      relativeUrl: true
    - name: 'twitter:card'
      value: summary_large_image
    - name: 'twitter:title'
      value: Projects
    - name: 'twitter:description'
      value: >-
        This is a portfolio of projects that I have completed 
    - name: 'twitter:image'
      value: images/style-guide.jpg
      relativeUrl: true
layout: projects
---

This is a collection of personal projects that I have completed over the years. More projects can be found at my github.

### Portfolio
{% if site.theme_config.show_projects == true %}
  {% include card_list.html collection=site.data.projects.project_entries %}
{% endif %}
### Old Projects

{% if site.theme_config.show_old_projects == true %}
  {% include card_list.html collection=site.data.projects.old_project_entries %}
{% endif %}
